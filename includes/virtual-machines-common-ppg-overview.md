---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: 3215f5952daef053c94432bc8fdef15e1775047a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73171079"
---
將 VM 放置在單個區域中可減小實例之間的物理距離。 將它們放置在單個可用性區域中也會使它們在物理上更緊密地結合在一起。 但是，隨著 Azure 佔用空間的增長，單個可用性區域可能會跨越多個物理資料中心，這可能會導致網路延遲影響應用程式。 

要盡可能接近 VM，實現盡可能低的延遲，應將它們部署在接近放置組中。

鄰近放置組是一種邏輯分組，用於確保 Azure 計算資源物理上彼此靠近。 接近放置組對於需要低延遲的工作負載非常有用。


- 獨立 VM 之間的低延遲。
- 單個可用性集中的 VM 或虛擬機器規模集中的 VM 之間的低延遲。 
- 獨立 VM、多個可用性集中的 VM 或多個規模集之間的低延遲。 您可以在單個放置組中具有多個計算資源，以組合多層應用程式。 
- 使用不同硬體類型的多個應用程式層之間的低延遲。 例如，在可用性集中使用 M 系列運行後端，在單個鄰近放置組中在比例集中在縮放集中在 D 系列實例上運行前端。


![接近放置組的圖形](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>使用鄰近放置組 

鄰近放置組是 Azure 中的新資源類型。 您需要先創建一個，然後再將其與其他資源一起使用。 創建後，它可以與虛擬機器、可用性集或虛擬機器擴展集一起使用。 在創建提供鄰近放置組 ID 的計算資源時，可以指定鄰近放置組。 

您還可以將現有資源移動到鄰近放置組中。 將資源移動到鄰近放置組時，應首先停止（取消分配）資產，因為它可能會重新部署到區域中的不同資料中心，從而滿足主機代管約束。 

對於可用性集和虛擬機器擴展集，應在資源級別而不是單個虛擬機器設置接近放置組。 

接近放置組是主機代管約束，而不是固定機制。 使用部署使用它的第一個資源，它將固定到特定的資料中心。 停止（處理位置）或刪除使用鄰近放置組的所有資源後，將不再固定它。 因此，在使用具有多個 VM 系列的鄰近放置組時，請務必盡可能在範本中預先指定所有必需的類型，或者遵循部署順序，從而提高成功部署的機會。 如果部署失敗，請重新開機具有 VM 大小的部署，該大小已作為要部署的第一個大小失敗。


## <a name="best-practices"></a>最佳作法 
- 對於最低延遲，請使用鄰近放置組和加速網路。 有關詳細資訊，請參閱[創建具有加速網路的 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)或[創建具有加速網路的 Windows 虛擬機器](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
- 在單個範本中部署所有 VM 大小。 為了避免在不支援所有 VM SKU 和大小的硬體上著陸，請在單個範本中包括所有應用程式層，以便同時部署它們。
- 如果使用 PowerShell、CLI 或 SDK 編寫部署腳本，則可能會收到分配錯誤`OverconstrainedAllocationRequest`。 在這種情況下，應停止/取消分配所有現有 VM，並更改部署腳本中的序列，以從失敗的 VM SKU/大小開始。 
- 重用從中刪除 VM 的現有放置組時，請等待刪除完全完成，然後再向其中添加 VM。
- 如果延遲是您的第一要務，則將 VM 放在接近放置組中，並將整個解決方案放在可用性區域中。 但是，如果恢復能力是您的頭等大事，則跨多個可用性區域（單個接近放置組不能跨區域）展開實例。