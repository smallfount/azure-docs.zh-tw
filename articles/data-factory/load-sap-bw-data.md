---
title: 載入 SAP 業務倉庫的資料
description: 使用 Azure 資料工廠從 SAP 業務倉庫 （BW） 複製資料
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 971871c28bd1b38b134c04b0334fbe99d1d655c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440174"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>使用 Azure 資料工廠從 SAP 業務倉庫複製資料

本文演示如何使用 Azure 資料工廠通過打開中心將資料從 SAP 業務倉庫 （BW） 複製到 Azure 資料湖存儲 Gen2。 您可以使用類似的過程將資料複製到其他[受支援的接收器資料存儲](copy-activity-overview.md#supported-data-stores-and-formats)。

> [!TIP]
> 有關從 SAP BW 複製資料的一般資訊，包括 SAP BW 開放中心集成和增量提取流，請參閱[通過使用 Azure 資料工廠通過開放中心複製 SAP 業務倉庫的資料](connector-sap-business-warehouse-open-hub.md)。

## <a name="prerequisites"></a>Prerequisites

- **Azure 資料工廠**：如果沒有，請按照步驟[創建資料工廠](quickstart-create-data-factory-portal.md#create-a-data-factory)。

- **具有目標型別"資料庫表"的 SAP BW 開放集線器目標 （OHD）：** 要創建 OHD 或檢查 OHD 是否正確配置用於資料工廠集成，請參閱本文的[SAP BW 開放集線器目標配置](#sap-bw-open-hub-destination-configurations)部分。

- **SAP BW 使用者需要以下許可權**：

  - 對遠端函式呼叫 （RFC） 和 SAP BW 的授權。
  - 對**S_SDSAUTH**授權物件的"執行"活動的許可權。

- **具有 SAP .NET 連接器 3.0[的自託管集成運行時 （IR）。](concepts-integration-runtime.md#self-hosted-integration-runtime) ** 按照以下設置步驟操作：

  1. 安裝並註冊自託管的集成運行時、版本 3.13 或更高版本。 （本文稍後將介紹此過程。

  2. 從 SAP 網站下載[Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html)的 64 位 SAP 連接器，並將其安裝到與自託管 IR 相同的電腦上。 在安裝過程中，請確保在 **"可選設置步驟"** 對話方塊中選擇**將程式集安裝到 GAC，** 如下圖所示：

     ![設置 SAP .NET 連接器對話方塊](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>從 SAP BW 開放中心執行完整副本

在 Azure 入口網站，移至您的資料處理站。 選擇 **"作者&監視器**"以在單獨的選項卡中打開資料工廠 UI。

1. 在"**讓我們開始"** 頁上，選擇 **"複製資料**"以打開"複製資料"工具。

2. 在 **"屬性"** 頁上，指定**任務名稱**，然後選擇 **"下一步**"。

3. 在 **"來源資料存儲"** 頁上，選擇 **"創建新連接**"。 從連接器庫中選擇**SAP BW 打開集線器**，然後選擇 **"繼續**"。 要篩選連接器，可以在搜索框中鍵入**SAP。**

4. 在 **"指定 SAP BW 打開集線器連接頁上**"，按照以下步驟創建新連接。

   ![創建 SAP BW 開放中心連結服務頁面](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. 從 **"通過集成運行時連接**"清單中，選擇現有的自託管 IR。 或者，如果您還沒有，請選擇創建一個。

      要創建新的自託管 IR，請選擇 **"新建**"，然後選擇 **"自託管**"。 輸入**名稱**，然後選擇 **"下一步**"。 選擇 **"快速設置**"以安裝在當前電腦上，或按照提供的**手動設置**步驟進行操作。

      如[先決條件](#prerequisites)中所述，請確保在運行自託管 IR 的同一台電腦上安裝了 Microsoft .NET 3.0 的 SAP 連接器。

   2. 填寫 SAP BW**伺服器名稱**、**系統號**、**用戶端 ID、****語言**（如果**EN**以外的）、**使用者名**和**密碼**。

   3. 選擇 **"測試連接**"以驗證設置，然後選擇 **"完成**"。

   4. 將創建新連接。 選取 [下一步]****。

5. 在 **"選擇打開中心目標"** 頁上，流覽 SAP BW 中可用的打開中心目標。 選擇 OHD 以從中複製資料，然後選擇 **"下一步**"。

   ![選擇 SAP BW 開放集線器目標表](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. 如果需要篩選器，請指定篩選器。 如果您的 OHD 僅包含單個資料傳輸過程 （DTP） 執行中的資料，並且具有單個請求 ID，或者您確定 DTP 已完成，並且想要複製資料，請清除 **"排除上次請求**"核取方塊。

   在本文的[SAP BW 開放集線器目標配置](#sap-bw-open-hub-destination-configurations)部分中瞭解有關這些設置的更多內容。 選擇 **"驗證"** 以仔細檢查將返回的資料。 然後選擇 **"下一步**"。

   ![配置 SAP BW 打開集線器篩選器](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. 在 **"目標資料存儲"** 頁上，選擇 **"創建新連接** > **Azure 資料湖存儲第 2** > 代**繼續**"。

8. 在 **"指定 Azure 資料湖存儲連接"** 頁上，按照以下步驟創建連接。

   ![創建 ADLS Gen2 連結服務頁面](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. 從 **"名稱**"下拉清單中選擇支援資料存儲湖存儲 Gen2 的帳戶。
   2. 選取 [完成]**** 以建立連線。 然後選擇 **"下一步**"。

9. 在 **"選擇輸出檔案或資料夾**"頁上，輸入**從 openhub 的副本**作為輸出檔案夾名稱。 然後選擇 **"下一步**"。

   ![選擇輸出檔案夾頁面](media/load-sap-bw-data/choose-output-folder.png)

10. 在 **"檔案格式設置"** 頁上，選擇 **"下一步**"以使用預設設置。

    ![指定接收器格式頁](media/load-sap-bw-data/specify-sink-format.png)

11. 在 **"設置"** 頁上，展開 **"性能設置**"。 輸入**複製並行度**的值，例如從 SAP BW 並行載入的 5。 然後選擇 **"下一步**"。

    ![配置複製設置](media/load-sap-bw-data/configure-copy-settings.png)

12. 在 [摘要]**** 頁面上檢閱設定。 然後選擇 **"下一步**"。

13. 在 **"部署"** 頁上，選擇 **"監視器"** 以監視管道。

    ![部署頁面](media/load-sap-bw-data/deployment.png)

14. 請注意，頁面左側的 **"監視器"** 選項卡將自動選中。 "**操作"** 列包括用於查看活動運行詳細資訊和重新運行管道的連結。

    ![管道監控視圖](media/load-sap-bw-data/pipeline-monitoring.png)

15. 要查看與管道運行關聯的活動運行，請在 **"操作"** 列中選擇 **"查看活動運行**"。 管線中只有一個活動 (複製活動)，所以您只會看到一個項目。 要切換回管道運行視圖，請選擇頂部的**管道**連結。 選取 [重新整理]**** 可重新整理清單。

    ![活動監控螢幕](media/load-sap-bw-data/activity-monitoring.png)

16. 要監視每個複製活動的執行詳細資訊，請選擇 **"詳細資訊"** 連結，該連結是活動監控視圖中 **"操作"下方的**眼鏡圖示。 可用詳細資訊包括從源複製到接收器的資料卷、資料輸送量、執行步驟和持續時間以及使用的配置。

    ![活動監控詳細資訊](media/load-sap-bw-data/activity-monitoring-details.png)

17. 要查看**最大請求 ID，** 請返回活動監視視圖，然後選擇"**操作**下的**輸出**"。

    ![活動輸出螢幕](media/load-sap-bw-data/activity-output.png)

    ![活動輸出詳細資訊視圖](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>來自 SAP BW 開放中心的增量副本

> [!TIP]
> 請參閱[SAP BW 開放集線器連接器增量提取流](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow)，瞭解資料工廠中的 SAP BW 開放集線器連接器如何從 SAP BW 複製增量資料。 本文還可以説明您瞭解基本連接器配置。

現在，讓我們繼續配置來自 SAP BW 開放中心的增量副本。

增量副本使用基於**請求 ID**的"高水位線"機制。 該 ID 由 DTP 在 SAP BW 開放中心目標中自動生成。 下圖顯示了此工作流：

![增量複製工作流流程圖](media/load-sap-bw-data/incremental-copy-workflow.png)

在資料工廠 **"讓我們開始"** 頁上，選擇 **"從範本創建管道**以使用內置範本"。

1. 搜索**SAP BW**以查找並選擇**從 SAP BW 到 Azure 資料存儲第 2 代範本的增量副本**。 此範本將資料複製到 Azure 資料存儲第 2 代。 您可以使用類似的工作流複製到其他接收器類型。

2. 在範本的主頁上，選擇或創建以下三個連接，然後在視窗右下角選擇 **"使用此範本**"。

   - **Azure Blob 存儲**：在本演練中，我們使用 Azure Blob 存儲來存儲高浮水印，這是*最大複製的請求 ID。*
   - **SAP BW 開放中心**：這是從中複製資料的源。 有關詳細配置，請參閱前面的全拷貝演練。
   - **Azure 資料存儲第 2 代**：這是將資料複製到的接收器。 有關詳細配置，請參閱前面的全拷貝演練。

   ![來自 SAP BW 範本的增量副本](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. 此範本生成一個管道，其中涉及以下三個活動，並使它們在成功時連結：*查找*、*複製資料和* *Web*。

   轉到管道**參數**選項卡。您將看到需要提供的所有配置。

   ![來自 SAP BW 配置的增量副本](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHub 目標名稱**：指定打開中心表名以從中複製資料。

   - **Data_Destination_Container**： 指定要將資料複製到的目標 Azure 資料存儲湖存儲 Gen2 容器。 如果容器不存在，資料工廠複製活動會在執行期間創建一個。
  
   - **Data_Destination_Directory**： 指定 Azure 資料湖存儲 Gen2 容器下的資料夾路徑以將資料複製到 。 如果路徑不存在，資料工廠複製活動將在執行期間創建路徑。
  
   - **高浮水印Blob容器**：指定容器以存儲高水位線值。

   - **高浮水印BlobDirectory：** 指定容器下的資料夾路徑以存儲高水位線值。

   - **高浮水印Blob名稱**：指定 blob 名稱以存儲高浮水印值，如`requestIdCache.txt`。 在 Blob 存儲中，轉到高水標記 Blob 容器_高水標記BlobDirectory_高水標記BlobName的相應路徑，如*容器/路徑/請求IdCache.txt*。 創建包含內容 0 的 Blob。

      ![Blob 內容](media/load-sap-bw-data/blob.png)

   - **邏輯AppURL：** 在此範本中，我們使用 WebActivity 調用 Azure 邏輯應用來設置 Blob 存儲中的高水位線值。 或者，您可以使用 Azure SQL 資料庫來存儲它。 使用預存程序活動更新值。

      必須先創建邏輯應用，如下圖所示。 然後，粘貼在**HTTP POST URL**中。

      ![邏輯應用配置](media/load-sap-bw-data/logic-app-config.png)

      1. 移至 Azure 入口網站。 選擇新的**邏輯應用**服務。 選擇 **#空白邏輯應用**轉到**邏輯應用設計器**。

      2. 創建"**何時收到 HTTP 要求"的**觸發器。 指定 HTTP 要求正文，如下所示：

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. 添加**創建 blob**操作。 對於**資料夾路徑**和**Blob 名稱**，請使用之前在*高水標記 Blob 容器_高水標記BlobDirectory*和*高水標記Blob名稱*中配置的值。

      4. 選取 [儲存]****。 然後，複製 HTTP **POST URL**的值以在資料工廠管道中使用。

4. 提供資料工廠管道參數後，選擇 **"調試** > **完成"** 以調用運行以驗證配置。 或者，選擇 **"發佈"** 以發佈所有更改，然後選擇 **"添加觸發器**"以執行運行。

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW 開放集線器目標配置

本節介紹 SAP BW 端的配置，以便使用資料工廠中的 SAP BW 開放集線器連接器來複製資料。

### <a name="configure-delta-extraction-in-sap-bw"></a>在 SAP BW 中配置增量提取

如果需要歷史副本和增量副本或僅增量複製，請配置 SAP BW 中的增量提取。

1. 創建打開中心目標。 您可以在 SAP 事務 RSA1 中創建 OHD，該進程會自動創建所需的轉換和資料傳輸過程。 套用下列設定：

   - **物件類型**：您可以使用任何物件類型。 在這裡，我們使用**InfoCube**作為示例。
   - **目標型別**：選擇**資料庫表**。
   - **表的鍵**：選擇**技術金鑰**。
   - **提取**：選擇 **"保留資料並將記錄插入到表中**"。

   ![創建 SAP BW OHD 增量提取對話方塊](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![創建 SAP BW OHD delta2 提取對話方塊](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   您可以增加 DTP 並行運行 SAP 工作流程的數量：

   ![創建-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. 在流程鏈中安排 DTP。

   僅當必要的行尚未壓縮時，多維資料集的增量 DTP 才起作用。 確保 BW 多維資料集壓縮在 DTP 到打開中心表之前未運行。 最簡單的方法是將 DTP 集成到現有流程鏈中。 在下面的示例中，DTP（到 OHD）插入到*調整*（聚合匯總）和*折疊*（多維資料集壓縮）步驟之間的流程鏈中。

   ![創建 SAP BW 流程流程圖](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>在 SAP BW 中配置完全提取

除了增量提取之外，您可能還希望完全提取同一 SAP BW 資訊供應商。 如果要執行完整複製而不是增量，或者要[重新同步增量提取](#resync-delta-extraction)，則通常適用這一點。

對於同一 OHD，不能有多個 DTP。 因此，在增量提取之前，必須創建額外的 OHD。

![創建全 SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

對於滿載 OHD，請選擇與增量提取不同的選項：

- 在 OHD 中：將**提取**選項設置為**刪除資料和插入記錄**。 否則，當您在 BW 流程鏈中重複 DTP 時，將多次提取資料。

- 在 DTP 中：將**提取模式**設置為 **"完整**"。 您必須在創建 OHD 後立即將自動創建的 DTP 從**增量**更改為 **"完整**"，如下圖所示：

   ![創建為"完全"提取配置的 SAP BW OHD 對話方塊](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- 在資料工廠的 BW 打開集線器連接器中：關閉**排除上次請求**。 否則，將提取任何內容。

您通常手動運行完整的 DTP。 或者，您可以為完整的 DTP 創建流程鏈。 它通常是獨立于現有流程鏈的獨立鏈。 在這兩種情況下，*請確保 DTP 已完成，然後才能使用資料工廠副本開始提取*。 否則，將僅複製部分資料。

### <a name="run-delta-extraction-the-first-time"></a>首次運行增量提取

第一個增量提取在技術上是*完全提取*。 預設情況下，SAP BW 開放集線器連接器在複製資料時排除最後一個請求。 對於第一個增量提取，資料工廠複製活動不會提取任何資料，直到後續 DTP 在具有單獨請求 ID 的表中生成增量資料。 有兩種方法可以避免這種情況：

- 關閉第一個增量提取的 **"排除最後一個請求**"選項。 在第一次啟動增量提取之前，請確保完成了第一個增量 DTP。
-  使用該過程重新同步增量提取，如下一節所述。

### <a name="resync-delta-extraction"></a>重新同步增量提取

以下方案更改 SAP BW 多維資料集中的資料，但增量 DTP 不考慮：

- SAP BW 選擇性刪除（使用任何篩選準則對行）
- SAP BW 請求刪除（錯誤請求）

SAP 開放集線器目標不是資料超市控制的資料目標（自 2015 年以來的所有 SAP BW 支援包中）。 因此，您可以在不更改 OHD 中的資料的情況下從多維資料集中刪除資料。 然後，您必須將資料多維資料集的資料與資料工廠重新同步：

1. 在資料工廠中運行完全提取（通過在 SAP 中使用完整的 DTP）。
2. 刪除增量 DTP 的"打開中心"表中的所有行。
3. 將增量 DTP 的狀態設置為 **"已提取**"。

在此之後，所有後續的增量 DTP 和資料工廠增量提取都按預期工作。

要將增量 DTP 的狀態設置為 **"提取"，** 可以使用以下選項手動運行增量 DTP：

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>後續步驟

瞭解 SAP BW 開放集線器連接器支援：

> [!div class="nextstepaction"]
>[SAP 業務倉庫打開中心連接器](connector-sap-business-warehouse-open-hub.md)
