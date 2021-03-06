---
title: 跨訂閱、資源組或區域遷移邏輯應用
description: 將邏輯應用或集成帳戶遷移到其他 Azure 訂閱、資源組或位置（區域）
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: f5944accb185f1311c811cf65a8ea8348fd569db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605602"
---
# <a name="move-logic-app-resources-to-other-azure-subscriptions-resource-groups-or-regions"></a>將邏輯應用資源移動到其他 Azure 訂閱、資源組或區域

要將邏輯應用或相關資源遷移到其他 Azure 訂閱、資源組或區域，可以多種方式完成這些任務，例如 Azure 門戶、Azure PowerShell、Azure CLI 和 REST API。 在移動資源之前，請查看以下注意事項： 

* 只能在 Azure 資源組或訂閱之間移動[特定的邏輯應用資源類型](../azure-resource-manager/management/move-support-resources.md#microsoftlogic)。

* 檢查 Azure 訂閱中和每個 Azure 區域中可以具有的邏輯應用資源數量[的限制](../logic-apps/logic-apps-limits-and-config.md)。 當區域在訂閱或資源組之間保持不變時，這些限制會影響是否可以移動特定資源類型。 例如，每個 Azure 訂閱中的每個 Azure 區域只能有一個免費層集成帳戶。

* 在訂閱、資源組或區域之間遷移邏輯應用後，必須重新創建或重新授權任何需要開放身份驗證 （OAuth） 的連接。

* 每當移動資源時，Azure 都會創建新的資源標識。 因此，請確保改用新 I 並更新與移動資源關聯的任何腳本或工具。

## <a name="prerequisites"></a>Prerequisites

* 用於創建要移動的邏輯應用或集成帳戶的相同 Azure 訂閱

* 資源擁有者移動和設置所需資源的許可權。 詳細瞭解[基於角色的存取控制 （RBAC）](../role-based-access-control/built-in-roles.md#owner)。

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>在訂用帳戶之間移動資源

要將資源（如邏輯應用或集成帳戶）移動到其他 Azure 訂閱，可以使用 Azure 門戶、Azure PowerShell、Azure CLI 或 REST API。 這些步驟涵蓋 Azure 門戶，當資源的區域保持不變時，可以使用該門戶。 有關其他步驟和常規準備工作，請參閱[將資源移動到新資源組或訂閱](../azure-resource-manager/management/move-resource-group-and-subscription.md)。

1. 在[Azure 門戶](https://portal.azure.com)中，查找並選擇要移動的邏輯應用資源。

1. 在資源的 **"概述"** 頁上，**在訂閱**旁邊，選擇**更改**連結。

1. 在 **"移動資源"** 頁上，選擇要移動的邏輯應用資源和任何相關資源。

1. 從 **"訂閱"** 清單中，選擇目標訂閱。

1. 從 **"資源組"** 清單中，選擇目標資源組。 或者，要創建其他資源組，請選擇"**創建新組**"。

1. 要確認您的理解，即與移動資源關聯的任何腳本或工具在使用新資源指示更新它們之前都不起作用，請選擇確認框，然後選擇"**確定**"。

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>在資源組之間移動資源

要將資源（如邏輯應用或集成帳戶）移動到其他 Azure 資源組，可以使用 Azure 門戶、Azure PowerShell、Azure CLI 或 REST API。 這些步驟涵蓋 Azure 門戶，當資源的區域保持不變時，可以使用該門戶。 有關其他步驟和常規準備工作，請參閱[將資源移動到新資源組或訂閱](../azure-resource-manager/management/move-resource-group-and-subscription.md)。

在實際在組之間移動資源之前，可以測試是否可以成功地將資源移動到其他組。 有關詳細資訊，請參閱[驗證您的移動](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move)。

1. 在[Azure 門戶](https://portal.azure.com)中，查找並選擇要移動的邏輯應用資源。

1. 在資源的 **"概述"** 頁上，**在資源組**旁邊，選擇**更改**連結。

1. 在 **"移動資源"** 頁上，選擇要移動的邏輯應用資源和任何相關資源。

1. 從 **"資源組"** 清單中，選擇目標資源組。 或者，要創建其他資源組，請選擇"**創建新組**"。

1. 要確認您的理解，即與移動資源關聯的任何腳本或工具在使用新資源指示更新它們之前都不起作用，請選擇確認框，然後選擇"**確定**"。

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>在區域之間移動資源

當您希望將邏輯應用移動到其他區域時，您的選項取決於創建邏輯應用的方式。 根據您選擇的選項，必須重新創建或重新授權邏輯應用中的連接。

* 在 Azure 門戶中，在新區域中重新創建邏輯應用並重新配置工作流設置。 為了節省時間，可以將基礎工作流定義和連接從源應用複製到目標應用。 要查看邏輯應用後面的"代碼"，請在邏輯應用設計器工具列上選擇 **"代碼"視圖**。

* 通過使用視覺化工作室和 Visual Studio 的 Azure 邏輯應用工具，可以從 Azure 門戶作為[Azure 資源管理器範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)[打開和下載邏輯應用](../logic-apps/manage-logic-apps-with-visual-studio.md)。 此範本大多可供部署，包括邏輯應用的資源定義，包括工作流本身和連接。 範本還聲明部署時要使用的值的參數。 這樣，您可以更輕鬆地根據需要更改部署邏輯應用的位置和方式。 要指定部署的位置和其他必要資訊，可以使用單獨的參數檔。

* 如果使用連續集成 （CI） 和連續傳遞 （CD） 工具（如 Azure DevOps 中的 Azure 管道）創建和部署邏輯應用，則可以使用這些工具將應用部署到其他區域。

有關邏輯應用的部署範本的詳細資訊，請參閱以下主題：

* [概述：使用 Azure 資源管理器範本自動部署 Azure 邏輯應用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [查找、打開邏輯應用並將邏輯應用從 Azure 門戶下載到視覺化工作室](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [為 Azure 邏輯應用創建 Azure 資源管理器範本](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [為 Azure 邏輯應用部署 Azure 資源管理器範本](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>相關資源

某些 Azure 資源（如 Azure 中的本地資料閘道資源）可以存在於與使用這些資源的邏輯應用不同的區域中。 但是，其他 Azure 資源（如連結集成帳戶）必須與邏輯應用位於同一區域中。 根據您的方案，請確保您的邏輯應用可以訪問應用期望存在於同一區域的資源。

例如，要將邏輯應用連結到集成帳戶，兩個資源必須存在於同一區域中。 在災害復原等方案中，通常需要具有相同配置和工件的集成帳戶。 在其他方案中，您可能需要具有不同配置和工件的集成帳戶。

Azure 邏輯應用中的自訂連接器對具有相同 Azure 訂閱和相同 Azure 活動目錄租戶的連接器作者和使用者可見。 這些連接器在部署邏輯應用的同一區域可用。 如需詳細資訊，請參閱[在貴組織中共用自訂連接器](https://docs.microsoft.com/connectors/custom-connectors/share)。

從 Visual Studio 獲取的範本僅包括邏輯應用及其連接的資源定義。 因此，如果邏輯應用使用其他資源（例如，集成帳戶和 B2B 專案（如合作夥伴、協定和架構），則必須使用 Azure 門戶匯出該集成帳戶的範本。 此範本包括集成帳戶和工件的資源定義。 但是，範本尚未完全參數化。 因此，必須手動參數化要用於部署的值。

### <a name="export-templates-for-integration-accounts"></a>集成帳戶的匯出範本

1. 在[Azure 門戶](https://portal.azure.com)中，查找並打開集成帳戶。

1. 在集成帳戶的功能表上，**在"設置"** 下，選擇 **"匯出範本**"。

1. 在工具列上，選擇 **"下載**"並保存範本。

1. 打開並編輯範本以參數化部署所需的值。

## <a name="next-steps"></a>後續步驟

[將 Azure 資源移動到新資源組或訂閱](../azure-resource-manager/management/move-resource-group-and-subscription.md)
