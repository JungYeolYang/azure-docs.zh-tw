---
title: Azure IoT 中樞移轉至診斷設定 | Microsoft Docs
description: 如何將「Azure IoT 中樞」更新成使用 Azure 診斷設定 (取代作業監視) 來即時監視 IoT 中樞上作業的狀態。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: b6cde8402c699a7477cd0efc79a44b3f5e150ad0
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792645"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>將 IoT 中樞從作業監視移轉至診斷設定

客戶如果使用[作業監視](iot-hub-operations-monitoring.md)來追蹤「IoT 中樞」內作業的狀態，可以將該工作流程遷移至 [Azure 診斷設定](../azure-monitor/platform/diagnostic-logs-overview.md)，這是「Azure 監視器」的一項功能。 診斷設定可針對許多 Azure 服務提供資源層級的診斷資訊。

**IoT 中樞的監視功能已被取代的作業**，而且已從入口網站中移除。 本文提供將您工作負載從作業監視移至診斷設定的步驟。 如需有關淘汰時間表的詳細資訊，請參閱[使用 Azure 監視器和 Azure 資源健康狀態來監視您的 Azure IoT 解決方案](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)。

## <a name="update-iot-hub"></a>更新 IoT 中樞

若要在 Azure 入口網站中更新您的「IoT 中樞」，請先開啟診斷設定，然後關閉作業監視。  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>關閉作業監視

> [!NOTE]
> 當做的 2019 年 3 月 11 日，監視功能會從 IoT 中樞的 Azure 入口網站介面移除的作業。 下列步驟不再適用。 若要移轉，請確定正確的分類會開啟 Azure 監視器上述的診斷設定中。

在工作流中测试新的诊断设置后，可以关闭操作监视功能。 

1. 在您的「IoT 中樞」功能表上，選取 [作業監視]。

2. 在每個監視類別底下，選取 [無]。

3. 儲存作業監視變更。

## <a name="update-applications-that-use-operations-monitoring"></a>更新使用作業監視的應用程式

作業監視與診斷設定的結構描述有些微差異。 請務必更新目前使用作業監視的應用程式，以對應診斷設定所使用的結構描述。 

此外，诊断设置还提供了五个新的跟踪类别。 在您針對現有的結構描述更新應用程式之後，請一併新增新的類別：

* 雲端到裝置對應項作業
* 裝置到雲端對應項作業
* 對應項查詢
* 作業的操作
* 直接方法

針對特定的結構描述結構，請參閱[了解診斷設定的結構描述](iot-hub-monitor-resource-health.md#understand-the-logs)。

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>監視低延遲的裝置連線和中斷連接事件

若要監視裝置連線和中斷連接事件，在生產環境中的，我們建議您訂閱[**中斷連線的裝置**事件](iot-hub-event-grid.md#event-types)上取得警示和監視裝置連線狀態的 Event Grid。 使用此[教學課程](iot-hub-how-to-order-connection-state-events.md)可了解如何將 IoT 中樞的「裝置連線」和「裝置中斷連線」事件整合至 IoT 解決方案中。

## <a name="next-steps"></a>後續步驟

[監視 Azure IoT 中樞的健康情況並快速診斷問題](iot-hub-monitor-resource-health.md)
