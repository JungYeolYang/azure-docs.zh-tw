---
title: 包含檔案
description: 包含檔案
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450207"
---
### <a name="code-walkthrough"></a>程式碼逐步解說

此節說明範例程式碼的一些主要部分，並說明它們與遠端監視解決方案加速器的關聯。

下列程式碼片段如何定義回報的屬性 (這些屬性會描述裝置的功能)。 這些屬性包括：

- 讓解決方案加速器可將裝置新增到地圖的裝置位置。
- 目前的韌體版本。
- 裝置支援的方法清單。
- 裝置所傳送之遙測訊息的結構描述。



範例包括 **serializeToJson** 函式，此函式會使用 Parson 程式庫來序列化此資料結構。

範例包括數個回呼函式，當用戶端與解決方案加速器互動時，這些函式會將資訊列印到主控台：

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

下列程式碼片段會顯示 **device_method_callback** 回呼函式。 此函式會決定當從解決方案加速器收到方法呼叫時要採取的動作。 函式會在 **userContextCallback**參數中收到對 **Chiller** 資料結構的參考。 在 **main** 函式中設定回呼函式時，會設定 **userContextCallback** 的值：



當解決方案加速器呼叫韌體更新方法時，範例會還原序列化 JSON 承載並啟動背景執行緒以完成更新程序。 下列程式碼片段顯示在執行緒上執行的 **do_firmware_update**：



下列程式碼片段顯示用戶端如何將遙測訊息傳送到解決方案加速器。 訊息屬性包括訊息結構描述，有助於解決方案加速器在儀表板上顯示遙測：



範例中的 **main** 函式：

- 初始化並關閉 SDK 子系統。
- 初始化 **Chiller** 資料結構。
- 傳送回報的屬性給解決方案加速器。
- 設定裝置方法回呼函式。
- 傳送模擬的遙測值給解決方案加速器。


