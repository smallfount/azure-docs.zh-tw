---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75466586"
---
### <a name="azure-storage-linked-service"></a>Azure 儲存體連結服務
「Azure 儲存體連結服務」**** 可讓您使用**帳戶金鑰**將 Azure 儲存體帳戶連結至 Azure Data Factory，而帳戶金鑰可將 Azure 儲存體的全域存取權提供給資料處理站。 下表提供 Azure 儲存體連結服務專屬 JSON 元素的描述。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type |類型屬性必須設置為 **：Azure 存儲** |是 |
| connectionString |針對 connectionString 屬性指定連接到 Azure 儲存體所需的資訊。 |是 |

如需有關如何擷取儲存體帳戶存取金鑰的資訊，請參閱[管理儲存體帳戶存取金鑰](../articles/storage/common/storage-account-keys-manage.md)。

**例子：**  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azure-storage-sas-linked-service"></a>Azure 儲存體 SAS 連結服務
共用存取簽章 (SAS) 可提供您儲存體帳戶中資源的委派存取。 它可讓您在無需分享您帳戶存取金鑰的情況下，將您儲存體帳戶中的物件有限權限授與用戶端，該用戶端便可在指定的時間期間內及使用指定的權限集來進行存取。 SAS 是一種 URI，此 URI 會在其查詢參數中包含對儲存體資源進行驗證式存取所需的一切資訊。 若要使用 SAS 存取儲存體資源，用戶端只需在適當的建構函式或方法中傳入 SAS 即可。 如需關於 SAS 的詳細資訊，請參閱[使用共用存取簽章 (SAS) 對 Azure 儲存體資源授與有限存取權](../articles/storage/common/storage-sas-overview.md)。

> [!IMPORTANT]
> Azure Data Factory 現在僅支援 **服務 SAS**，但不支援帳戶 SAS。 請注意，可從 Azure 入口網站或儲存體總管產生的 SAS URL 是帳戶 SAS (不提供支援)。

> [!TIP]
> 您可以執行下列 PowerShell 命令來產生儲存體帳戶的「服務 SAS」(取代預留位置和授與所需權限)：`$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Azure 儲存體 SAS 連結服務可讓您使用共用存取簽章 (SAS)，將 Azure 儲存體帳戶連結到 Azure Data Factory。 它提供受限制/時間界限存取權，讓資料處理站存取儲存體中的所有/特定資源 (blob/容器)。 下表提供 Azure 儲存體 SAS 連結服務專屬 JSON 元素的描述。 

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type |類型屬性必須設為： **AzureStorageSas** |是 |
| sasUri |指定 Azure 儲存體資源 (例如 Blob、容器或資料表) 的共用存取簽章 URI。  |是 |

**例子：**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

建立 **SAS URI**時，請考慮下列事項：  

* 根據資料工廠中的連結服務（讀取、寫入、讀取/寫入）的使用方式，設置物件的適當的讀/寫**許可權**。
* 設定適當的 [過期時間]****。 請確定 Azure 儲存體物件的存取權不會在管線的作用中期間內過期。
* URI 應根據需要在正確的容器/Blob 或資料表層級建立。 Azure Blob 的 SAS URI 可允許 Data Factory 服務存取該特定的 Blob。 Azure Blob 容器的 SAS URI 可允許 Data Factory 服務逐一查看該容器中的 Blob。 如果您稍後需要提供更多/較少物件的存取權，或需要更新 SAS URI，請記得使用新的 URI 更新連結的服務。   

