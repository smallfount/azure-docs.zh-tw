---
title: 包含檔案
description: 包含檔案
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f7495c52e36bdc207145f17ec72eb57b7ebf1784
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76279618"
---
目前僅支援將 Azure Cosmos DB 繫結用於 SQL API。 對於其他所有的 Azure Cosmos DB API，您均應對您的 API 使用靜態用戶端，以從函式存取資料庫，包括 [Azure Cosmos DB 的 MongoDB 版 API](../articles/cosmos-db/mongodb-introduction.md)、[Cassandra API](../articles/cosmos-db/cassandra-introduction.md)、[Gremlin API](../articles/cosmos-db/graph-introduction.md) 和[資料表 API](../articles/cosmos-db/table-introduction.md)。
