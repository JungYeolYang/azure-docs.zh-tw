---
title: 連線到商務用 OneDrive - Azure Logic Apps | Microsoft Docs
description: 使用商務用 OneDrive REST API 和 Azure Logic Apps上傳及管理檔案
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 87a162338cc67a3f2d2fc425bbcbefe2976661e5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104913"
---
# <a name="get-started-with-the-onedrive-for-business-connector"></a>開始使用 OneDrive for Business 連接器
連接到 OneDrive for Business 來管理您的檔案。 您可以對檔案執行各種動作，例如上傳、更新、取得及刪除。

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-onedrive-for-business"></a>建立 OneDrive for Business 的連線
若要使用 OneDrive for Business 建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料︰

| 屬性 | 必要項 | 描述 |
| --- | --- | --- |
| token |是 |提供 OneDrive for Business 認證 |

建立連線後，您就可以用它執行動作，並接聽本文所述的觸發程序。

> [!INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]
> 

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/onedriveforbusinessconnector/)的所有限制。

## <a name="more-connectors"></a>其他連接器
返回 [API 清單](apis-list.md)。