---
title: 在 Xamarin 中 watchOS 使用者介面控制項
description: 本檔描述可在 watchOS 使用者介面中使用的各種控制項。 它提供標籤、按鈕、開關、滑杆、影像、分隔符號、地圖等的描述。
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/19/2016
ms.openlocfilehash: c878416e98b8c530bbf90a621cfcc0d128aa55c7
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997055"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>在 Xamarin 中 watchOS 使用者介面控制項

[**WatchKitCatalog**](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog)範例會示範各種 watchOS 控制項。 應用程式的腳本會顯示在這裡（按一下以縮放）：

[![範例 watchOS 版面配置](images/storyboard-sml.png)](images/storyboard.png#lightbox)

所有控制項的程式設計名稱前面會加上 `WKInterface` （例如 `WKInterfaceLabel`, `WKInterfaceButton`).

|控制|描述|螢幕擷取畫面|
|---|---|---|
|標籤|使用 `SetText` 和其他屬性來控制標籤控制項中文字的外觀。 `NSAttributedString`也支援。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![標籤螢幕擷取畫面](Images/label.png)|
|按鈕|在分鏡腳本中建立並設定屬性。 Ctrl + 拖曳以加入 `Action` ，以在按下時執行處理常式。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![按鈕螢幕擷取畫面](Images/button.png)|
|參數|使用 `SetOn` 來控制參數狀態。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![切換畫面快照](Images/switch.png)|
|滑桿|可能會有許多不同的樣式。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![滑杆螢幕擷取畫面](Images/slider.png)|
|影像|使用在 `myImage.SetImage("MyWatchImage")` 監看式上載入影像，或快取以在 `WKInterfaceDevice.CurrentDevice.AddCachedImage` 監看式上重複使用。<br />[影像控制檔](~/ios/watchos/user-interface/image.md)<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![影像螢幕擷取畫面](Images/image.png)|
|Separator|使用分隔符號來協助建立吸引人的監看式 Ui。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![分隔符號螢幕擷取畫面](Images/separator.png)|
|對應|地圖影像會以靜態方式顯示在監看式，但您可以控制其外觀的許多層面，包括新增圖釘。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![地圖螢幕擷取畫面](Images/map.png)|
|電影 & InlineMove|電影可以自行開啟或內嵌<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![影片螢幕擷取畫面](Images/movie.png)|
|群組|使用群組協助建立吸引人的監看式 Ui。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![群組螢幕擷取畫面](Images/group.png)|
|Table|IOS 上的簡化版資料表。 執行 `DidSelectRow` 以回應使用者選擇（或使用 segue）。<br />[資料表控制項檔](~/ios/watchos/user-interface/table.md)<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![資料表螢幕擷取畫面](Images/table.png)|
|裝置|`WKInterfaceDevice.CurrentDevice`包含屬性，例如 `ScreenBounds` 、 `ScreenScale` 和 `PreferredContentSizeCategory` 。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![裝置螢幕擷取畫面](Images/device.png)|
|[功能表](~/ios/watchos/user-interface/menu.md)|定義分鏡腳本中的 [force] 功能表，並針對程式碼中的每個按鈕執行動作。<br />[Menu 控制項（Force Touch）檔](~/ios/watchos/user-interface/menu.md)<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![功能表螢幕擷取畫面](Images/controller.png)|
|文字輸入|使用 `PresentTextInputController` 和 `WKTextInputMode` 列舉。<br />[文字輸入檔](~/ios/watchos/user-interface/text-input.md)<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![文字輸入螢幕擷取畫面](Images/textinput.png)|
|Digital Crown|Digital Crown 可用來驅動選擇器，也可以在程式碼中追蹤它的旋轉。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![數位 crown 螢幕擷取畫面](Images/digital-crown.png)|
|軌跡|有四種類型的手勢辨識可新增到場景中：點一下、滑動、Pan 和 LongPress。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![手勢螢幕擷取畫面](Images/gestures.png)|

## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [觀看套件 API 參考](xref:WatchKit)
