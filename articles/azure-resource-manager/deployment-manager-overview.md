---
title: 跨區域的安全部署實務 - Azure Deployment Manager
description: 描述如何使用 Azure 部署管理員在許多區域中部署服務。 它會顯示安全部署實務，以便在推出到所有區域之前確認部署的穩定性。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/13/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: d93d9999c407e64658b88025feda48d33e1a5ad1
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595795"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>啟用安全部署實務與 Azure 部署管理員 （公開預覽）

若要跨多區域部署您的服務，並確定服務在每個區域中皆如預期般執行，您可以使用 Azure 部署管理員來協調服務的分段推出。 如同任何 Azure 部署，您要在 [Resource Manager 範本](resource-group-authoring-templates.md)中定義服務的資源。 在建立範本之後，您要使用部署管理員來描述您服務的拓撲，以及拓撲的推出方式。

部署管理員是 Resource Manager 的一項功能。 此功能可在部署期間延展您的功能。 當您有複雜的服務必須部署到數個區域時，可使用部署管理員。 透過將服務分段推出，您可以在服務部署到所有區域之前，先找出潛在的問題。 如果您不需要分段推出的額外預防措施，請使用適用於 Resource Manager 的標準[部署選項](resource-group-template-deploy-portal.md)。 部署管理員會與支援 Resource Manager 部署的所有現有第三方工具緊密整合，例如持續整合與持續傳遞 (CI/CD) 供應項目。 

