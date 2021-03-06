---
title: 將資料複製到搜索索引
description: 了解如何使用 Azure Data Factory 管線中的「複製活動」，將資料推送或複製到 Azure 搜尋服務索引。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/13/2019
ms.openlocfilehash: 418026d5569cd7e4a7c5239f99650833b1b9514d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892929"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>使用 Azure 資料工廠將資料複製到 Azure 認知搜索索引

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [版本 1](v1/data-factory-azure-search-connector.md)
> * [當前版本](connector-azure-search.md)

本文概述了如何使用 Azure 資料工廠中的複製活動將資料複製到 Azure 認知搜索索引中。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從任何受支援的來源資料存儲複製到搜索索引中。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各節提供有關用於定義特定于 Azure 認知搜尋連接器的資料工廠實體的屬性的詳細資訊。

## <a name="linked-service-properties"></a>連結服務屬性

Azure 認知搜索連結服務支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**AzureSearch** | 是 |
| url | 搜索服務的 URL。 | 是 |
| 索引鍵 | 搜索服務的管理金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或「自我裝載 Integration Runtime」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

> [!IMPORTANT]
> 將雲資料存儲中的資料複製到搜索索引時，在 Azure 認知搜索連結服務中，需要引用 Azure 集成運行時，其中具有 connactVia 中的顯式區域。 將地區設定為搜索服務所在的區域。 請參閱 [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime) 以深入了解。

**例子：**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

有關可用於定義資料集的節和屬性的完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Azure 認知搜索資料集支援的屬性清單。

要將資料複製到 Azure 認知搜索，支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**AzureSearchIndex** | 是 |
| IndexName | 搜索索引的名稱。 Data Factory 不會建立索引。 索引必須存在於 Azure 認知搜索中。 | 是 |

**例子：**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Azure 認知搜索源支援的屬性清單。

### <a name="azure-cognitive-search-as-sink"></a>Azure 認知搜索作為接收器

要將資料複製到 Azure 認知搜索，請將複製活動中的源類型設置為**AzureSearchIndexSink**。 複製活動**接收器**部分支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**AzureSearchIndexSink** | 是 |
| writeBehavior | 指定若文件已經存在於索引中，是否要合併或取代。 請參閱 [WriteBehavior 屬性](#writebehavior-property)。<br/><br/>允許的值為：**Merge** (預設值) 和 **Upload**。 | 否 |
| writeBatchSize | 當緩衝區大小達到 writeBatchSize 時，將資料上載到搜索索引中。 如需詳細資訊，請參閱 [WriteBatchSize 屬性](#writebatchsize-property)。<br/><br/>允許的值為：整數 1 到 1,000；預設值為 1000。 | 否 |

### <a name="writebehavior-property"></a>WriteBehavior 屬性

AzureSearchSink 會在寫入資料時更新插入。 換句話說，在編寫文檔時，如果搜索索引中已存在文檔金鑰，Azure 認知搜索將更新現有文檔，而不是引發衝突異常。

AzureSearchSink (藉由使用 AzureSearch SDK) 提供下列兩種更新插入行為：

- **合併**︰將新文件中的所有資料行與現有的文件相結合。 對於新文件中含有 null 值的資料行，則會保留現有文件中的值。
- **上傳**：新的文件會取代現有的文件。 針對新文件中未指定的資料行，不論現有的文件中是否具有非 null 的值，都會將值設為 null。

預設行為是**合併**。

### <a name="writebatchsize-property"></a>WriteBatchSize 屬性

Azure 認知搜索服務支援將文檔編寫為批次處理。 一個批次可包含 1 到 1,000 個動作。 一個動作可指示一份文件來執行上傳/合併作業。

**例子：**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Cognitive Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>資料類型支援

下表指定是否支援 Azure 認知搜索資料類型。

| Azure 認知搜索資料類型 | Azure 認知搜索接收器中支援 |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| Boolean | Y |
| DataTimeOffset | Y |
| 字串陣列 | N |
| GeographyPoint | N |

當前不支援其他資料類型，例如複雜類型。 有關 Azure 認知搜索支援的資料類型的完整清單，請參閱[支援的資料類型（Azure 認知搜索）。](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
