---
title: 如何使用適用於 VM 的 Azure 監視器來繪製效能圖表
description: 效能是適用於 VM 的 Azure 監視器的一項功能，可自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。 本文提供如何將它用於各種案例的詳細資料。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: a50ba39777e6a9d3d609e584c0c7d872f2a65f35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283713"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>如何使用適用於 VM 的 Azure 監視器來繪製效能圖表

適用於 VM 的 Azure 監視器包含一組以關鍵效能指標 (KPI) 為目標的效能圖表，可協助您判斷虛擬機器的執行狀況。 那些圖表會顯示一段時間的資源使用率，讓您能夠找出瓶頸、異常狀況，或切換至列出每部機器的檢視方塊，以根據所選計量來檢視資源使用率。 雖然在處理效能時要考量許多元素，不過適用於 VM 的 Azure 監視器著重於監視與處理器、記憶體、網路介面卡和磁碟使用率相關的主要作業系統指標。 效能可彌補健康狀態監視功能，並有助於揭示可能導致系統元件故障的問題、支援調整和最佳化以實現效率，或支援容量規劃。  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Azure 監視器的多 VM 檢視方塊

在 Azure 監視器中，性能功能提供了在訂閱或環境中跨工作組部署的所有受監視 VM 的視圖。 若要從 Azure 監視器存取，請執行下列步驟。 

1. 在 Azure 門戶中，選擇 **"監視器**"。 
2. 在 **"解決方案**"部分中選擇**虛擬機器**。
3. 選取 [效能]**** 索引標籤。

![VM Insights 效能 N 大排行榜檢視](media/vminsights-performance/vminsights-performance-aggview-01.png)

在 [N 大排行榜]**** 索引標籤上，如果您具有多個 Log Analytics 工作區，請從頁面頂端的 [工作區]**** 選取器選擇已可處理解決方案的工作區。 [群組]**** 選取器會傳回與選取的工作區有關的訂用帳戶、資源群組、[電腦群組](../platform/computer-groups.md)與電腦的虛擬機器擴展集，供您進一步篩選此頁面上的圖表所顯示的結果，和其他頁面上的結果。 您的選取只會套用至「效能」功能，而不會擴及「健康情況」或「對應」。  

根據預設，圖表會顯示過去 24 小時。 使用 [TimeRange]**** 選取器，您可以查詢最多 30 天的歷程記錄時間範圍，以顯示過去的效能狀況。

頁面上顯示的五個容量使用率圖表包括：

* CPU 使用率 % - 顯示平均處理器使用率最高的前五部機器 
* 可用記憶體 - 顯示平均可用記憶體數量最低的前五部機器 
* 邏輯磁碟空間使用量 % - 顯示所有磁碟區中平均磁碟空間使用量 % 最高的前五部機器 
* 位元組傳送率 - 顯示已傳送位元組平均最高的前五部機器 
* 位元組接收速率 - 顯示接收位元組平均值最高的前五台電腦 

按一下五個圖表中任意一個右上角的針腳圖示，將所選圖表固定到上次查看的 Azure 儀表板。  在儀表板中，您可以調整圖表的大小和重新置放。 從儀表板中選擇圖表將重定向到 VM 的 Azure 監視器，並載入正確的範圍和視圖。  

按一下五個圖表中的任何一個上的引腳圖示左側的圖示將打開 **"前 N 清單"** 視圖。  此處，您可以在清單檢視中查看個別 VM 的該效能計量的資源使用率，以及哪一部機器趨勢最高。  

![所選效能計量的 N 大排行榜檢視](media/vminsights-performance/vminsights-performance-topnlist-01.png)

按一下虛擬機器時，"**屬性"** 窗格將展開右側，以顯示所選項目的屬性，例如作業系統報告的系統資訊、Azure VM 的屬性等。按一下 **"快速連結"** 部分下的一個選項將直接從所選 VM 重定向到該功能。  

![虛擬機器屬性窗格](./media/vminsights-performance/vminsights-properties-pane-01.png)

切換到 [彙總圖表]**** 索引標籤可檢視依據平均或百分位數量值所篩選的效能計量。  

![VM Insights 效能彙總檢視](./media/vminsights-performance/vminsights-performance-aggview-02.png)

提供下列容量使用率圖表：

* CPU 使用率 % - 預設顯示的平均值和前 95 個百分位數 
* 可用記憶體 - 顯示平均值、前 5 位和第 10 個百分位數的預設值 
* 邏輯磁碟空間使用量 % - 預設顯示平均和前 95 個百分位數 
* 位元組傳送率 - 預設顯示平均已傳送位元組 
* 位元組接收率 - 預設顯示平均已接收位元組

