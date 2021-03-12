---
title: 在 Xamarin 中使用 tvOS 分割視圖控制器
description: 本檔說明如何在以 Xamarin 建立的應用程式中使用 tvOS 分割視圖。 它提供分割視圖控制器、如何搭配分鏡腳本使用、存取主要和詳細資料檢視，以及顯示和隱藏主視圖的概要說明。
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: cc7b61d4a02bf2b0dc64e7ebc3501a45832f5fa3
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603083"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>在 Xamarin 中使用 tvOS 分割視圖控制器
> [!WARNING]
> IOS 設計工具在 Visual Studio 2019 16.8 版和 Visual Studio 2019 for Mac 版本8.8 中已淘汰，並已在 Visual Studio 2019 版本16.9 和 Visual Studio for Mac 版本8.9 中移除。
> 建立 iOS 使用者介面的建議方式是直接在執行 Xcode 介面產生器的 Mac 上。 如需詳細資訊，請參閱 [使用 Xcode 設計使用者介面](~/ios/user-interface/storyboards/index.md)。 

分割視圖控制器會同時在螢幕上並排顯示和管理主要和詳細資料檢視控制器。 分割視圖控制器可用來在主版視圖中呈現持續性、可設定焦點的內容 (左邊) 的較社區段，以及詳細資料檢視中的相關詳細資料 (右邊) 較大的區段。

