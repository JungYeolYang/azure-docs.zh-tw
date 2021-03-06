---
title: 快速入門 - 在 Azure Container Registry 中建置和執行容器映像
description: 使用 Azure Container Registry 快速執行工作，在雲端中建置和部署隨選的容器映像。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: be120ea8ae588da486c9a5acd4eb7bfdb4e45dee
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701558"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>快速入門：使用 Azure Container Registry 工作建置和執行容器映像

在本快速入門中，您將使用 Azure Container Registry 工作命令在 Azure 中以原生方式快速建置、推送及執行 Docker 容器映像，以了解如何將「內部迴圈」開發週期卸載至雲端。 [ACR 工作][container-registry-tasks-overview]是 Azure Container Registry 內的一組功能，可協助您跨容器生命週期管理及修改容器映像。 

完成此快速入門後，請進一步探索 ACR 工作的進階功能。 ACR 工適用於多種案例，包括根據程式碼認可或基底映像更新將映像建置自動化，或是以平行方式測試多個容器。 

如果您沒有 Azure 訂用帳戶，請在開始前建立 [[免費帳戶]][azure-account]。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成此快速入門。 如果您想要在本機使用，建議使用 2.0.58 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="create-a-resource-group"></a>建立資源群組

如果您還沒有容器登錄，您可以使用 [az group create][az-group-create] 命令建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>建立容器登錄庫

使用 [az acr create][az-acr-create] 命令建立容器登錄。 登錄名稱在 Azure 內必須是唯一的，且包含 5-50 個英數字元。 下列範例將使用 *myContainerRegistry008*。 請將此更新為唯一的值。

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry008 --sku Basic
```

此範例會建立「基本」登錄，這是正在學習 Azure Container Registry 的開發人員所適用的成本最佳化選項。 如需可用服務層級的詳細資訊，請參閱[容器登錄 SKU][container-registry-skus]。

## <a name="build-an-image-from-a-dockerfile"></a>從 Dockerfile 建置映像

現在，請使用 Azure Container Registry 建置映像。 首先請建立工作目錄，然後使用下列內容建立名為 *Dockerfile* 的 Dockerfile。 這是建置 Linux 容器映像的簡單範例，但您可以自行建立標準 Dockerfile，並建置適用於其他平台的映像。

```bash
echo FROM hello-world > Dockerfile
```

執行 [az acr build][az-acr-build] 命令以建置映像。 成功建置後，映像會推送至您的登錄。 下列範例會推送 `sample/hello-world:v1` 映像。 命令結尾處的 `.` 會設定 Dockerfile 的位置，在此案例中為目前的目錄。

```azurecli-interactive
az acr build --image sample/hello-world:v1 --registry myContainerRegistry008 --file Dockerfile . 
```

成功的建置會產生如下的輸出並推送：

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM hello-world
latest: Pulling from library/hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>執行映像

現在，請快速執行您已建置並推送至登錄的映像。 在容器開發工作流程中，這可能是您部署映像之前的驗證步驟。

在本機工作目錄中使用下列內容建立 *quickrun.yaml* 檔案，以進行單一步驟。 請將 *\<acrLoginServer\>* 取代為您登錄的登入伺服器名稱。 登入伺服器名稱的格式為 *\<registry-name\>.azurecr.io* (全部小寫)，例如 *mycontainerregistry008.azurecr.io*。 此範例假設您已在上一節中建置並推送 `sample/hello-world:v1` 映像：

```yml
steps:
  - cmd: <acrLoginServer>/sample/hello-world:v1
```

在此範例中的 `cmd` 步驟會執行其預設組態中的容器，但 `cmd` 支援其他 `docker run` 參數甚或其他 `docker` 命令。

使用下列命令執行容器：

```azurecli-interactive
az acr run --registry myContainerRegistry008 --file quickrun.yaml .
```

輸出大致如下：

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete][az-group-delete] 命令移除資源群組、容器登錄，以及儲存於該處的容器映像。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已使用 ACR 工作的功能，以原生方式在 Azure 中快速建置、推送及執行 Docker 容器映像。 請繼續進行 Azure Container Registry 教學課程，以了解如何使用 ACR 工作將映像建置和更新自動化。

> [!div class="nextstepaction"]
> [Azure Container Registry 教學課程][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
