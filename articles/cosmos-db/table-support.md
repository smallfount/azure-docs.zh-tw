---
title: Azure Cosmos DB 的 Azure 表格儲存體支援
description: 了解 Azure Cosmos DB 資料表 API 和 Azure 儲存體資料表如何藉由共用作業中的相同表格資料模型來一起運作
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 12/02/2019
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 82397e49e473b8660dfada54a0e05cafe0da4120
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76770674"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>使用 Azure Cosmos DB 資料表 API 和 Azure 表格儲存體進行開發

Azure Cosmos DB 資料表 API 和 Azure 表格儲存體共用同一個資料表資料模型，並透過本身的 SDK 公開相同的建立、刪除、更新和查詢作業。

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>使用 Azure Cosmos DB 資料表 API 進行開發

此時，[Azure Cosmos DB 資料表 API](table-introduction.md) 有四個 SDK 可供開發使用： 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)：.NET SDK。 此程式庫以 .NET Standard 為目標，且具有與公用 [Windows Azure 儲存體 SDK](https://www.nuget.org/packages/WindowsAzure.Storage) 相同的類別和方法簽章，但它也可以使用資料表 API 來連線到 Azure Cosmos DB 帳戶。 建議 .NET Framework 程式庫 [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) 的使用者升級至 [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)，因為前者處於維護模式，且即將被取代。

* [Python SDK](table-sdk-python.md)：新的 Azure Cosmos DB Python SDK 是唯一支援 Azure 表格儲存體 (Python) 的 SDK。 此 SDK 與 Azure 表格儲存體和 Azure Cosmos DB 資料表 API 連線。

* [Java SDK](table-sdk-java.md)：此 Azure 儲存體 SDK 能夠使用資料表 API 連線至 Azure Cosmos DB 帳戶。

* [Node.js SDK](table-sdk-nodejs.md)：此 Azure 儲存體 SDK 能夠使用資料表 API 連線至 Azure Cosmos DB 帳戶。


如需使用資料表 API 的其他資訊，請參閱[常見問題集：使用資料表 API 開發](faq.md#table)一文。

## <a name="developing-with-azure-table-storage"></a>使用 Azure 表格儲存體進行開發

Azure 表格儲存體有下列 SDK 可用於開發：

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/). 此程式庫可讓您使用表格儲存體服務。
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python)。 適用於 Python 的 Azure Cosmos DB 資料表 SDK 可支援表格儲存體服務 (因為 Azure 表格儲存體和 Cosmos DB 的資料表 API 共用相同的功能，為了找出 SDK 開發工作的因素，建議您使用此 SDK)。
- [Azure Storage SDK for Java](https://github.com/azure/azure-storage-java)。 此 Azure 儲存體 SDK 會在 Java 中提供用戶端程式庫，以取用 Azure 表格儲存體。
- [Node.js SDK](https://github.com/Azure/azure-storage-node)。 這個 SDK 提供 Node.js 套件和瀏覽器相容 JavaScript 用戶端程式庫，以取用儲存體資料表服務。
- [AzureRmStorageTable PowerShell 模組](https://www.powershellgallery.com/packages/AzureRmStorageTable). 這個 PowerShell 模組有 Cmdlet 可處理儲存體資料表。
- [Azure Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp/)。 此程式庫可讓您針對 Azure 儲存體建置應用程式。
- [適用於 Ruby 的 Azure 儲存體資料表用戶端程式庫](https://github.com/azure/azure-storage-ruby/tree/master/table)。 這個專案提供 Ruby 套件，可讓您輕鬆地存取 Azure 儲存體資料表服務。
- [Azure 儲存體資料表 PHP 用戶端程式庫](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table)。 這個專案提供 PHP 用戶端程式庫，可讓您輕鬆地存取 Azure 儲存體資料表服務。


   





