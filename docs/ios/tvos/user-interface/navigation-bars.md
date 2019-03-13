---
title: 使用 tvOS 在 Xamarin 中的導覽列
description: 本文件說明如何使用導覽列中使用 Xamarin 建置的 tvOS 應用程式。 它討論設定分鏡腳本中的導覽列，以及從這些按鈕回應事件。
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 81e6cfe1e532bcfa7616e35adb28b314587bafc8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106945"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>使用 tvOS 在 Xamarin 中的導覽列

導覽列可加入的檢視，以顯示標題和選擇性的導覽列按鈕上方。 通常這些用於當使用者巡覽從主要頁面上，例如資料表檢視、 集合或子檢視，顯示所選項目的詳細資料 功能表。

[![](navigation-bars-images/navbar01.png "範例導覽列")](navigation-bars-images/navbar01.png#lightbox)

除了標題 （也就顯示在中心），在巡覽列可以包含一或多個導覽列按鈕 (`UIBarButtonItem`) 在左邊和右邊的列。

> [!IMPORTANT]
> 導覽列完全都預設為透明。 應該小心以確保導覽列的內容保持可讀取，透過它的內容。 例如，當資料表檢視表或集合中的內容捲動其下。

<a name="Navigation-Bars-and-Storyboards" />

## <a name="navigation-bars-and-storyboards"></a>導覽列和分鏡腳本

Xamarin.tvOS 應用程式中使用導覽列的最簡單方式是將它們新增至使用 iOS 設計工具的應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在  **Solution Pad**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**瀏覽列**從**工具箱**放在畫面頂端的 檢視： 

    [![](navigation-bars-images/navbar02.png "導覽列")](navigation-bars-images/navbar02.png#lightbox)
1. 按兩下**瀏覽列**以選取要**瀏覽項目**。 在  **Widget**索引標籤**Properties Pad**，您可以設定**標題**: 

    [![](navigation-bars-images/navbar03.png "設定標題")](navigation-bars-images/navbar03.png#lightbox)
1. 接下來，您可以在其中新增一或多個**橫條的按鈕項目**至列的任一端： 

    [![](navigation-bars-images/navbar04.png "Bar 按鈕項目 A")](navigation-bars-images/navbar04.png#lightbox)
1. 最後，會將網路接連**列按鈕的項目**中的動作**事件**索引標籤**屬性總管**: 

    [![](navigation-bars-images/navbar05.png "橫條按鈕項目動作")](navigation-bars-images/navbar05.png#lightbox)
1. 儲存您的變更。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. 在 **方案總管**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**瀏覽列**從**工具箱**放在畫面頂端的 檢視： 

    [![](navigation-bars-images/navbar02-vs.png "導覽列")](navigation-bars-images/navbar02-vs.png#lightbox)
1. 按兩下**瀏覽列**以選取要**瀏覽項目**。 在  **Widget**索引標籤**屬性總管**，您可以設定**標題**: 

    [![](navigation-bars-images/navbar03-vs.png "設定標題")](navigation-bars-images/navbar03-vs.png#lightbox)
1. 接下來，您可以在其中新增一或多個**橫條的按鈕項目**至列的任一端： 

    [![](navigation-bars-images/navbar04-vs.png "橫條 按鈕的項目")](navigation-bars-images/navbar04-vs.png#lightbox)
1. 最後，會將網路接連**列按鈕的項目**中的動作**事件**索引標籤**屬性總管**: 

    [![](navigation-bars-images/navbar05-vs.png "橫條按鈕項目動作")](navigation-bars-images/navbar05-vs.png#lightbox)
1. 儲存您的變更。


-----

> [!IMPORTANT]
> 雖然您可以指派事件，例如`TouchUpInside`UI 中的項目 （例如標記的 UIButton) iOS 設計工具，則會永遠不會呼叫因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 您應該一律使用`Primary Action`事件建立事件處理常式 tvOS 的使用者介面項目時。

下列程式碼會提供三個不同的 BarButtonItems 事件處理常式的範例： `ShowFirstHotel`， `ShowSecondHotel`，和`ShowThirdHotel`。 每個項目按一下的時，背景影像`HotelImage`變更。 這在 檢視控制器中編輯 (範例`ViewController.cs`) 檔案：

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void ShowFirstHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel01.jpg");
        }

        partial void ShowSecondHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel02.jpg");
        }

        partial void ShowThirdHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel03.jpg");
        }
        #endregion
    }
}
```

只要一個按鈕`Enabled`屬性是`true`並不受到另一個控制項或檢視，可使用 Siri 遠端單元的焦點項目。

如需有關使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計與 Xamarin.tvOS 應用程式內使用導覽列。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
