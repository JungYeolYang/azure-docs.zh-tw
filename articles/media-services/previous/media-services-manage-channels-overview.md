---
title: 使用 Azure 媒體服務之即時串流的概觀 | Microsoft Docs
description: 本主題提供了使用 Azure 媒體服務之即時串流的概觀。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: c5dd0146fe59e7dc85787f146b10cfde7d6addb4
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867901"
---
# <a name="overview-of-live-streaming-using-media-services"></a>使用媒體服務之即時串流的概觀

> [!NOTE]
> 媒體服務 v2 不會再新增任何新的特性或功能。 <br/>查看最新版本的[媒體服務 v3](https://docs.microsoft.com/azure/media-services/latest/)。 此外，請參閱[從 v2 至 v3 的移轉指導方針](../latest/migrate-from-v2-to-v3.md)

## <a name="overview"></a>概觀

使用 Azure 媒體服務傳遞即時串流時，通常涉及下列元件：

* 相機，用來廣播事件。
* 即時視訊編碼器，它會將相機中的訊號轉換成資料流，然後再傳送至即時資料流服務。

    (選擇性) 多個即時同步處理的編碼器。 針對某些需要相當高度可用性與高品質經驗的重要即時事件，建議使用主動對主動備援編碼器搭配時間同步處理，以便順利進行容錯移轉，而不會遺失資料。
* 即時串流服務可讓您執行下列動作：

  * 使用多种实时传送视频流协议（例如 RTMP 或平滑流式处理）引入实时内容
  * (選擇性) 將您的串流編碼成調適性位元速率串流
  * 预览实时流，
  * 記錄並儲存擷取的內容以於稍後進行串流 (隨選視訊)
  * 透過一般串流通訊協定 (例如，MPEG DASH、Smooth、HLS) 直接將內容傳遞給客戶，或傳遞至內容傳遞網路 (CDN) 供進一步的發佈。

**Microsoft Azure 媒體服務** (AMS) 提供擷取、編碼、預覽、儲存和傳遞即時串流內容的能力。

使用媒體服務，您可以善用[動態封裝](media-services-dynamic-packaging-overview.md)，可讓您將廣播即時資料流中 MPEG DASH、 HLS、 Smooth Streaming 格式從比重摘要傳送至服務。 您的檢視者可以搭配支援 HLS、DASH 或 Smooth Streaming 的播放器播放即時串流。 您可以使用 Azure Media Player 在您的 web 或行動應用程式，提供您在任何這些通訊協定中的資料流。

> [!NOTE]
> 從 2018 年 5 月 12 日開始，即時通道將不再支援 RTP/MPEG-2 傳輸串流內嵌通訊協定。 請從 RTP/MPEG-2 移轉到 RTMP 或分散式 MP4 (Smooth Streaming) 內嵌通訊協定。

## <a name="streaming-endpoints-channels-programs"></a>串流端點、 通道、 程式

在 Azure 媒體服務中，**通道**、**程式**及 **StreamingEndpoints** 會處理所有的即時串流功能，包括內嵌、格式化、DVR、安全性、延展性和備援能力。

在 Azure 媒體服務中， **通道** 代表處理即時串流內容的管線。 通道可以用下列方式接收即時輸入串流：

* 內部部署即時編碼器會傳送多位元速率 **RTMP** 或 **Smooth Streaming** (分散的 MP4) 到針對**即時通行**傳遞所設定的通道。 **即時通行**傳遞就是擷取的串流會通過**通道**，無需進一步的處理。 您可以使用下列輸出多位元速率 Smooth Streaming 的即時編碼器：MediaExcel、Ateme、Imagine Communications、Envivio、Cisco 和 Elemental。 下列即時編碼器會輸出 RTMP：Adobe Flash Media Live Encoder (FMLE)、Telestream Wirecast、Haivision、Teradek 和 Tricaster 轉錄器。  即時編碼器也會將單一位元速率串流傳送至無法用於即時編碼的通道，但是不建議您使用此方法。 接到要求時，媒體服務會傳遞串流給客戶。

  > [!NOTE]
  > 如果您在很長一段時間內進行多個事件，而且已投資內部部署編碼器時，使用傳遞方法是進行即時串流的最經濟實惠方式。 請參閱 [價格](https://azure.microsoft.com/pricing/details/media-services/) 詳細資料。
  > 
  > 
* 內部部署即時編碼器會將單一位元速率串流傳送至通道，可以使用下列格式之一，以媒體服務執行即時編碼：RTMP 或 Smooth Streaming (分散的 MP4)。 下列包含 RTMP 輸出的即時編碼器已知可與以下類型的通道協同作業：Telestream Wirecast 和 FMLE。 通道接著會執行即時編碼，將連入的單一位元速率串流編碼成多位元速率 (自動調整) 視訊串流。 收到请求时，媒体服务会将该流传送给客户。

自媒體服務 2.10 版起，當您建立通道時，您可以指定您希望通道接收輸入串流的方式，以及您是否想要通道執行串流的即時編碼。 您有兩個選擇：

* **無** (傳遞) – 如果您想要使用會輸出多位元速率串流 (傳遞串流) 的內部部署即時編碼器，請指定這個值。 在此情況下，連入的串流會傳遞至輸出，無須任何編碼。 这是 2.10 发行版以前的通道行为。  
* **標準** – 如果您打算使用媒體服務將單一位元速率即時串流編碼成多位元速率串流，請選擇這個值。 對於快速相應增加的非頻繁事件來說，這是較為經濟的方法。 请注意，实时编码会影响计费，应记住，将实时编码频道保持为“正在运行”状态会产生费用。  建議您在即時串流事件完成之後立即停止執行的通道，以避免額外的每小時費用。

## <a name="comparison-of-channel-types"></a>通道類型的比較

下表提供了媒體服務所支援的兩種通道類型比較指南

| 功能 | 傳遞通道 | 標準通道 |
| --- | --- | --- |
| 單一位元速率輸入會在雲端編碼為多重位元速率 |否 |是 |
| 最大解析度、分層數目 |1080p、8 層、60+fps |720p、6 層、30 fps |
| 輸入通訊協定 |RTMP、Smooth Streaming |RTMP、Smooth Streaming |
| 價格 |請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) 並按一下 [即時影片] 索引標籤 |請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) |
| 最長執行時間 |全天候 |8 小时 |
| 插入靜態圖像支援 |否 |是 |
| 廣告訊號支援 |否 |是 |
| 傳遞 CEA 608/708 字幕 |是 |是 |
| 支援未統一輸入的 GOP |是 |否 – 輸入必須為固定式 2 秒 GOP |
| 支援變動畫面播放速率輸入 |是 |否 – 輸入必須為固定畫面播放速率。<br/>輕微的差異可以接受，例如：處於高速動態場景的情況。 但編碼器無法降至 10 個畫面/秒的標準。 |
| 在輸入摘要遺失時自動關閉通道 |否 |經過 12 個小時，如果沒有程式仍在執行 |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>使用可從內部部署編碼器接收多位元速率即時串流的通道 (傳遞)

