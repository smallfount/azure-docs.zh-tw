---
title: 操作員最佳做法 - Azure Kubernetes Services (AKS) 中的叢集隔離
description: 了解叢集操作員在 Azure Kubernetes Service (AKS) 中進行隔離時的最佳做法
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 228b856d5c5ffa2bfac7df12094667e02f797690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594850"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中隔離叢集的最佳做法

您在管理 Azure Kubernetes Service (AKS) 中的叢集時，往往需要隔離小組和工作負載。 AKS 可讓您彈性決定要如何執行多租用戶叢集和隔離資源。 為了讓 Kubernetes 投資發揮最大效果，請了解並實作這些多租用戶和隔離功能。

這個最佳做法文章著重於叢集操作員的隔離操作。 在本文中，您將學會如何：

> [!div class="checklist"]
> * 規劃多租用戶叢集和隔離資源
> * 在您的 AKS 叢集中使用邏輯或實體隔離

## <a name="design-clusters-for-multi-tenancy"></a>設計多租用戶的叢集

Kubernetes 提供功能讓您以邏輯方式隔離相同叢集中的小組和工作負載。 其目標應該是要提供以每個小組所需資源為範圍的最少權限數目。 Kubernetes 中的[命名空間][k8s-namespaces]會建立邏輯隔離界限。 隔離和多租戶的其他 Kubernetes 功能和注意事項包括以下方面：

* **排程**包含使用基本功能，例如資源配額和 Pod 中斷預算。 如需這些功能的詳細資訊，請參閱 [AKS 中基本排程器功能的最佳做法][aks-best-practices-scheduler]。
  * 更進階的排程器功能包括污點和容差、節點選取器，以及節點和 Pod 的親和性或反親和性。 如需這些功能的詳細資訊，請參閱 [AKS 中進階排程器功能的最佳做法][aks-best-practices-advanced-scheduler]。
* **網路**包含使用網路原則來控制流入和流出 Pod 的流量。
* **驗證和授權**包含角色型存取控制 (RBAC) 及 Azure Active Directory (AD) 整合的使用者、Pod 身分識別和 Azure Key Vault 中的祕密。 如需這些功能的詳細資訊，請參閱 [AKS 中驗證和授權的最佳做法][aks-best-practices-identity]。
* **容器**包含 Pod 安全性原則、Pod 資訊安全內容、掃描映像和執行階段的弱點。 另外還包含使用 App Armor 或 Seccomp (安全運算) 來限制容器對基礎節點的存取。

## <a name="logically-isolate-clusters"></a>以邏輯方式隔離叢集

**最佳做法指引** - 使用邏輯隔離來區隔小組和專案。 請嘗試盡量減少為了隔離小組或應用程式而部署的實體 AKS 叢集數量。

若使用邏輯隔離，單一 AKS 叢集將可用於多個工作負載、小組或環境。 Kubernetes [命名空間][k8s-namespaces]會形成工作負載和資源的邏輯隔離界限。

![AKS 中 Kubernetes 叢集的邏輯隔離](media/operator-best-practices-cluster-isolation/logical-isolation.png)

叢集邏輯分隔通常會較實體隔離的叢集提供更高的 Pod 密度。 叢集中閒置的多餘計算容量會比較少。 與 Kubernetes 叢集自動調整程式結合時，您可以相應增加或減少節點數目以符合需求。 這個自動調整的最佳做法可讓您僅執行所需的節點數目，從而將成本降到最低。

多租用戶如有惡意的使用，AKS 或其他位置中的 Kubernetes 環境就並不完全安全。 在多租戶環境中，多個租戶正在處理一個通用的共用基礎結構。 因此，如果無法信任所有租戶，則需要執行其他規劃，以避免一個租戶影響另一個租戶的安全和服務。 將其他安全功能 (例如 *Pod 安全性原則*和更精細的角色型存取控制 (RBAC)) 用於節點，可以提高攻擊的難度。 不過，在執行惡意的多租用戶工作負載時若要保有真正的安全性，Hypervisor 才是您唯一可信賴的安全性層級。 Kubernetes 的安全性網域會成為整個叢集，而非個別節點。 對於這些類型的惡意多租用戶工作負載，您應使用實際隔離的叢集。

## <a name="physically-isolate-clusters"></a>實體隔離叢集

**最佳做法指引** - 請盡量不要對每個個別的小組或應用程式部署使用實體隔離。 相反地，請使用上一節所討論的「邏輯」** 隔離。

叢集隔離的常見方法是使用實際分隔的 AKS 叢集。 在此隔離模型中，小組或工作負載會獲派自己的 AKS 叢集。 這種方法看起來往往會是隔離工作負載或小組的最簡單方式，但會增添額外的管理和財務負荷。 您現在必須維護這些多重叢集，且必須個別提供存取權和指派權限。 您也要支付所有個別節點的費用。

![AKS 中個別 Kubernetes 叢集的實體隔離](media/operator-best-practices-cluster-isolation/physical-isolation.png)

實際分隔的叢集通常有較低密度的 Pod。 由於每個小組或工作負載都會擁有自己的 AKS 叢集，該叢集往往會過度佈建計算資源。 通常，在這些節點上安排少量 pod。 節點上未使用的容量又無法供其他小組用來開發應用程式或服務。 這些多餘的資源會導致實際分隔叢集的成本增加。

## <a name="next-steps"></a>後續步驟

本文著重於叢集隔離。 如需 AKS 中叢集作業的相關詳細資訊，請參閱下列最佳作法：

* [基本的 Kubernetes 排程器功能][aks-best-practices-scheduler]
* [Kubernetes 排程器的進階功能][aks-best-practices-advanced-scheduler]
* [身份驗證和授權][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
