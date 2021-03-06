---
title: Azure Service Fabric 事件存放區 | Microsoft Docs
description: 深入了解 Azure Service Fabric 的 EventStore
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: 520961fb4bd126ef878a779c10fb5689b8692c73
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683733"
---
# <a name="eventstore-service-overview"></a>EventStore 服務概觀

>[!NOTE]
>截至 Service Fabric 6.4 版為止， EventStore API 僅適用於在 Azure 上執行的 Windows 叢集。 我們正在將這個功能和我們的獨立叢集移植到 Linux。

## <a name="overview"></a>概觀

6.2 版中引進，EventStore 服務是 Service Fabric 中的監視選項。 EventStore 可讓您了解您的叢集或工作負載在指定時間點的狀態。 EventStore 是具狀態的 Service Fabric 服務，可維護叢集中的事件。 此事件會透過 Service Fabric Explorer、REST 和 API 公開。 EventStore 可直接查詢叢集，取得叢集中任何實體的診斷資料，應可用來協助您：

* 診斷開發或測試中的問題，或在使用監視管線的場合中診斷問題
* 確認正確處理您針對叢集採取的管理動作
* 取得 Service Fabric 如何與特定實體互動的「快照集」

![EventStore](media/service-fabric-diagnostics-eventstore/eventstore.png)

若要查看 EventStore 中可用事件的完整清單，請參閱 [Service Fabric 事件](service-fabric-diagnostics-event-generation-operational.md)。

>[!NOTE]
>截至 Service Fabric 6.2 版為止， EventStore API 目前為預覽版本，僅適用於在 Azure 上執行的 Windows 叢集。 我們正在將這個功能和我們的獨立叢集移植到 Linux。

您可以查詢 EventStore 服務，找出叢集中每個實體和實體類型適用的事件。 這表示您可以查詢以下層級的事件：
* 叢集：叢集本身特有的事件 (例如叢集升級)
* 節點：所有節點層級事件
* 節點：某個節點 (以 `nodeName` 識別) 特有的事件
* 應用程式：所有應用程式層級事件
* 應用程式：某個應用程式 (以 `applicationId` 識別) 特有的事件
* 服務：來自叢集中所有服務的事件
* 服務：來自特定服務 (以 `serviceId` 識別) 的事件
* 分割區：來自所有分割區的事件
* 分割區：來自特定分割區 (以 `partitionId` 識別) 的事件
* 分割區複本：特定分割區 (以 `partitionId` 識別) 內所有複本 / 執行個體中的事件
* 分割區複本：特定複本 / 執行個體 (以 `replicaId` 和 `partitionId` 識別) 中的事件

若要深入了解 API，請參閱 [EventStore API 參考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)。

EventStore 服務還能將叢集中的事件相互關聯。 透過同時間寫入的事件，您可以追溯到彼此影響的相異實體；EventStore 服務能連結這些事件，協助您找出叢集中活動的原因。 例如，如果某個應用程式在未引發變更的情況下成為不良狀況，EventStore 也會查看平台公開的其他事件，因而可能會將這個狀況與 `Error` 或 `Warning` 事件相互關聯。 這有助於縮短偵測失敗及分析根本原因的時間。

## <a name="enable-eventstore-on-your-cluster"></a>在您的叢集上啟用 EventStore

### <a name="local-cluster"></a>本機叢集

在[您叢集的 fabricSettings.json](service-fabric-cluster-fabric-settings.md) 中，新增 EventStoreService 作為附加元件功能，以及執行叢集升級。

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster"></a>Azure 叢集

在叢集的 Azure Resource Manager 範本中，您可以開啟 EventStore 服務藉由執行[叢集組態升級](service-fabric-cluster-config-upgrade-azure.md)並新增下列程式碼，您可以使用 PlacementConstraints 把 EventStore 的複本在某個特定節點類型，例如專用的系統服務的某個節點類型上的服務。 `upgradeDescription` 區段會設定要在節點上觸發重新啟動的組態升級。 您可以在另一項更新中移除此區段。

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>後續步驟
* 開始使用 EventStore API - [在 Azure Service Fabric 叢集中使用 EventStore API](service-fabric-diagnostics-eventstore-query.md)
* 深入了解 EventStore 所提供的事件清單 - [Service Fabric 事件](service-fabric-diagnostics-event-generation-operational.md)
* Service Fabric 的監視和診斷概觀 - [Service Fabric 的監視和診斷](service-fabric-diagnostics-overview.md)
* 檢視完整的 API 呼叫清單 - [EventStore REST API 參考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* 深入了解監視叢集 - [監視叢集和平台](service-fabric-diagnostics-event-generation-infra.md)。
