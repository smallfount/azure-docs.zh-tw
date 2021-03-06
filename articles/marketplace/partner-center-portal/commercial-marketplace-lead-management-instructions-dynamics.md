---
title: Dynamics 365 客戶參與的潛在客戶管理 |Azure 應用商店
description: 為客戶參與度配置 Dynamics 365 的潛在客戶管理。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dsindona
ms.openlocfilehash: 8af6b3a451d20bcc9cab3fa4adb9643f82b85e49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288813"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>為客戶參與度配置 Dynamics 365 的潛在客戶管理

本文介紹如何為客戶參與度設置 Dynamics 365（以前動態 CRM 線上），[在此處](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online)閱讀有關處理市場產品銷售線索的更改。 

>[!Note]
>這些說明特定于 Microsoft 託管的雲動態 365，用於客戶參與環境。 當前不支援直接連接到 Dynamics on-prem 環境，還有其他選項可供您接收潛在客戶，例如配置[HTTPs 終結點](./commercial-marketplace-lead-management-instructions-https.md)或[Azure 表](./commercial-marketplace-lead-management-instructions-azure-table.md)以接收潛在客戶。

## <a name="prerequisites"></a>Prerequisites

完成本文中的步驟需要以下使用者許可權：

* 您需要成為 Dynamics 365 客戶參與實例上的管理員，以便能夠安裝解決方案並遵循這些說明。
* 您需要是租戶管理員，才能為用於從市場產品/服務發送潛在客戶的潛在客戶服務創建新的服務帳戶。
* 您需要有權訪問 Office 365 監管中心。
* 您需要有權訪問 Azure 門戶。

## <a name="install-the-solution"></a>安裝解決方案

1.  下載[Microsoft 應用商店首席編寫器解決方案](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)，並將其本地保存到您的電腦。

2.  通過導航到 Dynamics 實例的 URL（如`https://tenant.crm.dynamics.com`），為客戶參與打開動態 365。

