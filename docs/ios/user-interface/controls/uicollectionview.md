---
title: Xamarin 中的集合視圖
description: 集合視圖可讓您使用任意版面配置來顯示內容。 它們可讓您輕鬆地建立現成的格線版面配置，同時也支援自訂版面配置。
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: eccd0415fdc1db357f904b843a015df60a35a488
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939603"
---
# <a name="collection-views-in-xamarinios"></a>Xamarin 中的集合視圖

_集合視圖可讓您使用任意版面配置來顯示內容。它們可讓您輕鬆地建立現成的格線版面配置，同時也支援自訂版面配置。_

類別中提供的集合 Views `UICollectionView` 是 iOS 6 中的新概念，引進使用版面配置在螢幕上呈現多個專案。 將資料提供給 `UICollectionView` 來建立專案並與這些專案互動的模式，會遵循常用於 iOS 開發的相同委派和資料來源模式。

不過，集合視圖會使用獨立于其本身的版面配置子系統 `UICollectionView` 。 因此，只要提供不同的版面配置，就可以輕鬆地變更集合視圖的呈現方式。

iOS 提供名為的配置類別，可讓您建立以程式碼為基礎的版面配置， `UICollectionViewFlowLayout` 而不需要額外的工作。 此外，也可以建立自訂版面配置，以允許您可以想像的任何簡報。

## <a name="uicollectionview-basics"></a>UICollectionView 基本概念

`UICollectionView`類別是由三個不同的專案所組成：

- **儲存格**–每個專案的資料驅動型視圖
- **補充視圖**–與區段相關聯的資料驅動視圖。
- **裝飾視圖**–版面配置所建立的非資料驅動型視圖

## <a name="cells"></a>資料格

