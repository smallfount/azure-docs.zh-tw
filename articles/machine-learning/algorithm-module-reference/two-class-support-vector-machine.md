---
title: 雙類支援向量機：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用**雙類支援向量機**模組創建基於支援向量機演算法的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ba788518951e72c1701d99decf46350e8665dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455803"
---
# <a name="two-class-support-vector-machine-module"></a>雙類支援向量機模組

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用此模組可以創建基於支援向量機演算法的模型。 

支援向量機 （SVM） 是一類研究良好的監督學習方法。 這種特定實現適用于根據連續變數或分類變數預測兩種可能的結果。

定義模型參數後，請使用訓練模組訓練模型，並提供包含標籤或結果列*的標記資料集*。

## <a name="about-support-vector-machines"></a>關於支援向量機

支援向量機器是其中一個最早的機器學習演算法，而且在許多應用程式 (從資訊擷取到文字和影像分類) 中，都已使用 SVM 模型。 SVM 可用於分類和回歸任務。

此 SVM 模型是一個監督學習模型，需要標記的資料。 在訓練過程中，該演算法分析輸入資料，識別稱為*超平面*的多維特徵空間中的模式。  所有輸入示例在此空間中表示為點，並映射到輸出類別，使類別除以盡可能寬且清除間隙。

為了預測，SVM 演算法將新示例分配給一個類別或另一個類別，將它們映射到同一空間。 

## <a name="how-to-configure"></a>如何設定 

對於此模型類型，建議您在使用資料集訓練分類器之前對資料集進行正常化。
  
1.  將**雙類支援向量機**模組添加到管道中。  
  
2.  通過設置 **"創建培訓師模式"** 選項，指定如何訓練模型。  
  
    -   **單一參數**：如果您知道如何配置模型，則可以提供一組特定的值作為參數。  

    -   **參數範圍**：如果您不確定最佳參數，則可以使用[Tune 模型 Hyper 參數](tune-model-hyperparameters.md)模組找到最佳參數。 提供一些值範圍，培訓師會對多個設置組合進行遍舍，以確定生成最佳結果的值的組合。

3.  對於**反覆運算次數**，鍵入表示生成模型時使用的反覆運算次數的數位。  
  
     這個參數可以用來控制定型速度和精確性之間的取捨。  
  
4.  對於**Lambda，** 鍵入一個值，用作 L1 正規化的權重。  
  
     這個正則化係數可用來調整模型。 值越大，模型就越複雜。  
  
5.  如果要在訓練前正常化功能，請選擇"**正常化要素**"選項。
  
     如果應用正常化，則在訓練之前，資料點以平均值為中心，並縮放為具有一個標準差的單位。
  
6.  選擇選項"**投影到單位球體**"以正常化係數。
  
     將值投影到單位空間意味著在訓練之前，資料點居中于 0，並縮放為具有一個標準差的單位。
  
7.  在**亂數種子中**，鍵入一個整數值，用於作為種子，如果要確保跨運行的可重複性。  否則，系統時鐘值將用作種子，這可能導致跨運行的結果略有不同。
  
9. 連接標記的資料集以及其中一個[訓練模組](module-reference.md)：
  
    -   如果將 **"創建訓練器模式"** 設置為 **"單一參數**"，請使用[訓練器模型](train-model.md)模組。
  
10. 提交管道。

## <a name="results"></a>結果

培訓完成後：

+ 要保存已訓練的模型的快照，請在 **"訓練"模型**模組的右側面板中選擇 **"輸出**"選項卡。 選擇 **"註冊"資料集**圖示以將模型保存為可重用模組。

+ 要使用模型進行評分，請使用 **"分數模型"** 模組添加到管道中。


## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 