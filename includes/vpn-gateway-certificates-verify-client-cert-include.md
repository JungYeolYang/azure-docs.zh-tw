---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 12/11/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319983"
---
如果您連線時遇到問題，請檢查下列項目︰

- 如果您已使用**憑證匯出精靈**來匯出用戶端憑證，請確定您已將其匯出為 .pfx 檔案，並且已選取 [如果可能的話，包含憑證路徑中的所有憑證]。 當您使用此值匯出它時，根憑證資訊也會一併匯出。 當您在用戶端電腦上安裝憑證後，.pfx 檔案中的根憑證也會一併安裝。 若要確認是否已安裝根憑證，可開啟 [管理使用者憑證]，然後選取 **Trusted Root Certification Authorities\Certificates**。 確認其中已列出根憑證，如此才能讓驗證正常運作。

- 如果您使用由企業 CA 解決方案簽發的憑證，但您無法進行驗證，請檢查用戶端憑證上的驗證順序。 按兩下用戶端憑證，依序選取 [詳細資料] 索引標籤和 [增強金鑰使用方法]，即可檢查驗證清單順序。 請確定清單中的第一個項目是「用戶端驗證」。 如果不是，請根據以「用戶端驗證」作為清單中第一個項目的「使用者」範本來簽發用戶端憑證。

- 如需其他 P2S 疑難排解詳細資訊，請參閱[針對 P2S 連線進行疑難排解](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。
