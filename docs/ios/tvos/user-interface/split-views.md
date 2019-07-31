---
title: 使用 Xamarin 中的 tvOS 分割視圖控制器
description: 本檔說明如何在以 Xamarin 建立的應用程式中使用 tvOS 分割 views。 其中提供分割視圖控制器的高階總覽、如何搭配使用分鏡腳本、存取主要和詳細資料檢視, 以及顯示和隱藏主要視圖。
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 52160d11c8c17a67a5db92e6a95f94815d54a0a6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648934"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>使用 Xamarin 中的 tvOS 分割視圖控制器

分割視圖控制器會同時在螢幕上並排顯示並管理主要和詳細資料檢視控制器。 分割視圖控制器是用來在主要視圖 (左邊較小的區段) 中呈現持續性的可設定焦點內容, 以及詳細資料檢視中的相關詳細資料 (右邊的較大區段)。

[![](split-views-images/intro01.png "範例分割視圖")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>關於分割視圖控制器

如上所述, 分割視圖控制器會管理並排顯示的主要和詳細資料檢視控制器, 主要是左邊較小的視圖, 而右邊的詳細資料則靠右。 

此外, 主 View 控制器可以隱藏或顯示為必要: 

[![](split-views-images/intro02.png "主要視圖控制器已隱藏")](split-views-images/intro02.png#lightbox)

分割視圖控制器通常用來呈現可篩選內容的清單, 其中的類別目錄位於主要視圖中, 而篩選後的結果則位於詳細資料檢視中。 這通常會顯示為左側的資料表視圖, 而右側則為[集合視圖](~/ios/tvos/user-interface/collection-views.md)。

在設計需要分割視圖控制器的使用者介面時, Apple 建議您使用不會變更的主要和詳細資料檢視控制器 (只有內容變更, 而不是結構)。 如果您需要交換視圖控制器, 最好使用流覽控制器作為需要變更的視圖控制器基礎 (主要或詳細資料)。

Apple 對於使用分割視圖控制器有下列建議:

- **使用正確的分割百分比**-根據預設, 分割視圖控制器會使用主要視圖控制器的第三個畫面, 以及三分之二的詳細資料檢視控制器。 (選擇性) 您可以使用50/50 分割。 選擇正確的百分比, 讓您的內容在畫面上顯示平衡。
- **保存主要選取範圍**: 當詳細資料檢視上的內容可以變更為主視圖中使用者選取的回應時, 主要視圖內容應該是固定的。 此外, 您應該清楚地在主視圖中顯示目前選取的專案。
- **使用單一、統一的標題**-一般而言, 您會想要在詳細資料檢視中使用單一的中央標題, 而不是詳細資料和主視圖中的標題。

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>分割視圖控制器和分鏡腳本

在 tvOS 應用程式中使用分割視圖控制器最簡單的方式, 就是使用 iOS 設計工具將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在  **Solution Pad**中, 按兩下`Main.storyboard`檔案, 然後開啟檔案進行編輯。
1. 從 [**工具箱**] 拖曳 [**分割視圖控制器**], 並將它放在視圖上: 

    [![](split-views-images/activity01.png "分割視圖控制器")](split-views-images/activity01.png#lightbox)
1. 根據預設, iOS 設計工具會在主要視圖中安裝流覽控制器和視圖控制器。 如果這不符合您應用程式的需求, 請直接刪除它們。
1. 如果您移除預設的主畫面, 請將新的 View Controller 拖曳至設計介面: 

    [![](split-views-images/activity02.png "視圖控制器")](split-views-images/activity02.png#lightbox)
1. 按一下 [分割視圖控制器], 並將它拖曳至新的主要視圖控制器。 
1. 從**快顯功能表**中選取 [ **Master** ]: 

    [![](split-views-images/activity03.png "從快顯功能表中選取 [Master]")](split-views-images/activity03.png#lightbox)
1. 設計主要和詳細資料檢視的內容: 

    [![](split-views-images/activity04.png "範例版面配置")](split-views-images/activity04.png#lightbox)
1. 在**Properties Pad**的 [ **Widget]** 索引標籤中指派**名稱**, 以在程式C#代碼中使用您的 UI 控制項。
1. 儲存您的變更, 並返回 Visual Studio for Mac。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管**中, 按兩下`Main.storyboard`檔案, 然後開啟檔案進行編輯。
1. 從 [**工具箱**] 拖曳 [**分割視圖控制器**], 並將它放在視圖上: 

    [![](split-views-images/activity01-vs.png "分割視圖控制器")](split-views-images/activity01-vs.png#lightbox)
1. 根據預設, iOS 設計工具會在主要視圖中新增 [流覽控制器] 和 [視圖控制器]。 如果這不符合您應用程式的需求, 請直接刪除它們。
1. 如果您移除預設的主畫面, 請將新的 View Controller 拖曳至設計介面: 

    [![](split-views-images/activity02-vs.png "視圖控制器")](split-views-images/activity02-vs.png#lightbox)
1. 按一下 [分割視圖控制器], 並將它拖曳至新的主要視圖控制器。 
1. 從**快顯功能表**中選取 [ **Master** ]: 

    [![](split-views-images/activity03-vs.png "從快顯功能表中選取 [Master]")](split-views-images/activity03-vs.png#lightbox)
1. 設計主要和詳細資料檢視的內容: 

    [![](split-views-images/activity04.png "內容版面配置")](split-views-images/activity04.png#lightbox)
1. 在 [**屬性] 瀏覽器**的 [ **Widget]** 索引標籤中指派**名稱**, C#以在程式碼中使用您的 UI 控制項。
1. 儲存您的變更。
    
-----

如需使用分鏡腳本的詳細資訊, 請參閱我們的[Hello, tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>使用分割視圖控制器

如上所述, 分割視圖控制器通常用於您向使用者顯示已篩選內容的情況。 主要的類別會顯示在主視圖的左側, 而詳細資料檢視中的篩選結果會根據使用者的選取專案。

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>存取主版和詳細資料

如果您需要以程式設計方式存取主要和詳細資料檢視控制器, `ViewControllers`請使用分割視圖控制器的屬性。 例如：

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

它會呈現為數組, 其中主要視圖控制器中的第一個元素 (0) 和第二個元素 (1) 是詳細資料。

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>從 Master 存取詳細資料

由於您通常會根據主伺服器中的使用者選取專案, 在詳細資料檢視中顯示詳細資訊, 因此您需要一種方式來存取主伺服器的詳細資料。

若要這麼做, 最簡單的方法是在您的主要 View Controller 類別上公開一個屬性, 例如:

```csharp
public DetailViewController DetailController { get; set;}
```

在分割視圖控制器中, 覆寫`ViewDidLoad`方法, 並將兩個視圖結合在一起。 例如：

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

您可以在詳細資料檢視控制器上公開屬性和方法, 主要可以視需要使用它來呈現新的資料。

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>顯示和隱藏主要

(選擇性) 您可以使用分割視圖控制器的`PreferredDisplayMode`屬性來顯示和隱藏主要視圖控制器。 例如：

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

`UISplitViewControllerDisplayMode`列舉會定義主視圖控制器如何呈現為下列其中一項:

- **自動**tvOS 會控制主要和詳細資料檢視的呈現方式。
- **PrimaryHidden** -這會隱藏主要視圖控制器。
- **AllVisible** -並排顯示主要和詳細資料檢視控制器。 這是一般的預設簡報。
- **PrimaryOverlay** -詳細資料檢視控制器會在底下延伸, 並由主要複本所涵蓋。

若要取得目前的呈現狀態, 請`DisplayMode`使用分割視圖控制器的屬性。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋設計和使用 tvOS 應用程式內的分割視圖控制器。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
