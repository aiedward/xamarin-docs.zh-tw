---
title: 使用 tvOS 在 Xamarin 中的分割檢視控制器
description: 本文件說明如何使用分割檢視中使用 Xamarin 建置的應用程式的 tvOS。 它提供的高階概觀，分割檢視控制器、 如何使用分鏡腳本，存取 [主要和詳細資料] 檢視中，並顯示和隱藏主版檢視。
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 9f1bd48378faa9ae6a4853083c93377268c38f01
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61374474"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>使用 tvOS 在 Xamarin 中的分割檢視控制器

分割檢視控制器提供和管理主要和詳細資料檢視控制器的並存，同時在螢幕上。 分割檢視控制器是用來呈現主版檢視 （左側較小的區段） 中的永續性且可焦點化的內容和相關詳細資料檢視 （在右側較大的區段） 中的詳細資料。

[![](split-views-images/intro01.png "範例分割檢視")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>關於分割檢視控制器

如上所述，分割檢視控制器會管理主要和詳細資料會顯示並存，含主版頁面要縮小檢視，在左邊，右邊較大的詳細資料的檢視控制器。 

此外，主版檢視控制器可以已隱藏或顯示視需要而定： 

[![](split-views-images/intro02.png "隱藏主版檢視控制器")](split-views-images/intro02.png#lightbox)

分割檢視控制器通常用於呈現一份可篩選的內容，在主要檢視類別與詳細資料檢視中篩選的結果。 這通常會顯示成資料表檢視，在左側，而[集合檢視](~/ios/tvos/user-interface/collection-views.md)右邊。

在設計時需要分割檢視控制器的使用者介面，Apple 建議使用 Master 和詳細資料檢視控制器不會變更 （只有在內容變更，不是結構）。 如果您需要交換出檢視控制器，最好是使用瀏覽控制器需要變更 （主要或詳細資料） 的檢視控制器的基底。

Apple 已使用分割檢視控制器的下列建議：

- **使用正確的分割百分比**-根據預設分割檢視控制器會使用主版檢視控制器的畫面的三分之一和三分之二的詳細資料檢視控制器。 （選擇性） 您可以使用 50/50 的分割。 選擇正確的百分比，以讓您平衡在畫面上的內容會顯示。
- **保存主要選取項目**-內容時，詳細資料檢視會變更回應使用者的選取項目，在 [主機] 檢視中，主版檢視的內容應該加以修正。 此外，您應該清楚地顯示目前選取的項目在 [主機] 檢視中。
- **使用單一、 統一的標題**-一般而言，您會想要使用單一、 置中的標題，在詳細資料檢視中，而不是在詳細資料和主版檢視的標題。

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>分割檢視控制器和分鏡腳本

Xamarin.tvOS 應用程式中使用分割檢視控制器的最簡單方式是將它們新增至使用 iOS 設計工具的應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在  **Solution Pad**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**分割檢視控制器**從**工具箱**並將它放在檢視上： 

    [![](split-views-images/activity01.png "分割檢視控制器")](split-views-images/activity01.png#lightbox)
1. 根據預設，iOS 設計工具會安裝瀏覽控制器和檢視控制器在主版檢視。 如果這不符您的應用程式需求，只要刪除它們。
1. 如果您移除預設的主要檢視中，將新的檢視控制器拖曳至設計介面： 

    [![](split-views-images/activity02.png "檢視控制器")](split-views-images/activity02.png#lightbox)
1. Control + 按一下，並從分割檢視控制器拖曳至新的主要檢視控制器。 
1. 選取 **母片**從**快顯功能表**: 

    [![](split-views-images/activity03.png "從快顯功能表中選取主要")](split-views-images/activity03.png#lightbox)
1. 設計您的主要和詳細資料檢視的內容： 

    [![](split-views-images/activity04.png "範例版面配置")](split-views-images/activity04.png#lightbox)
1. 指派**名稱**中**小工具 索引標籤**的**Properties Pad**來使用您的 UI 控制項，在C#的程式碼。
1. 儲存變更並返回 Visual Studio for mac。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**分割檢視控制器**從**工具箱**並將它放在檢視上： 

    [![](split-views-images/activity01-vs.png "分割檢視控制器")](split-views-images/activity01-vs.png#lightbox)
1. 根據預設，iOS 設計工具會將瀏覽控制器和檢視控制器中的主版檢視。 如果這不符您的應用程式需求，只要刪除它們。
1. 如果您移除預設的主要檢視中，將新的檢視控制器拖曳至設計介面： 

    [![](split-views-images/activity02-vs.png "檢視控制器")](split-views-images/activity02-vs.png#lightbox)
1. Control + 按一下，並從分割檢視控制器拖曳至新的主要檢視控制器。 
1. 選取 **母片**從**快顯功能表**: 

    [![](split-views-images/activity03-vs.png "從快顯功能表中選取主要")](split-views-images/activity03-vs.png#lightbox)
1. 設計您的主要和詳細資料檢視的內容： 

    [![](split-views-images/activity04.png "內容的配置")](split-views-images/activity04.png#lightbox)
1. 指派**名稱**中**小工具 索引標籤**的**屬性總管**來使用您的 UI 控制項，在C#的程式碼。
1. 儲存您的變更。
    
-----

如需有關使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>使用 分割檢視控制器

如上所述，分割檢視控制器通常會在情況下，您會向使用者顯示篩選過的內容。 主要的類別目錄會顯示在 [主機] 檢視中，在左邊和右邊詳細資料檢視中篩選的結果取決於使用者的選擇。

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>存取主版和詳細資料

如果您需要以程式設計方式存取的主要和詳細資料檢視控制器，使用`ViewControllers `分割檢視控制器的屬性。 例如：

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

它會顯示為陣列，其中第一個項目 (0)，在主版檢視控制器和 (1) 的第二個項目是詳細資料。

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>從主機存取的詳細資料

因為您通常會根據使用者的選取項目在 Master 中的詳細資料檢視中顯示的詳細的資訊，您必須從主要存取詳細資料的方法。

若要這樣做最簡單的方法是公開屬性，以在主版檢視控制器類別，例如：

```csharp
public DetailViewController DetailController { get; set;}
```

在分割檢視控制器中，覆寫`ViewDidLoad`方法，並繫結的兩個檢視在一起。 例如：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Gain access to master and detail view controllers
    var masterController = ViewControllers [0] as MasterViewController;
    var detailController = ViewControllers [1] as DetailViewController;

    // Wire-up views
    masterController.SplitViewController = this;
    masterController.DetailController = detailController;
    detailController.SplitViewController = this;
}
```

您可以在主要可用來呈現所需的新資料的詳細資料檢視控制器上公開屬性和方法。

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>顯示和隱藏主要

（選擇性） 您可以在其中顯示和隱藏主版檢視控制器使用`PreferredDisplayMode`分割檢視控制器的屬性。 例如：

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

`UISplitViewControllerDisplayMode`列舉會定義主版檢視控制器為下列其中之一的呈現方式：

- **自動**-tvOS 會控制主要和詳細資料檢視的呈現方式。
- **PrimaryHidden** -這可能會隱藏主版檢視控制器。
- **AllVisible** -會顯示主要和詳細資料檢視控制器的並存。 這是正常現象的預設呈現。
- **PrimaryOverlay** -詳細資料檢視控制器，擴充，並涵蓋主機。

若要取得目前的簡報狀態，請使用`DisplayMode`分割檢視控制器的屬性。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計與 Xamarin.tvOS 應用程式內使用分割檢視控制器。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