儲存格是物件，代表集合視圖所呈現之資料集中的單一專案。 每個資料格都是類別的實例 `UICollectionViewCell` ，它是由三個不同的視圖所組成，如下圖所示：

 [![每個資料格都是由三個不同的觀點組成，如下所示](uicollectionview-images/01-uicollectionviewcell.png)](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

`UICollectionViewCell`類別具有以下每個視圖的下列屬性：

- `ContentView`–此視圖包含儲存格所呈現的內容。 它會在螢幕上以最上層的迭置順序呈現。
- `SelectedBackgroundView`–儲存格已內建支援選取專案。 這個視圖用來以視覺方式表示已選取儲存格。 當選取資料格時，它就會呈現在正下方 `ContentView` 。
- `BackgroundView`–資料格也可以顯示背景，這是由呈現 `BackgroundView` 。 這個視圖會呈現在下方 `SelectedBackgroundView` 。

藉由設定，使 `ContentView` 其小於 `BackgroundView` 和 `SelectedBackgroundView` ， `BackgroundView` 可以用來以視覺化方式呈現內容，而 `SelectedBackgroundView` 當選取資料格時，將會顯示，如下所示：

 [![不同的 cell 元素](uicollectionview-images/02-cells.png)](uicollectionview-images/02-cells.png#lightbox)

上述螢幕擷取畫面中的資料格是藉由繼承自 `UICollectionViewCell` 並 `ContentView` 分別設定、和屬性所建立 `SelectedBackgroundView` `BackgroundView` ，如下列程式碼所示：

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

 <a name="Supplementary_Views"></a>

## <a name="supplementary-views"></a>補充視圖

[補充] 視圖是顯示與每個區段相關聯之資訊的視圖 `UICollectionView` 。 就像資料格一樣，補充視圖也是資料驅動的。 從資料來源呈現專案資料的地方，補充視圖會顯示區段資料，例如出版品介紹中的書籍分類，或音樂媒體櫃中的音樂類型。

例如，您可以使用補充視圖來呈現特定區段的標頭，如下圖所示：

 [![用來呈現特定區段標頭的補充視圖，如下所示](uicollectionview-images/02a-supplementary-view.png)](uicollectionview-images/02a-supplementary-view.png#lightbox)

若要使用補充視圖，首先必須在方法中註冊 `ViewDidLoad` ：

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

然後，您必須使用來傳回此視圖 `GetViewForSupplementaryElement` ，並使用建立， `DequeueReusableSupplementaryView` 並繼承自 `UICollectionReusableView` 。 下列程式碼片段會產生上面螢幕擷取畫面中所示的 SupplementaryView：

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

補充視圖比標頭和頁尾更為通用。
它們可以放在集合視圖中的任何位置，而且可以由任何視圖組成，使其外觀完全可自訂。

 <a name="Decoration_Views"></a>

## <a name="decoration-views"></a>裝飾視圖

裝飾視圖是單純的視覺化視圖，可以在中顯示 `UICollectionView` 。 不同于儲存格和輔助視圖，它們不是資料驅動的。 它們一律會建立在版面配置的子類別中，之後可以變更為內容的版面配置。 例如，裝飾視圖可用來呈現以中的內容進行滾動的背景視圖 `UICollectionView` ，如下所示：

 [![具有紅色背景的裝飾視圖](uicollectionview-images/02c-decoration-view.png)](uicollectionview-images/02c-decoration-view.png#lightbox)

 下列程式碼片段會將 samples 類別中的背景變更為紅色 `CircleLayout` ：

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

如同 iOS 的其他部分（例如 `UITableView` 和 `MKMapView` ）， `UICollectionView` 會從*資料來源*取得其資料，而這會透過類別在 Xamarin. iOS 中公開。 **`UICollectionViewDataSource`** 此類別會負責將內容提供給，例如 `UICollectionView` ：

- **儲存格**–從 `GetCell` 方法傳回。
- **補充視圖**–從 `GetViewForSupplementaryElement` 方法傳回。
- **區段數目**–從方法傳回 `NumberOfSections` 。 如果未執行，則預設為1。
- **每個區段的專案數**–從 `GetItemsCount` 方法傳回。

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
為了方便起見， `UICollectionViewController` 可以使用類別。這會自動設定為同時是下一節所討論的委派，以及其 view 的資料來源 `UICollectionView` 。

如同 `UITableView` ， `UICollectionView` 類別只會呼叫其資料來源，以取得螢幕上專案的儲存格。
從畫面中滾動的資料格會放入佇列中以供重複使用，如下圖所示：

 [![向外滾動畫面的資料格會放入佇列中以供重複使用，如這裡所示](uicollectionview-images/03-cell-reuse.png)](uicollectionview-images/03-cell-reuse.png#lightbox)

使用和簡化了資料格重複使用 `UICollectionView` `UITableView` 。 您不再需要直接在資料來源中建立儲存格（如果無法在重複使用的佇列中使用的話），因為儲存格會向系統註冊。 如果在進行呼叫時無法使用資料格，則會將儲存格從重複使用的佇列中取消佇列，iOS 會根據已註冊的類型或筆尖自動建立。
輔助視圖也可以使用相同的技術。

例如，請考慮下列註冊類別的程式碼 `AnimalCell` ：

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

當 `UICollectionView` 需要儲存格，因為它的專案是在螢幕上時，會 `UICollectionView` 呼叫其資料來源的 `GetCell` 方法。 類似于 UITableView 的運作方式，這個方法會負責從支援資料設定儲存格，這 `AnimalCell` 在此情況下會是類別。

下列程式碼顯示會傳回實例的執行 `GetCell` `AnimalCell` ：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, Foundation.NSIndexPath indexPath)
{
        var animalCell = (AnimalCell)collectionView.DequeueReusableCell (animalCellId, indexPath);

        var animal = animals [indexPath.Row];

        animalCell.Image = animal.Image;

        return animalCell;
}
```

的呼叫 `DequeReusableCell` 是，儲存格會從重複使用的佇列中取消佇列，或者，如果佇列中沒有可用的資料格，則會根據呼叫中註冊的類型建立 `CollectionView.RegisterClassForCell` 。

在此情況下，藉由註冊 `AnimalCell` 類別，iOS 會在內部建立新的，並在對 `AnimalCell` 資料格進行清除佇列的呼叫之後傳回它，在此情況下，它會使用動物類別中所包含的影像進行設定，並將其傳回以供顯示 `UICollectionView` 。

 <a name="Delegate"></a>

### <a name="delegate"></a>代理人

`UICollectionView`類別會使用類型的委派 `UICollectionViewDelegate` ，以支援與中的內容互動 `UICollectionView` 。 這可讓您控制：

- 資料**格選取**–決定是否選取儲存格。
- 資料**格**醒目提示–判斷是否正在觸及儲存格。
- 資料**格功能表**-針對儲存格顯示的功能表，以回應長按下手勢。

如同資料來源， `UICollectionViewController` 預設會將設定為的委派 `UICollectionView` 。

 <a name="Cell_HighLighting"></a>

#### <a name="cell-highlighting"></a>儲存格反白顯示

當按下儲存格時，資料格會轉換成反白顯示的狀態，而且在使用者從儲存格中將其手指帶到前，不會選取它。 這可讓您在實際選取資料格之前，暫時變更其外觀。 選取時，會顯示儲存格的 `SelectedBackgroundView` 。 下圖顯示選取專案發生之前的反白顯示狀態：

 [![下圖顯示在選取專案發生之前的反白顯示狀態](uicollectionview-images/04-cell-highlight.png)](uicollectionview-images/04-cell-highlight.png#lightbox)

若要執行反白顯示， `ItemHighlighted` `ItemUnhighlighted` 可以使用的和方法 `UICollectionViewDelegate` 。 例如，下列程式碼會在反白顯示資料格時，套用的黃色背景，而當取消醒目提示時則會套用 `ContentView` 白色背景，如上圖所示：

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

 <a name="Disabling_Selection"></a>

#### <a name="disabling-selection"></a>正在停用選取範圍

預設會在中啟用選取專案 `UICollectionView` 。 若要停用選取專案，請覆寫 `ShouldHighlightItem` 並傳回 false，如下所示：

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

當停用反白顯示時，也會停用選取儲存格的進程。 此外，也有一個 `ShouldSelectItem` 直接控制選取的方法，不過，如果 `ShouldHighlightItem` 已實作為並傳回 false， `ShouldSelectItem` 則不會呼叫。

 `ShouldSelectItem`當未執行時，允許依專案逐一開啟或關閉選取專案 `ShouldHighlightItem` 。 它也允許在沒有選取範圍的情況下進行反白顯示，如果 `ShouldHighlightItem` 已執行並傳回 true，則會傳回 `ShouldSelectItem` false。

 <a name="Cell_Menus"></a>

#### <a name="cell-menus"></a>資料格功能表

中的每個資料格 `UICollectionView` 都能夠顯示功能表，讓您可以選擇性地支援剪下、複製和貼上。 若要在資料格上建立 [編輯] 功能表：

1. `ShouldShowMenu`如果專案應該顯示功能表，則覆寫並傳回 true。
1. `CanPerformAction`針對專案可執行檔每個動作覆寫並傳回 true，這將會是剪下、複製或貼上。
1. 覆寫 `PerformAction` 以執行 [編輯]、[貼上] 操作。

下列螢幕擷取畫面顯示長時間按下資料格時的功能表：

 [![這個螢幕擷取畫面顯示長時間按下儲存格時的功能表](uicollectionview-images/04a-menu.png)](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout"></a>

## <a name="layout"></a>Layout

`UICollectionView`支援版面配置系統，允許將其所有專案、儲存格、補充視圖和裝飾視圖的定位，獨立于本身之外進行管理 `UICollectionView` 。
使用配置系統時，應用程式可以支援像我們在本文中看到的類似方格的版面配置，並提供自訂的版面配置。

 <a name="Layout_Basics"></a>

### <a name="layout-basics"></a>版面配置基本概念

中的版面配置 `UICollectionView` 定義于繼承自的類別中 `UICollectionViewLayout` 。 版面配置的執行負責為中的每個專案建立版面配置屬性 `UICollectionView` 。 有兩種方式可建立版面配置：

- 使用內建的 `UICollectionViewFlowLayout` 。
- 從繼承，以提供自訂的版面配置 `UICollectionViewLayout` 。

 <a name="Flow_Layout"></a>

### <a name="flow-layout"></a>流程配置

`UICollectionViewFlowLayout`類別會提供以線條為基礎的版面配置，適合用來排列資料格方格中的內容（如我們所見）。

若要使用流程版面配置：

- 建立的實例 `UICollectionViewFlowLayout` ：

```csharp
var layout = new UICollectionViewFlowLayout ();
```

- 將實例傳遞至的函式 `UICollectionView` ：

```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

這就是在方格中版面配置內容所需的一切。 此外，當方向變更時，會 `UICollectionViewFlowLayout` 適當地處理內容的重新排列，如下所示：

 [![方向變更的範例](uicollectionview-images/05-layout-orientation.png)](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset"></a>

#### <a name="section-inset"></a>區段內凹

為了提供周圍的一些空間 `UIContentView` ，版面配置具有 `SectionInset` 類型的屬性 `UIEdgeInsets` 。 例如，下列程式碼會在配置時，于的每個區段周圍提供50圖元的緩衝區 `UIContentView` `UICollectionViewFlowLayout` ：

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

這會導致區段周圍出現間距，如下所示：

 [![區段周圍的間距，如下所示](uicollectionview-images/06-sectioninset.png)](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout"></a>

#### <a name="subclassing-uicollectionviewflowlayout"></a>子類別化 UICollectionViewFlowLayout

在 [版本] 中 `UICollectionViewFlowLayout` ，您也可以將它設為子類別，以進一步自訂沿著一行的內容配置。 例如，這可以用來建立不會將資料格換成方格的配置，而是建立具有水準滾動效果的單一資料列，如下所示：

 [![具有水準滾動效果的單一資料列](uicollectionview-images/07-line-layout.png)](uicollectionview-images/07-line-layout.png#lightbox)

若要透過子類別化來執行此動作， `UICollectionViewFlowLayout` 需要：

- 將套用至配置本身的任何版面配置屬性，或在此函式中的版面配置中的所有專案初始化。
- 覆寫 `ShouldInvalidateLayoutForBoundsChange` ，傳回 true，如此一來，當變更的界限時 `UICollectionView` ，就會重新計算資料格的版面配置。 在此情況下，這是為了確保套用到 centermost 儲存格的轉換程式碼將會在滾動期間套用。
- 覆寫 `TargetContentOffset` ，讓 [centermost] 儲存格貼齊至的中央，做 `UICollectionView` 為「滾動」停止。
- 覆寫 `LayoutAttributesForElementsInRect` 以傳回的陣列 `UICollectionViewLayoutAttributes` 。 每個都 `UICollectionViewLayoutAttribute` 包含如何配置特定專案的資訊，包括如其 `Center` 、和的 `Size` 屬性 `ZIndex` `Transform3D` 。

下列程式碼顯示這類的實作為：

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

 <a name="Custom_Layout"></a>

### <a name="custom-layout"></a>自訂版面配置

除了使用之外 `UICollectionViewFlowLayout` ，您也可以直接從繼承，以完全自訂版面配置 `UICollectionViewLayout` 。

要覆寫的主要方法如下：

- `PrepareLayout`–用來執行將在整個版面配置過程中使用的初始幾何計算。
- `CollectionViewContentSize`–傳回用來顯示內容的區域大小。
- `LayoutAttributesForElementsInRect`–如同稍早所示的 UICollectionViewFlowLayout 範例，這個方法是用來提供有關 `UICollectionView` 如何配置每個專案的資訊。 不過，與不同的是 `UICollectionViewFlowLayout` ，在建立自訂版面配置時，您可以選擇放置專案。

例如，相同的內容可能會以迴圈配置呈現，如下所示：

 [![如下所示的迴圈自訂版面配置](uicollectionview-images/08-circle-layout.png)](uicollectionview-images/08-circle-layout.png#lightbox)

有關版面配置的強大功能，就是從類似方格的版面配置變更為水準滾動配置，然後再到這個迴圈版面配置，只需要提供給的版面配置類別才 `UICollectionView` 會變更。 中沒有任何內容 `UICollectionView` ，其委派或資料來源程式碼完全不會變更。

## <a name="changes-in-ios-9"></a>IOS 9 中的變更

在 iOS 9 中，集合視圖（ `UICollectionView` ）現在可新增新的預設手勢辨識器和數個新的支援方法，以支援將專案的重新排列。

您可以使用這些新方法，輕鬆地在您的集合視圖中執行拖曳以重新排序，並且可以選擇在重新排列程式的任何階段自訂專案外觀。

[![重新排列進程的範例](uicollectionview-images/intro01.png)](uicollectionview-images/intro01.png#lightbox)

在本文中，我們將探討如何在 Xamarin iOS 應用程式中執行拖放重新排序，以及 iOS 9 對集合視圖控制項進行的一些其他變更：

- [輕鬆重新排列專案的順序](#Easy-Reordering-of-Items)
  - [簡單的重新排序範例](#Simple-Reordering-Example)
  - [使用自訂手勢辨識器](#Using-a-Custom-Gesture-Recognizer)
  - [自訂版面配置和重新排列](#Custom-Layouts-and-Reording)
- [集合視圖變更](#collection-view-changes)

<a name="Easy-Reordering-of-Items"></a>

## <a name="reordering-of-items"></a>重新排列專案的順序

如上所述，iOS 9 中集合視圖最重大的變更之一，就是新增現成的輕鬆重新排序功能。

在 iOS 9 中，將重新排序加入至集合視圖的最快方式是使用 `UICollectionViewController` 。
集合視圖控制器現在具有 `InstallsStandardGestureForInteractiveMovement` 屬性，它會加入標準手勢辨識*器*，支援拖曳以重新排序集合中的專案。
由於預設值為 `true` ，因此您只需要執行類別的 `MoveItem` 方法， `UICollectionViewDataSource` 即可支援拖放重新排序。 例如：

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
  // Reorder our list of items
  ...
}
```

<a name="Simple-Reordering-Example"></a>

### <a name="simple-reordering-example"></a>簡單的重新排序範例

如需快速範例，請啟動新的 Xamarin iOS 專案，並編輯**主要**的分鏡腳本檔案。 將拖曳 `UICollectionViewController` 至設計介面：

[![新增 UICollectionViewController](uicollectionview-images/quick01.png)](uicollectionview-images/quick01.png#lightbox)

選取 [集合] 視圖（從檔大綱執行此動作可能最簡單）。 在 [Properties Pad 的 [配置] 索引標籤中，設定下列大小，如下列螢幕擷取畫面所示：

- 資料**格大小**：寬度– 60 |高度–60
- **標頭大小**：寬度– 0 |高度–0
- 頁尾**大小**：寬度– 0 |高度–0
- **最小間距**：適用于資料格– 8 |適用于行–8
- **區段**內凹： Top – 16 |下– 16 |左方– 16 |右方–16

[![設定集合視圖大小](uicollectionview-images/quick04.png)](uicollectionview-images/quick04.png#lightbox)

接下來，編輯預設資料格：

- 將其背景色彩變更為藍色
- 新增標籤以作為資料格的標題
- 將重新使用識別碼設定為**cell**

[![編輯預設資料格](uicollectionview-images/quick02.png)](uicollectionview-images/quick02.png#lightbox)

加入條件約束，讓標籤在資料格的大小變更時保持置中的位置：

在_CollectionViewCell_的**屬性 Pad**中，將**類別**設為 `TextCollectionViewCell` ：

[![將類別設定為 TextCollectionViewCell](uicollectionview-images/quick05.png)](uicollectionview-images/quick05.png#lightbox)

將 [**集合可重複使用的視圖**] 設定為 `Cell` ：

[![將集合可重複使用的 View 設定為 Cell](uicollectionview-images/quick06.png)](uicollectionview-images/quick06.png#lightbox)

最後，選取標籤，並將它命名 `TextLabel` 為：

[![名稱標籤 TextLabel](uicollectionview-images/quick07.png)](uicollectionview-images/quick07.png#lightbox)

編輯 `TextCollectionViewCell` 類別並新增下列屬性：

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

此 `Text` 標籤的屬性會公開為數據格的標題，因此可以從程式碼設定。

將新的 c # 類別加入至專案，並呼叫它 `WaterfallCollectionSource` 。 編輯檔案，使其看起來如下所示：

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

這個類別會是我們的集合視圖的資料來源，並提供集合中每個儲存格的資訊。
請注意， `MoveItem` 方法會實作為，讓集合中的專案可以拖曳重新排序。

將另一個新的 c # 類別加入至專案，並呼叫它 `WaterfallCollectionDelegate` 。 編輯此檔案，使其看起來如下所示：

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

這會做為我們的集合視圖的委派。 當使用者在集合視圖中與其互動時，已覆寫方法來反白顯示資料格。

將最後一個 c # 類別加入至專案，並呼叫它 `WaterfallCollectionView` 。 編輯此檔案，使其看起來如下所示：

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

請注意 `DataSource` ， `Delegate` 我們在上方建立的會在集合視圖從其分鏡腳本（或**xib**檔案）中建立時設定。

再次編輯**主要**的分鏡腳本檔案，然後選取 [集合] 視圖並切換至 [**屬性**]。 將**類別**設定為我們先前定義的自訂 `WaterfallCollectionView` 類別：

儲存您對 UI 所做的變更，並執行應用程式。
如果使用者從清單中選取專案，並將它拖曳至新位置，則其他專案會在移出項目的方式時自動建立動畫。
當使用者將專案放在新的位置時，它會停留在該位置。 例如：

[![將專案拖曳至新位置的範例](uicollectionview-images/intro01.png)](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer"></a>

### <a name="using-a-custom-gesture-recognizer"></a>使用自訂手勢辨識器

如果您無法使用 `UICollectionViewController` ，而且必須使用一般 `UIViewController` ，或如果您想要更充分掌控拖放手勢，您可以建立自己的自訂手勢辨識器，並在視圖載入時將它加入至集合視圖。 例如：

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

在這裡，我們會使用數個新增至集合視圖的新方法，來執行和控制拖曳作業：

- `BeginInteractiveMovementForItem`-標記移動作業的開始。
- `UpdateInteractiveMovementTargetPosition`-會隨著專案的位置更新而傳送。
- `EndInteractiveMovement`-標示專案移動的結尾。
- `CancelInteractiveMovement`-標示使用者取消移動作業。

當應用程式執行時，拖曳作業的運作方式會與集合視圖所附的預設拖曳手勢辨識器完全相同。

<a name="Custom-Layouts-and-Reording"></a>

### <a name="custom-layouts-and-reordering"></a>自訂版面配置和重新排列

在 iOS 9 中，已新增數個新的方法，以便在集合視圖中使用拖放順序和自訂版面配置。 為了探索這項功能，讓我們將自訂配置新增至集合。

首先，將名為的新 c # 類別加入 `WaterfallCollectionLayout` 至專案。 編輯它，讓它看起來如下所示：

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

這個類別可以用來提供自訂的兩個數據行、瀑布型別配置給集合視圖。
程式碼會使用索引鍵-值編碼（透過 `WillChangeValue` 和 `DidChangeValue` 方法）來提供此類別中計算屬性的資料系結。

接著，編輯， `WaterfallCollectionSource` 並進行下列變更和新增：

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

這會為將顯示在清單中的每個專案建立隨機高度。

接著，編輯 `WaterfallCollectionView` 類別並新增下列 helper 屬性：

```csharp
public WaterfallCollectionSource Source {
  get { return (WaterfallCollectionSource)DataSource; }
}
```

這可讓您更輕鬆地從自訂版面配置中取得資料來源（和專案高度）。

最後，編輯 view controller 並新增下列程式碼：

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

這會建立自訂配置的實例、設定事件以提供每個專案的大小，並將新的配置附加至我們的集合視圖。

如果我們再次執行 Xamarin 應用程式，則 [集合] 視圖現在看起來會像下面這樣：

[![[集合] 視圖現在看起來會像這樣](uicollectionview-images/custom01.png)](uicollectionview-images/custom01.png#lightbox)

我們仍然可以像之前一樣拖曳專案，但這些專案現在會變更大小，以符合其在放置時的新位置。

## <a name="collection-view-changes"></a>集合視圖變更

在下列各節中，我們將詳細探討 iOS 9 對集合視圖中每個類別所做的變更。

### <a name="uicollectionview"></a>UICollectionView

IOS 9 的類別已進行下列變更或新增 `UICollectionView` ：

- `BeginInteractiveMovementForItem`–標記拖曳作業的開始。
- `CancelInteractiveMovement`–通知集合視圖，使用者已取消拖曳作業。
- `EndInteractiveMovement`–通知集合視圖使用者已完成拖曳作業。
- `GetIndexPathsForVisibleSupplementaryElements`–傳回 `indexPath` 集合視圖區段中頁首或頁尾的。
- `GetSupplementaryView`–傳回給定的頁首或頁尾。
- `GetVisibleSupplementaryViews`–傳回所有顯示頁首和頁尾的清單。
- `UpdateInteractiveMovementTargetPosition`–通知集合視圖，表示使用者已在拖曳作業期間移動或移動專案。

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

IOS 9 中的類別已進行下列變更或新增 `UICollectionViewController` ：

- `InstallsStandardGestureForInteractiveMovement`–如果會 `true` 使用自動支援拖曳至重新排序的新手勢辨識器。
- `CanMoveItem`–當指定的專案可以拖曳重新排序時，通知集合視圖。
- `GetTargetContentOffset`–用來取得指定集合視圖專案的位移。
- `GetTargetIndexPathForMove`–取得 `indexPath` 拖曳作業之指定專案的。
- `MoveItem`–移動清單中指定專案的順序。

### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

IOS 9 中的類別已進行下列變更或新增 `UICollectionViewDataSource` ：

- `CanMoveItem`–當指定的專案可以拖曳重新排序時，通知集合視圖。
- `MoveItem`–移動清單中指定專案的順序。

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

IOS 9 中的類別已進行下列變更或新增 `UICollectionViewDelegate` ：

- `GetTargetContentOffset`–用來取得指定集合視圖專案的位移。
- `GetTargetIndexPathForMove`–取得 `indexPath` 拖曳作業之指定專案的。

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

IOS 9 中的類別已進行下列變更或新增 `UICollectionViewFlowLayout` ：

- `SectionFootersPinToVisibleBounds`–將區段頁尾指向可見的集合視圖範圍。
- `SectionHeadersPinToVisibleBounds`–將區段標頭指向可見的集合視圖範圍。

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

IOS 9 中的類別已進行下列變更或新增 `UICollectionViewLayout` ：

- `GetInvalidationContextForEndingInteractiveMovementOfItems`–當使用者完成拖曳或取消時，會在拖曳作業結束時傳回失效內容。
- `GetInvalidationContextForInteractivelyMovingItems`–傳回拖曳作業開始時的失效內容。
- `GetLayoutAttributesForInteractivelyMovingItem`–在拖曳專案時，取得指定專案的版面配置屬性。
- `GetTargetIndexPathForInteractivelyMovingItem`–在 `indexPath` 拖曳專案時，傳回位於指定點之專案的。

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

IOS 9 中的類別已進行下列變更或新增 `UICollectionViewLayoutAttributes` ：

- `CollisionBoundingPath`–在拖曳作業期間傳回兩個專案的衝突路徑。
- `CollisionBoundsType`–傳回在拖曳作業期間發生的衝突類型 `UIDynamicItemCollisionBoundsType` 。

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationCoNtext

IOS 9 中的類別已進行下列變更或新增 `UICollectionViewLayoutInvalidationContext` ：

- `InteractiveMovementTarget`–傳回拖曳作業的目標專案。
- `PreviousIndexPathsForInteractivelyMovingItems`–傳回 `indexPaths` 拖曳要重新排序作業的其他相關專案。
- `TargetIndexPathsForInteractivelyMovingItems`–傳回專案的， `indexPaths` 將會因為拖放重新排序作業而重新排序。

### <a name="uicollectionviewsource"></a>UICollectionViewSource

IOS 9 中的類別已進行下列變更或新增 `UICollectionViewSource` ：

- `CanMoveItem`–當指定的專案可以拖曳重新排序時，通知集合視圖。
- `GetTargetContentOffset`–傳回透過拖曳至重新排序作業移動之專案的位移。
- `GetTargetIndexPathForMove`–傳回 `indexPath` 要在拖曳重新排序作業期間移動之專案的。
- `MoveItem`–移動清單中指定專案的順序。

## <a name="summary"></a>總結

本文涵蓋 iOS 9 中的集合視圖變更，並已說明如何在 Xamarin 中執行它們。
其中涵蓋了在集合視圖中執行簡單的拖放動作，使用自訂手勢辨識器搭配拖曳以進行重新排序;而拖曳至重新排序會影響自訂集合視圖配置。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [集合視圖範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-collectionview)
- [SimpleCollectionView （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/simplecollectionview)
- [事件、通訊協定與委派](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [使用資料表和資料格](~/ios/user-interface/controls/tables/index.md)
