---
title: 使用 .NET 預覽 SDK 透過 Azure Cosmos DB 開發 ASP.NET MVC Web 應用程式的教學課程。
description: 本教學課程說明如何使用 Azure Cosmos DB 來建立 ASP .NET MVC Web 應用程式。 您將會儲存並存取來自待辦事項應用程式 (裝載在 Azure 上) 的 JSON 資料。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: dech
ms.openlocfilehash: bf1da7e8a1041b15076ebda6eeac9b0a75c567c0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57857159"
---
# <a name="tutorial-develop-an-aspnet-mvc-web-application-with-azure-cosmos-db-by-using-net-preview-sdk"></a>教學課程：使用 .NET 預覽 SDK，透過 Azure Cosmos DB 開發 ASP.NET MVC Web 應用程式 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [.NET 預覽](sql-api-dotnet-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


本教學課程說明如何使用 Azure Cosmos DB，以儲存和存取來自 ASP.NET MVC 應用程式 (裝載在 Azure 上) 的資料。 在本教學課程中，您會使用 .NET SDK V3 (目前為預覽版)。 下圖顯示您會藉由使用本文範例來建置的網頁：
 
![本教學課程所建立待辦事項清單 MVC Web 應用程式的螢幕擷取畫面 - ASP NET MVC 教學課程逐步解說](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-image01.png)

如果您沒有時間完成本教學課程，則可以從 [GitHub][GitHub] 下載完整的範例專案。 

本教學課程涵蓋下列項目：

> [!div class="checklist"]
> * 建立 Azure Cosmos 帳戶
> * 建立 ASP.NET MVC 應用程式
> * 將應用程式連線到 Azure Cosmos DB 
> * 對資料執行 CRUD 作業

