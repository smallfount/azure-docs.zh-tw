---
title: 自動調整規模和區域備援應用程式閘道 v2
description: 本文介紹了 Azure 應用程式Standard_v2和 WAF_v2 SKU，其中包括自動縮放和區域冗余功能。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 4cd2969f9a56c96af2b2c6db216f6829a080260c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371270"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>自動調整規模和區域備援應用程式閘道 v2 

應用程式閘道和 Web 應用程式防火牆 （WAF） 也可在 Standard_v2和 sKU WAF_v2下使用。 v2 SKU 提供性能增強功能，並增加了對關鍵新功能（如自動縮放、區域冗余和對靜態 VIP 的支援）的支援。 在新的 v2 SKU 中，標準和 WAF SKU 下的現有功能繼續得到支援，[比較部分列出了](#differences-with-v1-sku)一些例外情況。

新的 v2 SKU 包括以下增強功能：

- **自動調整規模**：自動調整規模 SKU 下的「應用程式閘道」或 WAF 部署可以根據變動的流量負載模式來調升或調降規模。 自動調整規模也可讓您在佈建時，無須選擇部署大小或執行個體計數。 此 SKU 提供真正的彈性。 在Standard_v2和WAF_v2 SKU 中，應用程式閘道可以在固定容量（禁用自動縮放）和啟用自動縮放模式下運行。 固定容量模式適用於工作負載一致且可預測的案例。 自動縮放模式在看到應用程式流量差異的應用程式中非常有用。
- **區域冗余**：應用程式閘道或 WAF 部署可以跨多個可用區域，無需在每個區域中使用流量管理器預配單獨的應用程式閘道實例。 您可以選擇部署應用程式閘道實例的單個區域或多個區域，這使得它更能抵禦區域故障。 應用程式的後端集區可以均等分散在可用性區域間。

  區域冗余僅在 Azure 區域可用時可用。 在其他地區，支援所有其他功能。 有關詳細資訊，請參閱[Azure 中有哪些可用性區域？](../availability-zones/az-overview.md#services-support-by-region)
- **靜態 VIP**： 應用程式閘道 v2 SKU 專門支援靜態 VIP 類型。 這可確保與應用程式閘道關聯的 VIP 不會在部署的生命週期中更改，即使在重新開機之後也是如此。  v1 中沒有靜態 VIP，因此您必須使用應用程式閘道 URL 而不是 IP 位址，以便通過應用程式閘道路由到應用服務。
- **標頭重寫**：應用程式閘道允許您使用 v2 SKU 添加、刪除或更新 HTTP 要求和回應標頭。 有關詳細資訊，請參閱[使用應用程式閘道重寫 HTTP 標頭](rewrite-http-headers.md)
- **金鑰保存庫集成**：應用程式閘道 v2 支援與連接到啟用 HTTPS 的攔截器的伺服器憑證的金鑰保存庫集成。 有關詳細資訊，請參閱[使用金鑰保存庫證書的 SSL 終止](key-vault-certs.md)。
- **Azure 庫伯內斯服務入口控制器**：應用程式閘道 v2 入口控制器允許將 Azure 應用程式閘道用作稱為 AKS 群集的 Azure 庫伯奈服務 （AKS） 的入口。 有關詳細資訊，請參閱[什麼是應用程式閘道入口控制器？](ingress-controller-overview.md)
- **性能增強**： 與標準/WAF SKU 相比，v2 SKU 可提供多達 5 倍的 SSL 卸載性能。
- **更快的部署和更新時間**與標準/WAF SKU 相比，v2 SKU 提供了更快的部署和更新時間。 這還包括 WAF 配置更改。

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>支援區域

sKU Standard_v2 和 WAF_v2 SKU 在以下區域提供：美國中北部、美國中南部、美國西部、美國西部 2、美國東部、美國東部、美國中部、北歐、西歐、東南亞、法國中部、英國西部、日本東部、日本西部、澳大利亞東部，澳大利亞東南部，巴西南部，加拿大中部，加拿大東部，東亞，韓國中部，韓國南部，英國南部，印度中部，西印度，南印度。

## <a name="pricing"></a>定價

使用 v2 SKU 時，定價模型由消耗驅動，不再附加到實例計數或大小。 v2 SKU 定價有兩個組成部分：

- **固定價格**- 這是每小時（或部分小時）價格，以提供Standard_v2或WAF_v2閘道。 請注意，0 個額外的最低實例仍然確保服務的高可用性，該服務始終包含在固定價格中。
- **容量單價**- 這是除固定成本外收取的基於消耗的成本。 容量單位費用也按小時或部分每小時計算。 容量單元有三個維度 - 計算單元、持久連接和輸送量。 計算單元是處理器容量消耗的度量。 影響計算單位的因素包括 TLS 連接/秒、URL 重寫計算和 WAF 規則處理。 持久連接是在給定計費間隔內與應用程式閘道建立 TCP 連接的度量。 輸送量是系統在給定計費間隔內處理的平均百萬位元/秒。  對於超出預留實例計數的任何內容，計費是在容量單位級別進行的。

每個容量單元最多由：1 個計算單元或 2500 個持久連接或 2.22-Mbps 輸送量組成。

計算單位指南：

- **Standard_v2** - 使用 RSA 2048 位金鑰 TLS 證書，每個計算單元每秒能夠提供大約 50 個連接。
- **WAF_v2** - 對於 70-30% 的流量組合，每個計算單元每秒可支援大約 10 個併發請求，70% 的請求小於 2 KB GET/POST，並且保持較高水準。 WAF 性能當前不受回應大小的影響。

> [!NOTE]
> 每個實例目前可以支援大約 10 個容量單元。
> 計算單元可以處理的請求數取決於各種條件，如 TLS 證書金鑰大小、金鑰交換演算法、標頭重寫以及 WAF 傳入請求大小。 我們建議您執行應用程式測試以確定每個計算單元的請求速率。 在計費開始之前，容量單位和計算單位都將作為指標提供。

下表顯示了示例價格，僅用於說明目的。

**定價在美國東部**：

|              SKU 名稱                             | 固定價格（美元/小時）  | 容量單價（$/CU-hr）   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

有關詳細資訊，請參閱[定價頁](https://azure.microsoft.com/pricing/details/application-gateway/)。 

**示例 1**

應用程式閘道Standard_v2預配，無需在固定容量為五個實例的手動縮放模式下自動縮放。

固定價格 = 744（小時） = $0.20 = $148.8 <br>
容量單位 = 744 （小時） = 每個實例 10 個容量單位 = 5 個實例 = 每個容量單位小時 0.008 美元 = 297.6 美元

總價 = $148.8 = $297.6 = $446.4

**示例 2**

應用程式閘道standard_v2預配一個月，最小實例為零，在此期間，它收到 25 個新的 SSL 連線/秒，平均資料傳輸為 8.88-Mbps。 假設連接是短暫的，您的價格將是：

固定價格 = 744（小時） = $0.20 = $148.8

容量單價 = 744（小時） = 最大值（連接/秒 25/50 計算單位，輸送量容量單位 8.88/2.22） = $0.008 = 744 = 4 = 0.008 = $23.81

總價 = $148.8*23.81 = $172.61

如您所見，您只為四個容量單位收費，而不是為整個實例收費。 

> [!NOTE]
> Max 函數返回一對值中的最大值。


**示例 3**

應用程式閘道standard_v2預配一個月，至少有五個實例。 假設沒有流量和連接是短暫的，您的價格將是：

固定價格 = 744（小時） = $0.20 = $148.8

容量單價 = 744（小時） = 最大值（連接/秒 0/50 計算單位，輸送量 0/2.22 容量單位） = $0.008 = 744 = 50 = 0.008 = $297.60

總價 = $148.80+297.60 = $446.4

在這種情況下，即使沒有流量，也會為五個實例的全部費用收費。

**示例 4**

應用程式閘道standard_v2預配一個月，至少有五個實例，但這次平均有 125 mbps 資料傳輸，每秒 25 個 SSL 連線。 假設沒有流量和連接是短暫的，您的價格將是：

固定價格 = 744（小時） = $0.20 = $148.8

容量單價 = 744（小時） = 最大值（連接/秒 25/50 計算單位，輸送量 125/2.22 容量單位） = 0.008 美元 = 744 * 57 = 0.008 = $339.26

總價 = $148.80*339.26 = $488.06

在這種情況下，您需要為全部五個實例以及 7 個容量單位（即實例的 7/10）收費。  

**示例 5**

應用程式閘道WAF_v2預配一個月。 在此期間，它接收 25 個新的 SSL 連線/秒，平均 8.88 Mbps 資料傳輸，每秒執行 80 個請求。 假設連接是短期的，並且應用程式的計算單位計算支援每個計算單位 10 RPS，則您的價格將是：

固定價格 = 744（小時） = $0.36 = $267.84

容量單價 = 744（小時） = 最大值（計算單位最大值（連接/秒計算單位 25/50，WAF RPS 80/10），輸送量容量單位 8.88/2.22） = 0.0144 * 744 * 8 = 0.0144 = $85.71

總價 = $267.84 = $85.71 = $353.55

> [!NOTE]
> Max 函數返回一對值中的最大值。

## <a name="scaling-application-gateway-and-waf-v2"></a>擴展應用程式閘道和 WAF v2

應用程式閘道和 WAF 可配置為以兩種模式進行擴展：

- **自動縮放**- 啟用自動縮放後，應用程式閘道和 WAF v2 VKU 會根據應用程式流量要求進行向上或向下擴展。 此模式為您的應用程式提供更好的彈性，無需猜測應用程式閘道大小或實例計數。 此模式還允許您通過不要求閘道在峰值預配容量下運行以預期的最大流量負載來節省成本。 您必須指定最小和可選的最大實例計數。 最小容量可確保應用程式閘道和 WAF v2 不會低於指定的最小實例計數，即使在沒有流量的情況下也是如此。 每個實例計為 10 個額外的預留容量單位。 零表示沒有保留容量，純自動縮放在性質上。 請注意，零額外最低實例仍可確保始終隨固定價格附帶的服務的高可用性。 您還可以選擇指定最大實例計數，這可確保應用程式閘道不會超出指定數量的實例。 您將繼續按閘道提供的流量計費。 實例計數的範圍可以從 0 到 125。 如果未指定，最大實例計數的預設值為 20。
- **手動**- 您也可以選擇閘道不會自動縮放的手動模式。 在此模式下，如果流量多於應用程式閘道或 WAF 可以處理的流量，則可能導致流量損失。 使用手動模式，必須指定實例計數。 實例計數可能從 1 到 125 個實例不等。

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>v1 SKU 和 v2 SKU 之間的功能比較

下表比較了每個 SKU 可用的功能。

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| 自動調整                                       |          | &#x2713; |
| 區域備援                                   |          | &#x2713; |
| 靜態 VIP                                        |          | &#x2713; |
| Azure 庫伯奈斯服務 （AKS） 入口控制器 |          | &#x2713; |
| Azure 金鑰保存庫整合                       |          | &#x2713; |
| 重寫 HTTP（S） 標頭                           |          | &#x2713; |
| URL 型路由                                 | &#x2713; | &#x2713; |
| 多網站裝載                             | &#x2713; | &#x2713; |
| 流量重定向                               | &#x2713; | &#x2713; |
| Web 應用程式防火牆 (WAF)                    | &#x2713; | &#x2713; |
| WAF 自訂規則                                  |          | &#x2713; |
| 安全通訊端層 (SSL) 終止            | &#x2713; | &#x2713; |
| 端到端 SSL 加密                         | &#x2713; | &#x2713; |
| 工作階段親和性                                  | &#x2713; | &#x2713; |
| 自訂錯誤頁面                                | &#x2713; | &#x2713; |
| WebSocket 支援                                 | &#x2713; | &#x2713; |
| HTTP/2 支援                                    | &#x2713; | &#x2713; |
| 清空連線                               | &#x2713; | &#x2713; |

> [!NOTE]
> 自動縮放 v2 SKU 現在支援[預設運行狀況探測器](application-gateway-probe-overview.md#default-health-probe)，以自動監視其後端池中所有資源的運行狀況，並突出顯示那些被視為不正常的後端成員。 預設運行狀況探測將自動設定為沒有任何自訂探測配置的後端。 要瞭解更多資訊，請參閱[應用程式閘道中的運行狀況探測器](application-gateway-probe-overview.md)。

## <a name="differences-with-v1-sku"></a>與 v1 SKU 的差異

|差異|詳細資料|
|--|--|
|驗證憑證|不支援。<br>有關詳細資訊，請參閱[使用應用程式閘道的端到端 SSL 概述](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)。|
|在相同子網路上混合使用 Standard_v2 和標準應用程式閘道|不支援|
|應用程式閘道子網上的使用者定義路由 （UDR）|支援（特定方案）。 在預覽中。<br> 有關受支援方案的詳細資訊，請參閱[應用程式閘道配置概述](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)。|
|輸入連接埠範圍的 NSG| - Standard_v2 SKU 適用 65200 至 65535<br>- 標準 SKU 適用 65503 至 65534<br>如需詳細資訊，請參閱[常見問題集](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)。|
|Azure 診斷中的效能記錄|不支援。<br>應該使用 Azure 計量。|
|計費|計費定于 2019 年 7 月 1 日開始。|
|FIPS 模式|目前不支援。|
|純 ILB 模式|目前不支援。 支援將公用與 ILB 模式搭配使用。|
|Netwatcher 整合|不支援。|
|Azure 資訊安全中心整合|尚未提供。

## <a name="migrate-from-v1-to-v2"></a>從 v1 遷移至 v2

PowerShell 庫中提供了 Azure PowerShell 腳本，可説明您從 v1 應用程式閘道/WAF 遷移到 v2 自動縮放 SKU。 此腳本可説明您從 v1 閘道複製配置。 流量遷移仍然是您的責任。 有關詳細資訊，請參閱將[Azure 應用程式閘道從 v1 遷移到 v2](migrate-v1-v2.md)。

## <a name="next-steps"></a>後續步驟

- [快速入門：使用 Azure 應用程式閘道引導網路流量 - Azure 入口網站](quick-create-portal.md)
- [使用 Azure PowerShell 建立具有保留虛擬 IP 位址的自動調整規模、區域備援應用程式閘道](tutorial-autoscale-ps.md)
- 深入了解[應用程式閘道](overview.md)。
- 深入了解 [Azure 防火牆](../firewall/overview.md)。
