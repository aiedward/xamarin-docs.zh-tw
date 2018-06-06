---
title: 使用在 Xamarin.iOS 屬性清單
description: 本文件介紹使用 Info.plist 與 Entitlements.plist Mac 的圖形化和進階屬性清單 (.plist) 編輯器的 Visual Studio。 它說明如何設定圖示和啟動映像，以從 iOS 應用程式在 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 6b22acab3fb19a6209fac8dcf6a0870763e601d2
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784464"
---
# <a name="working-with-property-lists-in-xamarinios"></a>使用在 Xamarin.iOS 屬性清單

_本文件介紹使用 Info.plist 與 Entitlements.plist Mac 的圖形化和進階屬性清單 (.plist) 編輯器的 Visual Studio。它說明如何設定圖示和啟動映像，以從 iOS 應用程式在 Visual Studio for mac。_

Visual Studio for Mac 功能.plist 圖形化編輯器，可讓您編輯應用程式屬性和功能更容易。 適用於 Mac 的 visual Studio 的兩個.plists-`Info.plist`編輯應用程式內容和圖示，並`Entitlements.plist`管理應用程式功能。 本指南介紹 Info.plists，並概略說明它們在中使用 Visual Studio for mac。 如需 Entitlements.plist 資訊，請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

## <a name="infoplist"></a>Info.plist

