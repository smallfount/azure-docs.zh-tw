---
title: 搜索 Azure 表存儲內容
titleSuffix: Azure Cognitive Search
description: 瞭解如何使用 Azure 認知搜索索引子對存儲在 Azure 表存儲中的資料進行索引。
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e8f6c0454497b1cb1d62417e566e9662469c56d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112992"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>如何通過 Azure 認知搜索從 Azure 表存儲中索引表

本文演示如何使用 Azure 認知搜索來索引存儲在 Azure 表存儲中的資料。

## <a name="set-up-azure-table-storage-indexing"></a>設定 Azure 表格儲存體編制索引

您可以使用下列資源設定 Azure 表格儲存體索引子︰

* [Azure 門戶](https://ms.portal.azure.com)
* Azure 認知搜索[REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure 認知搜索[.NET SDK](https://aka.ms/search-sdk)

我們會在此使用 REST API 示範流程。 

### <a name="step-1-create-a-datasource"></a>步驟 1：建立資料來源

資料來源指定要索引的資料、訪問資料所需的憑據以及使 Azure 認知搜索能夠有效地標識資料更改的策略。

若要為資料表編製索引，資料來源必須具有下列屬性︰

- **name** 是搜尋服務中資料來源的唯一名稱。
- **type** 必須是 `azuretable`。
- **credentials** 參數包含儲存體帳戶連接字串。 請參閱[指定認證](#Credentials)一節了解詳細資訊。
- **容器**設置表名稱和可選查詢。
    - 使用 `name` 參數指定資料表名稱。
    - (選擇性) 使用 `query` 參數指定查詢。 

> [!IMPORTANT] 
> 可能的話，在 Partitionkey 使用篩選以提升效能。 任何其他查詢會進行完整的資料表掃描，導致大型資料表的效能不佳。 請參閱[效能考量](#Performance)一節。


若要建立資料來源：

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

如需建立資料來源 API 的詳細資訊，請參閱[建立資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)。

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>指定認證的方法 ####

您可以採取下列其中一種方式提供資料表的認證︰ 

- **完全訪問存儲帳戶連接字串** `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` ：您可以通過訪問**存儲帳戶邊欄選項卡** > **設置** > **鍵**（用於經典存儲帳戶）或**設置** > **訪問金鑰**（用於 Azure 資源管理器存儲帳戶）從 Azure 門戶獲取連接字串。
- **存儲帳戶共用訪問簽名連接字串**：`TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl`共用訪問簽名應具有容器（本例中的表）和物件（表行）的清單和讀取權限。
-  **表共用訪問簽名**：`ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r`共用訪問簽名應具有表上的查詢（讀取）許可權。

如需儲存體共用存取簽章的詳細資訊，請參閱[使用共用存取簽章](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

> [!NOTE]
> 如果您使用共用存取簽章認證，您必須使用更新的簽章定期更新資料來源認證以防止其到期。 如果共用存取簽章認證過期，索引子會失敗並出現類似以下的錯誤訊息：「連接字串中提供的認證無效或已過期」。  

### <a name="step-2-create-an-index"></a>步驟 2：建立索引
索引會指定文件、屬性和其他建構中可形塑搜尋體驗的欄位。

若要建立索引：

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

如需建立索引的詳細資訊，請參閱[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)。

### <a name="step-3-create-an-indexer"></a>步驟 3：建立索引子
索引子會以目標搜尋索引連線資料來源，並提供排程來自動重新整理資料。 

建立索引和資料來源之後，您就可以開始建立索引子︰

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

這個索引子會每隔兩小時執行一次。 （計畫間隔設置為"PT2H"。要每 30 分鐘運行一次索引子，將間隔設置為"PT30M"。 支援的最短間隔為 5 分鐘。 排程為選擇性；如果省略，索引子只會在建立時執行一次。 不過，您隨時都可依需求執行索引子。   

如需建立索引子 API 的詳細資訊，請參閱[建立索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。

有關定義索引子計畫的詳細資訊，請參閱[如何為 Azure 認知搜索計畫索引子](search-howto-schedule-indexers.md)。

## <a name="deal-with-different-field-names"></a>處理不同的欄位名稱
有時候，現有索引中的欄位名稱與資料表中的屬性名稱不同。 您可以使用欄位對應，將資料表中的屬性名稱對應至您搜尋索引中的欄位名稱。 要瞭解有關欄位映射的更多資訊，請參閱[Azure 認知搜索索引子欄位映射，將資料來源和搜索索引之間的差異橋接](search-indexer-field-mappings.md)。

## <a name="handle-document-keys"></a>處理文件索引鍵
在 Azure 認知搜索中，文檔金鑰唯一標識文檔。 每個搜尋索引必須確實具有一個 `Edm.String`類型的索引鍵欄位。 要新增至索引的每個文件都需要有索引鍵欄位。 (實際上它是唯一必要的欄位。)

由於表行具有複合鍵，Azure 認知搜索將生成稱為`Key`分區鍵和行鍵值串聯的綜合欄位。 例如，如果資料列的 PartitionKey 為 `PK1` 且 RowKey 是 `RK1`，則 `Key` 欄位的值是 `PK1RK1`。

> [!NOTE]
> `Key` 值可能包含在文件索引鍵中無效的字元，例如連字號。 您可以使用 `base64Encode` [欄位對應函式](search-indexer-field-mappings.md#base64EncodeFunction)來處理無效字元。 如果您這樣做，請記得在 API 呼叫 (例如查閱) 中傳遞文件索引鍵時，也使用 URL 安全 Base64 編碼。
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>增量編製索引和刪除偵測
當您將資料表索引子設為依排程執行時，它會根據資料列的 `Timestamp` 值來決定是否只對新的或已更新過的資料列重新編製索引。 您不需要指定變更偵測原則。 系統會自動為您啟用增量編制索引。

若要指示必須從索引中移除特定文件，您可以使用虛刪除策略。 新增屬性來表示它已遭到刪除，而非刪除資料列，並在資料來源上設定虛刪除偵測原則。 例如，如果資料列有屬性 `IsDeleted` 的值為 `"true"`，則下列原則會認為已刪除資料列：

    PUT https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>效能考量

預設情況下，Azure 認知搜索使用以下查詢篩選器： `Timestamp >= HighWaterMarkValue`。 因為 Azure 資料表的 `Timestamp` 欄位沒有次要索引，這種類型的查詢需要完整的資料表掃描，因此若是大型資料表就會變慢。


以下是加強資料表索引效能兩個可能的做法。 這兩種做法都依賴使用資料表資料分割︰ 

- 如果您的資料可以自然地分割成幾個資料分割範圍，請建立資料來源以及與每個資料分割範圍對應的索引子。 每個索引子現在可以只處理某個特定資料分割範圍，進而提升查詢效能。 如果需要編製索引的資料有少量的固定資料分割，那更好：每個索引子只要執行一次資料分割掃描。 例如，要建立資料來源來處理金鑰 `000` 至 `100` 的資料分割範圍，可使用如下的查詢︰ 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- 如果您的資料是依時間分割 (例如，每日或每週建立新的資料分割)，請考慮下列做法︰ 
    - 使用這個形式的查詢︰`(PartitionKey ge <TimeStamp>) and (other filters)`。 
    - 使用 [Get Indexer Status API](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) 監視索引子進度，並根據最新成功的上限標準值定期更新查詢的 `<TimeStamp>` 條件。 
    - 採取此做法時，如果您需要觸發完整的重新索引，則除了重設索引子之外，還必須重設資料來源查詢。 


## <a name="help-us-make-azure-cognitive-search-better"></a>説明我們改進 Azure 認知搜索
如果您有功能要求或改進的想法，請在我們的 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search/)提出。
