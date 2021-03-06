---
title: 故障排除組合註冊 - Azure 活動目錄
description: 故障排除 Azure AD 多重要素驗證和自助服務密碼重設組合註冊（預覽）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab7c38d23cb1f05e07488810640aeb791ded3d4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847383"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>故障排除組合安全資訊註冊（預覽）

本文中的資訊旨在指導正在排除使用者報告的合併註冊體驗使用者報告的問題的管理員。

|     |
| --- |
| Azure 多重要素驗證和 Azure 活動目錄 （Azure AD） 自助服務密碼重設的組合安全資訊註冊是 Azure AD 的公共預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

## <a name="audit-logs"></a>稽核記錄

為合併註冊而記錄的事件位於 Azure AD 稽核記錄中的"身份驗證方法"類別中。

![Azure AD 稽核記錄介面，顯示註冊事件](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

下表列出了合併註冊生成的所有審核事件：

| 活動 | 狀態 | 原因 | 描述 |
| --- | --- | --- | --- |
| 使用者註冊了所有必需的安全資訊 | Success | 使用者註冊了所有必需的安全資訊。 | 當使用者成功完成註冊時，將發生此事件。|
| 使用者註冊了所有必需的安全資訊 | 失敗 | 使用者已取消安全資訊註冊。 | 當使用者取消中斷模式的註冊時，將發生此事件。|
| 使用者註冊的安全資訊 | Success | 使用者註冊*方法*. | 當使用者註冊單個方法時，將發生此事件。 *方法*可以是身份驗證器應用、電話、電子郵件、安全問題、應用密碼、備用電話等。| 
| 使用者查看的安全資訊 | Success | 使用者成功查看了安全資訊。 | 當使用者在安全資訊審核頁上選擇 **"看起來不錯"** 時，將發生此事件。|
| 使用者查看的安全資訊 | 失敗 | 使用者無法查看安全資訊。 | 當使用者在安全資訊審核頁上選擇 **"看起來不錯"，** 但後端出現問題時，將發生此事件。|
| 使用者刪除的安全資訊 | Success | 使用者刪除*的方法*。 | 當使用者刪除單個方法時，將發生此事件。 *方法*可以是身份驗證器應用、電話、電子郵件、安全問題、應用密碼、備用電話等。|
| 使用者刪除的安全資訊 | 失敗 | 使用者未能刪除*方法*。 | 當使用者嘗試刪除方法但嘗試由於某種原因失敗時，將發生此事件。 *方法*可以是身份驗證器應用、電話、電子郵件、安全問題、應用密碼、備用電話等。|
| 使用者更改的預設安全資訊 | Success | 使用者更改了*方法*的預設安全資訊。 | 當使用者更改預設方法時，將發生此事件。 *方法*可以是身份驗證器應用通知、來自驗證器應用或權杖的代碼、調用 #X XXXXXXXXXX、將代碼文本到 #X XXXXXXXXX 等。|
| 使用者更改的預設安全資訊 | 失敗 | 使用者未能更改*方法*的預設安全資訊。 | 當使用者嘗試更改預設方法，但嘗試由於某種原因失敗時，將發生此事件。 *方法*可以是身份驗證器應用通知、來自驗證器應用或權杖的代碼、調用 #X XXXXXXXXXX、將代碼文本到 #X XXXXXXXXX 等。|

## <a name="troubleshooting-interrupt-mode"></a>故障排除中斷模式

| 徵狀 | 疑難排解步驟 |
| --- | --- |
| 我沒有看到我期望看到的方法。 | 1. 檢查使用者是否具有 Azure AD 管理員角色。 如果是，請查看 SSPR 管理原則差異。 <br> 2. 確定使用者是否因為多重要素驗證註冊實施或 SSPR 註冊強制而中斷。 請參閱"合併註冊模式"下的[流程圖](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes)，以確定應顯示哪些方法。 <br> 3. 確定多因素身份驗證或 SSPR 策略更改最近的方式。 如果更改是最近更改，則更新的策略可能需要一些時間才能傳播。|

## <a name="troubleshooting-manage-mode"></a>故障排除管理模式

| 徵狀 | 疑難排解步驟 |
| --- | --- |
| 我沒有添加特定方法的選項。 | 1. 確定該方法是啟用的，用於多重要素驗證還是 SSPR。 <br> 2. 如果方法已啟用，請再次保存策略，然後等待 1-2 小時再進行測試。 <br> 3. 如果啟用了該方法，請確保使用者尚未設置允許其設置的最大方法數。|

## <a name="disable-combined-registration"></a>禁用合併註冊

當使用者在新的組合體驗中註冊電話號碼和/或移動應用時，我們的服務會為該使用者上這些方法標記一組標誌（增強式驗證方法）。 此功能允許使用者在需要多重要素驗證時使用這些方法執行多重要素驗證。

如果管理員啟用預覽，使用者通過新體驗註冊，然後管理員禁用預覽，則使用者也可能在不知不覺中註冊為多重要素驗證。

如果已完成合併註冊的使用者轉到 當前自助服務密碼重設 （SSPR） 註冊頁，[https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)系統將提示使用者執行多重要素驗證，然後才能訪問該頁面。 從技術角度來看，此步驟是預期的，但對於以前只為 SSPR 註冊的使用者來說，這是新的。 儘管此額外步驟通過提供另一級別的安全性來改善使用者的安全狀態，但管理員可能希望回滾其使用者，以便他們不再能夠執行多重要素驗證。  

### <a name="how-to-roll-back-users"></a>如何復原使用者

如果您作為管理員想要重置使用者的多重要素驗證設置，則可以使用下一節中提供的 PowerShell 腳本。 該腳本將清除使用者的移動應用和/或電話號碼的"增強式驗證方法"屬性。 如果為使用者運行此腳本，則如果需要，則需要重新註冊多重要素驗證。 我們建議在回滾所有受影響的使用者之前，使用一個或兩個使用者測試回滾。

以下步驟將説明您回滾使用者或使用者組。

#### <a name="prerequisites"></a>Prerequisites

1. 安裝相應的 Azure AD PowerShell 模組。 在 PowerShell 視窗中，執行下列命令來安裝模組：

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 將受影響的使用者物件識別碼 清單作為文字檔保存到您的電腦，每行包含一個 ID。 記下檔案的所在位置。
1. 將以下腳本保存到電腦並記下該腳本的位置：

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>復原

在 PowerShell 視窗中，運行以下命令，提供腳本和使用者檔位置。 出現提示時，輸入全域管理員認證。 該指令碼將輸出每個使用者更新作業的結果。

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>禁用預覽體驗

要禁用使用者的預覽體驗，請完成以下步驟：

1. 以使用者管理員身份登錄到 Azure 門戶。
2. 轉到**Azure 活動目錄** > **使用者設置** > **管理訪問面板預覽功能的設置**。
3. **在"使用者"下可以使用預覽功能註冊和管理安全資訊**，將選擇器設置為 **"無**"，然後選擇"**保存**"。

將不再提示使用者使用預覽體驗進行註冊。

## <a name="next-steps"></a>後續步驟

* [瞭解有關自助服務密碼重設和 Azure 多重要素驗證組合註冊的公共預覽](concept-registration-mfa-sspr-combined.md)
