---
title: Azure 虛擬 WAN 夥伴位置 | Microsoft Docs
description: 本文包含 Azure 虛擬 WAN 的合作夥伴和中樞位置的清單。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: f38cd0565b2e90fe0803d8e815c622e22e954a18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60459829"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>虛擬 WAN 夥伴與虛擬中樞位置

本文章提供有關虛擬 WAN 支援的區域和合作夥伴連線至虛擬中樞。

Azure 虛擬 WAN 是一種網路服務，透過 Azure 提供最佳且自動化的分支對分支連線。 虛擬 WAN 可讓您連線，並設定與 Azure 通訊的分支裝置。 做法是以手動的方式，或使用透過虛擬 WAN 的協力廠商的提供者裝置。 使用協力廠商裝置，可讓您容易使用，簡化的連線和組態管理。

從內部部署裝置連線到虛擬中樞是透過自動化方式建立。 虛擬中樞是受 Microsoft 管理的虛擬網路。 中樞包含不同的服務端點，可啟用您內部部署網路 (vpnsite) 中的連線。 每個地區都只能有一個中樞。

## <a name="automation"></a>自動化連線的合作夥伴

連接到 Azure 虛擬 WAN 的裝置已內建自動化連接能力。 這通常是在裝置管理 UI (或相同功能) 中設定，它可在 VPN 分支裝置與 Azure 虛擬中樞 VPN 端點 (VPN 閘道) 之間設定連線和組態管理。

下列高階自動化是在裝置主控台/管理中心中設定：

* 裝置擁有存取 Azure 虛擬 WAN 資源群組的適當權限
* 將分支裝置上傳到 Azure 虛擬 WAN
* 自動下載 Azure 的連線資訊
* 內部部署分支裝置的設定 

有些連線夥伴可能會擴充自動化功能以包括建立 Azure 虛擬中樞 VNet 與 VPN 閘道的能力。 如果您想要深入了解自動化，請參閱[設定自動化 – WAN 夥伴](virtual-wan-configure-automation-providers.md)。

## <a name="partners"></a>透過合作夥伴連線

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

## <a name="locations"></a>位置

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>後續步驟

* 如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 常見問題集](virtual-wan-faq.md)。

* 如需如何自動化連線到 Azure 虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 夥伴 - 如何自動化](virtual-wan-configure-automation-providers.md)。