資訊屬性清單 ( `Info.plist`) 是必要的 Io 檔案，提供您的應用程式組態系統的相關資訊。 Visual Studio for Mac 的自訂`Info.plist`控制索引標籤下方的三個面板左邊 [編輯器] 視窗的編輯器功能：

 [![](property-lists-images/tabs.png "編輯器視窗的左底端 Info.plist 編輯器索引標籤")](property-lists-images/tabs.png#lightbox)

每個面板控制項屬性不同，如下所述：

-  **應用程式面板**-圖形介面，可設定的通用應用程式屬性，以及圖示和啟動映像，指定對應整合和 backgrounding 模式。
-  **進階面板**-進階 面板就會指定支援的文件類型，UTIs、 位置和 URL 類型。
-  **來源面板**-來源面板控制較不常見的屬性，以及應用程式的自訂屬性。


以下三節調查每個面板中更詳細的功能。

## <a name="application-panel"></a>應用程式 面板

適用於 Mac 的 visual Studio 功能的圖形介面編輯一般`Info.plist`應用程式的項目：

1.  應用程式屬性
1.  支援的裝置類型
1.  每個裝置類型支援方向
1.  狀態列樣式和色彩
1.  圖示和啟動畫面
1.  對應和背景模式


這些會在下一節中詳細描述。

 <a name="iOS_Application_Target" />


### <a name="ios-application-target"></a>iOS 應用程式的目標

本節包含描述您的應用程式的重要資訊。
**識別碼**儲存這裡必須符合在 iTunes Connect （適用於應用程式市集應用程式），也在開發和發佈憑證與 iOS 佈建入口網站應用程式識別碼清單輸入的套件組合識別碼。

 [![](property-lists-images/image24.png "iOS 應用程式的目標")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>裝置部署

 [![](property-lists-images/deployment.png "裝置部署")](property-lists-images/deployment.png#lightbox)

裝置**部署**資訊區段會選擇性地根據中選取項目而顯示**裝置**下拉式清單中的**應用程式目標**上一節。 **主要介面**下拉式清單設定為**MainStoryboard**分鏡腳本驅動的應用程式中。 如果使用者介面完全以程式碼撰寫，這可以是空白。

### <a name="supported-device-orientations"></a>支援的裝置方向

 **支援裝置方向**控制應用程式如何回應裝置旋轉。 它是非常普遍的 iPhone/iPad 應用程式只支援**縱向**，或所有項目，但**面朝下**。 一般遊戲以外的所有 iPad 應用程式應該都支援所有方向。

### <a name="status-bar-styles"></a>狀態列樣式

**狀態軸樣式**區段是編輯應用程式的圖形化介面`UIStatusBarStyle`:

 [![](property-lists-images/status.png "狀態列樣式")](property-lists-images/status.png#lightbox)

 <a name="Icons" />


### <a name="icons-launch-images-and-itunes-artwork"></a>圖示、 啟動映像和 iTunes 圖檔

使用圖示、 影像和圖案 Info.plist 檔案中的資訊位於[處理映像](~/ios/app-fundamentals/images-icons/index.md)指南。




### <a name="maps-integration-and-background-modes"></a>對應整合和背景模式

`Info.plist`包含指定對應整合和 backgrounding 模式的特殊區段。 選擇您想要支援的選項將會加入您的應用程式所需的屬性。

 [![](property-lists-images/maps.png "對應整合")](property-lists-images/maps.png#lightbox)

如需有關使用 map 的詳細資訊，請參閱 Xamarin [iOS 對應](~/ios/user-interface/controls/ios-maps/index.md)指南。

 [![](property-lists-images/bging.png "背景模式")](property-lists-images/bging.png#lightbox)

如需背景模式的詳細資訊，請參閱 Xamarin [Backgrounding 在 iOS 中](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)指南。

## <a name="advanced-panel"></a>進階的面板

[進階] 面板控制文件類型和應用程式支援的 URL 結構描述。

 [![](property-lists-images/image34.png "進階的面板")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />


## <a name="document-types"></a>文件類型

對於支援開啟特定檔案類型的應用程式，提供 Io`CFBundleDocumentTypes`索引鍵。 如果我們想要我們的應用程式，以支援特定已知的檔案類型-例如 Pdf-我們會加入 PDF 值的索引鍵。 本節提供便利的方式輸入的資料會儲存在`CFBundleDocumentTypes`中的索引鍵`Info.plist`檔案。

請參閱文件上[註冊檔案類型您的應用程式支援](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)如需如何設定這些值的詳細資訊。

## <a name="utis"></a>UTIs

有時候應用程式必須支援開啟自訂檔案類型。 比方說，我們可能會想要開啟映像檔案的自訂延伸模組與 *.xam*。 若要指定自訂檔案類型，我們將建立自訂 UTI-通用類型識別碼-使用`UIExportedTypeDeclarations`索引鍵。 下列螢幕擷取畫面說明了如何建立自訂 UTI.xam 延伸模組：

 [![](property-lists-images/uti.png "UTIs 編輯器")](property-lists-images/uti.png#lightbox)

只為匯出的型別 UTIs 指定應用程式特有的自訂 UTIs*匯入類型 UTIs* (`UIImportedTypeDeclarations`索引鍵) 指定自訂類型支援，但不是屬於您的應用程式。

如需使用自訂 UTIs 的詳細資訊，請參閱 Apple[註冊檔案類型您的應用程式支援](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1)指南。

## <a name="custom-urls"></a>自訂 Url

（也稱為通訊協定） 的 URL 配置名稱是 URL 的第一個部分。 例如，`http://`和`https://`通用的 URL 結構描述。 您必須建立自訂的 URL 配置，您的應用程式的選項。 自訂的 URL 配置用來通訊，並使用其他應用程式，來回傳送的資料。 下列螢幕擷取畫面說明了如何建立新自訂 URL 配置呼叫`monkeys://`:

 [![](property-lists-images/url.png "自訂 Url")](property-lists-images/url.png#lightbox)



如需有關如何實作自訂的 URL 結構描述的詳細資訊，請參閱 Apple[實作自訂的 URL 配置本指南的章節](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>來源面板

**來源** 索引標籤`Info.plist`檔可讓自訂值以加入或編輯。 適用於 Mac 的 visual Studio 提供最常見的屬性的清單：

 [![](property-lists-images/image31.png "從下拉式清單中加入新屬性")](property-lists-images/image31.png#lightbox)

已知屬性適用於 Mac 的 Visual Studio 將一份有效的值，如下列螢幕擷取畫面所示：

 [![](property-lists-images/image32.png "從已知值清單中選取值")](property-lists-images/image32.png#lightbox)

適用於 Mac 的 visual Studio 也會偵測屬性型別，如所示：

 [![](property-lists-images/image33.png "可用的屬性類型")](property-lists-images/image33.png#lightbox)

檢閱 Apple[應用程式的相關資源](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)選擇性的屬性上的其他資訊的連結。

 <a name="Entitlements" />

## <a name="summary"></a>總結

本文示範使用圖形化和進階.plist 編輯器編輯一般應用程式設定也指定圖示和啟動映像。 它也導入了`Entitlements.plist`新增和管理應用程式功能。


## <a name="related-links"></a>相關連結

- [IDE](https://developer.xamarin.com/recipes/cross-platform/ide)
- [應用程式相關聯的資源](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [註冊檔案的應用程式支援的型別](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [實作自訂的 URL 結構描述](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [關於資產目錄](https://developer.apple.com/library/ioshttps://developer.xamarin.com/recipes/xcode_help-image_catalog-1.0/Recipe.html)
