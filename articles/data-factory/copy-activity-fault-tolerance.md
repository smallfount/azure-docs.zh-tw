---
title: Azure Data Factory 中複製活動的容錯
description: 了解如何跳過不相容的資料列，以在 Azure Data Factory 的複製活動中新增容錯。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: 42c637839172dab09a8721a93a67785a748afd2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75708897"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Azure Data Factory 中複製活動的容錯
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [版本 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [當前版本](copy-activity-fault-tolerance.md)

Azure Data Factory 中的複製活動可在來源和接收資料存放區之間複製資料時，提供您兩個方式來處理不相容的資料列：

- 遇到不相容的資料時，您可以中止並捨棄複製活動 (預設行為)。
- 您可以繼續複製所有的資料，方法是新增容錯並跳過不相容的資料列。 此外，您可以在 Azure Blob 儲存體或 Azure Data Lake Store 中記錄不相容的資料列。 接著，您可以檢查記錄來了解失敗的原因、修正資料來源上的資料，並重試複製活動。

## <a name="supported-scenarios"></a>支援的案例
複製活動支援三種情節，以偵測、跳過並記錄不相容的資料：

- **源資料類型和接收器本機類型之間的不相容**。 

    例如：使用包含三種 INT 類型資料行的結構描述定義，從 Blob 儲存體中的 CSV 檔案將資料複製到 SQL 資料庫。 包含數值資料的 CSV 檔案資料列 (例如 123,456,789) 會成功複製到接收存放區。 不過，包含非數值的資料列 (例如 123,456, abc) 會偵測為不相容並加以跳過。

- **源和接收器之間的列數不匹配**。

    例如：使用包含六個資料行的結構描述定義，從 Blob 儲存體中的 CSV 檔案將資料複製到 SQL 資料庫。 包含六個資料行的 CSV 檔案資料列會成功複製到接收存放區。 包含六列以上的 CSV 檔行將檢測為不相容，並跳過。

- **寫入 SQL 伺服器/Azure SQL 資料庫/Azure 宇宙資料庫 時的主要金鑰衝突**。

    例如：從 SQL Server 將資料複製到 SQL 資料庫。 會在接收 SQL 資料庫中定義主索引鍵，但是在來源 SQL Server 中不會定義這類主索引鍵。 無法將來源中的重複資料列複製到接收。 複製活動只會將來源資料中的第一個資料列複製到接收。 包含重複主索引鍵值的後續來源資料列會偵測為不相容，並加以跳過。

>[!NOTE]
>- 如需使用 PolyBase 將資料載入到 SQL 資料倉儲，請透過複製活動中的 "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" 來指定拒絕原則，以設定 PolyBase 的原生容錯設定。 如往常，您仍然可以啟用將 PolyBase 不相容的資料列重新導向到 Blob 或 ADLS 的功能，如下所示。
>- 當複製活動設定為叫用 [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)時，不會套用此功能。
>- 當複製活動配置為[從 SQL 接收器調用預存程序時，](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink)此功能不適用。

## <a name="configuration"></a>組態
下列範例提供的 JSON 定義，可設定在複製活動中跳過不相容資料列：

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

屬性 | 描述 | 允許的值 | 必要
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | 指定是否要在複製期間略過不相容的資料列。 | True<br/>FALSE (預設值) | 否
redirectIncompatibleRowSettings | 當您想要記錄不相容的資料列時，可指定的一組屬性。 | &nbsp; | 否
linkedServiceName | [Azure 儲存體](connector-azure-blob-storage.md#linked-service-properties)或 [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) 的連結服務，儲存包含跳過之資料列的記錄。 | `AzureStorage` 或 `AzureDataLakeStore` 類型連結服務的名稱，以代表您需要用來儲存記錄檔的儲存體執行個體。 | 否
path | 包含跳過之資料列的記錄檔路徑。 | 指定需要用來記錄不相容資料的路徑。 如不提供路徑，服務會為您建立容器。 | 否

## <a name="monitor-skipped-rows"></a>監視略過的資料列
複製活動執行完成之後，您會在複製活動的輸出中看到略過的資料列數目：

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
如果您設定記錄不相容的資料列，您可以在此路徑中找到記錄檔：`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`。 

記錄檔只能是 csv 檔案。 若有需要，系統會使用逗號做為資料行分隔符號，將目前跳過的原始資料記錄下來。 記錄檔中除了原始的來源資料之外，還額外新增了「ErrorCode」與「ErrorMessage」兩個資料行，可在其中查看不相容問題的根本原因。 系統會以雙引號標註 ErrorCode 與 ErrorMessage。 

記錄檔內容範例如下所示：

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概述](copy-activity-overview.md)
- [複製活動效能](copy-activity-performance.md)


