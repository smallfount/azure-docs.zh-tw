---
title: 建立適用於 PHP 的 Azure Web 和背景工作角色
description: 在 Azure 雲端服務中建立 PHP Web 和背景工作角色及設定 PHP 執行階段的指南。
services: ''
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 54410e1e70a2ec0d3a9e2f853dc9556cd05996ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297249"
---
# <a name="create-php-web-and-worker-roles"></a>建立 PHP Web 和背景工作角色

## <a name="overview"></a>總覽

本指南將說明如何在 Windows 開發環境中建立 PHP Web 或背景工作角色、從「內建」的可用版本中選擇特定版本的 PHP、變更 PHP 組態、啟用擴充功能，最終部署至 Azure。 此外也會說明如何設定 Web 或背景工作角色，以使用您所提供的 PHP 執行階段 (具有自訂組態和擴充功能)。

Azure 提供三種運算模型來執行應用程式：Azure 應用程式服務、Azure 虛擬機器和 Azure 雲端服務。 這三種模型都支援 PHP。 雲端服務 (包含 Web 和背景工作角色) 可提供 *平台即服務 (PaaS)*。 在雲端服務中，Web 角色提供專用的 Internet Information Services (IIS) Web 伺服器，用來代管前端 Web 應用程式。 背景工作角色可以執行非同步、長時間或永久的工作，且不受使用者互動或輸入所影響。

如需這些選項的詳細資訊，請參閱[計算 Azure 提供的裝載選項](cloud-services/cloud-services-choose-me.md)。

## <a name="download-the-azure-sdk-for-php"></a>下載 Azure SDK for PHP

