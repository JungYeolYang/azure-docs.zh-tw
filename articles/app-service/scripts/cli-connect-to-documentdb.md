---
title: Azure CLI 指令碼範例 - 將應用程式連線至 MongoDB (Cosmos DB) | Microsoft Docs
description: Azure CLI 指令碼範例 - 將應用程式連線至 MongoDB (Cosmos DB)
services: appservice
documentationcenter: appservice
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 68c69413322d87fc557686701c474730d2ed8405
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358279"
---
# <a name="connect-an-app-service-app-to-cosmos-db-using-cli"></a>使用 CLI 將 App Service 應用程式連線至 Cosmos DB

此範例指令碼會使用 Azure Cosmos DB 的 MongoDB 版 API 和 App Service 應用程式來建立 Azure Cosmos DB 帳戶。 然後使用應用程式設定，將 MongoDB 連接字串連結到 Web 應用程式。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，需要 Azure CLI 2.0 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立資源群組、App Service 應用程式、Cosmos DB 及所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | 建立 App Service 方案。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | 建立 App Service 應用程式。 |
| [`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-create) | 建立 Cosmos DB 帳戶。 |
| [`az cosmosdb list-connection-strings`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-list-connection-strings) | 列出所指定 Cosmos DB 帳戶的連接字串。 |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) | 建立或更新 App Service 應用程式的應用程式設定。 應用程式設定會顯示為應用程式的環境變數。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure App Service 文件](../samples-cli.md)中找到其他的 App Service CLI 指令碼範例。
