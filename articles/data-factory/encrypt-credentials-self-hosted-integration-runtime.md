---
title: 加密 Azure 資料工廠中的憑據
description: 了解如何在含自我裝載整合執行階段的電腦上加密和儲存內部部署資料存放區的認證。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 67ba2fadd5376997b528af4fcd2c5a666bb134a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443992"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>在 Azure Data Factory 中加密內部部署資料存放區的認證
您可以在含自我裝載整合執行階段的電腦上加密和儲存內部部署資料存放區的認證 (含敏感性資訊的連結服務)。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您會將具有認證的 JSON 定義檔傳遞至 <br/>[**New-AzDataFactoryV2LinkService加密憑據**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential)Cmdlet，用於生成具有加密憑據的輸出 JSON 定義檔。 然後，使用更新的 JSON 定義來建立連結服務。

## <a name="author-sql-server-linked-service"></a>編寫 SQL Server 連結服務
在任何資料夾中，使用下列內容建立名為 **SqlServerLinkedService.json** 的 JSON 檔案：  

先將 `<servername>`、`<databasename>`、`<username>` 和 `<password>` 取代為 SQL Server 的值，再儲存檔案。 並且，將 `<integration runtime name>` 取代為您整合執行階段的名稱。 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>加密認證
要加密本地自託管集成運行時來自 JSON 負載的敏感性資料，請運行**New-AzDataFactoryV2LinkServiceEncrypt憑據**，並傳遞 JSON 有效負載。 這個 Cmdlet 確保使用 DPAPI 來加密認證，並將其本機儲存在自我裝載整合執行階段節點上。 包含對憑據的加密引用的輸出負載可以重定向到另一個 JSON 檔（在本例中為"加密的LinkedService.json"）。

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>搭配使用 JSON 與加密認證
現在，使用包含加密認證之前一個命令的輸出 JSON 檔案來設定 **SqlServerLinkedService**。

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>後續步驟
如需資料移動安全性考量的資訊，請參閱[資料移動安全性考量](data-movement-security-considerations.md)。