[Azure SDK for PHP](https://github.com/Azure/azure-sdk-for-php) 由數個元件組成。 本文將使用下列兩個元件：Azure PowerShell 和 Azure 模擬器。 這兩個元件可透過 Microsoft Web Platform Installer 來安裝。 有關詳細資訊，請參閱[如何安裝和配置 Azure PowerShell](/powershell/azure/overview)。

## <a name="create-a-cloud-services-project"></a>建立雲端服務專案

建立 PHP Web 或背景工作角色的第一個步驟是建立 Azure 服務專案。 Azure 服務專案可作為 Web 和背景工作角色的邏輯容器，且包含專案的[服務定義 (.csdef)] 和[服務組態 (.cscfg)] 檔案。

若要建立新的 Azure 服務專案，請以系統管理員身分執行 Azure PowerShell 並執行下列命令：

    PS C:\>New-AzureServiceProject myProject

此命令會建立新目錄 (`myProject`)，讓您可將 Web 和背景工作角色新增至該處。

## <a name="add-php-web-or-worker-roles"></a>新增 PHP Web 或背景工作角色

若要將 PHP Web 角色新增至專案，請從專案的根目錄中執行下列命令：

    PS C:\myProject> Add-AzurePHPWebRole roleName

對於背景工作角色，請使用下列命令：

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> `roleName` 是選用參數。 若省略此參數，將會自動產生角色名稱。 第一個建立的 Web 角色將是 `WebRole1`、第二個將是 `WebRole2`，依此類推。 第一個建立的背景工作角色將是 `WorkerRole1`、第二個將是 `WorkerRole2`，依此類推。
>
>

## <a name="use-your-own-php-runtime"></a>使用您自己的 PHP 執行階段

在某些情況下，您可能會想要提供自己的 PHP 執行階段，而不依照前述的說明選取內建 PHP 執行階段並加以設定。 例如，您可以使用與在開發環境使用的 Web 或背景工作角色中相同的 PHP 執行階段。 這可讓您更輕鬆地確保應用程式在您的生產環境中不會變更行為。

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>設定 Web 角色以使用您自己的 PHP 執行階段

若要設定 Web 角色以使用您所提供的 PHP 執行階段，請遵循下列步驟：

1. 如本主題先前所述，建立 Azure 服務專案並加入 PHP Web 角色。
2. 在位於 Web 角色根目錄內的 `bin` 資料夾中建立 `php` 資料夾，然後將 PHP 執行階段 (所有的二進位檔、組態檔、子資料夾等) 新增至 `php` 資料夾。
3. (選擇性) 如果您的 PHP 執行階段使用[適用於 PHP for SQL Server 的 Microsoft 驅動程式][sqlsrv drivers]，您就必須將 Web 角色設定為在佈建時安裝 [SQL Server Native Client 2012][sql native client]。 若要執行此動作，請將 [sqlncli.msi x64 安裝程式] 新增至 Web 角色根目錄的 `bin` 資料夾中。 下一個步驟中說明的啟動指令碼，將會在角色進行佈建時以無訊息方式執行安裝程式。 如果您的 PHP 執行階段並未使用適用於 PHP for SQL Server 的 Microsoft 驅動程式，您可以從下一個步驟所顯示的指令碼中移除以下一行：

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. 定義啟動工作，設定 [Internet Information Services (IIS)][iis.net] 使用您的 PHP 執行階段來處理 `.php` 頁面的要求。 若要執行此動作，請在文字編輯器中開啟 `setup_web.cmd` 檔案 (位於 Web 角色根目錄的 `bin` 檔案中)，並使用下列指令碼來取代它的內容：

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. 將應用程式檔案新增至 Web 角色的根目錄。 這會是 Web 伺服器的根目錄。
6. 如以下的[發佈您的應用程式](#publish-your-application)一節所述，發佈您的應用程式。

> [!NOTE]
> 在執行前述步驟 (使用您自己的 PHP 執行階段) 之後，您可以將 `download.ps1` 指令碼 (位於 Web 角色根目錄的 `bin` 資料夾中) 刪除。
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>設定背景工作角色以使用您自己的 PHP 執行階段

若要設定背景工作角色以使用您所提供的 PHP 執行階段，請遵循下列步驟：

1. 如本主題先前所述，建立 Azure 服務專案並加入 PHP 背景工作角色。
2. 在背景工作角色的根目錄中建立 `php` 資料夾，然後將 PHP 執行階段 (所有的二進位檔、組態檔、子資料夾等) 新增至 `php` 資料夾。
3. (選擇性) 如果您的 PHP 執行階段使用[適用於 PHP for SQL Server 的 Microsoft 驅動程式][sqlsrv drivers]，您就必須將背景工作角色設定為在佈建時安裝 [SQL Server Native Client 2012][sql native client]。 若要執行此動作， 請將 [sqlncli.msi x64 安裝程式] 新增至背景工作角色的根目錄。 下一個步驟中說明的啟動指令碼，將會在角色進行佈建時以無訊息方式執行安裝程式。 如果您的 PHP 執行階段並未使用適用於 PHP for SQL Server 的 Microsoft 驅動程式，您可以從下一個步驟所顯示的指令碼中移除以下一行：

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. 定義啟動工作，在佈建角色時將您的 `php.exe` 可執行檔新增至背景工作角色的 PATH 環境變數中。 若要執行此動作，請在文字編輯器中開啟 `setup_worker.cmd` 檔案 (位於背景工作角色的根目錄中)，並使用下列指令碼來取代它的內容：

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. 將應用程式檔案新增至背景工作角色的根目錄。
6. 如以下的[發佈您的應用程式](#publish-your-application)一節所述，發佈您的應用程式。

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>在計算和儲存模擬器中執行您的應用程式

Azure 模擬器所提供的本機環境，可讓您在 Azure 應用程式部署至雲端前先加以測試。 模擬器與 Azure 環境之間有若干差異。 若要深入了解，請參閱[使用 Azure 儲存體模擬器進行開發和測試](storage/common/storage-use-emulator.md)。

請注意，您必須在本機安裝 PHP，才能使用計算模擬器。 計算模擬器會使用您的本機 PHP 安裝執行您的應用程式。

若要在模擬器中執行您的專案，請從專案的根目錄執行下列命令：

    PS C:\MyProject> Start-AzureEmulator

您將看到類似以下的輸出：

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

您可以開啟網頁瀏覽器，並瀏覽至輸出中顯示的本機位址 (在前述範例輸出中為`http://127.0.0.1:81` )，即可看見您的應用程式正在模擬器中執行。

若要停止模擬器，請執行下列命令：

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>發佈您的應用程式

若要發佈應用程式，您必須先使用 [Import-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) Cmdlet 匯入您的發佈設定。 使用 [Publish-AzureServiceProject](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) Cmdlet 發佈應用程式，如下所示。 如需登入的相關資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [PHP 開發人員中心](https://azure.microsoft.com/develop/php/)。

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[服務定義（.csdef）]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[服務配置 （.cscfg）]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[sqlncli.msi x64 安裝程式]: https://go.microsoft.com/fwlink/?LinkID=239648
