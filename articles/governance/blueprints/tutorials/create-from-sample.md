---
title: 從藍圖範例建立環境
description: 使用藍圖範例建立藍圖定義，以設定兩個資源群組及其各自的角色指派。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/05/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4f400e45d8defc304cf58c4bd05fa19f16d0501b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785950"
---
# <a name="create-an-environment-from-a-blueprint-sample"></a>從藍圖範例建立環境

藍圖範例會舉例說明可使用 Azure 藍圖來完成的工作。 這些範例各自都有特定目的或用途，但其本身並無法建立完整的環境。 其目的是作為起點，以探索如何使用由所含成品、設計和參數所組合而成的各種 Azure 藍圖。

下列教學課程會使用**具有 RBAC 的資源群組**藍圖範例，來展示藍圖服務的不同層面。 本文涵蓋下列步驟：

> [!div class="checklist"]
> - 從範例建立新的藍圖定義
> - 將您的範例複本標記為**已發佈**
> - 將您的藍圖複本指派給現有的訂用帳戶
> - 檢查為指派部署的資源
> - 取消指派藍圖以移除鎖定

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="create-blueprint-definition-from-sample"></a>從範例建立藍圖定義

首先，請實作藍圖範例。 匯入作業會根據此範例在您的環境中建立新的藍圖。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 在左側的 [快速入門] 頁面上，選取 [建立藍圖] **下方的 [建立]** 按鈕。

1. 在 [其他範例] 下方尋找 [具有 RBAC 的資源群組] 藍圖範例，然後選取 [使用此範例]。

1. 輸入藍圖範例的 [基本資料]：

   - **藍圖名稱**：為您的藍本範例複本提供名稱。 在本教學課程中，我們使用的名稱是「two-rgs-with-role-assignments」。
   - **定義位置**：使用省略符號，然後選取要作為範例複本儲存位置的管理群組或訂用帳戶。

1. 在頁面頂端選取 [成品]索引標籤，或在頁面底部選取 **[下一步：成品]**。

1. 檢閱構成此藍圖範例的成品清單。 這個範例會定義兩個資源群組，其顯示名稱分別為「ProdRG」和「PreProdRG」。 在指派藍圖期間會設定每個資源群組的最終名稱和位置。 「ProdRG」資源群組會獲派「參與者」角色，「PreProdRG」資源群組則會獲派「擁有者」和「讀取者」角色。 定義中所指派的角色是靜態的，但獲派該角色的使用者、應用程式或群組則是在藍圖指派期間進行設定的。

1. 當您檢閱完藍圖範例時，請選取 [儲存草稿]。

此步驟會在選取的管理群組或訂用帳戶中建立藍圖範例定義的複本。 所儲存的藍圖定義若要進行管理，方法和從頭開始建立的藍圖一樣。 您可以視需要將範例儲存至管理群組或訂用帳戶，次數不限。 不過，您必須為每個複本提供唯一的名稱。

出現**成功儲存藍圖定義**的入口網站通知後，請移至下一個步驟。

## <a name="publish-the-sample-copy"></a>發佈範例複本

您的環境中現已建立了藍圖範例複本。 該複本會以**草稿**模式建立，而且必須先**發佈**，才能進行指派和部署。 您可以根據環境和需求來自訂藍圖範例複本。 在本教學課程中，我們不會進行任何變更。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取左側的 [藍圖定義] 頁面。 使用篩選來尋找「two-rgs-with-role-assignments」藍圖定義，然後加以選取。

1. 選取頁面頂端的 [發佈藍圖]。 在右側的新窗格中，提供「1.0」作為藍圖範例複本的 [版本]。 如果您稍後會進行修改，此屬性十分實用。 提供 [變更附註]，例如「First version published from the resource groups with RBAC blueprint sample」。 然後選取頁面底部的 [發佈]。

此步驟可讓您將藍圖指派給訂用帳戶。 發佈之後，仍可進行變更。 進行其他變更時，需要以新的 [版本] 值來發佈，如此才能追蹤相同藍圖定義的各版本差異。

出現**成功發佈藍圖定義**的入口網站通知後，請移至下一個步驟。

