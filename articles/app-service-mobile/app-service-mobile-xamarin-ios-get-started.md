---
title: 在 Xamarin.iOS 應用程式中開始使用 Azure App Service Mobile Apps | Microsoft Docs
description: 遵循本教學課程，開始使用 Mobile Apps 進行 Xamarin.iOS 開發。
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 03fb286bd24bb12f3a1e508627a2de156e185568
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097452"
---
# <a name="create-a-xamarinios-app"></a>建立 Xamarin.iOS 應用程式
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概觀
本教程说明如何使用 Azure 移动应用后端向 Xamarin.iOS 移动应用添加基于云的后端服务。  您會建立新的行動應用程式後端，以及可在 Azure 中儲存應用程式資料的簡易「待辦事項清單」 Xaamrin.iOS 應用程式。

您必須先完成此教學課程，才能進行所有其他在 Azure App Service 中使用 Mobile Apps 的 Xamarin.iOS 相關教學課程。

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您需要下列必要條件：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，請註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* Visual Studio 和 Xamarin。 有关说明，请参阅 [设置和安装 Visual Studio 和 Xamarin](/visualstudio/cross-platform/setup-and-install) 。
* 已安裝 Xcode v7.0 或更新版本以及 Xamarin Studio Community 的 Mac。 請參閱[設定和安裝 Visual Studio 和 Xamarin](/visualstudio/cross-platform/setup-and-install)及[針對 Mac 使用者的設定、安裝和驗證](/visualstudio/cross-platform/setup-install-and-verifications-for-mac-users) (MSDN)。

## <a name="create-an-azure-mobile-app-backend"></a>建立 Azure 行動應用程式後端
依照下列步驟建立行動應用程式後端。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>配置服务器项目
您現在已佈建 Azure 行動應用程式後端，可供您的行動用戶端應用程式使用。 接下來，下載簡易「待辦事項清單」後端的伺服器專案，然後將專案發佈至 Azure。

請遵循下列步驟，將伺服器專案設定為使用 Node.js 或 .NET 後端。

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>下载并运行 Xamarin.iOS 应用
1. 在瀏覽器視窗中開啟 [Azure 门户] 。
2. 在行動應用程式的設定刀鋒視窗上，按一下 [開始使用]  >  [Xamarin.iOS]。 在步驟 3 中，按一下 [建立新的應用程式]  \(如果尚未選取的話)。  接著按一下 [下載]  按鈕。

      已下載連接到您的行動後端的用戶端應用程式。 將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。
3. 將您下載的專案解壓縮，並在 Xamarin Studio (或 Visual Studio) 中開啟。

    ![][9]

    ![][8]
4. 按 F5 鍵，以建置專案並在 iPhone 模擬器中啟動 app。
5. 在應用程式中，輸入有意義的文字 (例如「了解 Xamarin」)，然後按一下 **+** 按鈕。

    ![][10]

    要求中的資料會插入 TodoItem 資料表中。 行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

> [!NOTE]
> 您可以在 QSTodoService.cs C# 檔案中檢閱用來存取行動應用程式後端以查詢和插入資料的程式碼。
>
>

## <a name="next-steps"></a>後續步驟
* [將離線同步處理新增至您的應用程式](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [將驗證新增至應用程式中](app-service-mobile-xamarin-ios-get-started-users.md)
* [將推播通知新增至您的 Xamarin.Android 應用程式](app-service-mobile-xamarin-ios-get-started-push.md)
* [如何針對 Azure Mobile Apps 使用受控用戶端](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure 门户]: https://portal.azure.com/
