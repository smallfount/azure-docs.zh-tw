---
title: Azure 自動化中的連接資產
description: Azure 自動化中的連接資產包含從 Runbook 或 DSC 設定連接到外部服務或應用程式所需的資訊。 這篇文章說明連接的詳細資料，以及如何以文字和圖形化編寫形式加以使用。
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: b6276153921feb0e6f27194d36d1c32c1d0ffb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75940822"
---
# <a name="connection-assets-in-azure-automation"></a>Azure 自動化中的連接資產

自動化連接資產包含從 Runbook 或 DSC 設定連接到外部服務或應用程式所需的資訊。 除了連接資訊，例如 URL 或連接埠等，這可能包括驗證所需的資訊，例如使用者名稱和密碼。 連接的值會將連接到特定應用程式的所有屬性放在一個資產中，而不是建立多個變數。 使用者可以在一個地方編輯連接的值，而您可以將連接的名稱在單一參數中傳遞至 Runbook 或 DSC 設定。 可以在 Runbook 或 DSC 設定中使用 **Get-AutomationConnection** 活動存取連接的屬性。

當您建立連線時，您必須指定 [連線類型]**。 連接類型是定義一組屬性的範本。 連接會定義在其連接類型中定義的每一個屬性的值。 如果整合模組包含連線類型，且已匯入您的自動化帳戶，連線類型是在整合模組中加入 Azure 自動化或使用 [Azure 自動化 API](/previous-versions/azure/reference/mt163818(v=azure.100))建立。 否則，您必須建立中繼資料檔案來指定自動化連線類型。 有關此的詳細資訊，請參閱[集成模組](automation-integration-modules.md)。

>[!NOTE]
>Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一金鑰儲存在 Azure 自動化中。 此金鑰會儲存在系統管理的 Key Vault 中。 在儲存安全資產之前，系統會從 Key Vault 載入金鑰，然後用來加密資產。 此程序由 Azure 自動化所管理。

## <a name="connection-types"></a>連線類型

Azure 自動化中有三種類型的內置連接：

* **Azure** - 此連線可用來管理傳統資源。
* **AzureClassicCertificate** - 此連線由 **AzureClassicRunAs** 帳戶所使用。
* **AzureServicePrincipal** - 此連線由 **AzureRunAs** 帳戶所使用。

在大多數情況下，您不需要創建連接資源，因為它是在創建[RunAs 帳戶](manage-runas-account.md)時創建的。

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell Cmdlet

下表中的 Cmdlet 是用來使用 Windows PowerShell 建立和管理自動化連接。 它們作為[Azure PowerShell 模組](/powershell/azure/overview)的一部分發貨，可用於自動化運行簿和 DSC 配置。

|Cmdlet|描述|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|擷取連接。 包含具有連接欄位值的雜湊表。|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|建立新連接。|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|移除現有的連接。|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|設定現有連接的特定欄位的值。|

## <a name="activities"></a>活動

下表中的活動是用來存取 Runbook 或 DSC 設定中的連接。

|活動|描述|
|---|---|
|Get-AutomationConnection | 取得要使用的連接。 傳回具有連線屬性的雜湊表。|

>[!NOTE]
>您應該避免在 **Get-AutomationConnection** 的 -Name 參數使用變數，因為這可能使得在設計階段探索 Runbook 或 DSC 設定與連線資產之間的相依性變得複雜。


## <a name="python2-functions"></a>Python2 函式

下表中的函式用於存取 Python2 Runbook 中的連線。

| 函式 | 描述 |
|:---|:---|
| automationassets.get_automation_connection | 擷取連接。 傳回具有連線屬性的字典。 |

> [!NOTE]
> 您必須在 Python Runbook 的頂端匯入「automationassets」模組，才能存取資產函式。

## <a name="creating-a-new-connection"></a>建立新連接

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>使用 Azure 入口網站建立新的連接

