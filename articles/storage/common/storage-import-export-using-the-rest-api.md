---
title: 使用 Azure 匯入/匯出服務 REST API | Microsoft Docs
description: 了解可以使用 Azure 匯入/匯出服務 REST API 在何處尋找資源，包括使用說明及參考資料。
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978861"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>了解如何使用 Azure 匯入/匯出服務 REST API

Microsoft Azure 匯入/匯出服務會公開 REST API，以啟用程式設計方式控制匯入/匯出作業。 您可以使用 REST API 來執行您可以使用 [Azure 入口網站](https://portal.azure.com/)執行的所有匯入/匯出作業。 此外，您可以使用 REST API 來執行某些細微作業，例如查詢作業的完成百分比，此作業目前在 Azure 入口網站中無法使用。

請參閱[使用 Microsoft Azure 匯入/匯出服務將資料移轉至 Blob 儲存體](../storage-import-export-service.md)以取得匯入/匯出服務概觀及示範如何使用入口網站建立和管理匯入與匯出作業的教學課程。

## <a name="service-endpoints"></a>服務端點

Azure 匯入/匯出服務是 Azure Resource Manager 的資源提供者，並在下列 HTTPS 端點提供一組 REST API 以管理匯入/匯出作業︰

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>版本控制

匯入/匯出服務的要求必須指定 `api-version` 參數並將其值設定為 `2016-11-01`。

## <a name="importexport-service-operations"></a>匯入/匯出服務作業

[創建導入作業](../storage-import-export-creating-an-import-job.md)

[建立匯出作業](../storage-import-export-creating-an-export-job.md)

[擷取作業的狀態資訊](storage-import-export-retrieving-state-info-for-a-job.md)

[列舉作業](../storage-import-export-enumerating-jobs.md)

[取消和刪除作業](storage-import-export-cancelling-and-deleting-jobs.md)

[備份磁碟機清單](../storage-import-export-backing-up-drive-manifests.md)

[導入/匯出作業的診斷和錯誤恢復](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>後續步驟

* [儲存體匯入/匯出 REST](/rest/api/storageimportexport)