> [!TIP]
> 本教學課程假設您先前有過使用 ASP.NET MVC 和 Azure 網站的經驗。 如果您不熟悉 ASP.NET 或[必備工具](#prerequisites)，建議您從 [GitHub][GitHub] 下載完整的範例專案、新增必要的 NuGet 套件，並加以執行。 專案建置完成後，您可以檢閱本文，以加深對專案內容中程式碼的了解。

## <a name="prerequisites"></a>必要條件 

依照本文的指示進行之前，請確定您具備下列資源：

* **使用中的 Azure 帳戶：** 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

* Microsoft Azure SDK for .NET for Visual Studio 2017 (可透過 Visual Studio 安裝程式來取得)。

本文中的所有螢幕擷取畫面都是使用 Microsoft Visual Studio Community 2017 所擷取的。 如果系統是設定使用不同的版本，則您的畫面和選項可能不會完全相符，但只要您符合上述必要條件，本方案應該還是有效。

## <a name="create-an-azure-cosmos-account"></a>步驟 1：建立 Azure Cosmos 帳戶

我們將從建立 Azure Cosmos 帳戶開始著手。 如果您已經擁有 Azure Cosmos DB SQL API 帳戶，或您要使用 Azure Cosmos DB 模擬器來進行本教學課程，則可以跳到[建立新的 ASP.NET MVC 應用程式](#create-a-new-mvc-application)一節。

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

在下一節中，您會建立新的 ASP.NET MVC 應用程式。 

## <a name="create-a-new-mvc-application"></a>步驟 2：建立新的 ASP.NET MVC 應用程式

1. 在 Visual Studio 中，從 [檔案] 功能表選擇 [新增]，然後選取 [專案]。 [新增專案]  對話方塊隨即出現。

2. 在 [新增專案] 視窗中，依序展開 [已安裝] 範本、[Visual C#] 和 [Web]，然後選取 [ASP.NET Web 應用程式]。 

   ![建立新的 ASP.NET Web 應用程式專案](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-new-project-dialog.png)

3. 在 [名稱]  方塊中，輸入專案的名稱。 本教學課程使用 "todo" 名稱。 如果您選擇使用其他名稱，則每當本教學課程提及 todo 命名空間時，請調整所提供的程式碼範例，以便使用您為應用程式所命名的名稱。 

4. 選取 [瀏覽] 以瀏覽至您要在其中建立專案的資料夾，然後選擇 [.NET Framework 4.6.1] 或更新版本。 選取 [確定] 。 

5. [新增 ASP.NET Web 應用程式] 對話方塊隨即出現。 在 [範本] 窗格中，選取 [MVC] 。

6. 選取 [確定]，並讓 Visual Studio 圍繞空白的 ASP.NET MVC 範本執行 Scaffolding。 

7. Visual Studio 建立好未定案 MVC 應用程式之後，您便擁有可以在本機執行的空白 ASP.NET 應用程式。

## <a name="add-nuget-packages"></a>步驟 3：對專案新增 Azure Cosmos DB NuGet 套件

現在我們已經擁有此解決方案所需的大部分 ASP.NET MVC 架構程式碼，接下來讓我們新增所需的 NuGet 套件以便連線到 Azure Cosmos DB。

1. Azure Cosmos DB .NET SDK 會進行封裝，並以 NuGet 封裝形式加以散發。 若要在 Visual Studio 中取得 NuGet 封裝，請使用 Visual Studio 中的 NuGet 封裝管理員，方法是以滑鼠右鍵按一下 [方案總管] 中的專案，然後選取 [管理 NuGet 封裝]。
   
   ![[方案總管] 中 Web 應用程式專案滑鼠右鍵選項的螢幕擷取畫面，其中已醒目提示 [管理 NuGet 套件]。](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-manage-nuget.png)
   
2. [ **管理 NuGet 封裝** ] 對話方塊隨即出現。 在 NuGet [瀏覽] 方塊中，輸入「Microsoft.Azure.Cosmos」。 從結果中，安裝 **Microsoft.Azure.Cosmos** 3.0.0.1-preview 版本。 這會下載和安裝 Azure Cosmos DB 套件及其相依性，例如 Newtonsoft.Json。 選取 [預覽] 視窗中的 [確定]，以及 [接受授權] 視窗中的 [我接受] 來完成安裝。
   
   或者，您也可以使用 Package Manager Console 來安裝 NuGet 套件。 若要這樣做，請在 [工具] 功能表中，依序選取 [NuGet 套件管理員] 及 [套件管理器主控台]。 在出現提示時輸入下列命令：
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos -Version 3.0.0.1-preview
   ```        

3. 套件安裝好之後，Visual Studio 解決方案應該會包含兩個新的程式庫參考，參考對象分別為 Microsoft.Azure.Cosmos.Client 和 Newtonsoft.Json。
  
## <a name="set-up-the-mvc-application"></a>步驟 4：設定 ASP.NET MVC 應用程式

現在我們可以開始將模型、檢視和控制站新增至此 MVC 應用程式：

* [新增模型](#add-a-model)。
* [新增控制器](#add-a-controller)。
* [新增檢視](#add-views)。

### <a name="add-a-model"></a>新增模型

1. 從 [方案總管] 中，以滑鼠右鍵按一下 **Models** 資料夾，選取 [新增]，然後選取 [類別]。 [加入新項目]  對話方塊隨即出現。

1. 將新的類別命名為 **TodoItem.cs**，然後選取 [新增]。 

1. 接下來，以下列程式碼取代 "Todoitem" 類別中的程式碼：

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Models/TodoItem.cs)]
   
   Azure Cosmos DB 中所儲存的資料都會透過線路傳遞，並儲存為 JSON。 如需透過 JSON.NET 控管物件序列化/取消序列化，您可以使用剛才所建立 **TodoItem** 類別中示範的 **JsonProperty** 屬性。 使用 JSON 時，您不但可以控管屬性名稱格式，也可以跟命名 **Description** 屬性時一樣地重新命名您的 .NET 屬性。 

### <a name="add-a-controller"></a>新增控制器

1. 從 [方案總管] 中，以滑鼠右鍵按一下 **Controllers** 資料夾，選取 [新增]，然後選取 [控制器]。 [ **新增 Scaffold** ] 對話方塊隨即出現。

1. 選取 [Web API 5 控制器 - 空白]，然後選取 [新增]。

   ![[新增 Scaffold] 對話方塊的螢幕擷取畫面，其中已醒目提示 [MVC 5 控制器 - 空白] 選項](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. 將您的新控制器命名為 **ItemController，然後以下列程式碼取代該檔案中的程式碼：

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Controllers/ItemController.cs)]

   此處所使用的 **ValidateAntiForgeryToken** 屬性可協助應用程式防止跨網站偽造要求攻擊。 這不光只是新增此屬性，您的檢視也應該使用這個防偽權杖。 如需此主題的詳細資訊以及如何正確實作此作業的範例，請參閱[防止跨網站偽造要求][Preventing Cross-Site Request Forgery]。 [GitHub][GitHub] 上提供的原始程式碼已有完整實作。
   
   我們也會在方法參數上使用 **Bind** 屬性，以協助防範 over-posting 攻擊。 如需詳細資訊，請參閱 [ASP.NET MVC 中的基本 CRUD 作業][Basic CRUD Operations in ASP.NET MVC]。
    
### <a name="add-views"></a>新增檢視

接下來，讓我們建立下列三個檢視： 

* [新增清單項目檢視](#AddItemIndexView)。
* [新增新項目檢視](#AddNewIndexView)。
* [新增編輯項目檢視](#AddEditIndexView)。

#### <a name="AddItemIndexView"></a>新增清單項目檢視

1. 在 [方案總管] 中，展開 **Views** 資料夾，以滑鼠右鍵按一下稍早您在建立 **ItemController** 時，Visual Studio 為您建立的空白 **Item** 資料夾，按一下 [新增]，然後按一下 [檢視]。
   
   ![顯示 Visual Studio 建立之 [Item] 資料夾的 [方案總管] 螢幕擷取畫面，其中已醒目提示 [新增檢視] 命令](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view.png)

2. 在 [新增檢視] 對話方塊中，更新下列值：
   
   * 在 [檢視名稱] 方塊中，輸入***索引***。
   * 在 [範本] 方塊中，選取 [清單]。
   * 在 [模型類別] 方塊中，選取 [項目 (todo.Models)]。
   * 在 [版面配置頁面] 方塊中，輸入 ***~/Views/Shared/_Layout.cshtml***。
     
   ![顯示 [新增檢視] 對話方塊的螢幕擷取畫面](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view-dialog.png)

3. 新增這些值之後，請選取 [新增]，並讓 Visual Studio 建立新的範本檢視。 完成後，它會開啟所建立的 cshtml 檔案。 您稍後會回頭使用此檔案，因此可以先在 Visual Studio 中將檔案關閉。

#### <a name="AddNewIndexView"></a>新增新項目檢視

與您建立檢視以列出項目的方法類似，請使用下列步驟建立新的檢視，來建立項目：

1. 從 [方案總管] 中，再次以滑鼠右鍵按一下 **Item** 資料夾，選取 [新增]，然後選取 [檢視]。

1. 在 [新增檢視] 對話方塊中，更新下列值：
   
   * 在 [檢視名稱] 方塊中，輸入***建立***。
   * 在 [範本] 方塊中，選取 [建立]。
   * 在 [模型類別] 方塊中，選取 [項目 (todo.Models)]。
   * 在 [版面配置頁面] 方塊中，輸入 ***~/Views/Shared/_Layout.cshtml***。
   * 選取 [新增] 。
   
#### <a name="AddEditIndexView"></a>新增編輯項目檢視

最後，請新增檢視以透過下列步驟編輯項目：

1. 從 [方案總管] 中，再次以滑鼠右鍵按一下 **Item** 資料夾，選取 [新增]，然後選取 [檢視]。

1. 在 [新增檢視]  對話方塊中，執行下列動作：
   
   * 在 [檢視名稱] 方塊中，輸入***編輯***。
   * 在 [範本] 方塊中，選取 [編輯]。
   * 在 [模型類別] 方塊中，選取 [項目 (todo.Models)]。
   * 在 [版面配置頁面] 方塊中，輸入 ***~/Views/Shared/_Layout.cshtml***。
   * 選取 [新增] 。

完成這項作業之後，請將 Visual Studio 中的所有 cshtml 文件關閉，您稍後會回頭使用這些檢視。

## <a name="connect-to-cosmosdb"></a>步驟 5：連線至 Azure Cosmos DB 

我們已經建立了標準的 MVC 項目，現在可以開始新增程式碼以便連線至 Azure Cosmos DB 並執行 CRUD 作業。 

### <a name="perform-crud-operations"></a> 對資料執行 CRUD 作業

首先要執行的作業是新增類別，其中包含連線至及使用 Azure Cosmos DB 的邏輯。 在本教學課程中，我們會將此邏輯封裝到名為 TodoItemService.cs 的類別中。 此程式碼會從組態檔讀取 Azure Cosmos DB 端點值，並執行 CRUD 作業，例如，列出未完成的項目、建立、編輯和刪除項目。 

1. 從 [方案總管]，在名為 **Services** 的專案底下建立新的資料夾。

1. 以滑鼠右鍵按一下 **Services** 資料夾，選取 [新增]，然後選取 [類別]。 將新的類別命名為 **TodoItemService**，然後選取 [新增]。

1. 將下列程式碼新增至 **TodoItemService** 類別，並以下列程式碼取代該檔案中的程式碼：

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Services/TodoItemService.cs)]
 
1. 先前的程式碼會從組態檔讀取連接字串值。 若要更新 Azure Cosmos 帳戶的連接字串值，請開啟專案中的 **Web.config** 檔案，然後在 `<AppSettings>` 區段下新增下列幾行：

   ```csharp
    <add key="endpoint" value="<enter the URI from the Keys blade of the Azure Portal>" />
    <add key="primaryKey" value="<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>" />
    <add key="database" value="Tasks" />
    <add key="container" value="Items" />
   ```
 
1. 使用從 Azure 入口網站的 [金鑰] 刀鋒視窗擷取的值，來更新端點和 primarykey 的值。 使用 [金鑰] 刀鋒視窗的 [URI] 作為端點設定的值，並使用 [金鑰] 刀鋒視窗的 [主要金鑰] 或 [次要金鑰] 作為金鑰設定。 這會負責在應用程式中裝設 Azure Cosmos DB，現在讓我們新增應用程式邏輯。

1. 開啟 **Global.asax.cs** 並將下列一行新增至 **Application_Start** 方法 
   
   ```csharp
   TodoItemService.Initialize().GetAwaiter().GetResult();
   ```

   此時，您的解決方案應該已經可以建置專案，而不會發生任何錯誤。 如果您現在執行應用程式，**HomeController** 及該控制器的 [索引] 檢視應該就會開啟。 這是我們在一開始時所選擇的 MVC 範本專案預設行為。 讓我們變更此 MVC 應用程式上的路由以改變此行為。

1. 開啟 ***App\_Start\RouteConfig.cs***，並尋找以 "defaults:" 開頭的程式碼行，然後將該行更新為下列程式碼：

   ```csharp
   defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }
   ```

   如果您未在 URL 中指定控制路由行為的值，此程式碼會讓 ASP.NET MVC 知道改用 **Item** (**Home**) 作為控制器，並使用**索引**作為檢視。

現在如果您執行應用程式，它將會呼叫 **ItemController**，後者再從您在下一節中定義的 TodoItemService 類別呼叫 GetItems 方法。 

如果建置並立即執行此專案，您現在應該會看到如下的內容。    

![本資料庫教學課程所建立待辦事項清單 Web 應用程式的螢幕擷取畫面](./media/sql-api-dotnet-application-preview/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>步驟 6：在本機執行應用程式

若要在本機電腦測試應用程式，請使用下列步驟：

1. 在 Visual Studio 中按 F5，即可在偵錯模式下建置應用程式。 這樣應該可以建置應用程式，並啟動含有先前所看過之空白方格頁面的瀏覽器：
   
   ![本資料庫教學課程所建立待辦事項清單 Web 應用程式的螢幕擷取畫面](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. 按一下 [新建] 連結，並在 [名稱] 和 [描述] 欄位中新增值。 將 [已完成] 核取方塊保持為未選取狀態，否則，新項目會以已完成的狀態新增，但不會出現在初始清單中。
   
3. 按一下 [建立]，您便會被重新導向回到 [索引] 檢視，且您的項目會出現在清單中。 您可以再將一些項目新增至待辦事項清單。

    ![[索引] 檢視的螢幕擷取畫面](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item.png)
  
4. 按一下清單上 [項目] 旁邊的 [編輯]，您便會被帶到 [編輯] 檢視，您可以在此更新物件的任何屬性 (包括 [已完成] 旗標)。 如果您標示 [完成] 旗標，然後按一下 [儲存]，則**項目**會從未完成的工作清單中移除。
   
   ![[索引] 檢視的螢幕擷取畫面，其中已勾選 [已完成] 方塊](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-completed-item.png)

5. 完成測試應用程式後，按 Ctrl + F5 停止偵錯應用程式。 您現在可以開始進行部署。

## <a name="deploy-the-application-to-azure"></a>步驟 7：部署應用程式 
您已經擁有可在 Azure Cosmos DB 正常運作的完整應用程式，我們現在要將此 Web 應用程式部署至 Azure App Service。  

1. 若要發佈此應用程式，請以滑鼠右鍵按一下 [方案總管] 中的專案，然後選取 [發佈]。
   
2. 在 [發佈] 對話方塊中，選取 [Microsoft Azure App Service]，然後選取 [建立新的] 來建立 App Service 設定檔，或選擇 [選取現有的] 來使用現有設定檔。

3. 如果您有現有的 Azure App Service 設定檔，請從下拉式清單選取 [訂用帳戶]。 使用 [檢視] 篩選器來依資源群組或資源類型排序。 接著搜尋所需的 Azure App Service，然後選取 [確定]。 
   
   ![Visual Studio 中的 [App Service] 對話方塊](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-app-service.png)

4. 若要建立新的 Azure App Service 設定檔，請按一下 [發佈] 對話方塊中的 [建立新的]。 在 [建立 App Service] 對話方塊中，輸入您的 Web 應用程式名稱和適當的訂用帳戶、資源群組和 App Service 方案，然後選取 [建立]。

   ![Visual Studio 中的 [建立 App Service] 對話方塊](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-app-service.png)

幾秒後，Visual Studio 便會發佈 Web 應用程式並啟動瀏覽器，您可以在瀏覽器中看到您的專案已在 Azure 中執行！

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何建置能夠存取 Azure Cosmos DB 中所儲存資料的 ASP.NET MVC Web 應用程式。 您現在可以繼續進行下一篇文章：

> [!div class="nextstepaction"]
> [建置 Java 應用程式以存取 Azure Cosmos DB SQL API 帳戶中所儲存的資料]( sql-api-java-application.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-todo-app
