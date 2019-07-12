---
title: 在 Xamarin.iOS 中的集合檢視
description: 集合檢視允許使用任意的版面配置來顯示的內容。 可輕鬆地建立內建的類似方格的版面配置，同時也支援自訂的版面配置。
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 890c11908b11b18d6ca626820f1a835d817870da
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829867"
---
# <a name="collection-views-in-xamarinios"></a>在 Xamarin.iOS 中的集合檢視

_集合檢視允許使用任意的版面配置來顯示的內容。可輕鬆地建立內建的類似方格的版面配置，同時也支援自訂的版面配置。_

中可用的集合檢視`UICollectionView`類別中，是在 iOS 6 中的新概念介紹使用版面配置在螢幕上呈現多個項目。 提供資料至模式`UICollectionView`建立項目，以及與這些相同的委派和資料來源模式的 iOS 開發中常用的項目後續互動。

不過，集合檢視使用無關的版面配置子系統`UICollectionView`本身。 因此，只要提供不同的版面配置，可以輕鬆地變更集合檢視的呈現方式。

iOS 提供呼叫的配置類別`UICollectionViewFlowLayout`，可讓以線條為基礎的配置，例如方格來建立任何額外的工作。 此外，自訂的版面配置也可以建立可讓您可以想像任何簡報。

## <a name="uicollectionview-basics"></a>UICollectionView 基本概念

`UICollectionView`類別由三個不同的項目所組成：

-  **資料格**– 資料驅動的檢視，每個項目
-  **增補的檢視**– 與區段相關聯的資料驅動型檢視。
-  **裝飾檢視**– 非資料驅動的版面配置所建立的檢視

## <a name="cells"></a>資料格

