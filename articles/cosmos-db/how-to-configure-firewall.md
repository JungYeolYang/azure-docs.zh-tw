---
title: 設定 Azure Cosmos DB 帳戶的 IP 防火牆
description: 了解如何設定 IP 存取控制原則，以提供 Azure Cosmos DB 資料庫帳戶上的防火牆支援。
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: cdf2da745cc418190f6546fffc03e2ac2c330e0e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068727"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中設定 IP 防火牆

您可以使用 IP 防火牆來保護 Azure Cosmos DB 帳戶中所儲存的資料。 Azure Cosmos DB 支援 IP 型存取控制，以提供輸入防火牆支援。 您可以使用下列其中一種方法，在 Azure Cosmos DB 帳戶上設定 IP 防火牆：

* 從 Azure 入口網站
* 使用 Azure Resource Manager 範本，以宣告方式進行設定
* 藉由更新 **ipRangeFilter** 屬性，透過 Azure CLI 或 Azure PowerShell 以程式設計方式進行設定

## <a id="configure-ip-policy"></a> 使用 Azure 入口網站設定 IP 防火牆

若要在 Azure 入口網站中設定 IP 存取控制原則，請移至 Azure Cosmos DB 帳戶頁面，然後在導覽功能表中選取 [防火牆與虛擬網路]。 將 [允許從下項存取] 值變更為 [選取的網路]，然後選取 [儲存]。 

![顯示如何在 Azure 入口網站中開啟 [防火牆] 頁面的螢幕擷取畫面](./media/how-to-configure-firewall/azure-portal-firewall.png)

開啟 IP 存取控制時，Azure 入口網站就能指定 IP 位址、IP 位址範圍及參數。 這些參數可啟用對其他 Azure 服務與 Azure 入口網站的存取。 下列各節將提供這些參數的詳細資訊。

> [!NOTE]
> 當您啟用 Azure Cosmos DB 帳戶的 IP 存取控制原則之後，即會拒絕所允許 IP 位址範圍清單外部的電腦對 Azure Cosmos DB 帳戶的所有要求。 從入口網站瀏覽 Azure Cosmos DB 資源也會遭到封鎖，以確保存取控制的完整性。

### <a name="allow-requests-from-the-azure-portal"></a>允許來自 Azure 入口網站的要求

當您以程式設計方式啟用 IP 存取控制原則時，您必須將 Azure 入口網站的 IP 位址加入到 **ipRangeFilter** 屬性以維持存取權。 入口網站 IP 位址是：

|區域|IP 位址|
|------|----------|
|德國|51.4.229.218|
|中國|139.217.8.252|
|US Gov|52.244.48.71|
|所有其他區域|104.42.195.92、40.76.54.131、52.176.6.30、52.169.50.45、52.187.184.26|

您可以選取 [允許從 Azure 入口網站存取] 選項，來啟用對 Azure 入口網站的存取，如下列螢幕擷取畫面所示： 

