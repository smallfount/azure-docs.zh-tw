---
title: Azure 資料存儲 Gen2 Python SDK，用於& ACL 的檔
description: 在啟用了階層命名空間 （HNS） 的存儲帳戶中使用 Python 管理目錄以及檔和目錄存取控制清單 （ACL）。
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b43bfe5979b8232f1fee2f5c1c6873c9c62695a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061534"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 Python 管理 Azure 資料湖存儲 Gen2 中的目錄、檔和 ACL

本文介紹如何使用 Python 在啟用了階層命名空間 （HNS） 的存儲帳戶中創建和管理目錄、檔和許可權。 

[包（Python 包索引）](https://pypi.org/project/azure-storage-file-datalake/) | [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API 參考](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html) | [第 1 代映射到第 2 代映射](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [提供回饋](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間 （HNS） 的存儲帳戶。 按照[這些](data-lake-storage-quickstart-create-account.md)說明創建一個。

## <a name="set-up-your-project"></a>設定專案

使用[pip](https://pypi.org/project/pip/)為 Python 安裝 Azure 資料存儲庫用戶端庫。

```
pip install azure-storage-file-datalake --pre
```

將這些導入語句添加到代碼檔的頂部。

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>連接到帳戶

要使用本文中的程式碼片段，您需要創建表示存儲帳戶**的 DataLakeServiceClient**實例。 

### <a name="connect-by-using-an-account-key"></a>使用帳戶金鑰進行連接

這是連接到帳戶的最簡單方法。 

本示例使用帳戶金鑰創建**DataLakeServiceClient**實例。

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- 使用您的儲存體帳戶名稱取代 `storage_account_name` 預留位置值。

- 將`storage_account_key`預留位置值替換為存儲帳戶訪問金鑰。

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure 活動目錄 （AD） 進行連接

可以使用 Python[的 Azure 標識用戶端庫](https://pypi.org/project/azure-identity/)使用 Azure AD 對應用程式進行身份驗證。

本示例使用用戶端 ID、用戶端機密和租戶 ID 創建**DataLakeServiceClient**實例。  要獲取這些值，請參閱[從 Azure AD 獲取權杖以授權來自用戶端應用程式的請求](../common/storage-auth-aad-app.md)。

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> 有關詳細資訊，請參閱 Python 文檔[的 Azure 標識用戶端庫](https://pypi.org/project/azure-identity/)。

## <a name="create-a-file-system"></a>建立檔案系統

檔案系統充當檔的容器。 您可以通過調用**檔案系統DataLakeServiceClient.create_file_system**方法創建一個。

本示例創建名為 的`my-file-system`檔案系統。

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>建立目錄

通過調用**FileSystemClient.create_directory**方法創建目錄引用。

本示例向檔案系統添加名為`my-directory`的目錄。 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>重新命名目錄或移動目錄

通過調用**DataLakeDirectoryClient.rename_directory**方法重命名或移動目錄。 傳遞所需目錄的路徑參數。 

本示例將子目錄重命名為 名稱`my-subdirectory-renamed`。

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>刪除目錄

通過調用**DataLakeDirectoryClient.delete_directory**方法刪除目錄。

此示例刪除名為 的`my-directory`目錄。  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>管理目錄許可權

通過調用**DataLakeDirectoryClient.get_access_control**方法獲取目錄的存取控制清單 （ACL），並通過調用**DataLakeDirectory.set_access_control**方法設置 ACL。

> [!NOTE]
> 如果應用程式使用 Azure 活動目錄 （Azure AD） 授權訪問，則請確保已分配應用程式用於授權訪問的安全[主體。](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 要瞭解有關如何應用 ACL 許可權及其更改效果的更多內容，請參閱[Azure 資料湖存儲 Gen2 中的訪問控制項](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

此示例獲取並設置名為 的`my-directory`目錄的 ACL。 該字串`rwxr-xrw-`授予擁有的使用者讀取、寫入和執行許可權，僅授予擁有組讀取和執行許可權，並授予所有其他讀取和寫入權限。

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>將檔上載到目錄 

首先，通過創建**DataLakeFileClient**類的實例在目標目錄中創建檔引用。 通過調用**DataLakeFileClient.append_data**方法上傳檔。 請確保通過調用**DataLakeFileClient.flush_data**方法完成上載。

本示例將文字檔上載到名為 的`my-directory`目錄。   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> 如果檔案大小較大，則代碼必須對**DataLakeFileClient.append_data**方法進行多次調用。 請考慮改用**DataLakeFileClient.upload_data**方法。 這樣，您可以在單個調用中上載整個檔。 

## <a name="upload-a-large-file-to-a-directory"></a>將大型檔上載到目錄

使用**DataLakeFileClient.upload_data**方法上載大型檔，而無需對**DataLakeFileClient.append_data**方法進行多次調用。

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>管理檔許可權

通過調用**DataLakeFileClient.get_access_control**方法獲取檔的存取控制清單 （ACL），並通過調用**DataLakeFileClient.set_access_control**方法設置 ACL。

> [!NOTE]
> 如果應用程式使用 Azure 活動目錄 （Azure AD） 授權訪問，則請確保已分配應用程式用於授權訪問的安全[主體。](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 要瞭解有關如何應用 ACL 許可權及其更改效果的更多內容，請參閱[Azure 資料湖存儲 Gen2 中的訪問控制項](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

此示例獲取並設置名為`my-file.txt`的檔的 ACL。 該字串`rwxr-xrw-`授予擁有的使用者讀取、寫入和執行許可權，僅授予擁有組讀取和執行許可權，並授予所有其他讀取和寫入權限。

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>從目錄中下載 

打開本地檔以進行寫入。 然後，創建一個表示要下載的檔**的資料湖檔用戶端**實例。 調用**DataLakeFileClient.read_file**從檔中讀取位元組，然後將這些位元組寫入本地檔。 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        downloaded_bytes = file_client.read_file()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>列出目錄內容

通過調用**FileSystemClient.get_paths**方法，然後通過結果枚舉來列出目錄內容。

此示例列印位於名為`my-directory`的目錄中的每個子目錄和檔的路徑。

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>另請參閱

* [API 參考文件](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [包（Python 包索引）](https://pypi.org/project/azure-storage-file-datalake/)
* [樣品](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [第 1 代到第 2 代映射](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供回饋](https://github.com/Azure/azure-sdk-for-python/issues)
