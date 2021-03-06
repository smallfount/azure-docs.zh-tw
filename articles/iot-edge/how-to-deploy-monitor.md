---
title: 在 Azure 門戶中大規模部署模組 - Azure IoT 邊緣
description: 使用 Azure 入口網站為 IoT Edge 裝置群組建立自動部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271430"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>使用 Azure 入口網站大規模部署和監視 IoT Edge 模組

在 Azure 門戶中創建**IoT Edge 自動部署**，以便一次管理許多設備的正在進行的部署。 IoT Edge 的自動部署是 IoT 中心[自動裝置管理](/azure/iot-hub/iot-hub-automatic-device-management)功能的一部分。 部署是動態過程，使您能夠將多個模組部署到多個設備，跟蹤模組的狀態和運行狀況，並在必要時進行更改。

有關詳細資訊，請參閱[瞭解單個設備或規模上的 IoT 邊緣自動部署](module-deployment-monitoring.md)。

## <a name="identify-devices-using-tags"></a>使用標記識別裝置

在您可建立部署之前，必須能夠指定您要影響的裝置。 Azure IoT Edge 會使用裝置對應項中的 **tags** 來識別裝置。 每個設備可以有多個標記，您以任何對解決方案有意義的方式定義這些標記。

例如，如果您管理智慧建築的園區，則可以向設備添加位置、房間類型和環境標記：

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

如需裝置對應項和標記的詳細資訊，請參閱[了解和使用 IoT 中樞的裝置對應項](../iot-hub/iot-hub-devguide-device-twins.md)。

## <a name="create-a-deployment"></a>建立部署

IoT Edge 提供了兩種不同類型的自動部署，可用於自訂方案。 您可以創建一個標準*部署*，其中包括該系統運行時模組以及任何其他模組和路由。 每個設備只能應用一個部署。 或者，您可以創建*分層部署*，該部署僅包括自訂模組和路由，而不包括系統運行時。 許多分層部署可以在設備上（標準部署之上）進行組合。 有關兩種類型的自動部署如何協同工作的詳細資訊，請參閱[瞭解單個設備或規模中的 IoT Edge 自動部署](module-deployment-monitoring.md)。

創建部署和分層部署的步驟非常相似。 在以下步驟中調用任何差異。

