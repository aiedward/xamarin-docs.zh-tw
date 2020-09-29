---
title: 在 Xamarin 中使用 tvOS 巡覽列
description: 本檔說明如何在以 Xamarin 建立的 tvOS 應用程式中使用巡覽列。 它會討論如何在腳本中設定導覽列，以及如何回應這些按鈕的事件。
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 3ee159eaaf4a124f652b083e4dd7341909f52d82
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433125"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>在 Xamarin 中使用 tvOS 巡覽列

導覽列可以加入至 views 頂端，以顯示標題和選擇性導覽列按鈕。 通常會在使用者從主頁面（例如資料表視圖、集合或功能表）流覽至顯示所選取專案之詳細資料的子視圖時使用。

[![範例巡覽列](navigation-bars-images/navbar01.png)](navigation-bars-images/navbar01.png#lightbox)

除了中央) 所顯示的標題 (之外，導覽列也可以包含一或多個導覽列按鈕 (`UIBarButtonItem`) 在橫條的左邊和右邊。

> [!IMPORTANT]
> 依預設，巡覽列是透明的。 請小心確保導覽列的內容可透過其下的內容保持可讀取。 例如，當資料表視圖或集合中的內容在其下滾動時。

<a name="Navigation-Bars-and-Storyboards"></a>

## <a name="navigation-bars-and-storyboards"></a>巡覽列和分鏡腳本

在 tvOS 應用程式中使用導覽列最簡單的方式，就是使用 iOS 設計工具將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 [ **Solution Pad**中，按兩下 [檔案]， `Main.storyboard` 然後開啟它進行編輯。
1. 從 [**工具箱**] 拖曳**導覽**列，並將它放在畫面頂端的視圖上：

    [![巡覽列](navigation-bars-images/navbar02.png)](navigation-bars-images/navbar02.png#lightbox)
1. 按兩下 **導覽** 列，選取 **導覽專案**。 在**Properties Pad**的 [ **Widget** ] 索引標籤中，您可以設定**標題**：

    [![設定標題](navigation-bars-images/navbar03.png)](navigation-bars-images/navbar03.png#lightbox)
1. 接下來，您可以將一或多個 **條形按鈕專案** 加入至橫條的任一端：

    [![橫條按鈕專案](navigation-bars-images/navbar04.png)](navigation-bars-images/navbar04.png#lightbox)
1. 最後，在 [**屬性瀏覽器**] 的 [**事件**] 索引標籤中，將**橫條按鈕專案**連接到動作：

    [![橫條按鈕專案動作](navigation-bars-images/navbar05.png)](navigation-bars-images/navbar05.png#lightbox)
1. 儲存變更。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 [ **方案總管**中，按兩下 [檔案]， `Main.storyboard` 然後開啟它進行編輯。
1. 從 [**工具箱**] 拖曳**導覽**列，並將它放在畫面頂端的視圖上：

    [![巡覽列](navigation-bars-images/navbar02-vs.png)](navigation-bars-images/navbar02-vs.png#lightbox)
1. 按兩下 **導覽** 列，選取 **導覽專案**。 在 [**屬性瀏覽器**] 的 [ **Widget** ] 索引標籤中，您可以設定**標題**：

    [![設定標題](navigation-bars-images/navbar03-vs.png)](navigation-bars-images/navbar03-vs.png#lightbox)
1. 接下來，您可以將一或多個 **條形按鈕專案** 加入至橫條的任一端：

    [![橫條按鈕專案](navigation-bars-images/navbar04-vs.png)](navigation-bars-images/navbar04-vs.png#lightbox)
1. 最後，在 [**屬性瀏覽器**] 的 [**事件**] 索引標籤中，將**橫條按鈕專案**連接到動作：

    [![橫條按鈕專案動作](navigation-bars-images/navbar05-vs.png)](navigation-bars-images/navbar05-vs.png#lightbox)
1. 儲存變更。

-----

> [!IMPORTANT]
> 雖然可以將事件（例如 `TouchUpInside` UIButton) 在 IOS 設計工具中）指派給 UI (元素，但永遠不會呼叫它，因為 APPLE TV 沒有觸控式螢幕或支援觸控事件。 `Primary Action`建立 tvOS 使用者介面元素的事件處理常式時，您應該一律使用此事件。

下列程式碼提供三個不同 BarButtonItems 的事件處理常式範例： `ShowFirstHotel` 、 `ShowSecondHotel` 和 `ShowThirdHotel` 。 按一下每個專案時， `HotelImage` 會變更背景影像。 這會在 View Controller 中編輯 (範例 `ViewController.cs`) 檔：

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

只要按鈕的 `Enabled` 屬性為 `true` ，而且不是由另一個控制項或視圖所涵蓋，則可以使用 Siri 遠端來使其成為焦點專案。

如需使用分鏡腳本的詳細資訊，請參閱我們的 [Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了如何在 tvOS 應用程式內設計和使用導覽列。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)