---
title: Marketplace ‎商業交易功能和考量 | Azure
description: 此文章描述提供項目類型的交易定價、計費、發票開立和付款考量。
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: dsindona
ms.openlocfilehash: de93a3c48e0b0aceb447e54e11190f487d1aa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279822"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>商業商業市集交易功能和考量

本文介紹商業市場的以下與商業相關的主題

* Marketplace 發行選項
* 交易一般概觀
* 交易計費模型
* 交易需求

## <a name="marketplace-publishing-options"></a>Marketplace 發行選項

以下發布選項可供商業市場發佈商使用。

### <a name="list--trial-publishing-options"></a>清單和試用版發行選項

發佈商可以利用清單、試用版和 BYOL 發佈選項進行促銷和使用者獲取。 使用這些選項，Microsoft 不會直接參與發行者的軟體許可證交易，並且沒有關聯的交易費用。 發行者要負責支援軟體授權交易的所有層面，包含但不限於：訂購、履行、計量、計費、發票開立、付款與收帳。 使用清單和試用版發行選項，發行者保有從客戶收取的發行者軟體授權費用的 100%。 

### <a name="transact-publishing-option"></a>交易發行選項

除了清單和試用發佈選項外，發行商還可以使用交易發佈選項。 這利用了 Microsoft 的全球可用商務功能，並允許 Microsoft 代表發行者託管雲市場事務。

## <a name="transact-general-overview"></a>交易一般概觀

使用交易發佈選項時，Microsoft 允許銷售協力廠商軟體，並將某些產品/服務類型部署到客戶的 Azure 訂閱。 在選擇計費模型和產品/服務類型時，發行者必須考慮基礎結構費用的計費以及發行者自己的軟體許可費用。

當前支援以下產品類型（虛擬機器、Azure 應用程式和 SaaS 應用）的 Transact 發佈選項。


