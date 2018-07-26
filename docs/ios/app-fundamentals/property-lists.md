---
title: 使用 Xamarin.iOS 中的屬性清單
description: 本文件會介紹 Visual Studio for Mac 的圖形化和進階屬性清單 (.plist) 編輯器來使用 Info.plist 和 Entitlements.plist。 它說明如何設定圖示和啟動影像的 iOS 應用程式在 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: b102f268fd457ca42f3d64b5766a2b3824e3849d
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242325"
---
# <a name="working-with-property-lists-in-xamarinios"></a>使用 Xamarin.iOS 中的屬性清單

_本文件會介紹 Visual Studio for Mac 的圖形化和進階屬性清單 (.plist) 編輯器來使用 Info.plist 和 Entitlements.plist。它說明如何設定圖示和啟動影像的 iOS 應用程式在 Visual Studio for mac。_

Visual Studio for Mac 功能的圖形化的.plist 編輯器可編輯的應用程式屬性和功能更容易。 Visual Studio for Mac 具有兩個.plists-`Info.plist`編輯應用程式屬性和圖示和`Entitlements.plist`管理應用程式功能。 本指南介紹 Info.plists，並概述使用它們在 Visual Studio for mac。 在 Entitlements.plist 上的資訊，請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

## <a name="infoplist"></a>Info.plist

