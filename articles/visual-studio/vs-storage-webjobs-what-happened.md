---
title: 我的 WebJob 專案（視覺化工作室 Azure 存儲）發生了什麼？
description: 描述使用 Visual Studio 連接的服務連接到存儲帳戶後 Azure WebJob 專案中發生的情況
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17861b7c25dfaf9bc9399e5261cdf2a5b43caf21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298739"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>我的 WebJob 專案 (Visual Studio Azure 儲存體連接的服務) 發生什麼狀況？
## <a name="references-added"></a>加入參考
Azure 儲存體 NuGet 封裝已加入至 Visual Studio 專案或在其中更新。  
這個封裝會加入下列 .NET 參考：

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **系統.資料**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>加入 Azure 儲存體的連接字串
在專案的 App.config 檔案中，已使用所選儲存體帳戶的連接字串和金鑰更新 **AzureWebJobsStorage** 和 **AzureWebJobsDashboard** 項目。

如需詳細資訊，請參閱 [Azure WebJobs 文件資源](https://go.microsoft.com/fwlink/?linkid=390226)。

