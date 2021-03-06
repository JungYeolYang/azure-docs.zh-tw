---
title: 使用警示來觸發 Azure 自動化 Runbook
description: 了解如何在引發 Azure 警示時觸發 Runbook 執行。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8a1ae906a72d781f638fb171a409b860ffa6d501
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517711"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>使用警示來觸發 Azure 自動化 Runbook

您可以使用 [Azure 監視器](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json)來監視 Azure 中大多數服務的基準層級計量和記錄。 您可以使用[動作群組](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json)或使用傳統警示來呼叫 Azure 自動化 Runbook，以根據警示來自動執行工作。 本文示範如何使用警示來設定及執行 Runbook。

## <a name="alert-types"></a>警示類型

您可以將自動化 Runbook 與三種警示類型搭配使用：

* 常見的警示
* 活動記錄警示
* 近乎即時的計量警示

> [!NOTE]
> 常見的警示結構描述會立即標準化的使用體驗，在 Azure 中的警示通知。 在過去，三種警示類型在 Azure 中的立即 （計量、 記錄和活動記錄檔） 有它們自己的電子郵件範本、 webhook 結構描述等。若要進一步了解，請參閱[常見的警示結構描述](../azure-monitor/platform/alerts-common-schema.md)

當警示呼叫 Runbook 時，實際的呼叫是對 Webhook 的 HTTP POST 要求。 POST 要求的本文包含 JSON 格式的物件，其中具有與警示相關的實用屬性。 下表列出每種警示類型的承載結構描述連結：

|警示  |說明|承載結構描述  |
|---------|---------|---------|
|[常見的警示](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|一般警示架構今天標準化的使用體驗，在 Azure 中的警示通知。|[常見的警示承載結構描述](../azure-monitor/platform/alerts-common-schema-definitions.md?toc=%2fazure%2fautomation%2ftoc.json#sample-alert-payload)|
|[活動記錄警示](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |當 Azure 活動記錄中的任何新事件符合特定條件時，便傳送通知。 例如，當 **myProductionResourceGroup** 中發生 `Delete VM` 作業時，或出現狀態為 [作用中] 的新「Azure 服務健康狀態」事件時。| [活動記錄警示承載結構描述](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[近乎即時計量警示](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |當一或多個平台層級的計量符合指定的條件時，便以比計量警示快的速度傳送通知。 例如，當在過去 5 分鐘 VM 上 [CPU %] 的值大於 **90**，且 [網路輸入] 的值大於 **500 MB** 時。| [近乎即時計量警示承載結構描述](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

由於每種類型的警示所提供的資料不同，因此每種警示類型的處理方式也不同。 在下一節中，您將了解如何建立 Runbook 來處理不同類型的警示。

## <a name="create-a-runbook-to-handle-alerts"></a>建立 Runbook 來處理警示

若要搭配警示使用「自動化」，您需要具有邏輯的 Runbook，此邏輯會管理傳遞給 Runbook 的警示 JSON 承載。 下列範例 Runbook 必須從 Azure 警示呼叫。

如上一節所述，每種類型的警示都有不同的結構描述。 指令碼會從警示採用 `WebhookData` Runbook 輸入參數中的 Webhook 資料。 接著，指令碼會評估 JSON 承載，以判斷使用的是哪種警示類型。

此範例使用來自 VM 的警示。 它會從承載擷取 VM 資料，然後使用該資訊來停止 VM。 您必須在執行 Runbook 的「自動化」帳戶中設定連線。 使用警示來觸發 Runbook 時，請務必檢查所觸發 Runbook 中的警示狀態。 每一次警示變更狀態時，Runbook 都會觸發。 警示有多個狀態，最常見的兩個狀態為 `Activated` 和 `Resolved`。 在 Runbook 邏輯中檢查這個狀態，以確保 Runbook 不會執行多次。 本文中的範例只會示範如何尋找 `Activated` 警示。

Runbook 會使用**AzureRunAsConnection** [執行身分帳戶](automation-create-runas-account.md)向 Azure 進行驗證，以針對 VM 執行管理動作。

請使用此範例來建立名為 **Stop-AzureVmInResponsetoVMAlert** 的 Runbook。 您可以修改 PowerShell 指令碼，然後將它與許多不同的資源搭配使用。

1. 移至您的「Azure 自動化」帳戶。
2. 底下**程序自動化**，選取**Runbook**。
3. 在 runbook 清單頂端，選取 **+ 建立 runbook**。
4. 在 **新增 Runbook**頁面上，輸入**Stop-azurevminresponsetovmalert**針對 runbook 名稱。 針對 Runbook 類型，選取 [PowerShell]。 然後，選取 [Create] \(建立\)。  
5. 複製到下列 PowerShell 範例**編輯**頁面。

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. 選取 [發佈] 來儲存並發佈 Runbook。

## <a name="create-the-alert"></a>建立警示

警示使用動作群組，是由警示所觸發的動作的集合。 Runbook 只是可與動作群組搭配使用的眾多動作之一。

1. 在您的自動化帳戶中選取**警示**下方**監視**。
1. 選取  **+ 新增警示規則**。
1. 按一下 **選取 **下方**資源**。 在 **選取資源**頁面上，選取您的 VM 關閉的警示，然後按一下**完成**。
1. 按一下 **新增條件**下方**條件**。 選取您想要使用，例如的訊號**CPU 百分比**然後按一下**完成**。
1. 在上**設定訊號邏輯**頁面上，輸入您**臨界值**下**警示邏輯**，然後按一下**完成**。
1. 在 [動作群組] 底下，選取 [新建]。
1. 在 **新增動作群組**頁面上，提供您的動作群組的名稱和簡短名稱。
1. 指定動作的名稱。 動作類型中，選取**自動化 Runbook**。
1. 選取 **編輯詳細資料**。 在 [設定 Runbook] 頁面上的 [Runbook 來源] 底下，選取 [使用者]。  
1. 選取您的 [訂用帳戶] 和 [自動化帳戶]，然後選取 [Stop-AzureVmInResponsetoVMAlert] Runbook。  
1. 選取  **是** for**啟用常見的警示結構描述**。
1. 若要建立動作群組，請選取 [確定]。

    ![新增動作群組分頁](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    您可以在所建立的[活動記錄警示](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)和[近乎即時警示](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json)中使用此動作群組。

1. 底下**警示詳細資料**，新增警示規則名稱和描述，然後按一下**建立警示規則**。

## <a name="next-steps"></a>後續步驟

* 如需有關使用 Webhook 來啟動「自動化」Runbook 的詳細資訊，請參閱[從 Webhook 啟動 Runbook](automation-webhooks.md)。
* 如需以不同方式啟動 Runbook 的詳細資訊，請參閱 [啟動 Runbook](automation-starting-a-runbook.md)。
* 若要了解如何建立活動記錄警示，請參閱[建立活動記錄警示](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)。
* 若要了解如何建立近乎即時計量警示，請參閱[在 Azure 入口網站中建立警示規則](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json)。
