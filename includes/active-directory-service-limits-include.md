---
title: 包含檔案
description: 包含檔案
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/21/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 38f2dd301ddc2a5f8d28322856b2011bd2034c30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60472210"
---
以下是 Azure Active Directory (Azure AD) 服務的使用條件約束和其他服務限制。

| 類別 | 限制 |
| --- | --- |
| 目錄 | 單一使用者能以成員或來賓的身分，隸屬於最多 500 個 Azure AD 目錄。<br/>單一使用者最多可以建立 20 個目錄。 |
| 網域 | 您可以新增 900 個以內的受控網域名稱。 如果您要設定所有網域與內部部署 Active Directory 建立同盟，則可以在每個目錄中新增 450 個以內的網域名稱。 |
| 物件 |<ul><li>在免費版的 Azure Active Directory 中，最多可以在單一目錄中建立 500,000 個物件。</li><li>非系統管理員的使用者最多可以建立 250 個物件。 此配額可還原的計數包括作用中的物件和已刪除的物件。 只可還原 30 天內刪除的已刪除物件。 此配額無法還原的已刪除物件計數值為 30 天的四分之一。 您可以[將系統管理員角色指派給非系統管理員使用者](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md)，他們會因為需要進行的日常工作而重覆超過此配額。</li></ul> |
| 結構描述延伸模組 |<ul><li>字串類型延伸模組最多可以包含 256 個字元。 </li><li>二進位類型延伸模組受限於 256 個位元組。</li><li>任何單一物件均可寫入 100 個延伸模組值，包括所有類型和所有應用程式皆可。</li><li>只能使用「字串」類型或「二進位」類型單一值屬性來擴充 User、Group、TenantDetail、Device、Application 和 ServicePrincipal 實體。</li><li>結構描述延伸模組僅適用於圖形 API 版本 1.21 預覽。 應用程式必須取得寫入權限才能登錄延伸模組。</li></ul> |
| [應用程式] |最多 100 個使用者可以成為單一應用程式的擁有者。 |
| 群組 |<ul><li>最多 100 個使用者可以成為單一群組的擁有者。</li><li>任何數目的物件都可以是單一群組的成員。</li><li>使用者可以是任意數目群組的成員。</li><li>可以使用 Azure AD Connect 從您的內部部署 Active Directory 同步至 Azure Active Directory 群組中的成員數目限制為 5 萬個成員。</li></ul> |
| 存取面板 |<ul><li>每位使用者在存取面板中可以看到的應用程式數目沒有限制。 這適用於已指派 Azure AD Premium 或 Enterprise Mobility Suite 授權的使用者。</li><li>每位使用者可在存取面板中看到最多 10 個應用程式圖格。 這項限制適用於已指派「免費」或 Azure Active Directory 的 Azure AD Basic 版本授權的使用者。 應用程式圖格範例包括 Box、Salesforce 或 Dropbox。 此限制不適用於系統管理員帳戶。</li></ul> |
| 報告 | 在任何報告中，最多可以檢視或下載 1000 個資料列。 任何其他資料會遭到截斷。 |
| 管理單位 | 物件可以是有不超過 30 個管理單位的成員。 |
| 系統管理員角色與權限 | <li>無法將群組新增為擁有者。<li>無法將群組指派給角色。<li>除非切換租用戶，否則無法變更預設的使用者權限。您可以在 Azure AD 中的使用者設定進行此動作。 |
