---
title: 在 Xamarin 中使用 tvOS 頁面控制項
description: 本檔說明如何在以 Xamarin 建立的應用程式中使用 tvOS 頁面控制項。 它提供頁面控制項的高階描述、討論如何在分鏡腳本中進行設定，以及檢查如何回應頁面變更事件。
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 12fe9645ab832db1db37e36b0342664bbd2fe9f8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030408"
---
# <a name="working-with-tvos-page-controls-in-xamarin"></a>在 Xamarin 中使用 tvOS 頁面控制項

有時候您可能需要在 tvOS 應用程式中顯示一系列的頁面或影像。 網頁控制項的設計，是為了清楚地顯示使用者已超出最大頁面數目的頁面。 頁面控制項會針對深色、橢圓形狀背景顯示一系列的點。 目前的頁面會顯示已填滿的點，而所有其他頁面則會顯示為空心點。 如果在其背景區域中有太多無法容納，頁面控制項將會裁剪外部最多點。

[![](page-controls-images/page01.png "Sample Page control")](page-controls-images/page01.png#lightbox)

非互動式元素中的頁面控制項，其設計目的是只為使用者提供意見反應。 您將需要新增其他控制項，以變更目前的頁碼（例如手勢或按鈕）。

使用頁面控制項時，Apple 具有下列建議：

- **僅適用于完整集合**-頁面控制項在全螢幕環境中最適合使用，以顯示存在於單一集合中的多個頁面。
- **限制頁面數目**-頁面控制項最適用于十（10）個或更少的頁面，最多可達二十（20）頁。 針對二十頁以上的頁面，請考慮使用[集合視圖](~/ios/tvos/user-interface/collection-views.md)，並在方格中顯示頁面。

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>頁面控制項和分鏡腳本

在 tvOS 應用程式中使用頁面控制項最簡單的方式，就是使用 iOS 設計工具將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在  **Solution Pad**中，按兩下 `Main.storyboard` 檔案，然後將它開啟以供編輯。
1. 從 [**工具箱**] 拖曳**頁面控制項**，並將它放在視圖上：

    [![](page-controls-images/page02.png "A Page Control")](page-controls-images/page02.png#lightbox)
1. 在**Properties Pad**的 [ **Widget]** 索引標籤中，您可以調整頁面控制項的數個屬性，例如其**目前頁面**和**頁數**：

    [![](page-controls-images/page03.png "The Widget Tab")](page-controls-images/page03.png#lightbox)
1. 接下來，將控制項或手勢加入至視圖，以在頁面集合中向後和向前移動。
1. 最後，將**名稱**指派給控制項，以便您可以在程式碼中C#對其進行回應。 例如:

    [![](page-controls-images/page04.png "Name the control")](page-controls-images/page04.png#lightbox)
1. 儲存您的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管**中，按兩下 `Main.storyboard` 檔案，然後將它開啟以供編輯。
1. 從 [**工具箱**] 拖曳**頁面控制項**，並將它放在視圖上：

    [![](page-controls-images/page02-vs.png "A Page Control")](page-controls-images/page02-vs.png#lightbox)
1. 在 [**屬性] Explorer**的 [ **Widget]** 索引標籤中，您可以調整頁面控制項的數個屬性，例如其**目前頁面**和**頁數**：

    [![](page-controls-images/page03-vs.png "The Widget tab")](page-controls-images/page03-vs.png#lightbox)
1. 接下來，將控制項或手勢加入至視圖，以在頁面集合中向後和向前移動。
1. 最後，將**名稱**指派給控制項，以便您可以在程式碼中C#對其進行回應。 例如:

    [![](page-controls-images/page04-vs.png "Name the control")](page-controls-images/page04-vs.png#lightbox)
1. 儲存您的變更。

-----

> [!IMPORTANT]
> 雖然您可以在 iOS 設計工具中將 `TouchUpInside` 之類的事件指派給 UI 專案（例如 UIButton），但永遠不會呼叫它，因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 建立 tvOS 使用者介面元素的事件處理常式時，您應該一律使用 `Primary Action` 事件。

編輯您的 View Controller （範例 `ViewController.cs`）檔案，並新增程式碼來處理所變更的頁面。 例如:

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public nint PageNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            PageView.Pages = 6;
            ShowCat ();
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void NextCat (UIBarButtonItem sender) {

            // Display next Cat
            if (++PageNumber > 5) {
                PageNumber = 5;
            }
            ShowCat();

        }

        partial void PreviousCat (UIBarButtonItem sender) {
            // Display previous cat
            if (--PageNumber < 0) {
                PageNumber = 0;
            }
            ShowCat();
        }
        #endregion

        #region Private Methods
        private void ShowCat() {

            // Adjust UI
            PreviousButton.Enabled = (PageNumber > 0);
            NextButton.Enabled = (PageNumber < 5);
            PageView.CurrentPage = PageNumber;

            // Display new cat
            CatView.Image = UIImage.FromFile(string.Format("Cat{0:00}.jpg",PageNumber+1));
        }
        #endregion
    }
}
```

讓我們仔細看一下頁面控制項的兩個屬性。 首先，若要指定最大頁面數目，請使用下列各項：

```csharp
PageView.Pages = 6;
```

若要變更目前的頁碼，請使用下列程式碼：

```csharp
PageView.CurrentPage = PageNumber;
```

`CurrentPage` 屬性是以零（0）為基礎，因此第一個頁面會是零，而最後一個則是最大頁數的減。

如需使用分鏡腳本的詳細資訊，請參閱我們的[Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋設計和使用 tvOS 應用程式內的頁面控制項。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