1. 從您的自動化帳戶，按一下 [資產]**** 部分，以開啟 [資產]**** 刀鋒視窗。
2. 按一下 [連接]**** 部分，以開啟 [連接]**** 刀鋒視窗。
3. 在分頁的頂端按一下 [ **加入連接** ]。
4. 在 [ **類型** ] 下拉式清單中，選取您想要建立的連接類型。 表單會顯示該特定類型的屬性。
5. 完成表單，然後按一下 [ **建立** ] 以儲存新連接。

### <a name="to-create-a-new-connection-with-windows-powershell"></a>使用 Windows PowerShell 建立新連接

透過 Windows PowerShell 使用 [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) Cmdlet 建立新連接。 此 Cmdlet 具有名為 **ConnectionFieldValues** 的參數，該參數預期會有[雜湊表](https://technet.microsoft.com/library/hh847780.aspx)，而其中定義連線類型所定義之每個屬性的值。

如果您熟悉以自動化中的[執行身分帳戶](automation-sec-configure-azure-runas-account.md)向使用服務主體之 Runbook 驗證的方式，PowerShell 指令碼 (提供做為從入口網站建立執行身分帳戶的替代做法) 會使用下列範例命令建立新的連接資產。

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

您可以使用此指令碼來建立連線資產，因為建立「自動化」帳戶時，預設會自動包含數個全域模組以及 **AzureServicePrincipal** 連線類型，以建立**AzureRunAsConnection** 連線資產。 這很重要要牢記在心，因為如果您嘗試建立新的連接資產來連接到採用不同驗證方法的服務或應用程式，它會失敗，因為在您的自動化帳戶未定義該連接類型。 有關如何從[PowerShell 庫](https://www.powershellgallery.com)為自訂或模組創建自己的連線類型的詳細資訊，請參閱[集成模組](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>在 Runbook 或 DSC 設定中使用連接

您會使用 **Get-AutomationConnection** Cmdlet 在 Runbook 或 DSC 設定中擷取連接。 您不能使用 [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) 活動。 此活動檢索連接中不同欄位的值，並將其作為[雜湊表](https://go.microsoft.com/fwlink/?LinkID=324844)返回，然後可以使用 Runbook 或 DSC 配置中的相應命令。

### <a name="textual-runbook-sample"></a>文字式 Runbook 範例

下列範例命令示範如何使用先前提及的執行身分帳戶，向您的 Runbook 中的 Azure Resource Manager 資源驗證。 它會使用代表執行身分帳戶的連接資產，亦即會參考憑證型服務主體而非認證型。

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** 現在是 **Connect-AzureRMAccount** 的別名。 搜尋您的程式庫項目時，如果沒有看到 **Connect-AzureRMAccount**，便可以使用 **Add-AzureRmAccount**，或是在自動化帳戶中更新模組。

### <a name="graphical-runbook-samples"></a>圖形化 Runbook 範例

通過按右鍵圖形編輯器的 **"庫"** 窗格中的連接並選擇"**添加到畫布**"，將 **"獲取自動化連接"** 活動添加到圖形 Runbook。

![加入至畫布](media/automation-connections/connection-add-canvas.png)

下圖顯示在圖形化 Runbook 中使用連接的範例。 這是如上所述，使用執行身分帳戶與文字 Runbook 進行驗證的相同範例。 這個範例會對使用連接物件進行驗證的 **Get RunAs Connection** 活動使用 **Constant value** 資料集。 在此處使用[管線連結](automation-graphical-authoring-intro.md#links-and-workflow)，是因為 ServicePrincipalCertificate 參數集預期的是單一物件。

![取得連線](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Python2 Runbook 範例

下列範例會示範如何使用 Python2 Runbook 中的「執行身分」連線進行驗證。

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resoruce manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>後續步驟

- 閱讀[圖形化編寫中的連結](automation-graphical-authoring-intro.md#links-and-workflow)，以了解如何引導和控制您的 Runbook 中的邏輯流程。

- 若要進一步了解 Azure 自動化如何使用 PowerShell 模組，以及建立自有 PowerShell 模組來做為 Azure 自動化內整合模組的最佳做法，請參閱[整合模組](automation-integration-modules.md)。