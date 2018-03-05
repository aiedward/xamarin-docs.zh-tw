---
title: "使用頁面控制項"
description: "本文涵蓋設計和 Xamarin.tvOS 應用程式內使用網頁控制項。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d4da50dac901628b9baf10a07650d232a977a653
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-page-control"></a>使用頁面控制項

_本文涵蓋設計和 Xamarin.tvOS 應用程式內使用網頁控制項。_

有時候您可能需要顯示 Xamarin.tvOS 應用程式中的一系列的網頁或影像。 分頁控制項的設計可以清楚地顯示哪些使用者會在超出最大頁數的頁面。 分頁控制項顯示一連串的點，針對在深色、 oval 形狀背景。 目前的頁面會顯示填滿的點中，所有其他頁面會顯示為空心的點。 如果有太多而無法容納它的背景區域網頁控制項將會裁剪外部大部分的點。

[ ![](page-controls-images/page01.png "範例頁面控制項")](page-controls-images/page01.png)

提供意見反應給僅限的使用者而設計的非互動式項目中分頁控制項。 您必須加入其他控制項，以變更 （例如筆勢或按鈕） 的目前頁碼。

使用網頁控制項時，Apple 有下列建議：

- **僅限 Full 集合上的使用**-頁面控制項最適合在全螢幕環境中，以顯示存在於多個頁面單一集合中。
- **限制網頁數**-頁面控制項最適用於 10 部或更少的頁面，以及最多 20 （20） 頁面。 二十個以上的頁面，請考慮使用[集合檢視](~/ios/tvos/user-interface/collection-views.md)並顯示在方格中的頁面。

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>頁面控制項和分鏡腳本

Xamarin.tvOS 應用程式中使用的頁面控制項的最簡單方式是將它們新增到應用程式的 UI 使用 iOS 設計工具。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

    
1. 在**方案板**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**網頁控制項**從**工具箱**並將它放在檢視上： 

    [ ![](page-controls-images/page02.png "頁面控制項")](page-controls-images/page02.png)
1. 在**Widget 索引標籤**的**屬性板**，您可以調整控制項的數個屬性，例如其**本頁**和**頁數**: 

    [ ![](page-controls-images/page03.png "[小工具] 索引標籤")](page-controls-images/page03.png)
1. 接下來，加入要移向後和向前逐步執行的頁面集合的檢視控制項或手勢。
1. 最後，指派**名稱**控制項，讓您可以在 C# 程式碼回應給他們。 例如:  

    [ ![](page-controls-images/page04.png "控制項名稱")](page-controls-images/page04.png)
1. 儲存您的變更。
    

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

    
1. 在**方案總管 中**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**網頁控制項**從**工具箱**並將它放在檢視上： 

    [ ![](page-controls-images/page02-vs.png "頁面控制項")](page-controls-images/page02-vs.png)
1. 在**Widget 索引標籤**的**屬性總管**，您可以調整控制項的數個屬性，例如其**本頁**和**頁數**: 

    [ ![](page-controls-images/page03-vs.png "[小工具] 索引標籤")](page-controls-images/page03-vs.png)
1. 接下來，加入要移向後和向前逐步執行的頁面集合的檢視控制項或手勢。
1. 最後，指派**名稱**控制項，讓您可以在 C# 程式碼回應給他們。 例如:  

    [ ![](page-controls-images/page04-vs.png "控制項名稱")](page-controls-images/page04-vs.png)
1. 儲存您的變更。
    

-----

> [!IMPORTANT]
> **注意：**雖然可以指派事件，例如`TouchUpInside`UI 中的項目 （例如 UIButton) iOS 設計工具，它會永遠不會呼叫因為 Apple TV 沒有觸控螢幕或支援觸控事件。 您應該一律使用`Primary Action`事件時建立事件處理常式 tvos 使用者介面項目。




編輯檢視控制器 (範例`ViewController.cs`) 檔案，然後加入程式碼來處理變更的頁面。 例如: 

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

讓我們探討兩個控制項的屬性。 首先，若要指定最大頁面數目，使用下列方法：

```csharp
PageView.Pages = 6;
```

若要變更目前的頁碼，請使用下列程式碼：

```csharp
PageView.CurrentPage = PageNumber;
```

`CurrentPage`屬性為基礎，因此第一頁會是 0，而且最後會是其中一個最大網頁數目減的零 (0)。

如需使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和 Xamarin.tvOS 應用程式內使用網頁控制項。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
