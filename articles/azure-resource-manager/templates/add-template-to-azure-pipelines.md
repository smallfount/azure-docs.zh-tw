---
title: 包含 Azure 管道和範本的 CI/CD
description: 介紹如何使用 Visual Studio 中的 Azure 資源組部署專案在 Azure 管道中設置連續集成來部署資源管理器範本。
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 7617bf47595fce7baa533b0f7cc94a1803ddd349
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153449"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>將 ARM 範本與 Azure 管道集成

Visual Studio 提供 Azure 資源組專案，用於創建 Azure 資源管理器 （ARM） 範本並將其部署到 Azure 訂閱。 您可以將此專案與 Azure 管道集成，以便持續集成和持續部署 （CI/CD）。

有兩種方法可以使用 Azure 管道部署範本：

* **添加運行 Azure PowerShell 腳本的任務**。 此選項的優點是在整個開發生命週期中提供一致性，因為您使用的腳本與 Visual Studio 專案（部署 AzureResourceGroup.ps1）中包含的腳本相同。 該腳本將專案階段到資源管理器可以訪問的存儲帳戶。 專案是專案中的專案，如連結範本、腳本和應用程式二進位檔案。 然後，腳本部署範本。

* **添加任務以複製和部署任務**。 此選項提供了專案腳本的便捷替代方法。 在管道中配置兩個任務。 一個任務階段工件，另一個任務部署範本。

本文章說明這兩種方法。

## <a name="prepare-your-project"></a>準備您的專案

本文假定視覺化工作室專案和 Azure DevOps 組織已準備好創建管道。 以下步驟演示如何確保您已準備就緒：

* 您有一個 Azure DevOps 組織。 如果沒有，請[免費創建一個](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)。 如果您的團隊已經擁有 Azure DevOps 組織，請確保您是要使用的 Azure DevOps 專案的管理員。

* 您已配置到 Azure 訂閱[的服務連接](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)。 管道中的任務在服務主體的標識下執行。 有關創建連接的步驟，請參閱創建[DevOps 專案](template-tutorial-use-azure-pipelines.md#create-a-devops-project)。

* 您有一個從**Azure 資源組**初學者範本創建的視覺化工作室專案。 有關創建該類型的專案的資訊，請參閱通過[Visual Studio 創建和部署 Azure 資源組](create-visual-studio-deployment-project.md)。

* 您的視覺化工作室專案[已連接到 Azure DevOps 專案](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)。

## <a name="create-pipeline"></a>建立管線

1. 如果以前未添加管道，則需要創建新管道。 從 Azure DevOps 組織中，選擇**管道**和新**管道**。

   ![添加新管道](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. 指定代碼的存儲位置。 下圖顯示了選擇**Azure 存儲庫 Git**。

   ![選擇代碼源](./media/add-template-to-azure-pipelines/select-source.png)

1. 從該源中，選擇具有專案代碼的存儲庫。

   ![選擇存儲庫](./media/add-template-to-azure-pipelines/select-repo.png)

1. 選擇要創建的管道類型。 您可以選擇**初學者管道**。

   ![選擇管道](./media/add-template-to-azure-pipelines/select-pipeline.png)

您已準備好添加 Azure PowerShell 任務或複製檔並部署任務。

## <a name="azure-powershell-task"></a>Azure 電源殼任務

本節演示如何使用在專案中運行 PowerShell 腳本的單個任務來配置連續部署。 以下 YAML 檔創建[Azure PowerShell 任務](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)：

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus'
    azurePowerShellVersion: LatestVersion
```

將任務設置為`AzurePowerShell@3`時，管道將使用 AzureRM 模組中的命令對連接進行身份驗證。 預設情況下，視覺化工作室專案中的 PowerShell 腳本使用 AzureRM 模組。 如果已更新腳本以使用[Az 模組](/powershell/azure/new-azureps-module-az)，請將任務設置為`AzurePowerShell@4`。

```yaml
steps:
- task: AzurePowerShell@4
```

對於`azureSubscription`，提供您創建的服務連接的名稱。

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

對於`scriptPath`，提供從管道檔到腳本的相對路徑。 您可以在存儲庫中查看路徑。

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

如果不需要暫占專案，只需傳遞要用於部署的資源組的名稱和位置。 如果資源組不存在，則視覺化工作室腳本將創建資源組。

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

如果需要將專案暫存到現有存儲帳戶，請使用：

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

現在，您瞭解如何創建任務，讓我們完成編輯管道的步驟。

1. 打開管道，並將內容替換為 YAML：

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. 選取 [儲存]****。

   ![儲存管線](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. 為提交提供消息，並直接提交到**主**。

1. 當您選擇 **"保存"** 時，生成管道將自動運行。 返回生成管道的摘要，並查看狀態。

   ![檢視結果](./media/add-template-to-azure-pipelines/view-results.png)

您可以選擇當前正在運行的管道以查看有關任務的詳細資訊。 完成後，您將看到每個步驟的結果。

## <a name="copy-and-deploy-tasks"></a>複製和部署任務

本節演示如何使用兩個任務來暫派專案並部署範本來配置連續部署。

以下 YAML 顯示了[Azure 檔案複製任務](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)：

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

此任務有幾個部分需要針對您的環境進行修訂。 指示`SourcePath`專案相對於管道檔的位置。 在此示例中，檔存在於名為`AzureResourceGroup1`專案名稱的資料夾中。

```yaml
SourcePath: '<path-to-artifacts>'
```

對於`azureSubscription`，提供您創建的服務連接的名稱。

```yaml
azureSubscription: '<your-connection-name>'
```

對於存儲和容器名稱，請提供要用於存儲工件的存儲帳戶和容器的名稱。 存儲帳戶必須存在。

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

以下 YAML 顯示了[Azure 資源管理器範本部署任務](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)：

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

此任務有幾個部分需要針對您的環境進行修訂。

- `deploymentScope`： 從選項中選擇部署範圍： `Management Group` `Subscription`和`Resource Group`。 在演練中使用**資源組**。 若要深入了解範圍，請參閱[部署範圍](deploy-rest.md#deployment-scope)。

- `ConnectedServiceName`：提供您創建的服務連接的名稱。

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`：提供目標訂閱 ID。 此屬性僅適用于資源組部署範圍和訂閱部署範圍。

- `resourceGroupName`和`location`： 提供要部署到的資源組的名稱和位置。 如果資源組不存在，則該任務將創建該資源組。

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

部署任務連結到名為 WebSite.parameter.json`WebSite.json`的範本和參數檔。 使用範本和參數檔的名稱。

現在，您瞭解如何創建任務，讓我們完成編輯管道的步驟。

1. 打開管道，並將內容替換為 YAML：

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
    - task: AzureResourceGroupDeployment@2
      displayName: 'Deploy template'
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. 選取 [儲存]****。

1. 為提交提供消息，並直接提交到**主**。

1. 當您選擇 **"保存"** 時，生成管道將自動運行。 返回生成管道的摘要，並查看狀態。

   ![檢視結果](./media/add-template-to-azure-pipelines/view-results.png)

您可以選擇當前正在運行的管道以查看有關任務的詳細資訊。 完成後，您將看到每個步驟的結果。

## <a name="next-steps"></a>後續步驟

有關將 Azure 管道與 ARM 範本一起使用的分步過程，請參閱[教程：ARM 範本與 Azure 管道的持續集成](template-tutorial-use-azure-pipelines.md)。
