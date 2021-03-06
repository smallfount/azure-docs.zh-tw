---
title: 從 Visual Studio 將 Web 應用程式發行到 Azure VM
description: 從 Visual Studio 將 ASP.NET Web 應用程式發行到 Azure 虛擬機器
services: virtual-machines-windows
author: ghogen
manager: jillfra
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 6e5db9b4e46019aa386057d51d956ff11d90f498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71970869"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>從 Visual Studio 將 ASP.NET Web 應用程式發行到 Azure VM

本文檔介紹如何使用 Visual Studio 2019 中的**Microsoft Azure 虛擬機器**發佈功能將ASP.NET Web 應用程式發佈到 Azure 虛擬機器 （VM）。  

## <a name="prerequisites"></a>Prerequisites
若要使用 Visual Studio 將 ASP.NET 專案發行到 Azure VM，必須正確設定 VM。

- 機器必須設定為執行 ASP.NET web 應用程式，並安裝 WebDeploy。

- VM 必須設定 DNS 名稱。 如需詳細資訊，請參閱[在 Azure 入口網站中為 Windows VM 建立完整網域名稱](portal-create-fqdn.md)。

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>使用 Visual Studio 將您的 ASP.NET Web 應用程式發行到 Azure VM
下列章節說明如何將現有的 ASP.NET Web 應用程式發行到 Azure 虛擬機器。

1. 在 Visual Studio 2019 中打開 Web 應用解決方案。
2. 在方案總管中以滑鼠右鍵按一下專案，並選擇 [發行...]****
3. 使用頁面右邊的箭頭捲動發行選項，直到您找到 [Microsoft Azure 虛擬機器]**** 為止。  

   ![發行頁面 - 發行按鈕]

4. 選取 [Microsoft Azure 虛擬機器]**** 圖示，然後選擇 [發行]****。

   ![發行頁面 - Microsoft Azure 虛擬機器圖示]

5. 選擇適當的帳戶 (具有連線到虛擬機器的 Azure 訂用帳戶)。  
   - 若您已登入 Visual Studio，帳戶清單會填入您所有已驗證的帳戶。  
   - 如果您未登入或未列出您需要的帳戶，請選擇 [新增帳戶...]，並遵循提示來登入。  
   ![Azure 帳戶選取器]  

6. 從現有的虛擬機器清單中選取適當的 VM。

   > [!Note]
   > 填入此清單可能需要一些時間。

   ![Azure VM 選取器]

7. 按一下 [確定] 以開始發行。

8. 當提示輸入憑據時，在配置了發佈許可權的目標 VM 上提供使用者帳戶的使用者名和密碼。 這些憑據通常是創建 VM 時使用的管理員使用者名和密碼。  

   ![WebDeploy 登入]

9. 接受安全性憑證。

   ![憑證錯誤]

10. 查看 [輸出] 視窗來檢查發行作業的進度。

    ![輸出視窗]

11. 如果已成功發行，便會啟動瀏覽器以開啟新發行網站的 URL。

**成功！**

您現在已經成功將 Web 應用程式發行到 Azure 虛擬機器。

## <a name="publish-page-options"></a>發行頁面選項

完成發行精靈之後，會在文件中開啟 [發行] 頁面，並預先選取新的發行設定檔。

### <a name="re-publish"></a>重新發行

若要將更新發佈到 Web 應用程式，請選取發行頁面上的 [發行]**** 按鈕。  
- 若出現提示，請輸入使用者名稱和密碼。  
- 會立即開始發行。

![發行頁面 - 發行按鈕]

### <a name="modify-publish-profile-settings"></a>修改發行設定檔設定

若要檢視和修改發行設定檔設定，請選取 [設定...]****。  

![發行頁面 - 設定按鈕]

設定應該會看起來像這樣：  

![發行設定 - 連線頁面]

#### <a name="save-user-name-and-password"></a>儲存使用者名稱和密碼
- 避免在每次發佈時提供身份驗證資訊。 為此，請填充**使用者名和密碼****欄位，** 然後選擇"**保存密碼"** 框。
- 使用 [驗證連線]**** 按鈕可確認您已輸入正確的資訊。

#### <a name="deploy-to-clean-web-server"></a>部署到乾淨的 Web 伺服器

- 如果要確保 Web 服務器在每次上載後具有 Web 應用程式的乾淨副本，並且以前的部署中未留下其他檔，則可以在 **"設置"** 選項卡中選中 **"在目標選中其他檔**"核取方塊。

- 警告：以這項設定發行會刪除位於 Web 伺服器 (wwwroot 目錄) 上的所有檔案。 請確定您知道啟用這個選項下，發行前的電腦狀態。 

![發行設定 - 設定頁面]

## <a name="next-steps"></a>後續步驟

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>設定 CI/CD 以自動化部署到 Azure VM

若要以 Azure Pipelines 設定持續傳遞管線，請參閱[部署到 Windows 虛擬機器](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups)。

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[發行頁面 - 發行按鈕]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[發行頁面 - Microsoft Azure 虛擬機器圖示]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure 帳戶選取器]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure VM 選取器]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy 登入]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[憑證錯誤]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[輸出視窗]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[發行頁面 - 發行按鈕]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[發行頁面 - 設定按鈕]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[發行設定 - 連線頁面]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[發行設定 - 設定頁面]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