1. 在[Azure 門戶](https://portal.azure.com)中，轉到 IoT 中心。
1. 在左側窗格中的功能表上，在 **"自動裝置管理**"下選擇**IoT 邊緣**。
1. 在上欄上，選擇 **"創建部署"** 或 **"創建分層部署**"。

建立部署有五個步驟。 下列各節將逐步解說每一個步驟。

### <a name="step-1-name-and-label"></a>第 1 步：名稱和標籤

1. 為部署指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。
1. 您可以新增機碼值組形式的標籤，以協助追蹤部署。 例如，**HostPlatform** 和 **Linux**，或 **Version** 和 **3.0.1**。
1. 選擇 **"下一步"：要**移動到步驟二的模組。

### <a name="step-2-modules"></a>第 2 步：模組

您最多可以將 20 個模組添加到部署中。 如果創建沒有模組的部署，它將從目標設備中刪除任何當前模組。

在部署中，您可以管理 IoT Edge 代理和 IoT Edge 中心模組的設置。 選擇**運行時設置**以配置兩個運行時模組。 在分層部署中，不包括運行時模組，因此無法配置。

您可以添加三種類型的模組：

* 物聯網邊緣模組
* 市場模組
* Azure 流分析模組

#### <a name="add-an-iot-edge-module"></a>添加 IoT 邊緣模組

若要新增自訂程式碼作為模組，或手動新增 Azure 服務模組，請遵循下列步驟：

1. 在頁面的 **"容器註冊表憑據"** 部分中，提供包含此部署的模組映射的任何專用容器註冊表的名稱和憑據。 如果 IoT 邊緣代理找不到 Docker 映射的容器註冊表憑據，它將報告錯誤 500。
1. 在頁面的 [IoT Edge 模組]**** 區段中，按一下 [新增]****。
1. 從下拉式功能表中選擇**IoT 邊緣模組**。
1. 為您的模組指定**IoT 邊緣模組名稱**。
1. 針對 [映像 URI]**** 欄位，為您的模組輸入容器映像。
1. 使用下拉式功能表來選取**重新啟動原則**。 從下列選項中選擇：
   * **始終**- 如果模組因任何原因關閉，則始終重新開機。
   * **從不**- 模組永遠不會重新開機，如果它因任何原因關閉。
   * **故障**- 如果模組崩潰，模組將重新開機，但如果它乾淨地關閉，則重新開機。
   * **on 不正常**- 如果模組崩潰或返回不正常狀態，則重新開機。 每個模組必須負責實作健康情況狀態函式。
1. 使用下拉功能表來選取模組的**預期狀態**。 從下列選項中選擇：
   * **正在運行**- 運行是預設選項。 模組將會在部署後立即開始執行。
   * **已停止**- 部署後，模組將保持空閒狀態，直到您或其他模組調用該模組。
1. 指定任何應傳遞至容器的**容器建立選項**。 如需詳細資訊，請參閱 [docker create](https://docs.docker.com/engine/reference/commandline/create/)。
1. 如果要向模組孿生委任標記或其他屬性，請選擇 **"模組孿生設置**"。
1. 輸入此模組的 [環境變數]****。 環境變數向模組提供配置資訊。
1. 選擇 **"添加"** 以將模組添加到部署。

#### <a name="add-a-module-from-the-marketplace"></a>從應用商店添加模組

要從 Azure 應用商店添加模組，請按照以下步驟操作：

1. 在頁面的 [IoT Edge 模組]**** 區段中，按一下 [新增]****。
1. 從下拉式功能表中選擇 **"市場模組**"。
1. 從**IoT 邊緣模組應用商店**頁面中選擇模組。 您選擇的模組將自動為訂閱、資源組和設備配置。 然後，它將顯示在 IoT 邊緣模組清單中。 某些模組可能需要其他配置。 有關詳細資訊，請參閱從[Azure 應用商店部署模組](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)。

#### <a name="add-a-stream-analytics-module"></a>添加流分析模組

若要從 Azure 串流分析新增模組，請遵循下列步驟：

1. 在頁面的 [IoT Edge 模組]**** 區段中，按一下 [新增]****。
1. 從下拉式功能表中選擇**Azure 流分析模組**。
1. 在右側窗格中，選擇 **"訂閱**"。
1. 選擇 IoT**邊緣作業**。
1. 選取 [儲存]****，將您的模組新增至部署。

#### <a name="configure-module-settings"></a>配置模組設置

將模組添加到部署後，可以選擇其名稱以打開 **"更新 IoT 邊緣模組"** 頁。 在此頁上，您可以編輯模組設置、環境變數、創建選項和模組孿生。 如果從市場添加了模組，則該模組可能已經填充了其中一些參數。

如果要創建分層部署，則可能是正在配置針對同一設備的其他部署中存在的模組。 要更新模組孿生而不覆蓋其他版本，請打開 **"模組孿生設置"** 選項卡。為模組孿生所需的屬性中的子節創建一個新的**模組孿生屬性**，該屬性具有唯一的名稱`properties.desired.settings`，例如 。 如果僅在`properties.desired`欄位中定義屬性，它將覆蓋任何低優先順序部署中定義的模組所需的屬性。

![為分層部署設置模組孿生屬性](./media/how-to-deploy-monitor/module-twin-property.png)

有關分層部署中的模組孿生配置的詳細資訊，請參閱[分層部署](module-deployment-monitoring.md#layered-deployment)。

配置部署的所有模組後，選擇 **"下一步：路由**"以移動到步驟三。

### <a name="step-3-routes"></a>第 3 步：路由

路由會定義模組在部署內彼此通訊的方式。 預設情況下，嚮導為您提供了一條稱為**上游**路由，定義為**FROM /消息\*/into $upstream**，這意味著任何模組輸出的任何消息都發送到 IoT 中心。  

請使用[宣告路由](module-composition.md#declare-routes)中的資訊來新增或更新路由，然後選取 [下一步]**** 繼續檢閱區段。

選擇 **"下一步"：指標**。

### <a name="step-4-metrics"></a>第 4 步：指標

計量能提供在套用設定內容後，裝置所回報各種狀態的摘要計數。

1. 輸入**指標名稱**的名稱。

1. 在 [計量準則]**** 輸入查詢。 查詢會以 IoT Edge 中樞模組對應項[所報告的屬性](module-edgeagent-edgehub.md#edgehub-reported-properties)作為基礎。 此計量代表查詢所傳回的資料列數目。

   例如：

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

選擇 **"下一步"：目標設備**。

### <a name="step-5-target-devices"></a>第 5 步：目標設備

使用裝置的 tags 屬性，將目標設為應該接收此部署的特定裝置。

由於多個部署可能會以相同裝置為目標，因此，您應該為每個部署提供優先順序號碼。 如果存在衝突，則具有最高優先順序的部署（值越大表示優先順序越高）將獲勝。 如果兩個部署具有相同的優先順序號碼，則最新建立的部署獲勝。

如果多個部署針對同一設備，則僅應用優先順序較高的設備。 如果多個分層部署以同一設備為目標，則它們全部應用。 但是，如果複製任何屬性（例如，如果有兩個具有相同名稱的路由），則來自較高優先順序分層部署的路由將覆蓋其餘。

任何針對設備的分層部署都必須具有比基本部署更高的優先順序才能應用。

1. 為部署**優先順序**輸入一個正整數。
1. 輸入**目標條件**來判斷這個部署會將哪些裝置設為目標。條件會以裝置對應項標籤或裝置對應項報告屬性為基礎，且應符合運算式格式。例如，`tags.environment='test'` 或 `properties.reported.devicemodel='4000x'`。

選擇 **"下一步"：查看 + 創建**以繼續執行最後一步。

### <a name="step-6-review-and-create"></a>第 6 步：查看和創建

查看部署資訊，然後選擇 **"創建**"。

## <a name="monitor-a-deployment"></a>監視部署

若要檢視部署的詳細資料並監視正在執行該部署的裝置，請使用下列步驟：

1. 登錄到 Azure[門戶](https://portal.azure.com)並導航到 IoT 中心。
1. 選取 [IoT Edge]****。
1. 選擇**IoT 邊緣部署**選項卡。

   ![檢視 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 檢查部署清單。針對每個部署，您可以檢視下列詳細資料：
   * **識別碼**：部署的名稱。
   * **類型**- 部署的類型，**部署**或**分層部署**。
   * **目標條件**- 用於定義目標設備的標記。
   * **優先順序**：指派給部署的優先順序號碼。
   * **系統指標** - **"目標"** 指定 IoT 中心中與定位條件匹配的設備孿生數，**應用指定**已將部署內容應用於 IoT 中心中的模組孿生的設備數。
   * **設備指標**- 部署中報告 IoT Edge 用戶端運行時成功或錯誤的數量。
   * **自訂指標**- 部署報告資料中為部署定義的任何指標的 IoT Edge 設備數。
   * **創建時間**- 創建部署時的時間戳記。 當兩個部署具有相同的優先順序時，可使用此時間戳記來中斷繫結。
1. 選取您想要監視的部署。  
1. 檢查部署詳細資料。 您可以使用索引標籤來檢閱部署的詳細資料。

## <a name="modify-a-deployment"></a>修改部署

當您修改部署時，所做的變更會立即複寫到所有目標裝置。

如果您更新目標條件，就會發生下列更新：

* 如果裝置不符合舊的目標條件，但符合新的目標條件，而且此部署為該裝置的最高優先順序，則會將此部署套用到裝置。
* 如果目前執行此部署的裝置不再符合目標條件，它會解除安裝此部署，並採用下一個最高優先順序的部署。
* 如果目前執行此部署的裝置不再符合目標條件，且不符合任何其他部署的目標條件，則不會在裝置上發生任何變更。 裝置會以模組目前的狀態繼續執行它目前的模組，但不再將其當成此部署的一部分來管理。 一旦它符合任何其他部署的目標條件之後，就會解除安裝此部署，並採用新的部署。

若要修改部署，請使用下列步驟：

1. 登錄到 Azure[門戶](https://portal.azure.com)並導航到 IoT 中心。
1. 選取 [IoT Edge]****。
1. 選擇**IoT 邊緣部署**選項卡。

   ![檢視 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 選取您想要修改的部署。
1. 在以下選項卡上進行更新：
   * **目標條件**
   * **指標**- 您可以修改或刪除已定義的指標，或添加新指標。
   * **標籤**
   * **模組**
   * **路線**
   * **部署**

1. 選取 [儲存]****。
1. 依照[監視部署](#monitor-a-deployment)中的步驟來監看所推出的變更。

## <a name="delete-a-deployment"></a>刪除部署

刪除部署時，任何已部署的設備都將執行其下一個最高優先順序部署。 如果您的裝置不符合任何其他部署的目標條件，則在刪除部署時不會移除模組。

1. 登錄到 Azure[門戶](https://portal.azure.com)並導航到 IoT 中心。
1. 選取 [IoT Edge]****。
1. 選擇**IoT 邊緣部署**選項卡。

   ![檢視 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 使用核取方塊來選取您想要刪除的部署。
1. 選擇 **"刪除**"。
1. 提示將會通知您，這個動作將刪除此部署，並將所有裝置還原成先前狀態。這表示將套用優先順序較低的部署。如果沒有將其他部署設為目標，將不會移除任何模組。 如果您想要從裝置中移除所有模組，請建立不含模組的部署，並將其部署至相同的裝置。選擇 **"是**"以繼續。

## <a name="next-steps"></a>後續步驟

瞭解有關[將模組部署到 IoT 邊緣設備](module-deployment-monitoring.md)方面瞭解有關的介紹。
