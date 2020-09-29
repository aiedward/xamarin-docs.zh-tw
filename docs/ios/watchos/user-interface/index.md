---
title: Xamarin 中的 watchOS 消費者介面控制項
description: 本檔說明可在 watchOS 使用者介面中使用的各種控制項。 它會提供標籤、按鈕、切換、滑杆、影像、分隔符號、地圖等的描述。
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/19/2016
ms.openlocfilehash: 3d47b8d524d8f798ab9c0a654fde8246b9fbcc36
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437157"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>Xamarin 中的 watchOS 消費者介面控制項

[**WatchKitCatalog**](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog)範例會示範各種 watchOS 控制項。 應用程式的分鏡腳本如下所示 (按一下以放大) ：

[![範例 watchOS 版面配置](images/storyboard-sml.png)](images/storyboard.png#lightbox)

所有控制項的程式設計名稱前面都會加上 `WKInterface` (例如。 `WKInterfaceLabel`, `WKInterfaceButton`).

|控制|描述|螢幕擷取畫面|
|---|---|---|
|標籤|使用 `SetText` 和其他屬性來控制標籤控制項中文字的外觀。 `NSAttributedString` 也支援。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![標籤螢幕擷取畫面](Images/label.png)|
|按鈕|在腳本中建立並設定屬性。 Ctrl + 拖曳以加入 `Action` ，以執行按一下時的處理常式。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![按鈕螢幕擷取畫面](Images/button.png)|
|參數|用 `SetOn` 來控制切換狀態。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![切換畫面快照](Images/switch.png)|
|滑桿|可能有許多不同的樣式。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![滑杆螢幕擷取畫面](Images/slider.png)|
|映像|用 `myImage.SetImage("MyWatchImage")` 來載入 watch 上的影像，或將影像快取以 `WKInterfaceDevice.CurrentDevice.AddCachedImage` 在監看時重複使用。<br />[影像控制項檔](~/ios/watchos/user-interface/image.md)<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![影像螢幕擷取畫面](Images/image.png)|
|Separator|使用分隔符號來協助建立吸引人的監看式 Ui。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![分隔符號螢幕擷取畫面](Images/separator.png)|
|對應|地圖影像會在監看式上以靜態方式顯示，但您可以控制其外觀的許多層面，包括新增圖釘。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![地圖螢幕擷取畫面](Images/map.png)|
|Movie & InlineMove|電影可以單獨開啟或內嵌<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![影片螢幕擷取畫面](Images/movie.png)|
|Group|使用群組來協助建立吸引人的監看式 Ui。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![群組螢幕擷取畫面](Images/group.png)|
|資料表|IOS 上的簡化版本資料表。 執行 `DidSelectRow` 以回應使用者選取 (或使用 segue) 。<br />[Table 控制項檔](~/ios/watchos/user-interface/table.md)<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![資料表螢幕擷取畫面](Images/table.png)|
|裝置|`WKInterfaceDevice.CurrentDevice` 包含 `ScreenBounds` 、和等屬性 `ScreenScale` `PreferredContentSizeCategory` 。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![裝置螢幕擷取畫面](Images/device.png)|
|[功能表](~/ios/watchos/user-interface/menu.md)|在分鏡腳本中定義強制按下功能表，並針對程式碼中的每個按鈕執行動作。<br />[Menu 控制項 (Force Touch) 檔](~/ios/watchos/user-interface/menu.md)<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![功能表螢幕擷取畫面](Images/controller.png)|
|文字輸入|使用 `PresentTextInputController` 和 `WKTextInputMode` 列舉。<br />[文字輸入檔](~/ios/watchos/user-interface/text-input.md)<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![文字輸入螢幕擷取畫面](Images/textinput.png)|
|Digital Crown|Digital Crown 可以用來驅動選擇器，也可以在程式碼中追蹤它的旋轉。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![數位 crown 螢幕擷取畫面](Images/digital-crown.png)|
|軌跡|有四種類型的手勢辨識可新增至場景：點一下、滑動、平移及 LongPress。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![手勢螢幕擷取畫面](Images/gestures.png)|

## <a name="related-links"></a>相關連結

- [WatchKitCatalog (範例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [觀賞套件 API 參考](xref:WatchKit)