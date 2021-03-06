---
title: 識別適用於 Azure Machine Learning 的案例 - Team Data Science Process
description: 選取適合使用 Team Data Science Process 進行進階預測性分析的案例。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 48b51c40e5de8f10d9d1d16b02e2c70b045816b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251618"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>在 Azure 機器學習中的進階分析案例
本文概述可以運用 [Team Data Science Process (TDSP)](overview.md)來處理的各種範例資料來源和目標案例。 TDSP 提供系統化的方法，可讓小組共同建置智慧型應用程式。 此處呈現的案例將根據資料特性、來源位置和在 Azure 中的目標儲存機制，來說明資料處理工作流程中可用的選項。

最後一節介紹了用於選擇適合您的資料和目標的示例方案**的決策樹**。

下列各節均提供一個範例案例。 在每個案例中，都會列出可能的資料科學或進階分析流程，以及支援的 Azure 資源。

> [!NOTE]
> **對於所有下列案例，您必須：**
> <br/>
> 
> * [創建存儲帳戶](../../storage/common/storage-account-create.md)
>   <br/>
> * [創建 Azure 機器學習工作區](../studio/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>方案\#1：本地檔中的中小型表格資料集
![中小型本機檔案][1]

#### <a name="additional-azure-resources-none"></a>其他 Azure 資源：無
1. 登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。
1. 上傳資料集。
1. 建置從上傳的資料集開始的 Azure 機器學習實驗流程。

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>方案\#2：需要處理的本地檔的中小型資料集
![需要處理的中小型本機檔案][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>其他 Azure 資源：Azure 虛擬機器 (IPython Notebook 伺服器)
1. 建立執行 IPython Notebook 的 Azure 虛擬機器。
1. 將資料上載到 Azure 存儲容器。
1. 預處理和清理 IPython 筆記本中的資料，從 Azure 存儲容器訪問資料。
1. 將資料轉換為已清理的表格形式。
1. 將轉換的資料儲存在 Azure Blob 中。
1. 登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。
1. 使用[匯入資料][import-data]模組從 Azure Blob 讀取資料。
1. 建置從內嵌的資料集開始的 Azure 機器學習實驗流程。

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>方案\#3：面向 Azure Blob 的本地檔的大型資料集
![大型本機檔案][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>其他 Azure 資源：Azure 虛擬機器 (IPython Notebook 伺服器)
1. 建立執行 IPython Notebook 的 Azure 虛擬機器。
1. 將資料上載到 Azure 存儲容器。
1. 前置處理和清除 IPython Notebook 中的資料 (從 Azure Blob 存取資料)。
1. 如果需要，將資料轉換為已清理的表格形式。
1. 視需要瀏覽資料並建立功能。
1. 擷取中小型資料範例。
1. 將取樣資料儲存在 Azure Blob 中。
1. 登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。
1. 使用[匯入資料][import-data]模組從 Azure Blob 讀取資料。
1. 建置從內嵌的資料集開始的 Azure 機器學習實驗流程。

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>案例 \#4：本機檔案的中小型資料集，以 Azure 虛擬機器中的 SQL Server 為目標
![中小型本機檔案至 Azure 中的 SQL DB][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 資源：Azure 虛擬機器 (SQL Server / IPython Notebook 伺服器)
1. 建立執行 SQL Server + IPython Notebook 的 Azure 虛擬機器。
1. 將資料上載到 Azure 存儲容器。
1. 使用 IPython 筆記本在 Azure 存儲容器中預處理和清理資料。
1. 如果需要，將資料轉換為已清理的表格形式。
1. 將資料儲存至 VM-local 檔案 (IPython Notebook 會在 VM 上執行，本機磁碟是指 VM 磁碟機)。
1. 將資料載入執行於 Azure VM 的 SQL Server 資料庫。
   
   選項 \#1：使用 SQL Server Management Studio。
   
   * 登錄到 SQL 伺服器 VM
   * 執行 SQL Server Management Studio。
   * 建立資料庫和目標資料表。
   * 使用其中一種大量匯入方法，從 VM 本機檔案載入資料。
   
   選項 \#2：使用 IPython Notebook – 不建議用於中型和大型資料集
   
   <!-- -->    
   * 使用 ODBC 連線字串，存取 VM 上的 SQL Server。
   * 建立資料庫和目標資料表。
   * 使用其中一種大量匯入方法，從 VM 本機檔案載入資料。
1. 視需要瀏覽資料並建立功能。 不需要在資料庫表中具體化這些功能。 僅注意建立這些功能的必要查詢。
1. 若有需要，請決定資料範例大小。
1. 登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。
1. 使用[匯入資料][import-data]模組直接從 SQL Server 讀取資料。 粘貼提取欄位、創建要素和採樣資料的必要查詢（如果需要）直接在["導入資料"][import-data]查詢中。
1. 建置從內嵌的資料集開始的 Azure 機器學習實驗流程。

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>方案\#5：本地檔中的大型資料集，Azure VM 中的目標 SQL 伺服器
![大型本機檔案至 Azure 中的 SQL DB][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 資源：Azure 虛擬機器 (SQL Server / IPython Notebook 伺服器)
1. 建立執行 SQL Server 和 IPython Notebook 伺服器的 Azure 虛擬機器。
1. 將資料上載到 Azure 存儲容器。
1. (選擇性) 前置處理和清除資料。
   
    a.  前置處理和清除 IPython Notebook 中的資料 (從 Azure Blob 存取資料)。
   
    b.  如果需要，將資料轉換為已清理的表格形式。
   
    c.  將資料儲存至 VM-local 檔案 (IPython Notebook 會在 VM 上執行，本機磁碟是指 VM 磁碟機)。
1. 將資料載入執行於 Azure VM 的 SQL Server 資料庫。
   
    a.  登錄到 SQL Server VM。
   
    b.  如果資料尚未保存，請將資料檔案從 Azure 存儲容器下載到本地 VM 資料夾。
   
    c.  執行 SQL Server Management Studio。
   
    d.  建立資料庫和目標資料表。
   
    e.  使用其中一個大量匯入方法來載入資料。
   
    f.  如果需要資料表聯結，請建立索引以加速聯結。
   
   > [!NOTE]
   > 如需加快大型資料的載入速度，建議您建立分割資料表並大量平行匯入資料。 如需詳細資訊，請參閱 [平行資料匯入至 SQL 分割資料表](parallel-load-sql-partitioned-tables.md)。
   > 
   > 
1. 視需要瀏覽資料並建立功能。 不需要在資料庫表中具體化這些功能。 僅注意建立這些功能的必要查詢。
1. 若有需要，請決定資料範例大小。
1. 登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。
1. 使用[匯入資料][import-data]模組直接從 SQL Server 讀取資料。 粘貼提取欄位、創建要素和採樣資料的必要查詢（如果需要）直接在["導入資料"][import-data]查詢中。
1. 從上傳的資料集開始的簡單 Azure Machine Learning 實驗流程

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>方案\#6：本地 SQL Server 資料庫中的大型資料集，以 Azure 虛擬機器中的 SQL Server 為目標
![大型 SQL DB 內部部署至 Azure 中的 SQL DB][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 資源：Azure 虛擬機器 (SQL Server / IPython Notebook 伺服器)
1. 建立執行 SQL Server 和 IPython Notebook 伺服器的 Azure 虛擬機器。
1. 使用其中一個資料匯出方法來將資料從 SQL Server 匯出成傾印檔案。
   
   > [!NOTE]
   > 如果決定從內部內部資料庫移動所有資料，則採用備用（更快）方法將完整資料庫移動到 Azure 中的 SQL Server 實例。 略過匯出資料、建立資料庫，和將資料載入/匯入目標資料庫等步驟，並依照替代方法進行。
   > 
   > 
1. 將轉儲檔上載到 Azure 存儲容器。
1. 將資料載入執行於 Azure 虛擬機器的 SQL Server 資料庫。
   
   a.  登錄到 SQL Server VM。
   
   b.  將資料檔案從 Azure 存儲容器下載到本地 VM 資料夾。
   
   c.  執行 SQL Server Management Studio。
   
   d.  建立資料庫和目標資料表。
   
   e.  使用其中一個大量匯入方法來載入資料。
   
   f.  如果需要資料表聯結，請建立索引以加速聯結。
   
   > [!NOTE]
   > 如需加快大型資料的載入速度，請建立分割資料表並大量平行匯入資料。 如需詳細資訊，請參閱 [平行資料匯入至 SQL 分割資料表](parallel-load-sql-partitioned-tables.md)。
   > 
   > 
1. 視需要瀏覽資料並建立功能。 不需要在資料庫表中具體化這些功能。 僅注意建立這些功能的必要查詢。
1. 若有需要，請決定資料範例大小。
1. 登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。
1. 使用[匯入資料][import-data]模組直接從 SQL Server 讀取資料。 粘貼提取欄位、創建要素和採樣資料的必要查詢（如果需要）直接在["導入資料"][import-data]查詢中。
1. 從上傳的資料集開始的簡單 Azure Machine Learning 實驗流程。

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>將整個資料庫從內部部署 SQL Server 複製到 Azure SQL Database 的替代方法
![卸離本機 DB 和連結至 Azure 中的 SQL DB][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 資源：Azure 虛擬機器 (SQL Server / IPython Notebook 伺服器)
若要在您的 SQL Server VM 中複寫整個 SQL Server 資料庫，您應將資料庫從一個位置/伺服器複製到另一個位置/伺服器 (假設資料庫可以暫時設定離線)。 您可以使用 SQL 伺服器管理工作室物件資源管理器，或使用等效的 Transact-SQL 命令。

1. 在來源位置卸離資料庫。 有關詳細資訊，請參閱[分離資料庫](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx)。
1. 在 Windows 檔案總管或 Windows 命令提示字元視窗中，將已卸離的資料庫檔案和記錄檔複製到位於 Azure 中 SQL Server VM 上的目標位置。
1. 將複製檔案連結至目標 SQL Server 執行個體。 如需詳細資訊，請參閱 [Attach a Database](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx)。

[使用卸離與附加移動資料庫 (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>方案\#7：本地檔中的大資料，在 Azure HDInsight Hadoop 群集中定位 Hive 資料庫
![本機目標 Hive 中的巨量資料][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>其他 Azure 資源：Azure HDInsight Hadoop 叢集和 Azure 虛擬機器 (IPython Notebook 伺服器)
1. 建立執行 IPython Notebook 伺服器的 Azure 虛擬機器。
1. 建立 Azure HDInsight Hadoop 叢集。
1. (選擇性) 前置處理和清除資料。
   
   a.  前置處理和清除 IPython Notebook 中的資料，從 Azure 存取資料
   
       blobs.
   
   b.  如果需要，將資料轉換為已清理的表格形式。
   
   c.  將資料儲存至 VM-local 檔案 (IPython Notebook 會在 VM 上執行，本機磁碟是指 VM 磁碟機)。
1. 將資料上傳至步驟 2 中已選取 Hadoop 叢集的預設容器。
1. 將資料載入 Azure HDInsight Hadoop 叢集中的 Hive 資料庫。
   
   a.  登入 Hadoop 叢集的前端節點
   
   b.  開啟 Hadoop 命令列。
   
   c.  透過 Hadoop 命令列中的 `cd %hive_home%\bin` 命令進入 Hive 根目錄。
   
   d.  執行 Hive 查詢以建立資料庫和資料表，並將資料從 Blob 儲存體載入 Hive 資料表。
   
   > [!NOTE]
   > 如果資料太大，使用者可以建立包含分割區的 Hive 資料表 接著，使用者可以在前端節點上的 Hadoop 命令列中使用 `for` 迴圈，以將資料載入使用資料分割的 Hive 分割資料表。
   > 
   > 
1. 在 Hadoop 命令列中，視需要瀏覽資料並建立功能。 不需要在資料庫表中具體化這些功能。 僅注意建立這些功能的必要查詢。
   
   a.  登入 Hadoop 叢集的前端節點
   
   b.  開啟 Hadoop 命令列。
   
   c.  透過 Hadoop 命令列中的 `cd %hive_home%\bin` 命令進入 Hive 根目錄。
   
   d.  在 Hadoop 叢集前端節點的 Hadoop 命令列中執行 Hive 查詢，以瀏覽資料並視需要建立功能。
1. 若有需要，取樣資料以符合 Azure Machine Learning Studio 需求。
1. 登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。
1. 使用[匯入資料][import-data]模組直接從 `Hive Queries` 讀取資料。 粘貼提取欄位、創建要素和採樣資料的必要查詢（如果需要）直接在["導入資料"][import-data]查詢中。
1. 從上傳的資料集開始的簡單 Azure Machine Learning 實驗流程。

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>用於案例選擇的決策樹
---
下圖摘要說明上述案例，以及讓您連結到每個分項案例的「進階分析程序和技術」選擇。 資料處理、探索、功能工程和採樣可在一種或多種方法/環境中（在源、中間和/或目標環境中）進行，並可根據需要進行反覆運算。 圖表的目的只是說明一些可能的流程，並不提供詳盡的列舉。

![範例 DS 程序逐步解說案例][8]

### <a name="advanced-analytics-in-action-examples"></a>進階分析實務範例
如需透過公用資料集運用進階分析程序和技術的端對端 Azure Machine Learning 逐步解說，請參閱：

* [Team Data Science Process 實務：使用 SQL Server](sql-walkthrough.md)。
* [Team Data Science Process 實務：使用 HDInsight Hadoop 叢集](hive-walkthrough.md)。

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