3.  通過選擇頂部巡覽列上的齒輪圖示和**高級設置**來訪問設置。
 
    ![動態 - 高級設置](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  在"設置"頁上，從頂部巡覽列訪問"設置"功能表並選擇 **"解決方案**"。

    >[!Note]
    >如果在下一個螢幕截圖中看不到這些選項，則您沒有繼續所需的許可權。 聯繫 Dynamics 365 上的管理員，瞭解客戶參與度實例。

    ![動態 365 - 解決方案](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. 在"解決方案"頁上，選擇 **"導入"** 並導航到在步驟 1 中下載的*Microsoft 應用商店首席編寫器*解決方案保存的位置。

    ![面向客戶參與的動態 365 - 導入](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. 按照導入解決方案嚮導完成導入解決方案。

## <a name="configure-user-permissions"></a>設定使用者權限

要將潛在客戶寫入 Dynamics 365 的客戶參與實例，您必須與我們共用服務帳戶，並配置該帳戶的許可權。

使用下列步驟建立服務帳戶，並指派權限。 您可以使用 **Azure Active Directory** 或 **Office 365**。

>[!Note]
>根據您選擇的身份驗證選項，您可以根據您的選擇跳到相應的說明。 請參閱[Azure 活動目錄](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory)或[Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365)。

### <a name="azure-active-directory"></a>Azure Active Directory

我們建議使用此選項，因為您得到了無需更新使用者名/密碼即可繼續獲取潛在客戶的額外好處。 要使用 Azure 活動目錄選項，請從活動目錄應用程式提供應用 ID、應用程式金鑰和目錄 ID。

使用以下步驟為客戶參與配置 Dynamics 365 的 Azure 活動目錄。

1. 登錄到[Azure 門戶](https://portal.azure.com/)，然後從左側導航中選擇 Azure 活動目錄服務。

2. 從 Azure 活動目錄左側導航中選擇**屬性**，並在該頁上複製**目錄 ID**值。 保存此值，因為需要在發佈門戶中提供*目錄 ID*值，以便接收市場產品/服務的潛在客戶。

    ![Azure 活動目錄 - 屬性](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. 從 Azure 活動目錄左側導航**中選擇應用註冊**，然後選擇該頁上**的新註冊**。
4. 輸入應用程式名稱的名稱。 提供有意義的應用程式名稱。
5. 在"支援帳戶類型"下，選擇**任何組織目錄中的帳戶**。
6. 在重定向 URI 下，選擇**Web**並提供 URI（如`https://contosoapp1/auth`）。 
7. 選取 [註冊]****。

    ![註冊應用程式](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. 註冊應用程式後，訪問應用程式的概覽頁並複製該頁上**的應用程式（用戶端）ID**值。 保存此值，因為需要在發佈門戶和 Dynamics 中提供*應用程式（用戶端）ID*值，以便接收市場產品的潛在客戶。

    ![應用程式 (用戶端) 識別碼](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. 從應用的左側導航中選擇**證書和機密**，然後選擇該頁上**的"新建用戶端機密**"。 輸入用戶端機密的有意義的說明，然後選擇"過期"下的 **"從不"** 選項。 選擇 **"添加"** 以創建用戶端機密。

    ![應用程式 - 認證和秘密](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. 成功創建用戶端金鑰後，**立即複製用戶端機密值**。 導航離開頁面後，您將無法檢索該值。 保存此值，因為它是您需要在發佈門戶中提供的*用戶端機密*值，以便接收市場產品的潛在客戶。 
11. 從應用的左側導航中選擇**API 許可權**，然後選擇 **"添加許可權**"。
12. 選擇 Microsoft API，然後選擇**動態 CRM**作為 API。
13. 在 *"應用程式需要哪些類型的許可權*"下，請確保選擇了**委派許可權**。 檢查**user_impersonation***作為組織使用者訪問公共資料服務*的許可權。 選取 [新增權限]****。

    ![新增權限](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. 在 Azure 門戶上完成步驟 1-13 後，通過導航到 URL（如`https://tenant.crm.dynamics.com`），導航到客戶參與實例的動態 365。
15. 通過選擇齒輪圖示和頂部巡覽列上的 **"高級設置"** 來訪問設置。
16. 在"設置"頁上，從頂部巡覽列訪問"設置"功能表並選擇 **"安全**"。
17. 在"安全"頁上一次，選擇 **"使用者**"。  在"使用者"頁上，選擇"啟用使用者"下拉清單以切換到**應用程式使用者**。
18. 選取 [新增]**** 以建立新的使用者。 

    ![建立新的使用者](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. 在新**使用者**中，請確保選擇了使用者：應用使用者。 為要使用此連接的使用者提供使用者名、全名和電子郵件地址。 此外，請粘貼到步驟 8 中在 Azure 門戶中創建的應用**的應用程式 ID**中。 選擇 **"保存"和"關閉**"以完成使用者添加。

    ![新增使用者](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. 移至此文章中的「安全性設定」以完成設定此使用者的連線。

### <a name="office-365"></a>Office 365

如果不想使用 Azure 活動目錄，可以在*Microsoft 365 管理中心*註冊新使用者。 您需要每 90 天更新一次使用者名稱/密碼，以繼續獲得潛在客戶。

使用以下步驟為 Dynamics 365 為客戶參與配置 Office 365。

1. 登入 [Microsoft 365 系統管理中心](https://admin.microsoft.com)。

2. 選取 [新增使用者]****。

    ![微軟365管理中心 - 添加使用者](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. 為潛在客戶寫入器服務建立新使用者。 設定下列設定：

    * 提供使用者名
    * 提供密碼，並取消選擇"讓此使用者在首次登錄時更改其密碼"選項。
    * 選取 [使用者 (沒有系統管理員存取權)] 作為使用者的角色。
    * 選擇"動態 365 客戶參與計畫"作為下一個螢幕捕獲中顯示的產品許可證。 您將需要支付您所選擇的授權。 

保存這些值，因為它們是您在發佈門戶中接收市場產品的潛在客戶所需的*使用者名和密碼*值。

![微軟365管理中心 - 新使用者](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>安全性設定

最後一個步驟是讓您所建立的 [使用者] 寫入潛在客戶。

1. 通過導航到 Dynamics 實例的 URL（如`https://tenant.crm.dynamics.com`），為客戶參與打開動態 365。
2. 通過選擇頂部巡覽列上的齒輪圖示和**高級設置**來訪問設置。
3. 在"設置"頁上，從頂部巡覽列訪問"設置"功能表並選擇 **"安全**"。
4. 在"安全"頁上，選擇 **"使用者"** 並選擇您在本文檔的"配置使用者許可權"部分中創建的使用者，然後選擇 **"管理角色**"。 

    ![管理角色](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. 搜索角色名稱"Microsoft 市場首席編寫器"並選擇它來分配使用者的角色。

    ![管理使用者角色](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >此角色由您匯入的解決方案所建立，僅擁有寫入潛在客戶，以及追蹤解決方案版本以確保相容性的權限。

6. 導航回安全頁面並選擇**安全形色**。 搜索角色"微軟市場首席編寫者"並選擇它。

    ![安全性角色](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. 進入安全形色後，選擇 **"核心記錄**"選項卡。搜索"使用者實體 UI 設置"實體，並通過按一下一次到每個相應的圈子，為該實體啟用該實體的創建、讀取和寫入權限（1/4 黃色圓圈）。

    ![微軟市場首席撰稿人 - 核心記錄](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. 現在導航到 **"自訂**"選項卡。搜索 tor"系統作業"實體，並通過按一下四次按一下每個相應圈子，為該實體啟用"讀取、寫入和追加到組織"許可權（純綠色）。

    ![微軟市場首席編寫器 - 自訂](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **保存並關閉**。

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>將產品/服務配置為將潛在客戶發送到 Dynamics 365 以進行客戶互動

準備好在發佈門戶中配置產品/服務的首席管理資訊時，請按照以下步驟操作：

1. 導航到產品 **/服務"產品/服務"設置**頁面。
2. 在"潛在客戶管理"部分下選擇 **"連接**"。

    ![連接到潛在客戶管理](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. 在"連接詳細資訊"快顯視窗上，為潛在客戶目標**的客戶參與度選擇 Dynamics 365**

    ![連接詳細資訊 - 潛在客戶目標](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. 提供**Dynamics 365 實例 URL，** 如`https://contoso.crm4.dynamics.com`。
5. 選擇**身份驗證**、Azure 活動目錄或 Office 365 的方法。 
6. 如果選擇 Azure 活動目錄，請提供**應用程式（用戶端）ID（** 例如`23456052-aaaa-bbbb-8662-1234df56788f`： ），**目錄 ID（** 示例：）`12345678-8af1-4asf-1234-12234d01db47`和**用戶端金鑰**（示例： `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`）。

    ![連接詳細資訊 - Azure 活動目錄](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. 如果選擇 Office 365，請提供**使用者名**（示例：）`contoso@contoso.onmicrosoft.com`和密碼（示例： `P@ssw0rd`）。

    ![連接詳細資訊 - 使用者名](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>您必須完成配置產品/服務的其餘部分併發布它，然後才能收到產品/服務的潛在客戶。
