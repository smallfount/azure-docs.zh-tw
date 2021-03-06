---
title: 按雲簡單私有雲管理 Azure VMware 解決方案
description: 描述可用於管理雲簡單私有雲資源和活動的功能
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 620c0226d3aca907352658ebbe1b94c7673d91cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014822"
---
# <a name="manage-private-cloud-resources-and-activity"></a>管理私有雲資源和活動

私有雲由雲簡單門戶管理。  從 CloudSimple 門戶檢查狀態、可用資源、私有雲上的活動和其他設置。

## <a name="sign-in-to-azure"></a>登入 Azure

登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

訪問[雲簡單門戶](access-cloudsimple-portal.md)。

## <a name="view-the-list-of-private-clouds"></a>查看私有雲清單

**"資源**"頁上的 **"私有雲**"選項卡列出了訂閱中的所有私有雲。 資訊包括名稱、vSphere 群集的數量、位置、私有雲的目前狀態以及資源資訊。

![私有雲頁面](media/manage-private-cloud.png)

選擇私有雲以獲取其他資訊和操作。

## <a name="private-cloud-summary"></a>私有雲摘要

查看所選私有雲的全面摘要。  摘要頁面包括部署在私有雲上的 DNS 伺服器。  您可以將 DNS 轉發從本地 DNS 伺服器連接到私有雲 DNS 伺服器。  有關 DNS 轉發的詳細資訊，請參閱[配置 DNS 以瞭解本地私有雲 vCenter 的名稱解析](https://docs.azure.cloudsimple.com/on-premises-dns-setup/)。

![私有雲摘要](media/private-cloud-summary.png)

### <a name="available-actions"></a>可用動作

* [啟動 vSphere 用戶端](https://docs.azure.cloudsimple.com/vsphere-access/)。 訪問此私有雲的 vCenter。
* [購買節點](create-nodes.md)。 向此私有雲添加節點。
* [展開](expand-private-cloud.md)。 向此私有雲添加節點。
* **刷新**。 更新此頁面上的資訊。
* **刪除**。 您可以隨時刪除私有雲。 **在刪除之前，請確保已備份所有系統和資料。** 刪除私有雲將刪除所有 VM、vCenter 配置和資料。 按一下所選私有雲的摘要部分中的 **"刪除**"。 刪除後，所有私有雲資料在安全、高度合規的擦除過程中被擦除。
* [更改 vSphere 許可權](escalate-private-cloud-privileges.md)。  上報您在此私有雲上的許可權。

## <a name="private-cloud-vlanssubnets"></a>私有雲 VLANS/子網

查看所選私有雲的已定義 VLAN/子網的清單。  該清單包括創建私有雲時創建的管理 VLAN/子網。

![私有雲 - VLAN/子網](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>可用動作

* [添加 VLANS/子網](https://docs.azure.cloudsimple.com/create-vlan-subnet/)。 向此私有雲添加 VLAN/子集。

選擇 VLAN/子網以進行以下操作
* [附加防火牆表](https://docs.azure.cloudsimple.com/firewall/)。 將防火牆表附加到此私有雲。
* **編輯**
* **刪除**（僅限使用者定義的 VLAN/子網路）

## <a name="private-cloud-activity"></a>私有雲活動

查看所選私有雲的以下資訊。  活動資訊是所選私有雲所有活動的篩選清單。  此頁面顯示多達 25 個最近的活動。

* 最近的警報
* 最近的事件
* 最近的任務
* 最近的審計

![私有雲 - 活動](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>雲機架

雲機架是私有雲的構建基塊。 每個機架提供一個容量單元。 CloudSimple 在創建或擴展私有雲時，根據您的選擇自動設定雲機架。  查看雲機架的完整清單，包括每個雲分配給的私有雲。

![私有雲 - 雲機架](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere 管理網路

當前在私有雲上配置的 VMware 管理資源和虛擬機器的清單。 資訊包括軟體版本、完全限定的功能變數名稱 （FQDN） 和資源的 IP 位址。

![私有雲 - vSphere 管理網路](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>後續步驟

* [在 Azure 上取用 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 瞭解有關[私有雲](cloudsimple-private-cloud.md)的更多