---
title: 處理工作流中的錯誤和異常
description: 瞭解如何處理使用 Azure 邏輯應用創建的自動化任務和工作流中發生的錯誤和異常
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: 73b116117530e5a2103b604efbf757d691006508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284027"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>處理 Azure Logic Apps 中的錯誤和例外狀況

適當地處理相依系統所造成的停機情況或問題，對任何整合架構來說都是種挑戰。 為了協助您建立穩固且具有恢復能力的整合方案來處理問題與失敗，Logic Apps 提供了一流的錯誤和例外狀況處理體驗。

<a name="retry-policies"></a>

## <a name="retry-policies"></a>重試原則

對於最基本的異常和錯誤處理，可以在支援的任何操作或觸發器中使用*重試策略*，例如，請參閱[HTTP 操作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)。 此重試原則會定義當原始要求逾時或失敗時 (也就是，任何造成 408、429 或 5xx 回應的要求)，動作或觸發程序是否要重試要求，以及要如何重試。 如果未使用任何其他重試原則，則會使用預設原則。

以下是重試原則的類型：

| 類型 | 描述 |
|------|-------------|
| **預設** | 此原則會以*指數漸增*的間隔 (依 7.5 秒調整，但限制在 5 到 45 秒之間) 傳送最多 4 次重試。 |
| **指數間隔**  | 此原則會先等待選自指數成長範圍內的隨機間隔時間，再傳送下一個要求。 |
| **固定間隔**  | 此原則會先等待指定的間隔時間，再傳送下一個要求。 |
| **無**  | 不重新傳送要求。 |
|||

