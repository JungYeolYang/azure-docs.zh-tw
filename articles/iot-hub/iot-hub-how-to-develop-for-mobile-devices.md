---
title: 使用 Azure IoT SDK 開發行動裝置 | Microsoft Docs
description: 開發人員指南 - 了解如何使用 Azure IoT 中樞 SDK 開發行動裝置。
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 5256a58a2b68584888abcac915392d8e389e9772
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399363"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>使用 Azure IoT SDK 開發行動裝置

物聯網中的物是指各式各樣功能不同的裝置，例如感應器、微控制器、智慧型裝置、工業閘道，甚至是行動裝置。  行動裝置可以是 IoT 裝置，不斷從傳送裝置遙測資料到雲端，並且受到雲端管理；  也可以是另一種裝置，負責執行管理其他 IoT 裝置的後端服務應用程式。  在這兩種情況下，[Azure IoT 中樞 SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) 皆可用來開發適用於行動裝置的應用程式。  

## <a name="develop-for-native-ios-platform"></a>開發原生 iOS 平台

Azure IoT 中樞 SDK 透過 Azure IoT 中樞 C SDK 提供原生 iOS 平台支援。  您可以將其視為可以併入 Swift 或 Objective C XCode 專案中的 iOS SDK。  在 iOS 上使用 C SDK 的方法有兩種：

* 直接在 XCode 專案中使用 CocoaPod 程式庫。  
* 下載 C SDK 的原始程式碼，並依照 MacOS 的[組建指示](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)組建 iOS 平台。  

Azure IoT 中樞 C SDK 是以 C99 撰寫，因此擁有極大的可攜性，適用於各種平台。  移轉程序包含為平台特定元件撰寫精簡採用層，您可以在這裡找到 [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx) 適用的程序。  C SDK 中的功能可以運用在 iOS 平台上，包括 Azure IoT 中樞基本支援的功能及 SDK 特定功能，例如網路可靠性的重試原則。  適用於 iOS SDK 的介面也類似於 Azure IoT 中樞 C SDK 的介面。  

這些文件逐步解說如何在 iOS 裝置上開發裝置應用程式或服務應用程式：

* [快速入門：將遙測資料從裝置傳送到 IoT 中樞](quickstart-send-telemetry-ios.md)  
* [使用 IoT 中樞將訊息從雲端傳送至裝置](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>使用 Azure IoT 中樞 CocoaPod 程式庫開發

Azure IoT 中樞 SDK 會發出一組適用於 iOS 開發的 Objective-C CocoaPod 程式庫。  若要查看最新的 CocoaPod 程式庫清單，請參閱 [Microsoft Azure IoT 的 CocoaPods](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md)。  一旦相關程式庫併入到您的 XCode 專案後，有兩種方式可撰寫 IoT 中樞相關程式碼：

* Objective C 函式：如果您的專案是以 Objective-C 撰寫，您可以直接從 Azure IoT 中樞 C SDK 呼叫 API。  如果您的專案是以 Swift 撰寫，您可以先呼叫 `@objc func`，再建立函式，然後繼續使用 C 或 Objective-C 程式碼撰寫與 Azure IoT 中樞相關的所有邏輯。  在[範例存放庫](https://github.com/Azure-Samples/azure-iot-samples-ios)中可找到一組示範這兩種程式碼的範例。  

* 併入 C 範例：如果您已撰寫 C 裝置應用程式，您可以直接在 XCode 專案中參照該應用程式：
    * 從 XCode 將 sample.c 檔案新增至您的 XCode 專案。  
    * 將標頭檔新增至您的相依性。  [範例存放庫](https://github.com/Azure-Samples/azure-iot-samples-ios)中包含一個標頭檔範例。 如需詳細資訊，請造訪 Apple 文件頁面中有關 [Objective-C](https://developer.apple.com/documentation/objectivec) 的說明。

## <a name="develop-for-android-platform"></a>針對 Android 平台開發
Azure IoT 中樞 Java SDK 支援 Android 平台。  如需特定已測試 API 版本，請瀏覽我們的[平台支援頁面](iot-hub-device-sdk-platform-support.md)，以取得最新的更新。

這些文件逐步解說如何使用 Gradle 和 Android Studio 開發 Android 裝置上的裝置應用程式或服務應用程式：

* [快速入門：將遙測資料從裝置傳送到 IoT 中樞](quickstart-send-telemetry-android.md)  
* [快速入門：控制裝置](quickstart-control-device-android.md) 

## <a name="next-steps"></a>後續步驟

* [IoT 中樞 REST API 參考](https://docs.microsoft.com/rest/api/iothub/)
* [Azure IoT C SDK 原始程式碼](https://github.com/Azure/azure-iot-sdk-c)
