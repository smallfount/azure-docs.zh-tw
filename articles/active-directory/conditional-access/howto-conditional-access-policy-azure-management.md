---
title: 條件訪問 - 需要 MFA 進行 Azure 管理 - Azure 活動目錄
description: 創建自訂條件訪問策略，以需要 Azure 管理工作的多重要素驗證
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c90566006868c817d977699c35f2213895f3fe70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295232"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>條件訪問：需要 MFA 進行 Azure 管理

組織使用各種 Azure 服務，並從基於 Azure 資源管理器的工具管理這些服務，例如：

* Azure 入口網站
* Azure PowerShell
* Azure CLI

這些工具可以提供對資源的極特權訪問，這些資源可以更改訂閱範圍配置、服務設置和訂閱計費。 為了保護這些特權資源，Microsoft 建議對訪問這些資源的任何使用者進行多重要素驗證。

## <a name="user-exclusions"></a>使用者排除

條件訪問策略是功能強大的工具，我們建議將以下帳戶排除在策略之外：

* **緊急訪問**或**破玻璃**帳戶，以防止租戶範圍的帳戶鎖定。 在不太可能的情況下，所有管理員都鎖定在租戶之外，您的緊急訪問管理帳戶可用於登錄到租戶，採取措施恢復存取權限。
   * 有關詳細資訊，請參閱"[在 Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)"一文。
* **服務帳戶****和服務原則**，如 Azure AD 連接同步帳戶。 服務帳戶是非與任何特定使用者關聯的非互動式帳戶。 它們通常由後端服務使用，並允許以程式設計方式訪問應用程式。 應排除服務帳戶，因為無法以程式設計方式完成 MFA。
   * 如果您的組織在指令碼或程式碼中使用這些帳戶，請考慮將它們取代為[受管理的身分識別](../managed-identities-azure-resources/overview.md)。 作為臨時解決方法，可以從基線策略中排除這些特定帳戶。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

以下步驟將説明創建條件訪問策略，以要求分配的管理角色執行多重要素驗證。

1. 以全域管理員、安全管理員或條件訪問管理員的身份登錄到**Azure 門戶**。
1. 流覽到**Azure 活動目錄** > **安全** > **條件訪問**。
1. 選取 [新增原則]****。
1. 為您的策略指定一個名稱。 我們建議組織為其策略的名稱創建有意義的標準。
1. 在 **"分配"** 下，選擇 **"使用者"和"組**"
   1. 在 **"包括**"下，選擇**所有使用者**。
   1. 在 **"排除"** 下，選擇 **"使用者"和"組"，** 然後選擇組織的緊急訪問或碎玻璃帳戶。 
   1. 選擇 **"完成**"。
1. 在 **"雲應用"或"操作** > **包括"下**，**選擇"選擇應用**"，選擇**Microsoft Azure 管理**，然後**選擇"****選擇完成**"。
1. 在**條件** > **用戶端應用（預覽）** 下，將 **"配置配置為** **"，** 然後選擇 **"完成**"。
1. 在 **"存取控制** > **授予"下**，選擇 **"授予訪問**"，"**需要多重要素驗證**"，然後選擇 **"選擇**"。
1. 確認設置並將 **"啟用策略"** 設置為 **"打開**"。
1. 選擇 **"創建**"以啟用策略。

## <a name="next-steps"></a>後續步驟

[條件訪問通用策略](concept-conditional-access-policy-common.md)

[使用僅條件訪問報告模式確定影響](howto-conditional-access-report-only.md)

[使用條件訪問"如果"工具類比登錄行為](troubleshoot-conditional-access-what-if.md)
