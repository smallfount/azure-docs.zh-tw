---
title: 在映射資料流程中更改行轉換
description: 如何在映射資料流程中使用更改行轉換更新資料庫目標
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 0798a3f9ab45ce68086681e7aea96deeb9639f94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834530"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>在映射資料流程中更改行轉換

使用"更改行"轉換對行設置插入、刪除、更新和更新策略。 您可以將一對多條件添加為運算式。 這些條件應按優先順序順序指定，因為每行都將用與第一個匹配運算式對應的策略進行標記。 每個條件都可能導致插入、更新、刪除或向上更新行（或行）。 更改行可以針對資料庫生成 DDL & DML 操作。

![更改行設置](media/data-flow/alter-row1.png "更改行設置")

更改行轉換將僅在資料流程中的資料庫或 CosmosDB 接收器上運行。 在調試會話期間，不會執行分配給行的操作（插入、更新、刪除、向上更新）。 在管道中運行執行資料流程活動，以在資料庫表上執行更改行策略。

## <a name="specify-a-default-row-policy"></a>指定預設行策略

創建"更改行"轉換並指定條件為 的`true()`行策略。 將為每個與以前定義的運算式不匹配的行進行標記。 預設情況下，與任何條件運算式不匹配的每行都將標記為`Insert`。

![更改行策略](media/data-flow/alter-row4.png "更改行策略")

> [!NOTE]
> 要用一個策略標記所有行，可以為該策略創建條件，並將條件指定為`true()`。

## <a name="view-policies-in-data-preview"></a>在資料預覽中查看策略

使用[偵錯模式](concepts-data-flow-debug-mode.md)在資料預覽窗格中查看更改行策略的結果。 更改行轉換的資料預覽不會針對目標生成 DDL 或 DML 操作。

![更改行策略](media/data-flow/alter-row3.png "更改行策略")

每個更改行策略都由一個圖示表示，該圖示指示是否將發生插入、更新、向上或已刪除的操作。 頂部標題顯示預覽中每個策略受影響的行數。

## <a name="allow-alter-row-policies-in-sink"></a>允許在接收器中更改行策略

要使更改行策略正常工作，資料流程必須寫入資料庫或 Cosmos 接收器。 在接收器中的 **"設置"** 選項卡中，啟用允許該接收器更改行策略的。

![更改行接收器](media/data-flow/alter-row2.png "更改行接收器")

 預設行為是只允許插入。 要允許更新、升級或刪除，請在接收器中選中與該條件對應的框。 如果啟用了更新、升級或刪除，則必須指定要匹配的接收器中的鍵列。

> [!NOTE]
> 如果插入、更新或 upserts 修改接收器中目標表的架構，資料流程將失敗。 要修改資料庫中的目標架構，請選擇 **"重新創建表**"作為表操作。 這將刪除新的架構定義並重新創建表。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>範例

下面的示例是一個命名`CleanData`更改行轉換，該轉換採用傳入`SpecifyUpsertConditions`流並創建三個更改行條件。 在上一個轉換中，將`alterRowCondition`計算一個命名的列，該列確定在資料庫中是否插入、更新或刪除行。 如果列的值具有與 alter 行規則匹配的字串值，則為其分配該策略。

在資料工廠 UX 中，此轉換類似于下圖：

![更改行示例](media/data-flow/alter-row4.png "更改行示例")

此轉換的資料流程腳本位於下面的程式碼片段中：

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>後續步驟

在 Alter Row 轉換後，您可能希望[將資料沉入目標資料存儲中](data-flow-sink.md)。