## <a name="assign-the-sample-copy"></a>指派範例複本

成功**發佈**藍圖範例複本後，可以將藍圖定義指派給其所在管理群組中的訂用帳戶。 此步驟用於提供參數，以建立每個專屬的藍圖範例複本部署。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取左側的 [藍圖定義] 頁面。 使用篩選來尋找「two-rgs-with-role-assignments」藍圖定義，然後加以選取。

1. 選取藍圖定義頁面頂端的 [指派藍圖]。

1. 提供用於指派藍圖的參數值：

   - 基本概念

     - **訂用帳戶**：在用來儲存藍圖範例複本的管理群組中，選取一或多個訂用帳戶。 如果您選取多個訂用帳戶，輸入的參數就會用來為每個訂用帳戶建立指派。
     - **指派名稱**：名稱會根據藍圖定義名稱預先填入。
     - **位置**：選取要在其中建立受控識別的區域。 Azure 藍圖會使用此受控識別來部署指派的藍圖之中的所有成品。 若要深入了解，請參閱[適用於 Azure 資源的受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)。
       在本教學課程中，我們選取 [美國東部 2]。
     - **藍圖定義版本**：選取 [1.0] 作為藍圖範例定義複本的 [已發佈] 版本。

   - 鎖定指派

     選取 [唯讀] 藍圖鎖定模式。 如需詳細資訊，請參閱[藍圖資源鎖定](../concepts/resource-locking.md)。

   - 受控識別

     保留預設的 [系統指派] 選項。 如需詳細資訊，請參閱[受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)。

   - 構件參數

     本節中定義的參數會套用至其定義所屬的成品。 這些參數是[動態參數](../concepts/parameters.md#dynamic-parameters)，因為定義於藍圖指派期間。 針對每個成品，將其參數值設定為 [值] 資料行中所定義的值。 針對 `{Your ID}`，請選取 Azure 使用者帳戶。

     |成品名稱|成品類型|參數名稱|值|說明|
     |-|-|-|-|-|
     |ProdRG 資源群組|資源群組|Name|ProductionRG|定義第一個資源群組的名稱。|
     |ProdRG 資源群組|資源群組|位置|美國西部 2|設定第一個資源群組的位置。|
     |參與者|角色指派|使用者或群組|{您的識別碼}|定義要對第一個資源群組內的哪些使用者或群組授與「參與者」角色指派。|
     |PreProdRG 資源群組|資源群組|Name|PreProductionRG|定義第二個資源群組的名稱。|
     |PreProdRG 資源群組|資源群組|位置|美國西部|設定第二個資源群組的位置。|
     |擁有者|角色指派|使用者或群組|{您的識別碼}|定義要對第二個資源群組內的哪些使用者或群組授與「擁有者」角色指派。|
     |讀取者|角色指派|使用者或群組|{您的識別碼}|定義要對第二個資源群組內的哪些使用者或群組授與「讀取者」角色指派。|

1. 輸入所有參數後，選取頁面底部的 [指派]。

此步驟會部署已定義的資源，並設定所選的 [鎖定指派]。 套用藍圖鎖定可能需要 30 分鐘的時間。

出現**成功指派藍圖定義**的入口網站通知後，請移至下一個步驟。

## <a name="inspect-resources-deployed-by-the-assignment"></a>檢查指派所部署的資源

藍圖指派會建立藍圖定義中所定義的成品，並加以追蹤。 從 [藍圖指派] 頁面和直接查看資源皆可查看資源的狀態。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取左側的 [指派的藍圖] 頁面。 使用篩選來尋找「Assignment-two-rgs-with-role-assignments」藍圖指派，然後加以選取。

   在這個頁面中，我們可以看到指派已成功建立，並可看到所建立資源的清單及其藍圖鎖定狀態。 如果更新指派，則 [指派作業] 下拉式清單會顯示每個定義版本的部署詳細資料。 所列出的每個已建立資源均可點按，並於點按後開啟該資源屬性頁。

1. 選取 [ProductionRG] 資源群組。

   我們會看到資源群組的名稱是 **ProductionRG**，而不是成品的顯示名稱「ProdRG」。 此名稱會符合藍圖指派期間所設定的值。

1. 選取左側的 [存取控制 (IAM)] 頁面，然後選取 [角色指派] 索引標籤。

   在這裡，我們會看到您的帳戶已獲得「參與者」角色，且範圍為「此資源」。 我們使用「Assignment-two-rgs-with-role-assignments」藍圖指派來建立該資源群組，因此這個藍圖指派具有「擁有者」角色。 這些權限也會用來管理具有所設定藍圖鎖定的資源。

1. 從 Azure 入口網站的階層連結選取 [Assignment-two-rgs-with-role-assignments] 來返回上一頁，然後選取 [PreProductionRG] 資源群組。

1. 選取左側的 [存取控制 (IAM)] 頁面，然後選取 [角色指派] 索引標籤。

   在這裡，我們會看到您的帳戶已同時獲得「擁有者」和「讀取者」角色，且這兩個角色的範圍皆為「此資源」。 和第一個資源群組一樣，此藍圖指派也有「擁有者」角色。

1. 選取 [拒絕指派] 索引標籤。

   藍圖指派已在部署的資源群組上建立[拒絕指派](../../../role-based-access-control/deny-assignments.md)，進而強制執行「唯讀」鎖定模式。 針對在 [角色指派] 索引標籤上具有適當權限的人員，拒絕指派可阻止他們採取特定動作。 拒絕指派會影響「所有主體」。

1. 選取 [拒絕指派]，然後選取左側的 [拒絕的權限] 頁面。

   拒絕指派會阻止所有使用 **\*** 的作業和**動作**設定，但允許透過 **NotActions** 排除 **\*/read** 來允許讀取存取。

1. 從 Azure 入口網站的階層連結中，選取 [PreProductionRG - 存取控制 (IAM)]。 然後選取左側的 [概觀] 頁面，然後選取 [刪除資源群組] 按鈕。 輸入「PreProductionRG」名稱以確認刪除，然後選取窗格底部的 [刪除]。

   **無法刪除 PreProductionRG 資源群組**的入口網站通知會隨即出現。 該錯誤表示，雖然您的帳戶有權刪除資源群組，但藍圖指派拒絕了您的存取。 別忘了，我們已在藍圖指派期間選取「唯讀」藍圖鎖定模式。 藍圖鎖定會阻止具有權限的帳戶 (甚至是「擁有者」權限) 刪除資源。 如需詳細資訊，請參閱[藍圖資源鎖定](../concepts/resource-locking.md)。

這些步驟說明我們的資源已依照定義加以建立，且該藍圖可防止意外的刪除動作，甚至是具有權限的帳戶也無法刪除資源。

## <a name="unassign-the-blueprint"></a>取消指派藍圖

最後一個步驟是要移除藍圖的指派和其所部署的資源。
移除指派並不會移除所部署的成品。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取左側的 [指派的藍圖] 頁面。 使用篩選來尋找「Assignment-two-rgs-with-role-assignments」藍圖指派，然後加以選取。

1. 選取頁面頂端的 [取消指派藍圖] 按鈕。 閱讀確認對話方塊中的警告，然後選取 [確定]。

   移除藍圖指派後，藍圖鎖定也會一併移除。 具有權限的帳戶可再次刪除已建立的資源。

1. 從 Azure 功能表選取 [資源群組]，然後選取 [ProductionRG]。

1. 選取左側的 [存取控制 (IAM)] 頁面，然後選取 [角色指派] 索引標籤。

每個資源群組的安全性仍會具有所部署的角色指派，但藍圖指派則不再具有「擁有者」存取權。

出現**成功移除藍圖指派**的入口網站通知後，請移至下一個步驟。

## <a name="clean-up-resources"></a>清除資源

完成本教學課程後，請刪除下列資源：

- ProductionRG 資源群組
- PreProductionRG 資源群組
- two-rgs-with-role-assignments 藍圖定義

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](../concepts/lifecycle.md) (英文)
- 了解如何使用[靜態和動態參數](../concepts/parameters.md)
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md) (英文)
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md) (英文)
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題