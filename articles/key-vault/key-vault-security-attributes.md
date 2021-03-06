---
title: Azure 金鑰保存庫的常見安全性屬性
description: 用來評估 Azure Key Vault 的常見安全性屬性檢查清單
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 652e6ecb9ef9506fd306942eae45b13b52ae1c24
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691834"
---
# <a name="common-security-attributes-for-azure-key-vault"></a>Azure 金鑰保存庫的常見安全性屬性

Azure 服務的各個層面都整合了安全性。 本文记录了内置到 Azure Key Vault 中的常见安全特性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 | 所有物件都會加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | 所有通訊皆透過加密的 API 呼叫來進行 |
| 加密金鑰處理 （CMK、 BYOK）| 是 | 客戶控制其金鑰保存庫中的所有索引鍵。 當指定的硬體安全性模組 (HSM) 備份金鑰時，FIPS 層級 2 HSM 保護金鑰、 憑證或密碼。 |
| 資料行層級加密 (Azure Data Services)| N/A |  |
| API 呼叫加密| 是 | 使用 HTTPS。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 使用虛擬網路 (VNet) 服務端點。 |
| VNet 插入支援| 否 |  |
| 網路隔離，而且防火牆支援| 是 | 使用 VNet 防火牆規則。 |
| 強制通道的支援| 否 |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 使用 Log Analytics。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 授權| 是 | 使用 Key Vault 存取原則。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面記錄和稽核| 是 | 使用 Log Analytics。 |
| 資料平面記錄與稽核| 是 | 使用 Log Analytics。 |

## <a name="access-controls"></a>存取控制

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面存取控制 | 是 | Azure Resource Manager 角色型存取控制 (RBAC) |
| 資料平面存取控制 (在每個服務層級上) | 是 | Key Vault 存取原則 |