---
title: 配置容器 - 面
titleSuffix: Azure Cognitive Services
description: 臉部容器執行階段環境可使用 `docker run` 命令引數來設定。 有必需和可選設置。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 3c78c9eb85c3a8be236be5c3a24bd877db204b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76167969"
---
# <a name="configure-face-docker-containers"></a>設定臉部 Docker 容器

**臉部**容器執行階段環境可使用 `docker run` 命令引數來設定。 此容器有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括計費設定。 

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`Billing`](#billing-configuration-setting) [`Eula`](#eula-setting)和[`ApiKey`](#apikey-configuration-setting)設置一起使用，您必須為所有三個設置提供有效值;因此，您必須為所有三個設置提供有效值。否則您的容器將無法啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](face-how-to-install-containers.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure資源金鑰。 您必須為 ApiKey 指定值，並且該值必須是為[`Billing`](#billing-configuration-setting)配置設置指定的_認知服務_資源的有效金鑰。

此設定可在下列位置找到：

* Azure 門戶：在**金鑰**下**進行認知服務**資源管理

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

該`Billing`設置指定 Azure 上的_認知服務_資源的終結點 URI，用於計量容器的計費資訊。 您必須為此配置設置指定值，並且該值必須是 Azure 上的_認知服務_資源的有效終結點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

此設定可在下列位置找到：

* Azure 門戶：**認知服務**概述，標記為`Endpoint`

請記住將_面_路由添加到終結點 URI，如示例所示。 

|必要| 名稱 | 資料類型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 計費終結點 URI。 有關獲取計費 URI 的詳細資訊，請參閱[收集所需的參數](face-how-to-install-containers.md#gathering-required-parameters)。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](../cognitive-services-custom-subdomains.md)。 |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>CloudAI 組態設定

`CloudAI` 區段中的組態設定能提供對您容器而言是唯一的容器特定選項。 下列是 `CloudAI` 區段中支援臉部容器的設定和物件

| 名稱 | 資料類型 | 描述 |
|------|-----------|-------------|
| `Storage` | Object | 臉部容器所使用的儲存體案例。 如需適用於 `Storage` 物件之儲存體案例和相關設定的詳細資訊，請參閱[儲存體案例設定](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>儲存體案例設定

依所儲存的內容而定，臉部容器有可能會儲存 Blob、快取、中繼資料，以及佇列資料。 例如，針對大型人員群組的定型索引和結果會被儲存為 Blob 資料。 與下列資料類型互動及儲存它們時，臉部容器會提供兩種不同的儲存體案例：

* 記憶體  
  全部四種資料類型都會被儲存在記憶體中。 它們不會散發，也不具永續性。 如果停止或移除臉部容器，系統便會終結該容器之儲存體中的所有資料。  
  這是適用於臉部容器的預設儲存體案例。
* Azure  
  臉部容器會使用 Azure 儲存體和 Azure Cosmos DB 來將這四種資料類型散發至永續性儲存體上。 Blob 和佇列資料是由 Azure 儲存體負責處理。 中繼資料和快取資料是由 Azure Cosmos DB 來處理。 如果停止或移除臉部容器，該容器之儲存體中的所有資料都會繼續存放在 Azure 儲存體和 Azure Cosmos DB 中。  
  Azure 儲存體案例所使用的資源具有下列額外需求
  * Azure 儲存體資源必須使用 StorageV2 帳戶類型
  * Azure Cosmos DB 資源必須使用 Azure Cosmos DB 的 MongoDB 版 API

儲存體案例和相關的組態設定是由 `CloudAI` 組態區段底下的 `Storage` 物件所管理。 `Storage` 物件提供下列組態設定：

| 名稱 | 資料類型 | 描述 |
|------|-----------|-------------|
| `StorageScenario` | String | 容器所支援的儲存體案例。 可以使用下列值<br/>`Memory`：預設值。 容器會針對單一節點的暫時性使用方式，使用非永續性、非分散式且記憶體內部的儲存體。 如果停止或移除容器，系統便會終結該容器的儲存體。<br/>`Azure`：容器使用 Azure 資源作為儲存體。 如果停止或移除容器，該容器的儲存體仍會保存。|
| `ConnectionStringOfAzureStorage` | String | 容器所使用之 Azure 儲存體資源的連接字串。<br/>此設定只有在已針對 `StorageScenario` 組態設定指定 `Azure` 的情況下才會套用。 |
| `ConnectionStringOfCosmosMongo` | String | 容器所使用之 Azure Cosmos DB 資源的 MongoDB 連接字串。<br/>此設定只有在已針對 `StorageScenario` 組態設定指定 `Azure` 的情況下才會套用。 |

例如，下列命令會指定 Azure 儲存體案例，並針對用來儲存臉部容器資料的 Azure 儲存體和 Cosmos DB 資源提供範例連接字串。

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

儲存體案例會與輸入裝載和輸出裝載分開處理。 您可以針對單一容器指定那些功能的組合。 例如，下列命令會將 Docker 繫結裝載定義至主機電腦上的 `D:\Output` 資料夾作為輸出裝載，然後從臉部容器映像將容器具現化，並以 JSON 格式將記錄檔儲存至輸出裝載。 該命令也會指定 Azure 儲存體案例，並針對用來儲存臉部容器資料的 Azure 儲存體和 Cosmos DB 資源提供範例連接字串。

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP Proxy 認證設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 選項，以指定輸入裝載或輸出裝載。

臉部容器不會使用輸入或輸出裝載來儲存訓練或服務資料。 

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，[主機電腦](face-how-to-install-containers.md#the-host-computer)的裝載位置可能會因為 Docker 服務帳戶所使用的權限與主機裝載位置的權限互相衝突，而無法存取。 

|選用| 名稱 | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|不允許| `Input` | String | 臉部容器不會使用此項目。|
|選用| `Output` | String | 輸出裝載的目標。 預設值是 `/output`。 這是記錄的位置。 這包括容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令 

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](face-how-to-install-containers.md#stop-the-container)。

* **行延續字元**：以下部分中的 Docker 命令使用反斜線 ，`\`作為行延續字元。 請根據您主機作業系統的需求加以替換或移除。 
* **參數順序**：除非非常熟悉 Docker 容器，否則不要更改參數的順序。

請將 {_argument_name_} 取代為您自己的值：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **[API_KEY]** | Azure`Face``Face`鍵頁上資源的終結點鍵。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **[ENDPOINT_URI]** | 計費終結點值在 Azure`Face`概述頁上可用。| 有關顯式示例，請參閱[收集所需的參數](face-how-to-install-containers.md#gathering-required-parameters)。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](face-how-to-install-containers.md#billing)。
> ApiKey 值是 Azure`Cognitive Services`資源鍵頁中的 **"鍵**"。 

## <a name="face-container-docker-examples"></a>臉部容器 Docker 範例

下列是臉部容器的 Docker 範例。 

### <a name="basic-example"></a>基本範例 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>日誌記錄示例 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>後續步驟

* 檢閱[如何安裝及執行容器](face-how-to-install-containers.md)