如需有關重試原則限制的資訊，請參閱 [Logic Apps 限制和設定](../logic-apps/logic-apps-limits-and-config.md#request-limits)。

### <a name="change-retry-policy"></a>變更重試原則

若要選取不同的重試原則，請遵循下列步驟：

1. 在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 開啟動作或觸發程序的 [設定]****。

1. 如果動作或觸發程序支援重試原則，請於 [重試原則]**** 底下選取您想要的類型。

或者，您也可以針對支援重試原則的動作或觸發程序，在其 `inputs` 區段中手動指定重試原則。 如果您未指定重試原則，則動作會使用預設原則。

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*必要*

| 值 | 類型 | 描述 |
|-------|------|-------------|
| <*重試策略類型*> | String | 您想要使用的重試原則類型：`default`、`none`、`fixed` 或 `exponential` |
| <*重試間隔*> | String | 值必須使用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的重試間隔。 預設最小間隔是 `PT5S`，最大間隔則是 `PT1D`。 當您使用指數間隔原則時，您可以指定不同的最小和最大值。 |
| <*重試嘗試*> | 整數  | 重試次數必須介於 1 到 90 之間 |
||||

*選*

| 值 | 類型 | 描述 |
|-------|------|-------------|
| <*最小間隔*> | String | 對於指數間隔原則，此為隨機選取間隔的最小間隔，且採用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
| <*最大間隔*> | String | 對於指數間隔原則，此為隨機選取間隔的最大間隔，且採用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
||||

以下是不同原則類型的詳細資訊。

<a name="default-retry"></a>

### <a name="default"></a>預設

若您未指定重試原則，則動作會使用預設原則 (實際上就是[指數間隔原則](#exponential-interval))，此原則會以指數漸增間隔 (以 7.5 秒作調整) 傳送最多四次重試。 間隔的最小值與最大值為 5 秒和 45 秒。

雖然動作或觸發程序中未明確定義，但以下是 HTTP 動作範例中的預設原則行為：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>None

若要將動作或觸發程序指定為不會重試失敗的要求，請將 <retry-policy-type**> 設定為 `none`。

### <a name="fixed-interval"></a>固定間隔

若要將動作或觸發程序指定為先等候指定間隔再傳送下一個要求，請將 <retry-policy-type**> 設定為 `fixed`。

*範例*

此重試原則會嘗試在第一次失敗要求後再取得最新消息兩次，每次嘗試之間有 30 秒的延遲：

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>指數間隔

若要將動作或觸發程序指定為先等候隨機間隔再傳送下一個要求，請將 <retry-policy-type**> 設定為 `exponential`。 隨機間隔會從指數成長範圍來選取。 (選擇性) 您也可以藉由指定自己的最小和最大間隔，覆寫預設的最小和最大間隔。

**隨機變數範圍**

此資料表說明 Logic Apps 如何針對每次重試 (最高可達重試次數 (含))，產生指定範圍內的統一隨機變數：

| 重試數目 | 最小間隔 | 最大間隔 |
|--------------|------------------|------------------|
| 1 | 最大（0，<*最小間隔*>） | 最小值（間隔，<*最大間隔*>） |
| 2 | 最大（間隔，<*最小間隔*>） | 最小值（2 = 間隔，<*最大間隔*>） |
| 3 | 最大（2 = 間隔，<*最小間隔*>） | 最小值（4 = 間隔，<*最大間隔*>） |
| 4 | 最大（4 = 間隔，<*最小間隔*>） | 最小值（8 = 間隔，<*最大間隔*>） |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>通過更改"後運行"行為來捕獲和處理故障

在邏輯應用設計器中添加操作時，將隱式聲明用於運行這些操作的順序。 操作完成`Succeeded`運行後，該操作將標有狀態，如 、`Failed``Skipped`或`TimedOut`。 在每個操作定義中`runAfter`，屬性指定必須先完成的前置任務操作以及該前置任務執行者可以運行之前允許的狀態。 預設情況下，在設計器中添加的操作僅在前置任務完成狀態`Succeeded`後運行。

當操作引發未處理的錯誤或異常時，將標記`Failed`該操作，並標記`Skipped`任何後續操作 。 如果對於具有平行分支的操作發生此行為，邏輯應用引擎將遵循其他分支來確定其完成狀態。 例如，如果分支以操作`Skipped`結尾，則該分支的完成狀態基於跳過的操作的前置狀態。 邏輯應用運行完成後，引擎通過評估所有分支狀態來確定整個運行的狀態。 如果任何分支以失敗告終，則對整個邏輯應用運行進行標記`Failed`。

![顯示如何計算運行狀態的示例](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

為了確保操作在其前身的狀態如何仍可以運行，[請自訂操作的"後運行"行為](#customize-run-after)以處理前置任務的失敗狀態。

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>自訂"後運行"行為

您可以自訂操作的"後運行"行為，以便在前置任務的狀態為`Succeeded`、、、、、`Failed``Skipped`或`TimedOut`任何這些狀態時運行該操作。 例如，在為 Excel Online`Add_a_row_into_a_table`前置任務前置操作`Failed`進行標記後`Succeeded`發送電子郵件，而不是通過以下任一步驟更改"運行後"行為：

* 在設計檢視中，選擇橢圓 **（...）** 按鈕，然後選擇 **"配置運行後**運行"。

  ![為操作配置"運行後"行為](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  操作形狀顯示前置操作所需的預設狀態，在此示例中，該狀態是**將一行添加到表中**：

  ![操作的預設"運行後"行為](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  將"運行後運行"行為更改為所需的狀態，**在此示例中失敗：**

  ![將"運行後"行為更改為"已失敗"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  要指定操作是否將前置操作標記為`Failed`"`Skipped`或`TimedOut`"，請選擇其他狀態：

  ![將"運行後"行為更改為具有任何其他狀態](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* 在代碼視圖中，在操作的 JSON 定義中，編輯`runAfter`遵循以下語法的屬性：

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  在此示例中，將`runAfter`屬性從`Succeeded`更改為`Failed`：

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  要指定操作是否將前置操作標記為`Failed`"`Skipped`或`TimedOut`"添加其他狀態：：

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>以範圍和其結果評估動作

與使用`runAfter`屬性執行單個操作後運行步驟類似，您可以在[作用域](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)內將操作組合在一起。 當您想要以邏輯方式將動作群組在一起、評估範圍的彙總狀態，並根據這些狀態執行動作時，即可使用範圍。 當範圍中的所有動作都執行完成之後，範圍本身會取得自己的狀態。

要檢查作用域的狀態，可以使用用於檢查邏輯應用的運行狀態的相同條件，例如`Succeeded`、`Failed`等。

預設情況下，當作用域的所有操作成功時，將標記`Succeeded`作用域的狀態。 如果作用域中的最後操作為`Failed`或`Aborted`，則將標記`Failed`作用域的狀態。

要捕獲`Failed`作用域中的異常並運行處理這些錯誤的操作，可以使用 該`runAfter``Failed`作用域的屬性。 這樣 *，如果作用域*中的任何操作失敗，並且對該作用域使用`runAfter`該屬性，則可以創建單個操作來捕獲失敗。

如需範圍的限制，請參閱[限制和設定](../logic-apps/logic-apps-limits-and-config.md)。

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>取得失敗的內容和結果

雖然從範圍擷取失敗很實用，但還是建議您取得內容以協助解實際上有哪些動作失敗，以及所傳回的任何錯誤或狀態碼。

函數[`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result)提供有關作用域中所有操作的結果的上下文。 函數`result()`接受單個參數（即作用域的名稱），並返回一個陣列，其中包含來自該作用域中的所有操作結果。 這些操作物件包括與`actions()`物件相同的屬性，如操作的開始時間、結束時間、狀態、輸入、相關指示和輸出。 要為作用域內失敗的任何操作發送上下文，可以輕鬆地將`@result()`運算式與`runAfter`屬性配對。

要對`Failed`具有結果的作用域中的每個操作運行操作，並將結果陣列篩選為失敗操作，`@result()`可以將運算式與[**篩選器陣列**](logic-apps-perform-data-operations.md#filter-array-action)操作和 for[**每個**](../logic-apps/logic-apps-control-flow-loops.md)迴圈配對。 您可以採用篩選的結果陣列，並使用`For_each`迴圈對每個失敗執行操作。

以下範例 (隨後並有詳細說明) 會傳送 HTTP POST 要求，其中含有任何在 "My_Scope" 範圍內失敗之動作的回應本文：

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

以下是說明此範例中所發生情況的詳細逐步解說︰

1. 若要從 "My_Scope" 內的所有動作取得結果，**篩選陣列**動作會使用這個篩選條件運算式：`@result('My_Scope')`

1. **篩選器陣列**的條件是狀態等於`@result()``Failed`的任何項。 此條件會將具有 "My_Scope" 所有動作結果的陣列篩選成只剩失敗動作結果的陣列。

1. 對`For_each`*篩選的陣列*輸出執行迴圈操作。 此步驟會對之前篩選的每個失敗動作結果執行動作。

   如果作用域中的單個操作失敗，`For_each`則迴圈中的操作僅運行一次。 如果有多個失敗動作，則會導致對每個失敗執行一個動作。

1. 在`For_each`專案回應正文（運算式`@item()['outputs']['body']`）上發送 HTTP POST。

   `@result()` 項目圖形和 `@actions()` 圖形相同，並可透過相同方式剖析。

1. 包含兩個具有失敗動作名稱 (`@item()['name']`) 和失敗執行用戶端追蹤識別碼 (`@item()['clientTrackingId']`) 的自訂標頭。

供您參考，以下範例是單一 `@result()` 項目，會顯示前一個範例中剖析的 `name`、`body` 和 `clientTrackingId` 屬性。 在`For_each`操作之外，`@result()`返回這些物件的陣列。

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

若要執行不同的例外狀況處理模式，您可以使用本文前面描述的運算式。 您可以選擇在接受整個篩選的故障陣列的範圍之外執行單個異常處理操作，並刪除該`For_each`操作。 您還可以包括回應中的其他有用屬性，`\@result()`如前面所述。

## <a name="set-up-azure-monitor-logs"></a>設定 Azure 監視器記錄

上述模式非常適合處理執行內的錯誤和例外狀況，但您也可以識別和回應與執行本身無關的錯誤。 [Azure 監視器](../azure-monitor/overview.md)提供了一種將所有工作流事件（包括所有運行和操作狀態）發送到[日誌分析工作區](../azure-monitor/platform/data-platform-logs.md)[、Azure 存儲帳戶](../storage/blobs/storage-blobs-overview.md)或[Azure 事件中心](../event-hubs/event-hubs-about.md)的簡單方法。

若要評估執行狀態，您可以監視記錄和度量，或將它們發佈至您偏好使用的任何監視工具。 其中一個可能的選項是透過「事件中樞」將所有事件串流到 [Azure 串流分析](https://azure.microsoft.com/services/stream-analytics/)。 在「串流分析」中，您可以根據診斷記錄中的任何異常、平均或失敗，撰寫即時查詢。 您可以使用「串流分析」將資訊傳送到其他資料來源，例如傳送到佇列、主題、SQL、Azure Cosmos DB 或 Power BI。

## <a name="next-steps"></a>後續步驟

* [看看客戶如何使用 Azure Logic Apps 建置錯誤處理](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [查找更多邏輯應用示例和方案](../logic-apps/logic-apps-examples-and-scenarios.md)
