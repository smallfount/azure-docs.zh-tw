---
title: 從 Web 應用程式調用 Web api - 微軟身份平臺 |蔚藍
description: 瞭解如何構建調用 Web API 的 Web 應用（調用受保護的 Web API）
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
ms.openlocfilehash: 28b4be46dc686c6e1b55f1ab36e0607057ebdbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758966"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>調用 Web API 的 Web 應用：調用 Web API

現在您有了權杖，就可以調用受保護的 Web API。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

下面是 的簡化代碼。 `HomeController` 此代碼獲取一個權杖，稱為 Microsoft 圖形。 已添加代碼以演示如何將 Microsoft 圖形稱為 REST API。 Microsoft 圖形 API 的 URL 在 appsettings.json 檔中提供，並在名為`webOptions`的變數中讀取：

```JSon
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```csharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account.
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users, because the guest ID / tenant ID are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;

 // Calls the web API (Microsoft Graph in this case).
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> 您可以使用同一原則調用任何 Web API。
>
> 大多數 Azure Web API 都提供了一個 SDK，該 SDK 簡化了調用 API 的 SDK。 微軟圖形也是如此。 在下一篇文章中，您將瞭解在哪裡可以找到說明 API 使用的教程。

# <a name="java"></a>[JAVA](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
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

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移動至生產環境](scenario-web-app-call-api-production.md)