Azure Deployment Manager 為個人預覽版。 若要使用 Azure 部署管理員，請完成[報名表](https://aka.ms/admsignup)。 請提供[意見反應](https://aka.ms/admfeedback)協助改進功能。

若要使用部署管理員，您必須建立四個檔案：

* 拓撲範本
* 推出範本
* 拓撲的參數檔案
* 推出的參數檔案

在部署推出範本之前，請部署拓樸範本。

在[這裡](https://docs.microsoft.com/rest/api/deploymentmanager/)可以找到 Azure 部署管理員 REST API 參考。

## <a name="identity-and-access"></a>身分識別與存取

透過部署管理員，[user-assigned managed identity](../active-directory/managed-identities-azure-resources/overview.md) 會執行部署動作。 在開始部署之前，您要建立此身分識別。 此身分識別必須可存取其中將部署服務的訂用帳戶，並且有足夠的權限可完成部署。 如需透過角色所授與的動作相關資訊，請參閱[適用於 Azure 資源的內建角色](../role-based-access-control/built-in-roles.md)。

身分識別必須位於與首度發行相同的位置。

## <a name="topology-template"></a>拓撲範本

拓撲範本會說明構成服務的 Azure 資源及其部署位置。 下圖顯示範例服務的拓撲：

![從服務拓撲到服務再到服務單位的階層](./media/deployment-manager-overview/service-topology.png)

拓撲範本包含下列資源：

* 成品來源 - 儲存您 Resource Manager 範本和參數的位置
* 服務拓撲 - 指向成品來源
  * 服務 - 指定位置和 Azure 訂用帳戶識別碼
    * 服務單位 - 指定資源群組、部署模式，以及範本和參數檔案的路徑

若要了解每個層級所發生的情況，可查看您提供的值。

![每個層級的值](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>範本的成品來源

在您的拓撲範本中，您可以建立包含範本和參數檔案的成品來源。 成品來源是提取檔案進行部署的方法。 您在本文稍後會看到另一個適用於二進位檔的成品來源。

下列範例顯示成品來源的一般格式。

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "<artifact-source-name>",
    "location": "<artifact-source-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "<root-folder-for-templates>",
        "authentication": {
            "type": "SAS",
            "properties": {
                "sasUri": "<SAS-URI-for-storage-container>"
            }
        }
    }
}
```

如需詳細資訊，請參閱 [artifactSources 範本參考](/azure/templates/Microsoft.DeploymentManager/artifactSources)。

### <a name="service-topology"></a>服務拓撲

下列範例顯示服務拓撲來源的一般格式。 您需提供成品來源 (其中保留範本和參數檔案) 的資源識別碼。 服務拓撲包含所有的服務資源。 為確定成品來源可以使用，服務拓撲會以其作為依據。

```json
{
    "type": "Microsoft.DeploymentManager/serviceTopologies",
    "name": "<topology-name>",
    "location": "<topology-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "artifactSourceId": "<resource-ID-artifact-source>"
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "resources": [
        {
            "type": "services",
            ...
        }
    ]
}
```

如需詳細資訊，請參閱 [serviceTopologies 範本參考](/azure/templates/Microsoft.DeploymentManager/serviceTopologies)。

### <a name="services"></a>服務

下列範例顯示服務資源的一般格式。 在每個服務中，您需提供要用於部署您服務的位置與 Azure 訂用帳戶識別碼。 若要部署到數個區域，您可以定義每個區域的服務。 服務相依於服務拓撲。

```json
{
    "type": "services",
    "name": "<service-name>",
    "location": "<service-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service-topology>"
    ],
    "properties": {
        "targetSubscriptionId": "<subscription-ID>",
        "targetLocation": "<location-of-deployed-service>"
    },
    "resources": [
        {
            "type": "serviceUnits",
            ...
        }
    ]
}
```

如需詳細資訊，請參閱[服務範本參考](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services)。

### <a name="service-units"></a>服務單位

下列範例顯示服務單位資源的一般格式。 在每個服務單位中，您要指定資源群組、要用於部署的[部署模式](deployment-modes.md)，以及範本和參數檔案的路徑。 如果您指定範本和參數的相對路徑，則會從成品來源的根資料夾中建構完整路徑。 您可以指定範本和參數的絕對路徑，但您將無法輕鬆地控制發行的版本。 服務單位相依於服務。

```json
{
    "type": "serviceUnits",
    "name": "<service-unit-name>",
    "location": "<service-unit-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service>"
    ],
    "tags": {
        "serviceType": "Service West US Web App"
    },
    "properties": {
        "targetResourceGroup": "<resource-group-name>",
        "deploymentMode": "Incremental",
        "artifacts": {
            "templateArtifactSourceRelativePath": "<relative-path-to-template>",
            "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
        }
    }
}
```

每個範本都應該包含您想要在一個步驟中部署的相關資源。 例如，服務單位可能會有一個範本，部署您服務前端的所有資源。

如需詳細資訊，請參閱 [serviceUnits 範本參考](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits)。

## <a name="rollout-template"></a>推出範本

推出範本會說明部署服務時所應採取的步驟。 您可以指定服務拓撲來使用及定義部署服務單位的順序。 它包含的成品來源可用於儲存部署的二進位檔。 在推出範本中，您可以定義下列階層：

* 成品來源
* 步驟
* 推出
  * 步驟群組
    * 部署作業

下圖顯示推出範本的階層：

![從推出到步驟的階層](./media/deployment-manager-overview/Rollout.png)

每一個推出都可能有許多步驟群組。 每個步驟群組都有一個部署作業，會指向服務拓撲中的服務單位。

### <a name="artifact-source-for-binaries"></a>二進位檔的成品來源

在推出範本中，您會為部署至服務所需的二進位檔建立成品來源。 這個成品來源類似於[範本的成品來源](#artifact-source-for-templates)，只不過它是包含指令碼、網頁、已編譯的程式碼，或是您服務所需的其他檔案。

### <a name="steps"></a>步驟

您可以定義要在部署作業之前或之後執行的步驟。 目前，只有`wait`步驟和 'healthCheck' 步驟可供使用。 

在繼續進行之前，等候步驟會暫停部署。 這可讓您確認在部署下一個服務單位之前，您的服務正在如預期般執行。 下列範例顯示等候步驟的一般格式。

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.DeploymentManager/steps",
    "name": "waitStep",
        "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

持續時間屬性使用 [ISO 8601 標準](https://en.wikipedia.org/wiki/ISO_8601#Durations)。 上述範例會指定一分鐘的等候。

如需有關健全狀況檢查步驟的詳細資訊，請參閱[導入健康情況整合首度發行至 Azure 部署管理員](./deployment-manager-health-check.md)和[教學課程：使用 Azure 部署管理員 」 中的健康情況檢查](./deployment-manager-tutorial-health-check.md)。

如需詳細資訊，請參閱[步驟範本參考](/azure/templates/Microsoft.DeploymentManager/steps)。

### <a name="rollouts"></a>推出

為確定成品來源可以使用，推出會以其作為依據。 推出會定義每個所部署服務單位的步驟群組。 您可以定義要在部署之前或之後採取的動作。 例如，您可以指定已部署服務單位之後的部署等待。 您可以定義步驟群組的順序。

身分識別物件會指定執行部署動作的[使用者指派受控識別](#identity-and-access)。

下列範例會顯示推出的一般格式。

```json
{
    "type": "Microsoft.DeploymentManager/rollouts",
    "name": "<rollout-name>",
    "location": "<rollout-location>",
    "apiVersion": "2018-09-01-preview",
    "Identity": {
        "type": "userAssigned",
        "identityIds": [
            "<managed-identity-ID>"
        ]
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "properties": {
        "buildVersion": "1.0.0.0",
        "artifactSourceId": "<artifact-source-ID>",
        "targetServiceTopologyId": "<service-topology-ID>",
        "stepGroups": [
            {
                "name": "stepGroup1",
                "dependsOnStepGroups": ["<step-group-name>"],
                "preDeploymentSteps": ["<step-ID>"],
                "deploymentTargetId":
                    "<service-unit-ID>",
                "postDeploymentSteps": ["<step-ID>"]
            },
            ...
        ]
    }
}
```

如需詳細資訊，請參閱[推出範本參考](/azure/templates/Microsoft.DeploymentManager/rollouts)。

## <a name="parameter-file"></a>參數檔案

您會建立兩個參數檔案。 部署服務拓撲時，會使用一個參數檔案，另一個則用於推出部署。 您必須確定這兩個參數檔案中的某些值是相同的。  

## <a name="containerroot-variable"></a>containerRoot 變數

透過已建立版本的部署，您的成品路徑會隨著每個新版本而有所變更。 您第一次執行部署的路徑可能是 `https://<base-uri-blob-container>/binaries/1.0.0.0`。 第二次可能會是 `https://<base-uri-blob-container>/binaries/1.0.0.1`。 部署管理員會使用 `$containerRoot` 變數來輕鬆取得目前部署的正確根路徑。 這個值會隨著每個版本變更，且在部署之前是未知的。

使用範本參數檔案中的 `$containerRoot` 變數來部署 Azure 資源。 在部署期間，此變數會取代為推出中的實際值。 

例如，在推出期間，您會建立二進位成品的成品來源。

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "[variables('rolloutArtifactSource').name]",
    "location": "[parameters('azureResourceLocation')]",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "[parameters('binaryArtifactRoot')]",
        "authentication" :
        {
            "type": "SAS",
            "properties": {
                "sasUri": "[parameters('artifactSourceSASLocation')]"
            }
        }
    }
},
```

請注意 `artifactRoot` 和 `sasUri` 屬性。 成品根目錄可能會設定為類似 `binaries/1.0.0.0` 的值。 SAS URI 是您儲存體容器的 URI，包含可供存取的 SAS 權杖。 部署管理員會自動建構 `$containerRoot` 變數的值。 它以 `<container>/<artifactRoot>` 格式結合這些值。

您的範本和參數檔案必須知道取得已建立版本二進位檔的正確路徑。 例如，若要部署 Web 應用程式的檔案，請使用 $containerRoot 變數來建立下列參數檔案。 您必須在路徑中使用兩個反斜線 (`\\`)，因為第一個是逸出字元。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "deployPackageUri": {
            "value": "$containerRoot\\helloWorldWebAppWUS.zip"
        }
    }
}
```

然後，在您的範本中使用該參數：

```json
{
    "name": "MSDeploy",
    "type": "extensions",
    "location": "[parameters('location')]",
    "apiVersion": "2015-08-01",
    "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
    ],
    "tags": {
        "displayName": "WebAppMSDeploy"
    },
    "properties": {
        "packageUri": "[parameters('deployPackageURI')]"
    }
}
```

您可以藉由建立新的資料夾並在推出期間傳入該根來管理版本控制的部署。 路徑會流經部署資源的範本。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解部署管理員的相關資訊。 請前往下一篇文章，以了解如何使用部署管理員進行部署。

> [!div class="nextstepaction"]
> [教學課程：使用 Azure 部署管理員搭配 Resource Manager 範本](./deployment-manager-tutorial.md)