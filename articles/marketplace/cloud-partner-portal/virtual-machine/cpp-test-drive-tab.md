---
title: Azure 應用商店雲合作夥伴門戶中的虛擬機器測試磁碟機選項卡
description: 說明用來建立 Microsoft Azure Marketplace 虛擬機器供應項目的試用產品 索引標籤。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: d4fc8762e25825b21637b16b751d57a0dcbf369e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288796"
---
# <a name="virtual-machine-test-drive-tab"></a>虛擬機器試用產品索引標籤

**新增供應項目**頁面的**試用產品**索引標籤，可讓您為潛在客戶提供實際操作體驗，以自助式方式試用您的產品，包括在真實情況中操作主要功能並體驗各項優點。  試用產品是支援試用產品之供應項目類型的選擇性功能。  試用產品需要正確實作支援資產。  如需詳細資訊，請參閱 [Azure Marketplace 試用產品](https://azure.microsoft.com/blog/azure-marketplace-test-drive/)文章。  <!--TD: Replace with migrated version of Test Drive article! -->

若要啟用這項功能，請在**試用產品**索引標籤上，按一下**啟用試用產品**上的**是**選項。  **試用產品**索引標籤會顯示可供編輯的欄位。  欄位名稱上附加星號 (*) 表示為必填欄位。

![虛擬機器新增供應項目表單上的試用產品索引標籤](./media/publishvm_007.png)


## <a name="field-values"></a>欄位值

下表說明這些欄位的用途和內容。  必要欄位由星號 (*) 標示。


|    欄位                  |       描述                                                            |
|  ---------                |     ---------------                                                          |
|  *詳細資料*   |  |
| **描述\***           | 提供您試用產品情況的概觀。 佈建試用產品時，此文字會顯示給使用者。 此欄位支援基本 HTML，因此您可以提供格式豐富的內容。  |
| **使用者手冊\***           | 上傳詳細的使用者手冊 (.pdf)，協助試用產品使用者了解如何使用您的解決方案。  |
| **試用產品示範影片** | 上傳展示解決方案的影片。  如果您選擇此選項，您必須提供名稱、影片 URL (裝載於 YouTube 或 Vimeo) 和 (533x324 像素) 影片縮圖。 |
| *技術設定* |  |
| **實例\***             | 指定 VM 執行個體的區域可用性和相對可用性 (按一下資訊圖示以了解詳細資料)。  <br/>潛在並行試用產品工作階段不得超過訂用帳戶的配額上限。  前者的計算方式：[選取的區域數目] x [熱執行個體數目] + [選取的區域數目] x [軟執行個體數目] + [選取的區域數目] x [冷執行個體數目] |
| **試駕持續時間\***   | 工作階段持續時間上限 (小時)。 超過此持續時間後，試用產品工作階段將會自動終止。  |
|**試駕 ARM 範本\***| 上傳與此試用產品相關聯的 Azure Resource Manager 範本。 如需詳細資訊，請參閱[試用產品轉換虛擬機器部署範本](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)。 |
| **訪問資訊\***    | Azure Resource Manager 存取和免費試用登入資訊的寫入格式為純文字或簡單 HTML。 |
| *試駕部署訂閱詳細資訊* |  |
| **Azure 訂閱 ID\*** | 您可以透過登入 [Microsoft Azure 入口網站](https://ms.portal.azure.com)並按一下左側功能表列的**訂用帳戶**來取得。 （示例："a83645ac-1234-5ab6-6789-1h234g764ghty"）   此識別碼應為表單`a83645ac-1234-5ab6-6789-1h234g764ghty`的 GUID。|
| **Azure AD 租戶 ID\***    | Azure Active Directory 租用戶識別碼。  您可以透過登入 [Microsoft Azure 入口網站](https://ms.portal.azure.com)，按一下左側功能表列的 **Azure Active Directory**，然後按一下中間功能表列的**屬性**，並複製表單中的**目錄識別碼**來取得。  此識別碼也應為 GUID。  如果沒有識別碼，您必須為組織建立租用戶識別碼。 |
| **Azure AD 應用 ID\***       | 已註冊 Azure VM 解決方案的識別碼  |
| **Azure AD 應用金鑰\***      | 已註冊解決方案的驗證金鑰 |
|   |   |


## <a name="next-steps"></a>後續步驟

您可以在下一個 [Marketplace](./cpp-marketplace-tab.md) 索引標籤中提供解決方案的行銷和法律聲明資訊。
