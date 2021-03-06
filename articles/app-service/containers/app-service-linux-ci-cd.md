---
title: 以用於容器的 Web App 進行持續部署 - Azure App Service | Microsoft Docs
description: 如何在用於容器的 Web App 中設定持續部署。
keywords: azure app service, linux, docker, acr,oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2018
ms.author: yili
ms.custom: seodec18
ms.openlocfilehash: 4acadc4c08ef50e7d52303689c38c43f81187669
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852487"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>使用用於容器的 Web 應用程式進行持續部署

在本教學課程中，您可以從受控 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 存放庫或 [Docker Hub](https://hub.docker.com) 設定自訂容器映像的持續部署。

## <a name="enable-continuous-deployment-with-acr"></a>透過 ACR 啟用持續部署

![ACR Webhook 的螢幕擷取畫面](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取頁面左側的 [App Service]  選項。
3. 選取您要設定持續部署的應用程式名稱。
4. 在 [容器設定] 頁面上，選取 [單一容器]
5. 選取 [Azure Container Registry]
6. 選取 [持續部署] > [開啟]
7. 選取 [儲存] 以啟用持續部署。

## <a name="use-the-acr-webhook"></a>使用 ACR Webhook

啟用持續部署之後，您可以在 Azure Container Registry Webhook 頁面上檢視新建立的 Webhook。

![ACR Webhook 的螢幕擷取畫面](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

在 Container Registry 中，按一下 Webhook 以檢視目前的 Webhook。

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>透過 Docker Hub 啟用持續部署 (選用)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取頁面左側的 [App Service]  選項。
3. 選取您要設定持續部署的應用程式名稱。
4. 在 [容器設定] 頁面上，選取 [單一容器]
5. 選取 [Docker Hub]
6. 選取 [持續部署] > [開啟]
7. 選取 [儲存] 以啟用持續部署。

![應用程式設定的螢幕擷取畫面](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

複製 Webhook URL。 若要新增 Docker Hub 的 Webhook，請遵循 <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Docker Hub 的 Webhook</a>。

## <a name="next-steps"></a>後續步驟

* [Linux 上的 Azure App Service 簡介](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [在 Linux 上的 App Service 中建立.NET Core Web 應用程式](quickstart-dotnetcore.md)
* [在 Linux 上的 App Service 中建立 Ruby Web 應用程式](quickstart-ruby.md)
* [在用於容器的 Web App 中部署 Docker/Go Web 應用程式](quickstart-docker-go.md)
* [Linux 上的 Azure App Service 常見問題集](./app-service-linux-faq.md)
* [使用 Azure CLI 管理用於容器的 Web App](./app-service-linux-cli.md)
