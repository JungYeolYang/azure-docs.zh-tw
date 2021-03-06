---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: e368b1590f263618969423d57cdf0531fc2bb54d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652849"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>修改區域網路閘道 'gatewayIpAddress'

如果您想要連線的 VPN 裝置已變更其公用 IP 位址，您需要修改區域網路閘道，以反映該變更。 您可變更閘道 IP 位址，而不需移除現有的 VPN 閘道連線 (如果有的話)。 若要修改閘道 IP 位址，使用 [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway) 命令以您自己的值取代 'Site2' 和 'TestRG1'。

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

確認輸出中的 IP 位址正確無誤︰

```
"gatewayIpAddress": "23.99.222.170",
```
