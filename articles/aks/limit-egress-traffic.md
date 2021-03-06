---
title: 限制輸出流量的 Azure Kubernetes Service (AKS)
description: 了解哪些連接埠和位址都需要以控制 Azure Kubernetes Service (AKS) 中的輸出流量
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: de0ba13a527569e446a44c275b7323d4487f53b6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780304"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>預覽-限制輸出流量的叢集節點和控制存取權所需的連接埠和服務在 Azure Kubernetes Service (AKS)

根據預設，AKS 叢集中具有無限制存取網際網路的輸出 （輸出）。 節點與您執行視需要存取外部資源的服務，可讓此層級的網路存取。 如果您想要限制輸出流量，有限的數目的連接埠和位址必須能夠維護狀況良好的叢集維護工作。 您的叢集則會設定為僅使用 從 Microsoft 容器登錄 (MCR) 或 Azure Container Registry (ACR)，不是外部公用儲存機制的基底的系統容器映像中。

本文詳細說明哪些網路連接埠和完整的網域名稱 (Fqdn) 是必要和選擇性，如果您限制在 AKS 叢集中的輸出流量。  此功能目前為預覽狀態。

> [!IMPORTANT]
> AKS 预览功能是自助服务和可以选择加入的功能。 提供预览是为了从我们的社区收集反馈和 bug。 但是，Azure 技术支持部门不为其提供支持。 如果你创建一个群集，或者将这些功能添加到现有群集，则除非该功能不再为预览版并升级为公开发布版 (GA)，否则该群集不会获得支持。
>
> 如果遇到预览版功能的问题，请[在 AKS GitHub 存储库中提交问题][aks-github]，并在 Bug 标题中填写预览版功能的名称。

## <a name="before-you-begin"></a>開始之前

您需要 Azure CLI 2.0.61 版或更新版本安裝並設定。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

若要建立的 AKS 叢集，可能會限制輸出流量，請先啟用您訂用帳戶的功能旗標。 此功能註冊會設定任何您要使用的基本系統從 MCR 或 ACR 的容器映像建立的 AKS 叢集。 若要註冊*AKSLockingDownEgressPreview*功能旗標，請使用[az 功能註冊][ az-feature-register]命令，在下列範例所示：

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

狀態需要幾分鐘的時間才會顯示「已註冊」。 您可以藉由檢查註冊狀態[az 功能清單][ az-feature-list]命令：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

準備好時，重新整理的註冊*Microsoft.ContainerService*使用的資源提供者[az provider register] [ az-provider-register]命令：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>輸出流量概觀

對於管理和作業目的，在 AKS 叢集中的節點需要存取特定的連接埠和完整的網域名稱 (Fqdn)。 這些動作可以進行通訊與 API 伺服器，或下載並安裝核心 Kubernetes 叢集元件和節點的安全性更新。 根據預設，輸出 （輸出） 的網際網路流量不會限制在 AKS 叢集中的節點。 叢集可能提取基底的系統容器映像從外部存放庫。

若要增加您的 AKS 叢集的安全性，您可能想要限制輸出流量。 叢集已設定為提取基底的系統從 MCR 或 ACR 的容器映像。 如果您鎖定的輸出流量，以這種方式時，您必須定義特定連接埠，以便正確地與所需的外部服務進行通訊的 AKS 節點的 Fqdn。 如果沒有這些授權的連接埠和 Fqdn，AKS 節點無法與 API 伺服器通訊，或安裝核心元件。

您可以使用[Azure 防火牆][ azure-firewall]或第 3 方防火牆應用裝置來保護您的輸出流量，並定義這些必要連接埠和位址。

在 AKS，有兩組的連接埠和位址：

* [AKS 叢集所需的連接埠和位址](#required-ports-and-addresses-for-aks-clusters)詳述已獲授權的輸出流量的最低需求。
* [選擇性的建議地址和 AKS 叢集中的連接埠](#optional-recommended-addresses-and-ports-for-aks-clusters)並不需要所有的案例，但與其他服務整合，例如 Azure 監視器將無法正常運作。 檢閱的選擇性連接埠和 Fqdn，這份清單，並授權的任何服務和 AKS 叢集中使用的元件。

> [!NOTE]
> 限制輸出流量僅適用於新的 AKS 叢集建立之後啟用功能旗標註冊。 現有的叢集，如[執行叢集升級作業][ aks-upgrade]使用`az aks upgrade`命令之前限制輸出流量。

## <a name="required-ports-and-addresses-for-aks-clusters"></a>必要的連接埠和位址 AKS 叢集

下列的輸出連接埠 / 網路規則所需的 AKS 叢集：

* TCP 連接埠*443*
* TCP 連接埠*9000*

下列的 FQDN / 必須要有應用程式規則：

| FQDN                      | Port      | 使用情況      |
|---------------------------|-----------|----------|
| *.azmk8s.io               | HTTPS:443 | 此位址是 API 的伺服器端點。 |
| aksrepos.azurecr.io       | HTTPS:443 | 此位址，才能存取映像在 Azure Container Registry (ACR)。 |
| *.blob.core.windows.net   | HTTPS:443 | 此位址是儲存在 ACR 中映像的後端存放區。 |
| mcr.microsoft.com         | HTTPS:443 | 此位址，才能存取映像在 Microsoft 容器登錄 (MCR)。 |
| management.azure.com      | HTTPS:443 | Kubernetes GET/PUT 作業需要此位址。 |
| login.microsoftonline.com | HTTPS:443 | Azure Active Directory 驗證需要此位址。 |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>選擇性的建議地址和 AKS 叢集中的連接埠

下列的輸出連接埠 / 網路規則不需要的 AKS 叢集，才能正確運作，但建議使用：

* UDP 連接埠*123* NTP 時間同步處理
* UDP 連接埠*53* dns

下列的 FQDN / 建議的 AKS 叢集，才能正確運作的應用程式規則：

| FQDN                                    | Port      | 使用情況      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | 此位址可讓您下載的必要的安全性修補程式和更新的 Linux 叢集節點。 |
| packages.microsoft.com                  | HTTPS:443 | 此位址是使用 Microsoft 封裝存放庫的快取*apt get*作業。 |
| dc.services.visualstudio.com            | HTTPS:443 | 建議針對正確的計量，並使用 Azure 監視器監視。 |
| *.opinsights.azure.com                  | HTTPS:443 | 建議針對正確的計量，並使用 Azure 監視器監視。 |
| *.monitoring.azure.com                  | HTTPS:443 | 建議針對正確的計量，並使用 Azure 監視器監視。 |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | （目前在 AKS 中的預覽） 的正常運作的 Azure 原則使用此位址。 |
| apt.dockerproject.org                   | HTTPS:443 | 使用此位址是正確的驅動程式安裝和以 GPU 為基礎的節點上的作業。 |
| nvidia.github.io                        | HTTPS:443 | 使用此位址是正確的驅動程式安裝和以 GPU 為基礎的節點上的作業。 |

## <a name="next-steps"></a>後續步驟

在本文中，您已了解哪些連接埠和位址，以允許則限制為叢集的輸出流量。 您也可以定義自己的 pod 可以通訊的方式和限制能在叢集內。 如需詳細資訊，請參閱 <<c0> [ 保護在 AKS 中使用網路原則的 pod 之間的流量][network-policy]。

<!-- LINKS - external -->
[aks-github]: https://github.com/azure/aks/issues]

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
