---
title: Azure Cosmos DB 查詢語言中的 LOG10
description: 深入瞭解 Azure Cosmos DB 中的 LOG10 SQL 系統函數，以傳回指定數值運算式的以10為底數的對數
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302486"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 （Azure Cosmos DB）
 傳回指定之數值運算式的以 10 為基底的對數。  
  
## <a name="syntax"></a>語法
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expression*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="remarks"></a>備註
  
  LOG10 和 POWER 函數是彼此反向關聯。 例如，10 ^ LOG10(n) = n。  
  
## <a name="examples"></a>範例
  
  下列範例會宣告一個變數，並傳回指定變數 (100) 的 LOG10 值。  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 以下為結果集。  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>備註

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
