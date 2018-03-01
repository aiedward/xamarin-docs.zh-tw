---
title: "watchOS 使用者介面"
ms.topic: article
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/19/2016
ms.openlocfilehash: 34063be728f8a13bbe5d68440d9aceba417c5627
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="watchos-user-interface"></a>watchOS 使用者介面

[ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog)範例會示範各種 watchOS 控制項。 應用程式的分鏡腳本如下所示 （按一下以顯示比例）：

[ ![](images/storyboard-sml.png "範例 watchOS 版面配置")](images/storyboard.png)

所有控制項的程式設計名稱前面會加上`WKInterface`（例如。 `WKInterfaceLabel`, `WKInterfaceButton`).


<table align="center" border="1" cellpadding="1" cellspacing="1">
  <thead>
      <th>
        <strong>控制項</strong>
      </th>
      <th>
        <strong>描述</strong>
      </th>
      <th>
        <strong>螢幕擷取畫面</strong>
      </th>
    </thead>
    <tbody>
    <tr>
      <td valign="top">
ThisAddIn </td>
      <td valign="top">
使用<code>SetText</code>和其他屬性，以控制標籤控制項中文字的外觀。 <code>NSAttributedString</code> 也支援。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/label.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
按鈕 </td>
      <td valign="top">
建立並設定屬性中的分鏡腳本。 <kbd>Ctrl + 拖曳</kbd>新增<code>Action</code>實作按一下時的處理常式。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/button.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
參數 </td>
      <td valign="top">
使用<code>SetOn</code>控制切換狀態。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/switch.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
滑桿 </td>
      <td valign="top">
可能會有許多不同的樣式。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/slider.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Image </td>
      <td valign="top">
使用<code>myImage.SetImage("MyWatchImage")</code>載入監看式 上的映像或<code>WKInterfaceDevice.CurrentDevice.AddCachedImage</code>進行快取上監看式重複使用。
        <br />
        <a href="~/ios/watchos/user-interface/image.md">影像控制項文件</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/image.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Separator </td>
      <td valign="top">
您可以使用分隔符號，幫助您建立具吸引力的監看式 Ui。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/separator.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
對應 </td>
      <td valign="top">
地圖影像會以靜態方式顯示在 監看式，但是您可以控制其外觀，包括新增 pin 的許多層面。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/map.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Movie & InlineMove </td>
      <td valign="top">
影片可以開啟在其本身或內嵌 <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/movie.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
群組 </td>
      <td valign="top">
使用群組來幫助您建立具吸引力的監看式 Ui。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/group.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
資料表 </td>
      <td valign="top">
在 iOS 上資料表的簡化的版本。
實作<code>DidSelectRow</code>回應使用者選取 （或使用 segue）。
        <br />
        <a href="~/ios/watchos/user-interface/table.md">表格控制項文件</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TableDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/table.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
裝置 </td>
      <td valign="top">
        <code>WKInterfaceDevice.CurrentDevice</code> 包含屬性，例如<code>ScreenBounds</code>， <code>ScreenScale</code>，和<code>PreferredContentSizeCategory</code>。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/device.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="~/ios/watchos/user-interface/menu.md">功能表</a>
      </td>
      <td valign="top">
在分鏡腳本中定義的強制按下功能表和實作程式碼中的每個按鈕的動作。
        <br />
        <a href="~/ios/watchos/user-interface/menu.md">功能表控制項 （強制觸控） 文件</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/controller.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
文字輸入 </td>
      <td valign="top">
使用<code>PresentTextInputController</code>和<code>WKTextInputMode</code>列舉型別。
        <br />
        <a href="~/ios/watchos/user-interface/text-input.md">文字輸入文件</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/textinput.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
數位皇冠 </td>
      <td valign="top">
數位皇冠可以用來驅動選擇器，或在程式碼中可追蹤它的旋轉。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/digital-crown.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
軌跡 </td>
      <td valign="top">
有四種類型的筆勢辨識可加入至場景： 點選、 撥動、 取景位置調整和 LongPress。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs">類別目錄的程式碼</a>
      </td>
      <td>
        <img src="Images/gestures.png" class="tableimg">
      </td>
    </tr>
    </tbody>
</table>



## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [監看式套件 API 參考](https://developer.xamarin.com/api/namespace/WatchKit/)
