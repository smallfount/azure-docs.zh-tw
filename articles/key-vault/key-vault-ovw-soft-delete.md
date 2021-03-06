---
title: Azure Key Vault 虛刪除 | Microsoft Docs
description: Azure 金鑰保存庫中的虛刪除允許您恢復已刪除的金鑰保存庫和金鑰保存庫物件，如金鑰、機密和證書。
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 9c72b2ea71da94215fc9193ffdf3906449ec5571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457367"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Azure Key Vault 虛刪除概觀

Key Vault 的虛刪除功能可復原已刪除的保存庫和保存庫物件，也稱為「虛刪除」。 具體而言，我們會說明下列案例：

- 可復原的 Key Vault 刪除支援
- 可復原的 Key Vault 物件刪除支援 (物件的範例如： 金鑰、密碼和憑證)

## <a name="supporting-interfaces"></a>支援的介面

虛刪除功能最初可通過[REST、CLI、PowerShell](key-vault-soft-delete-cli.md)和[PowerShell](key-vault-soft-delete-powershell.md) [.NET/C++ 介面](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet)提供。 [REST](/rest/api/keyvault/)

## <a name="scenarios"></a>案例

Azure Key Vault 是由 Azure Resource Manager 管理的追蹤資源。 Azure Resource Manager 也會指定妥善定義的刪除行為，而成功的「刪除」作業必須使資源無法再被存取。 虛刪除功能可復原已刪除的物件，無論是無意或有意刪除的。

1. 在常見的案例中，使用者可能不小心刪除 Key Vault 或 Key Vault 物件；如果該 Key Vault 或 Key Vault 物件在預定期限內是可復原的，使用者可以還原刪除作業並復原其資料。

2. 而在其他案例中，惡意使用者可能會嘗試刪除 Key Vault 或 Key Vault 物件 (例如保存庫內的金鑰) 而使業務中斷。 將 Key Vault 或 Key Vault 物件的刪除與基礎資料的實際刪除做區隔，可做為一種安全措施，例如，將刪除資料的權限限制為其他信任的角色。 實際上，此方法這需要作業仲裁，否則可能導致直接的資料遺失。

### <a name="soft-delete-behavior"></a>虛刪除行為

啟用虛刪除後，標記為已刪除資源的資源將保留指定時間段（預設情況下為 90 天）。 此服務進一步提供復原已刪除物件的機制 (基本上是復原刪除作業)。

創建新金鑰保存庫時，虛刪除預設處於打開狀態。 您可以通過[Azure CLI](key-vault-soft-delete-cli.md)或[Azure Powershell](key-vault-soft-delete-powershell.md)創建金鑰保存庫，而無需虛刪除。 在金鑰保存庫上啟用虛刪除後，無法禁用

預設保留期為 90 天，但在創建金鑰保存庫期間，可以通過 Azure 門戶將保留原則間隔設置為 7 到 90 天的值。 清除保護保留原則使用相同的間隔。 設置後，無法更改保留原則間隔。

在保留期結束之前，不能重用已虛刪除的金鑰保存庫的名稱。

### <a name="purge-protection"></a>淨化保護 

