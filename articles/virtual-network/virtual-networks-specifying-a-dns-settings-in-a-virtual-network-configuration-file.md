---
title: 指定虛擬網路組態檔中的 DNS 設定 | Microsoft Docs
description: 如何使用傳統部署模型中的虛擬網路組態檔變更虛擬網路的 DNS 伺服器設定
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: genli
ms.openlocfilehash: 36f7ed9b02b66718327c1a05a6cf29eedf39e7a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232847"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>指定虛擬網路組態檔中的 DNS 設定
网络配置文件有两个可用于指定域名系统 (DNS) 设置的元素：**DnsServers** 和 **DnsServerRef**。 您可以藉由指定其 IP 位址並參考 **DnsServers** 項目的名稱，新增 DNS 伺服器的清單。 然後您可以使用 **DnsServerRef** 項目，來針對您虛擬網路內的不同網站，指定要使用 DnsServers 項目中哪些 DNS 伺服器項目。

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文涵蓋之內容包括傳統部署模型。

网络配置文件可能包含以下元素。 每個項目的標題會連結至提供項目值設定之其他相關資訊的網頁。

> [!IMPORTANT]
> 如需如何設定網路組態檔的相關資訊，請參閱 [使用網路組態檔設定虛擬網路](virtual-networks-using-network-configuration-file.md)。 如需網路組態檔中所包含之各個項目的相關資訊，請參閱 [Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)。
> 
> 

[Dns 項目](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> **DnsServer** 項目中的 **name** 屬性僅做為 **DnsServerRef** 項目的參考。 它不代表 DNS 伺服器的主機名稱。 每個 **DnsServer** 屬性值在整個 Microsoft Azure 訂用帳戶中必須是唯一的。
> 
> 

[Virtual Network Sites 項目](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> 若要指定 Virtual Network Sites 項目的這項設定，先前在 DNS 項目中必須定義它。 Virtual Network Sites 項目中的 DnsServerRef *name* 必須參考 DnsServer *name* 的 DNS 項目中指定的名稱值。
> 
> 

## <a name="next-steps"></a>後續步驟
* 了解 [Azure 虛擬網路組態結構描述](https://go.microsoft.com/fwlink/?LinkId=248093)。
* 了解 [Azure 服務組態結構描述](https://msdn.microsoft.com/library/windowsazure/ee758710)。
* [使用網路組態檔設定虛擬網路](virtual-networks-using-network-configuration-file.md)

