---
title: 適用於 Azure 備份的 Azure Resource Manager 範本
description: Azure 備份 PowerShell 範例
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c0b023b6bdd01bc64eb5a6deb5fafa8e3c34c616
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819015"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>適用於 Azure 備份的 Azure Resource Manager 範本

下表包含 Azure Resource Manager 範本的連結，這些範本適用於復原服務保存庫和 Azure 備份功能。 若要深入了解 JSON 語法和屬性，請參閱 [Microsoft.RecoveryServices 資源類型](/azure/templates/microsoft.recoveryservices/allversions)。

|   |   |
|---|---|
|**復原服務保存庫** | |
| [建立復原服務保存庫](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| 建立復原服務保存庫。 保存庫可以使用於 Azure 備份和 Azure Site Recovery。 |
|**備份虛擬機器**| |
| [備份 Resource Manager VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | 使用現有復原服務保存庫與備份原則，備份相同資源群組中的 Resource Manager 虛擬機器。|
| [將 IaaS VM 備份到復原服務保存庫](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | 用以備份傳統和 Resource Manager 虛擬機器的範本。 |
| [建立 IaaS VM 的每週備份原則](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | 範本會建立復原服務保存庫，以及用來備份傳統和 Resource Manager 虛擬機器的每週備份原則。|
| [建立 IaaS VM 的每日備份原則](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | 範本會建立復原服務保存庫，以及用來備份傳統和 Resource Manager 虛擬機器的每日備份原則。|
| [部署已啟用備份的 Windows Server VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | 範本可建立已啟用預設備份原則的 Windows Server VM 和復原服務保存庫。|
|**監視備份作業** |  |
| [使用 Azure 監視器記錄搭配 Azure 備份](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | 範本可部署適用於 Azure 備份的 Azure 監視器記錄，讓您監視備份和還原作業、備份警示，以及您復原服務保存庫中使用的雲端儲存體。|  
|   |   |

