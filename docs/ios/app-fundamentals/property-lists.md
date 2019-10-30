---
title: 使用 Xamarin 中的屬性清單
description: 本檔介紹 Visual Studio for Mac 的圖形化和先進的屬性清單（. plist）編輯器，以使用 plist 和 plist。 其中說明如何設定圖示，並從 Visual Studio for Mac 內啟動 iOS 應用程式的映射。
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 2060e0786b5401b44217318b647dfa7412f934f4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009871"
---
# <a name="working-with-property-lists-in-xamarinios"></a>使用 Xamarin 中的屬性清單

_本檔介紹 Visual Studio for Mac 的圖形化和先進的屬性清單（. plist）編輯器，以使用 plist 和 plist。其中說明如何設定圖示，並從 Visual Studio for Mac 內啟動 iOS 應用程式的映射。_

Visual Studio for Mac 的特色是 plist 編輯器，可讓您更輕鬆地編輯應用程式屬性和功能。 Visual Studio for Mac 有兩個 plists `Info.plist`，可用於編輯應用程式屬性和圖示，以及 `Entitlements.plist` 來管理應用程式功能。 本指南介紹 plists，並概述如何在 Visual Studio for Mac 中使用它們。 如需 plist 的詳細資訊，請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

## <a name="infoplist"></a>Info.plist

