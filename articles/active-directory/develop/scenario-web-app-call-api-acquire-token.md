---
title: 在調用 Web API 的 Web 應用中獲取權杖 - 微軟身份平臺 |蔚藍
description: 瞭解如何為調用 Web API 的 Web 應用獲取權杖
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: abf7d800eda376c21dfdd672032ddb65e27355be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759069"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>調用 Web API 的 Web 應用：獲取應用的權杖

您已經生成了用戶端應用程式物件。 現在，您將使用它獲取權杖來調用 Web API。 在ASP.NET或ASP.NET核心中，調用 Web API 在控制器中完成：

- 使用權杖緩存獲取 Web API 的權杖。 要獲取此權杖，請調用`AcquireTokenSilent`方法。
- 調用受保護的 API，將訪問權杖作為參數傳遞給它。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

控制器方法受強制`[Authorize]`經過身份驗證的使用者使用 Web 應用的屬性的保護。 下面是調用 Microsoft 圖形的代碼：

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

該服務`ITokenAcquisition`由ASP.NET使用依賴項注入注入。

下面是 的簡化代碼，用於 操作，`HomeController`該代碼獲取權杖以調用 Microsoft Graph：

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

要更好地瞭解此方案所需的代碼，請參閱[ms-身份-aspnetcore-webapp 教程](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)的第 2 階段[（2-1-Web 應用程式調用 Microsoft 圖形](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)）步驟。

還有其他複雜的變化，例如：

- 調用多個 API。
- 處理增量同意和條件訪問。

這些高級步驟在[3-WebApp 多 API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)教程的第 3 章仲介紹。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET的代碼類似于為 ASP.NET Core 顯示的代碼：

- 受 [授權] 屬性保護的控制器操作提取控制器成員的`ClaimsPrincipal`租戶 ID 和使用者 ID。 （ASP.NET`HttpContext.User`使用 .）
- 從那裡，它生成一個MSAL.NET`IConfidentialClientApplication`物件。
- 最後，`AcquireTokenSilent`調用機密用戶端應用程式的方法。

# <a name="java"></a>[JAVA](#tab/java)

在 JAVA 示例中，調用 API 的代碼位於[AuthPageController.java_L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)中的 getUserFromGraph 方法中。

該方法嘗試調用`getAuthResultBySilentFlow`。 如果使用者需要同意更多作用域，代碼將處理`MsalInteractionRequiredException`物件以挑戰使用者。

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

在 Python 示例中，調用 Microsoft 圖形的代碼位於[app.py_L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62)中。

代碼嘗試從權杖緩存獲取權杖。 然後，在設置授權標頭後，它調用 Web API。 如果無法獲取權杖，則再次登錄使用者。

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API](scenario-web-app-call-api-call-api.md)