資訊屬性清單 ( `Info.plist`) 是提供給系統的應用程式的組態資訊的必要的 iOS 檔案。 Visual Studio for Mac 的自訂`Info.plist`底部的索引標籤所控制的三個面板方的 [編輯器] 視窗的編輯器功能：

 [![](property-lists-images/tabs.png "Info.plist 編輯器索引標籤底部方的 [編輯器] 視窗")](property-lists-images/tabs.png#lightbox)

每個面板控制項不同的屬性，如下所示：

-  **應用程式面板**-圖形介面，可設定一般的應用程式屬性，以及圖示和啟動映像，指定地圖整合 」 和 「 背景模式。
-  **進階面板**-進階的面板可供指定支援的文件類型 Uti，和 URL 類型。
-  **來源面板**-[來源] 面板可控制較不常見的屬性，以及應用程式的自訂屬性。


接下來三節調查更多詳細資料中的每個面板的功能。

## <a name="application-panel"></a>應用程式面板

Visual Studio for Mac 功能編輯常見的圖形化介面`Info.plist`應用程式的項目：

1.  應用程式屬性
1.  支援的裝置類型
1.  每個裝置類型支援方向
1.  狀態列樣式和色彩
1.  圖示和啟動畫面
1.  對應和背景模式


在下一節中詳細說明。

 <a name="iOS_Application_Target" />


### <a name="ios-application-target"></a>iOS 應用程式目標

本節將描述您的應用程式的重要資訊。
**識別碼**儲存這裡必須符合在 iTunes Connect （適用於應用程式市集應用程式），也在開發和散佈的憑證與 iOS 佈建入口網站應用程式識別碼清單輸入套件組合識別碼。

 [![](property-lists-images/image24.png "iOS 應用程式目標")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>裝置部署

 [![](property-lists-images/deployment.png "裝置部署")](property-lists-images/deployment.png#lightbox)

裝置**部署**根據中的選取範圍會選擇性地顯示資訊區段**裝置**下拉式清單中的**應用程式目標**上一節。 **主要介面**下拉式清單設為**MainStoryboard**分鏡腳本驅動的應用程式中。 如果使用者介面完全以程式碼撰寫，則這可以保留空白。

### <a name="supported-device-orientations"></a>支援的裝置方向

 **支援裝置方向**控制應用程式如何回應裝置旋轉。 它是非常普遍的 iPhone/iPad 應用程式，以支援僅**縱向**，或所有項目，但**上下顚倒**。 一般遊戲以外的所有 iPad 應用程式應該都支援所有方向。

### <a name="status-bar-styles"></a>狀態列樣式

**狀態的列樣式**一節是編輯應用程式的圖形化介面`UIStatusBarStyle`:

 [![](property-lists-images/status.png "狀態列樣式")](property-lists-images/status.png#lightbox)

 <a name="Icons" />


### <a name="icons-launch-images-and-itunes-artwork"></a>圖示、 啟動映像和 iTunes 插圖

中，可以找到有關使用 Info.plist 檔案中的圖示、 影像和插圖[處理映像](~/ios/app-fundamentals/images-icons/index.md)指南。




### <a name="maps-integration-and-background-modes"></a>地圖整合 」 和 「 背景模式

`Info.plist`包含指定地圖整合 」 和 「 背景模式的特殊區段。 選擇您想要支援的選項會將您的應用程式，為您所需的屬性。

 [![](property-lists-images/maps.png "地圖整合")](property-lists-images/maps.png#lightbox)

如需有關使用對應的詳細資訊，請參閱 Xamarin [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md)指南。

 [![](property-lists-images/bging.png "背景模式")](property-lists-images/bging.png#lightbox)

如需有關背景模式的詳細資訊，請參閱 Xamarin [ios 中的背景](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)指南。

## <a name="advanced-panel"></a>進階的面板

[進階] 面板會控制文件類型和應用程式支援的 URL 配置。

 [![](property-lists-images/image34.png "進階的面板")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />


## <a name="document-types"></a>文件類型

對於支援開啟特定檔案類型的應用程式，提供 iOS`CFBundleDocumentTypes`索引鍵。 如果我們想要我們的應用程式，以支援特定已知的檔案類型-例如 Pdf-我們會將 PDF 值加入至機碼。 本節提供便利的方式輸入的資料會儲存在`CFBundleDocumentTypes`中的索引鍵`Info.plist`檔案。

參閱文件上[註冊檔案類型您的應用程式支援](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)如需有關如何設定這些值。

## <a name="utis"></a>Uti

有時候應用程式必須支援開啟自訂檔案類型。 比方說，我們可能會想要開啟的自訂延伸模組映像檔 *.xam*。 若要指定自訂檔案類型，我們將建立自訂 UTI-通用類型識別碼-使用`UIExportedTypeDeclarations`索引鍵。 以下螢幕擷取畫面說明如何建立自訂的 UTI.xam 延伸模組：

 [![](property-lists-images/uti.png "Uti 編輯器")](property-lists-images/uti.png#lightbox)

只為匯出的類型 Uti 指定您的應用程式特有的自訂 Uti*匯入類型 Uti* (`UIImportedTypeDeclarations`金鑰) 指定自訂類型支援，但不是屬於您的應用程式。

如需有關如何使用自訂 Uti 的詳細資訊，請參閱 Apple[註冊檔案類型您的應用程式支援](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1)指南。

## <a name="custom-urls"></a>自訂 Url

URL 配置名稱 （也稱為通訊協定） 是 URL 的第一個部分。 例如，`http://`和`https://`常見的 URL 配置。 您可以選擇建立自訂的 URL 配置，您的應用程式。 自訂的 URL 配置用來通訊，並使用其他應用程式來回傳送的資料。 下列螢幕擷取畫面說明如何建立新自訂呼叫的 URL 配置`monkeys://`:

 [![](property-lists-images/url.png "自訂 Url")](property-lists-images/url.png#lightbox)



如需有關如何實作自訂的 URL 配置的詳細資訊，請參閱 Apple 的[本指南的實作自訂的 URL 配置區段](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>來源面板

**來源**索引標籤`Info.plist`檔可讓自訂值以新增或編輯。 Visual Studio for Mac 提供最常見的屬性清單：

 [![](property-lists-images/image31.png "從下拉式清單中加入新的屬性")](property-lists-images/image31.png#lightbox)

已知的屬性 Visual Studio for Mac 會一份有效的值，如下列螢幕擷取畫面所示：

 [![](property-lists-images/image32.png "從已知值清單中選取值")](property-lists-images/image32.png#lightbox)

Visual Studio for Mac 也會偵測屬性類型，如所示：

 [![](property-lists-images/image33.png "可用的屬性類型")](property-lists-images/image33.png#lightbox)

檢閱 Apple[應用程式的相關資源](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)之選擇性的屬性上的其他資訊連結。

 <a name="Entitlements" />

## <a name="summary"></a>總結

這篇文章示範如何使用圖形化和進階的.plist 編輯器來編輯通用應用程式設定也指定圖示和啟動影像。 它也引進了`Entitlements.plist`新增和管理應用程式功能。


## <a name="related-links"></a>相關連結

- [IDE](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide)
- [應用程式相關資源](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [註冊檔案類型的應用程式支援](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [實作自訂的 URL 配置](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [資產目錄格式參考](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/index.html#//apple_ref/doc/uid/TP40015170-CH18-SW1)
