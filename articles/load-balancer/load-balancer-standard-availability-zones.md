---
title: Azure Standard Load Balancer 和可用性區域
titleSuffix: Azure Load Balancer
description: 使用此學習路徑，開始使用 Azure 標準負載等化器和可用性區域。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5a65982c5c13eb4e4273efcfd8d14910b0f35572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197142"
---
# <a name="standard-load-balancer-and-availability-zones"></a>標準 Load Balancer 和可用性區域

Azure 標準負載等化器支援[可用性區域](../availability-zones/az-overview.md)方案。 您可以使用標準負載等化器通過將資源與區域對齊並跨區域分發來優化端到端方案中的可用性。  查看[可用性區域](../availability-zones/az-overview.md)，瞭解什麼是可用性區域、當前支援哪些區域以及其他相關概念和產品。 可用性區域與標準負載等化器相結合，是一個廣泛而靈活的功能集，可以創建許多不同的方案。  請檢閱本文以了解這些[概念](#concepts)，以及基本案例的[設計指引](#design)。

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a>適用於 Load Balancer 的可用性區域概念

Load Balancer 資源本身是區域的，且絕不具區域性。 可以配置的內容的細微性受前端、規則和後端池定義的每個配置的約束。
在可用性區域的上下文中，負載等化器規則的行為和屬性被描述為區域冗余或區域。  區域備援和區域性說明屬性的區域性。  在負載等化器的上下文中，區域冗余始終意味著*多個區域*，區域意味著將服務隔離到*單個區域*。
公用和內部的 Load Balancer 均支援區域備援和區域性案例，且兩者都可視需要在區域之間進行流量導向 (「跨區域負載平衡」**)。 

### <a name="frontend"></a>前端

負載等化器前端是一種前端 IP 配置，用於引用虛擬網路資源子網中的公共 IP 位址資源或私人 IP 位址。  它會構成服務公開所在的負載平衡端點。
負載等化器資源可以同時包含具有區域和區域冗余前端的規則。 當公共 IP 資源或私人 IP 位址已保證到區域時，區域區域（或缺少其）不可改變。  如果要更改或省略公共 IP 或私人 IP 位址前端的區位，則需要在適當的區域中重新創建公共 IP。  可用性區域不會更改多個前端的約束，請查看[負載等化器的多個前端](load-balancer-multivip-overview.md)，瞭解此功能的詳細資訊。

#### <a name="zone-redundant"></a>區域備援 

在具有可用性區域的區域中，標準負載等化器前端可以是區域冗余。  區域冗余意味著所有入站或出站流都由區域內的多個可用性區域同時使用單個 IP 位址提供服務。 您不需要進行 DNS 備援配置。 單個前端 IP 位址可以經受住區域故障，並可用於覆蓋所有（未受影響的）後端池成員，而不考慮區域。 一個或多個可用性區域可能會失敗，只要區域中的一個區域保持正常，資料路徑即可生存。 前端的單 IP 位址由多個可用性區域中的多個獨立基礎結構部署同時提供。  這並不意味著無命中資料路徑，但任何重試或重建都將在不受區域故障影響的其他區域中成功。   

#### <a name="optional-zone-isolation"></a>可選區域隔離

您可以選擇讓某個前端保證屬於單一區域，我們稱之為*區域性前端*。  這表示任何輸入或輸出流量都會由區域中的單一區域提供服務。  您的前端會具有與區域相同的健康情況。  非保證區域中的失敗皆不會對資料路徑造成影響。 您可以使用區域性前端來公開每個可用性區域的 IP 位址。  

此外，您可以直接為每個區域內的負載平衡端點使用區域前端。 您也可以使用此前端公開每個區域的負載平衡端點，以個別監視每個區域。  或者對於公共終結點，您可以將它們與 DNS 負載平衡產品（如[流量管理器](../traffic-manager/traffic-manager-overview.md)）集成，並使用單個 DNS 名稱。 然後，用戶端將解析為此 DNS 名稱到多個區域 IP 位址。  

如果您想要綜合這些概念 (相同後端的區域備援和區域性)，請檢閱 [Azure Load Balancer 的多個前端](load-balancer-multivip-overview.md)。

對於公共負載等化器前端，向相應規則使用的前端 IP 配置引用的公共 IP 資源添加*區域*參數。

對於內部 Load Balancer 前端，請將*區域*參數新增至內部 Load Balancer 前端 IP 組態。 區域性前端會使 Load Balancer 保證子網路中的某個 IP 位址屬於特定區域。

### <a name="cross-zone-load-balancing"></a>跨區域負載平衡

跨區域負載平衡是 Load Balancer 可觸達任何區域中的後端端點，並與前端及其區域性無關的能力。  任何負載平衡規則都可以在任何可用性區域或區域實例中定位後端實例。

您需要注意以表示可用性區域概念的方式構建方案。 例如，您需要保證在單個區域或多個區域內部署虛擬機器，並將區域前端和區域後端資源對齊到同一區域。  如果僅具有區域資源，則方案將工作，但相對於可用性區域可能沒有明確的故障模式。 

### <a name="backend"></a>後端

負載等化器適用于虛擬機器實例。  這些可以是獨立、可用性集或虛擬機器擴展集。  單個虛擬網路中的任何虛擬機器實例都可以成為後端池的一部分，而不管它是否被保證為區域或哪個區域得到保證。

如果要將前端和後端與單個區域對齊並保證，則僅將虛擬機器放在同一區域中，以放入相應的後端池中。

如果要跨多個區域解決虛擬機器問題，只需將來自多個區域的虛擬機器放入同一後端池即可。  使用虛擬機器擴展集時，您可以將一或多個虛擬機器擴展集放入相同的後端集區中。  而且，每個虛擬機器擴展集可位於單一或多個區域中。

### <a name="outbound-connections"></a>輸出連線

相同的區域冗余和區域屬性適用于[出站連接](load-balancer-outbound-connections.md)。  所有區域都提供用於出站連接的區域冗余公共 IP 位址。 區域公共 IP 位址僅由保證在其中的區域提供服務。  出站連接 SNAT 埠分配在區域故障中倖存，如果不受區域故障影響，您的方案將繼續提供出站 SNAT 連接。  如果受影響的區域提供了流，則可能需要傳輸或為區域冗余方案重新建立連接。  受影響區域以外的區域中的流不受影響。

SNAT 埠預分配演算法在可用區域或可用區域時相同。

### <a name="health-probes"></a>健康狀態探查

現有運行狀況探測定義將保持現有無可用區域狀態。  但是，我們已經在基礎結構級別擴展了運行狀況模型。 

使用區域冗余前端時，Load 平衡器擴展其內部運行狀況模型，以獨立探測虛擬機器從每個可用性區域的可訪問性，並關閉可能在未經客戶干預的情況下出現故障的區域之間的路徑。  如果給定路徑從一個區域的負載等化器基礎結構到另一個區域中的虛擬機器不可用，負載等化器可以檢測並避免此故障。 其他可存取此 VM 的區域將可繼續從其各自的前端為 VM 提供服務。  因此，在故障事件期間，每個區域可能具有略有不同的新流分佈，同時保護端到端服務的整體運行狀況。

## <a name="design-considerations"></a><a name="design"></a>設計注意事項

負載等化器在可用性區網域內容中具有特意的靈活性。 您可以選擇與區域對齊，也可以為每個規則選擇區域冗余。  增加可用性可能會導致複雜性隨之提高，因此您的可用性設計必須顧及最佳效能。  以下我們將討論一些重要的設計考量。

### <a name="automatic-zone-redundancy"></a>自動區域備援

Load Balancer 可簡化以單一 IP 作為區域備援前端的作業。 區域備援 IP 位址可安全地為任何區域中的區域性資源提供服務，且在一或多個區域失敗時仍可存留，只要區域內有一個區域維持良好狀況即可。 相反地，區域性前端則是將服務縮減為單一區域，且個別的區域將會有共同的結果。

區域備援不代表無中斷的資料路徑或控制平面；它實際上是資料平面。 區域備援流量可以使用任何區域，且客戶的流量將會使用區域中所有狀況良好的區域。 如果區域失敗，在該時間點使用良好區域的流量並不會受到影響。  在區域故障時使用區域的流量可能會受到影響，但應用程式可以恢復。 一旦 Azure 在區域故障附近聚集，這些流可以在重新傳輸或重新建立時在區域內的剩餘正常區域中繼續。

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a>跨區域界限

請務必了解，無論何時，使用跨區域的端對端服務都不會產生來自單一區域的結果，而是可能來自多個區域的結果。  因此，您的端對端服務可能無法透過非區域性部署獲得任何可用性。

避免引入意外的跨區域依賴關係，這將在使用可用性區域時取消可用性提升。  如果您的應用程式包含多個元件，而您想要有發生區域失敗後的復原能力，您必須謹慎確保有足夠的重要元件能夠在發生區域失敗時存留。  例如，如果應用程式的單一重要元件僅存在於存留區域以外的一個區域中，就可能對整個應用程式造成影響。  此外，也請考慮區域還原以及應用程式的收斂方式。 您需要瞭解應用程式對部分失敗的原因。 接下來我們將回顧一些要點，藉以激發您在思考特定案例所應提出的問題。

- 如果應用程式有兩個元件，如 IP 位址和具有託管磁片的虛擬機器，並且它們在區域 1 和區域 2 中得到保證，則當區域 1 發生故障時，當區域 1 發生故障時，端到端服務將不能生存。  除非您完全瞭解正在創建具有潛在危險的故障模式，否則不要跨區域與區域方案。  此方案允許提供靈活性。

- 如果應用程式有兩個元件，如 IP 位址和具有託管磁片的虛擬機器，並且它們分別保證為區域冗余和區域 1，則端到端服務將生存區域 2、區域 3 或兩者的區域故障，除非區域 1 發生故障。  不過，如果您所關注的都只是前端的可存取性，您研判服務健康情況的能力就會削弱。  請考慮開發更大範圍的健康情況和容量模型。  您可以一起使用區域備援和區域性概念來擴充見解與管理能力。

- 如果您的應用程式在三個區域中有兩個元件 (例如，區域備援 Load Balancer 前端和跨區域虛擬機器擴展集)，則在區域失敗期間，不受失敗影響之區域中的資源仍將可供使用，但您的端對端服務容量可能會降低。 從基礎結構的觀點而言，您的部署在歷經一或多個區域失敗後仍可存留，而這就產生了下列問題：
  - 您是否了解應用程式如何判讀這類失敗和降低的容量？
  - 您的服務中是否需要有保護措施，以在必要時強制容錯移轉至某個區域組？
  - 您將如何監視、偵測並消弭這種案例？ 您可以使用標準 Load Balancer 診斷，以加強您對於端對端服務效能的監視。 請考量可用項目以及可能需要增強的層面，以勾勒完整藍圖。

- 區域有助於您了解及抑制失敗。  不過，就逾時、重試和輪詢演算法等概念而言，區域失敗與其他失敗並無不同。 即使 Azure Load Balancer 提供區域備援路徑，而且會嘗試在封包層級即時快速復原，但在失敗發生時，仍可能會執行重新傳輸或重新建立，因此請務必了解您的應用程式處理失敗的方式。 您的負載平衡配置將繼續存留，但您必須進行下列規劃：
  - 當區域失敗時，您的端對端服務是否了解此狀況？且在狀態遺失時，您將如何復原？
  - 區域回復時，您的應用程式了解如何安全地收斂嗎？

查看[Azure 雲設計模式](https://docs.microsoft.com/azure/architecture/patterns/)以提高應用程式對故障方案的恢復能力。

## <a name="next-steps"></a>後續步驟
- 瞭解有關[可用性區域](../availability-zones/az-overview.md)的更多資訊
- 瞭解有關[標準負載等化器](load-balancer-standard-overview.md)的更多
- 了解如何[使用具有區域前端的標準 Load Balancer 來進行區域內的 VM 負載平衡](load-balancer-standard-public-zonal-cli.md)
- 了解如何[使用具有區域備援前端的標準 Load Balancer 來進行跨區域的 VM 負載平衡](load-balancer-standard-public-zone-redundant-cli.md)
- 瞭解[Azure 雲設計模式](https://docs.microsoft.com/azure/architecture/patterns/)，以提高應用程式對故障方案的恢復能力。
