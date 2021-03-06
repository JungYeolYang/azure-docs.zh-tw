---
title: 使用 Azure 入口網站管理 Azure 資源 |Microsoft Docs
description: 使用 Azure 门户和 Azure 资源管理器管理资源。
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 20bf38b87ce29f8506a5611ecd25cf38f6d4ed61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550640"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>使用 Azure 门户管理 Azure 资源

了解如何将 [Azure 门户](https://portal.azure.com)与 [Azure 资源管理器](resource-group-overview.md)配合使用来管理 Azure 资源。 若要管理资源组，请参阅[使用 Azure 门户管理 Azure 资源组](./manage-resource-groups-portal.md)。

有关资源管理的其他文章：

- [使用 Azure CLI 管理 Azure 资源](./manage-resources-cli.md)
- [使用 Azure PowerShell 管理 Azure 资源](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>将资源部署到资源组

创建资源管理器模板后，可以使用 Azure 门户部署 Azure 资源。 有关如何创建模板，请参阅[快速入门：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](./resource-manager-quickstart-create-templates-use-the-portal.md)。 有关如何使用门户部署模板，请参阅[使用资源管理器模板和 Azure 门户部署资源](resource-group-template-deploy-portal.md)。

## <a name="open-resources"></a>打开资源

Azure 资源按 Azure 服务和资源组进行组织。 以下过程说明如何打开名为 **mystorage0207** 的存储帐户。 虚拟机驻留在名为 **mystorage0207rg** 的资源组中。

按服务类型打开资源：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左窗格中选择 Azure 服务。 在本例中，请选择“存储帐户”。  如果该服务未列出，请选择“所有服务”，然后选择服务类型。

    ![在门户中打开 Azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. 选择要打开的资源。

    ![在门户中打开 Azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    存储帐户类似于：

    ![在门户中打开 Azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

按资源组打开资源：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左窗格中，选择“资源组”列出该组中的资源。
3. 选择要打开的资源。 

## <a name="manage-resources"></a>管理資源

在入口網站中檢視資源時，您會看到用於管理特定資源的選項。

![管理 Azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

屏幕截图显示了 Azure 虚拟机的管理选项。 可以执行启动、重启和停止虚拟机等操作。

## <a name="delete-resources"></a>刪除資源

1. 在门户中打开资源。 有关步骤，请参阅[打开资源](#open-resources)。
2. 選取 [刪除] 。 以下屏幕截图显示了虚拟机的管理选项。

    ![删除 Azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. 键入资源名称以确认删除，然后选择“删除”。

若要详细了解 Azure 资源管理器如何控制资源的删除，请参阅 [Azure 资源管理器资源组的删除](./resource-group-delete.md)。

## <a name="move-resources"></a>移動資源

1. 在门户中打开资源。 有关步骤，请参阅[打开资源](#open-resources)。
2. 選取 [移動]。 以下屏幕截图显示了存储帐户的管理选项。

    ![移动 Azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. 根据需要选择“移到另一资源组”或“移到另一订阅”。

如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。

## <a name="lock-resources"></a>鎖定資源

锁定可以防止组织中的其他用户意外删除或修改关键资源，例如 Azure 订阅、资源组或资源。 

1. 在门户中打开资源。 有关步骤，请参阅[打开资源](#open-resources)。
2. 选择“锁定”。 以下屏幕截图显示了存储帐户的管理选项。

    ![锁定 Azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. 选择“添加”，然后指定锁定属性。

如需詳細資訊，請參閱[使用 Azure Resource Manager 來鎖定資源](resource-group-lock-resources.md)。

## <a name="tag-resources"></a>標記資源

标记有助于按逻辑方式组织资源组和资源。 

1. 在门户中打开资源。 有关步骤，请参阅[打开资源](#open-resources)。
2. 选择“标记”。 以下屏幕截图显示了存储帐户的管理选项。

    ![标记 Azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. 指定标记属性，然后选择“保存”。

有关信息，请参阅[使用标记组织 Azure 资源](./resource-group-using-tags.md#portal)。

## <a name="monitor-resources"></a>監視資源

打开某个资源时，门户会显示用于监视该资源类型的默认图形和表。 以下屏幕截图显示了虚拟机的图形：

![监视 Azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

可以选择图形右上角的图钉图标，将图形固定到仪表板。 若要了解如何使用儀表板，請參閱 [Creating and sharing dashboards in the Azure portal (在 Azure 入口網站建立和共用儀表板)](../azure-portal/azure-portal-dashboards.md)。

## <a name="manage-access-to-resources"></a>管理对资源的访问

[角色型存取控制 (RBAC)](../role-based-access-control/overview.md) 是您對 Azure 中的資源存取進行管理的機制。 如需詳細資料，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟

- 若要了解 Azure 资源管理器，请参阅 [Azure 资源管理器概述](./resource-group-overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](./resource-group-authoring-templates.md)。
- 若要了解如何开发模板，请参阅[分步教程](/azure/azure-resource-manager/)。
- 若要查看 Azure 资源管理器模板架构，请参阅[模板参考](/azure/templates/)。