---
title: 在 Azure 宇宙 DB 查詢語言中的長度
description: 瞭解 Azure 宇宙 DB 中的 SQL 系統函數 LENGTH。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303710"
---
# <a name="length-azure-cosmos-db"></a>長度（Azure 宇宙資料庫）
 傳回指定字串運算式的字元數目。  
  
## <a name="syntax"></a>語法
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   要評估的字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回字串的長度。  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 以下為結果集。  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
