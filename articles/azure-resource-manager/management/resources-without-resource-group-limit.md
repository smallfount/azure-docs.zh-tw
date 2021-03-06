---
title: 沒有 800 計數限制的資源
description: 列出資源組中可以具有 800 多個實例的 Azure 資源類型。
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 735cad0bfa936c41f603e42bdb9be77a1562cc1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937942"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>資源不限於每個資源組 800 個實例

預設情況下，每個資源組中最多可以部署 800 個資源類型的實例。 但是，某些資源類型不受 800 實例限制的約束。 本文列出了資源組中可以具有 800 多個實例的 Azure 資源類型。 所有其他資源類型限制為 800 個實例。

對於某些資源類型，您需要聯繫支援部門以刪除 800 實例限制。 本文將記錄這些資源類型。


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrations/customerSubscriptions
* registrations/products
* 驗證金鑰

## <a name="microsoftbotservice"></a>Microsoft.BotService

* 機器人服務 - 預設情況下，限制為 800 個實例。 可以通過聯繫支援部門來提高這一限制。

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* images
* snapshots
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* 註冊表/生成任務/清單源存儲庫屬性
* registries/buildTasks/steps
* 註冊表/生成任務/步驟/清單生成參數
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* 伺服器組
* servers
* 伺服器v2

## <a name="microsoftenterpriseknowledgegraph"></a>微軟.企業知識圖

* 服務

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* 命名空間

## <a name="microsoftexperimentation"></a>微軟.實驗

* 實驗工作

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* 自動管理Vm配置設定檔
* 配置設定檔分配
* guestConfigurationAssignments
* software
* 軟體更新設定檔
* 軟體更新

## <a name="microsoftinsights"></a>Microsoft.Insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* 淨應用帳戶
* 淨應用帳戶/容量池
* 淨應用帳戶/容量池/卷
* 淨應用帳戶/容量池/卷/裝載目標
* 淨應用帳戶/容量池/卷/快照

## <a name="microsoftnetwork"></a>Microsoft.Network

* 應用程式閘道Web應用程式防火牆策略
* applicationSecurityGroups
* 堡壘主機
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* 私有 Dns 區
* 私有 Dns 區域/A
* 私有 Dns 區域/AAA
* 私有 Dns 區域/CNAME
* 專用 Dns 區域/MX
* 專用 Dns 區域/PTR
* 私有 Dns 區域/SOA
* 專用 Dns 區/SRV
* 專用 Dns 區域/TXT
* 私有 Dns 區域/所有
* 專用 Dns 區域/虛擬網路連結
* 私有終結點
* privateLinkServices
* 公共 IP 位址 - 預設情況下，限制為 800 個實例。 可以通過聯繫支援部門來提高這一限制。
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* 工作區集合 - 預設情況下，限制為 800 個實例。 可以通過聯繫支援部門來提高這一限制。

## <a name="microsoftrelay"></a>Microsoft.Relay

* 命名空間

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* 命名空間

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* 應用程式所需
* containerGroups
* gateways
* networks
* 密碼
* 磁碟區

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>後續步驟

有關配額和限制的完整清單，請參閱[Azure 訂閱和服務限制、配額和約束](azure-subscription-service-limits.md)。
