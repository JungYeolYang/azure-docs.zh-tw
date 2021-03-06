---
title: 應用程式閘道的運作方式
description: 本文章提供有關應用程式閘道的運作方式的資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: a16421182f533f5aa2ad4bcc2e58e910cc7e8ca6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702416"
---
# <a name="how-an-application-gateway-works"></a>應用程式閘道的運作方式

這篇文章說明如何接受連入要求應用程式閘道，並將它們路由傳送至後端。

![應用程式閘道如何接受要求](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>應用程式閘道如何接受要求

1. 用戶端會將要求傳送至應用程式閘道之前，它會使用網域名稱系統 (DNS) 伺服器來解析應用程式閘道的網域名稱。 Azure 會控制的 DNS 項目，因為所有的應用程式閘道是在 azure.com 的網域。

2. Azure DNS 會傳回給用戶端，也就是應用程式閘道的前端 IP 位址的 IP 位址。

3. 應用程式閘道會接受一或多個接聽程式上的連入流量。 侦听器是检查连接请求的逻辑实体。 會設定前端 IP 位址、 通訊協定和用戶端應用程式閘道的連線的連接埠號碼。

4. 如果 web 應用程式防火牆 (WAF) 正在使用中，應用程式閘道檢查要求的標頭和本文中，如果有的話，依 WAF 規則。 此動作會判斷要求是否為有效的要求或安全性威脅。 如果要求是有效的它會路由傳送至後端。 如果要求不是有效的它會封鎖為 安全性威脅。

Azure 應用程式閘道可用的應用程式內部負載平衡器或網際網路對向應用程式負載平衡器。 網際網路對向應用程式閘道會使用公用 IP 位址。 網際網路對向應用程式閘道的 DNS 名稱是可公開解析為其公用 IP 位址。 如此一來，網際網路對向應用程式閘道會將用戶端要求路由至網際網路。

內部應用程式閘道會使用只有私人 IP 位址。 内部应用程序网关的 DNS 名称可公开解析为其专用 IP 地址。 因此，內部負載平衡器可以只將路由具有存取權的用戶端應用程式閘道的虛擬網路的要求。

網際網路和內部應用程式閘道會將要求路由至後端伺服器使用私人 IP 位址。 後端伺服器不需要接收來自內部的要求或網際網路對向應用程式閘道的公用 IP 位址。

## <a name="how-an-application-gateway-routes-a-request"></a>應用程式閘道如何路由傳送要求

如果要求是有效或 WAF 不在使用中，應用程式閘道會評估與接聽程式相關聯的要求路由規則。 此動作會決定哪一個後端集區，以將要求路由傳送。

它們會列在入口網站中的順序處理規則。 應用程式閘道為基礎的要求路由規則，決定是否要將所有要求都路由到特定後端集區中，接聽程式上都路由要求給不同的後端集區根據 URL 路徑或要求重新導向至另一個連接埠或外部網站。

當應用程式閘道選取後端集區時，它會將要求傳送至其中一個狀況良好的後端伺服器集區 (y.y.y.y) 中。 伺服器健全狀況取決於健康狀態探查。 如果後端集區包含多部伺服器，應用程式閘道會使用循環配置資源演算法來路由狀況良好的伺服器之間的要求。 此負載平衡的伺服器上的要求。

應用程式閘道判斷後端伺服器之後，它會根據 HTTP 設定的後端伺服器中開啟新的 TCP 工作階段。 HTTP 設定會指定通訊協定、 連接埠，以及其他路由相關的設定，才能建立新的工作階段的後端伺服器。

連接埠和 HTTP 設定中所使用的通訊協定決定應用程式閘道和後端伺服器之間的流量 （因此完成端對端 SSL） 加密或未加密。

當應用程式閘道會將原始要求傳送至後端伺服器時，它會接受在覆寫主機名稱、 路徑和通訊協定的相關的 HTTP 設定中所做的任何自訂設定。 這個動作會維護 cookie 型工作階段親和性、 連線清空]、 [主機名稱選取項目從後端，依此類推。

內部應用程式閘道會使用只有私人 IP 位址。 內部應用程式閘道的 DNS 名稱會解析成其私人 IP 位址。 如此一來，內部負載平衡器可以只將路由具有存取權的用戶端應用程式閘道的虛擬網路的要求。

 >[!NOTE]
 >這兩個網際網路對向及內部應用程式閘道會使用私人 IP 位址，將要求路由至後端伺服器。 當您的後端集區資源包含的私人 IP 位址、 VM NIC 組態或在內部解析的位址時，就會發生此動作。 如果後端集區：
> - **是公用端點**，應用程式閘道會使用其前端的公用 IP 來連線到伺服器。 如果沒有前端公用 IP 位址，其中被指派給輸出的外部連線能力。
> - **包含在內部解析的 FQDN 或私人 IP 位址**，應用程式閘道會將要求路由到後端伺服器使用其執行個體的私人 IP 位址。
> - **包含外部端點或外部可解析 FQDN**，應用程式閘道會將要求路由到後端伺服器使用其前端公用 IP 位址。 DNS 解析根據私人 DNS 區域或自訂的 DNS 伺服器，如果設定，或使用預設 Azure 提供的 DNS。 如果沒有前端公用 IP 位址，其中被指派給輸出的外部連線能力。

### <a name="modifications-to-the-request"></a>对请求的修改

它將要求轉送至後端之前，應用程式閘道會插入的所有要求四個額外的標頭。 這些標頭是 x 轉送的、 x 轉送 proto、 x 轉送-連接埠和 x 原始主機。 x-forwarded-for 标头的格式是逗号分隔的“IP:端口”列表。

x-forwarded-proto 的有效值为 HTTP 或 HTTPS。 X 轉送埠指定要求到達應用程式閘道的位置的連接埠。 x-original-host 标头包含随请求一起抵达的原始主机标头。 此標頭可用於 Azure 網站整合，流量會路由傳送至後端之前，修改的連入的主機標頭。 如果工作階段親和性已啟用的選項，它會將閘道管理的親和性 cookie。

您可以藉由修改標頭的應用程式閘道設定[重寫的 HTTP 標頭](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)或修改使用路徑覆寫設定的 URI 路徑。 不過，除非設定此選項，若要這樣做，則所有連入要求會代理到後端。

## <a name="next-steps"></a>後續步驟

[了解應用程式閘道的元件](application-gateway-components.md)