清除保護是可選的金鑰保存庫行為，**預設情況下不會啟用**。 它可以通過[CLI](key-vault-soft-delete-cli.md#enabling-purge-protection)或[Powershell](key-vault-soft-delete-powershell.md#enabling-purge-protection)打開。

啟用清除保護時，在保留期結束之前，無法清除處於已刪除狀態的保存庫或物件。 虛刪除的保存庫和物件仍然可以恢復，確保遵循保留原則。 

預設保留期為 90 天，但可以通過 Azure 門戶將保留原則間隔設置為 7 到 90 天的值。 設置並保存保留原則間隔後，無法更改該保存庫。 

### <a name="permitted-purge"></a>允許的清除作業

在 Proxy 資源上可透過 POST 作業永久刪除、清除 Key Vault，而這需要特殊權限。 一般而言，只有訂用帳戶擁有者可以清除 Key Vault。 POST 作業會對該保存庫觸發立即性且無法復原的刪除作業。 

例外情況包括：
- 當 Azure 訂閱被標記為*不可刪除時*。 在此情況下，只有此服務可接著執行實際的刪除作業，而且會以排程的程序執行。 
- 在保存庫本身上啟用啟用 -啟用-清除保護標誌時。 在此情況下，Key Vault 會從原始祕密物件標示為要刪除的那天算起，等待 90 天後才永久刪除該物件。

### <a name="key-vault-recovery"></a>Key Vault 復原

一旦刪除 Key Vault，此服務會在訂用帳戶下建立 Proxy 資源，以新增足夠使用於復原的中繼資料。 Proxy 資源是預存物件，和刪除的 Key Vault 位於相同位置。 

### <a name="key-vault-object-recovery"></a>Key Vault 物件復原

刪除金鑰保存庫物件（如金鑰）後，服務將物件置於已刪除狀態，從而使任何檢索操作都無法訪問該物件。 此狀態下的 Key Vault 物件只能列出、復原或強制/永久刪除。 

同時，Key Vault 會根據刪除的 Key Vault 或 Key Vault 物件來排程基礎資料的刪除，並在預定的保留間隔後執行。 在保留間隔期間，也會保留與保存庫相對應的 DNS 記錄。

### <a name="soft-delete-retention-period"></a>虛刪除保留期限

虛刪除的資源預設會保留 90 天的時間。 在虛刪除保留間隔期限內，下列說明是成立的：

- 您可以列出您的訂用帳戶下狀態是虛刪除的所有 Key Vault 和 Key Vault 物件，也能存取與它們相關的刪除和復原資訊。
    - 只有具備特殊權限的使用者可以列出已刪除的保存庫。 我們建議使用者建立具有這些特殊權限的自訂角色，以處理刪除的保存庫。
- 不能在同一個位置建立同名的 Key Vault；同樣地，若指定的 Key Vault 中含有同名且是已刪除狀態的物件，就無法在其中建立該 Key Vault 物件。 
- 只有具備特殊權限的使用者，可以在對應的 Proxy 資源上發出復原命令來還原 Key Vault 或 Key Vault 物件。
    - 身為自訂角色成員 (這個角色有權在資源群組下建立 Key Vault) 的使用者，可以還原保存庫。
- 只有具備特殊權限的使用者，可以在對應的 Proxy 資源上發出刪除命令來強制刪除 Key Vault 或 Key Vault 物件。

除非 Key Vault 或 Key Vault 物件已復原，否則在保留間隔結束時，此服務會對虛刪除的 Key Vault 或 Key Vault 物件及其內容執行清除作業。 資源刪除作業無法重新排程。

### <a name="billing-implications"></a>計費影響

一般情況下，當物件 (金鑰保存庫或金鑰或密碼) 處於已刪除狀態時，可執行的作業只有兩種：「清除」與「復原」。 其他所有作業都會失敗。 因此，即使物件存在，但因為無法執行任何作業，所以不會有使用量發生，當然也就不會有費用。 然而，以下例外狀況不適用：

- 「清除」與「復原」動作都算是一般金鑰保存庫作業，因此將予以計費。
- 如果物件是 HSM 金鑰，而且金鑰版本在過去 30 天內曾經使用過，我們將會針對每個金鑰版本收取「HSM 保護的金鑰」費用的月費。 後續由於物件處於已刪除狀態，沒有可執行的作業，因此我們將不收費。

## <a name="next-steps"></a>後續步驟

下列兩個指南提供使用虛刪除的主要使用方式案例。

- [如何透過 PowerShell 使用金鑰保存庫虛刪除](key-vault-soft-delete-powershell.md) 
- [如何以 CLI 使用金鑰保存庫虛刪除](key-vault-soft-delete-cli.md)

