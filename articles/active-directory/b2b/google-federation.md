---
title: 添加 Google 作為 B2B - Azure AD 的標識供應商
description: 與 Google 同盟以便讓來賓使用者使用自己的 Gmail 帳戶登入 Azure AD 應用程式
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c18e48c27942c7bea47931ec79a31af941064e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126674"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>將 Google 新增為 B2B 來賓使用者的識別提供者

通過與 Google 建立聯合，您可以允許受邀使用者使用自己的 Gmail 帳戶登錄共用應用和資源，而無需創建 Microsoft 帳戶 （MSA）。 

> [!NOTE]
> Google 聯盟專為 Gmail 使用者而設計。 要與 G Suite 域聯合，請使用[直接聯合功能](direct-federation.md)。

## <a name="what-is-the-experience-for-the-google-user"></a>Google 使用者有何體驗？
當您傳送邀請給 Google Gmail 使用者時，來賓使用者應該會使用包含租用戶內容的連結來存取共用應用程式或資源。 其體驗會因為是否已登入 Google 而有所不同：
  - 如果來賓使用者未登入 Google，系統會提示他們登入 Google。
  - 如果來賓使用者已登入 Google，系統會提示他們選擇所要使用的帳戶。 他們必須選擇您用來邀請他們的帳戶。

來賓使用者若看到「標頭太長」錯誤，則可嘗試清除其 Cookie，也可開啟私密或匿名的視窗，然後再試著登入一次。

![顯示 Google 登錄頁面的螢幕截圖](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>限制

團隊在所有設備上完全支援 Google 訪客使用者。 Google 使用者可以從常見的終結點（如`https://teams.microsoft.com`）登錄到 Teams。

其他應用程式的常見終結點可能不支援 Google 使用者。 Google 來賓使用者必須使用包含租戶資訊的連結登錄。 範例如下所示：
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   如果 Google 來賓使用者嘗試使用連結，如`https://myapps.microsoft.com``https://portal.azure.com`或 ，他們會收到錯誤。

您還可以向 Google 來賓使用者提供指向應用程式或資源的直接連結，只要此連結包含您的租戶資訊，例如`https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`。 

## <a name="step-1-configure-a-google-developer-project"></a>步驟 1︰設定 Google 開發人員專案
首先，在 Google 開發人員主控台中，建立新專案來取得用戶端識別碼和用戶端密碼，以供稍後新增至 Azure AD。 
1. 移至 Google API (網址為：https://console.developers.google.com)，並使用 Google 帳戶來登入。 建議您使用共用小組的 Google 帳戶。
2. 建立新專案：在儀表板上，選取 [建立專案]****，然後選取 [建立]****。 在 [新增專案] 頁面上，輸入 [專案名稱]****，然後選取 [建立]****。
   
   ![顯示 Google 新專案頁面的螢幕截圖](media/google-federation/google-new-project.png)

3. 確實地在 [專案] 功能表中選取新專案。 然後在**API & 服務**下，選擇**OAuth 同意螢幕**。

4. 選擇 **"外部**"，然後選擇 **"創建**"。 
5. 在**OAuth 同意螢幕上**，輸入**應用程式名稱**。 (其他設定保留不變)。

   ![顯示 Google OAuth 同意螢幕選項的螢幕截圖](media/google-federation/google-oauth-consent-screen.png)

6. 捲動至 [授權的網域]**** 區段並輸入 microsoftonline.com。

   ![顯示授權域部分的螢幕截圖](media/google-federation/google-oauth-authorized-domains.png)

7. 選取 [儲存]****。

8. 選擇**憑據**。 在 [建立認證]**** 功能表中，選擇 [OAuth 用戶端識別碼]****。

   ![顯示 Google API 創建憑據選項的螢幕截圖](media/google-federation/google-api-credentials.png)

9. 在 [應用程式類型]**** 底下，選擇 [Web 應用程式]****，然後在 [授權的重新導向 URI]**** 底下，輸入下列 URI：
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(其中，`<directory id>` 是目錄識別碼)
   
     > [!NOTE]
     > 若要尋找目錄識別碼，請移至 https://portal.azure.com，然後在 [Azure Active Directory]**** 底下，選擇 [屬性]**** 並複製 [目錄識別碼]****。

   ![顯示授權重定向 URI 部分的螢幕截圖](media/google-federation/google-create-oauth-client-id.png)

10. 選取 [建立]****。 複製用戶端識別碼和用戶端密碼，以便用來在 Azure AD 入口網站中新增識別提供者。

   ![顯示 OAuth 用戶端 ID 和用戶端機密的螢幕截圖](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>步驟 2：在 Azure AD 中設定 Google 同盟 
現在，您會設定 Google 用戶端識別碼和用戶端密碼，可行方法是在 Azure AD 入口網站中加以輸入，也可以使用 PowerShell 來輸入。 請務必藉由使用 Gmail 地址邀請您自己，並嘗試使用受邀的 Google 帳戶兌換邀請，來測試 Google 同盟設定。 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>在 Azure AD 入口網站中設定 Google 同盟 
1. 轉到[Azure 門戶](https://portal.azure.com)。 在左窗格中，選取 [Azure Active Directory]****。 
2. 選取 [組織關係]****。
3. 選取 [識別提供者]****，然後按一下 [Google]**** 按鈕。
4. 輸入名稱。 然後輸入您稍早取得的用戶端識別碼和用戶端密碼。 選取 [儲存]****。 

   ![顯示添加 Google 標識供應商頁面的螢幕截圖](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>使用 PowerShell 設定 Google 同盟
1. 安裝最新版的 Azure AD PowerShell for Graph 模組 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 執行下列命令：`Connect-AzureAD`。
3. 在出現登入提示時，使用受控的全域系統管理員帳戶來登入。  
4. 執行以下命令： 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > 請使用您在＜步驟 1︰設定 Google 開發人員專案＞所建立的應用程式中，所取得的用戶端識別碼和用戶端密碼。 如需詳細資訊，請參閱 [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) 一文。 
 
## <a name="how-do-i-remove-google-federation"></a>如何移除 Google 同盟？
您可以刪除 Google 同盟設定。 如果您這麼做，已兌換邀請的 Google 來賓使用者將無法登入，但您可以先從目錄刪除這些使用者，再重新邀請他們，藉此為他們提供資源存取權。 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>在 Azure AD 入口網站中刪除 Google 同盟： 
1. 轉到[Azure 門戶](https://portal.azure.com)。 在左窗格中，選取 [Azure Active Directory]****。 
2. 選取 [組織關係]****。
3. 選擇**標識提供程式**。
4. 在**Google**行上，選擇內容功能表 **（...），** 然後選擇 **"刪除**"。 
   
   ![顯示社交標識提供程式的"刪除"選項的螢幕截圖](media/google-federation/google-social-identity-providers.png)

1. 選取 [是]**** 以確認要刪除。 

### <a name="to-delete-google-federation-by-using-powershell"></a>使用 PowerShell 刪除 Google 同盟： 
1. 安裝最新版的 Azure AD PowerShell for Graph 模組 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 執行 `Connect-AzureAD`。  
4. 在登入提示中，使用受控的全域系統管理員帳戶來登入。  
5. 輸入下列命令：

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > 如需詳細資訊，請參閱 [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)。 
