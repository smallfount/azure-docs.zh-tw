---
title: 範本部署（預覽）
description: 在部署 Azure 資源管理器範本之前，確定資源將發生哪些更改。
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: bc42585204e5cc2c3ece5293a3934fd22fe8507b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156441"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>ARM 範本部署操作（預覽）

在部署 Azure 資源管理器 （ARM） 範本之前，可能需要預覽將發生的更改。 Azure 資源管理器提供假設操作，以便查看部署範本時資源將如何更改。 如果操作不會對現有資源進行任何更改。 相反，如果部署了指定的範本，它將預測更改。

> [!NOTE]
> "如果"操作當前處於預覽狀態。 若要加以使用，您必須[註冊預覽版](https://aka.ms/armtemplatepreviews)。 作為預覽版本，結果有時可能會顯示資源將在實際不會發生更改時更改。 我們正在努力減少這些問題，但我們需要您的説明。 請在 上[https://aka.ms/whatifissues](https://aka.ms/whatifissues)報告這些問題。

您可以將"如果"操作與 PowerShell 命令或 REST API 操作一起使用。

在 PowerShell 中，輸出包括顏色編碼的結果，可説明您查看不同類型的更改。

![資源管理器範本部署操作"全資源負載"和更改類型](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

文本為：

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>如果命令

可以使用 Azure PowerShell 或 Azure REST API 執行 If 操作。

### <a name="azure-powershell"></a>Azure PowerShell

要在部署範本之前查看更改的預覽，請將`-Whatif`switch 參數添加到部署命令。

* `New-AzResourceGroupDeployment -Whatif`資源組部署
* `New-AzSubscriptionDeployment -Whatif`和`New-AzDeployment -Whatif`訂閱級別部署

或者，您可以使用`-Confirm`Switch 參數預覽更改，並提示您繼續部署。

* `New-AzResourceGroupDeployment -Confirm`資源組部署
* `New-AzSubscriptionDeployment -Confirm`和`New-AzDeployment -Confirm`訂閱級別部署

前面的命令返回可以手動檢查的文本摘要。 要獲取可以以程式設計方式檢查更改的物件，請使用：

* `$results = Get-AzResourceGroupDeploymentWhatIf`資源組部署
* `$results = Get-AzSubscriptionDeploymentWhatIf`或`$results = Get-AzDeploymentWhatIf`用於訂閱級別部署

> [!NOTE]
> 在版本 2.0.1-Alpha5 發佈之前，您使用了`New-AzDeploymentWhatIf`該命令。 此命令已替換為`Get-AzDeploymentWhatIf`、`Get-AzResourceGroupDeploymentWhatIf`和`Get-AzSubscriptionDeploymentWhatIf`命令。 如果您使用了早期版本，則需要更新該語法。 該`-ScopeType`參數已被刪除。

### <a name="azure-rest-api"></a>Azure REST API

對於 REST API，請使用：

* [部署 -](/rest/api/resources/deployments/whatif)資源組部署的 IF
* [部署 - 訂閱級別部署的訂閱範圍中"如果"內容](/rest/api/resources/deployments/whatifatsubscriptionscope)

## <a name="change-types"></a>更改類型

"如果"操作列出了六種不同類型的更改：

- **創建**： 資源當前不存在，但在範本中定義。 將創建資源。

- **刪除**：此更改類型僅適用于使用[完整模式](deployment-modes.md)進行部署時。 資源存在，但未在範本中定義。 使用完整模式，資源將被刪除。 此更改類型中僅包含[支援完全模式刪除](complete-mode-deletion.md)的資源。

- **忽略**： 資源存在，但在範本中未定義。 不會部署或修改資源。

- **NoChange**： 資源存在，並在範本中定義。 資源將被重新部署，但資源的屬性不會更改。 當[結果格式](#result-format)設置為`FullResourcePayloads`時，將返回此更改類型，這是預設值。

- **修改**： 資源存在，並在範本中定義。 資源將被重新部署，資源的屬性將更改。 當[結果格式](#result-format)設置為`FullResourcePayloads`時，將返回此更改類型，這是預設值。

- **部署**： 資源存在，並在範本中定義。 資源將被重新部署。 資源的屬性可能更改，也可能不更改。 當該操作沒有足夠的資訊來確定是否有任何屬性將更改時，該操作將返回此更改類型。 僅當[結果格式](#result-format)設置為`ResourceIdOnly`時，您才會看到此條件。

## <a name="result-format"></a>結果格式

您可以控制返回有關預測更改的詳細資訊級別。 在部署命令 （`New-Az*Deployment`中）中使用 **-WhatifResult格式**參數。 在程式設計物件命令 （`Get-Az*DeploymentWhatIf`中）中使用**結果格式**參數。

將格式參數設置為 **"完全資源有效負載"，** 以獲取有關將更改的屬性的資源清單和詳細資訊。 將格式參數設置為 **"僅資源 Id"** 以獲取將更改的資源清單。 預設值為 **"完全資源有效負載**"。  

以下結果顯示兩種不同的輸出格式：

- 完整的資源負載

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- 僅資源識別碼

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>運行"如果"操作

### <a name="set-up-environment"></a>設定環境

要查看假設的工作原理，讓我們運行一些測試。 首先，部署[創建虛擬網路的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json)。 您將使用此虛擬網路來測試由 What-if 報告更改的方式。

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>測試修改

部署完成後，即可測試"備地操作"。 這次部署一個[範本來更改虛擬網路](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)。 缺少原始標記之一，子網已被刪除，位址首碼已更改。

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

如果輸出看起來類似于：

![資源管理器範本部署操作輸出](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

文本輸出為：

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

請注意，在輸出的頂部，顏色被定義以指示更改的類型。

在輸出的底部，它顯示標記擁有者已被刪除。 位址首碼從 10.0.0.0/16 更改為 10.0.0.0/15。 名為子網001的子網已被刪除。 請記住，此更改實際上未部署。 您將看到部署範本時將發生的更改的預覽。

某些列為已刪除的屬性實際上不會更改。 當屬性不在範本中時，可以錯誤地報告為已刪除，但在部署期間會自動設置為預設值。 此結果在 If 回應中被視為"噪音"。 最終部署的資源將設置屬性的值。 當 What-if 操作成熟時，這些屬性將從結果中篩選出來。

## <a name="programmatically-evaluate-what-if-results"></a>以程式設計方式評估"如果"結果

現在，讓我們通過將命令設置為變數來以程式設計方式評估假設結果。

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

您可以看到每個更改的摘要。

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>確認刪除

Whatif 操作支援使用[部署模式](deployment-modes.md)。 當設置為完成模式時，將刪除範本中未使用的資源。 下面的示例部署在完整模式下[未定義資源的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json)。

要在部署範本之前預覽更改，`-Confirm`請使用具有部署命令的 switch 參數。 如果更改與預期相同，請確認您希望部署完成。

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

由於範本中未定義任何資源，並且部署模式設置為完成，因此虛擬網路將被刪除。

![資源管理器範本部署 If 操作輸出部署模式完成](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

文本輸出為：

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

您將看到預期的更改，並可以確認您希望部署運行。

## <a name="next-steps"></a>後續步驟

- 如果您注意到 What-if 預覽版本中的結果不正確，請在 上[https://aka.ms/whatifissues](https://aka.ms/whatifissues)報告問題。
- 要使用 Azure PowerShell 部署範本，請參閱[使用 ARM 範本和 Azure PowerShell 部署資源](deploy-powershell.md)。
- 要使用 REST 部署範本，請參閱[使用 ARM 範本和資源管理器 REST API 部署資源](deploy-rest.md)。
