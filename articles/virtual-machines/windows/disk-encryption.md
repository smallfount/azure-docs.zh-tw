---
title: Azure 託管磁碟的伺服器端加密 - PowerShell
description: Azure 儲存通過在靜態加密數據之前對其進行靜態加密來保護數據,然後再將其保存到存儲群集。 您可以依賴 Microsoft 管理的密鑰來加密託管磁碟,也可以使用客戶管理的密鑰使用您自己的密鑰管理加密。
author: roygara
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 13985b07b4903504fde6b58031a532337d3b1971
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584604"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Azure 託管磁碟的伺服器端加密

默認情況下,Azure 託管磁碟在將數據保存到雲中時自動加密數據。 伺服器端加密可保護您的數據,並説明您履行組織安全性和合規性承諾。 Azure 託管磁碟中的資料使用 256 位[元 AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)進行透明加密,這是可用的最強的塊密碼之一,並且符合 FIPS 140-2 標準。

加密不會影響託管磁碟的性能。 加密沒有額外費用。

有關 Azure 託管磁碟基礎的加密模組的詳細資訊,請參閱加密[API:下一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

您可以依賴平臺管理的密鑰來加密託管磁碟,也可以使用自己的密鑰管理加密。 如果選擇使用自己的密鑰管理加密,則可以指定*客戶託管密鑰*,用於加密和解密託管磁盤中的所有資料。 

以下各節將更詳細地介紹密鑰管理的每個選項。

## <a name="platform-managed-keys"></a>平台管理金鑰

預設情況下,託管磁碟使用平臺管理的加密密鑰。 自 2017 年 6 月 10 日起,所有寫入現有託管磁碟的新託管磁碟、快照、映射和新數據都使用平臺託管密鑰自動加密靜態。

## <a name="customer-managed-keys"></a>客戶管理的金鑰

您可以選擇使用自己的金鑰在每個託管磁碟級別管理加密。 使用客戶託管金鑰的託管磁碟的伺服器端加密提供了 Azure 密鑰保管庫的整合體驗。 您可以將[RSA 金鑰](../../key-vault/key-vault-hsm-protected-keys.md)匯入金鑰保管庫,或在 Azure 密鑰保管庫生成新的 RSA 金鑰。 

Azure 託管磁碟使用[信封加密](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)以完全透明的方式處理加密和解密。 它使用基於[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 的資料加密金鑰 (DEK) 加密數據,而密鑰又使用金鑰進行保護。 儲存服務生成資料加密金鑰,並使用 RSA 加密使用客戶管理的密鑰對其進行加密。 信封加密允許您根據合規性策略定期輪換(更改)密鑰,而不會影響 VM。 旋轉金鑰時,儲存服務使用新的客戶管理密鑰重新加密數據加密密鑰。 

您必須授予對金鑰保管庫中的託管磁碟的訪問許可權,才能使用密鑰加密和解密 DEK。 這允許您完全控制數據和密鑰。 您可以隨時禁用金鑰或撤銷對託管磁碟的訪問許可權。 還可以使用 Azure 金鑰保管庫監視審核加密金鑰使用方式,以確保只有託管磁碟或其他受信任的 Azure 服務才能存取金鑰。

對於進階 SSD、標準 SSD 和標準 HDD:當您關閉或刪除金鑰時,任何使用該金鑰的磁碟的 VM 將自動關閉。 在此之後,除非再次啟用密鑰或您分配新密鑰,否則 VM 將不可用。

對於超級磁碟,當您禁用或刪除密鑰時,使用該密鑰的任何具有超磁碟的 VM 不會自動關閉。 取消分配並重新啟動 VM 後,磁碟將停止使用密鑰,然後 VM 將無法重新連線。 要使 VM 重新連線,必須分配新密鑰或啟用現有金鑰。

下圖顯示了託管磁碟如何使用 Azure 活動目錄和 Azure 金鑰保管庫使用客戶管理的金鑰發出請求:

![託管磁碟和客戶管理密鑰工作流。 管理員創建 Azure 密鑰保管庫,然後創建磁碟加密集,並設置磁碟加密集。 該集與 VM 相關聯,它允許磁碟利用 Azure AD 進行身份驗證](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


下面的清單更詳細地解釋了該關係圖:

1. Azure 密鑰保管庫管理員創建金鑰保管庫資源。
1. 金鑰保管庫管理員將 RSA 金鑰導入金鑰保管庫或在密鑰保管庫中生成新的 RSA 金鑰。
1. 該管理員創建磁碟加密集資源的實例,指定 Azure 密鑰保管庫 ID 和密鑰 URL。 磁碟加密集是為簡化託管磁碟的密鑰管理而引入的新資源。 
1. 建立磁碟加密集時,在 Azure 活動目錄 (AD) 中創建[系統分配的託管標識](../../active-directory/managed-identities-azure-resources/overview.md),並與磁碟加密集關聯。 
1. 然後,Azure 密鑰保管庫管理員授予託管標識許可權,以在密鑰保管庫中執行操作。
1. VM 用戶通過將磁碟與磁碟加密集相關聯來創建磁碟。 VM 使用者還可以透過將現有資源的金鑰與磁碟加密集相關聯,使用客戶管理的現有資源密鑰啟用伺服器端加密。 
1. 託管磁碟使用託管標識向 Azure 密鑰保管庫發送請求。
1. 對於讀取或寫入資料,託管磁碟向 Azure 密鑰保管庫發送請求以加密(包裝)和解密(解包)數據加密密鑰,以便對數據執行加密和解密。 

要復原對客戶管理金鑰的存取,請參閱[Azure 金鑰保管庫 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/)和[Azure 金鑰保管庫 CLI](https://docs.microsoft.com/cli/azure/keyvault)。 由於 Azure 儲存無法存取加密金鑰,因此復原存取可有效地阻止對儲存帳戶中的所有資料的存取。

### <a name="supported-regions"></a>支援區域

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>限制

目前,客戶管理的金鑰具有以下限制:

- 如果為磁碟啟用了此功能,則無法禁用它。
    如果需要解決此問題,則必須[將所有數據複製到](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)不使用客戶託管密鑰的完全不同的託管磁碟。
- 僅支援大小為 2080 的[「軟」和「硬」RSA 密鑰](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types),沒有其他鍵或大小。
- 使用伺服器端加密和客戶管理的密鑰加密的自定義映射創建的磁碟必須使用相同的客戶管理密鑰進行加密,並且必須在同一訂閱中。
- 使用伺服器端加密和客戶管理的密鑰加密的磁碟創建的快照必須使用相同的客戶管理金鑰進行加密。
- 在共享映射庫中,無法使用使用伺服器端加密和客戶管理密鑰加密的自定義映射。
- 與客戶管理的密鑰(Azure 密鑰保管庫、磁碟加密集、VM、磁碟和快照)相關的所有資源必須位於同一訂閱和區域中。
- 使用客戶管理的密鑰加密的磁碟、快照和映射無法移動到其他訂閱。
- 如果使用 Azure 門戶創建磁碟加密集,則當前無法使用快照。
- 使用客戶託管金鑰加密的託管磁碟也不能使用 Azure 磁碟加密進行加密。

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>設定 Azure 金鑰保管庫和磁碟加密集

1. 請確保您已安裝最新的 Azure [PowerShell 版本](/powershell/azure/install-az-ps),並且已登入 Connect-AzAccount 登入 Azure 帳戶

1. 建立 Azure 金鑰保管庫和加密金鑰的實例。

    創建金鑰保管庫實例時,必須啟用軟刪除和清除保護。 軟刪除可確保金鑰保管庫在給定保留期(預設為 90 天)保留已刪除的密鑰。 清除保護可確保在保留期結束之前不能永久刪除已刪除的密鑰。 這些設定可保護您不因意外刪除而丟失資料。 使用金鑰保管庫加密託管磁碟時,這些設置是必需的。

    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.    創建磁碟加密集的實例。 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    授予磁碟加密集對密鑰保管庫的資源訪問許可權。

        > [!NOTE]
        > Azure 可能需要幾分鐘才能在 Azure 活動目錄中創建磁碟加密集的標識。 如果在運行以下命令時遇到"找不到活動目錄物件"之類的錯誤,請等待幾分鐘,然後重試。
        
        ```powershell
        $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
         
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
         
        New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>使用應用商店映像建立 VM,使用客戶管理的金鑰加密作業系統和資料磁碟

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>使用使用客戶管理的金鑰使用伺服器端加密建立加密的空白磁碟,並將其附加到 VM

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

#### <a name="encrypt-existing-unattached-managed-disks"></a>加密現有未連線託管磁碟 

您現有的磁碟不得連線到正在執行的 VM,以便您使用以下文稿對其進行加密:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>使用應用商店映像建立虛擬機器規模集,使用客戶管理的金鑰加密作業系統和資料磁碟

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>變更磁碟加密集的鍵以旋轉參考磁碟加密集的所有資源的金鑰

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>尋找磁碟伺服器端加密的狀態

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$DiskName="yourDiskName"

$disk=Get-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName
$disk.Encryption.Type

```

> [!IMPORTANT]
> 客戶託管金鑰依賴於 Azure 資源的託管標識,這是 Azure 活動目錄 (Azure AD) 的一項功能。 配置客戶管理的密鑰時,託管標識將自動分配給所覆蓋的資源。 如果隨後將訂閱、資源組或託管磁碟從一個 Azure AD 目錄移動到另一個 Azure AD 目錄,則與託管磁碟關聯的託管標識不會傳輸到新租戶,因此客戶託管密鑰可能不再工作。 有關詳細資訊,請參閱在[Azure AD 目錄之間傳輸訂閱](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> 客戶託管金鑰依賴於 Azure 資源的託管標識,這是 Azure 活動目錄 (Azure AD) 的一項功能。 配置客戶管理的密鑰時,託管標識將自動分配給所覆蓋的資源。 如果隨後將訂閱、資源組或託管磁碟從一個 Azure AD 目錄移動到另一個 Azure AD 目錄,則與託管磁碟關聯的託管標識不會傳輸到新租戶,因此客戶託管密鑰可能不再工作。 有關詳細資訊,請參閱在[Azure AD 目錄之間傳輸訂閱](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>伺服器端加密與 Azure 磁碟加密

[Azure 磁碟加密](../../security/fundamentals/azure-disk-encryption-vms-vmss.md)利用 Windows 的[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)功能和 Linux 的[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)功能在來賓 VM 中使用客戶託管金鑰對託管磁碟進行加密。  使用客戶管理的密鑰的伺服器端加密通過加密儲存服務中的數據來為 VM 使用任何作業系統類型和映射,從而改進了 ADE 上的任何作業系統類型和映射。

## <a name="next-steps"></a>後續步驟

- [瀏覽 Azure 資源管理員樣本,以便使用客戶管理的金鑰建立加密磁碟](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [何謂 Azure Key Vault？](../../key-vault/key-vault-overview.md)
- [使用啟用客戶管理的金鑰磁碟複製電腦](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [使用 PowerShell 設定 VMware VM 至 Azure 的災害復原](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [針對 Hyper-V VM，使用 PowerShell 和 Azure Resource Manager 設定至 Azure 的災害復原](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
