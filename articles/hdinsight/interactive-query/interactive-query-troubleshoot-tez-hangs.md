---
title: Apache Tez 應用程式懸掛在 Azure HDInsight 中
description: Apache Tez 應用程式懸掛在 Azure HDInsight 中
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: ec5a0d6e8c0a5236ae3929560e81033d983d4dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895120"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>方案：Apache Tez 應用程式懸掛在 Azure HDInsight 中

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

提交 Apache Hive 作業後，從 Tez 視圖中作業狀態為"正在運行"，但似乎未取得任何進展

## <a name="cause"></a>原因

提交的作業太多;長紗佇列。

## <a name="resolution"></a>解決方案

向上擴展群集，或等待 Yarn 佇列耗盡。

預設情況下`yarn.scheduler.capacity.maximum-applications`，控制正在運行的或掛起的應用程式的最大數量，它預設為`10000`。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