![[在 Azure Marketplace 中交易企業交易]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>基礎結構成本計費

**虛擬機器和 Azure 應用程式**

對於虛擬機器和 Azure 應用程式，Azure 基礎結構使用費將計費到客戶的 Azure 訂閱。  基礎設施使用費在客戶發票上與軟體供應商的許可費分開定價和列報。

**SaaS 應用程式**

對於 SaaS 應用程式，發行者必須將 Azure 基礎結構使用費用及軟體授權費用為算為單一的成本項目。  它表示為客戶收取固定費用。 Azure 基礎結構使用量是對合作夥伴直接管理及計費。  客戶不會看到實際的基礎結構使用量費用。  發行者通常選擇將 Azure 基礎結構使用量費用算在他們的軟體授權定價中。  軟體授權費用不是計量付費或以使用情況為基礎。

## <a name="transact-billing-models"></a>交易計費模型

根據所使用的交易選項，發行者的軟體許可費用可以顯示如下：  

* 免費：軟體授權不收費。 

* 自備授權 (BYOL)：軟體授權任何適用的費用，在發行者與客戶之間直接管理。 Microsoft 僅處理 Azure 基礎結構費用。 (僅限虛擬機器和 Azure 應用程式。)

* 隨用隨付：軟體授權費用會依使用的 Azure 基礎結構，呈現為每小時、每核心 (vCPU) 費率。 這只適用於虛擬機器和 Azure 應用程式。

* 訂閱定價：軟體許可費以每月或每年定期費用的形式提供，按統一費率或按席位計費。 這僅適用于 SaaS 應用和 Azure 應用程式 - 託管應用。

* 軟體免費試用版：30 天或 90 天軟體授權不收費。

### <a name="free-and-bring-your-own-license-byol-pricing"></a>免費和自備授權 (BYOL) 定價

在發行免費或自備授權交易供應項目的情況下，Microsoft 不會扮演為您的您軟體授權費用進行輔助銷售交易的角色。 如同清單和試用版發行選項，發行者保有發行者軟體授權費用的 100%。 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>隨用隨付和月租方案 (網站型) 定價

在發行隨用隨付或月租方案交易供應項目的情況下，Microsoft 會提供處理軟體授權購買、退貨及退款的技術和服務。 在此案例中，發行者針對這些目的授權 Microsoft 為代理人。 發行者允許 Microsoft 協助軟體授權交易，同時保留其為賣方、提供者、散發者和授權人的角色。

Microsoft 使客戶能夠訂購、許可和使用發佈商軟體，但須遵守 Microsoft 商業應用商店和發佈商使用者授權合約的條款及條件。 發佈商必須在創建產品/服務時提供其使用者授權合約或選擇[標準合同](https://docs.microsoft.com/azure/marketplace/standard-contract)。


### <a name="free-software-trials"></a>免費軟體試用

針對交易發行的案例，發行者可以讓授權在 30 天或 90 天內可免費取得。 此折扣功能不包含因使用合作夥伴解決方案而產生的 Azure 基礎結構使用量成本。

### <a name="private-offers"></a>私人供應項目

除了使用產品/服務類型和計費模型將產品/服務貨幣化外，發佈商還可以處理私人產品/服務，並完成協商、特定于交易的定價或自訂配置。 所有 3 個交易發行選項都支援私人供應項目。

此選項允許比公開產品更高或更低的定價。 私人供應項目可用於為供應項目折扣或加入進階功能。 藉由將客戶的 Azure 訂用帳戶加入供應項目層級的允許清單，他們就能取得私人供應項目。


### <a name="examples"></a>範例

**即用即付** 

* 如果您啟用 [隨用隨付] 選項，就會有下列成本結構。

|授權成本  | 每小時美金 $1.00 元  |
|---------|---------|
|Azure 使用量成本 (D1/1 核心)    |   每小時美金 $0.14 元     |
|*由 Microsoft 向客戶收取的費用*    |  *每小時美金 $1.14 元*       |

* 在此案例中，使用您發佈的 VM 映像時，Microsoft 會收取每小時 $1.14 美元的費用。

|Microsoft 收取的費用  | 每小時美金 $1.14 元  |
|---------|---------|
|Microsoft 向您支付授權成本的 80%|   每小時美金 $0.80 元     |
|Microsoft 保留授權成本的 20%  |  每小時美金 $0.20 元       |
|Microsoft 保留 Azure 使用量成本的 100% | 每小時美金 $0.14 元 |

**自帶許可證 （BYOL）**

* 如果您啟用 [BYOL] 選項，就會有下列成本結構。

|授權成本  | 由您協商和收取的授權費用  |
|---------|---------|
|Azure 使用量成本 (D1/1 核心)    |   每小時美金 $0.14 元     |
|*由 Microsoft 向客戶收取的費用*    |  *每小時美金 $0.14 元*       |

* 在此案例中，使用您發佈的 VM 映像時，Microsoft 會收取每小時 $0.14 美元的費用。

|Microsoft 收取的費用  | 每小時美金 $0.14 元  |
|---------|---------|
|Microsoft 保留 Azure 使用量成本    |   每小時美金 $0.14 元     |
|Microsoft 保留授權成本的 0%   |  每小時美金 $0.00 元       |

**SaaS 應用程式訂閱**

此選項必須配置為通過 Microsoft 銷售，並且可以按固定費率定價，也可以按月或按年按每個使用者定價。
*    如果為 SaaS 產品/服務啟用"通過 Microsoft 銷售"選項，則具有以下成本結構。

|授權成本       | 每月 $100.00  |
|--------------|---------|
|Azure 使用量成本 (D1/1 核心)    | 直接向發行者收費，不是向客戶收費 |
|*由 Microsoft 向客戶收取的費用*    |  *每月 $100.00 (注意：發行者必須在授權費用中計算任何產生的或處理的基礎結構成本)*  |

* 在此案例中，Microsoft 針對您的軟體授權收費 $100.00，並支付 $80.00 給發行者。
* 符合減少市場服務費條件的合作夥伴將在 2019 年 5 月至 2020 年 6 月期間降低 SaaS 優惠的交易費用。 在這種情況下，Microsoft 會為您的軟體許可證支付 100.00 美元，然後向發行者支付 90.00 美元。

|Microsoft 收取的費用  | 每月 $100.00  |
|---------|---------|
|Microsoft 向您支付授權成本的 80% <br> \*Microsoft 為任何合格的 SaaS 應用支付 90% 的許可證成本   |   每月 $80.00 <br> \*$90.00 每月    |
|Microsoft 保留授權成本的 20% <br> \*對於任何合格的 SaaS 應用，Microsoft 保留 10% 的許可證成本。  |  每月 $20.00 <br> \*$100.00     |

* **降低市場服務費：** 對於您在我們的商業市場上發佈的某些 SaaS 產品，Microsoft 將降低其市場服務費從 20%（如 Microsoft 發佈商協定中所述）降至 10%。  為了使您的產品符合資格，Microsoft 必須至少將其中一種產品指定為 IP 共同銷售就緒或 IP 共同銷售優先順序。 要獲得該月此降低的市場服務費，必須在上一個日曆月結束前至少五 （5） 個工作日滿足資格。 降低的市場服務費不適用於 VM、託管應用或通過我們的商業市場提供的任何其他產品。  此降低的市場服務費將提供給合格的產品/服務，Microsoft 將在 2019 年 5 月 1 日至 2020 年 6 月 30 日期間收取許可費。  之後，市場服務費將恢復到正常金額。

### <a name="customer-invoicing-payment-billing-and-collections"></a>客戶發票開立、付款和收帳

**發票開立和付款**

發佈商可以使用客戶首選的開票方式提供訂閱或 PAYGO 軟體許可費用。

**企業協定** 

如果客戶的首選開票方法是 Microsoft 企業協定，則軟體許可費用將使用此開票方法作為明細成本計費，與任何特定于 Azure 的使用成本分開。

**信用卡和每月發票** 

客戶也可以使用信用卡和每月發票來付款。 在此情況下，您的軟體授權費用的計費方式會如同 Enterprise 合約案例，作為分項成本獨立於任何 Azure 特定的使用量成本。

例如，如果客戶使用信用卡購買：

|描述    |    Date  |
|----------|----------|
|訂單期間   | 2018 年 8 月 15 日 - 2018 年 8 月 30 日 |
|期間結束 (月)   | 2018 年 8 月 30 日 |
|計費日期 | 2018 年 9 月 1 日 |
|客戶付款日期 | 2018 年 9 月 1 日 |
|委付期間 (僅信用卡，30 天) | 九月 1， 2018 - 九月 30， 2018 |
|收帳期間開始 | 2018 年 9 月 1 日 |
|收帳期間結束 (最大值，30 天) | 2018 年 9 月 30 日 |
|付款計算日期 (每月第 15 日) | 2018 年 10 月 1 日 |
|付款日期 | 2018 年 10 月 15 日 |

如果客戶使用 Enterprise 合約購買：

| 描述 |    Date  |
|----------|----------|
|訂單期間 | 2018 年 8 月 15 日 - 2018 年 8 月 30 日 |
|期間結束 (季) | 2018 年 9 月 30 日 |
|計費日期 | 2018 年 10 月 15 日 |
|委付期間 (僅信用卡，30 天) | n/a |
|收帳期間開始 | 2018 年 10 月 15 日 |
|收帳期間結束 (最大值，90 天) | 2019 年 1 月 15日 |
|客戶付款日期 | 2018 年 12 月 30 日 |
|付款計算日期 (每月第 15 日) | 2019 年 1 月 15日 |
|付款日期 | 2019 年 2 月 15 日 |

**免費點數與金錢履約承諾** 

有些客戶選擇要使用金錢履約承諾，在 Enterprise 合約中預付給 Azure，或已收到用於 Azure 的免費點數。 雖然可以使用這些點數來支付 Azure 使用量，但不能用它們支付發行者軟體授權費用。

**計費和收帳** 

發行者軟體授權計費是使用客戶所選取的發票開立方法來呈現，並遵循發票開立時間軸。 系統會針對市集軟體授權，向沒有 Enterprise 合約的客戶每月計費。 系統會透過每季呈現的發票，向有 Enterprise 合約的客戶計費。

在選取月租方案或隨用隨付定價模型的情況下，Microsoft 是作為發行者的代理人，負責計費、付款和收帳的所有層面。

### <a name="publisher-payout-and-reporting"></a>發行者付款和報告

* 若未另外指定，任何由 Microsoft 代理收帳的軟體授權費用都依 20% 手續費收費，並在發行者付款時扣除。

* 客戶通常會使用 Enterprise 合約或以信用卡付款的隨用隨付合約來購買。 合約類型會決定計費、發票開立、收帳和付款時間。

>[!NOTE] 
>交易發佈選項的所有報告和見解均可通過合作夥伴中心的雲合作夥伴門戶或分析部分的"見解"部分獲得。

#### <a name="billing-questions-and-support"></a>計費問題和支援

如需詳細資訊和法律原則，請參閱可在 Cloud Partner 入口網站取得的[發行者合約](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) \(英文\)。

要獲取有關計費問題的説明，請聯繫[商業市場發佈商支援](https://aka.ms/marketplacepublishersupport)。

## <a name="transact-requirements"></a>交易需求

本節涵蓋不同供應項目類型的交易需求。

### <a name="requirements-for-all-offer-types"></a>所有提供項目類型的需求

- 無論產品/服務/服務的價格模式如何，交易發佈選項都需要 Microsoft 帳戶和財務資訊。
- 強制性財務資訊包括付款帳戶和稅務設定檔。

有關設置這些帳戶的詳細資訊，請參閱[管理合作夥伴中心帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account)。


### <a name="requirements-for-specific-offer-types"></a>特定供應項目類型的需求

交易發行選項只能用來搭配下列市集供應項目類型： 

**虛擬機器** 

從免費、自備授權或隨用隨付定價模型選取，並以在供應項目層級定義的 SKU 呈現。 在客戶的 Azure 帳單上，Microsoft 將發行者軟體許可證費用與基礎 Azure 基礎結構費用分開顯示。 Azure 基礎結構費用是由使用發行者軟體而產生。

**Azure 應用程式：解決方案範本或受控應用程式** 

必須佈建一或多個虛擬機器，並透過虛擬機器定價總和來提取。 對於單一方案上的受控應用程式，可以選取一般費率的每月月租方案作為定價模型，而不是虛擬機器定價。 在某些情況下，Azure 基礎結構使用費與軟體許可證費用分開傳遞給客戶，但在同一計費報表上。 但是，如果為 ISV 基礎結構費用配置託管應用產品，Azure 資源將計費給發行者，並且客戶將收到固定費用，其中包括基礎結構、軟體許可證和管理服務的成本。

## <a name="next-steps"></a>後續步驟

* 請依據供應項目類型區段，檢閱發佈選項中的資格需求，以完成供應項目的選取和設定。
* 請依據店面檢閱發佈模式，以取得解決方案如何對應至供應項目類型和組態的範例。
