---
title: Azure SQL Database 動態資料遮罩 | Microsoft Docs
description: SQL Database 動態資料遮罩可藉由遮罩處理，使不具權限的使用者無法看見機密資料
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
ms.date: 03/04/2019
ms.openlocfilehash: 2be1f7bb6417834196da03b7cba22387744f8c19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075534"
---
# <a name="sql-database-dynamic-data-masking"></a>SQL Database 動態資料遮罩

SQL Database 動態資料遮罩可藉由遮罩處理，使不具權限的使用者無法看見機密資料。 

動態資料遮罩可讓客戶在應用程式層級受到最小影響的情況下指定要顯示多少機密資料，而協助防止未經授權者存取機密資料。 它是以原則為基礎的安全性功能，可針對指定的資料庫欄位隱藏查詢結果集中的機密資料，而不變更資料庫中的資料。

例如，客服中心服務代表可透過信用卡號碼的幾個數字來識別來電者，但這些資料項目不應完全公開給服務代表。 可以定義遮罩規則，以針對任何查詢的結果集中任何信用卡號碼的末四碼以外的所有數字進行遮罩處理。 另举一例，通过定义适当的数据掩码来保护个人身份信息 (PII) 数据，开发人员一方面可以查询生产环境以进行故障排除，同时又不违反法规遵从性要求。

## <a name="sql-database-dynamic-data-masking-basics"></a>SQL Database 動態資料遮罩的基本概念

您可以在 Azure 入口網站的 SQL Database [組態] 刀鋒視窗或 [設定] 刀鋒視窗中，選取 [動態資料遮罩] 作業，來設定動態資料遮罩原則。

### <a name="dynamic-data-masking-permissions"></a>動態資料遮罩權限

動態資料遮罩可由 Azure SQL Database 管理員、伺服器管理員或 [SQL 安全性管理員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)等角色來設定。

### <a name="dynamic-data-masking-policy"></a>動態資料遮罩原則

* **從遮罩處理中排除的 SQL 使用者** - 一組 SQL 使用者或 AAD 身分識別，可在 SQL 查詢結果中取得未經遮罩處理的資料。 具有系統管理員權限的使用者永遠會從遮罩處理中排除，而且會看到沒有任何遮罩的原始資料。
* **遮罩規則** - 一組規則，定義會遮罩處理的指定欄位和所使用的遮罩函數。 指定的欄位可使用資料庫結構描述名稱、資料表名稱和資料行名稱來定義。
* **掩码函数** - 一组方法，用于控制不同情况下的数据透露。

| 遮罩函數 | 遮罩邏輯 |
| --- | --- |
| **預設值** |**根據指定欄位的資料類型進行完整遮罩**<br/><br/>• 如果字串資料類型的欄位大小少於 4 個字元 (nchar、ntext、nvarchar)，請使用 XXXX 或更少 X。<br/>• 針對數值資料類型 (bigint、bit、decimal、int、money、numeric、smallint、smallmoney、tinyint、float、real)，使用零值。<br/>• 針對日期/時間資料類型 (date、datetime2、datetime、datetimeoffset、smalldatetime、time)，使用 01-01-1900 時間。<br/>• 對於 SQL 變數，會使用目前類型的預設值。<br/>• 对于 XML，使用文档 <masked/>。<br/>• 針對特殊資料類型 (時間戳記、資料表、hierarchyid、GUID、二進位值、影像、varbinary spatial 類型)，使用空值。 |
| **信用卡** |**遮罩方法會公開指定欄位的末四碼**，並新增常數字串做為信用卡格式的前置詞。<br/><br/>XXXX-XXXX-XXXX-1234 |
| **电子邮件** |**遮罩方法會公開第一個字母並以 XXX.com 取代網域**，使用的常數字串前置詞會以電子郵件地址為格式。<br/><br/>aXX@XXXX.com |
| **隨機數字** |**遮罩方法會產生一個隨機數字**，其根據為選取的界限與實際資料類型。 如果指定的邊界相等，則遮罩函數是常數。<br/><br/>![导航窗格](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **自定义文本** |**遮罩方法會公開第一個和最後一個字元**，並在中間新增自訂填補字串。 如果原始字串小於公開的前置詞和後置詞，則只會使用填補字串。 <br/>prefix[padding]suffix<br/><br/>![導覽窗格](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>要遮罩處理的建議欄位

DDM 建議引擎會將您資料庫中的特定欄位標示為潛在敏感性欄位，而這類欄位可能適合進行遮罩處理。 在入口網站的 [動態資料遮罩] 刀鋒視窗中，您會看到您的資料庫的建議資料行。 您只需要對一或多個資料行按一下 [新增遮罩]，然後按一下 [儲存]，以對這些欄位套用遮罩。

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>使用 PowerShell cmdlet 为数据库设置动态数据掩码

請參閱 [Azure SQL Database Cmdlet](https://docs.microsoft.com/powershell/module/az.sql)。

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>使用 REST API 為您的資料庫設定動態資料遮罩

請參閱 [Azure SQL Database 的作業](https://docs.microsoft.com/rest/api/sql/) \(英文\)。
