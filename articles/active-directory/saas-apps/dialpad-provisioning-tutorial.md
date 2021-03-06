---
title: 教程：使用 Azure 活動目錄配置撥號板以自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消向 Dialpad 預配使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058334"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置撥號板

本教程的目的是演示在 Dialpad 和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消將使用者和/或組預配到撥號板。

> [!NOTE]
>  本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。

> 此連接器目前為預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [撥號板租戶](https://www.dialpad.com/pricing/)。
* 具有管理員許可權的撥號板中的使用者帳戶。

## <a name="assign-users-to-dialpad"></a>將使用者分配到撥號板
Azure 活動目錄使用稱為分配的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者和/或組需要訪問 Dialpad。 決定後，您可以按照此處的說明將這些使用者和/或組分配給 Dialpad：
 
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>將使用者分配給撥號板的重要提示

 * 建議將單個 Azure AD 使用者分配給 Dialpad 以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 Dialpad 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有預設存取角色的使用者會從佈建中排除。

## <a name="setup-dialpad-for-provisioning"></a>設置撥號板進行預配

在使用 Azure AD 配置撥號板以進行自動使用者預配之前，需要從撥號板檢索一些預配資訊。

1. 登錄到您的[撥號板管理主控台](https://dialpadbeta.com/login)並選擇**管理設置**。 確保從下拉清單中選擇**我的公司**。 導航到**API 金鑰>身份驗證**。

    ![撥號板添加 SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. 通過按一下"**添加金鑰"** 並配置金鑰權杖的屬性來生成新金鑰。

    ![撥號板添加 SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![撥號板添加 SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. **按一下"按一下"以顯示**最近創建的 API 金鑰的值按鈕，並複製顯示的值。 此值將在 Azure 門戶中的 Dialpad 應用程式的預配選項卡中的 **"機密權杖"** 欄位中輸入。 

    ![撥號板創建權杖](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>從庫中添加撥號板

要配置 Dialpad 以使用 Azure AD 進行自動使用者預配，需要將 Dialpad 從 Azure AD 應用程式庫添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫添加撥號板，請執行以下步驟：**

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，在結果面板中輸入**撥號板**，選擇**撥號板**。
    ![結果清單中的撥號板](common/search-new-app.png)

5. 導航到下面在單獨的瀏覽器中突出顯示的**URL。** 

    ![撥號板添加 SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. 在右上角，選擇 **"登錄>連線使用撥號板**。

    ![撥號板添加 SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. 由於撥號板是 OpenIDConnect 應用，因此選擇使用 Microsoft 工作帳戶登錄到撥號板。

    ![撥號板添加 SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. 身份驗證成功後，接受同意頁的同意提示。 然後，應用程式將自動添加到您的租戶，您將被重定向到您的撥號板帳戶。

    ![撥號板添加 SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>將自動使用者預配配置為撥號板

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 Dialpad 中創建、更新和禁用使用者和/或組的步驟。

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>要在 Azure AD 中配置撥號板的自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選擇**撥號板**。

    !["應用程式"清單中的撥號板連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證"** 部分下`https://dialpad.com/scim`，在**租戶 URL**中輸入 。 輸入您以前從"**秘密權杖**"中的 Dialpad 檢索和保存的值。 按一下 **"測試連接**"以確保 Azure AD 可以連接到撥號板。 如果連接失敗，請確保您的 Dialpad 帳戶具有管理員許可權，然後重試。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]****。

8. 在 **"映射**"部分下，選擇**將 Azure 活動目錄使用者同步到撥號板**。

    ![撥號板使用者映射](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. 在**屬性對應**部分中查看從 Azure AD 同步到撥號板的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Dialpad 中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![撥號板使用者屬性](media/dialpad-provisioning-tutorial/dialpad07.png)

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 要為撥號板啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到 Dialpad 的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 可以使用 **"同步詳細資訊"** 部分監視進度並跟蹤指向預配活動報告的連結，該報表描述 Azure AD 預配服務在 Dialpad 上執行的所有操作。

有關如何讀取 Azure AD 預配日誌的詳細資訊，請參閱[報告自動使用者帳戶預配](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>連接器限制
* 撥號板今天不支援組重命名。 這意味著對 Azure AD 中組顯示**名稱**的任何更改將不會更新並在撥號板中反映出來。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
