---
title: 在 Xamarin 中使用 tvOS 導覽列
description: 本檔說明如何在以 Xamarin 建立的 tvOS 應用程式中使用巡覽列。 它討論如何設定分鏡腳本中的導覽列，以及回應這些按鈕的事件。
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 0d5ec4bc10747a287def3fd9a83a703d2ec4b2a2
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84572373"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>在 Xamarin 中使用 tvOS 導覽列

導覽列可以加入至 [視圖] 頂端，以顯示標題和選擇性的導覽列按鈕。 當使用者從主頁面（例如資料表視圖、集合或功能表）導覽至顯示所選取專案之詳細資料的子視圖時，通常會使用它們。

[![](navigation-bars-images/navbar01.png "Sample Navigation Bar")](navigation-bars-images/navbar01.png#lightbox)

除了標題（顯示在中央）之外，導覽列也可以包含列左右兩側的一個或多個導覽列按鈕（ `UIBarButtonItem` ）。

> [!IMPORTANT]
> 導覽列預設是完全透明的。 請特別小心，以確保導覽列的內容可以透過其底下的內容保持可讀取狀態。 例如，當資料表視圖或集合中的內容在其下滾動時。

<a name="Navigation-Bars-and-Storyboards"></a>

## <a name="navigation-bars-and-storyboards"></a>巡覽列和分鏡腳本

在 tvOS 應用程式中使用導覽列的最簡單方式，就是使用 iOS 設計工具將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 [ **Solution Pad**中，按兩下 [檔案]， `Main.storyboard` 然後開啟以進行編輯。
1. 從 [**工具箱**] 將**導覽**列拖放到畫面頂端的視圖上：

    [![](navigation-bars-images/navbar02.png "A Navigation Bar")](navigation-bars-images/navbar02.png#lightbox)
1. 按兩下**導覽**列以選取 [**流覽] 專案**。 在 [ **Properties Pad**的 [ **Widget** ] 索引標籤中，您可以設定**標題**：

    [![](navigation-bars-images/navbar03.png "Set the Title")](navigation-bars-images/navbar03.png#lightbox)
1. 接下來，您可以將一個或多個**橫條按鈕專案**新增至橫條的任一個結尾：

    [![](navigation-bars-images/navbar04.png "A Bar Button Item")](navigation-bars-images/navbar04.png#lightbox)
1. 最後，在 [**屬性] Explorer**的 [**事件**] 索引標籤中，將 [**橫條] 按鈕專案**連接至 [動作]：

    [![](navigation-bars-images/navbar05.png "A Bar Button Item Action")](navigation-bars-images/navbar05.png#lightbox)
1. 儲存您的變更。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 [**方案總管**中，按兩下 [檔案]， `Main.storyboard` 然後開啟以進行編輯。
1. 從 [**工具箱**] 將**導覽**列拖放到畫面頂端的視圖上：

    [![](navigation-bars-images/navbar02-vs.png "A Navigation Bar")](navigation-bars-images/navbar02-vs.png#lightbox)
1. 按兩下**導覽**列以選取 [**流覽] 專案**。 在 [**屬性] Explorer**的 [ **Widget** ] 索引標籤中，您可以設定**標題**：

    [![](navigation-bars-images/navbar03-vs.png "Set the Title")](navigation-bars-images/navbar03-vs.png#lightbox)
1. 接下來，您可以將一個或多個**橫條按鈕專案**新增至橫條的任一個結尾：

    [![](navigation-bars-images/navbar04-vs.png "A Bar Button Items")](navigation-bars-images/navbar04-vs.png#lightbox)
1. 最後，在 [**屬性] Explorer**的 [**事件**] 索引標籤中，將 [**橫條] 按鈕專案**連接至 [動作]：

    [![](navigation-bars-images/navbar05-vs.png "A Bar Button Item Actions")](navigation-bars-images/navbar05-vs.png#lightbox)
1. 儲存您的變更。

-----

> [!IMPORTANT]
> 雖然您可以 `TouchUpInside` 在 IOS 設計工具中將事件（例如）指派給 UI 專案（例如 UIButton），但永遠不會呼叫它，因為 APPLE TV 沒有觸控式螢幕或支援觸控事件。 `Primary Action`建立 tvOS 使用者介面元素的事件處理常式時，您應該一律使用事件。

下列程式碼提供三種不同 BarButtonItems 上的事件處理常式範例： `ShowFirstHotel` 、 `ShowSecondHotel` 和 `ShowThirdHotel` 。 按一下每個專案時， `HotelImage` 就會變更背景影像。 這會在 View Controller （範例）檔案中進行編輯 `ViewController.cs` ：

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

只要按鈕的 `Enabled` 屬性為 `true` ，而且它不是由另一個控制項或視圖所涵蓋，就可以使用 Siri 遠端，將它設為焦點專案。

如需使用分鏡腳本的詳細資訊，請參閱我們的[Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Summary"></a>

## <a name="summary"></a>總結

本文涵蓋在 tvOS 應用程式內設計和使用導覽列。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