下圖顯示 **即時通行** 工作流程中涉及的 AMS 平台主要部分。

![即時工作流程](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

如需詳細資訊，請參閱 [使用通道，從內部部署編碼器接收多位元速率即時串流](media-services-live-streaming-with-onprem-encoders.md)。

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>使用能够通过 Azure 媒体服务执行实时编码的通道

下圖顯示 AMS 平台的主要部分，與通道可以使用媒體服務執行即時編碼的即時串流工作流程有關。

![即時工作流程](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

有关详细信息，请参阅[使用能够通过 Azure 媒体服务执行实时编码的频道](media-services-manage-live-encoder-enabled-channels.md)。

## <a name="description-of-a-channel-and-its-related-components"></a>通道和其相關元件的說明

### <a name="channel"></a>通道

在媒體服務中， [通道](https://docs.microsoft.com/rest/api/media/operations/channel)負責處理即時資料流內容。 通道會提供輸入端點 (內嵌 URL)，接著您再提供給即時轉碼器。 通道從即時轉碼器接收即時輸入資料流，再透過一或多個 StreamingEndpoint 進行串流處理。 通道也會提供預覽端點 (預覽 URL)，您可在進一步處理和傳遞之前先用來預覽及驗證您的資料流。

您可在建立通道時取得內嵌 URL 和預覽 URL。 通道不需處於啟動狀態也可以取得這些 URL。 當您準備好要開始從即時轉碼器推播資料給通道時，就必須先啟動通道。 即時轉碼器開始內嵌資料後，您就可以預覽您的資料流。

每個媒體服務帳戶可以包含多個通道、多個程式和多個 StreamingEndpoints。 根據頻寬和安全性需求，StreamingEndpoint 服務可以專屬於一或多個通道。 任何 StreamingEndpoint 可以從任何通道中提取。

建立通道時，您能以下列其中一種格式指定允許的 IP 位址：具有 4 個數字的 IpV4 位址、CIDR 位址範圍。

### <a name="program"></a>程式
[程式](https://docs.microsoft.com/rest/api/media/operations/program) 可讓您控制即時資料流區段的發佈和儲存體。 频道管理节目。 通道和程式的關聯性非常類似於傳統媒體，此處的通道有常數內容資料流，而程式的範圍是該通道上的某些計時事件。
您可以透過設定 **ArchiveWindowLength** 屬性，指定您想要保留已記錄程式內容的時數。 此值最小可以設定為 5 分鐘，最大可以設定為 25 個小時。

ArchiveWindowLength 也指定用戶端可從目前即時位置往回搜尋的最大時間量。 程式可以執行超過指定的時間量，但是超過此時間長度的內容會被持續捨棄。 此屬性的這個值也會決定用戶端資訊清單可以成長多長的時間。

每個程式都是與「資產」相關聯。 若要发布节目，必须为关联的资产创建定位符。 擁有此定位器，可讓您建置可提供給用戶端的串流 URL。

通道支援最多三個同時執行的程式，因此您可以建立相同內送串流的多個封存。 這可讓您視需要發行和封存事件的不同部分。 例如，您的商務需求是封存 6 小時的程式，但只廣播最後 10 分鐘。 為了達成此目的，您必須建立兩個同時執行的程式。 其中一個程式設定為封存 6 小時的事件，但是未發行該程式。 另一個程式則設定為封存 10 分鐘，並發行程式。

## <a name="billing-implications"></a>計費影響
一旦通过 API 将频道的状态转换为“正在运行”，就会开始计费。  

下表顯示通道狀態如何對應至 API 和 Azure 入口網站的計費狀態。 請注意，API 和入口網站 UX 之間的狀態稍有不同。 一旦通道透過 API 處於「執行中」狀態，或在 Azure 入口網站中處於「就緒」或「串流」狀態，就會開始計費。

若要停止通道進一步向您計費，您必須停止透過 API 或在 Azure 入口網站中的通道。
使用完通道后，需要亲自停止通道。 無法停止通道將會導致持續計費。

> [!NOTE]
> 使用標準通道時，在輸入饋送遺失 12 小時之後且沒有執行任何程式時，AMS 會自動關閉仍處於「執行中」狀態的任何通道。 不過，您將還是需要支付通道處於「執行中」狀態時間的費用。
>
>

### <a id="states"></a>通道状态及其如何映射到计费模式
通道的目前狀態。 可能的值包括：

* **已停止**。 這是通道建立後的初始狀態 (除非在入口網站中選取自動啟動)。此狀態中不會計費。 在此狀態下，通道屬性可以更新，但是不允許串流。
* **啟動中**。 正在啟動通道。 此状态下不会发生计费。 在此狀態期間允許任何更新或串流。 如果發生錯誤，通道會回到已停止狀態。
* **執行中**。 通道能夠處理即時串流。 現在針對使用量計費。 您必須停止通道來防止進一步計費。
* **停止中**。 正在停止通道。 此暫時性狀態中不會計費。 在此狀態期間允許任何更新或串流。
* **刪除中**。 正在刪除通道。 此暫時性狀態中不會計費。 在此狀態期間允許任何更新或串流。

下表顯示通道狀態如何對應至計費模式。

| 頻道狀態 | 入口網站 UI 指標 | 會計費嗎？ |
| --- | --- | --- |
| 啟動中 |啟動中 |無 (暫時性狀態) |
| 正在运行 |准备就绪（没有正在运行的节目）<br/>或<br/>流式处理（至少有一个正在运行的节目） |是 |
| 停止中 |停止中 |否（暂时状态） |
| 已停止 |已停止 |否 |

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>相關主題
[Azure 媒体服务分片 MP4 实时引入规范](media-services-fmp4-live-ingest-overview.md)

[使用啟用的通道來以 Azure 媒體服務執行即時編碼](media-services-manage-live-encoder-enabled-channels.md)

[使用通道，從內部部署編碼器接收多位元速率即時串流](media-services-live-streaming-with-onprem-encoders.md)

[配額和限制](media-services-quotas-and-limitations.md)。  

[媒體服務概念](media-services-concepts.md)
