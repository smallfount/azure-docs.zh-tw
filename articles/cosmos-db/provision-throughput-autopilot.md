---
title: 在自動駕駛模式下創建 Azure Cosmos 容器和資料庫。
description: 瞭解優勢、用例以及如何在自動駕駛模式下預配 Azure Cosmos 資料庫和容器。
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246652"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>在 Autopilot 模式中建立 Azure Cosmos 容器和資料庫 (預覽)

Azure Cosmos DB 可讓您以手動或 Autopilot 模式在容器上佈建輸送量。 本文說明 Autopilot 模式的優點和使用案例。

> [!NOTE]
> 自動駕駛模式當前在公共預覽版中可用。 只能[為新資料庫和容器啟用自動駕駛儀](#create-a-database-or-a-container-with-autopilot-mode)。 它不適用於現有的容器和資料庫。

除了手動預配輸送量外，現在還可以以自動駕駛模式配置 Azure Cosmos 容器。 在自動駕駛模式下配置的容器和資料庫將根據**應用程式需求自動立即擴展預配輸送量，而不會影響工作負載的可用性、延遲、輸送量或性能。**

在自動駕駛模式下配置容器和資料庫時，需要指定不超出的最大輸送量`Tmax`。 然後，容器可以擴展其輸送量，`0.1*Tmax < T < Tmax`以便 。 換句話說，容器和資料庫會根據工作負載需求進行即時擴展，從您配置的最大輸送量值的 10% 到配置的最大輸送量值。 您可以隨時更改自動駕駛儀資料庫或容器`Tmax`上的最大輸送量 （ ） 設置。 使用自動駕駛儀選項，每個容器或資料庫的 400 RU/s 最小輸送量不再適用。

在自動駕駛儀預覽期間，對於容器或資料庫上指定的最大輸送量，系統允許在計算的存儲限制內運行。 如果超過存儲限制，則最大輸送量將自動調整到較高的值。 將資料庫級輸送量與自動駕駛模式一起使用時，資料庫中允許的容器數計算為： `0.001*TMax`。 例如，如果預配 20，000 個自動駕駛儀 RU/s，則資料庫可以有 20 個容器。

## <a name="benefits-of-autopilot-mode"></a>自動駕駛模式的優點

在自動駕駛模式下配置的 Azure Cosmos 容器具有以下優點：

* **簡單：** 自動引導模式下的容器消除了手動管理各種容器的預配輸送量 （R） 和容量的複雜性。

* **可擴展：** 處於自動駕駛模式下的容器根據需要無縫擴展預配的吞吐能力。 用戶端連接、應用程式不會中斷，也不會影響任何現有的 SL。

* **經濟高效：** 使用在自動駕駛模式下配置的容器時，只需按小時支付工作負載所需的資源。

* **高度可用：** 自動駕駛模式下的容器使用相同的全域分佈、容錯、高度可用的後端，以確保資料的持久性和高可用性。

## <a name="use-cases-of-autopilot-mode"></a>自動駕駛模式的用例

在自動駕駛模式下配置的 Azure Cosmos 容器的用例包括：

* **可變工作負載：** 當您運行一個使用時間輕巧的應用程式時，每天使用時間為 1 小時至幾個小時，每天幾次或每年多次。 示例包括人力資源申請、預算編制和運營報告。 對於此類方案，可以使用在自動駕駛模式下配置的容器，並且不再需要手動預配峰值或平均容量。

* **不可預測的工作負載：** 當您運行的工作負載時，其中全天都有資料庫使用方式，但也運行難以預測的活動高峰。 例如，當天氣預報發生變化時，流量網站將看到活動激增。 在自動駕駛模式下配置的容器可調整容量以滿足應用程式峰值負載的需求，並在活動激增結束後縮減。

* **新應用程式：** 如果要部署新應用程式，並且不確定需要多少預配輸送量（即有多少個 R。 在自動引導模式下配置容器時，您可以自動縮放以滿足應用程式的容量需求和要求。

* **不常使用的應用程式：** 如果您的應用程式每天、每週或每月只使用幾個小時，例如低容量應用程式/Web/博客網站。

* **開發和測試資料庫：** 如果您讓開發人員在工作時間使用容器，但在夜間或週末不需要容器。 在自動引導模式下配置容器時，在不使用時，容器會縮減到最小。

* **計畫生產工作負載/查詢：** 當您在單個容器上有一系列計畫的請求/操作/查詢，並且如果存在要以絕對低輸送量運行的空閒期間，現在可以輕鬆執行此操作。 當計畫查詢/請求提交到在自動駕駛模式下配置的容器時，它將自動根據需要向上擴展並運行該操作。

解決以前問題的解決方案不僅需要大量實施時間，而且還會引入配置或代碼的複雜性，並且經常需要手動干預來解決它們。 自動駕駛模式可開箱即用上述方案，因此您無需再擔心這些問題。

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>比較 = 在手動模式下配置的容器與自動駕駛模式的容器

|  | 在手動模式下配置的容器  | 在自動駕駛模式下配置的容器 |
|---------|---------|---------|
| **預配輸送量** | 手動預配。 | 根據工作負載使用模式自動和即時縮放。 |
| **限制請求/操作的速率 （429）**  | 如果消耗量超過預配容量，則可能發生。 | 如果消耗的輸送量在自動引導模式下選擇的最大輸送量內，則不會發生。   |
| **容量規劃** |  您必須執行初始容量規劃和提供所需的輸送量。 |    您不必擔心容量規劃。 系統自動負責容量規劃和容量管理。 |
| **定價** | 每小時手動預配 RU/s。 | 對於單個寫入區域帳戶，您可以使用自動駕駛儀 RU/s/s/小時費率支付每小時使用的輸送量。 <br/><br/>對於具有多個寫入區域的帳戶，自動駕駛儀不收取額外費用。 使用相同的多主機 RU/s/小時費率，為每小時使用的輸送量付費。 |
| **最適合工作負載類型** |  可預測且穩定的工作負載|   不可預測和可變工作負載  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>創建具有自動駕駛模式的資料庫或容器

通過 Azure 門戶創建新資料庫或容器時，可以配置新資料庫或容器的自動駕駛儀。 使用以下步驟創建新資料庫或容器、啟用自動駕駛儀並指定最大輸送量 （RU/s）。

1. 登錄到 Azure[門戶](https://portal.azure.com)或[Azure 宇宙資料庫資源管理器。](https://cosmos.azure.com/)

1. 導航到 Azure Cosmos 資料庫帳戶並打開**資料資源管理器**選項卡。

1. 選擇 **"新容器"。** 輸入資料庫、容器和分區鍵的名稱。 在 **"輸送量**"下，選擇 **"自動駕駛儀"** 選項，然後選擇資料庫或容器在使用自動駕駛儀選項時不能超過的最大輸送量 （RU/s）。

   ![創建容器並配置自動駕駛儀輸送量](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. 選取 [確定]****。

您可以通過選擇 **"預配資料庫輸送量**"選項，使用自動駕駛模式創建共用輸送量資料庫。

## <a name="throughput-and-storage-limits-for-autopilot"></a><a id="autopilot-limits"></a>自動駕駛儀的輸送量和存儲限制

下表顯示了自動駕駛模式下不同選項的最大範圍和存儲限制：

|最大輸送量限制  |最大存儲限制  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20，000 RU/s  |  200 GB  |
|100，000 RU/s    |  1 TB   |
|500，000 RU/s    |  5 TB  |

## <a name="next-steps"></a>後續步驟

* 查看[自動駕駛儀常見問題解答](autopilot-faq.md)。
* 瞭解有關[邏輯分區](partition-data.md)的更多詳細資訊。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
