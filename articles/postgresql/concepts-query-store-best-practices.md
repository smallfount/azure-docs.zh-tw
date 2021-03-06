---
title: 查詢在 Azure 資料庫中存儲適用于 PostgreSQL - 單個伺服器的最佳做法
description: 本文介紹了 Azure 資料庫中的"PostgreSQL - 單伺服器"查詢存儲的最佳做法。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 51239f4cf49784dd47470e1272b90508eaf25e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70764225"
---
# <a name="best-practices-for-query-store"></a>查詢存放區的最佳做法

**適用于：** 用於 PostgreSQL 的 Azure 資料庫 - 單伺服器版本 9.6、10、11

此文章概述在適用於 PostgreSQL 的 Azure 資料庫中使用查詢存放區的最佳做法。

## <a name="set-the-optimal-query-capture-mode"></a>設定最佳查詢擷取模式
讓查詢存放區擷取對您而言重要的資料。 

|**pg_qs.query_capture_mode** | **案例**|
|---|---|
|_所有_  |根據您的所有查詢與其執行頻率與其他統計資料徹底分析您的工作負載。 識別您工作負載中的新查詢。 檢測是否使用臨時查詢來識別使用者或自動參數化的機會。 _全都_有增加的資源耗用成本。 |
|_返回頁首_  |專注在前幾個查詢 - 那些由客戶發出的查詢。
|_無_ |您已擷取查詢集與一段時間，您想要在這段時間內調查並減少其他查詢可能會產生之令人困惑之事。 _無_適用於測試及效能評定環境。 _無_應該謹慎使用，因為您可能可能會錯過追蹤及最佳化重要新查詢的機會。 我們無法復原過去時段內的資料。 |

查詢存放區也包含等候統計資料的存放區。 有一個額外的擷取模式查詢，此查詢規範等候統計資料：**pgms_wait_sampling.query_capture_mode** 可設定為 _none_ 或 _all_。 

> [!NOTE] 
> **pg_qs.query_capture_mode** 已取代 **pgms_wait_sampling.query_capture_mode**。 如果pg_qs.query_capture_mode_為無_，則pgms_wait_sampling.query_capture_mode設置無效。 


## <a name="keep-the-data-you-need"></a>保留您所需的資料
**pg_qs.retention_period_in_days** 參數可指定查詢存放區的資料保留期間 (天)。 較舊的查詢與統計資料會被刪除。 根據預設，查詢存放區是設定為將資料保留 7 天。 避免保留您不打算使用的歷史資料。 若需要保留資料較長的時間，請增加該值。


## <a name="set-the-frequency-of-wait-stats-sampling"></a>設定等候統計資料取樣的頻率 
**pgms_wait_sampling.history_period** 參數可指定等候事件取樣頻率 (毫秒)。 期間越短，取樣頻率就越頻繁。 會擷取更多資訊，但會也產生較高的資源耗用成本。 若伺服器負載不足或您不需要精細度，您可以增加此期間長度


## <a name="get-quick-insights-into-query-store"></a>取得查詢存放區的快速見解
您可以使用 Azure入口網站中的[查詢效能深入解析](concepts-query-performance-insight.md)來取得查詢存放區資料的快速見解。 視覺效果會呈現一段時間內執行時間最長的查詢，以及最長的等候時間。

## <a name="next-steps"></a>後續步驟
- 了解如何使用 [Azure 入口網站](howto-configure-server-parameters-using-portal.md) 或 [Azure CLI](howto-configure-server-parameters-using-cli.md) 來取得或設定參數。