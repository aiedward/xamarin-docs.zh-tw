---
title: 使用分割檢視控制器
description: 本文涵蓋設計和 Xamarin.tvOS 應用程式內使用分割檢視的控制器。
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 25151eb2929e2bc61dba27a9937ffdf4ee224626
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-split-view-controllers"></a>使用分割檢視控制器

_本文涵蓋設計和 Xamarin.tvOS 應用程式內使用分割檢視的控制器。_


分割檢視控制器呈現，並管理 Master 和詳細資料檢視控制器的並行，同時在螢幕上。 分割檢視的控制器是用來呈現主版檢視 （左邊的較小區段） 中的持續性、 可設定焦點的內容和相關的詳細資料檢視 （在右側較大的區段） 的詳細資料。

[![](split-views-images/intro01.png "分割檢視範例")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>分割檢視控制器的相關

如前所述，分割檢視控制器會管理 Master 和詳細資料檢視控制器提供並存，說明較小的檢視，在左邊，右邊較大的詳細資料。 

此外，將主版檢視控制器已隱藏或顯示視需要而定： 

[![](split-views-images/intro02.png "隱藏主版檢視控制器")](split-views-images/intro02.png#lightbox)

分割檢視控制器通常會使用主版檢視中的分類和篩選的結果的詳細資料檢視提供一份可篩選的內容。 這通常會顯示成資料表檢視的左側，而[集合檢視](~/ios/tvos/user-interface/collection-views.md)右邊。

在設計使用者介面，需要分割檢視控制站時，Apple 建議使用 Master 和詳細資料檢視控制站不會變更 （只有在內容變更，結構不）。 如果您需要交換逾時的檢視控制器，所以最好使用導覽控制站需要變更 （主要或詳細資料） 的檢視控制器的基底。

Apple 已分割檢視控制器所使用的下列建議：

- **使用正確的分割百分比**-根據預設分割檢視控制器使用三分之一之畫面的主版檢視控制器和三分之二詳細資料檢視控制站。 （選擇性） 您可以使用 50/50 分割。 選擇正確的百分比，讓您平衡螢幕上的內容會出現。
- **保存主要選取項目**-時內容詳細資料檢視可以變更回應使用者的主要檢視中的選取項目，必須先解決主版檢視內容。 此外，您應該在主版檢視清楚地顯示目前選取的項目。
- **使用單一、 統一的標題**-一般而言，您會想要使用單一、 置中的標題，在詳細資料檢視中，而不是在詳細資料和主版檢視的標題。

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>分割檢視控制器和分鏡腳本

Xamarin.tvOS 應用程式中使用分割檢視控制站的最簡單方式是將它們新增到應用程式的 UI 使用 iOS 設計工具。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在**方案板**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**分割檢視控制器**從**工具箱**並將它放在檢視上： 

    [![](split-views-images/activity01.png "分割檢視控制器")](split-views-images/activity01.png#lightbox)
1. 根據預設，iOS 設計工具會安裝瀏覽控制器和檢視控制器在主版檢視。 如果這不符合您的應用程式需求，只刪除它們。
1. 如果您沒有移除預設的主要檢視中，將新的檢視控制器拖曳至設計介面： 

    [![](split-views-images/activity02.png "檢視控制器")](split-views-images/activity02.png#lightbox)
1. 控制項按一下並拖曳至新的主要檢視控制器的分割檢視控制器。 
1. 選取**Master**從**快顯功能表**: 

    [![](split-views-images/activity03.png "從快顯功能表中選取主機")](split-views-images/activity03.png#lightbox)
1. 設計您的主要和詳細資料檢視的內容： 

    [![](split-views-images/activity04.png "版面配置範例")](split-views-images/activity04.png#lightbox)
1. 指派**名稱**中**Widget 索引標籤**的**屬性板**使用 C# 程式碼將 UI 控制項。
1. 儲存變更並返回 Visual Studio for mac。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在**方案總管 中**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**分割檢視控制器**從**工具箱**並將它放在檢視上： 

    [![](split-views-images/activity01-vs.png "分割檢視控制器")](split-views-images/activity01-vs.png#lightbox)
1. 根據預設，iOS 設計工具會將瀏覽控制器和檢視控制器中主版檢視。 如果這不符合您的應用程式需求，只刪除它們。
1. 如果您沒有移除預設的主要檢視中，將新的檢視控制器拖曳至設計介面： 

    [![](split-views-images/activity02-vs.png "檢視控制器")](split-views-images/activity02-vs.png#lightbox)
1. 控制項按一下並拖曳至新的主要檢視控制器的分割檢視控制器。 
1. 選取**Master**從**快顯功能表**: 

    [![](split-views-images/activity03-vs.png "從快顯功能表中選取主機")](split-views-images/activity03-vs.png#lightbox)
1. 設計您的主要和詳細資料檢視的內容： 

    [![](split-views-images/activity04.png "內容配置")](split-views-images/activity04.png#lightbox)
1. 指派**名稱**中**Widget 索引標籤**的**屬性總管**使用 C# 程式碼將 UI 控制項。
1. 儲存您的變更。
    
-----

如需使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>使用分割檢視控制器

如前所述，分割檢視控制器常用在情況下，您會向使用者顯示篩選過的內容。 主要的類別目錄會顯示在主版檢視中，左邊和右邊的詳細資料檢視篩選的結果會根據使用者的選取項目。

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>存取 Master 和詳細資料

如果您要以程式設計方式存取 Master 和詳細資料檢視的控制器，請使用`ViewControllers `分割檢視控制器的屬性。 例如: 

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

它會顯示成陣列，其中第一個項目 (0)，在主版檢視控制器和 (1) 的第二個項目是詳細資料。

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>從主機存取詳細資料

因為您通常要根據使用者的選擇主要詳細資料檢視中顯示的詳細的資訊，您必須從主機存取的詳細資料的方法。

若要這樣做最簡單的方式是公開屬性，以在主版檢視控制器類別，例如：

```csharp
public DetailViewController DetailController { get; set;}
```

分割檢視控制站，在覆寫`ViewDidLoad`方法，並繫結的兩個檢視在一起。 例如: 

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

您可以在主要可用來呈現所需的新資料的詳細資料檢視控制器上公開的屬性和方法。

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>顯示和隱藏 Master

（選擇性） 您可以在其中顯示和隱藏主版檢視控制器使用`PreferredDisplayMode`分割檢視控制器的屬性。 例如: 

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

`UISplitViewControllerDisplayMode`列舉會定義主機檢視控制站做為下列其中之一的呈現方式：

- **自動**-tvOS 會控制主要和詳細資料檢視的呈現方式。
- **PrimaryHidden** -這會隱藏主版檢視控制器。
- **AllVisible** -顯示主要和詳細資料檢視控制器-並存。 這是正常的預設呈現方式。
- **PrimaryOverlay** -詳細資料檢視控制器延伸，並涵蓋主機。

若要取得目前的簡報狀態，請使用`DisplayMode`分割檢視控制器的屬性。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和 Xamarin.tvOS 應用程式內使用分割檢視的控制器。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
