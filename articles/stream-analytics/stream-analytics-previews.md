---
title: Azure 串流分析預覽功能
description: 本文列出目前處於預覽狀態的 Azure 串流分析功能。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 3e99263f6bf472c256e1747b8567249bbd62a445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969624"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure 串流分析預覽功能

本文摘要說明目前處於預覽狀態的所有 Azure 串流分析功能。 生產環境不建議使用預覽功能。

## <a name="public-previews"></a>公開預覽

下列功能目前處於公開預覽狀態。 您目前可以利用這些功能，但請勿將其用於生產環境。

### <a name="online-scaling"></a>線上縮放

使用連線縮放，如果需要更改 SU 分配，則無需停止作業。 您可以增加或減少正在運行的作業的 SU 容量，而無需停止它。 這建立在客戶承諾的長期運行的任務關鍵型管道，流分析今天提供。 有關詳細資訊，請參閱配置[Azure 流分析流單元](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus)。

### <a name="c-custom-de-serializers"></a>C# 自訂序列化器
開發人員可以利用 Azure 流分析的強大功能來處理 Protobuf、XML 或任何自訂格式的資料。 您可以在 C# 中實現[自訂序列化程式](custom-deserializer-examples.md)，然後可用於取消 Azure 流分析接收的事件序列化。

### <a name="extensibility-with-c-custom-code"></a>使用 C# 自訂代碼擴展

在雲或 IoT Edge 上創建流分析模組的開發人員可以編寫或重用自訂 C# 函數，並通過[使用者定義的函數](stream-analytics-edge-csharp-udf-methods.md)直接在查詢中調用它們。


### <a name="debug-query-steps-in-visual-studio"></a>視覺化工作室中的調試查詢步驟

在為 Visual Studio 的 Azure 流分析工具執行本地測試時，可以輕鬆地預覽資料圖上的中間行集。 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>使用視覺化工作室代碼中的即時資料進行本地測試

在將作業提交到 Azure 之前，可以根據本地電腦上的即時資料測試查詢。 每次測試反覆運算平均花費不到兩到三秒，從而獲得非常高效的開發過程。

### <a name="visual-studio-code-for-azure-stream-analytics"></a>適用於 Azure 串流分析的 Visual Studio Code

Azure 串流分析作業可以在 Visual Studio Code 中撰寫。 請參閱我們的[VS 代碼入門教程](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)。


### <a name="integration-with-azure-machine-learning"></a>與 Azure Machine Learning 整合

您可以使用 Machine Learning (ML) 函式來調整串流分析作業。 若要深入了解如何在串流分析作業中使用 ML 函式，請造訪[使用 Azure Machine Learning 函式調整串流分析作業](stream-analytics-scale-with-machine-learning-functions.md)。 透過[使用 Azure 串流分析和 Azure Machine Learning 執行情感分析](stream-analytics-machine-learning-integration-tutorial.md)來了解真實案例。


### <a name="live-data-testing-in-visual-studio"></a>在 Visual Studio 中即時測試資料

Azure 串流分析的 Visual Studio 工具加強了本機測試功能，讓您可以針對雲端來源 (例如，事件中樞或 IoT 中樞) 的即時事件串流查詢進行測試。 了解如何[使用適用於 Visual Studio 的 Azure 串流分析工具在本機測試即時資料](stream-analytics-live-data-local-testing.md)。


### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge 上的 .NET 使用者定義函式

有了 .NET 標準使用者定義函式，您可以在串流管線運作期間執行 .NET Standard 程式碼。 您可以建立簡單的 C# 類別，或匯入完整的專案和程式庫。 Visual Studio 支援完整的撰寫和偵錯體驗。 如需詳細資訊，請造訪[針對 Azure 串流分析 Edge 作業開發 .NET Standard 使用者定義的函式](stream-analytics-edge-csharp-udf-methods.md)。

## <a name="other-previews"></a>其他預覽

以下功能也可應要求預覽版提供。

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>使用 Azure 機器學習管理的自訂 ML 模型即時高性能評分

Azure 流分析利用 Azure 機器學習管理的自訂預先訓練的機器學習模型，並使用工作流託管在 Azure 庫伯奈斯服務 （AKS） 或 Azure 容器實例 （ACI） 中，支援高性能的即時評分不需要編寫代碼。 [註冊](https://aka.ms/asapreview1)預覽

### <a name="support-for-azure-stack"></a>支援 Azure 堆疊
此功能在 Azure IoT Edge 運行時啟用，利用自訂 Azure 堆疊功能，例如對 Azure 堆疊上運行的本地輸入和輸出的本機支援（例如事件中心、IoT 中心、Blob 存儲）。 這種新的集成使您能夠構建混合體系結構，以便分析資料在生成位置附近，從而降低延遲並最大化見解。
此功能使您能夠構建混合體系結構，以便分析資料在生成位置附近，從而降低延遲並最大化見解。 您必須[註冊](https://aka.ms/asapreview1)此預覽。
