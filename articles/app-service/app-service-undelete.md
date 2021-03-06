---
title: 還原已刪除的應用
description: 瞭解如何在 Azure 應用服務中還原已刪除的應用。 避免意外刪除的應用程式令人頭痛。
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: c7d778a0afca4b3552976526d58a2cb2efe12161
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75689612"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>使用 PowerShell 還原已刪除的 App Service 應用程式

如果在 Azure 應用服務中意外刪除應用，則可以使用[Az PowerShell 模組](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)中的命令還原應用。

> [!NOTE]
> 已刪除的應用在初始刪除後 30 天內從系統中清除。 清除應用後，無法恢復該應用。
>

## <a name="re-register-app-service-resource-provider"></a>重新註冊應用服務資來源提供者
某些客戶可能會遇到檢索已刪除應用清單失敗的問題。 要解決此問題，運行以下命令：

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>列出已刪除的應用

要獲取已刪除應用的集合，可以使用`Get-AzDeletedWebApp`。

有關特定已刪除應用的詳細資訊，可以使用：

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

詳細資訊包括：

- **已刪除的SiteId**：應用的唯一識別碼，用於刪除多個同名應用的方案
- **訂閱 ID**：包含已刪除資源的訂閱
- **位置**： 原始應用程式的位置
- **資源組名稱**：原始資源組的名稱
- **名稱**：原始應用的名稱。
- **插槽**：插槽的名稱。
- **刪除時間**：何時刪除應用  

## <a name="restore-deleted-app"></a>恢復已刪除的應用

標識要還原的應用後，可以使用 還原它`Restore-AzDeletedWebApp`。

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

命令的輸入是：

- **資源組**：將還原應用的目標資源組
- **名稱**：應用的名稱，應全域唯一。
- **目標應用服務方案名稱**：連結到應用的應用服務方案

預設情況下`Restore-AzDeletedWebApp`，將同時還原應用配置和內容。 如果只想還原內容，請使用此命令的`-RestoreContentOnly`標記。

> [!NOTE]
> 如果應用託管在應用服務環境上，然後從應用服務環境中刪除，則只有在相應的應用服務環境仍然存在時才能還原該應用。
>

您可以在此處找到完整的命令引用：[還原-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp)。
