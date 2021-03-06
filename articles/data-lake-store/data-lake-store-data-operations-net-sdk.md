---
title: .NET SDK：Azure 資料存儲第 1 代上的檔案系統操作
description: 使用 Azure 資料存儲庫第 1 代 .NET SDK 對資料存儲第 1 代（如創建資料夾等）的檔案系統操作。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 7e33ecbbb49fc2b0683d0757da36deec72796806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638896"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>使用 .NET SDK 對資料存儲第 1 代的檔案系統操作

> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [JAVA SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

在本文中，您將瞭解如何使用 .NET SDK 對資料存儲第 1 代執行檔案系統操作。 檔案系統作業包括在 Data Lake Storage Gen1 帳戶中建立資料夾、上傳檔案、下載檔案等。

有關如何使用 .NET SDK 對資料存儲第 1 代執行帳戶管理操作的說明，請參閱[使用 .NET SDK 對資料存儲第 1 代的帳戶管理操作](data-lake-store-get-started-net-sdk.md)。

## <a name="prerequisites"></a>Prerequisites

* **視覺工作室 2013 或以上**. 本文中的說明使用 Visual Studio 2019。

* **Azure 訂閱**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure 資料存儲第 1 代帳戶**。 有關如何創建帳戶的說明，請參閱[開始使用 Azure 資料湖存儲 Gen1](data-lake-store-get-started-portal.md)。

## <a name="create-a-net-application"></a>建立 .NET 應用程式

[GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) 上可用的程式碼範例會逐步引導您完成在存放區中建立檔案，串連檔案、下載檔案，以及刪除存放區中一些檔案的程序。 本節文章會逐步解說程式碼的主要部分。

1. 在視覺化工作室中，選擇 **"新建****"** 和"**專案**"功能表。
1. 選擇**主控台應用 （.NET 框架），** 然後選擇 **"下一步**"。
1. 在 [專案名稱]**** 中，輸入 `CreateADLApplication`，然後選取 [建立]****。
1. 將 NuGet 套件新增至您的專案。

   1. 在方案總管中以滑鼠右鍵按一下專案名稱，然後按一下 [ **管理 NuGet 封裝**]。
   1. 在**NuGet 包管理器**選項卡中，請確保**包源**設置為**nuget.org**。此外，請確保選中"**包括預發佈**"核取方塊。
   1. 搜尋並安裝下列 NuGet 封裝：

      * `Microsoft.Azure.DataLake.Store`- 本文使用 v1.0.0。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`- 本文使用 v2.3.1。

      關閉**NuGet 包管理器**。

1. 開啟 **Program.cs**，刪除現有的程式碼，然後納入下列陳述式以新增命名空間的參考。

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. 宣告變數如下，並提供預留位置的值。 此外，請確定電腦上有此處提供的本機路徑和檔案名稱。

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

在本文的其餘部分中，您可以看到如何使用可用的 .NET 方法執行身份驗證、檔上載等操作。

## <a name="authentication"></a>驗證

* 如需讓應用程式進行使用者驗證，請參閱[使用 .NET SDK 向 Data Lake Storage Gen1 進行使用者驗證](data-lake-store-end-user-authenticate-net-sdk.md)。
* 如需讓應用程式進行服務對服務驗證，請參閱[使用 .NET SDK 向 Data Lake Storage Gen1 進行服務對服務驗證](data-lake-store-service-to-service-authenticate-net-sdk.md)。

## <a name="create-client-object"></a>建立用戶端物件

下列程式碼片段會建立 Data Lake Storage Gen1 檔案系統用戶端物件，以便對服務發出要求。

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>建立檔案和目錄

將下列程式碼片段新增至應用程式。 此程式碼片段添加檔和不存在的任何父目錄。

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>附加到檔案

下列程式碼片段會將資料附加至 Data Lake Storage Gen1 帳戶中的現有檔案。

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>讀取檔案

下列程式碼片段會讀取 Data Lake Storage Gen1 中的檔案內容。

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>取得檔案屬性

下列程式碼片段會傳回與檔案或目錄相關聯的屬性。

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

`PrintDirectoryEntry`該方法的定義作為[GitHub 上](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)的示例的一部分提供。

## <a name="rename-a-file"></a>重新命名檔案

下列程式碼片段會重新命名 Data Lake Storage Gen1 帳戶中的現有檔案。

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>列舉目錄

以下程式碼片段枚舉了資料湖存儲 Gen1 帳戶中的目錄。

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

`PrintDirectoryEntry`該方法的定義作為[GitHub 上](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)的示例的一部分提供。

## <a name="delete-directories-recursively"></a>以遞迴方式刪除目錄

以下程式碼片段會遞迴地刪除目錄及其所有子目錄。

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>範例

下面是一些示例，演示如何使用資料存儲存儲 Gen1 檔案系統 SDK。

* [GitHub 上的基本示例](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [GitHub 上的高級示例](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>另請參閱

* [使用.NET SDK 在 Data Lake Storage Gen1 上進行帳戶管理作業](data-lake-store-get-started-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK 參考](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>後續步驟

* [保護 Data Lake Storage Gen1 中的資料](data-lake-store-secure-data.md)
