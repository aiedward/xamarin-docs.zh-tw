---
title: 在 Xamarin 中使用 tvOS 頁面控制項
description: 本檔說明如何在以 Xamarin 建立的應用程式中使用 tvOS 頁面控制項。 它提供頁面控制項的高階描述、討論如何在分鏡腳本中進行設定，並檢查如何回應頁面變更事件。
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 47e9c1ed4a043f7d11b5dfd52dca903e0a613b33
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436422"
---
# <a name="working-with-tvos-page-controls-in-xamarin"></a>在 Xamarin 中使用 tvOS 頁面控制項

有時您可能需要在 tvOS 應用程式中顯示一系列的頁面或影像。 頁面控制項的設計目的是要清楚顯示使用者的頁面數目上限。 頁面控制項會根據深色、oval 形的背景顯示一連串的點。 目前的頁面會顯示填滿的點，所有其他頁面會顯示為空心點。 如果有太多的背景區域無法容納，頁面控制項將會裁剪最外面的點。

[![範例頁面控制項](page-controls-images/page01.png)](page-controls-images/page01.png#lightbox)

非互動式專案中的頁面控制項，其設計目的是要將意見反應提供給使用者。 您將需要新增其他控制項來變更目前的頁碼 (例如) 的手勢或按鈕。

使用頁面控制項時，Apple 有下列建議：

- **僅在完整集合上使用** -頁面控制項最適合在全螢幕環境中使用，以顯示存在於單一集合中的多個頁面。
- **限制** 頁面控制項的數目最適合 10 (10) 或更少的頁面，以及最多20個 (20) 頁。 若是超過20個頁面，請考慮使用 [集合視圖](~/ios/tvos/user-interface/collection-views.md) ，並在方格中顯示頁面。

<a name="Page-Controls-and-Storyboards"></a>

## <a name="page-controls-and-storyboards"></a>頁面控制項和分鏡腳本

在 tvOS 應用程式中使用頁面控制項最簡單的方式，就是使用 iOS 設計工具將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 [ **Solution Pad**中，按兩下該檔案 `Main.storyboard` ，然後開啟它進行編輯。
1. 從 [**工具箱**] 拖曳**頁面控制項**，然後將它放在視圖上：

    [![頁面控制項](page-controls-images/page02.png)](page-controls-images/page02.png#lightbox)
1. 在**Properties Pad**的 [ **Widget]** 索引標籤中，您可以調整頁面控制項的數個屬性，例如其**目前的頁面**和**頁面**數目：

    [![[Widget] 索引標籤](page-controls-images/page03.png)](page-controls-images/page03.png#lightbox)
1. 接下來，將控制項或手勢新增至視圖，以在頁面的集合中向前及向後移動。
1. 最後，將 **名稱** 指派給控制項，讓您可以在 c # 程式碼中回應它們。 例如：

    [![為控制項命名](page-controls-images/page04.png)](page-controls-images/page04.png#lightbox)
1. 儲存變更。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 [ **方案總管**中，按兩下該檔案 `Main.storyboard` ，然後開啟它進行編輯。
1. 從 [**工具箱**] 拖曳**頁面控制項**，然後將它放在視圖上：

    [![頁面控制項](page-controls-images/page02-vs.png)](page-controls-images/page02-vs.png#lightbox)
1. 在 [**屬性] Explorer**的 [ **Widget]** 索引標籤中，您可以調整頁面控制項的數個屬性，例如其**目前的頁面**和**頁數**：

    [![[Widget] 索引標籤](page-controls-images/page03-vs.png)](page-controls-images/page03-vs.png#lightbox)
1. 接下來，將控制項或手勢新增至視圖，以在頁面的集合中向前及向後移動。
1. 最後，將 **名稱** 指派給控制項，讓您可以在 c # 程式碼中回應它們。 例如：

    [![為控制項命名](page-controls-images/page04-vs.png)](page-controls-images/page04-vs.png#lightbox)
1. 儲存變更。

-----

> [!IMPORTANT]
> 雖然可以將事件（例如 `TouchUpInside` UIButton) 在 IOS 設計工具中）指派給 UI (元素，但永遠不會呼叫它，因為 APPLE TV 沒有觸控式螢幕或支援觸控事件。 `Primary Action`建立 tvOS 使用者介面元素的事件處理常式時，您應該一律使用此事件。

編輯您的視圖控制器 (範例 `ViewController.cs`) 檔案，並新增程式碼來處理要變更的頁面。 例如：

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

讓我們仔細看看頁面控制項的兩個屬性。 首先，若要指定最大數目的頁面，請使用下列程式碼：

```csharp
PageView.Pages = 6;
```

若要變更目前的頁數，請使用下列程式碼：

```csharp
PageView.CurrentPage = PageNumber;
```

`CurrentPage`屬性為零 (0) 基礎，因此第一個頁面將會是零，而最後一個頁面會是減去最大頁數。

如需使用分鏡腳本的詳細資訊，請參閱我們的 [Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了設計和使用 tvOS 應用程式內的頁面控制項。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)