資料格是物件，表示集合檢視所顯示資料集中的單一項目。 每個資料格是的執行個體`UICollectionViewCell`類別，這由所組成的三個不同的檢視，如下圖所示：

 [![](uicollectionview-images/01-uicollectionviewcell.png "每個資料格組成三個不同的檢視，如下所示")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

`UICollectionViewCell`類別具有下列屬性的每個檢視：

-   `ContentView` – 此檢視會包含儲存格所顯示的內容。 它會轉譯成上層疊置順序在螢幕上。
-   `SelectedBackgroundView` -資料格具有內建支援的選取項目。 此檢視會用來以視覺方式表示 已選取的資料格。 呈現的正下方`ContentView`時選取的資料格。
-   `BackgroundView` -資料格也可以顯示的背景呈現由`BackgroundView`。 此檢視會呈現下方`SelectedBackgroundView`。


藉由設定`ContentView`使得小於`BackgroundView`並`SelectedBackgroundView`，則`BackgroundView`可用來以視覺方式來框住時內容，`SelectedBackgroundView`時將顯示為選取的資料格，如下所示：

 [![](uicollectionview-images/02-cells.png "不同的資料格元素")](uicollectionview-images/02-cells.png#lightbox)

上述螢幕擷取畫面中的資料格藉由繼承自`UICollectionViewCell`並設定`ContentView`，`SelectedBackgroundView`和`BackgroundView`屬性，分別，如下列程式碼所示：

```csharp
public class AnimalCell : UICollectionViewCell
{
        UIImageView imageView;

        [Export ("initWithFrame:")]
        public AnimalCell (CGRect frame) : base (frame)
        {
            BackgroundView = new UIView{BackgroundColor = UIColor.Orange};

            SelectedBackgroundView = new UIView{BackgroundColor = UIColor.Green};

            ContentView.Layer.BorderColor = UIColor.LightGray.CGColor;
            ContentView.Layer.BorderWidth = 2.0f;
            ContentView.BackgroundColor = UIColor.White;
            ContentView.Transform = CGAffineTransform.MakeScale (0.8f, 0.8f);

            imageView = new UIImageView (UIImage.FromBundle ("placeholder.png"));
            imageView.Center = ContentView.Center;
            imageView.Transform = CGAffineTransform.MakeScale (0.7f, 0.7f);

            ContentView.AddSubview (imageView);
        }

        public UIImage Image {
            set {
                imageView.Image = value;
            }
        }
}
```

 <a name="Supplementary_Views" />


## <a name="supplementary-views"></a>增補的檢視

增補的檢視是呈現資訊的每個區段相關聯的檢視`UICollectionView`。 資料格，例如增補的檢視會是資料驅動。 資料格會顯示從資料來源的項目資料，其中增補的檢視會呈現的區段資料，例如在書櫃的書籍的類別目錄] 或 [音樂媒體櫃中音樂的類型。

比方說，增補檢視可用來呈現特定區段中，標頭如下圖所示：

 [![](uicollectionview-images/02a-supplementary-view.png "增補的檢視用來呈現特定區段中，標頭如下所示")](uicollectionview-images/02a-supplementary-view.png#lightbox)

若要使用的增補的檢視，就必須先註冊在`ViewDidLoad`方法：

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

然後，使用傳回檢視需要`GetViewForSupplementaryElement`藉由建立`DequeueReusableSupplementaryView`，並繼承自`UICollectionReusableView`。 下列程式碼片段會產生 SupplementaryView 上方螢幕擷取畫面所示：

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

增補的檢視是更泛型比只頁首和頁尾。
它們可以放在集合檢視中的任何位置，且可以包含任何檢視，使其外觀完全自訂。

 <a name="Decoration_Views" />


## <a name="decoration-views"></a>裝飾檢視

裝飾檢視是可在中顯示的純粹視覺檢視`UICollectionView`。 不同於資料格和增補的檢視，它們不是資料導向。 它們一律會建立內配置的子類別，而且後續可以變更做為內容的配置。 比方說，裝飾檢視可用來呈現使用中的內容的背景檢視捲動`UICollectionView`，如下所示：

 [![](uicollectionview-images/02c-decoration-view.png "具有紅色背景的裝飾檢視")](uicollectionview-images/02c-decoration-view.png#lightbox)

 下列程式碼片段變成在範例中的紅色背景`CircleLayout`類別：

 ```csharp
 public class MyDecorationView : UICollectionReusableView
    {
        [Export ("initWithFrame:")]
        public MyDecorationView (CGRect frame) : base (frame)
        {
            BackgroundColor = UIColor.Red;
        }
    }
 ```


## <a name="data-source"></a>資料來源

如同其他部分的 iOS、 這類`UITableView`並`MKMapView`，`UICollectionView`取得其資料*資料來源*，公開於透過 Xamarin.iOS **`UICollectionViewDataSource`** 類別。 這個類別會負責提供內容`UICollectionView`例如：

-  **資料格**– 傳回`GetCell`方法。
-  **增補的檢視**– 傳回`GetViewForSupplementaryElement`方法。
-  **區段數目**– 傳回`NumberOfSections`方法。 預設值為 1，如果未實作。
-  **每個區段的項目數**– 傳回`GetItemsCount`方法。

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
為了方便起見，`UICollectionViewController`類別是可用。這會自動設定這兩個委派，這下一節所討論，以及資料來源及其`UICollectionView`檢視。

如同`UITableView`，則`UICollectionView`類別只會呼叫其資料來源，以取得儲存格是在螢幕上的項目。
捲動到螢幕外部的資料格位於到佇列，以便重複使用，如下圖所示：

 [![](uicollectionview-images/03-cell-reuse.png "捲動到螢幕外部的資料格會放在佇列，以供重複使用如下所示")](uicollectionview-images/03-cell-reuse.png#lightbox)

儲存格重複使用已經使用簡化`UICollectionView`和`UITableView`。 您不再需要直接在資料來源中建立一個儲存格，若其中一個不可用在重複使用的佇列中，因為資料格會向系統。 如果資料格沒有可用的儲存格，重複使用的佇列中清除佇列呼叫時，iOS 會自動根據型別或已註冊的 nib 予以建立。
相同的技巧也可供增補的檢視。

例如，請考慮下列程式碼會註冊`AnimalCell`類別：

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

當`UICollectionView`需要的資料格，因為其項目是在畫面上，`UICollectionView`呼叫它的資料來源`GetCell`方法。 類似於如何將使用 UITableView，這個方法會負責設定備份資料，這就是資料格`AnimalCell`類別在此情況下。

下列程式碼示範如何實作`GetCell`傳回`AnimalCell`執行個體：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, Foundation.NSIndexPath indexPath)
{
        var animalCell = (AnimalCell)collectionView.DequeueReusableCell (animalCellId, indexPath);

        var animal = animals [indexPath.Row];

        animalCell.Image = animal.Image;

        return animalCell;
}
```

若要在呼叫`DequeReusableCell`是在儲存格將會是從佇列中重複使用佇列，或者如果儲存格不是根據在呼叫中註冊的型別所建立的佇列中可用`CollectionView.RegisterClassForCell`。

在此情況下，藉由註冊`AnimalCell`類別，iOS 會建立新`AnimalCell`內部並將它傳回進行呼叫清除佇列的儲存格時之後, 已使用的影像，包含 animal 類別中，然後顯示回到`UICollectionView`.

 <a name="Delegate" />


### <a name="delegate"></a>Delegate - 委派

`UICollectionView`類別會使用型別的委派`UICollectionViewDelegate`來支援與內容互動`UICollectionView`。 這可讓您控制：

-  **資料格選取範圍**– 是否選取的資料格會決定。
-  **儲存格醒目提示**– 判斷是否儲存格目前所碰觸。
-  **資料格功能表**– 為長按筆勢的回應中的資料格顯示功能表。


使用資料來源，如`UICollectionViewController`設定預設採用的委派`UICollectionView`。

 <a name="Cell_HighLighting" />


#### <a name="cell-highlighting"></a>反白顯示的資料格

當儲存格已按下，資料格會轉換成反白顯示的狀態，且它將不會選取直到使用者會將他們的手指從儲存格。 這可以讓暫存的變更儲存格的外觀之前實際上已選取。 在選取之後，儲存格的`SelectedBackgroundView`隨即出現。 下圖顯示反白顯示的狀態，就會發生的選取項目之前：

 [![](uicollectionview-images/04-cell-highlight.png "選取項目，就會發生之前，此圖顯示反白顯示的狀態")](uicollectionview-images/04-cell-highlight.png#lightbox)

若要實作項目醒目提示`ItemHighlighted`並`ItemUnhighlighted`方法`UICollectionViewDelegate`可用。 例如，下列程式碼會套用的黃色背景`ContentView`時儲存格會反白顯示，和白色背景時未反白顯示，如上圖所示：

```csharp
public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.Yellow;
}

public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.White;
}
```

 <a name="Disabling_Selection" />


#### <a name="disabling-selection"></a>停用選取範圍

根據預設，在啟用選取`UICollectionView`。 若要停用選取範圍，請覆寫`ShouldHighlightItem`並傳回 false，如下所示：

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

停用反白顯示時，選取儲存格的程序也會停用。 此外，還有`ShouldSelectItem`雖然直接控制選取範圍的方法如果`ShouldHighlightItem`實作，並傳回 false，`ShouldSelectItem`就不會呼叫。

 `ShouldSelectItem` 允許選取的項目由項目為基礎，開啟或關閉時`ShouldHighlightItem`未實作。 它也可讓反白顯示，而不需要在選取的項目，如果`ShouldHighlightItem`實作，並傳回 true，同時`ShouldSelectItem`會傳回 false。

 <a name="Cell_Menus" />


#### <a name="cell-menus"></a>資料格功能表

在每個資料格`UICollectionView`能夠顯示功能表，讓剪下、 複製和貼上，選擇性地支援。 若要建立的儲存格上的 [編輯] 功能表：

1.  覆寫`ShouldShowMenu`並傳回 true，如果項目應該會顯示功能表。
1.  覆寫`CanPerformAction`並傳回可執行項目，每個動作，則為 true，將會任何剪下、 複製或貼。
1.  覆寫`PerformAction`進行編輯，複製貼上作業。


長按下儲存格時，下列螢幕擷取畫面會顯示功能表：

 [![](uicollectionview-images/04a-menu.png "長按下儲存格時，此螢幕擷取畫面會顯示功能表")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />


## <a name="layout"></a>配置

`UICollectionView` 支援的版面配置系統，可讓所有其項目，儲存格，增補檢視裝飾檢視受管理的獨立位置`UICollectionView`本身。
使用版面配置系統時，應用程式可支援類似方格的類似我們在本文中所見，以及提供自訂的版面配置的配置。

 <a name="Layout_Basics" />


### <a name="layout-basics"></a>版面配置的基本概念

中的版面配置`UICollectionView`繼承自類別中定義`UICollectionViewLayout`。 版面配置實作會負責建立每個項目中的版面配置屬性`UICollectionView`。 有兩種方式可建立的配置：

-  使用內建`UICollectionViewFlowLayout`。
-  提供自訂的版面配置，藉由繼承自`UICollectionViewLayout`。


 <a name="Flow_Layout" />


### <a name="flow-layout"></a>流程配置

`UICollectionViewFlowLayout`類別會提供適用於排列格線中的資料格的內容，如我們所見的列配置。

若要使用 流程版面配置：

-  建立的執行個體`UICollectionViewFlowLayout`:


```csharp
var layout = new UICollectionViewFlowLayout ();
```

-  傳遞給建構函式的執行個體`UICollectionView`:


```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

這是所有所需的版面配置方格中的內容。 此外，當方向變更時，`UICollectionViewFlowLayout`處理重新排列內容適當，如下所示：

 [![](uicollectionview-images/05-layout-orientation.png "方向變更的範例")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />


#### <a name="section-inset"></a>區段內凹

若要提供一些空間`UIContentView`，版面配置有`SectionInset`型別的屬性`UIEdgeInsets`。 例如，下列程式碼提供周圍的每個區段的 50 個像素緩衝區`UIContentView`時由配置`UICollectionViewFlowLayout`:

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

這會導致間距周圍的區段，如下所示：

 [![](uicollectionview-images/06-sectioninset.png "間距周圍的區段，如下所示")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />


#### <a name="subclassing-uicollectionviewflowlayout"></a>子類別化 UICollectionViewFlowLayout

若要使用的版本`UICollectionViewFlowLayout`直接，它可以也子類別化來進一步自訂內容沿著一條線的配置。 比方說，這可以用來建立配置，不會包裝到方格中，資料格，但改為以水平捲動效果，建立單一資料列，如下所示：

 [![](uicollectionview-images/07-line-layout.png "以水平捲動效果的單一資料列")](uicollectionview-images/07-line-layout.png#lightbox)

若要實作此子類別化`UICollectionViewFlowLayout`需要：

-  正在初始化本身的配置適用於任何配置屬性或建構函式中的配置中的所有項目。
-  覆寫`ShouldInvalidateLayoutForBoundsChange`，其中會傳回 true，當範圍`UICollectionView`儲存格的版面配置的變更，就會重新計算。 這用在此情況下轉換套用至 centermost 儲存格將會套用捲動期間，確保程式碼。
-  覆寫`TargetContentOffset`讓 centermost 資料格嵌入式管理單元的中央`UICollectionView`為捲動停駐點。
-  覆寫`LayoutAttributesForElementsInRect`傳回的陣列`UICollectionViewLayoutAttributes`。 每個`UICollectionViewLayoutAttribute`包含有關如何配置特定的項目，包括屬性，例如其`Center`， `Size` ，`ZIndex`和`Transform3D`。


下列程式碼顯示這類實作：

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;
using CoreGraphics;
using CoreAnimation;

namespace SimpleCollectionView
{
    public class LineLayout : UICollectionViewFlowLayout
    {
        public const float ITEM_SIZE = 200.0f;
        public const int ACTIVE_DISTANCE = 200;
        public const float ZOOM_FACTOR = 0.3f;

        public LineLayout ()
        {
            ItemSize = new CGSize (ITEM_SIZE, ITEM_SIZE);
            ScrollDirection = UICollectionViewScrollDirection.Horizontal;
            SectionInset = new UIEdgeInsets (400,0,400,0);
            MinimumLineSpacing = 50.0f;
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            return true;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var array = base.LayoutAttributesForElementsInRect (rect);
            var visibleRect = new CGRect (CollectionView.ContentOffset, CollectionView.Bounds.Size);

            foreach (var attributes in array) {
                if (attributes.Frame.IntersectsWith (rect)) {
                    float distance = (float)(visibleRect.GetMidX () - attributes.Center.X);
                    float normalizedDistance = distance / ACTIVE_DISTANCE;
                    if (Math.Abs (distance) < ACTIVE_DISTANCE) {
                        float zoom = 1 + ZOOM_FACTOR * (1 - Math.Abs (normalizedDistance));
                        attributes.Transform3D = CATransform3D.MakeScale (zoom, zoom, 1.0f);
                        attributes.ZIndex = 1;
                    }
                }
            }
            return array;
        }

        public override CGPoint TargetContentOffset (CGPoint proposedContentOffset, CGPoint scrollingVelocity)
        {
            float offSetAdjustment = float.MaxValue;
            float horizontalCenter = (float)(proposedContentOffset.X + (this.CollectionView.Bounds.Size.Width / 2.0));
            CGRect targetRect = new CGRect (proposedContentOffset.X, 0.0f, this.CollectionView.Bounds.Size.Width, this.CollectionView.Bounds.Size.Height);
            var array = base.LayoutAttributesForElementsInRect (targetRect);
            foreach (var layoutAttributes in array) {
                float itemHorizontalCenter = (float)layoutAttributes.Center.X;
                if (Math.Abs (itemHorizontalCenter - horizontalCenter) < Math.Abs (offSetAdjustment)) {
                    offSetAdjustment = itemHorizontalCenter - horizontalCenter;
                }
            }
            return new CGPoint (proposedContentOffset.X + offSetAdjustment, proposedContentOffset.Y);
        }

    }
}
```

 <a name="Custom_Layout" />


### <a name="custom-layout"></a>自訂版面配置

除了使用`UICollectionViewFlowLayout`，版面配置，也可以完全自訂藉由直接從繼承`UICollectionViewLayout`。

若要覆寫的主要方法如下：

-   `PrepareLayout` – 用來執行初始幾何計算將用於整個版面配置程序。
-   `CollectionViewContentSize` -傳回用來顯示內容區域的大小。
-   `LayoutAttributesForElementsInRect` – As UICollectionViewFlowLayout 範例稍早所示，這個方法用來提供資訊給`UICollectionView`有關如何配置每個項目。 不過，不同於`UICollectionViewFlowLayout`，在建立自訂的版面配置時，您就可以定位項目，不論您選擇。


例如，相同的內容會以循環配置中看到，如下所示：

 [![](uicollectionview-images/08-circle-layout.png "循環的自訂配置，如下所示")](uicollectionview-images/08-circle-layout.png#lightbox)

版面配置功能強大的事情是從類似方格的版面配置變更為水平捲動的配置，和接著這個循環配置需要只提供給版面配置類別`UICollectionView`變更。 在中為 nothing `UICollectionView`，它的委派或資料來源的程式碼變更完全。


## <a name="changes-in-ios-9"></a>在 iOS 9 中的變更


在 iOS 9、 集合檢視中 (`UICollectionView`) 現在支援拖放藉由新增新的預設筆勢辨識器和數個新的支援方法重新排列現成的項目。

使用這些新方法，您可以輕鬆地實作拖放以重新排列集合檢視中，並可以選擇自訂項目外觀的重新排序的程序任何階段。

[![](uicollectionview-images/intro01.png "重新排序的程序的範例")](uicollectionview-images/intro01.png#lightbox)

在本文中，我們將了解 Xamarin.iOS 應用程式，以及一些其他變更 iOS 9 對集合檢視控制項中實作拖曳來重新排序：

- [輕鬆重新排列項目](#Easy-Reordering-of-Items)
    - [重新排列的簡單範例](#Simple-Reordering-Example)
    - [使用自訂的筆勢辨識器](#Using-a-Custom-Gesture-Recognizer)
    - [自訂版面配置和重新排列](#Custom-Layouts-and-Reording)
- [集合檢視變更](#collection-view-changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>重新排列項目

如上所述，其中一個最重要的變更，以在 iOS 9 中的 [集合] 檢視會是現成的簡單的拖放以重新排列功能加入。

在 iOS 9 中，新增 重新排列到集合檢視的最快方式是使用`UICollectionViewController`。
集合檢視控制器現在已`InstallsStandardGestureForInteractiveMovement`屬性，加入標準*筆勢辨識器*支援拖曳來重新排列集合中的項目。
因為預設值是`true`，您只需要實作`MoveItem`方法`UICollectionViewDataSource`類別，以支援拖曳來重新排列。 例如：

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    // Reorder our list of items
    ...
}
```
<a name="Simple-Reordering-Example" />

### <a name="simple-reordering-example"></a>重新排列的簡單範例

快速的範例中，啟動 新的 Xamarin.iOS 專案，並編輯**Main.storyboard**檔案。 拖曳`UICollectionViewController`拖曳至設計介面：

[![](uicollectionview-images/quick01.png "新增 UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

選取 （它可能是最簡單的方式執行這項操作，從文件大綱） 的集合檢視。 在 [屬性] 面板的 [配置] 索引標籤中，設定下列的大小，，如以下螢幕擷取畫面所示：

- **儲存格大小**:寬度 – 60 |高度 – 60
- **標頭大小**:寬度 – 0 |高度 – 0
- **頁尾大小**:寬度 – 0 |高度 – 0
- **最小間距**:資料格 – 8 |線條 – 8
- **區段內凹**:上 – 16 |底部-16 |左 – 16 |權限 – 16

[![](uicollectionview-images/quick04.png "設定集合檢視大小")](uicollectionview-images/quick04.png#lightbox)

接著，編輯儲存格的預設值：
    - 變更為藍色背景色彩
    - 加入標籤以便做為儲存格的標題
    - 若要設定重複使用識別碼**資料格**

[![](uicollectionview-images/quick02.png "編輯儲存格的預設值")](uicollectionview-images/quick02.png#lightbox)

新增條件約束，以保留變更大小，在儲存格中置中標籤：

中**屬性 Pad**如_CollectionViewCell_並設定**類別**至`TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "設 TextCollectionViewCell 類別")](uicollectionview-images/quick05.png#lightbox)

設定**集合的可重複使用檢視**到`Cell`:

[![](uicollectionview-images/quick06.png "設定儲存格集合的可重複使用檢視")](uicollectionview-images/quick06.png#lightbox)

最後，選取標籤並將它命名`TextLabel`:

[![](uicollectionview-images/quick07.png "名稱標籤 TextLabel")](uicollectionview-images/quick07.png#lightbox)

編輯`TextCollectionViewCell`類別，並新增下列屬性。:

```csharp
using System;
using Foundation;
using UIKit;

namespace CollectionView
{
    public partial class TextCollectionViewCell : UICollectionViewCell
    {
        #region Computed Properties
        public string Title {
            get { return TextLabel.Text; }
            set { TextLabel.Text = value; }
        }
        #endregion

        #region Constructors
        public TextCollectionViewCell (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

這裡`Text`以便設定從程式碼，將會公開為資料格，標題的標籤屬性。

新增C#類別至專案，並稱之為`WaterfallCollectionSource`。 編輯檔案，並讓它看起來如下：

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionSource : UICollectionViewDataSource
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        public List<int> Numbers { get; set; } = new List<int> ();
        #endregion

        #region Constructors
        public WaterfallCollectionSource (WaterfallCollectionView collectionView)
        {
            // Initialize
            CollectionView = collectionView;

            // Init numbers collection
            for (int n = 0; n < 100; ++n) {
                Numbers.Add (n);
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView) {
            // We only have one section
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section) {
            // Return the number of items
            return Numbers.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a reusable cell and set {~~it's~>its~~} title from the item
            var cell = collectionView.DequeueReusableCell ("Cell", indexPath) as TextCollectionViewCell;
            cell.Title = Numbers [(int)indexPath.Item].ToString();

            return cell;
        }

        public override bool CanMoveItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // We can always move items
            return true;
        }

        public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
        {
            // Reorder our list of items
            var item = Numbers [(int)sourceIndexPath.Item];
            Numbers.RemoveAt ((int)sourceIndexPath.Item);
            Numbers.Insert ((int)destinationIndexPath.Item, item);
        }
        #endregion
    }
}
```

這個類別會是我們的集合檢視的資料來源，並提供集合中的每個資料格的資訊。
請注意，`MoveItem`實作方法，以允許在集合中的項目拖曳重新排列。

新增另一個新的C#類別至專案，並稱之為`WaterfallCollectionDelegate`。 編輯此檔案，並讓它看起來如下：

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionDelegate : UICollectionViewDelegate
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        #endregion

        #region Constructors
        public WaterfallCollectionDelegate (WaterfallCollectionView collectionView)
        {

            // Initialize
            CollectionView = collectionView;

        }
        #endregion

        #region Overrides Methods
        public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // Always allow for highlighting
            return true;
        }

        public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and change to green background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(183,208,57);
        }

        public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and return to blue background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(164,205,255);
        }
        #endregion
    }
}
```

這將作為我們的集合檢視的委派。 當使用者與它互動集合檢視中，反白顯示儲存格已覆寫方法。

要新增最後C#類別至專案，並稱之為`WaterfallCollectionView`。 編輯此檔案，並讓它看起來如下：

```csharp
using System;
using UIKit;
using System.Collections.Generic;
using Foundation;

namespace CollectionView
{
    [Register("WaterfallCollectionView")]
    public class WaterfallCollectionView : UICollectionView
    {

        #region Constructors
        public WaterfallCollectionView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Initialize
            DataSource = new WaterfallCollectionSource(this);
            Delegate = new WaterfallCollectionDelegate(this);

        }
        #endregion
    }
}
```

請注意，`DataSource`並`Delegate`上述建立集合檢視建構自其分鏡腳本時所設定 (或 **.xib**檔案)。

編輯**Main.storyboard**再次檔案並選取 [集合] 檢視，然後切換至**屬性**。 設定**類別**自訂`WaterfallCollectionView`上面定義的類別：

儲存您的 ui 所做的變更，並執行應用程式。
如果使用者從清單中選取項目，並將它拖曳至新位置，其他項目會以動畫顯示自動的方式移動的項目。
當使用者在新位置放開項時，它會留在那裡至該位置。 例如：

[![](uicollectionview-images/intro01.png "舉例來說，將項目拖曳至新位置")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>使用自訂的筆勢辨識器

在無法使用的情況下`UICollectionViewController`，而且必須使用一般`UIViewController`，或者如果您想要進一步控制拖放手勢，您可以建立您自己自訂的筆勢辨識器，並將它新增至集合檢視，檢視載入時。 例如：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Create a custom gesture recognizer
    var longPressGesture = new UILongPressGestureRecognizer ((gesture) => {

        // Take action based on state
        switch(gesture.State) {
        case UIGestureRecognizerState.Began:
            var selectedIndexPath = CollectionView.IndexPathForItemAtPoint(gesture.LocationInView(View));
            if (selectedIndexPath !=null) {
                CollectionView.BeginInteractiveMovementForItem(selectedIndexPath);
            }
            break;
        case UIGestureRecognizerState.Changed:
            CollectionView.UpdateInteractiveMovementTargetPosition(gesture.LocationInView(View));
            break;
        case UIGestureRecognizerState.Ended:
            CollectionView.EndInteractiveMovement();
            break;
        default:
            CollectionView.CancelInteractiveMovement();
            break;
        }

    });

    // Add the custom recognizer to the collection view
    CollectionView.AddGestureRecognizer(longPressGesture);
}
```

這裡我們使用新增至集合檢視中的數個新方法來實作，並控制在拖曳作業：

- `BeginInteractiveMovementForItem` -將標記在移動操作開始。
- `UpdateInteractiveMovementTargetPosition` -這是傳送更新項目的位置時。
- `EndInteractiveMovement` -標示項目移動的結尾。
- `CancelInteractiveMovement` -標記為使用者取消移動作業。

執行應用程式時，在拖曳作業運作方式，完全如同預設拖曳筆勢辨識器所隨附的集合檢視。

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>自訂版面配置和重新排列

在 iOS 9 中，已新增數個新方法來使用集合檢視中拖曳來重新排列和自訂版面配置。 若要探索這項功能，讓我們加入至集合的自訂版面配置。

首先，新增C#類別，稱為`WaterfallCollectionLayout`專案。 編輯它，並讓它看起來如下：

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;
using CoreGraphics;

namespace CollectionView
{
    [Register("WaterfallCollectionLayout")]
    public class WaterfallCollectionLayout : UICollectionViewLayout
    {
        #region Private Variables
        private int columnCount = 2;
        private nfloat minimumColumnSpacing = 10;
        private nfloat minimumInterItemSpacing = 10;
        private nfloat headerHeight = 0.0f;
        private nfloat footerHeight = 0.0f;
        private UIEdgeInsets sectionInset = new UIEdgeInsets(0, 0, 0, 0);
        private WaterfallCollectionRenderDirection itemRenderDirection = WaterfallCollectionRenderDirection.ShortestFirst;
        private Dictionary<nint,UICollectionViewLayoutAttributes> headersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private Dictionary<nint,UICollectionViewLayoutAttributes> footersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private List<CGRect> unionRects = new List<CGRect>();
        private List<nfloat> columnHeights = new List<nfloat>();
        private List<UICollectionViewLayoutAttributes> allItemAttributes = new List<UICollectionViewLayoutAttributes>();
        private List<List<UICollectionViewLayoutAttributes>> sectionItemAttributes = new List<List<UICollectionViewLayoutAttributes>>();
        private nfloat unionSize = 20;
        #endregion

        #region Computed Properties
        [Export("ColumnCount")]
        public int ColumnCount {
            get { return columnCount; }
            set {
                WillChangeValue ("ColumnCount");
                columnCount = value;
                DidChangeValue ("ColumnCount");

                InvalidateLayout ();
            }
        }

        [Export("MinimumColumnSpacing")]
        public nfloat MinimumColumnSpacing {
            get { return minimumColumnSpacing; }
            set {
                WillChangeValue ("MinimumColumnSpacing");
                minimumColumnSpacing = value;
                DidChangeValue ("MinimumColumnSpacing");

                InvalidateLayout ();
            }
        }

        [Export("MinimumInterItemSpacing")]
        public nfloat MinimumInterItemSpacing {
            get { return minimumInterItemSpacing; }
            set {
                WillChangeValue ("MinimumInterItemSpacing");
                minimumInterItemSpacing = value;
                DidChangeValue ("MinimumInterItemSpacing");

                InvalidateLayout ();
            }
        }

        [Export("HeaderHeight")]
        public nfloat HeaderHeight {
            get { return headerHeight; }
            set {
                WillChangeValue ("HeaderHeight");
                headerHeight = value;
                DidChangeValue ("HeaderHeight");

                InvalidateLayout ();
            }
        }

        [Export("FooterHeight")]
        public nfloat FooterHeight {
            get { return footerHeight; }
            set {
                WillChangeValue ("FooterHeight");
                footerHeight = value;
                DidChangeValue ("FooterHeight");

                InvalidateLayout ();
            }
        }

        [Export("SectionInset")]
        public UIEdgeInsets SectionInset {
            get { return sectionInset; }
            set {
                WillChangeValue ("SectionInset");
                sectionInset = value;
                DidChangeValue ("SectionInset");

                InvalidateLayout ();
            }
        }

        [Export("ItemRenderDirection")]
        public WaterfallCollectionRenderDirection ItemRenderDirection {
            get { return itemRenderDirection; }
            set {
                WillChangeValue ("ItemRenderDirection");
                itemRenderDirection = value;
                DidChangeValue ("ItemRenderDirection");

                InvalidateLayout ();
            }
        }
        #endregion

        #region Constructors
        public WaterfallCollectionLayout ()
        {
        }

        public WaterfallCollectionLayout(NSCoder coder) : base(coder) {

        }
        #endregion

        #region Public Methods
        public nfloat ItemWidthInSectionAtIndex(int section) {

            var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
            return (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);
        }
        #endregion

        #region Override Methods
        public override void PrepareLayout ()
        {
            base.PrepareLayout ();

            // Get the number of sections
            var numberofSections = CollectionView.NumberOfSections();
            if (numberofSections == 0)
                return;

            // Reset collections
            headersAttributes.Clear ();
            footersAttributes.Clear ();
            unionRects.Clear ();
            columnHeights.Clear ();
            allItemAttributes.Clear ();
            sectionItemAttributes.Clear ();

            // Initialize column heights
            for (int n = 0; n < ColumnCount; n++) {
                columnHeights.Add ((nfloat)0);
            }

            // Process all sections
            nfloat top = 0.0f;
            var attributes = new UICollectionViewLayoutAttributes ();
            var columnIndex = 0;
            for (nint section = 0; section < numberofSections; ++section) {
                // Calculate section specific metrics
                var minimumInterItemSpacing = (MinimumInterItemSpacingForSection == null) ? MinimumColumnSpacing :
                    MinimumInterItemSpacingForSection (CollectionView, this, section);

                // Calculate widths
                var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
                var itemWidth = (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);

                // Calculate section header
                var heightHeader = (HeightForHeader == null) ? HeaderHeight :
                    HeightForHeader (CollectionView, this, section);

                if (heightHeader > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Header, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, heightHeader);
                    headersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);

                    top = attributes.Frame.GetMaxY ();
                }

                top += SectionInset.Top;
                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }

                // Calculate Section Items
                var itemCount = CollectionView.NumberOfItemsInSection(section);
                List<UICollectionViewLayoutAttributes> itemAttributes = new List<UICollectionViewLayoutAttributes> ();

                for (nint n = 0; n < itemCount; n++) {
                    var indexPath = NSIndexPath.FromRowSection (n, section);
                    columnIndex = NextColumnIndexForItem (n);
                    var xOffset = SectionInset.Left + (itemWidth + MinimumColumnSpacing) * (nfloat)columnIndex;
                    var yOffset = columnHeights [columnIndex];
                    var itemSize = (SizeForItem == null) ? new CGSize (0, 0) : SizeForItem (CollectionView, this, indexPath);
                    nfloat itemHeight = 0.0f;

                    if (itemSize.Height > 0.0f && itemSize.Width > 0.0f) {
                        itemHeight = (nfloat)Math.Floor (itemSize.Height * itemWidth / itemSize.Width);
                    }

                    attributes = UICollectionViewLayoutAttributes.CreateForCell (indexPath);
                    attributes.Frame = new CGRect (xOffset, yOffset, itemWidth, itemHeight);
                    itemAttributes.Add (attributes);
                    allItemAttributes.Add (attributes);
                    columnHeights [columnIndex] = attributes.Frame.GetMaxY () + MinimumInterItemSpacing;
                }
                sectionItemAttributes.Add (itemAttributes);

                // Calculate Section Footer
                nfloat footerHeight = 0.0f;
                columnIndex = LongestColumnIndex();
                top = columnHeights [columnIndex] - MinimumInterItemSpacing + SectionInset.Bottom;
                footerHeight = (HeightForFooter == null) ? FooterHeight : HeightForFooter(CollectionView, this, section);

                if (footerHeight > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Footer, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, footerHeight);
                    footersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);
                    top = attributes.Frame.GetMaxY ();
                }

                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }
            }

            var i =0;
            var attrs = allItemAttributes.Count;
            while(i < attrs) {
                var rect1 = allItemAttributes [i].Frame;
                i = (int)Math.Min (i + unionSize, attrs) - 1;
                var rect2 = allItemAttributes [i].Frame;
                unionRects.Add (CGRect.Union (rect1, rect2));
                i++;
            }

        }

        public override CGSize CollectionViewContentSize {
            get {
                if (CollectionView.NumberOfSections () == 0) {
                    return new CGSize (0, 0);
                }

                var contentSize = CollectionView.Bounds.Size;
                contentSize.Height = columnHeights [0];
                return contentSize;
            }
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForItem (NSIndexPath indexPath)
        {
            if (indexPath.Section >= sectionItemAttributes.Count) {
                return null;
            }

            if (indexPath.Item >= sectionItemAttributes [indexPath.Section].Count) {
                return null;
            }

            var list = sectionItemAttributes [indexPath.Section];
            return list [(int)indexPath.Item];
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForSupplementaryView (NSString kind, NSIndexPath indexPath)
        {
            var attributes = new UICollectionViewLayoutAttributes ();

            switch (kind) {
            case "header":
                attributes = headersAttributes [indexPath.Section];
                break;
            case "footer":
                attributes = footersAttributes [indexPath.Section];
                break;
            }

            return attributes;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var begin = 0;
            var end = unionRects.Count;
            List<UICollectionViewLayoutAttributes> attrs = new List<UICollectionViewLayoutAttributes> ();


            for (int i = 0; i < end; i++) {
                if (rect.IntersectsWith(unionRects[i])) {
                    begin = i * (int)unionSize;
                }
            }

            for (int i = end - 1; i >= 0; i--) {
                if (rect.IntersectsWith (unionRects [i])) {
                    end = (int)Math.Min ((i + 1) * (int)unionSize, allItemAttributes.Count);
                    break;
                }
            }

            for (int i = begin; i < end; i++) {
                var attr = allItemAttributes [i];
                if (rect.IntersectsWith (attr.Frame)) {
                    attrs.Add (attr);
                }
            }

            return attrs.ToArray();
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            var oldBounds = CollectionView.Bounds;
            return (newBounds.Width != oldBounds.Width);
        }
        #endregion

        #region Private Methods
        private int ShortestColumnIndex() {
            var index = 0;
            var shortestHeight = nfloat.MaxValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height < shortestHeight) {
                    shortestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int LongestColumnIndex() {
            var index = 0;
            var longestHeight = nfloat.MinValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height > longestHeight) {
                    longestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int NextColumnIndexForItem(nint item) {
            var index = 0;

            switch (ItemRenderDirection) {
            case WaterfallCollectionRenderDirection.ShortestFirst:
                index = ShortestColumnIndex ();
                break;
            case WaterfallCollectionRenderDirection.LeftToRight:
                index = ColumnCount;
                break;
            case WaterfallCollectionRenderDirection.RightToLeft:
                index = (ColumnCount - 1) - ((int)item / ColumnCount);
                break;
            }

            return index;
        }
        #endregion

        #region Events
        public delegate CGSize WaterfallCollectionSizeDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, NSIndexPath indexPath);
        public delegate nfloat WaterfallCollectionFloatDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);
        public delegate UIEdgeInsets WaterfallCollectionEdgeInsetsDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);

        public event WaterfallCollectionSizeDelegate SizeForItem;
        public event WaterfallCollectionFloatDelegate HeightForHeader;
        public event WaterfallCollectionFloatDelegate HeightForFooter;
        public event WaterfallCollectionEdgeInsetsDelegate InsetForSection;
        public event WaterfallCollectionFloatDelegate MinimumInterItemSpacingForSection;
        #endregion
    }
}
```

這可以是用來提供自訂的兩個資料行、 瀑布圖類型的版面配置到集合檢視的類別。
程式碼會使用索引鍵-值撰寫程式碼 (透過`WillChangeValue`和`DidChangeValue`方法) 提供我們計算的屬性，這個類別中的資料繫結。

接著，編輯`WaterfallCollectionSource`並進行下列變更和新功能：

```csharp
private Random rnd = new Random();
...

public List<nfloat> Heights { get; set; } = new List<nfloat> ();
...

public WaterfallCollectionSource (WaterfallCollectionView collectionView)
{
    // Initialize
    CollectionView = collectionView;

    // Init numbers collection
    for (int n = 0; n < 100; ++n) {
        Numbers.Add (n);
        Heights.Add (rnd.Next (0, 100) + 40.0f);
    }
}
```

針對每個將會顯示在清單中的項目，這會建立隨機的高度。

接著，編輯`WaterfallCollectionView`類別，並新增下列協助程式屬性：

```csharp
public WaterfallCollectionSource Source {
    get { return (WaterfallCollectionSource)DataSource; }
}
```

這會讓您更輕鬆地從自訂的版面配置中取得位於我們的資料來源 （和項目高度）。

最後，編輯檢視控制器，並加入下列程式碼：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    var waterfallLayout = new WaterfallCollectionLayout ();

    // Wireup events
    waterfallLayout.SizeForItem += (collectionView, layout, indexPath) => {
        var collection = collectionView as WaterfallCollectionView;
        return new CGSize((View.Bounds.Width-40)/3,collection.Source.Heights[(int)indexPath.Item]);
    };

    // Attach the custom layout to the collection
    CollectionView.SetCollectionViewLayout(waterfallLayout, false);
}
```

這會建立我們自訂的版面配置的執行個體、 設定事件提供的每個項目大小，並將新的版面配置附加到集合檢視。

如果我們再次執行 Xamarin.iOS 應用程式，此集合檢視現在看起來如下所示：

[![](uicollectionview-images/custom01.png "集合檢視現在看起來像這樣")](uicollectionview-images/custom01.png#lightbox)

我們可以仍拖曳來排序項目，但項目現在會變更大小，以符合其新位置，它們會在捨棄時。

## <a name="collection-view-changes"></a>集合檢視變更

在下列章節中，我們將深入了解 ios 9 的每個集合檢視中的類別所做的變更。

### <a name="uicollectionview"></a>UICollectionView

下列變更或新增項目已對`UICollectionView`適用於 iOS 9 的類別：

- `BeginInteractiveMovementForItem` – 標記拖曳作業的開始。
- `CancelInteractiveMovement` – 告知集合檢視的使用者已取消拖曳作業。
- `EndInteractiveMovement` – 告知集合檢視的使用者已完成拖曳作業。
- `GetIndexPathsForVisibleSupplementaryElements` -傳回`indexPath`的頁首或頁尾中的集合檢視區段。
- `GetSupplementaryView` -傳回指定的頁首或頁尾。
- `GetVisibleSupplementaryViews` -傳回一份所有可見的頁首和頁尾。
- `UpdateInteractiveMovementTargetPosition` – 告知集合檢視的使用者已移動，或會移動，在拖曳作業期間的項目。

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

下列變更或新增項目已對`UICollectionViewController`iOS 9 中的類別：

- `InstallsStandardGestureForInteractiveMovement` – 如果`true`將使用新的筆勢辨識器，會自動支援拖曳來重新排列。
- `CanMoveItem` – 如果指定的項目可以重新排列的拖曳，會通知此集合檢視。
- `GetTargetContentOffset` – 用來取得指定的集合檢視項目的位移。
- `GetTargetIndexPathForMove` -取得`indexPath`的拖曳作業的指定項目。
- `MoveItem` – 在清單中移動指定的項目順序。


### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

下列變更或新增項目已對`UICollectionViewDataSource`iOS 9 中的類別：

- `CanMoveItem` – 如果指定的項目可以重新排列的拖曳，會通知此集合檢視。
- `MoveItem` – 在清單中移動指定的項目順序。

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

下列變更或新增項目已對`UICollectionViewDelegate`iOS 9 中的類別：

- `GetTargetContentOffset` – 用來取得指定的集合檢視項目的位移。
- `GetTargetIndexPathForMove` -取得`indexPath`的拖曳作業的指定項目。

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

下列變更或新增項目已對`UICollectionViewFlowLayout`iOS 9 中的類別：

- `SectionFootersPinToVisibleBounds` – 會遵循顯示的集合檢視邊界區段頁尾。
- `SectionHeadersPinToVisibleBounds` – 會遵循顯示的集合檢視界限的區段標頭。

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

下列變更或新增項目已對`UICollectionViewLayout`iOS 9 中的類別：

- `GetInvalidationContextForEndingInteractiveMovementOfItems` – 在使用者完成拖曳，或取消時，傳回失效內容結尾的拖曳作業。
- `GetInvalidationContextForInteractivelyMovingItems` -傳回在拖曳作業開始的失效內容。
- `GetLayoutAttributesForInteractivelyMovingItem` -取得版面配置屬性指定的項目時拖曳項目。
- `GetTargetIndexPathForInteractivelyMovingItem` -傳回`indexPath`拖曳項目時，會在指定的時間點的項目。

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

下列變更或新增項目已對`UICollectionViewLayoutAttributes`iOS 9 中的類別：

- `CollisionBoundingPath` – 在拖曳作業期間會傳回兩個項目衝突路徑。
- `CollisionBoundsType` -傳回的衝突類型 (為`UIDynamicItemCollisionBoundsType`)，在拖曳作業期間發生。

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

下列變更或新增項目已對`UICollectionViewLayoutInvalidationContext`iOS 9 中的類別：

- `InteractiveMovementTarget` -傳回在拖曳作業的目標項目。
- `PreviousIndexPathsForInteractivelyMovingItems` -傳回`indexPaths`中拖曳來重新排列作業牽涉到的其他項目。
- `TargetIndexPathsForInteractivelyMovingItems` -傳回`indexPaths`拖曳來重新排列作業的結果就會重新排列的項目。

### <a name="uicollectionviewsource"></a>UICollectionViewSource

下列變更或新增項目已對`UICollectionViewSource`iOS 9 中的類別：

- `CanMoveItem` – 如果指定的項目可以重新排列的拖曳，會通知此集合檢視。
- `GetTargetContentOffset` -傳回的項目會變成透過拖曳來重新排列作業的位移。
- `GetTargetIndexPathForMove` -傳回`indexPath`將拖曳來重新排序作業期間移動的項目。
- `MoveItem` – 在清單中移動指定的項目順序。

## <a name="summary"></a>總結

這篇文章已涵蓋在 iOS 9 中的集合檢視中所做的變更，並說明如何在 Xamarin.iOS 中實作它們。
其中涵蓋了在集合檢視中，實作簡單的拖放以重新排序動作使用自訂的筆勢辨識器與拖曳-排序;並拖曳來重新排列會自訂集合的檢視版面配置的影響。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [集合檢視範例](https://developer.xamarin.com/samples/monotouch/ios9/CollectionView/)
- [SimpleCollectionView （範例）](https://developer.xamarin.com/samples/monotouch/SimpleCollectionView/)
- [事件、通訊協定與委派](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [使用資料表和資料格](~/ios/user-interface/controls/tables/index.md)
