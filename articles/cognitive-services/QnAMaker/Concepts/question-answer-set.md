---
title: 設計知識庫 - QnA 製造商
description: QnA Maker 知識庫由一組問答 （QnA） 集和與每個 QnA 對關聯的可選中繼資料組成。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76846372"
---
# <a name="question-and-answer-set-concepts"></a>問答集概念

知識庫由問答 （QnA） 集組成。  每個集都有一個答案，一個集包含與該_答案_關聯的所有資訊。 答案可以大致類似于資料庫行或資料結構實例。

## <a name="question-and-answer-sets"></a>問答集

問答 （QnA） 集中**所需的**設置包括：

* **一個問題**- 使用者查詢文本，用於 QnA Maker 的機器學習，以不同的措辭但相同的答案與使用者問題文本保持一致
* **答案**- 集的答案是在使用者查詢與關聯的問題匹配時返回的回應

每組由**ID**表示。

集的**可選**設置包括：

* **問題的替代方法 -** 這有助於 QnA Maker 返回對更多問題短語的正確答案
* **中繼資料**：中繼資料是與 QnA 對關聯的標記，表示為鍵值對。 中繼資料標記用來篩選 QnA 配對，並限制執行查詢比對的集合。
* **多轉提示**，用於繼續多轉對話

![QnA Maker 知識庫](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>透過編輯來新增至知識庫

如果沒有預先存在的內容來填充知識庫，則可以在 QnA Maker 門戶中以編輯方式添加 QnA 集。 在[這裡](../How-To/edit-knowledge-base.md)了解如何更新知識庫。

## <a name="editing-your-knowledge-base-locally"></a>在本機編輯您的知識庫

在建立知識庫之後，建議您在 [QnA Maker 入口網站](https://qnamaker.ai)中對知識庫文字進行編輯，而不要透過本機檔案匯出和重新匯入。 不過，有時您可能需要在本機編輯知識庫。

請從 [設定]**** 頁面匯出知識庫，然後使用 Microsoft Excel 來編輯知識庫。 如果選擇使用其他應用程式編輯匯出的檔，則應用程式可能會引入語法錯誤，因為它並不完全符合 TSV。 Microsoft Excel 的 TSV 檔案通常不會造成任何格式設定錯誤。

完成編輯之後，請從 [設定]**** 檔案重新匯入 TSV 檔案。 這會以匯入的知識庫完全取代目前的知識庫。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker 中的知識庫生命週期](./development-lifecycle-knowledge-base.md)