---
title: 在 Xamarin 中 watchOS 使用者介面控制項
description: 本檔描述可在 watchOS 使用者介面中使用的各種控制項。 它提供標籤、按鈕、開關、滑杆、影像、分隔符號、地圖等的描述。
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/19/2016
ms.openlocfilehash: 5b8cbfb3529af0eef81524d450bf395a07c65471
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651742"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>在 Xamarin 中 watchOS 使用者介面控制項

[**WatchKitCatalog**](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog)範例會示範各種 watchOS 控制項。 應用程式的腳本會顯示在這裡 (按一下以縮放):

[![](images/storyboard-sml.png "範例 watchOS 版面配置")](images/storyboard.png#lightbox)

所有控制項的程式設計名稱前面會加`WKInterface`上 (例如 `WKInterfaceLabel`, `WKInterfaceButton`).

|控制項|描述|螢幕擷取畫面|
|---|---|---|
|ThisAddIn|使用`SetText`和其他屬性來控制標籤控制項中文字的外觀。 `NSAttributedString`也支援。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![](Images/label.png)|
|按鈕|在分鏡腳本中建立並設定屬性。 Ctrl + 拖曳以加入`Action` , 以在按下時執行處理常式。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![](Images/button.png)|
|參數|使用`SetOn`來控制參數狀態。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![](Images/switch.png)|
|滑桿|可能會有許多不同的樣式。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![](Images/slider.png)|
|Image|使用`myImage.SetImage("MyWatchImage")`在監看式上載入影像, `WKInterfaceDevice.CurrentDevice.AddCachedImage`或快取以在監看式上重複使用。<br />[影像控制檔](~/ios/watchos/user-interface/image.md)<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![](Images/image.png)|
|Separator|使用分隔符號來協助建立吸引人的監看式 Ui。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![](Images/separator.png)| 
|地圖|地圖影像會以靜態方式顯示在監看式, 但您可以控制其外觀的許多層面, 包括新增圖釘。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![](Images/map.png)|
|電影 & InlineMove|電影可以自行開啟或內嵌<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![](Images/movie.png)|
|群組|使用群組協助建立吸引人的監看式 Ui。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![](Images/group.png)|
|資料表|IOS 上的簡化版資料表。 執行`DidSelectRow`以回應使用者選擇 (或使用 segue)。<br />[資料表控制項檔](~/ios/watchos/user-interface/table.md)<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![](Images/table.png)|
|裝置|`WKInterfaceDevice.CurrentDevice`包含屬性`ScreenBounds`, 例如、 `ScreenScale`和。 `PreferredContentSizeCategory`<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![](Images/device.png)|
|[Menu](~/ios/watchos/user-interface/menu.md)|定義分鏡腳本中的 [force] 功能表, 並針對程式碼中的每個按鈕執行動作。<br />[Menu 控制項 (Force Touch) 檔](~/ios/watchos/user-interface/menu.md)<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![](Images/controller.png)|
|文字輸入|`PresentTextInputController` 使用`WKTextInputMode`和列舉。<br />[文字輸入檔](~/ios/watchos/user-interface/text-input.md)<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![](Images/textinput.png)|
|Digital Crown|Digital Crown 可用來驅動選擇器, 也可以在程式碼中追蹤它的旋轉。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![](Images/digital-crown.png)|
|軌跡|有四種類型的手勢辨識可以加入場景中:點一下、滑動、Pan 和 LongPress。<br />[目錄程式碼](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![](Images/gestures.png)|


## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [觀看套件 API 參考](xref:WatchKit)