資訊屬性清單（`Info.plist`）是必要的 iOS 檔案，可提供您的應用程式設定至系統的相關資訊。 Visual Studio for Mac 的自訂 `Info.plist` 編輯器具有三個面板，由 [編輯器] 視窗左下方的索引標籤所控制：

 [![](property-lists-images/tabs.png "The Info.plist editor tabs at the bottom left of the editor window")](property-lists-images/tabs.png#lightbox)

每個面板都會控制不同的屬性，如下所述：

- **應用程式面板**-設定通用應用程式屬性以及圖示和啟動影像的圖形化介面;指定 maps 整合和背景處理模式。
- [**高級] 面板**-[advanced] 面板是用來指定支援的檔案類型、UTI 和 URL 類型的位置。
- **來源面板**-[來源] 面板會控制較不常用的屬性，以及應用程式的自訂屬性。

接下來的三節會更詳細地探討每個面板的功能。

## <a name="application-panel"></a>應用程式面板

Visual Studio for Mac 具有圖形化介面，可用於編輯應用程式的常見 `Info.plist` 專案：

1. 應用程式屬性
1. 支援的裝置類型
1. 每種裝置類型的支援方向
1. 狀態列樣式和色彩
1. 圖示和啟動畫面
1. 地圖和背景模式

下一節將詳細說明這些功能。

 <a name="iOS_Application_Target" />

### <a name="ios-application-target"></a>iOS 應用程式目標

本節包含描述應用程式的重要資訊。
此處儲存的**識別碼**必須符合在 iTunes Connect （適用于 App Store 應用程式）中輸入的配套識別碼，以及 iOS 布建入口網站應用程式 id 清單和開發和散發憑證。

 [![](property-lists-images/image24.png "iOS Application Target")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>裝置部署

 [![](property-lists-images/deployment.png "Device Deployment")](property-lists-images/deployment.png#lightbox)

[裝置**部署**資訊] 區段會選擇性地顯示，視上述 [**應用程式目標**] 區段的 [**裝置**] 下拉式清單中的選取專案而定。 **主要介面**下拉式會在分鏡腳本驅動的應用程式中設定為**mainstoryboard.storyboard** 。 如果使用者介面是以程式碼完整撰寫，則可以保留空白。

### <a name="supported-device-orientations"></a>支援的裝置方向

 **支援的裝置方向**可控制應用程式回應裝置旋轉的方式。 IPhone/iPad 應用程式**通常只支援**直**向**或所有專案，但卻很有説明。 一般來說，遊戲以外的所有 iPad 應用程式都應該支援所有方向。

### <a name="status-bar-styles"></a>狀態列樣式

[**狀態列樣式**] 區段是用來編輯應用程式 `UIStatusBarStyle`的圖形化介面：

 [![](property-lists-images/status.png "Status Bar Styles")](property-lists-images/status.png#lightbox)

 <a name="Icons" />

### <a name="icons-launch-images-and-itunes-artwork"></a>圖示、啟動影像和 iTunes 插圖

您可以在使用[影像](~/ios/app-fundamentals/images-icons/index.md)指南中找到在 plist 檔案中使用圖示、影像和插圖的資訊。

### <a name="maps-integration-and-background-modes"></a>地圖整合和背景模式

`Info.plist` 包含指定 maps 整合和背景處理模式的特殊區段。 選擇您想要支援的選項，就會為您將必要的屬性新增至您的應用程式。

 [![](property-lists-images/maps.png "Maps Integration")](property-lists-images/maps.png#lightbox)

如需使用地圖的詳細資訊，請參閱 Xamarin [IOS 地圖](~/ios/user-interface/controls/ios-maps/index.md)指南。

 [![](property-lists-images/bging.png "Background Modes")](property-lists-images/bging.png#lightbox)

如需背景模式的詳細資訊，請參閱《 [iOS 中](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)的 Xamarin 背景處理指南》。

## <a name="advanced-panel"></a>[Advanced] 面板

[Advanced] 面板可控制應用程式支援的檔案類型和 URL 配置。

 [![](property-lists-images/image34.png "Advanced Panel")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />

## <a name="document-types"></a>檔案類型

對於支援開啟特定檔案類型的應用程式，iOS 提供 `CFBundleDocumentTypes` 金鑰。 如果我們想要讓應用程式支援某些已知的檔案類型（例如 Pdf），我們會將 PDF 值新增至金鑰。 本節提供一個便利的方式，讓您輸入 `Info.plist` 檔案中將儲存在 `CFBundleDocumentTypes` 機碼中的資料。

如需有關如何設定這些值的詳細資訊，請參閱[註冊應用程式支援的檔案類型](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)的相關檔。

## <a name="utis"></a>Uti

有時候應用程式必須支援開啟自訂檔案類型。 例如，我們可能會想要開啟具有自訂擴充功能的影像檔案 *。* 為了指定自訂檔案類型，我們將使用 `UIExportedTypeDeclarations` 金鑰來建立自訂的 UTI-通用類型識別碼。 下列螢幕擷取畫面說明如何建立適用于. xam 擴充功能的自訂 UTI：

 [![](property-lists-images/uti.png "UTIs Editor")](property-lists-images/uti.png#lightbox)

就像匯出的類型 Uti 指定您的應用程式專屬的自訂 Uti，匯*入的類型 uti* （`UIImportedTypeDeclarations` 金鑰）會指定您的應用程式支援但不會擁有的自訂類型。

如需使用自訂 Uti 的詳細資訊，請參閱 Apple 的[註冊檔案類型您的應用程式支援](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1)指南。

## <a name="custom-urls"></a>自訂 Url

URL 配置名稱（也稱為通訊協定）是 URL 的第一個部分。 例如，`http://` 和 `https://` 是常見的 URL 配置。 您可以選擇為您的應用程式建立自訂 URL 配置。 自訂 URL 配置是用來與其他應用程式來回通訊和傳送資料。 下列螢幕擷取畫面說明如何建立稱為 `monkeys://`的新自訂 URL 配置：

 [![](property-lists-images/url.png "Custom URLs")](property-lists-images/url.png#lightbox)

如需有關如何執行自訂 URL 配置的詳細資訊，請參閱[本指南的 Apple 的執行自訂 url 配置一節](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)。

## <a name="source-panel"></a>來源面板

`Info.plist` 檔案的 [**來源**] 索引標籤可讓您新增或編輯自訂值。 Visual Studio for Mac 提供最常見的屬性清單：

 [![](property-lists-images/image31.png "Adding a new property from a dropdown")](property-lists-images/image31.png#lightbox)

若為已知屬性 Visual Studio for Mac 將會是有效值的清單，如下列螢幕擷取畫面所示：

 [![](property-lists-images/image32.png "Select a value from a know value list")](property-lists-images/image32.png#lightbox)

Visual Studio for Mac 也會偵測屬性類型，如下所示：

 [![](property-lists-images/image33.png "The available property types")](property-lists-images/image33.png#lightbox)

如需選擇性屬性的其他資訊，請參閱 Apple 的[應用程式相關資源](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)連結。

 <a name="Entitlements" />

## <a name="summary"></a>總結

本文示範了如何使用圖形化和先進的 plist 編輯器來編輯一般應用程式設定，以及指定圖示和啟動影像。 同時也引進了新增和管理應用程式功能的 `Entitlements.plist`。

## <a name="related-links"></a>相關連結

- [IDE](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide)
- [應用程式相關資源](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [註冊應用程式支援的檔案類型](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [執行自訂 URL 配置](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [資產目錄格式參考](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/index.html#//apple_ref/doc/uid/TP40015170-CH18-SW1)
