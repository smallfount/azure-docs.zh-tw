---
title: Azure 上的 SAP：Azure 上支援哪些 SAP 軟體
description: 說明支援在 Azure 上部署哪些 SAP 軟體
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0aaa13ff4d3331378cc17cd0cde29be43822397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460784"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Azure 部署支援哪些 SAP 軟體
本文介紹如何瞭解 Azure 部署支援哪些 SAP 軟體，以及什麼是必要的作業系統版本或 DBMS 版本。

評估當前 SAP 軟體是否受支援，Azure 中的 SAP 軟體支援哪些作業系統和 DBMS 版本，您需要訪問：

- SAP 支援說明
- SAP 產品可用性矩陣



## <a name="general-restrictions-for-sap-workload"></a>SAP 工作負載的一般限制
可用於 SAP 工作負載的 Azure IaaS 服務僅限於 x86-64 或 x64 硬體。 沒有適用于 SAP 工作負載的基於 Sparc 或 Power CPU 的優惠。 在 IBM 大型機或 AS400 等硬體體系結構專有的作業系統上運行其應用程式的客戶，或者在使用 HP-UX、Solaris 或 AIX 的作業系統時，需要將其 SAP 應用程式（包括 DBMS）更改為以下作業系統：

- x86-64 平臺的 Windows 伺服器 64 位
- X86-64 平臺的 SUSE linux 64 位
- 紅帽Linux 64Bit用於x86-64平臺
- 用於 x86-64 平臺的 Oracle Linux 64 位

與 SAP 軟體結合使用，不支援其他作業系統版本或 Linux 發行版本。 有關特定版本和案例的準確詳細資訊將記錄在文檔的後面部分。