![顯示如何允許存取 Azure 入口網站的螢幕擷取畫面](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>允許來自全球 Azure 資料中心或 Azure 中其他來源的要求

如果您從不提供靜態 IP 的服務 (例如 Azure 串流分析和 Azure Functions) 存取 Azure Cosmos DB 帳戶，您仍然可以使用 IP 防火牆來限制存取。 若要允許從這類服務存取 Azure Cosmos DB 帳戶，請將 IP 位址 0.0.0.0 新增至允許的 IP 位址清單。 0.0.0.0 位址會限制只能從 Azure 資料中心 IP 範圍向您的 Azure Cosmos DB 帳戶提出要求。 此設定不允許針對任何其他 IP 範圍存取您的 Azure Cosmos DB 帳戶。

> [!NOTE]
> 這個選項會將防火牆設定為允許所有來自 Azure 的連線，包括來自 Azure 中所部署之其他客戶訂用帳戶的連線。 此選項所允許的 IP 清單範圍寬鬆，因而限制了防火牆原則的效果。 只有當您的要求不是來自靜態 IP 或虛擬網路中的子網路時，才使用此選項。 由於 Azure 入口網站部署於 Azure 中，因此，選擇此選項就會自動允許從 Azure 入口網站存取。

您可以選取 [接受來自公用 Azure 資料中心內的連線] 選項，來啟用對 Azure 入口網站的存取，如下列螢幕擷取畫面所示： 

![顯示如何在 Azure 入口網站中開啟 [防火牆] 頁面的螢幕擷取畫面](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>來自您目前 IP 的要求

為了簡化開發工作，Azure 入口網站協助您識別用戶端電腦的 IP 並新增至允許清單。 您電腦上執行的應用程式可以接著存取 Azure Cosmos DB 帳戶。 

入口網站會自動偵測用戶端 IP 位址。 它可能是您電腦的用戶端 IP 位址或網路閘道的 IP 位址。 請務必在將您的工作負載投入生產環境之前移除此 IP 位址。 

若要將您目前的 IP 新增至 IP 清單，請選取 [新增我目前的 IP]。 然後選取 [儲存]。

![顯示如何為目前的 IP 進行防火牆設定的螢幕擷取畫面](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>來自雲端服務的要求

在 Azure 中，雲端服務是使用 Azure Cosmos DB 裝載中介層服務邏輯的常見方式。 若要從雲端服務啟用對 Azure Cosmos DB 帳戶的存取，您必須[設定 IP 存取控制原則](#configure-ip-policy)，以將雲端服務的公用 IP 位址新增至與您 Azure Cosmos DB 帳戶相關聯的允許 IP 位址清單。 這確保雲端服務的所有角色執行個體都能存取您的 Azure Cosmos DB 帳戶。 

您可以在 Azure 入口網站中擷取雲端服務的 IP 位址，如下列螢幕擷取畫面所示：

![這個螢幕擷取畫面顯示 Azure 入口網站中所顯示雲端服務的公用 IP 位址](./media/how-to-configure-firewall/public-ip-addresses.png)

當您藉由新增角色執行個體來相應放大雲端服務時，那些新的執行個體將可自動存取 Azure Cosmos DB 帳戶，因為它們是相同雲端服務的一部分。

### <a name="requests-from-virtual-machines"></a>來自虛擬機器的要求

您也可以使用[虛擬機器](https://azure.microsoft.com/services/virtual-machines/)或[虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)，透過 Azure Cosmos DB 裝載中介層服務。 若要將您的 Cosmos DB 帳戶設定為允許來自虛擬機器的存取，您必須[設定 IP 存取控制原則](#configure-ip-policy)，以將虛擬機器和 (或) 虛擬機器擴展集的公用 IP 位址設定為 Azure Cosmos DB 帳戶的其中一個允許 IP 位址。 

您可以在 Azure 入口網站中擷取虛擬機器的 IP 位址，如下列螢幕擷取畫面所示：

![這個螢幕擷取畫面顯示 Azure 入口網站中所顯示虛擬機器的公用 IP 位址](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

當您將虛擬機器執行個體新增至群組時，它們即可自動存取您的 Azure Cosmos DB 帳戶。

### <a name="requests-from-the-internet"></a>來自網際網路的要求

從網際網路上的電腦存取 Azure Cosmos DB 帳戶時，必須將電腦的用戶端 IP 位址或 IP 位址範圍新增至您帳戶的允許 IP 位址清單。

## <a id="configure-ip-firewall-arm"></a>使用 Resource Manager 範本設定 IP 防火牆

為了設定 Azure Cosmos DB 帳戶的存取控制，請確定 Resource Manager 範本指定 **ipRangeFilter** 屬性，其中含有允許的 IP 範圍清單。 例如，將下列 JSON 程式碼新增至您的範本：

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
       "databaseAccountOfferType": "Standard",
       "name": "[parameters('databaseAccountName')]",
       "ipRangeFilter":"183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
     }
   }
```

## <a id="configure-ip-firewall-cli"></a>使用 Azure CLI 設定 IP 存取控制原則

下列命令示範如何使用 IP 存取控制來建立 Azure Cosmos DB 帳戶： 

```azurecli-interactive

name="<Azure Cosmos DB account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

若要更新現有帳戶的防火牆設定，請執行下列命令：

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="configure-ip-firewall-ps"></a>使用 PowerShell 設定 IP 存取控制原則

下列指令碼說明如何使用 IP 存取控制來建立 Azure Cosmos DB 帳戶：

```azurepowershell-interactive

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

# Add local machine's IP address to firewall, InterfaceAlias is your Network Adapter's name
$ipRangeFilter = Get-NetIPConfiguration | Where-Object InterfaceAlias -eq "Ethernet 2" | Select-Object IPv4Address

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "ipRangeFilter"=$ipRangeFilter
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="troubleshoot-ip-firewall"></a>針對 IP 存取控制原則問題進行疑難排解

您可以使用下列選項，針對 IP 存取控制原則問題進行疑難排解： 

### <a name="azure-portal"></a>Azure 入口網站 
藉由啟用 Azure Cosmos DB 帳戶的 IP 存取控制原則，您可以封鎖所允許 IP 位址範圍清單外部的電腦對您帳戶的所有要求。 若要啟用入口網站資料平面作業 (例如瀏覽容器和查詢文件)，則需要在入口網站中使用 [防火牆] 窗格，明確地允許 Azure 入口網站存取。

### <a name="sdks"></a>SDK 
當您從不在允許清單中的電腦使用 SDK 來存取 Azure Cosmos DB 資源時，將會傳回一般的 **403 禁止**回應，且沒有其他詳細資料。 請確認您帳戶的允許 IP 清單，並確保會將正確的原則設定套用至您的 Azure Cosmos DB 帳戶。 

### <a name="source-ips-in-blocked-requests"></a>已封鎖要求中的來源 IP
啟用 Azure Cosmos DB 帳戶的診斷記錄。 這些記錄會顯示每個要求和回應。 防火牆相關的訊息會以 403 傳回碼進行記錄。 藉由篩選這些訊息，您就能看到已封鎖要求的來源 IP。 請參閱 [Azure Cosmos DB 診斷記錄](logging.md)。

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>要求來自具有已啟用 Azure Cosmos DB 之服務端點的子網路
若要求來自虛擬網路中具有已啟用 Azure Cosmos DB 之服務端點的子網路，即會將虛擬網路和子網路身分識別傳送至 Azure Cosmos DB 帳戶。 這些要求沒有來源的公用 IP，因此 IP 篩選器會予以拒絕。 若要允許來自虛擬網路中特定子網路的存取，請新增[如何針對 Azure Cosmos DB 帳戶設定虛擬網路和子網路型存取](how-to-configure-vnet-service-endpoint.md)中所述的存取控制清單。 套用防火牆規則最多可能需要 15 分鐘。


## <a name="next-steps"></a>後續步驟

若要設定 Azure Cosmos DB 帳戶的虛擬網路服務端點，請參閱下列文章：

* [適用於 Azure Cosmos DB 帳戶的虛擬網路和子網路存取控制](vnet-service-endpoint.md)
* [針對 Azure Cosmos DB 帳戶設定虛擬網路和子網路型存取](how-to-configure-vnet-service-endpoint.md)

