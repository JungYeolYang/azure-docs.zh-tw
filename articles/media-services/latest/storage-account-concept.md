---
title: 使用 Azure 媒體服務進行雲端上傳和儲存 | Microsoft Docs
description: 本文提供雲端上傳和儲存體的概念。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/11/2019
ms.author: juliako
ms.openlocfilehash: 9cbb995eb3310a2263185d6fd6dba20efce37f38
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550154"
---
# <a name="cloud-upload-and-storage"></a>雲端上傳和儲存體

若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 建立媒體服務帳戶時，您需要提供 Azure 儲存體帳戶資源的名稱。 指定的儲存體帳戶會附加到您的媒體服務帳戶。 

媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。 強烈建議使用與媒體服務帳戶相同位置中的儲存體帳戶，以避免產生額外的延遲和資料輸出費用。

您只能有一個**主要**儲存體帳戶，而與您的媒體服務帳戶相關聯的**次要**儲存體帳戶可以有任意數量。 媒體服務支援**一般用途 v2** (GPv2) 或**一般用途 v1** (GPv1) 帳戶。 

>[!NOTE]
> 僅有 Blob 的帳戶不允許作為**主要**帳戶。 

我們建議您使用 GPv2，這樣便能在經常性存取層與非經常性存取層之間做選擇。 若要深入了解儲存體帳戶，請參閱 [Azure 儲存體帳戶概觀](../../storage/common/storage-account-overview.md)。 

有不同的 Sku，您可以選擇儲存體帳戶。 如需詳細資訊，請參閱[儲存體帳戶](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)。 如果您想要以儲存體帳戶進行試驗，請使用 `--sku Standard_LRS`。 不過，在選擇用於生產環境的 SKU 時應考慮使用 `--sku Standard_RAGRS`，以提供地理複寫功能而確保商務持續性。 

## <a name="assets-in-a-storage-account"></a>儲存體帳戶中的資產

在媒體服務 v3，儲存體 Api 來將檔案上傳到資產。 如需詳細資訊，請參閱 <<c0> [ 資產概念](assets-concept.md)。

> [!Note]
> blob 容器若是由媒體服務 SDK 產生時，請務必使用媒體服務 API 變更 blob 容器的內容。
 
## <a name="storage-side-encryption"></a>儲存端加密

若要保護待用資產，資產應該透過儲存端加密來進行加密。 下表顯示儲存端加密在媒體服務 v3 中的運作方式：

|加密選項|說明|媒體服務 v3|
|---|---|---|
|媒體服務的儲存體加密| AES-256 加密，由媒體服務管理金鑰|不受支援<sup>(1)</sup>|
|[待用資料的儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure 儲存體提供的伺服器端加密，由 Azure 或客戶管理金鑰|支援|
|[儲存體用戶端加密](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure 儲存體提供的用戶端加密，由客戶管理 Key Vault 中的金鑰|不支援|

<sup>1</sup> 在媒體服務 v3 中，如果您的資產是以媒體服務 v2 建立，則儲存體加密 (AES-256 加密) 只對回溯相容性有所支援。 這表示 v3 可用於現有的儲存體加密資產，但不允許建立新的。

## <a name="storage-account-errors"></a>儲存體帳戶錯誤

媒體服務帳戶的 「 中斷連接 」 狀態指出帳戶不再在儲存體存取金鑰具有一或多個連結的儲存體帳戶，由於變更的存取權。 最新的儲存體存取金鑰，才能由 Media Services 帳戶中執行許多工作。

以下是可能會導致無法存取連結的儲存體帳戶的媒體服務帳戶的主要案例。 

|問題|解決方法|
|---|---|
|媒體服務帳戶或連結的儲存體帳戶已移轉至不同的訂用帳戶中。 |將媒體服務帳戶的儲存體帳戶的移轉，使它們全都放在相同的訂用帳戶。 |
|媒體服務帳戶不同的訂用帳戶中使用連結的儲存體帳戶，因為它是早期的媒體服務帳戶，這支援。 所有早期的媒體服務帳戶已轉換為最新的 Azure 資源管理員 (ARM) 架構帳戶，而且必須中斷連線的狀態。 |移轉儲存體帳戶或媒體服務帳戶，使它們全都放在相同的訂用帳戶。|

## <a name="next-steps"></a>後續步驟

若要深入了解如何將儲存體帳戶附加到媒體服務帳戶，請參閱[建立帳戶](create-account-cli-quickstart.md)。