[![範例分割視圖](split-views-images/intro01.png)](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers"></a>

## <a name="about-split-view-controllers"></a>關於分割視圖控制器

如上所述，「分割視圖控制器」會管理並排顯示的主要和詳細資料查看控制器，主要畫面是左邊較小的視圖，右邊的詳細資料則詳細。 

此外，您可以視需要隱藏或顯示主 View 控制器： 

[![主 View 控制器已隱藏](split-views-images/intro02.png)](split-views-images/intro02.png#lightbox)

分割視圖控制器通常是用來呈現可篩選內容的清單，並在 [詳細資料] 視圖中顯示 [主視圖] 和 [篩選的結果] 中的分類。 這通常會顯示為左側的表格視圖，以及右邊的 [集合視圖](~/ios/tvos/user-interface/collection-views.md) 。

當您設計需要分割視圖控制器的使用者介面時，Apple 建議使用主要和詳細資料檢視控制器，這些控制器不會 (只變更內容變更，而不會變更) 結構。 如果您需要交換視圖控制器，最好使用流覽控制器作為 View Controller 的基底，此控制器需要變更 (Master 或詳細) 。

Apple 針對使用分割視圖控制器有下列建議：

- **使用正確的分割百分比** -根據預設，「分割視圖控制器」會使用主視圖控制器的第三個畫面，而針對「詳細資料檢視控制器」使用三分之二。 （選擇性）您可以使用50/50 分割。 選擇正確的百分比，讓您的內容在畫面上顯示平衡。
- **保存主要選取範圍** -雖然詳細資料檢視上的內容可以變更為回應使用者在主視圖中的選取專案，但主視圖內容應該是固定的。 此外，您應該清楚地在主版視圖中顯示目前選取的專案。
- **使用單一、統一的標題** -一般而言，您會想要在詳細資料檢視中使用單一的中央標題，而不是詳細資料和主視圖中的標題。

<a name="Split-View-Controllers-and-Storyboards"></a>

## <a name="split-view-controllers-and-storyboards"></a>分割視圖控制器和分鏡腳本

在 tvOS 應用程式中使用分割視圖控制器最簡單的方式，就是使用 iOS 設計工具將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **Solution Pad** 中，按兩下該檔案 `Main.storyboard` ，然後開啟它進行編輯。
1. 從 [**工具箱**] 拖曳 [**分割視圖控制器**]，並將它放在視圖上： 

    [![分割視圖控制器](split-views-images/activity01.png)](split-views-images/activity01.png#lightbox)
1. 根據預設，iOS 設計工具會在主版視圖中安裝流覽控制器和 View Controller。 如果這不符合您應用程式的需求，請直接刪除它們。
1. 如果您移除預設的主版視圖，請將新的視圖控制器拖曳至設計介面： 

    [![視圖控制器](split-views-images/activity02.png)](split-views-images/activity02.png#lightbox)
1. Control-Click 並從 [分割視圖控制器] 拖曳至新的主視圖控制器。 
1. 從 **快顯功能表** 選取 [ **Master** ]： 

    [![從快顯功能表選取 [Master]](split-views-images/activity03.png)](split-views-images/activity03.png#lightbox)
1. 設計主要和詳細資料檢視的內容： 

    [![範例版面配置](split-views-images/activity04.png)](split-views-images/activity04.png#lightbox)
1. 在 [**屬性] 面板** 的 [ **Widget]** 索引標籤中指派 **名稱**，以使用 c # 程式碼中的 UI 控制項。
1. 儲存您的變更，並返回 Visual Studio for Mac。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 [ **方案瀏覽器**] 中，按兩下該檔案 `Main.storyboard` ，然後開啟它進行編輯。
1. 從 [**工具箱**] 拖曳 [**分割視圖控制器**]，並將它放在視圖上： 

    [![分割視圖控制器](split-views-images/activity01-vs.png)](split-views-images/activity01-vs.png#lightbox)
1. 根據預設，iOS 設計工具會在主版視圖中新增流覽控制器和查看控制器。 如果這不符合您應用程式的需求，請直接刪除它們。
1. 如果您移除預設的主版視圖，請將新的視圖控制器拖曳至設計介面： 

    [![視圖控制器](split-views-images/activity02-vs.png)](split-views-images/activity02-vs.png#lightbox)
1. Control-Click 並從 [分割視圖控制器] 拖曳至新的主視圖控制器。 
1. 從 **快顯功能表** 選取 [ **Master** ]： 

    [![從快顯功能表選取 [Master]](split-views-images/activity03-vs.png)](split-views-images/activity03-vs.png#lightbox)
1. 設計主要和詳細資料檢視的內容： 

    [![內容版面配置](split-views-images/activity04.png)](split-views-images/activity04.png#lightbox)
1. 在 [**屬性瀏覽器**] 的 [ **Widget]** 索引標籤中指派 **名稱**，以使用 c # 程式碼中的 UI 控制項。
1. 儲存您的變更。

-----

如需使用分鏡腳本的詳細資訊，請參閱我們的 [Hello，TvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Working-with-Split-View-Controllers"></a>

## <a name="working-with-split-view-controllers"></a>使用分割視圖控制器

如上所述，當您向使用者顯示已篩選的內容時，通常會使用「分割視圖控制器」。 主要的類別會顯示在主視圖的左側，而詳細資料檢視會根據使用者的選取專案，在右邊顯示篩選的結果。

<a name="Accessing-Master-and-Detail"></a>

### <a name="accessing-master-and-detail"></a>存取主要和詳細資料

如果您需要以程式設計方式存取主要和詳細資料檢視控制器，請使用 `ViewControllers` 分割視圖控制器的屬性。 例如：

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

它會顯示為數組，其中第一個元素 (0) 在主視圖控制器中，第二個元素 (1) 是詳細資料。

<a name="Accessing-Detail-from-Master"></a>

### <a name="accessing-detail-from-master"></a>從 Master 存取詳細資料

因為您通常會根據使用者在主版中選取的專案，在詳細資料檢視中顯示詳細資訊，所以您需要一種方式來從主伺服器存取詳細資料。

若要這麼做，最簡單的方式就是在主 View 控制器類別上公開屬性，例如：

```csharp
public DetailViewController DetailController { get; set;}
```

在分割視圖控制器中，覆寫 `ViewDidLoad` 方法，並將兩個視圖結合在一起。 例如：

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

您可以在詳細資料查看控制器上公開屬性和方法，以供主控制項在必要時用來呈現新的資料。

<a name="Showing-and-Hiding-Master"></a>

### <a name="showing-and-hiding-master"></a>顯示和隱藏 Master

（選擇性）您可以使用「分割視圖控制器」的屬性來顯示和隱藏主 View 控制器 `PreferredDisplayMode` 。 例如：

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

`UISplitViewControllerDisplayMode`列舉會定義如何以下列其中一種方式呈現主視圖控制器：

- **自動** tvOS 將會控制主要和詳細資料檢視的呈現方式。
- **PrimaryHidden** -這會隱藏主 View 控制器。
- **AllVisible** -並排顯示主要和詳細資料檢視控制器。 這是一般預設呈現方式。
- **PrimaryOverlay** -詳細資料檢視控制器會在底下延伸，並由主要主機涵蓋。

若要取得目前的呈現狀態，請使用「 `DisplayMode` 分割視圖控制器」的屬性。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了如何設計和使用 tvOS 應用程式內的分割視圖控制器。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)