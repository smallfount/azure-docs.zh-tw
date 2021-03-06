---
title: Azure Migrate 專案疑難排解
description: 説明您解決創建和管理 Azure 遷移專案的問題。
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: 3b8c2f6ec33965317d2aaa23a36b6becff11a54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75725724"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Azure Migrate 專案疑難排解

本文可説明您在創建和管理[Azure 遷移](migrate-services-overview.md)專案時解決問題。

## <a name="how-to-add-new-project"></a>如何添加新專案？

訂閱中可以有多個 Azure 遷移專案。 [瞭解如何](how-to-add-tool-first-time.md)首次創建專案或[添加其他](create-manage-projects.md#create-additional-projects)專案。

## <a name="what-azure-permissions-are-needed"></a>需要哪些 Azure 許可權？

您需要訂閱中的參與者或擁有者許可權才能創建 Azure 遷移專案。

## <a name="cant-find-a-project"></a>找不到專案

查找現有的 Azure 遷移專案取決於您使用的是當前版本還是舊版本的 Azure 遷移。 [按照](create-manage-projects.md#find-a-project)。


## <a name="cant-find-a-geography"></a>找不到地理

可以在[受支援的地理位置](migrate-support-matrix.md#supported-geographies)中創建 Azure 遷移專案。 請注意，專案地理區用於存儲已發現的電腦中繼資料。 您也可以評估或遷移其他位置的電腦。

## <a name="what-are-vm-limits"></a>什麼是 VM 限制？

您可以在單個專案中評估多達 35，000 個 VMware VM 或多達 35，000 台 Hyper-V VM。 專案可以同時包括 VMware VM 和超 V VM，但達到評估限制。

## <a name="can-i-upgrade-old-project"></a>我可以升級舊專案嗎？

無法更新以前版本的 Azure 遷移的專案。 您需要[創建一個新專案](how-to-add-tool-first-time.md)，並添加工具。

## <a name="cant-create-a-project"></a>無法創建專案

如果嘗試創建專案並遇到部署錯誤：

- 請嘗試再次創建專案，以防出現暫時性錯誤。 在 **"部署"中**，按一下 **"重新部署**"以重試。
- 檢查訂閱中具有參與者或擁有者許可權。
- 如果要在新添加的地理位置中部署，請稍等片刻，然後重試。
- 如果收到錯誤，"請求必須包含使用者標識標頭"，這可能表示您無法訪問組織的 Azure 活動目錄 （Azure AD） 租戶。 在此案例中：
    - 首次添加到 Azure AD 租戶時，會收到加入租戶的電子郵件邀請。
    - 接受要添加到租戶的邀請。
    - 如果您看不到電子郵件，請與有權訪問租戶的使用者聯繫，並要求他們[重新向您發送邀請](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)。
    - 收到邀請電子郵件後，打開它並選擇接受邀請的連結。 然後，登出 Azure 門戶，然後重新登錄。 （刷新瀏覽器不起作用。然後，您可以開始創建遷移專案。

## <a name="how-do-i-delete-a-project"></a>如何刪除專案

[按照這些說明](create-manage-projects.md#delete-a-project)刪除專案。 請注意，刪除專案時，將刪除專案以及有關專案中發現的電腦的中繼資料。

## <a name="added-tools-dont-show"></a>添加的工具不顯示

請確保您選擇了正確的專案。 在 Azure 遷移中心>**伺服器**或**資料庫中**，按一下螢幕右上角**的"遷移專案（更改）** 旁邊**更改**"。 選擇正確的訂閱和專案名稱>**確定**。 頁面應使用所選項目的附加工具刷新。

## <a name="next-steps"></a>後續步驟

向 Azure 遷移專案添加[評估](how-to-assess.md)或[遷移](how-to-migrate.md)工具。