---
title: 概念 - Azure Kubernetes Service (AKS) 中的安全性
description: 了解 Azure Kubernetes Service (AKS) 中的安全性，包括主要和節點的通訊、網路原則和 Kubernetes 祕密。
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 7238e6cd7ab3625e2953a4408c82802d43372256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595938"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中的應用程式和叢集的安全性概念

當您在 Azure Kubernetes Service (AKS) 中執行應用程式工作負載時，若要保護您的客戶資料，叢集的安全性是一項重要考量。 Kubernetes 包含*網路原則*和*秘密*等安全性元件。 Azure 隨後會新增網路安全性群組和協調的叢集升級等元件。 這些安全性元件可相互結合，使您的 AKS 叢集執行最新的 OS 安全性更新和 Kubernetes 版本，且具有安全的 Pod 流量和機密認證存取。

本文將介紹對 AKS 中的應用程式進行保護的核心概念：

- [主要元件安全性](#master-security)
- [節點安全性](#node-security)
- [群集升級](#cluster-upgrades)
- [網路安全](#network-security)
- [Kubernetes 秘密](#kubernetes-secrets)

## <a name="master-security"></a>主要元件安全性

在 AKS 中，Kubernetes 主要元件包含在 Microsoft 所提供的受控服務中。 每個 AKS 群集都有自己的單租戶專用 Kubernetes 主機，用於提供 API 伺服器、計畫程式等。此主機由 Microsoft 管理和維護。

預設情況下，Kubernetes API 伺服器使用公共 IP 位址和完全限定的功能變數名稱 （FQDN）。 您可以使用 Kubernetes 角色型存取控制和 Azure Active Directory 來控制對 API 伺服器的存取。 如需詳細資訊，請參閱 [Azure AD 與 AKS 的整合][aks-aad]。

## <a name="node-security"></a>節點安全性

AKS 節點是您所管理和維護的 Azure 虛擬機器。 Linux 節點使用 Moby 容器運行時運行優化的 Ubuntu 發行版本。 Windows 伺服器節點（當前在 AKS 中處於預覽狀態）運行優化的 Windows Server 2019 版本，並使用 Moby 容器運行時。 當 AKS 叢集建立或相應增加時，節點將會自動以最新的 OS 安全性更新和設定進行部署。

Azure 平臺每晚自動將作業系統安全修補程式應用於 Linux 節點。 如果 Linux OS 安全更新需要主機重新開機，則不會自動執行重新開機。 您可以手動重新開機 Linux 節點，或者一種常見的方法是使用[Kured，][kured]這是 Kubernetes 的開源重啟守護程式。 Kured 會以 [DaemonSet][aks-daemonsets] 執行，並監視每個節點，查看是否有檔案指示需重新啟動。 系統會使用相同的 [cordon 和 drain 程序](#cordon-and-drain)作為叢集升級，跨叢集管理作業系統重新啟動。

對於 Windows 伺服器節點（當前在 AKS 中預覽），Windows 更新不會自動運行和應用最新更新。 在有關 Windows 更新發佈週期和您自己的驗證過程的定期計畫下，應在 AKS 群集中的 Windows Server 節點池上執行升級。 此升級過程創建運行最新 Windows Server 映射和修補程式的節點，然後刪除較舊的節點。 有關此過程的詳細資訊，請參閱[升級 AKS 中的節點池][nodepool-upgrade]。

節點會部署至私人虛擬網路子網路中，且不會指派公用 IP 位址。 基於疑難排解和管理用途，依預設會啟用 SSH。 此 SSH 存取僅供內部 IP 位址使用。

若要防止儲存，節點應使用 Azure 受控磁碟。 就大部分的 VM 節點大小而言，這是指採用高效能 SSD 的進階磁碟。 儲存於受控磁碟上的資料會自動加密，並在 Azure 平台內待用。 若要提高備援性，這些磁碟也會安全地複寫於 Azure 資料中心內。

目前，多租用戶如有惡意的使用，AKS 或其他位置中的 Kubernetes 環境就並不完全安全。 將其他安全功能 (例如 *Pod 安全性原則*或更精細的角色型存取控制 (RBAC)) 用於節點，可以提高攻擊的難度。 不過，在執行惡意的多租用戶工作負載時若要保有真正的安全性，Hypervisor 才是您唯一可信賴的安全性層級。 Kubernetes 的安全性網域會成為整個叢集，而非個別節點。 對於這些類型的惡意多租用戶工作負載，您應使用實際隔離的叢集。 如需有關如何隔離工作負載的詳細資訊，請參閱[在 AKS 隔離叢集的最佳做法][cluster-isolation]。

## <a name="cluster-upgrades"></a>叢集升級

若要達到安全性與合規性，或是要使用最新功能，可以利用 Azure 提供用來協調 AKS 叢集和元件升級的工具。 此升級協調流程包含 Kubernetes 主要元件和代理程式元件。 您可以查看 AKS 群集[的可用庫伯奈斯版本的清單](supported-kubernetes-versions.md)。 若要開始執行升級程序，您必須指定其中一個可用版本。 然後，Azure 會安全地隔離和清空每個 AKS 節點，並執行升級。

### <a name="cordon-and-drain"></a>隔離和清空

在升級過程中，AKS 節點從群集中單獨設置，因此不會安排新的 pod。 這些節點接著會清空並升級，如下所示：

- 將新節點部署到節點池中。 此節點運行最新的作業系統映射和修補程式。
- 已標識一個現有節點進行升級。 此節點上的 Pod 在節點池中的其他節點上正常終止和計畫。
- 此現有節點將從 AKS 群集中刪除。
- 群集中的下一個節點使用相同的過程進行隔離和排空，直到所有節點在升級過程中成功替換。

如需詳細資訊，請參閱[升級 AKS 叢集][aks-upgrade-cluster]。

## <a name="network-security"></a>網路安全性

針對內部部署網路的連線和安全性，您可以將 AKS 叢集部署到現有的 Azure 虛擬網路子網路中。 這些虛擬網路可能有連回您內部部署網路的 Azure 站對站 VPN 或 ExpressRoute 連線。 Kubernetes 輸入控制器可使用私人的內部 IP 位址進行定義，使服務只能透過此內部網路連線來存取。

### <a name="azure-network-security-groups"></a>Azure 網路安全性群組

為了篩選虛擬網路中的流量，Azure 會使用網路安全性群組規則。 這些規則可定義允許或拒絕存取資源的來源和目的地 IP 範圍、連接埠和通訊協定。 創建預設規則以允許 TLS 流量到庫伯內斯 API 伺服器。 當您建立具有負載平衡器、連接埠對應或輸入路由的服務時，AKS 將會自動修改網路安全性群組，讓流量以適當方式傳輸。

## <a name="kubernetes-secrets"></a>Kubernetes 秘密

Kubernetes *祕密*可用來將敏感性資料插入 Pod 中，例如存取認證或金鑰。 首先，您必須使用 Kubernetes API 建立祕密。 您在定義 Pod 或部署時，系統可能會要求特定秘密。 對於有已排程的 Pod 需要秘密的節點，才會提供秘密，且秘密會儲存在 *tmpfs* 中，不會寫入至磁碟。 當節點上最後一個需要祕密的 Pod 遭刪除時，即會從該節點的 tmpfs 中刪除秘密。 祕密儲存在指定的命名空間內，且僅供相同命名空間中的 Pod 存取。

使用祕密可減少在 Pod 或服務 YAML 資訊清單中定義的敏感性資訊。 秘密會以 YAML 資訊清單的形式儲存在 Kubernetes API 伺服器中，供您要求。 此方法僅可供特定 Pod 存取祕密。 請注意：原始機密清單檔包含基於 base64 格式的秘密資料（有關詳細資訊，請參閱[官方文檔][secret-risks]）。 因此，此檔應視為敏感資訊，並且永遠不會承諾原始程式碼管理。

## <a name="next-steps"></a>後續步驟

若要開始保護您的 AKS 叢集，請參閱[升級 AKS 叢集][aks-upgrade-cluster]。

有關相關的最佳實踐，請參閱[AKS 中群集安全和升級的最佳做法][operator-best-practices-cluster-security]和[AKS 中 pod 安全的最佳做法][developer-best-practices-pod-security]。

如需關於 Kubernetes 及 AKS 核心概念的詳細資訊，請參閱下列文章：

- [Kubernetes / AKS 叢集和工作負載][aks-concepts-clusters-workloads]
- [Kubernetes / AKS 身分識別][aks-concepts-identity]
- [Kubernetes / AKS 虛擬網路][aks-concepts-network]
- [Kubernetes / AKS 儲存體][aks-concepts-storage]
- [Kubernetes / AKS 調整][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
