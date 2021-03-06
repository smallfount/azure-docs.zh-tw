---
title: 微軟身份平台的代碼示例 |微軟文件
description: 提供可用 Microsoft 識別平臺 (v2.0 終結點) 代碼範例的索引,按方案組織。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d8f69096544b0c3b51513d0cb5c4330fcbb1c18c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419838"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>微軟識別平台代碼範例 (v2.0 終結點)

您可以使用 Microsoft 身分識別平台：

- 將驗證和授權新增至您的 Web 應用程式與 Web API。
- 要求存取權杖以存取受保護的 Web API。

本文簡要介紹並為您提供指向 Microsoft 標識平台終結點示例的連結。 這些示例向您展示了如何完成,並提供可在應用程式中使用的代碼段。 在代碼示例頁上,您將找到詳細的 readme 主題,這些主題有助於滿足要求、安裝和設置。 代碼中的註解可説明您瞭解關鍵部分。

> [!NOTE]
> 如果您對 v1.0 示例感興趣,請參閱[Azure AD 代碼範例(v1.0 終結點)。](../azuread-dev/sample-v1-code.md)

要瞭解每種範例類型的基本方案,請參閱[Microsoft 識別平台終結點的應用類型](v2-app-types.md)。

您也可以在 GitHub 上參與範例。 若要了解做法，請參閱 [Microsoft Azure Active Directory 範例與文件](https://github.com/Azure-Samples?page=3&query=active-directory)。

## <a name="single-page-applications"></a>單頁應用程式

這些範例展示如何編寫使用Microsoft識別平臺保護的單頁應用程式。 這些示例使用MSAL.js的口味之一。

| 平台 | 描述 | 連結 |
| -------- | --------------------- | -------- |
| ![此影像顯示 JavaScript](media/sample-v2-code/logo_js.png)徽標[JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 呼叫 Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) (英文) |
| ![此影像顯示 JavaScript](media/sample-v2-code/logo_js.png)徽標[JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 呼叫 B2C |[b2c-javascript-msal 單頁應用](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![此影像顯示 JavaScript](media/sample-v2-code/logo_js.png)徽標[JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 呼叫自己的 Web API |[javascript-單頁應用-點網-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![此影像顯示角徽標](media/sample-v2-code/logo_angular.png) [JavaScript(MSAL 角)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| 呼叫 Microsoft Graph  | [活動-目錄-javascript-單頁應用-角](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![此影像顯示角徽標](media/sample-v2-code/logo_angular.png) [JavaScript(MSAL 角)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | 呼叫 B2C |[活動-目錄-b2c-javascript-角-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |

## <a name="web-applications"></a>Web 應用程式

下列範例會說明登入使用者的 Web 應用程式。 部分範例也會示範呼叫 Microsoft Graph 或使用使用者身分識別呼叫您自有 Web API 的應用程式。

| 平台 | 僅登入使用者 | 登入使用者與呼叫 Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![此影像顯示ASP.NET核心徽標](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET核心 2.2 | [ASP.NET核心 WebApp 登入使用者教學](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET核心 Web 應用](https://aka.ms/aspnetcore-webapp-call-msgraph)中的相同範例為 Microsoft 圖形階段 |
| ![此影像顯示ASP.NET徽標](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET 快速入門](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2) (英文)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) (英文) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![此影像顯示 Java 徽標](media/sample-v2-code/logo_java.png)  |                   | [ms-身份-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![此影像顯示 Python 標誌](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![此影像顯示紅寶石徽標](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>桌上型與行動裝置公開用戶端應用程式

以下範例顯示存取 Microsoft 圖形 API 的公共用戶端應用程式(桌面或行動應用程式),或者以使用者的名義訪問您自己的 Web API。 所有這些用戶端應用程式都使用 Microsoft 身份驗證庫 (MSAL)。

| 用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph | 呼叫ASP.NET核心 2.0 Web API |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| 桌上型 (WPF)      | ![此影像顯示 .NET/C# 徽標](media/sample-v2-code/logo_NET.png) | [互動](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) (英文) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) (英文) |
| 桌上型 (主控台)   | ![此影像顯示 .NET/C+ (桌面) 徽標](media/sample-v2-code/logo_NET.png) | [整合式 Windows 驗證](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| 桌上型 (主控台)   | ![此影像顯示 Java 徽標](media/sample-v2-code/logo_java.png) | [整合式 Windows 驗證](msal-authentication-flows.md#integrated-windows-authentication) |[ms-身份-java-桌面](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 桌上型 (主控台)   | ![此影像顯示 .NET/C+ (桌面) 徽標](media/sample-v2-code/logo_NETcore.png) | [使用者名稱/密碼](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| 帶 WAM 桌面(主控台)  | ![此影像顯示 .NET/C+ (桌面) 徽標](media/sample-v2-code/logo_NETcore.png) | [與 WAM 互動](msal-authentication-flows.md#interactive) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| 桌上型 (主控台)   | ![此影像顯示 Java 徽標](media/sample-v2-code/logo_java.png) | [使用者名稱/密碼](msal-authentication-flows.md#usernamepassword) |[ms-身份-java-桌面](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 桌上型 (主控台)   | ![此影像顯示 Python 標誌](media/sample-v2-code/logo_python.png) | [使用者名稱/密碼](msal-authentication-flows.md#usernamepassword) |[ms-身份-python-桌面](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| 行動裝置 (Android、iOS、UWP)   | ![此影像顯示 .NET/C# (Xamarin) 徽標](media/sample-v2-code/logo_xamarin.png) | [互動](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) (英文) |  |
| 行動裝置 (iOS)       | ![此圖片顯示 iOS/目標 C 或 Swift](media/sample-v2-code/logo_iOS.png) | [互動](msal-authentication-flows.md#interactive) |[ios-swift-objc-本機-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) (英文) |  |
| 桌面 (macOS)       | macOS | [互動](msal-authentication-flows.md#interactive) |[macOS-swift-objc-本機-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| 移動(安卓-爪哇)   | ![此圖片顯示 Android 徽標](media/sample-v2-code/logo_Android.png) | [互動](msal-authentication-flows.md#interactive) |  [安卓-爪哇](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| 手機(安卓-科特林)   | ![此圖片顯示 Android 徽標](media/sample-v2-code/logo_Android.png) | [互動](msal-authentication-flows.md#interactive) |  [安卓-科特林](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>精靈應用程式

下列範例說明如何使用其自有的身分識別 (無使用者) 存取 Microsoft Graph API 的應用程式。

| 用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| 主控台 | ![此影像顯示 .NET 核心徽標](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [用戶端認證](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web 應用程式 | ![此影像顯示ASP.NET徽標](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [用戶端認證](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) (英文) |
| 主控台 | ![此影像顯示 Java 徽標](media/sample-v2-code/logo_java.png) | [用戶端認證](msal-authentication-flows.md#client-credentials) | [ms-身份-java-守護程式](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| 主控台 | ![此影像顯示 Python 標誌](media/sample-v2-code/logo_python.png) | [用戶端認證](msal-authentication-flows.md#client-credentials) | [ms-身份-python-守護神](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>無周邊應用程式

下列範例示範在裝置 (沒有網頁瀏覽器) 上執行的公開用戶端應用程式。 該應用程式可以是命令列工具、在 Linux 或 Mac 上運行的應用程式或 IoT 應用程式。 該示例以在另一台設備(如行動電話)上以互動登錄的用戶的名義,提供訪問Microsoft圖形API的應用。 此用戶端應用程式使用 Microsoft 身份驗證庫 (MSAL)。

| 用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| 桌上型 (主控台)   | ![此影像顯示 .NET/C+ (桌面) 徽標](media/sample-v2-code/logo_NETcore.png) | [裝置程式碼流程](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| 桌上型 (主控台)   | ![此影像顯示 Java 徽標](media/sample-v2-code/logo_java.png) | [裝置程式碼流程](msal-authentication-flows.md#device-code) |[ms-身份-java-裝置代碼串流](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| 桌上型 (主控台)   | ![此影像顯示 Python 標誌](media/sample-v2-code/logo_python.png) | [裝置程式碼流程](msal-authentication-flows.md#device-code) |[ms-身份-python-裝置碼流](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Web API

以下範例展示如何使用Microsoft識別平台終結點保護Web API,以及如何從Web API呼叫下游API。

| 平台 | 範例 |
| -------- | ------------------- |
| ![此影像顯示ASP.NET核心徽標](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET核心 2.2 | ASP.NET[點網-本機-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)的核心 Web API(服務)  |
| ![此影像顯示ASP.NET徽標](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [ms-身份-阿斯普內-Webapi 代表的](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof)Web API(服務) |
| ![此影像顯示 Java 徽標](media/sample-v2-code/logo_java.png) | [ms-身份-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)的 Web API(服務) |
| ![此影像顯示 Node.js 徽標](media/sample-v2-code/logo_nodejs.png) | [活動-目錄-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)的 Web API(服務) |
| ![此影像顯示 Node.js 徽標](media/sample-v2-code/logo_nodejs.png) | B2C Web API(服務)的活動[目錄-b2c-javascript-節點js-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure 函數為 Web API

以下範例展示如何使用 HttpTrigger 使用 HttpTrigger 和使用 Microsoft 識別平台終結點公開 Web API 來保護 Azure 函數,以及如何從 Web API 呼叫下游 API。

| 平台 | 範例 |
| -------- | ------------------- |
| ![此影像顯示ASP.NET核心徽標](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET核心 2.2 | ASP.NET[點網本機-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)的核心 Web API(服務)Azure 函數  |
| ![此影像顯示 Node.js 徽標](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | NodeJS 的 Web API(服務)[和護照-azure 廣告](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![此影像顯示 Python 標誌](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)的 Web API(服務) |
| ![此影像顯示 Node.js 徽標](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | NodeJS 的 Web API(服務)[與代表](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>其他 Microsoft Graph 範例

若要了解示範 Microsoft Graph API 不同使用模式 (包括使用 Azure AD 驗證) 的[範例](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) (英文) 和教學課程，請參閱 [Microsoft Graph 社群範例和教學課程](https://github.com/microsoftgraph/msgraph-community-samples) (英文).

## <a name="see-also"></a>另請參閱

- [Azure 活動目錄 (v1.0) 開發人員指南](../azuread-dev/v1-overview.md)
- [微軟圖形 API 概念和參考](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
