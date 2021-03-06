---
title: 將單頁應用移動到生產 - 微軟身份平臺 |蔚藍
description: 瞭解如何構建單頁應用程式（移動到生產）
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01b923e0d013fab1815456e55eac6036ded772bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701836"
---
# <a name="single-page-application-move-to-production"></a>單頁應用程式：移動到生產

現在，您已經瞭解如何獲取權杖來調用 Web API，請瞭解如何遷移到生產。

## <a name="improve-your-app"></a>改進應用

[啟用日誌記錄](msal-logging.md)可使應用生產準備就緒。

## <a name="test-your-integration"></a>測試集成

遵循 [Microsoft 身分識別平台整合檢查清單](identity-platform-integration-checklist.md)來測試您的整合。

## <a name="next-steps"></a>後續步驟

深入深入快速入門示例，其中解釋了如何使用 MSAL.js 登錄使用者獲取訪問權杖以調用 Microsoft 圖形 API 的代碼：

> [!div class="nextstepaction"]
> [JavaScript SPA 教程](./tutorial-v2-javascript-spa.md)

演示如何使用 MSAL.js 獲取自己的後端 Web API 的權杖的示例：

> [!div class="nextstepaction"]
> [帶ASP.NET後端的 SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

演示如何使用 MSAL.js 登錄到已註冊 Azure 活動目錄 B2C（Azure AD B2C）的應用中的使用者的示例：

> [!div class="nextstepaction"]
> [帶 Azure AD B2C 的 SPA](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
