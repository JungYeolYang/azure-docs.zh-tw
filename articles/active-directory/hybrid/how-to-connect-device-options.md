---
title: Azure AD Connect：裝置選項 |Microsoft Docs
description: 本文件詳述 Azure AD Connect 中可用的裝置選項
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8f397401581cc1bc947f1b19ce248c17fba26f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245642"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect：裝置選項

下列文件提供與 Azure AD Connect 中的各種可用裝置選項有關的資訊。 您可以使用 Azure AD Connect 來設定下列兩項作業： 
* **混合式 Azure AD Join**：如果您的環境具有內部部署 AD 使用量，而且您想要獲得 Azure AD 的好處，您可以實作已加入混合式 Azure AD 的裝置。 這些裝置會加入您的內部部署 Active Directory 和您的 Azure Active Directory。
* **裝置回寫**：裝置回寫用於對 AD FS (2012 R2 或更高版本) 保護的裝置啟用裝置型條件式存取

## <a name="configure-device-options-in-azure-ad-connect"></a>設定 Azure AD Connect 中的裝置選項

1.  執行 Azure AD Connect。 在 [其他工作] 頁面中，選取 [設定裝置選項]。  单击“下一步”。
    ![設定裝置選項](./media/how-to-connect-device-options/deviceoptions.png) 

    [概觀] 頁面顯示詳細資料。
    ![概觀](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > 新的設定裝置選項僅適用於 1.1.819.0 版和更新版本。

2.  提供 Azure AD 的認證後，您可以選擇要在 [裝置選項] 頁面上執行的作業。
    ![裝置作業](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>後續步驟

* [設定加入混合式 Azure AD](../device-management-hybrid-azuread-joined-devices-setup.md)
* [設定/停用裝置回寫](how-to-connect-device-writeback.md)

