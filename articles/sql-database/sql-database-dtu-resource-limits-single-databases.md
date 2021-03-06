---
title: DTU 資源限制單個資料庫
description: 本頁介紹 Azure SQL 資料庫中單個資料庫的一些常見 DTU 資源限制。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: a4c435b4874301fe6fb804085c5b265954cd4f5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256311"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>使用 DTU 購買模型的單一資料庫的資源限制

本文使用 DTU 採購模型提供 Azure SQL 資料庫單個資料庫的詳細資源限制。

有關彈性池的 DTU 採購模型資源限制，請參閱[DTU 資源限制 - 彈性池](sql-database-dtu-resource-limits-elastic-pools.md)。 有關 vCore 資源限制，請參閱[vCore 資源限制 - 單個資料庫](sql-database-vcore-resource-limits-single-databases.md)和[vCore 資源限制 - 彈性池](sql-database-vcore-resource-limits-elastic-pools.md)。 如需不同購買模型的詳細資訊，請參閱[購買模型和服務層](sql-database-purchase-models.md)。

## <a name="single-database-storage-sizes-and-compute-sizes"></a>單一資料庫：儲存體大小與計算大小

下表顯示單一資料庫在每個服務層級和計算大小的可用資源。 您可以使用 [Azure 入口網站](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)、[Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases)、[PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)、[Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) 或 [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases) 來為單一資料庫設定服務層、計算大小與儲存體數量。

> [!IMPORTANT]
> 有關縮放指南和注意事項，請參閱[縮放單個資料庫](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>基本服務層級

| **計算大小** | **基本** |
| :--- | --: |
| 最大 DTU | 5 |
| 內含儲存體 (GB) | 2 |
| 最大儲存體選擇 (GB) | 2 |
| OLTP 記憶體內部儲存體上限 (GB) |N/A |
| 並行背景工作 (要求) 數上限 | 30 |
| 並行工作階段數上限 | 300 |
|||

> [!IMPORTANT]
> 基本服務層提供少於一個 vCore （CPU）。  對於 CPU 密集型工作負載，建議使用 S3 或更高級別的服務層。 
>
>關於資料存儲，基本服務層放置在標準頁面 Blob 上。 標準頁面 Blobs 使用基於硬碟 （HDD） 的存儲介質，最適合開發、測試和其他對性能可變性不太敏感的不常訪問的工作負載。
>

### <a name="standard-service-tier"></a>標準服務層級

| **計算大小** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| 最大 DTU | 10 | 20 | 50 | 100 |
| 內含儲存體 (GB) | 250 | 250 | 250 | 250 |
| 最大儲存體選擇 (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| OLTP 記憶體內部儲存體上限 (GB) | N/A | N/A | N/A | N/A |
| 並行背景工作 (要求) 數上限| 60 | 90 | 120 | 200 |
| 並行工作階段數上限 |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> 標準 S0、S1 和 S2 層提供的 vCore （CPU） 不到一個 vCore。  對於 CPU 密集型工作負載，建議使用 S3 或更高級別的服務層。 
>
>關於資料存儲，標準 S0 和 S1 服務層放置在標準頁面 Blob 上。 標準頁面 Blobs 使用基於硬碟 （HDD） 的存儲介質，最適合開發、測試和其他對性能可變性不太敏感的不常訪問的工作負載。
>

### <a name="standard-service-tier-continued"></a>標準服務層級 (續)

| **計算大小** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| 最大 DTU | 200 | 400 | 800 | 1600 | 3000 |
| 內含儲存體 (GB) | 250 | 250 | 250 | 250 | 250 |
| 最大儲存體選擇 (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| OLTP 記憶體內部儲存體上限 (GB) | N/A | N/A | N/A | N/A |N/A |
| 並行背景工作 (要求) 數上限| 400 | 800 | 1600 | 3200 |6000 |
| 並行工作階段數上限 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>高階服務層級

| **計算大小** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| 最大 DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 內含儲存體 (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| 最大儲存體選擇 (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| OLTP 記憶體內部儲存體上限 (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 並行背景工作 (要求) 數上限| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| 並行工作階段數上限 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\*從 1024 GB 到 4096 GB，增量為 256 GB

> [!IMPORTANT]
> 除中國東部、華北、德國中部、德國東北部、美國中西部、美國 DoD 地區和美國政府中心外，高級級別中目前所有區域都提供超過 1 TB 的存儲。 在這些區域中，進階層中的儲存空間上限為 1 TB。  如需詳細資訊，請參閱 [P11-P15 目前的限制](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)。  
> [!NOTE]
> 有關`tempdb`限制，請參閱[tempdb 限制](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)。

## <a name="next-steps"></a>後續步驟

- 有關單個資料庫的 vCore 資源限制，請參閱[使用 vCore 購買模型的單個資料庫的資源限制](sql-database-vcore-resource-limits-single-databases.md)
- 有關彈性池的 vCore 資源限制，[請參閱使用 vCore 購買模型的彈性池的資源限制](sql-database-vcore-resource-limits-elastic-pools.md)
- 有關彈性池的 DTU 資源限制，[請參閱使用 DTU 採購模型的彈性池的資源限制](sql-database-dtu-resource-limits-elastic-pools.md)
- 如需受控執行個體的資源限制，請參閱[受控執行個體資源限制](sql-database-managed-instance-resource-limits.md)。
- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 如需資料庫伺服器資源限制的相關資訊，請參閱 [SQL Database 伺服器上的資源限制概觀](sql-database-resource-limits-database-server.md)，以了解伺服器和訂用帳戶層級的限制。
