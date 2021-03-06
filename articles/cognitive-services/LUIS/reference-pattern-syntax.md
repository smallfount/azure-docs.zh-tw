---
title: 模式語法引用 - LUIS
titleSuffix: Azure Cognitive Services
description: 創建實體以從語言理解 （LUIS） 應用中的使用者話語中提取關鍵資料。 提取的資料由用戶端應用程式使用。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 696f4bdc22bed01a4b5be8bff63ade482a8dbe0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221281"
---
# <a name="pattern-syntax"></a>模式語法

模式語法是語句的範本。 此範本應該包含您要比對的字組和實體，以及您要忽略的字組和標點符號。 它**不是**一個規則運算式。

> [!CAUTION]
> 模式僅包括機器學習的實體父級，不包括子元件。

模式中的實體是用大括弧 `{}` 括住。 模式可以包含實體，以及具有角色的實體。 [模式.any](luis-concept-entity-types.md#patternany-entity)是僅在模式中使用的實體。

模式語法支援以下語法：

|函式|語法|巢狀層級|範例|
|--|--|--|--|
|實體| {}- 捲曲括弧|2|表單 [機構名稱] 在哪裡？|
|選用|* - 方括弧<BR><BR>可選和分組任意組合的嵌套級別限制為 3 |2|問號是可選的 [？]|
|群組|（） - 括弧|2|是 （a \| b）|
|或| \|- 分隔號（管）<br><br>一組中的分隔號（Or）有 2 的限制 |-|表單（表單名稱-短） &#x7c; [形式名稱長] &#x7c; [表單編號]）|
|話語的開始和/或結束|• - 小心|-|*開始話語<br>話語完成。<br>[整個話語與 [數位] 實體的嚴格文本匹配|

## <a name="nesting-syntax-in-patterns"></a>在模式中嵌套語法

**可選**語法（帶方括弧）可以嵌套兩個級別。 例如：`[[this]is] a new form`。 此示例允許以下表述：

|嵌套可選話語示例|說明|
|--|--|
|這是一個新的形式|匹配模式中的所有單詞|
|是一種新的形式|在模式中匹配外部可選單詞和非可選單詞|
|新表單|僅匹配所需單詞|

具有括弧的**分組**語法可以嵌套兩個級別。 例如：`(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`。 此功能允許匹配三個實體中的任何一個。

如果 Entity1 是具有源（西雅圖）和目標（開羅）和實體 2 等角色的位置，則實體 2 是從清單實體 （RedWest-C） 的已知建築名稱，則以下陳述將映射到此模式：

|嵌套分組陳述示例|說明|
|--|--|
|紅色西-C|匹配外部分組實體|
|Seattle|匹配其中一個內部分組實體|
|Cairo|匹配其中一個內部分組實體|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>具有可選語法的組的嵌套限制

**分組**與**可選**語法的組合具有 3 個嵌套級別的限制。

|允許|範例|
|--|--|
|是|（ 測試1&#x7c;測試2 ） = &#x7c;測試3 ）|
|否|（ [ ] 測試1 [ &#x7c;測試2 ） [ &#x7c;測試3 ）|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>使用 or ing 語法的組的嵌套限制

與**or-ing**語法**組合**的組合限制為 2 個分隔號形。

|允許|範例|
|--|--|
|是|（ 測試1 &#x7c;測試2 &#x7c; （ 測試3 &#x7c;測試4 ） ）|
|否|（ test1 &#x7c;測試2 &#x7c;測試3 &#x7c; （ 測試4 &#x7c;測試5 ） ） |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>將實體新增至模式範本的語法
若要將實體新增至模式範本，請用大括弧括住實體名稱，例如 `Who does {Employee} manage?`。

|模式與實體|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>將實體和角色新增至模式範本的語法
實體角色以 `{entity:role}` 的形式表示，其格式為實體名稱後接冒號，然後接著角色名稱。 若要將具有角色的實體新增至模式範本，請用大括弧括住實體名稱和角色名稱，例如 `Book a ticket from {Location:Origin} to {Location:Destination}`。

|模式與實體角色|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>將 Pattern.any 新增至模式範本的語法
Pattern.any 實體可讓您將變動長度的實體新增至模式。 只要遵循模式範本，Pattern.any 就可以是任意長度。

若要將 **Pattern.any** 實體新增至模式範本，請用大括弧括住 Pattern.any 實體，例如 `How much does {Booktitle} cost and what format is it available in?`。

|模式與 Pattern.any 實體|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|模式中的書名|
|--|
|How much does **steal this book** cost and what format is it available in?|
|How much does **ask** cost and what format is it available in?|
|How much does **The Curious Incident of the Dog in the Night-Time** cost and what format is it available in?|

書名的字詞對 LUIS 並不感到困惑，因為 LUIS 知道書名的結尾位置，基於 Pattern.任何實體。

## <a name="explicit-lists"></a>明確清單

通過創作 API 創建[顯式清單](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)，以便在以下時間允許異常：

* 您的模式包含一個[模式。](luis-concept-entity-types.md#patternany-entity)
* 並且，這種模式語法允許基於陳述的錯誤實體提取的可能性。

例如，假設您有一個模式，其包含選用語法 `[]` 和實體語法 `{}`，且以錯誤擷取資料的方式合併在一起。

請考慮使用模式 `[find] email about {subject} [from {person}]'。

在下列語句中，系統會正確及錯誤地擷取 **subject** 和 **person** 實體：

|語句|單位|正確擷取|
|--|--|:--:|
|email about dogs from Chris|subject=dogs<br>person=Chris|✔|
|email about the man from La Mancha|subject=the man<br>person=La Mancha|X|

在上表中，主題應為`the man from La Mancha`（書名），但由於主題包含可選詞`from`，因此對標題的預測不正確。

若要修正模式的這個例外狀況，請使用[撰寫明確清單的 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)，新增 `the man from la mancha` 作為符合 {subject} 實體的明確清單。

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>在範本語句中標記選用文字的語法
您可以使用規則運算式的方括弧語法 `[]`，在語句中標記選用文字。 選用文字最多只能在方括弧中套嵌兩個大括弧。

|模式與選用文字|意義|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`是`from {person}`可選的|
|"你能幫我嗎？|標點符號是可選的|

標點符號`?`（，）`.``!`應忽略，您需要在模式中使用方括弧語法忽略它們。

## <a name="next-steps"></a>後續步驟

瞭解有關模式的更多詳細資訊：

* [如何添加模式](luis-how-to-model-intent-pattern.md)
* [如何添加模式. 任何實體](luis-how-to-add-entities.md#add-a-patternany-entity)
* [模式概念](luis-concept-patterns.md)

瞭解在 .json 回應中如何返回[情緒](luis-reference-prebuilt-sentiment.md)。