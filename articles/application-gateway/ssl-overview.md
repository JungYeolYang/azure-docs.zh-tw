---
title: 在 Azure 應用程式閘道上啟用端對端 SSL
description: 本文將簡介應用程式閘道端對端 SSL 支援。
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 92799019d13de71d911767d8e400598513587667
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715212"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>应用程序网关的 SSL 终止和端到端 SSL 概述

安全套接字层 (SSL) 是用于在 Web 服务器与浏览器之间建立加密链接的标准安全技术。 此链接可确保在 Web 服务器与浏览器之间传递的所有数据都会得到保密和加密。 应用程序网关支持网关上的 SSL 终止以及端到端的 SSL 加密。

## <a name="ssl-termination"></a>SSL 終止

應用程式閘道支援在閘道上終止 SSL，之後流量通常會以未加密狀態流至後端伺服器。 在应用程序网关上执行 SSL 终止可以带来诸多优势：

- **更佳的效能**– 最大效能的衝擊進行 SSL 解密時是初始信號交換。 为了提高性能，执行解密的服务器将会缓存 SSL 会话 ID 并管理 TLS 会话票证。 如果此操作是在应用程序网关上执行的，则来自同一个客户端的所有请求都可以使用缓存的值。 如果此操作是在后端服务器上执行的，则每当客户端的请求转到另一台服务器时，客户端都必须重新进行身份验证。 使用 TLS 票证有助于缓解此问题，但并非所有客户端都支持 TLS 票证，并且配置和管理这些票证可能有难度。
- **更好的後端伺服器的使用率**– SSL/TLS 處理是非常 CPU 密集，並且成為更密集的索引鍵的大小增加時。 从后端服务器中消除此任务可使它们专注于以最有效的方式提供内容。
- **智慧型路由**– 解密流量，應用程式閘道具有存取權的要求內容，例如標頭、 URI，並依此類推，而且可以使用將要求路由傳送此資料。
- **憑證管理**– 憑證只需要購買並安裝應用程式閘道和並非所有的後端伺服器上。 这可以节省时间和开支。

若要配置 SSL 终止，需将一个 SSL 证书添加到侦听器，使应用程序网关能够根据 SSL 协议规范派生对称密钥。 然后，可以使用该对称密钥来加密和解密发送到网关的流量。 SSL 证书需采用个人信息交换 (PFX) 格式。 此檔案格式可允許將私密金鑰匯出，而應用程式閘道需要這個匯出的金鑰來執行流量的加密和解密。

> [!NOTE] 
>
> 应用程序网关不会提供任何用于创建新证书，或者将证书请求发送到证书颁发机构的功能。

若要建立 SSL 连接，需要确保 SSL 证书符合以下条件：

- 当前日期和时间处于证书上的“Valid from”（有效起始日期）和“Valid to”（有效结束日期）日期范围内。
- 证书的“公用名”(CN) 与请求中的主机标头相匹配。 例如，如果客户端正在向 `https://www.contoso.com/` 发出请求，则 CN 必须是 `www.contoso.com`。

### <a name="certificates-supported-for-ssl-termination"></a>支持用于 SSL 终止的证书

应用程序网关支持以下类型的证书：

- CA（证书颁发机构）证书：CA 证书是证书颁发机构 (CA) 颁发的数字证书
- EV（扩展验证）证书：EV 证书是行业标准证书准则。 使用此类证书会使浏览器地址栏变为绿色，并发布公司名称。
- 通配符证书：此证书支持任意数量的基于 *.site.com 的子域（其中的 * 需替换为你的子域）。 但是，它不支持 site.com，因此，如果用户在不键入前导“www”的情况下访问你的网站，则通配符证书不会反映这一点。
- 自签名证书：客户端浏览器不信任这些证书，并且会警告用户，指出虚拟服务的证书不是信任链的一部分。 自签名证书适合用于测试，或者管理员会在其中控制客户端并且可以安全绕过浏览器安全警报的环境。 切勿将自签名证书用于生产工作负荷。

有关详细信息，请参阅[配置应用程序网关的 SSL 终止](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)。

## <a name="end-to-end-ssl-encryption"></a>端到端 SSL 加密

某些客户可能不希望与后端服务器进行未加密的通信。 可能是因為安全性需求、合規性需求，或應用程式可能只接受安全連線。 對於這類應用程式，應用程式閘道可支援端對端 SSL 加密。

端對端 SSL 可讓您將機密資料以加密方式安全地傳輸到後端，同時又享有應用程式閘道所提供第 7 層負載平衡功能的好處。 部分功能為 cookie 型工作階段親和性、URL 型路由、支援根據站台或能注入 X-Forwarded-* 標頭的路由。

