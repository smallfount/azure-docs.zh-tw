---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174583"
---
如果 Python 在啟動應用程式時發生錯誤，則只會傳回簡單的錯誤頁面 (例如，「無法顯示網頁，因為發生內部伺服器錯誤」)。

若要擷取 Python 應用程式錯誤：

1. 在 Azure 入口網站的 Web 應用程式中，選取 [設定]****。
2. 在 [設定]**** 索引標籤中，選取 [應用程式設定]****。
3. 在 [應用程式設定]**** 下，輸入下列索引鍵/值組：
    * 索引鍵：WSGI_LOG
    * 值：D:\home\site\wwwroot\logs.txt (輸入您選擇的檔案名稱)

您現在應該會看到 wwwroot 資料夾的 logs.txt 檔案中出現錯誤。
