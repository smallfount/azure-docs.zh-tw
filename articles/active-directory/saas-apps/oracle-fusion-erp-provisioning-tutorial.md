---
title: 教程：配置 Oracle 融合 ERP 以自動使用 Azure 活動目錄進行使用者預配 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消向 Oracle Fusion ERP 預配使用者帳戶。
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
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 08a82be5a11ba7b81cd1939fc5b386c161c43480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061171"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>教程：配置 Oracle 融合 ERP 以自動調配使用者

本教程的目的是演示在 Oracle Fusion ERP 和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消向 Oracle Fusion ERP 預配使用者和/或組。

> [!NOTE]
>  本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為預覽版。 有關預覽功能的一般 Microsoft Azure 使用條款的詳細資訊，請參閱[Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [甲骨文融合ERP租戶](https://www.oracle.com/applications/erp/)。
* 具有管理員許可權的 Oracle Fusion ERP 中的使用者帳戶。

## <a name="assign-users-to-oracle-fusion-erp"></a>將使用者分配到 Oracle 融合 ERP 
Azure 活動目錄使用稱為分配的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者和/或組需要訪問 Oracle Fusion ERP。 一旦確定，您可以按照此處的說明將這些使用者和/或組分配給 Oracle Fusion ERP：
 
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>將使用者分配給 Oracle 融合 ERP 的重要提示 

 * 建議將單個 Azure AD 使用者分配給 Oracle Fusion ERP 以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 Oracle Fusion ERP 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有預設存取角色的使用者會從佈建中排除。

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>設置 Oracle 融合 ERP 進行預配

在配置 Oracle Fusion ERP 以使用 Azure AD 進行自動使用者預配之前，您需要在 Oracle Fusion ERP 上啟用 SCIM 預配。

1. 登錄[您的 Oracle 融合 ERP 管理主控台](https://cloud.oracle.com/sign-in)

2. 按一下左上角的導航器。 在 **"工具"** 下，選擇 **"安全主控台**"。

    ![甲骨文融合ERP添加SCIM](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. 導航到**使用者**。
    
    ![甲骨文融合ERP添加SCIM](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. 保存管理員使用者帳戶的使用者名和密碼，您將用於登錄到 Oracle Fusion ERP 管理主控台。 這些值需要在 Azure 門戶中的 Oracle Fusion ERP 應用程式的預配選項卡中的 **"管理員使用者名和密碼****"欄位中輸入**。

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>從庫中添加 Oracle 融合 ERP

要配置 Oracle Fusion ERP 以自動預配 Azure AD，您需要將 Oracle Fusion ERP 從 Azure AD 應用程式庫添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫添加 Oracle Fusion ERP，請執行以下步驟：**

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，輸入**Oracle 融合 ERP**，在結果面板中選擇**Oracle 融合 ERP。**

    ![結果清單中的 Oracle Fusion ERP](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>配置自動使用者預配到 Oracle 融合 ERP 

本節將指導您完成將 Azure AD 預配服務配置為基於 Azure AD 中的使用者和/或組分配在 Oracle Fusion ERP 中創建、更新和禁用使用者和/或組的步驟。

> [!TIP]
> 您也可以按照[Oracle Fusion ERP 單一登入教程](oracle-fusion-erp-tutorial.md)中提供的說明，選擇啟用基於 SAML 的單一登入，以實現 Oracle Fusion ERP。 單一登入可以獨立于自動使用者預配進行配置，儘管這兩個功能相輔相成。

> [!NOTE]
> 要瞭解有關 Oracle Fusion ERP SCIM 終結點的更多資訊，請參閱[REST API 瞭解 Oracle 應用程式雲中的通用功能](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)。

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>要在 Azure AD 中配置 Fuze 的自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Oracle Fusion ERP]****。

    ![應用程式清單中的 Oracle Fusion ERP 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證"** 部分下`https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/`，在**租戶 URL**中輸入 。 在 **"管理員使用者名和密碼"****欄位中輸入**較早檢索的管理員使用者名和密碼。 按一下 Azure AD 和 Oracle 融合 ERP 之間的**測試連接**。 

    ![甲骨文融合ERP添加SCIM](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]****。

8. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到 Oracle 融合 ERP**。

    ![甲骨文融合ERP添加SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. 在**屬性對應**部分中查看從 Azure AD 同步到 Oracle 融合 ERP 的使用者屬性。 選擇為**匹配**屬性的屬性用於匹配 Oracle Fusion ERP 中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![甲骨文融合ERP添加SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄組同步到 Oracle 融合 ERP**。

    ![甲骨文融合ERP集團映射](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. 在**屬性對應**部分中查看從 Azure AD 同步到 Oracle 融合 ERP 的組屬性。 選擇為**匹配**屬性的屬性用於匹配 Oracle Fusion ERP 中的組以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![甲骨文融合ERP集團屬性](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 要為 Oracle Fusion ERP 啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到 Oracle Fusion ERP 的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

    此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 **"同步詳細資訊"** 部分監視進度並關注指向預配活動報告的連結，該報表描述 Azure AD 預配服務在 Oracle Fusion ERP 上執行的所有操作。

    如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Oracle 融合 ERP 僅支援其 SCIM 終結點的基本驗證。
* Oracle 融合 ERP 不支援組預配。
* Oracle 融合 ERP 中的角色映射到 Azure AD 中的組。 要從 Azure AD 向 Oracle Fusion ERP 中的使用者分配角色，您需要將使用者分配給以 Oracle Fusion ERP 中的角色命名的所需的 Azure AD 組。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
