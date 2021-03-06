---
title: 將 Azure 資料存儲從第 1 代遷移到第 2 代
description: 將 Azure 資料存儲從第 1 代遷移到第 2 代。
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: fb982324b66c5ac0d2db00eb906ed850827bc72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533278"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>將 Azure 資料存儲從第 1 代遷移到第 2 代

您可以將資料、工作負載和應用程式從資料存儲存儲第 1 代遷移到資料湖存儲 Gen2。

Azure 資料存儲第 2 代基於[Azure Blob 存儲](storage-blobs-introduction.md)構建，並提供一組專用於大資料分析的功能。 [資料存儲庫 Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/)結合了[Azure 資料存儲庫 Gen1](https://docs.microsoft.com/azure/data-lake-store/index)的功能，如檔案系統語義、目錄和檔級安全性，以及[Azure Blob 存儲](storage-blobs-introduction.md)的低成本、分層存儲、高可用性/災害復原功能。

> [!NOTE]
> 為了便於閱讀，本文使用術語*Gen1*引用 Azure 資料存儲第 1 代，術語*Gen2*引用 Azure 資料存儲庫第 2 代。

## <a name="recommended-approach"></a>推薦方法

要遷移到 Gen2，我們建議採用以下方法。

：heavy_check_mark：步驟1：評估準備情況

：heavy_check_mark：步驟 2：準備遷移

：heavy_check_mark：步驟 3：遷移資料和應用程式工作負載

：heavy_check_mark：第4步：從第1代到第2代的分後

> [!NOTE]
> Gen1 和 Gen2 是不同的服務，沒有就地升級體驗，需要有意遷移。 

### <a name="step-1-assess-readiness"></a>第 1 步：評估就緒情況

1. 瞭解[資料存儲湖存儲第 2 代產品](https://azure.microsoft.com/services/storage/data-lake-storage/)/它的好處，成本和一般架構。 

2. 將第 1 代的功能與第 2 代[的功能進行比較](#gen1-gen2-feature-comparison)。 

3. 查看[已知問題](data-lake-storage-known-issues.md)清單，以評估功能上的任何差距。

4. Gen2 支援 Blob 存儲功能，如[診斷日誌記錄](../common/storage-analytics-logging.md)、[訪問層](storage-blob-storage-tiers.md)和[Blob 存儲生命週期管理原則](storage-lifecycle-management-concepts.md)。 如果您有興趣使用這些功能中的任何一個，請查看[當前的支援級別](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features)。

5. 查看[Azure 生態系統支援的](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access)目前狀態，以確保 Gen2 支援解決方案所依賴的任何服務。

### <a name="step-2-prepare-to-migrate"></a>第 2 步：準備遷移

1. 標識要遷移的資料集。

   借此機會清理不再使用的資料集。 除非您計畫一次遷移所有資料，否則請借此機會確定可以分階段遷移的邏輯資料組。
   
2. 確定遷移對您的業務的影響。

   例如，考慮在遷移進行期間，您是否承受任何停機時間。 這些注意事項可以説明您確定合適的移轉模式，並選擇最合適的工具。

3. 創建遷移計畫。 

   我們建議這些[移轉模式](#migration-patterns)。 您可以選擇這些模式之一，將它們組合在一起，或設計您自己的自訂模式。

### <a name="step-3-migrate-data-workloads-and-applications"></a>第 3 步：遷移資料、工作負載和應用程式

使用您喜歡的模式遷移資料、工作負載和應用程式。 我們建議您以增量方式驗證方案。

1. [創建存儲帳戶](data-lake-storage-quickstart-create-account.md)並啟用階層命名空間功能。 

2. 遷移資料。 

3. 在[工作負荷中佈建服務](data-lake-storage-integrate-with-azure-services.md)以指向 Gen2 終結點。 
   
4. 更新應用程式以使用 Gen2 API。 請參閱[.NET](data-lake-storage-directory-file-acl-dotnet.md)、JAVA、Python、JavaScript 和[REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)的指南。 [Java](data-lake-storage-directory-file-acl-java.md) [Python](data-lake-storage-directory-file-acl-python.md) [JavaScript](data-lake-storage-directory-file-acl-javascript.md) 
   
5. 更新腳本以使用資料存儲湖存儲 Gen2 [PowerShell Cmdlet](data-lake-storage-directory-file-acl-powershell.md)和[Azure CLI 命令](data-lake-storage-directory-file-acl-cli.md)。
   
6. 搜索包含代碼檔中字串`adl://`的 URI 引用，或在 Databricks 筆記本、Apache Hive HQL 檔或用作工作負載一部分的任何其他檔中。 將這些引用替換為新存儲帳戶的[Gen2 格式化 URI。](data-lake-storage-introduction-abfs-uri.md) 例如：第 1 代`adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile`URI：`abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`可能變為 。 

7. 將帳戶上的安全性配置為包括[基於角色的存取控制 （RBAC） 角色](../common/storage-auth-aad-rbac-portal.md)、[檔和資料夾級安全性](data-lake-storage-access-control.md)以及 Azure[存儲防火牆和虛擬網路](../common/storage-network-security.md)。

### <a name="step-4-cutover-from-gen1-to-gen2"></a>第 4 步：從第 1 代到第 2 代的分距

在確信應用程式和工作負載在 Gen2 上保持穩定後，您可以開始使用 Gen2 來滿足您的業務方案。 關閉在 Gen1 上運行的任何剩餘管道，並停用您的第一代帳戶。 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>第 1 代 vs 第 2 代功能

下表將第一代的功能與第 2 代的功能進行比較。

|區域 |Gen1   |Gen2 |
|---|---|---|
|資料組織|[階層式命名空間](data-lake-storage-namespace.md)<br>檔和資料夾支援|[階層式命名空間](data-lake-storage-namespace.md)<br>容器、檔和資料夾支援 |
|異地備援| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage)， [ZRS](../common/storage-redundancy.md#zone-redundant-storage)， [GRS](../common/storage-redundancy.md#geo-redundant-storage)， [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|驗證|[AAD 託管標識](../../active-directory/managed-identities-azure-resources/overview.md)<br>[服務主體](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD 託管標識](../../active-directory/managed-identities-azure-resources/overview.md)<br>[服務主體](../../active-directory/develop/app-objects-and-service-principals.md)<br>[共用訪問金鑰](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|授權|管理 - [RBAC](../../role-based-access-control/overview.md)<br>資料 + [ACL](data-lake-storage-access-control.md)|管理與[RBAC](../../role-based-access-control/overview.md)<br>資料 - [ACL](data-lake-storage-access-control.md)， [RBAC](../../role-based-access-control/overview.md) |
|加密 = 靜態資料|伺服器端 — 使用[Microsoft 管理](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)或[客戶管理的](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)金鑰|伺服器端 — 使用[Microsoft 管理](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)或[客戶管理的](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)金鑰|
|VNET 支援|[VNET 集成](../../data-lake-store/data-lake-store-network-security.md)|[服務終結點](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)，[專用終結點（公共預覽）](../common/storage-private-endpoints.md)|
|開發人員體驗|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md)， [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md)， [JAVA](../../data-lake-store/data-lake-store-get-started-java-sdk.md)， [Python](../../data-lake-store/data-lake-store-data-operations-python.md)，[電源外殼](../../data-lake-store/data-lake-store-get-started-powershell.md)， [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|[REST](/rest/api/storageservices/data-lake-storage-gen2)， [.NET](data-lake-storage-directory-file-acl-dotnet.md)， [JAVA](data-lake-storage-directory-file-acl-java.md)， [Python](data-lake-storage-directory-file-acl-python.md)， [JavaScript](data-lake-storage-directory-file-acl-javascript.md)， [PowerShell](data-lake-storage-directory-file-acl-powershell.md)， [Azure CLI](data-lake-storage-directory-file-acl-cli.md) （在公共預覽版中）|
|診斷記錄|經典日誌<br>[集成 Azure 監視器](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[經典日誌](../common/storage-analytics-logging.md)（在公共預覽版中）<br>Azure 監視器集成 + 時間表 TBD|
|生態系統|[HDInsight （3.6）](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)， [Azure 資料磚 （3.1 及以上）](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html)， [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)， [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight （3.6， 4.0）](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)， [Azure 資料磚 （5.1 及以上）](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2)， [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)， [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>第 1 代到第 2 代模式

選擇移轉模式，然後根據需要修改該模式。

|||
|---|---|
|**提升和換檔**|最簡單的模式。 如果資料管道能夠承受停機時間，則是理想的選擇。|
|**增量複製**|類似于*提升和換檔*，但停機時間較少。 非常適合複製時間更長的大量資料。|
|**雙管道**|非常適合承受任何停機時間的管道。|
|**雙向同步**|類似于*雙管道*，但採用更分階段的方法，適用于更複雜的管道。|

讓我們仔細看看每種模式。
 
### <a name="lift-and-shift-pattern"></a>提升和換檔模式

這是最簡單的模式。

1. 停止對 Gen1 的所有寫入。

2. 將資料從第 1 代移動到第 2 代。 我們建議[Azure 資料工廠](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 ACL 隨資料一起複製。

3. 將引入操作和工作負載指向第 2 代。

4. 停用第 1 代。

> [!div class="mx-imgBorder"]
> ![提升和換檔模式](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>使用提升和換檔模式的注意事項

：heavy_check_mark：同時將所有工作負載從第 1 代劃合到第 2 代。

：heavy_check_mark：在遷移和縮短期間預計停機。

：heavy_check_mark：非常適合提供停機時間且所有應用可以同時升級的管道。

### <a name="incremental-copy-pattern"></a>增量複製模式

1. 開始將資料從第 1 代移動到第 2 代。 我們建議[Azure 資料工廠](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 ACL 隨資料一起複製。

2. 增量複製來自 Gen1 的新資料。

3. 複製所有資料後，停止對 Gen1 的所有寫入，並將工作負載指向 Gen2。

4. 停用第 1 代。

> [!div class="mx-imgBorder"]
> ![增量複製模式](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>使用增量複製模式的注意事項：

：heavy_check_mark：同時將所有工作負載從第 1 代劃合到第 2 代。

：heavy_check_mark：僅在縮短期間預計停機。

：heavy_check_mark：非常適合所有應用同時升級的管道，但資料副本需要更多時間。

### <a name="dual-pipeline-pattern"></a>雙管線模式

1. 將資料從第 1 代移動到第 2 代。 我們建議[Azure 資料工廠](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 ACL 隨資料一起複製。

2. 將新資料引入第 1 代和第 2 代。

3. 將工作負載指向第 2 代。

4. 停止對第 1 代的所有寫入，然後停用第 1 代。

> [!div class="mx-imgBorder"]
> ![雙管線模式](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>使用雙管線模式的注意事項：

：heavy_check_mark：第 1 代和第二代管道並排運行。

：heavy_check_mark：支援零停機時間。

：heavy_check_mark：在工作負載和應用程式無法承受任何停機時間，並且您可以引入兩個存儲帳戶的情況下，是理想的選擇。

### <a name="bi-directional-sync-pattern"></a>雙向同步模式

1. 設置第 1 代和 Gen2 之間的雙向複製。 我們建議[萬迪斯可](https://docs.wandisco.com/bigdata/wdfusion/adls/)。 它為現有資料提供了修復功能。

3. 完成所有移動後，停止對 Gen1 的所有寫入並關閉雙向複製。

4. 停用第 1 代。

> [!div class="mx-imgBorder"]
> ![雙向圖案](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>使用雙向同步模式的注意事項：

：heavy_check_mark：非常適合涉及大量管道和依賴關係的複雜方案，其中分階段方法可能更有意義。  

：heavy_check_mark：遷移工作量很高，但它為第一代和第二代提供了並行支援。

## <a name="next-steps"></a>後續步驟

- 瞭解為存儲帳戶設置安全性的各個部分。 請參閱[Azure 存儲安全指南](../common/storage-security-guide.md)。
- 優化資料湖存儲的性能。 有關[性能，請參閱優化 Azure 資料存儲第 2 代](data-lake-storage-performance-tuning-guidance.md)
- 查看管理資料湖存儲的最佳做法。 請參閱[使用 Azure 資料存儲第 2 代的最佳做法](data-lake-storage-best-practices.md)

