---
title: 將資料複製到 Azure 資料資源管理器或從 Azure 資料資源管理器中複製
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料複製到 Azure 資料總管或從該處複製資料。
services: data-factory
ms.author: orspodek
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 4c265cb0cdc665ef52f4dc6e69440e83c22db449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460968"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>使用 Azure 資料工廠將資料複製到或從 Azure 資料資源管理器中複製資料

本文介紹如何使用 Azure 資料工廠中的複製活動將資料複製到 Azure 資料資源管理器或從[Azure 資料資源管理器中複製](../data-explorer/data-explorer-overview.md)。 它基於[複製活動概述](copy-activity-overview.md)文章，其中提供了複製活動的一般概述。

>[!TIP]
>對於 Azure 資料工廠和 Azure 資料資源管理器集成，從[將 Azure 資料資源管理器與 Azure 資料工廠集成](../data-explorer/data-factory-integration.md)中瞭解詳細資訊。

## <a name="supported-capabilities"></a>支援的功能

以下活動支援此 Azure 資料資源管理器連接器：

- 使用[支援的源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)

您可以將資料從任何支援的來源資料存放區複製到 Azure 資料總管。 您也可以將資料從 Azure 資料總管複製到任何支援的接收資料存放區。 有關複製活動支援為源或接收器的資料存儲的清單，請參閱[支援資料存儲](copy-activity-overview.md#supported-data-stores-and-formats)表。

>[!NOTE]
>版本 3.14 及更高版本支援使用自託管集成運行時將資料複製到或從 Azure 資料資源管理器中通過本地資料存儲複製資料。

使用 Azure 資料資源管理器連接器，可以執行以下操作：

* 使用 Azure Active Directory (Azure AD) 應用程式權杖驗證搭配**服務主體**來複製資料。
* 作為來源時，請使用 KQL (Kusto) 查詢擷取資料。
* 作為接收時，請將資料附加至目的地資料表。

## <a name="getting-started"></a>開始使用

>[!TIP]
>有關 Azure 資料資源管理器連接器的演練，請參閱[使用 Azure 資料工廠將資料從 Azure 資料資源管理器複製資料，](../data-explorer/data-factory-load-data.md)並將[批量副本從資料庫複製到 Azure 資料資源管理器](../data-explorer/data-factory-template.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供有關屬性的詳細資料，這些屬性會用來定義 Azure 資料總管連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Azure 資料資源管理器連接器使用服務主體身份驗證。 按照以下步驟獲取服務主體並授予許可權：

1. 按照 Azure [AD 租戶註冊應用程式](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)的步驟，在 Azure 活動目錄中註冊應用程式實體。 請記下以下的值，您可以使用這些值來定義連結服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 在 Azure 資料資源管理器中授予服務主體正確的許可權。 有關角色和許可權以及管理許可權的詳細資訊，請參閱[管理 Azure 資料資源管理器資料庫許可權](../data-explorer/manage-database-permissions.md)。 通常，您必須：

    - **作為源**，至少將**資料庫檢視器**角色授予資料庫
    - **作為接收器**，至少將**資料庫引入者**角色授予資料庫

>[!NOTE]
>使用資料工廠 UI 創作時，登錄使用者帳戶用於列出 Azure 資料資源管理器群集、資料庫和表。 如果您沒有這些操作的許可權，請手動輸入名稱。

Azure 資料資源管理器連結服務支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **類型**屬性必須設置為 Azure**資料資源管理器**。 | 是 |
| 端點 | Azure 資料總管叢集的端點 URL，格式為 `https://<clusterName>.<regionName>.kusto.windows.net`。 | 是 |
| [資料庫] | 資料庫名稱。 | 是 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 這在[Kusto 連接字串](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)中稱為"權威 ID"。 通過將滑鼠指標懸停在 Azure 門戶的右上角來檢索它。 | 是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 這在[Kusto 連接字串](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)中稱為"AAD 應用程式用戶端 ID"。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 這在[Kusto 連接字串](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)中稱為"AAD 應用程式金鑰"。 將此欄位標記為**安全字串**，以將其安全地存儲在資料工廠中，或[引用存儲在 Azure 金鑰保存庫中的安全資料](store-credentials-in-key-vault.md)。 | 是 |

**連結服務屬性示例：**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

有關可用於定義資料集的節和屬性的完整清單，請參閱 Azure[資料工廠 中的資料集](concepts-datasets-linked-services.md)。 本節列出了 Azure 資料資源管理器資料集支援的屬性。

若要將資料複製到 Azure 資料總管，請將資料集的 type 屬性設定為 **AzureDataExplorerTable**。

以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **類型**屬性必須設置為**AzureDataExplorerTable**。 | 是 |
| 資料表 | 連結服務所參考的資料表名稱。 | Yes (接收)：No (來源) |

**資料集屬性示例：**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

有關可用於定義活動的節和屬性的完整清單，請參閱 Azure[資料工廠中的管道和活動](concepts-pipelines-activities.md)。 本節提供 Azure 資料資源管理器源和接收器支援的屬性的清單。

### <a name="azure-data-explorer-as-source"></a>作為來源的 Azure 資料總管

若要從 Azure 資料總管複製資料，請將複製活動來源中的 **type** 屬性設定為 **AzureDataExplorerSource**。 複製活動**源**部分支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須將複製活動源**的類型**屬性設置為 **：AzureDataExplorerSource** | 是 |
| 查詢 | [KQL 格式](/azure/kusto/query/)中指定的唯讀要求。 使用自訂的 KQL 查詢作為參考。 | 是 |
| queryTimeout | 查詢請求超時前的等待時間。預設值為 10 分鐘（00：10：00）;允許的最大值為 1 小時（01：00：00）。 | 否 |
| 不流 | 指示是否截截返回的結果集。 預設情況下，結果在 500，000 條記錄或 64 百萬位元組 （MB） 後被截斷。 強烈建議進行截斷，以確保活動的正確行為。 |否 |

>[!NOTE]
>預設情況下，Azure 資料資源管理器源的大小限制為 500，000 條記錄或 64 MB。 若要檢索所有記錄而不進行截斷，可以在查詢的開頭`set notruncation;`指定。 有關詳細資訊，請參閱[查詢限制](https://docs.microsoft.com/azure/kusto/concepts/querylimits)。

**例子：**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>作為接收的 Azure 資料總管

若要將資料複製到 Azure 資料總管，請將複製活動接收中的 type 屬性設定為 **AzureDataExplorerSink**。 複製活動**接收器**部分支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須將複製活動接收器**的類型**屬性設置為 **：AzureDataExplorerSink**。 | 是 |
| ingestionMappingName | 庫斯托表上預先創建的[映射](/azure/kusto/management/mappings#csv-mapping)的名稱。 要將列從源映射到 Azure 資料資源管理器（適用于[所有受支援的源存儲和格式](copy-activity-overview.md#supported-data-stores-and-formats)（包括 CSV/JSON/Avro 格式），可以使用複製活動[列映射](copy-activity-schema-and-type-mapping.md)（隱式名稱或顯式表示為配置）和/或 Azure 資料資源管理器映射。 | 否 |
| 其他屬性 | 屬性包，可用於指定 Azure 資料資源管理器接收器尚未設置的任何引入屬性。 具體來說，它可用於指定引入標記。 從[Azure 資料流覽資料引入文檔](https://kusto.azurewebsites.net/docs/management/data-ingestion/index.html)中瞭解更多資訊。 | 否 |

**例子：**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>查找活動屬性

有關屬性的詳細資訊，請參閱[查找活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟

* 有關 Azure 資料工廠中複製活動作為源和接收器支援的資料存儲的清單，請參閱[受支援的資料存儲](copy-activity-overview.md#supported-data-stores-and-formats)。

* 詳細瞭解如何[將資料從 Azure 資料工廠複製到 Azure 資料資源管理器](/azure/data-explorer/data-factory-load-data)。
