---
title: 使用 Azure HPC 緩存進列區域冗余和容錯移轉恢復
description: 使用 Azure HPC 緩存為災害復原提供容錯移轉功能的技術
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4eb203915c8fedbef6af0e5a3bc14eff1835a92b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982167"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>使用多個緩存進列區域容錯移轉恢復

每個 Azure HPC 緩存實例都在特定訂閱中運行，並在一個區域中運行。 這意味著，如果區域完全中斷，您的緩存工作流可能會中斷。

本文介紹了一種通過使用第二個區域進行緩存容錯移轉來降低工作中斷風險的策略。

關鍵是使用可從多個區域訪問的後端存儲。 此存儲可以是具有適當 DNS 支援的本地 NAS 系統，也可以是駐留在與緩存不同的區域中的 Azure Blob 存儲。

當工作流在主區域中進行時，資料將保存在區域外部的長期存儲中。 如果快取區域不可用，則可以在次要區域中創建重複的 Azure HPC 緩存實例，連接到同一存儲，並從新緩存中恢復工作。

## <a name="planning-for-regional-failover"></a>區域容錯移轉的規劃

要設置為可能的容錯移轉準備的緩存，請按照以下步驟操作：

1. 確保後端存儲在第二個區域中可訪問。
1. 在計畫創建主緩存實例時，還應準備在第二個區域中複製此設置過程。 包括以下專案：

   1. 虛擬網路和子網結構
   1. 緩存容量
   1. 存儲目標詳細資訊、名稱和命名空間路徑
   1. 有關用戶端電腦的詳細資訊（如果它們與緩存位於同一區域）
   1. 用於快取用戶端的裝載命令

   > [!NOTE]
   > Azure HPC 緩存可以通過[Azure 資源管理器範本](../azure-resource-manager/templates/overview.md)或直接存取其 API 以程式設計方式創建。 有關詳細資訊，請與 Azure HPC 緩存團隊聯繫。

## <a name="failover-example"></a>容錯移轉範例

例如，假設您要在 Azure 的東美國區域中定位 Azure HPC 緩存。 它將訪問存儲在本地資料中心的資料。

您可以將美國西部 2 區域中的緩存用作容錯移轉備份。

在美國東部創建緩存時，準備第二個緩存以在西部美國 2 中部署。 您可以使用腳本或範本來自動執行此準備工作。

如果美國東部發生區域範圍故障，請創建您在美國西部 2 區域準備的緩存。

創建緩存後，添加指向同一本地資料存儲的存儲目標，並使用與舊緩存的存儲目標相同的聚合命名空間路徑。

如果原始用戶端受到影響，請在美國西部 2 區域創建新用戶端以與新緩存一起使用。

所有用戶端都需要裝載新緩存，即使用戶端不受區域中斷的影響。 新緩存具有與舊緩存不同的裝載位址。

## <a name="learn-more"></a>深入了解

Azure 應用程式體系結構指南包含有關如何[從區域範圍的服務中斷中恢復](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)的詳細資訊。
<!-- this should be an internal link instead of a URL but I can't find the tree  -->
