---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: ffd17f7a641e1481aa4c88f8b2eb12ec11fa7d8b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741183"
---
Fluentd 是適用於統一記錄的開放原始碼資料收集器。 `Fluentd` 設定可管理容器對 [Fluentd](https://www.fluentd.org) 伺服器的連線。 容器包含 Fluentd 記錄提供者，可讓您的容器將記錄 (及選擇性的計量資料) 寫入至 Fluentd 伺服器。

下表說明 `Fluentd` 區段下所支援的組態設定。

| 名稱 | 資料類型 | 描述 |
|------|-----------|-------------|
| `Host` | 字串 | Fluentd 伺服器的 IP 位址或 DNS 主機名稱。 |
| `Port` | 整數  | Fluentd 伺服器的連接埠。<br/> 預設值為 24224。 |
| `HeartbeatMs` | 整數  | 活動訊號間隔，以毫秒為單位。 如果在此間隔到期之前未傳送任何事件流量，系統便會將活動訊號傳送至 Fluentd 伺服器。 預設值為 60000 毫秒 (1 分鐘)。 |
| `SendBufferSize` | 整數  | 針對傳送作業所配置的網路緩衝空間，以位元組為單位。 預設值為 32768 個位元組 (32 KB)。 |
| `TlsConnectionEstablishmentTimeoutMs` | 整數  | 向 Fluentd 伺服器建立 SSL/TLS 連線的逾時，以毫秒為單位。 預設值是 10000 毫秒 (10 秒)。<br/> 如果將 `UseTLS` 設定為 false，便會忽略此值。 |
| `UseTLS` | BOOLEAN | 指出容器是否應使用 SSL/TLS 來與 Fluentd 伺服器通訊。 預設值為 False。 |