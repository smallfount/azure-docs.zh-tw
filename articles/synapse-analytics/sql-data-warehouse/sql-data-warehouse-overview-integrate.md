---
title: 編譯整合解決方案
description: 與 Synapse SQL 池整合的解決方案工具和合作夥伴。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c8e3598e55d3f90ab2b7401380406677f56c0ce3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586489"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool"></a>將其他服務與 Synapse SQL 池整合

Azure 突觸分析中的 Synapse SQL 池功能使用戶能夠與 Azure 中的許多其他服務整合。 使用 Synapse SQL,您可以透過其 SQL 池資源建立資料倉儲,然後可以使用其他服務,其中一些服務包括:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure 串流分析

有關跨 Azure 整合服務的詳細資訊,請查看[整合合作夥伴](sql-data-warehouse-partner-data-integration.md)一文。

## <a name="power-bi"></a>Power BI
Power BI 整合允許您將資料主目錄的運算能力與 Power BI 的動態報告和可視化相結合。 目前 Power BI 整合包括：

* **直接連接**:針對使用 SQL 池預配的資料主目錄的邏輯向下設置的更進階連接。 下推可提供更快速且更大規模的分析。
* **在 Power BI 中打開**:「在電源 BI 中打開」按鈕將實例資訊傳遞給 Power BI,以便簡化連接方式。

如需詳細資訊，請參閱[與 Power BI 整合](sql-data-warehouse-get-started-visualize-with-power-bi.md)或 [Power BI 文件](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/)。

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory 提供使用者一個受控平台，以建立複雜的擷取和載入管線。 與 Azure 資料工廠的 SQL 池整合的包括:

* **存儲過程**:協調存儲過程的執行。
* **複製**:使用 ADF 將資料移動到 SQL 池中。 這項作業可以在幕後使用 ADF 的標準資料移動機制或 PolyBase。 

如需詳細資訊，請參閱[與 Azure Data Factory 整合](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json)。

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning 是完全受控的分析服務，可讓您使用一組大型預測工具建立複雜模型。 SQL 池作為這些模型的源和目標都受支援,並且具有以下功能:

* **讀取資料:** 使用 T-SQL 針對 SQL 池大規模驅動模型。
* **寫入資料:** 將更改從任何模型提交回 SQL 池。

如需詳細資訊，請參閱[與 Azure Machine Learning 整合](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)。

## <a name="azure-stream-analytics"></a>Azure 串流分析
Azure 串流分析是複雜、完全受控的基礎結構，可處理和取用產生自 Azure 事件中樞的事件資料。  與 SQL 池集成,可以有效地處理流數據並將其與關係數據一起存儲,從而實現更深入、更高級的分析。  

* **工作輸出:** 將流分析作業的輸出直接發送到 SQL 池。

如需詳細資訊，請參閱[與 Azure 串流分析整合](sql-data-warehouse-integrate-azure-stream-analytics.md)。