當設定為端對端 SSL 通訊模式時，應用程式閘道會在閘道上終止 SSL 工作階段，並解密使用者流量。 然後，它會套用所設定的規則來選取要將流量路由傳送到的適當後端集區執行個體。 應用程式閘道接著再起始連往後端伺服器的新 SSL 連線，並先使　用後端伺服器的公開金鑰憑證重新加密資料，再將要求傳輸至後端。 任何來自 Web 伺服器的回應都會經歷相同的程序而回到使用者端。 若要启用端到端 SSL，可将 [后端 HTTP 设置](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) 中的协议设置为 HTTPS，此设置随后将应用到后端池。

SSL 策略将应用到前端和后端流量。 在前端上，应用程序网关充当服务器并强制实施该策略。 在后端上，应用程序网关充当客户端，并在 SSL 握手期间将协议/密码信息作为首选项发送。

应用程序网关只会与下面所述的后端实例通信：已将其证书加入应用程序网关的白名单，或者其证书已由已知的 CA 颁发机构签名，并且证书 CN 与 HTTP 后端设置中的主机名匹配。 这些实例包括受信任的 Azure 服务，例如 Azure 应用服务 Web 应用和 Azure API 管理。

如果后端池成员的证书未由已知 CA 颁发机构签名，则必须为后端池中已启用端到端 SSL 的每个实例配置一个证书，这样才能进行安全通信。 新增憑證可確保應用程式閘道只會與已知的後端執行個體通訊。 這會進而保護端對端通訊。

> [!NOTE] 
>
> Azure 应用服务 Web 应用和 Azure API 管理等受信任的 Azure 服务不需要身份验证证书设置。

> [!NOTE] 
>
> 添加到“后端 HTTP 设置”中的、用于对后端服务器进行身份验证的证书，可以是添加到**侦听器**的、用于在应用程序网关上实现 SSL 终止的同一个证书；为了增强安全性，两者也可以不同。

![端對端 SSL 案例][1]

在此範例中，使用端對端 SSL，將使用 TLS1.2 的要求路由傳送至 Pool1 中的後端伺服器。

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>端對端 SSL 和憑證允許清單

應用程式閘道只會與已知的後端執行個體通訊，後者已將其憑證加入到應用程式閘道的允許清單。 若要啟用憑證允許清單，您必須將後端伺服器憑證的公開金鑰上傳至應用程式閘道 (不是根憑證)。 於是，只允許連接至已知和允許清單中的後端。 其餘的後端會導致閘道錯誤。 自签名证书仅用于测试目的，不建议用于生产工作负荷。 這類憑證必須如先前步驟所述，加入應用程式閘道的允許清單之中，才能使用。

> [!NOTE]
> 受信任的 Azure 服務 (例如 Azure App Service) 不需進行驗證憑證設定。

## <a name="end-to-end-ssl-with-the-v2-sku"></a>v2 SKU 的端對端 SSL

在「應用程式閘道 v2 SKU」中，「驗證憑證」已被淘汰且被「受信任的根憑證」取代。 它們的功能與「驗證憑證」類似，但有幾個主要差異：

- 憑證如果是由 CN 與 HTTP 後端設定中主機名稱相符的已知 CA 授權單位所簽署，則無須執行任何其他步驟，端對端 SSL 就能運作。 

   例如，如果後端憑證是由已知 CA 所簽發且 CN 為 contoso.com，而後端 HTTP 設定的主機欄位也設定為 contoso.com，便無須執行任何其他步驟。 您可以將後端 HTTP 設定通訊協定設定為 HTTPS，健康狀態探查和資料路徑就會都啟用 SSL。 如果您使用 Azure App Service 或其他 Azure web 服務作為後端，則這些也是以隱含方式信任和任何進一步的步驟所需的端對端 SSL。
- 如果憑證是自我簽署的，或由未知的媒介所簽署的，則若要在 v2 SKU 中啟用端對端 SSL，就必須定義受信任的根憑證。 「應用程式閘道」只會與符合下列條件的後端進行通訊：其伺服器憑證的根憑證符合與集區相關之後端 HTTP 設定中受信任根憑證清單內的其中一個憑證。
- 除了根憑證相符之外，「應用程式閘道」也會驗證後端 HTTP 設定中所指定的「主機」設定是否符合後端伺服器 SSL 憑證所出示的通用名稱 (CN)。 嘗試與後端建立 SSL 連線時，「應用程式閘道」會將「伺服器名稱指示」(SNI) 延伸模組設定為後端 HTTP 設定中所指定的「主機」。
- 如果選擇**從後端位址挑選主機名稱**，而不是從後端 HTTP 設定中的 [主機] 欄位挑選，則 SNI 標頭會一律設定為後端集區 FQDN，而後端伺服器 SSL 憑證上的 CN 必須符合其 FQDN。 在此案例中，不支援與 Ip 的後端集區成員。
- 根憑證是一個來自後端伺服器憑證的 Base64 編碼根憑證。

## <a name="next-steps"></a>後續步驟

了解端對端 SSL 之後，請移至[使用 PowerShell 以應用程式閘道設定端對端 SSL](application-gateway-end-to-end-ssl-powershell.md)，以使用端對端 SSL 來建立應用程式閘道。

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
