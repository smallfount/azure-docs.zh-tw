---
title: 從 Azure 媒體服務 v2 遷移至 v3 | Microsoft Docs
description: 本文說明 Azure 媒體服務 v3 中所導入的變更，並說明兩個版本之間的差異。 該文章也提供從媒體服務 v2 移至 v3 的移轉指導。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: Azure 媒體服務, 串流, 廣播, 即時, 離線
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 72d413c5d8bc982d885d889da35b29a3607410cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472062"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>從媒體服務 v2 移動至 v3 的移轉指導

>通過複製和粘貼此 URL：`https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us`到 RSS 源閱讀器，獲得有關何時重新訪問此頁面以進行更新的通知。

本文提供從媒體服務 v2 到 v3 的遷移指南。

如果您現今已經在[舊版媒體服務 v2 API](../previous/media-services-overview.md) 上開發影片服務，您應該先檢閱下列指導方針和考量，然後再移轉至 v3 API。 v3 API 中有許多優點和新功能，可改善媒體服務的開發人員經驗和功能。 不過，如本文[已知問題](#known-issues)一節中所提出，由於 API 版本之間的變更，因此也有一些限制。 此頁面將經過維護，因為媒體服務團隊會一直持續不斷地改進 v3 API，並解決版本之間的差距。 

## <a name="prerequisites"></a>Prerequisites

* 查看[媒體服務 v2 vs. v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>媒體服務 v3 的優點
  
### <a name="api-is-more-approachable"></a>API 會更平易近人

*  v3 是會統一的 API 表面為基礎，併公開建置於 Azure Resource Manager 上的管理和操作功能。 Azure Resource Manager 範本可用於建立及部署轉換、串流端點、即時事件等等。
* [OpenAPI 規範（以前稱為斯瓦格）](https://aka.ms/ams-v3-rest-sdk)文檔。
    公開所有服務元件的結構描述，包括檔案型編碼。
* SDK 適用於 [.NET](https://aka.ms/ams-v3-dotnet-ref)、.Net Core、[Node.js](/javascript/api/overview/azure/mediaservices/management)、[Python](https://aka.ms/ams-v3-python-ref)、[Java](https://aka.ms/ams-v3-java-ref)、[Go](https://aka.ms/ams-v3-go-ref) 和 Ruby。
* [Azure CLI](https://aka.ms/ams-v3-cli-ref) 整合，適用於簡單的指令碼支援。

### <a name="new-features"></a>新功能

* 對於檔案型作業處理，您可以使用 HTTP(S) URL 作為輸入。<br/>您不需要已經將內容儲存在 Azure 中，也不需要建立 Asset。
* 介紹檔案型作業處理的 [Transform](transforms-jobs-concept.md) 概念。 Transform 可用來建置可重複使用的組態、建立 Azure Resource Manager 範本，並隔離多個客戶或租用戶之間的處理設定。
* 一個 Asset 可以有多個[串流定位器](streaming-locators-concept.md)，各自具有不同的[動態封裝](dynamic-packaging-overview.md)與「動態加密」設定。
* [內容保護](content-key-policy-concept.md)支援多鍵功能。
* 使用媒體服務將單一位元速率貢獻饋送轉碼為有多個位元速率的輸出資料流時，您可以串流處理長達 24 小時的即時事件。
* 即時事件上新的低延遲即時串流處理支援。 如需詳細資訊，請參閱[延遲](live-event-latency.md)。
* 即時事件預覽版可支援[動態封裝](dynamic-packaging-overview.md)與「動態加密」。 這可在預覽版上實現內容保護，以及 DASH 和 HLS 封裝。
* 即時輸出在使用上比 v2 API 中的 Program 實體更簡單。 
* 改進的 RTMP 支援 (更高的穩定性及更多來源編碼器支援)。
* RTMPS 安全內嵌。<br/>當您建立即時事件時，您會取得 4 個內嵌 URL。 4 個內嵌 URL 幾乎完全相同，並有相同的串流權杖 (AppId)，只有連接埠號碼部分不同。 其中兩個 URL 是 RTMPS 的主要部分和備份。   
* 您對實體已經有角色型存取控制 (RBAC)。 

## <a name="known-issues"></a>已知問題

*  目前，可以使用 Azure[門戶](https://portal.azure.com/)：

    * 管理媒體服務 v3[即時事件](live-events-outputs-concept.md)， 
    * 視圖（未管理） v3[資產](assets-concept.md)， 
    * [獲取有關訪問 API 的資訊](access-api-portal.md)。 

    對於所有其他管理工作（例如，[轉換、作業](transforms-jobs-concept.md)和[內容保護](content-protection-overview.md)），請使用[REST API](https://docs.microsoft.com/rest/api/media/) [、CLI](https://aka.ms/ams-v3-cli-ref)或受支援的[SDK](media-services-apis-overview.md#sdks)之一。
* 您需要在您的帳戶中佈建媒體保留單位 (MRU)，才能控制作業的並行和效能，尤其是與視訊或音訊分析相關的作業。 如需詳細資訊，請參閱[調整媒體處理](../previous/media-services-scale-media-processing-overview.md)。 您可以使用[CLI 2.0 管理 MrUs，用於媒體服務 v3、](media-reserved-units-cli-how-to.md)使用[Azure 門戶](../previous/media-services-portal-scale-media-processing.md)或使用[v2 API。](../previous/media-services-dotnet-encoding-units.md) 不論您是使用媒體服務 v2 或 v3 API，都需要佈建 MRU。
* 使用 v3 API 建立的媒體服務實體無法由 v2 API 管理。  
* V2 API 中的所有實體都未自動顯示在 V3 API 中。  以下是兩個版本中不相容的實體示例：  
    * 在 v2 中建立的 Job 和 Task 不會出現在 v3 中，因為它們與 Transform 沒有關聯。 建議是切換至 v3 Transform 和 Job。 在轉換期間將會有一段相當短的時間需要監視傳遞 v2 Job。
    * 使用 v2 建立的通道和程式 (這會對應到 v3 中的即時事件和即時輸出) 無法繼續使用 v3 管理。 建議在方便的通道停止時，切換至 v3 即時事件和即時輸出。<br/>目前您無法移轉持續執行的通道。  

> [!NOTE]
> 此頁面將經過維護，因為媒體服務團隊會一直持續不斷地改進 v3 API，並解決版本之間的差距。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

[教程：基於 URL 對遠端檔進行編碼並資料流視頻 - .NET](stream-files-dotnet-quickstart.md)
