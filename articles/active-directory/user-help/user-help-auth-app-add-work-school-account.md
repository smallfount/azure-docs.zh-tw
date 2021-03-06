---
title: 將工作或學校帳戶添加到 Microsoft 身份驗證器應用 - Azure AD
description: 將您的工作或學校帳戶添加到 Microsoft 身份驗證器應用，以驗證您的身份，同時使用雙重驗證。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: f0cc14a53f7ead7f0a496728d477d7d30857a0fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063912"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>將您的工作或學校帳戶添加到 Microsoft 身份驗證器應用

如果您的組織使用雙因素驗證，您可以設定公司或學校帳戶，使其使用 Microsoft Authenticator 應用程式作為其中一種驗證方法。

>[!Important]
>您必須先下載並安裝 Microsoft Authenticator 應用程式，才可新增帳戶。 如果尚未這麼做，請依照[下載並安裝應用程式](user-help-auth-app-download-install.md)文章中的步驟。

## <a name="add-your-work-or-school-account"></a>新增公司或學校帳戶

1. 在您的電腦上，移至[其他安全性驗證](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)頁面。

    >[!Note]
    >如果您未看到 [其他安全性驗證]**** 頁面，有可能是您的系統管理員已開啟安全性資訊 (預覽) 體驗。 若是如此，您即應依照[將安全性資訊設定為使用驗證器應用程式](security-info-setup-auth-app.md)一節中的指示操作。 若非如此，您必須連絡組織的技術支援人員以尋求協助。 有關安全資訊的詳細資訊，請參閱[安全資訊（預覽）概述](user-help-security-info-overview.md)。

2. 核取 [驗證器應用程式]**** 旁的方塊，然後選取 [設定]****。

    [設定行動應用程式]**** 頁面隨即出現。

    ![提供 QR 代碼的畫面](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. 開啟 Microsoft Authenticator 應用程式、從右上角的 [自訂和控制]**** 圖示中選取 [新增帳戶]****，然後選取 [公司或學校帳戶]****。

    >[!Note]
    >如果這是您第一次設定 Microsoft Authenticator 應用程式，系統可能會顯示提示詢問您是否允許應用程式存取您的相機 (iOS)，或是否允許應用程式拍攝相片和錄製影片 (Android)。 您必須選取 [允許]****，讓驗證器應用程式能夠存取您的相機，以在下一個步驟中拍攝 QR 代碼的相片。 如果您不允許存取相機，您仍可設定驗證器應用程式，但必須手動新增代碼資訊。 有關如何手動添加代碼的資訊，請參閱[手動向應用添加帳戶](user-help-auth-app-add-account-manual.md)。

4. 使用裝置相機掃描電腦的 [設定行動應用程式]**** 畫面中顯示的 QR 代碼，然後選擇 [完成]****。

    >[!Note]
    >如果您的相機無法擷取 QR 代碼，您可以將您的帳戶資訊手動新增至 Microsoft Authenticator 應用程式，以進行雙因素驗證。 如需詳細資訊及其操作方式，請參閱[手動新增帳戶](user-help-auth-app-add-account-manual.md)。

5. 在您的裝置上檢閱應用程式的 [帳戶]**** 畫面，以確定您的帳戶正確無誤，並且有相關聯的六位數驗證碼。 為了提高安全性，驗證碼每隔 30 秒就會變更，避免多次使用同一個驗證碼。

    ![帳戶畫面](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>後續步驟

- 將您的帳戶新增至應用程式之後，您即可在裝置上使用 Authenticator 應用程式登入。 如需詳細資訊，請參閱[使用應用程式登入](user-help-auth-app-sign-in.md)。

- 對於執行 iOS 的裝置，您也可以將您的帳戶認證和相關應用程式設定 (例如您的帳戶順序) 備份到雲端。 如需更多資訊，請參閱[使用 Microsoft Authenticator 應用程式備份和復原](user-help-auth-app-backup-recovery.md)。
