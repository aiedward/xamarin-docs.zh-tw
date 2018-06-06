---
title: 使用 tvOS 導覽列中的 Xamarin
description: 本文件說明如何使用導覽列中使用 Xamarin 建置 tvOS 應用程式。 它討論設定分鏡腳本中的巡覽列，以及從這些按鈕回應事件。
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: b6c8ff8551c91578b9399b88e90e94c6af12ac68
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789287"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>使用 tvOS 導覽列中的 Xamarin

可以檢視以顯示標題和選擇性的導覽列按鈕的頂端加入導覽列。 通常這些用於當使用者巡覽從主要頁面上，例如資料表檢視表、 集合或功能表來顯示選取之項目的詳細資料子檢視。

[![](navigation-bars-images/navbar01.png "範例的導覽列")](navigation-bars-images/navbar01.png#lightbox)

除了標題 （也就顯示在中央），在巡覽列可以包含一或多個導覽列按鈕 (`UIBarButtonItem`) 左邊和右邊的列。

> [!IMPORTANT]
> 導覽列完全都預設為透明。 應該小心，確保其下方內容上保持可讀取導覽列的內容。 例如，當資料表檢視表或集合中的內容捲動其下。

<a name="Navigation-Bars-and-Storyboards" />

## <a name="navigation-bars-and-storyboards"></a>導覽列和分鏡腳本

Xamarin.tvOS 應用程式中使用導覽列的最簡單方式是將它們新增到應用程式的 UI 使用 iOS 設計工具。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在**方案板**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**導覽列**從**工具箱**並放在畫面頂端的檢視： 

    [![](navigation-bars-images/navbar02.png "瀏覽列")](navigation-bars-images/navbar02.png#lightbox)
1. 按兩下**導覽列**選取**瀏覽項目**。 在**Widget**  索引標籤**屬性板**，您可以設定**標題**: 

    [![](navigation-bars-images/navbar03.png "設定標題")](navigation-bars-images/navbar03.png#lightbox)
1. 接下來，您可以在其中加入一個或多個**列按鈕項目**至列的任一端點： 

    [![](navigation-bars-images/navbar04.png "橫條按鈕項目")](navigation-bars-images/navbar04.png#lightbox)
1. 最後，網路向上**列按鈕項目**中的動作**事件** 索引標籤**屬性總管**: 

    [![](navigation-bars-images/navbar05.png "橫條按鈕項目動作")](navigation-bars-images/navbar05.png#lightbox)
1. 儲存您的變更。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


1. 在**方案總管 中**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**導覽列**從**工具箱**並放在畫面頂端的檢視： 

    [![](navigation-bars-images/navbar02-vs.png "瀏覽列")](navigation-bars-images/navbar02-vs.png#lightbox)
1. 按兩下**導覽列**選取**瀏覽項目**。 在**Widget**  索引標籤**屬性總管**，您可以設定**標題**: 

    [![](navigation-bars-images/navbar03-vs.png "設定標題")](navigation-bars-images/navbar03-vs.png#lightbox)
1. 接下來，您可以在其中加入一個或多個**列按鈕項目**至列的任一端點： 

    [![](navigation-bars-images/navbar04-vs.png "橫條按鈕項目")](navigation-bars-images/navbar04-vs.png#lightbox)
1. 最後，網路向上**列按鈕項目**中的動作**事件** 索引標籤**屬性總管**: 

    [![](navigation-bars-images/navbar05-vs.png "橫條按鈕項目動作")](navigation-bars-images/navbar05-vs.png#lightbox)
1. 儲存您的變更。


-----

> [!IMPORTANT]
> 雖然您可以指派事件，例如`TouchUpInside`UI 中的項目 （例如 UIButton) iOS 設計工具，它會永遠不會呼叫因為 Apple TV 沒有觸控螢幕或支援觸控事件。 您應該一律使用`Primary Action`事件時建立事件處理常式 tvos 使用者介面項目。

下列程式碼會提供三個不同 BarButtonItems 事件處理常式的範例： `ShowFirstHotel`， `ShowSecondHotel`，和`ShowThirdHotel`。 當每個項目按一下時，背景影像`HotelImage`變更。 這在檢視控制器中編輯 (範例`ViewController.cs`) 檔案：

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

只要按鈕`Enabled`屬性是`true`而且它未涵蓋其他控制項或檢視，可以設為使用 Siri 遠端的焦點在項目。

如需使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和 Xamarin.tvOS 應用程式內使用導覽列。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
