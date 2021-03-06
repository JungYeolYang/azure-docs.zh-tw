---
title: Azure 入口網站：SQL Database 動態資料遮罩 | Microsoft Docs
description: 如何開始在 Azure 入口網站中使用 SQL Database 動態資料遮罩
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 03/04/2018
ms.openlocfilehash: 3d5ab203268ced1951d2ba9c852ece5bd5467c68
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61077555"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>透過 Azure 入口網站開始使用 SQL Database 動態資料遮罩

本文說明如何使用 Azure 入口網站來實作[動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)。 您也可以使用 [Azure SQL Database Cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 或 [REST API](https://docs.microsoft.com/rest/api/sql/) 來實作動態資料遮罩。

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>使用 Azure 入口網站為您的資料庫設定動態資料遮罩

1. 在 [https://portal.azure.com](https://portal.azure.com) 上啟動 Azure 入口網站。
2. 導覽至您要遮罩處理的敏感性資料所在資料庫的設定頁面。
3. 按一下 [動態資料遮罩] 圖格，以啟動 [動態資料遮罩] 組態頁面。

   * 或者，您可以向下捲動至 [作業] 區段，然後按一下 [動態資料遮罩]。

     ![瀏覽窗格](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. 在 [動態資料遮罩] 設定頁面中，您可能會看到建議引擎已標示要進行遮罩處理的某些資料庫資料行。 若要接受建議，只要對一或多個資料行按一下 [新增遮罩] ，就會根據此資料行的預設類型建立遮罩。 您可按一下遮罩規則並編輯遮罩欄位格式，使其成為您選擇的不同格式，即可變更遮罩函數。 務必按一下 [儲存]  以儲存您的設定。

    ![瀏覽窗格](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. 若要為資料庫中的任何資料行新增遮罩，請在 [動態資料遮罩] 設定頁面的頂端，按一下 [新增遮罩] 以開啟 [新增遮罩規則] 設定頁面。

    ![瀏覽窗格](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. 請選取 [結構描述]、[資料表] 和 [資料行]，以定義要用於遮罩處理的指定欄位。
7. 從敏感性資料遮罩類別清單中，選擇 [遮罩欄位格式]  。

    ![瀏覽窗格](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. 按一下資料遮罩規則頁面中的 [儲存]，以更新動態遮罩原則中的遮罩資料規則集。
9. 輸入應從遮罩處理中排除，並可存取未經遮罩處理之敏感性資料的 SQL 使用者或 AAD 身分識別。 這應該是以分號分隔的使用者清單。 具有系統管理員權限的使用者永遠都有未經遮罩處理之原始資料的存取權。

    ![瀏覽窗格](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > 若要使应用程序层向应用程序特权用户显示敏感数据，请添加应用程序查询数据库时需要使用的 SQL 用户或 AAD 标识。 強烈建議此清單應包含最少的特殊權限使用者數目，以儘可能減少公開的敏感性資料。

10. 按一下資料遮罩組態頁面中的 [儲存]，以儲存新的或更新的遮罩原則。

## <a name="next-steps"></a>後續步驟

* 如需動態資料遮罩的概觀，請參閱[動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)。
* 还可以使用 [Azure SQL 数据库 cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 或 [REST API](https://docs.microsoft.com/rest/api/sql/) 实现动态数据掩码。
