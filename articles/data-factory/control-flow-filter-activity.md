---
title: Azure Data Factory 中的篩選活動
description: 篩選活動會篩選輸入。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/04/2018
ms.openlocfilehash: 5794a2c754e2082fdf45ad689bdb89ea494541c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73679863"
---
# <a name="filter-activity-in-azure-data-factory"></a>Azure Data Factory 中的篩選活動
您可以在管線中使用篩選活動，將篩選運算式套用至輸入陣列。 

## <a name="syntax"></a>語法

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
NAME | `Filter` 活動的名稱。 | String | 是
type | 必須設定為**篩選**。 | String | 是
condition (條件) | 要用來篩選輸入的條件。 | 運算是 | 是
項目 | 應套用篩選條件的輸入陣列。 | 運算是 | 是

## <a name="example"></a>範例

在此範例中，管線有兩個活動：**篩選**與 **ForEach**。 篩選活動設定為會對值大於 3 之項目的輸入陣列進行篩選。 然後，"ForEach"活動遍越對篩選的值進行遍向，並將變數**測試**設置為當前值。

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
            "name": "MyForEach",
            "type": "ForEach",
            "dependsOn": [
                {
                    "activity": "MyFilterActivity",
                    "dependencyConditions": [
                        "Succeeded"
                    ]
                }
            ],
            "userProperties": [],
            "typeProperties": {
                "items": {
                    "value": "@activity('MyFilterActivity').output.value",
                    "type": "Expression"
                },
                "isSequential": "false",
                "batchCount": 1,
                "activities": [
                    {
                        "name": "Set Variable1",
                        "type": "SetVariable",
                        "dependsOn": [],
                        "userProperties": [],
                        "typeProperties": {
                            "variableName": "test",
                            "value": {
                                "value": "@string(item())",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        }],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        },
        "variables": {
            "test": {
                "type": "String"
            }
        },
        "annotations": []
    }
}
```

## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動： 

- [If 條件活動](control-flow-if-condition-activity.md)
- [執行管道活動](control-flow-execute-pipeline-activity.md)
- [對於每個活動](control-flow-for-each-activity.md)
- [獲取中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
