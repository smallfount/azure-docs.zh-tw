---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Velpic SAML 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Velpic SAML 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f699e2244cde53200a9885602bd20c899c0ce4cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "72241552"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Velpic SAML 整合

在此教學課程中，您將了解如何整合 Velpic SAML 與 Azure Active Directory (Azure AD)。 在整合 Velpic SAML 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Velpic SAML 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Velpic SAML。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Velpic SAML 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Velpic SAML 支援由 **SP** 起始的 SSO

## <a name="adding-velpic-saml-from-the-gallery"></a>從資源庫新增 Velpic SAML

若要設定 Velpic SAML 與 Azure AD 整合，您需要從資源庫將 Velpic SAML 新增到受控 SaaS app 清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Velpic SAML**。
1. 從結果面板選取 [Velpic SAML]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>設定及測試 Velpic SAML 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Velpic SAML 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Velpic SAML 中相關使用者之間的連結關聯性。

若要設定及測試與 Velpic SAML 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Velpic SAML SSO](#configure-velpic-saml-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Velpic SAML 測試使用者](#create-velpic-saml-test-user)** - 使 Velpic SAML 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Velpic SAML]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<sub-domain>.velpicsaml.net`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > 請注意，登入 URL 會由 Velpic SAML 小組提供，而識別碼值則會在您於 Velpic SAML 端設定 SSO 外掛程式時可供取得。 您需要從 Velpic SAML 應用程式頁面將該值複製並貼到此處。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Velpic SAML]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Velpic SAML 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Velpic SAML]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-velpic-saml-sso"></a>設定 Velpic SAML SSO

1. 若要自動執行 Velpic SAML 內的設定，您必須按一下 [安裝擴充功能]  來安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，按一下 [設定 Velpic SAML]  便會將您導向到 Velpic SAML 應用程式。 請從該處提供用以登入 Velpic SAML 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 8。

    ![設定組態](common/setup-sso.png)

3. 如果您想要手動設定 Velpic SAML，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Velpic SAML 公司網站，然後執行下列步驟：

4. 按一下 [管理]  索引標籤，然後移至 [整合]  區段，您必須在此處按一下 [外掛程式]  按鈕以建立用於登入的新外掛程式。

    ![外掛程式](./media/velpicsaml-tutorial/velpic_1.png)

5. 按一下 [新增外掛程式]  按鈕。
    
    ![外掛程式](./media/velpicsaml-tutorial/velpic_2.png)

6. 按一下 [新增外掛程式] 頁面中的 [SAML]  圖格。
    
    ![外掛程式](./media/velpicsaml-tutorial/velpic_3.png)

7. 為新的 SAML 外掛程式輸入名稱，然後按一下 [新增]  按鈕。

    ![外掛程式](./media/velpicsaml-tutorial/velpic_4.png)

8. 輸入詳細資料，如下所示︰

    ![外掛程式](./media/velpicsaml-tutorial/velpic_5.png)

    a. 在 [名稱]  文字方塊中，輸入 SAML 外掛程式的名稱。

    b. 在 [簽發者 URL]  文字方塊中，貼上您從 Azure 入口網站的 [設定登入]  視窗中複製的 [Azure AD 識別碼]  。

    c. 在 [提供者中繼資料設定]  中，上傳您從 Azure 入口網站下載的中繼資料 XML 檔案。

    d. 您也可以選擇啟用 [自動建立新使用者]  核取方塊，來啟用 SAML 即時佈建。 如果使用者不存在於 Velpic，而且這個旗標未啟用，從 Azure 登入時就會失敗。 如果該旗標已啟用，則系統會在使用者登入時自動將其佈建到 Velpic。 

    e. 從文字方塊複製**單一登入 URL**，並將它貼到 Azure 入口網站。
    
    f. 按一下 [檔案]  。

### <a name="create-velpic-saml-test-user"></a>建立 Velpic SAML 測試使用者

您通常不需要進行此步驟，因為應用程式支援即時使用者佈建。 如果自動使用者佈建未啟用，那麼您可以如下所述手動建立使用者。

以系統管理員身分登入您的 Velpic SAML 公司網站，然後執行下列步驟：
    
1. 按一下 [管理] 索引標籤並移至 [使用者] 區段，然後按一下 [新增] 按鈕來新增使用者。

    ![新增使用者](./media/velpicsaml-tutorial/velpic_7.png)

2. 在 [建立新的使用者]  對話方塊中，執行下列步驟。

    ![user](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. 在 [名字]  文字方塊中，輸入 B 這個名字。

    b. 在 [姓氏]  文字方塊中，輸入 Simon 這個姓氏。

    c. 在 [使用者名稱]  文字方塊中，輸入 B.Simon 的使用者名稱。

    d. 在 [電子郵件]  文字方塊中，輸入 B.Simon@contoso.com 帳戶的電子郵件地址。

    e. 其餘資訊是選擇性的，您可以視需要來填入。
    
    f. 按一下 [儲存]  。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

1. 當您按一下存取面板中的 [Velpic SAML] 圖格時，您應該會看到 Velpic SAML 應用程式的登入頁面。 您應該會在登入頁面上看到 [以 Azure AD 登入]  按鈕。

    ![外掛程式](./media/velpicsaml-tutorial/velpic_6.png)

1. 按一下 [以 Azure AD 登入]  按鈕以使用 Azure AD 帳戶登入 Velpic。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD Velpic 使用 SAML](https://aad.portal.azure.com/)

