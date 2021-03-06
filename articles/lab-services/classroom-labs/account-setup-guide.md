---
title: Azure 實驗室服務的加速實驗室帳戶設置指南
description: 本指南可説明管理員快速設置實驗室帳戶,供其學校使用。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: 88b37ea4ff717689f05afbb41d33a56a8cbb2c22
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547627"
---
# <a name="lab-account-setup-guide"></a>實驗室帳戶設置指南

管理員需要完成的第一步是在 Azure 訂閱中設置實驗室帳戶。  實驗室帳戶是教室實驗室的容器,只需幾分鐘即可設置。

## <a name="understand-your-schools-lab-account-requirements"></a>瞭解您學校的實驗室帳戶要求

要瞭解如何根據學校的需求配置實驗室帳戶,應考慮以下問題:

**我有權訪問 Azure 訂閱嗎?**

要創建實驗室帳戶,您需要訪問為學校配置的 Azure 訂閱;因此,需要訪問為學校配置的 Azure 訂閱。您的學校可能有一個或多個訂閱。  訂閱用於管理其中使用的所有 Azure 資源(包括實驗室帳戶)的計費和安全。

**需要創建多少個實驗室帳戶?**

要快速入門,一種合理的方法是創建單個實驗室帳戶,然後根據需要創建其他實驗室帳戶。  例如,您最終可能會演變為每個部門有一個實驗室帳戶。

**誰應該是實驗室帳戶的擁有者和貢獻者?**

管理員通常是實驗室帳戶的所有者/貢獻者,因為他們負責管理適用於實驗室帳戶中包含的所有實驗室的策略。  創建實驗室帳戶的人員將自動成為擁有者。  您可以添加其他擁有者/參與者(通常來自與您的訂閱關聯的 AAD 租戶),通過在實驗室帳戶級別分配"擁有者/參與者"角色來説明管理實驗室帳戶。

**誰將被允許創建和管理實驗室?**

您可以選擇讓管理員和\或教職員工創建和管理實驗室;這些使用者(通常來自與您的訂閱關聯的 AAD 租戶)被分配到實驗室帳戶中的實驗室建立者角色。

**是否要使實驗室建立者能夠保存可在實驗室之間共用的圖像?**

共用映射庫是可用於保存和共用圖像的儲存庫。  這樣做的好處是,如果您有多個類需要相同的圖像,實驗室建立者可以創建映射一次,並在實驗室之間共用它。  但是,要開始,在沒有共用圖像庫的情況下開始是完全合理的;並且,您始終可以選擇稍後添加一個。

如果您對此問題回答"是",則需要創建和\或將共用圖像庫附加到實驗室帳戶。  如果你回答,『我不知道』,那麼你可以推遲這個決定,直到以後。

將分享影像庫附加到實驗室帳戶時

**教室實驗室將使用 Azure 應用商店中的哪些映射?**

Azure 應用商店提供了數百個映射,您可以啟用這些映射,以便實驗室建立者可以使用該映射創建其實驗室。  某些圖像可能包含實驗室已經需要的所有內容。  在其他情況下,您可以使用圖像作為起點,然後實驗室建立者可以通過安裝其他應用程式、工具等對其進行自定義。

如果您不知道需要使用哪些圖像,則以後可以隨時返回以啟用它們。  此外,查看哪些圖像可用的最佳方式是首先創建一個實驗室帳戶 - 這將為您提供訪問許可權,以便您可以查看可用圖像及其內容的清單。  下面提供更多資訊。
  
**實驗室的虛擬機 (VM) 是否需要造訪其他 Azure 或預置資源?**

設置實驗室帳戶時,還可以選擇與虛擬網路 (VNet) 對等。  要確定是否需要與 VNet 對等,請考慮以下問題:

- **您是否需要提供對許可伺服器的訪問許可權?**
  
   如果計劃使用 Azure 應用商店映射,操作系統許可證的成本將捆綁到實驗室服務的定價中,*因此無需為*操作系統本身提供許可證。  但是,對於已安裝的其他軟體\應用程式,您需要根據需要提供許可證。

- **實驗室 VM 是否需要造訪其他預置資源,如檔案共享、資料庫等?**

   需要創建 VNet 以提供對網站到網站虛擬網路閘道的訪問。  如果您未配置 VNet,則需要為此投入更多時間。  有關如何設置此功能的詳細資訊,請參閱下文。

- **實驗室 VM 是否需要造訪位於 VNet 中的其他 Azure 資源?**

    如果需要訪問 VNet 中*未*保護的 Azure 資源,則可以通過公共 Internet 訪問這些資源,而無需進行任何對等互連。

    如果您對一個或多個問題回答"是",則需要將實驗室帳戶與 VNet 對等。  如果您回答"我不知道",則可以將此決定推遲到以後,因為在創建實驗室帳戶后,您始終可以選擇對 VNet 進行對等。

## <a name="set-up-your-lab-account"></a>設定實驗室帳戶

了解實驗室帳戶的要求后,即可將其設置。  請按照本節中的連結瞭解如何設置實驗室帳戶:

1. **建立實驗室帳戶**

   有關[創建實驗室帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account)的說明,請參閱教程。

   創建實驗室帳戶時,您可能會發現熟悉所涉及的 Azure 資源會很有説明;有關創建這些資源的詳細資訊和指導,請參閱以下清單:

   - [訂用帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [資源群組](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [實驗室帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [課堂實驗室](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [選擇區域\位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [資源命名指南](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **將使用者新增到實驗室建立者角色**

   有關[將使用者添加到實驗室建立者角色的](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)教程,請參閱有關說明的教程。

   此外,有關可以分配給將管理實驗室帳戶和實驗室的使用者的不同角色的詳細資訊,請參閱[有關管理標識的指南](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)。

3. **連接到對等 VNet**

   有關[將實驗室網路與對等 VNet 連接](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)的操作指南,請參閱說明。

   您可能還需要參考有關[配置實驗室 VM 位址範圍](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)的說明。

4. **開啟與檢視影像**

    有關[實驗室建立者啟用應用商店映射](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)的說明,請參閱操作指南。

    要查看每個應用商店圖像的內容,請單擊圖像名稱。  例如,請參閱以下螢幕截圖,其中顯示了 Ubuntu 資料科學 VM 映射的詳細資訊:

    ![查看應用商店映像](../media/setup-guide/review-marketplace-images.png)

    如果您有一個共用圖像庫附加到實驗室帳戶,並且希望允許自定義圖像由實驗室建立者共用,則需要完成類似步驟,如以下螢幕截圖所示:

    ![在分享影像庫中開啟自訂影像](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>後續步驟

查看下列文章：

- [管理實驗室帳戶](how-to-manage-lab-accounts.md)

- [課堂實驗室設置指南](setup-guide.md)
