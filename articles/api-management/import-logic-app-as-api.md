---
title: 使用 Azure 入口網站匯入 Logic App 作為 API | Microsoft Docs
description: 本教學課程示範如何使用 API 管理 (APIM) 來匯入 Logic App 作為 API。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: apimpm
ms.openlocfilehash: 4077187fe04e3be914a6f7fba84c03df1b79d06a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74108404"
---
# <a name="import-a-logic-app-as-an-api"></a>匯入 Logic App 作為 API

本文會示範如何匯入 Logic App 作為 API，以及測試匯入的 API。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> -   匯入 Logic App 作為 API
> -   在 Azure 入口網站中測試 API
> -   在開發人員入口網站中測試 API

## <a name="prerequisites"></a>Prerequisites

-   完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)
-   確定您的訂用帳戶中有一個邏輯應用程式會公開 HTTP 端點。 如需詳細資訊，請參閱[透過 HTTP 端點觸發工作流程](../logic-apps/logic-apps-http-endpoint.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>匯入和發佈後端 API

1. 選取 [API 管理]  下方的 [API]  。
2. 從 [加入新的 API]  清單選取 [Logic App]  。

    ![邏輯應用程式](./media/import-logic-app-as-api/logic-app-api.png)

3. 按 [瀏覽]  以查看您訂用帳戶中包含 HTTP 觸發程式的 Logic Apps 清單。 (請注意，未包含 HTTP 觸發程式的 Logic Apps 不會出現在清單中。)
4. 選取應用程式。 APIM 會尋找與所選取應用程式相關聯的 Swagger，擷取並匯入它。
5. 新增 API URL 尾碼。 此尾碼可用來在此 API 管理執行個體中識別這個 API 的名稱。 該尾碼在此 APIM 執行個體中必須是唯一的。
6. 透過將 API 關聯至某個產品來發佈 API。 在本案例中，我們使用「無限制」  的產品。 如果您想要發佈 API 以供開發人員使用，請將它新增至產品。 您可以在 API 建立期間執行此動作，或稍後設定它。

    產品是一或多個 API 的關聯。 您可以包括數個 API，並透過開發人員入口網站將它們提供給開發人員。 開發人員必須先訂閱產品，才能取得 API 的存取權。 當他們訂閱時，就能取得適用於該產品中任何 API 的中訂用帳戶金鑰。 如果您建立了 APIM 執行個體，您就已經是系統管理員，因此根據預設，您已訂閱每一個產品。

    依預設，每個 API 管理執行個體會隨附兩個範例產品：

    - **入門**
    - **無限制**

7. 選取 [建立]  。

## <a name="test-the-api-in-the-azure-portal"></a>在 Azure 入口網站中測試 API

您可以從 Azure 入口網站直接呼叫作業，以便檢視和測試 API 的作業。

1. 選取您在上一個步驟中建立的 API。
2. 按 [測試]  索引標籤。
3. 選取某個作業。

    頁面會顯示查詢參數的欄位和標頭的欄位。 其中一個標頭是 "Ocp-Apim-Subscription-Key"，它適用於與此 API 相關聯之產品的訂用帳戶金鑰。 如果您建立了 API 管理執行個體，您就已經是系統管理員，因此會自動填入此金鑰。

4. 按 [傳送]  。

    後端會回應 **200 確定**與部分資料。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>每個 Logic App 都有 **manual-invoke** 作業。 如果您想讓 API 包含多個邏輯應用程式，為避免發生衝突，您必須重新命名函式。

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>
> [轉換及保護已發佈的 API](transform-api.md)
