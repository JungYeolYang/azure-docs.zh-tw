---
title: 在 SQL 資料倉儲中使用標籤來檢測查詢 | Microsoft Docs
description: 在 Azure SQL 資料倉儲中使用標籤來檢測查詢以開發解決方案的秘訣。
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
origin.date: 04/17/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.openlocfilehash: 5c60a7dc44471599834c4b1225b397c8e6eabbd5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439769"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>在 Azure SQL 資料倉儲中使用標籤來檢測查詢
在 Azure SQL 資料倉儲中使用標籤來檢測查詢以開發解決方案的秘訣。


## <a name="what-are-labels"></a>什麼是標籤？
SQL 資料倉儲支援稱為查詢標籤的概念。 繼續進行之前，讓我們看看一個範例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最後一行將字串 'My Query Label' 標記為查詢。 此標籤特別有幫助，因為標籤可透過 DMV 查詢。 查詢標籤提供可找出問題查詢的機制，並可協助透過 ELT 執行識別進度。

良好的命名慣例非常有幫助。 例如，以 PROJECT、PROCEDURE、STATEMENT 或 COMMENT 標籤為開頭，有助於在原始檔控制中的所有程式碼唯一識別查詢。

下列查詢會使用動態管理檢視以依標籤來搜尋。

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> 查詢時，請務必將方括弧或雙引號放在文字標籤兩側。 標籤是一個保留字，不以符號分隔時會導致發生錯誤。 
> 
> 

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。


