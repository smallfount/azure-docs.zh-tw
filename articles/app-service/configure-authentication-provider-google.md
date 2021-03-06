---
title: 設定 Google 驗證
description: 瞭解如何將 Google 身份驗證配置為應用服務或 Azure 功能應用的標識提供者。
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: e8a9fbe6072f3628d755ad3ad5aa5a623fc3ab23
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519935"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>設定應用服務或 Azure 功能應用以使用 Google 登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題介紹如何配置 Azure 應用服務或 Azure 功能以使用 Google 作為身份驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。 若要建立新的 Google 帳戶，請前往 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)。

## <a name="register-your-application-with-google"></a><a name="register"> </a>向 Google 註冊您的應用程式

1. 按照[Google 登錄伺服器端應用](https://developers.google.com/identity/sign-in/web/server-side-flow)的文件創建用戶端 ID 和用戶端機密。 無需進行任何代碼更改。 只需使用以下資訊:
    - 對於**授權 JavaScript**`https://<app-name>.azurewebsites.net`源, 請與*\<應用名稱>中的應用名稱*一起使用。
    - 此**選項**, 請使用`https://<app-name>.azurewebsites.net/.auth/login/google/callback`此選項 。
1. 複製應用 ID 和應用機密值。

    > [!IMPORTANT]
    > 應用密鑰是一個重要的安全憑據。 請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>將 Google 資訊新增至應用程式

1. 在[Azure 門戶]中,轉到應用服務應用。
1. 選擇 **「設定** > **身份驗證/授權**」,並確保**應用服務身份驗證****處於打開。**
1. 選擇**Google**,然後貼上到您以前獲得的應用 ID 和應用機密值中。 啟用應用程式所需的任何作用域。
1. 選取 [確定]  。

   應用服務提供身份驗證,但不限制對網站內容和 API 的授權訪問。 有關詳細資訊,請參閱[授權或拒絕使用者](app-service-authentication-how-to.md#authorize-or-deny-users)。

1. ( 選擇性的 )要僅限制使用者訪問 Google,請設置 **「操作」,當請求未**對**Google**進行身份驗證時執行。 設置此功能時,應用要求對所有請求進行身份驗證。 它還將所有未經身份驗證的請求重定向到 Google 進行身份驗證。

    > [!CAUTION]
    > 以這種方式限制訪問適用於對應用的所有調用,對於具有公開可用主頁的應用(如許多單頁應用程式中)來說,這可能不可取。 對於此類應用程式,可能首選**允許匿名請求(不執行任何操作),** 以便應用手動啟動身份驗證本身。 有關詳細資訊,請參閱[身份驗證流](overview-authentication-authorization.md#authentication-flow)。

1. 選取 [儲存]  。

現在，您已可在應用程式中使用 Google 進行驗證。

## <a name="next-steps"></a><a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 入口網站]: https://portal.azure.com/

