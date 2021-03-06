---
title: 建立技能集
titleSuffix: Azure Cognitive Search
description: 定義資料提取、自然語言處理或圖像分析步驟，以豐富和提取資料中的結構化資訊，以便用於 Azure 認知搜索。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 43251783cbcd6501562913b7b9cafb4f9f7cb3f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754559"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>如何在 Azure 認知搜索中的 AI 擴充管道中創建技能集 

AI 富集提取和豐富資料，使其在 Azure 認知搜索中可搜索。 我們將擷取和擴充步驟稱為*認知技能*，而這些技能會合併為在索引編製期間所參考的*技能集*。 技能集可以使用[內置技能](cognitive-search-predefined-skills.md)或自訂技能（請參閱[示例：有關詳細資訊，在 AI 充實管道中創建自訂技能](cognitive-search-create-custom-skill-example.md)）。

在本文中，您將了解如何為您要使用的技能建立擴充管線。 技能集附加到 Azure 認知搜索[索引子](search-indexer-overview.md)。 在本文的說明中，建構技能集本身屬於管線設計的一部分。 

> [!NOTE]
> 管線設計的另一個部分是指定索引子，如[下一個步驟](#next-step)所說明。 索引子定義包含對技能集的參考，以及將輸入連線至目標索引中的輸出時所使用的欄位對應。

須留意的重點：

+ 每個索引子只能有一個技能集。
+ 一個技能集至少必須有一個技能。
+ 您可以建立多個相同類型的技能 (例如，影像分析技能的變體)。

## <a name="begin-with-the-end-in-mind"></a>開始前請先預想結果

建議的初始步驟是決定要從原始資料中擷取何種資料，以及如何將該資料用於搜尋解決方案中。 建立整個擴充管線的圖例，有助於識別必要的步驟。

假設您想要處理一組財務分析師評論。 對於每個檔案，您想要擷取這些評論的公司名稱和一般意見。 您也可以撰寫自訂擴充程式，以使用 Bing 實體搜尋服務來尋找公司的其他相關資訊，例如公司所從事的是哪方面的業務。 基本上，您會想要擷取已針對每個文件編製索引的如下資訊：

| 記錄文字 | 公司 | 情感 | 公司說明 |
|--------|-----|-----|-----|
|範例記錄| ["Microsoft", "LinkedIn"] | 0.99 | ["Microsoft Corporation is an American multinational technology company ..." , "LinkedIn is a business- and employment-oriented social networking..."]

下圖說明在假設性的擴充管線：

![假想的濃縮管道](media/cognitive-search-defining-skillset/sample-skillset.png "假想的濃縮管道")


在您了解要在管線中放置哪些項目後，即可呈現出提供這些步驟的技能集。 在功能上，當您將索引子定義上載到 Azure 認知搜索時，將表示技能集。 若要深入了解如何上傳您的索引子，請參閱[索引子文件](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。


在上圖中，*文件萃取*步驟會自動執行。 從本質上講，Azure 認知搜索知道如何打開已知檔並創建包含從每個文檔中提取的文本*的內容*欄位。 白色方塊是內建的擴充程式，而虛線的「Bing 實體搜尋」方塊則表示您要建立的自訂擴充程式。 如圖所示，技能集包含三項技能。

## <a name="skillset-definition-in-rest"></a>REST 中的技能集定義

技能集會定義為技能的陣列。 每項技能分別定義其輸入的來源和所產生的輸出名稱。 使用[建立技能集 REST API](https://docs.microsoft.com/rest/api/searchservice/create-skillset)，可以定義對應於上圖的技能集： 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>建立技能集

在建立技能集時，您可以提供說明，使技能集自我文件化。 說明是選擇性的，但可用來追蹤技能集的用途。 由於技能集是 JSON 文件，不允許註解，因此為此您必須使用 `description` 元素。

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

技能集中的下一組件是技能的陣列。 您可以將每項技能視為擴充的基礎。 每項技能都會在此擴充管線中執行一項小型工作。 每項工作都會有一項輸入 (或一組輸入)，然後傳回某些輸出。 接下來的幾節重點介紹如何指定內置和自訂技能，通過輸入和輸出引用將技能連結在一起。 輸入可來自於來源資料或是其他技能。 輸出可以對應至搜尋索引中的欄位，或作為下游技能的輸入。

## <a name="add-built-in-skills"></a>添加內置技能

讓我們來看看第一個技能，即內置[的實體識別技能](cognitive-search-skill-entity-recognition.md)：

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* 每個內置技能都有`odata.type`，`input`和`output`屬性。 技能特有的屬性可提供適用於該技能的其他資訊。 就實體辨識而言，`categories` 是預先定型的模型可從一組固定的實體類型中辨識出來的實體。

* 每項技能都應有 ```"context"```。 內容表示作業執行的層級。 在上面的技能中，上下文是整個文檔，這意味著每個文檔調用實體識別技能一次。 輸出也會在該層級上產生。 更具體來說，```"organizations"``` 會產生作為 ```"/document"``` 的成員。 在下游技能中，您可以將這項新建立的資訊稱為 ```"/document/organizations"```。  如果 ```"context"``` 欄位未明確設定，預設內容將是文件。

* 技能有一個名為「文字」的輸入，其來源輸入設定為 ```"/document/content"```。 技能（實體識別）在每個文檔*的內容*欄位上運行，該欄位是 Azure blob 索引子創建的標準欄位。 

* 技能會有一個名為 ```"organizations"``` 的輸出。 只有在處理期間才會有輸出。 若要將此輸出鏈結至下游技能的輸入，請將輸出作為 ```"/document/organizations"``` 來參考。

* 對於特定文件，```"/document/organizations"``` 的值是從文字中擷取的組織陣列。 例如：

  ```json
  ["Microsoft", "LinkedIn"]
  ```

某些情況下，會需要個別參考陣列的每個元素。 例如，假設您想要個別將 ```"/document/organizations"``` 的每個元素傳遞至另一項技能 (例如自訂的 Bing 實體搜尋擴充程式)。 您可以藉由在路徑中加上星號，來參照陣列的每個元素：```"/document/organizations/*"``` 

情感擷取的第二項技能採用與第一個擴充程式相同的模式。 它會以 ```"/document/content"``` 作為輸入，並傳回每個內容執行個體的情感分數。 由於您並未明確設定 ```"context"``` 欄位，此時的輸出 (mySentiment) 會是 ```"/document"``` 的子系。

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>新增自訂技能

先前曾經提到自訂 Bing 實體搜尋擴充程式的結構：

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

此定義是一種[自訂技能](cognitive-search-custom-skill-web-api.md)，用於調用 Web API 作為豐富過程的一部分。 對於實體識別標識的每個組織，此技能調用 Web API 來查找該組織的描述。 何時應呼叫 Web API 以及如何傳輸接收到的資訊等協調流程，由擴充引擎在內部處理。 不過，呼叫此自訂 API 所需的初始化，必須提供於 JSON 中 (例如 URI、httpHeaders 和預期的輸入)。 如需為擴充管線建立自訂 Web API 的指引，請參閱[如何定義自訂介面](cognitive-search-custom-skill-interface.md)。

請注意，[內容] 欄位設定為附有星號的 ```"/document/organizations/*"```，這表示擴充步驟是對 ```"/document/organizations"``` 下的「每個」** 組織而呼叫的。 

系統會為所識別的每個組織產生輸出 (在此案例中為公司說明)。 在下游步驟中參照說明時 (例如，在擷取關鍵片語時)，您會使用路徑 ```"/document/organizations/*/description"``` 來執行。 

## <a name="add-structure"></a>添加結構

技能集會從非結構化資料產生結構化資訊。 請考慮下列範例：

*在第四季度，微軟去年收購的社交網路公司LinkedIn（LinkedIn）的收入達到11億美元。通過此次收購，Microsoft 能夠將LinkedIn功能與其 CRM 和 Office 功能相結合。股東們對迄今為止的進展感到興奮。*

可能的結果將是類似於下圖的產出結構：

![樣品輸出結構](media/cognitive-search-defining-skillset/enriched-doc.png "樣品輸出結構")

到目前為止，此結構是僅內部的，僅記憶體，並且僅在 Azure 認知搜索索引中使用。 添加知識存儲後，您可以保存形狀豐富的以用於搜索之外。

## <a name="add-a-knowledge-store"></a>添加知識存儲

[知識存儲](knowledge-store-concept-intro.md)是 Azure 認知搜索中的預覽功能，用於保存豐富的文檔。 創建的知識存儲庫由 Azure 存儲帳戶支援，是豐富資料所在的存儲庫。 

知識存儲定義將添加到技能集中。 有關整個過程的演練，請參閱在 REST[中創建知識存儲](knowledge-store-create-rest.md)。

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

您可以選擇將富集文檔保存為保留層次結構關係的表，也可以將其保存為 blob 存儲中的 JSON 文檔。 技能集中的任何技能的輸出都可以作為投影的輸入來源。 如果要將資料投影到特定形狀中，則更新後的[Shaper 技能](cognitive-search-skill-shaper.md)現在可以對複雜類型建模，供您使用。 

<a name="next-step"></a>

## <a name="next-steps"></a>後續步驟

現在，您已熟悉擴充管線和技能集，接著請繼續進行[如何參考技能集中的註解](cognitive-search-concept-annotations-syntax.md)或[如何將輸出對應至索引中的欄位](cognitive-search-output-field-mapping.md)。 
