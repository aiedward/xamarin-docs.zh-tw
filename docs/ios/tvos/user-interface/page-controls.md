---
title: 使用 tvOS 在 Xamarin 中的頁面控制項
description: 本文件說明如何使用 Xamarin 建置的應用程式中的 tvOS 頁面控制項使用。 提供頁面控制項的高層級描述中，討論如何將它們安裝在分鏡腳本，並檢驗如何回應頁面變更事件。
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 173bc7713b5b8c330d4d4c5863bef24be8bdcb52
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61179551"
---
# <a name="working-with-tvos-page-controls-in-xamarin"></a>使用 tvOS 在 Xamarin 中的頁面控制項

有時候您可能需要在 Xamarin.tvOS 應用程式中顯示一系列的頁面或映像。 頁面控制項的設計是清楚地顯示哪些使用者會在超出最大頁數的頁面。 網頁控制項顯示一連串的點對暗色調，oval 形狀背景。 目前的頁面會顯示填色的點，所有其他頁面會顯示為空心的點。 網頁控制項將會裁剪外部大部分的點，如果有太多，無法納入其背景區域。

[![](page-controls-images/page01.png "範例頁面控制項")](page-controls-images/page01.png#lightbox)

頁面中的控制項設計來提供回饋給使用者只能的非互動式項目。 您必須新增其他控制項來變更目前的頁碼，（例如筆勢或按鈕）。

使用網頁控制項時，Apple 會有下列建議：

- **只有完整的集合上使用**-頁面控制項最適合在全螢幕環境中，以顯示存在於多個頁面單一集合中。
- **限制網頁數**-頁面控制項最適用於十 （10） 或更少的頁面，以及最多二十 （20） 頁面。 超過 20 種頁面，請考慮使用[集合檢視](~/ios/tvos/user-interface/collection-views.md)並顯示在方格中的頁面。

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>頁面控制項和分鏡腳本

Xamarin.tvOS 應用程式中使用頁面控制項的最簡單方式是將它們新增至使用 iOS 設計工具的應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

    
1. 在  **Solution Pad**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**網頁控制項**從**工具箱**並將它放在檢視上： 

    [![](page-controls-images/page02.png "頁面控制項")](page-controls-images/page02.png#lightbox)
1. 在**小工具 索引標籤**的**Properties Pad**，您可以調整頁面控制項的數個屬性，例如其**目前頁面**並 **# of 頁面**: 

    [![](page-controls-images/page03.png "小工具 索引標籤")](page-controls-images/page03.png#lightbox)
1. 接下來，將控制項或軌跡新增至要移向後和向前逐步執行的頁面集合的檢視。
1. 最後，將指派**名稱**控制項，讓您可以回應其C#程式碼。 例如:  

    [![](page-controls-images/page04.png "將控制項")](page-controls-images/page04.png#lightbox)
1. 儲存您的變更。
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. 在 **方案總管**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**網頁控制項**從**工具箱**並將它放在檢視上： 

    [![](page-controls-images/page02-vs.png "頁面控制項")](page-controls-images/page02-vs.png#lightbox)
1. 在**小工具 索引標籤**的**屬性總管**，您可以調整頁面控制項的數個屬性，例如其**目前頁面**並**的頁面數目**: 

    [![](page-controls-images/page03-vs.png "小工具 索引標籤")](page-controls-images/page03-vs.png#lightbox)
1. 接下來，將控制項或軌跡新增至要移向後和向前逐步執行的頁面集合的檢視。
1. 最後，將指派**名稱**控制項，讓您可以回應其C#程式碼。 例如： 

    [![](page-controls-images/page04-vs.png "將控制項")](page-controls-images/page04-vs.png#lightbox)
1. 儲存您的變更。
    

-----

> [!IMPORTANT]
> 雖然您可以指派事件，例如`TouchUpInside`UI 中的項目 （例如標記的 UIButton) iOS 設計工具，則會永遠不會呼叫因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 您應該一律使用`Primary Action`事件建立事件處理常式 tvOS 的使用者介面項目時。

編輯您的檢視控制器 (範例`ViewController.cs`) 檔案，並新增程式碼來處理變更的頁面。 例如：

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

讓我們看看頁面控制項的兩個屬性。 首先，若要指定的頁面數目上限，使用下列方法：

```csharp
PageView.Pages = 6;
```

若要變更目前的頁碼，請使用下列程式碼：

```csharp
PageView.CurrentPage = PageNumber;
```

`CurrentPage`屬性為零 (0) 為基礎，因此第一頁將會是零，因此最後一個 1 減的頁面數目上限。

如需有關使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計與 Xamarin.tvOS 應用程式內使用網頁控制項。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
