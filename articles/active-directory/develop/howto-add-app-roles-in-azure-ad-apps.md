---
title: 添加應用角色並從權杖中獲取它們 |蔚藍
titleSuffix: Microsoft identity platform
description: 了解如何在 Azure Active Directory 註冊的應用程式中新增應用程式角色、將使用者和群組指派給這些角色，以及在權杖的 `roles` 中宣告接收它們。
services: active-directory
documentationcenter: ''
author: kkrishna
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3a911db36fd03ebcb5e0fc53d4d7f36d68648249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399083"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>如何：在您的應用程式中新增應用程式角色，並且在權杖中接收這些角色

角色型存取控制 (RBAC) 是一個熱門的機制，可在應用程式中強制授權。 使用 RBAC 時，系統管理員會將權限授與給角色，而非授與個別使用者或群組。 系統管理員可以接著將角色指派給不同的使用者和群組，控制誰可以存取哪些內容和功能。

使用 RBAC 搭配應用程式角色和角色宣告，開發人員可以輕易在其應用程式中安全地強制授權。

另一種方法是使用 Azure AD 群組和群組宣告，如 [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) 所示。 Azure AD 群組和應用程式角色完全不會互斥；它們可以串聯地使用，以提供更細微的存取控制。

## <a name="declare-roles-for-an-application"></a>宣告應用程式的角色

這些應用程式角色會定義於 [Azure 入口網站](https://portal.azure.com)中應用程式的註冊資訊清單中。  當使用者登入應用程式時，Azure AD 會針對已個別授與給使用者和自其群組成員資格授與的每個角色發出 `roles` 宣告。  透過入口網站的 UI，或以程式設計方式使用 [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview)，即可將使用者和群組指派給角色。

### <a name="declare-app-roles-using-azure-portal"></a>使用 Azure 入口網站宣告應用程式角色

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在門戶工具列中選擇 **"目錄 + 訂閱**"圖示。
1. 在 **"我的最愛**"或 **"所有目錄"** 清單中，選擇要註冊應用程式的"活動目錄"租戶。
1. 在 Azure 入口網站中，搜尋並選取 [Azure Active Directory]****。
1. 在 [Azure Active Directory]**** 窗格中，選取 [應用程式註冊]**** 以檢視所有應用程式的清單。
1. 選取您要在其中定義應用程式角色的應用程式。 然後選擇 **"清單**"。
1. 找出 `appRoles` 設定並新增所有的應用程式角色，以編輯應用程式資訊清單。

     > [!NOTE]
     > 此清單中的每個應用角色定義都必須在`id`屬性的清單上下文中具有不同的有效 GUID。
     >
     > 每個`value`應用角色定義的屬性應與應用程式中的代碼中使用的字串完全符合。 屬性`value`不能包含空格。 如果是，則在保存清單時將收到錯誤。

1. 儲存資訊清單。

### <a name="examples"></a>範例

下列範例顯示您可以指派給 `users` 的 `appRoles`。

> [!NOTE]
>`id` 必須是唯一的 GUID。

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>`displayName`不能包含空格。

您可以將應用程式角色定義成以 `users`、`applications` 或兩者為目標。 可供 `applications` 使用時，應用程式角色就會在 [必要權限]**** 刀鋒視窗中顯示為應用程式權限。 下列範例顯示以 `Application` 為目標的應用程式角色。

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

定義的角色數會影響應用程式清單的限制。 已在[清單限制](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits)頁上詳細討論了這些問題。

### <a name="assign-users-and-groups-to-roles"></a>將使用者和群組指派給角色

在應用程式中新增應用程式角色之後，您即可將使用者和群組指派給這些角色。

1. 在**Azure 活動目錄**窗格中，從**Azure 活動目錄**左側導航功能表中選擇**企業應用程式**。
1. 選擇 **"所有應用程式**"以查看所有應用程式的清單。

     如果看不到您希望在此顯示的應用程式，請使用 [所有應用程式]**** 清單頂端的各種篩選條件來限制清單，或向下捲動清單以找出應用程式。

1. 選取您想要在其中將使用者或安全性群組指派給角色的應用程式。
1. 在應用程式的左側導航功能表中選擇 **"使用者和組**"窗格。
1. 在 [使用者和群組]**** 清單頂端，選取 [新增使用者]**** 按鈕，以開啟 [新增指派]**** 窗格。
1. 從"**添加分配**"窗格中選擇 **"使用者"和"組**"選擇器。

     隨即會顯示使用者和安全性群組清單，以及用來搜尋並找出特定使用者或群組的文字方塊。 此畫面可讓您一次選取多個使用者和群組。

1. 在您選好使用者和群組後，請按底部的 [選取]**** 按鈕，以移至下一個部分。
1. 選擇 [新增指派]**** 窗格中的 [選取角色]**** 選取器。 將會顯示先前在應用程式資訊清單中宣告的所有角色。
1. 選擇角色，然後按 [選取]**** 按鈕。
1. 按底部的 [指派]**** 按鈕，即可將使用者和群組指派給應用程式。
1. 確認您新增的使用者和群組有顯示在更新後的 [使用者和群組]**** 清單中。

## <a name="more-information"></a>詳細資訊

- [使用應用角色&角色聲明向 ASP.NET核心 Web 應用添加授權](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [在您的應用程式中使用安全性群組和應用程式角色 (影片)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory 現在具有群組宣告和應用程式角色](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory 應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [AAD 存取權杖](access-tokens.md)
- [AAD`id_tokens`](id-tokens.md)