您也可以在百分位數選取器中選取 [Avg]****、[Min]****、[Max]****、[50th]****、[90th]**** 和 [95th]****，來變更時間範圍內的圖表細微度。

要查看單個 VM 在清單視圖中的資源利用率，並查看使用率最高的電腦的趨勢，請選擇 **"前 N 清單**"選項卡。 **"前 N 清單"** 頁顯示按公制*CPU 利用率 %* 的 95 個百分位數使用最多的前 20 台電腦排序。  您可以選取 [載入更多]**** 來檢視更多機器，而且其結果會展開以顯示前 500 部機器。 

>[!NOTE]
>清單無法一次顯示超過 500 部機器。  
>

![N 大排行榜頁面範例](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

若要篩選清單中特定虛擬機器的結果，請在 [依名稱搜尋]**** 文字方塊中輸入其電腦名稱。  

如果您想要檢視不同效能計量的使用率，請從 [計量]**** 下拉式清單中選取 [可用記憶體]****、[邏輯磁碟空間使用量 %]****、[網路已接收位元組/s]**** 或 [網路已傳送位元組/s]**** 和清單更新，以顯示該計量範圍的使用率。  

從清單中選取虛擬機器會開啟頁面右側的 [屬性]**** 面板，您可以在此處選取 [效能詳細資料]****。  [虛擬機器詳細資料]**** 頁面隨即開啟，而且範圍限定在該 VM，類似於直接從 Azure VM 存取 VM Insights 效能時的體驗。  

## <a name="view-performance-directly-from-an-azure-vm"></a>直接從 Azure VM 檢視效能

要直接從虛擬機器訪問，執行以下步驟。

1. 在 Azure 入口網站中，選取 [虛擬機器]****。 
2. 從清單中，選擇 VM，並在 **"監視**"部分選擇 **"見解**"。  
3. 選取 [效能]**** 索引標籤。 

此頁面不僅包含效能使用率圖表，也是一個表格，可顯示已找到的每個邏輯磁碟、其容量、使用率，以及每個量值的總平均。  

提供下列容量使用率圖表：

* CPU 使用率 % - 預設顯示的平均值和前 95 個百分位數 
* 可用記憶體 - 顯示平均值、前 5 位和第 10 個百分位數的預設值 
* 邏輯磁碟空間使用量 % - 預設顯示平均和前 95 個百分位數 
* 邏輯磁碟 IOPS - 預設顯示平均和前 95 個百分位數
* 邏輯磁碟 MB/s - 預設顯示平均和前 95 個百分位數
* 最大邏輯磁碟使用量 - 預設顯示平均和前 95 個百分位數
* 位元組傳送率 - 預設顯示平均已傳送位元組 
* 位元組接收率 - 預設顯示平均已接收位元組

按一下任一圖表右上角的針腳圖示，將所選圖表固定到您查看的最後一個 Azure 儀表板。 在儀表板中，您可以調整圖表的大小和重新置放。 從儀表板中選擇圖表會將您重定向到 VM 的 Azure 監視器，並載入 VM 的性能詳細資訊視圖。  

![直接從 VM 檢視查看 VM insights 效能](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>直接從 Azure 虛擬機器縮放集中查看性能

要直接從 Azure 虛擬機器縮放集進行訪問，可以執行以下步驟。

1. 在 Azure 門戶中，選擇**虛擬機器縮放集**。
2. 從清單中選擇 VM，並在 **"監視**"部分選擇 **"見解**"以查看 **"性能"** 選項卡。

此頁載入 Azure 監視器效能檢視，範圍為所選比例集。 這使您能夠查看跨監視指標集的比例集中的 Top N 實例，查看整個比例集的聚合性能，並在比例集內查看各個實例中所選指標的趨勢。 通過從清單視圖中選擇實例，可以載入實例的映射或導航到該實例的詳細效能檢視。

按一下任一圖表右上角的針腳圖示，將所選圖表固定到您查看的最後一個 Azure 儀表板。 在儀表板中，您可以調整圖表的大小和重新置放。 從儀表板中選擇圖表會將您重定向到 VM 的 Azure 監視器，並載入 VM 的性能詳細資訊視圖。  

![VM 洞察 性能直接從虛擬機器縮放集視圖](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>您還可以從規模集的實例視圖中訪問特定實例的詳細效能檢視。 導航到 **"設置"** 部分下的**實例**，然後選擇 **"見解**"。



## <a name="next-steps"></a>後續步驟

- 瞭解如何使用 Azure 監視器中隨附的 VM[活頁簿](vminsights-workbooks.md)來進一步分析性能和網路指標。  

- 要瞭解已發現的應用程式依賴項，請參閱[查看 VM 映射的 Azure 監視器](vminsights-maps.md)。