## <a name="you-start-here"></a>從這裡開始
您的起點是 SAP[支援說明#1928533](https://launchpad.support.sap.com/#/notes/1928533)。 從上到下流覽此 SAP 說明時，將顯示支援的軟體和 VM 的幾個領域

第一節列出了 Azure VM 中 SAP 軟體普遍支援的操作版本的最低要求。 如果您沒有達到這些最低要求並運行這些作業系統的舊版本，則需要將作業系統版本升級到此類最低版本，甚至更新版本。 Azure 通常支援其中一些作業系統的舊版本是正確的。 但列出的限制或最小版本基於已執行的測試和資格，不會進一步擴展。 


> [!NOTE]
>有一些特定的 VM 類型、HANA 大型實例或 SAP 工作負載需要更新的作業系統版本。 在檔中將提及類似案例。 類似案例清楚地記錄在 SAP 說明或其他 SAP 出版物中。

以下部分列出了受支援的版本以及受支援的 SAP 內核支援的通用 SAP 平臺。 它列出了 NetWeaver/ABAP 或 JAVA 堆疊受支援，需要最少的內核版本。 Azure 上支援較新的 ABAP 堆疊，但不需要最少的內核版本，因為 Azure 的更改是從開發較新的堆疊開始時實現的

您需要檢查：

- 您正在運行的 SAP 應用程式是否包含在所述的最低版本中。 如果沒有，則需要定義新的目標版本，簽入 SAP 產品可用性矩陣、新目標版本支援哪些作業系統生成和 DBMS 組合。 因此，您可以選擇正確的作業系統版本和 DBMS 版本
- 是否需要在遷移到 Azure 時更新 SAP 內核
- 是否需要更新 SAP 支援包。 特別是基礎支援包，在需要遷移到較新的 DBMS 版本的情況下可能需要這些包


下一節將介紹 SAP 支援的 Windows 和 Linux Azure 上支援的其他 SAP 產品和 DBMS 版本的詳細資訊。 

> [!NOTE]
> 不同的 DBMS 的最小版本是經過精心挑選的，可能並不總是反映 DBMS 發佈的整個 DBMS 版本，而不同的 DBMS 供應商通常支援 Azure。 許多 SAP 工作負載相關注意事項都考慮在定義這些最小版本。 無需努力測試和鑒定較舊的 DBMS 版本。 

> [!NOTE]
> 列出的最小版本表示舊版本的作業系統和資料庫版本。 我們強烈建議使用最新的作業系統版本和資料庫版本。 在許多情況下，較新的作業系統和資料庫版本考慮了在公共雲中運行的使用方式，並調整了代碼以優化在公共雲或更具體 Azure 中運行

## <a name="oracle-dbms-support"></a>Oracle DBMS 支援
作業系統、Oracle DBMS 版本和 Azure 上支援的 Oracle 功能特別列在[SAP 支援說明#2039619](https://launchpad.support.sap.com/#/notes/2039619)。 注釋的精髓可以概括如下：

- 通過 NetWeaver 認證的 Azure VM 上支援的最小 Oracle 版本是 Oracle 11g 版本 2 修補程式集 3 （11.2.0.4）
- 作為客體作業系統，只有 Windows 和 Oracle Linux 才符合條件。 說明中列出了作業系統和相關最小 DBMS 版本的確切版本
- Oracle Linux 的支援也擴展到 Oracle DBMS 用戶端。 這意味著所有 SAP 元件（如 ABAP 或 JAVA 堆疊的對話方塊實例）都需要在 Oracle Linux 上運行。 只有此類 SAP 系統中無法連接到 Oracle DBMS 的 SAP 元件才允許運行不同的 Linux 作業系統
- 不支援 Oracle RAC 
- 某些案例支援 Oracle ASM。 詳細資訊列在注釋中
- 只有使用 Windows 客體作業系統運行的應用程式伺服器才支援非 Unicode SAP 系統。 DBMS 的客體作業系統可以是 Oracle Linux 或 Windows。 檢查 SAP 產品可用性矩陣 （PAM） 時，此限制的原因顯而易見。 對於 Oracle Linux，SAP 從未發佈非 Unicode SAP 內核

瞭解目標 Azure 基礎結構支援的 DBMS 版本，您需要檢查 SAP 產品可用性矩陣，瞭解您打算運行的 SAP 產品版本是否支援所需的作業系統版本和 DBMS。 


## <a name="sap-hana-support"></a>SAP HANA 支援
在 Azure 中有兩個服務，可用於運行 HANA 資料庫：

- Azure 虛擬機器
- [HANA 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

對於運行 SAP HANA，SAP 比運行 NetWeaver 或其他 SAP 應用程式和 DBMS 相比，基礎架構需要滿足更多、更強的條件。 因此，少數 Azure VM 有資格運行 SAP HANA DBMS。 SAP HANA 支援的支援 Azure 基礎結構的清單可以在所謂的[SAP HANA 硬體目錄中](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)找到。 

> [!NOTE]
> 以字母"S"開頭的單位是[HANA 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)單位。 

> [!NOTE]
> SAP 沒有特定的認證依賴于 SAP HANA 主要版本。 與普遍看法相反，該欄目在[HANA認證IaaS平臺](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中的**認證方案**，該列**沒有聲明HANA主要或次要版本認證**。 您需要假設，只要 HANA 1.0 版本也支援特定單元的經過認證的作業系統版本，就可以為 HANA 1.0 和 HANA 2.0 列出的所有設備。 

對於 SAP HANA 的使用，可能應用與一般 NetWeaver 案例不同的最低作業系統版本。 您需要單獨簽出每個單元的支援作業系統，因為這些作業系統可能有所不同。 通過按一下每個單元來執行此操作。 將顯示更多詳細資訊。 列出的詳細資訊之一是此特定單元支援的不同作業系統。

> [!NOTE]
> 與 Azure VM 相比，Azure HANA 大型實例單元與受支援的作業系統相比更為嚴格。 另一方面，Azure VM 可能會強制使用較新的操作版本作為最小版本。 對於一些需要更改 Linux 內核的大型 VM 單元尤其如此

瞭解 Azure 基礎結構受支援的作業系統，需要檢查[SAP 支援說明#2235581](https://launchpad.support.sap.com/#/notes/2235581)有關所針對的 Azure 單元支援的確切 SAP HANA 版本和修補程式級別。 

> [!IMPORTANT]
> 檢查支援的確切 SAP HANA 版本和修補程式級別的步驟非常重要。 在許多情況下，對特定作業系統版本的支援取決於 SAP HANA 可執行檔的特定修補程式級別。

如果您知道可以在目標 Azure 基礎結構上運行的特定 HANA 版本，因此需要簽入 SAP 產品可用性矩陣，以瞭解支援篩選出來的 HANA 版本的 SAP 產品版本是否存在限制


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>認證的 Azure VM 和 HANA 大型實例單元和業務事務輸送量
除了評估支援的作業系統版本、DBMS 版本和 Azure 基礎結構單元的從屬支援 SAP 軟體版本外，還需要按業務事務輸送量對這些單元進行限定，該輸送量在單位"SAP"中表示：Sap。 所有 SAP 大小取決於 SAPS 計算。 評估現有的 SAP 系統時，您通常可以在基礎結構供應商的説明下計算單位的 SAPS。 對於 DBMS 層以及應用程式層。 在創建新功能的其他情況下，使用 SAP 進行大小調整練習可以顯示應用程式層和 DBMS 層所需的 SAPS 編號。 作為基礎設施供應商，Microsoft 有義務提供 NetWeaver 和/或 HANA 認證的不同單元的 SAP 輸送量特徵。

對於 Azure VM，這些 SAPS 輸送量編號記錄在[SAP 支援說明#1928533](https://launchpad.support.sap.com/#/notes/1928533)。 對於 Azure HANA 大型實例單元，SAPS 輸送量編號記錄在[SAP 支援說明#2316233](https://launchpad.support.sap.com/#/notes/2316233)

查看[SAP 支援說明#1928533，](https://launchpad.support.sap.com/#/notes/1928533)以下注釋適用：

- **對於 M 系列 Azure VM 和 Mv2 系列 Azure VM，應用的最小作業系統版本與其他 Azure VM 類型不同**。 對更新的作業系統版本的要求基於不同作業系統供應商在其作業系統版本中必須提供的更改，以便啟用在特定 Azure VM 類型上運行的作業系統，或者優化性能和這些 VM 類型上的 SAP 工作負載的輸送量
- 有兩個表指定不同的 VM 類型。 第二個表指定僅支援 Azure 標準存儲的 Azure VM 類型的 SAPS 輸送量。 不支援在注釋的第二個表中指定的單元上的 DBMS 部署


## <a name="other-sap-products-supported-on-azure"></a>Azure 上支援的其他 SAP 產品
通常，假設在 Azure 等超大規模雲的狀態下，大多數 SAP 軟體應在 Azure 中運行，而不會出現功能問題。 然而，與私有雲視覺化相反，SAP 仍然明確表達了對不同 hyerpscale 雲供應商的不同 SAP 產品的支援。 因此，有不同的 SAP 支援說明，指示對 Azure 的不同 SAP 產品的支援。 

對於業務物件 BI 平臺[，SAP 支援說明#2145537](https://launchpad.support.sap.com/#/notes/2145537)提供了 Azure 上支援的 SAP 業務物件產品的清單。 如果有關軟體版本和作業系統版本的元件或組合的問題似乎未列出或支援，並且比列出的最小版本更新，則需要針對您詢問的元件打開 SAP 支援請求支援。

對於業務物件資料服務[，SAP 支援說明#22288344](https://launchpad.support.sap.com/#/notes/2288344)解釋了在 Azure 上運行的 SAP 資料服務的最低支援。 

> [!NOTE]
> 如 SAP 支援說明所示，您需要簽入 SAP PAM 以確定要在 Azure 上支援的正確支援包級別

Azure 庫伯奈斯服務 （AKS） 中的 SAP Datahub/Vora 支援在[SAP 支援說明#2464722](https://launchpad.support.sap.com/#/notes/2464722)

[SAP 支援說明](https://launchpad.support.sap.com/#/notes/2451795)#2451795介紹了對 SAP BPC 10.1 SP08 的支援

[在 Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud)中詳細介紹了對 Azure 上的 SAP Hybris 商務平臺 5.x 和 6.x 的支援


## <a name="next-steps"></a>後續步驟
閱讀[SAP NetWeaver Azure 虛擬機器規劃和實現的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)後續步驟

