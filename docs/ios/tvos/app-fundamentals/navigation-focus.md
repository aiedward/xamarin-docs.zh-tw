---
title: 使用 Xamarin 中的 tvOS 導覽和焦點
description: 本文涵蓋焦點的概念，以及如何使用它來呈現和處理 tvOS 應用程式內的導覽。
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 3c754acc3502d7aa2c47264e734187ffe060c029
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030842"
---
# <a name="working-with-tvos-navigation-and-focus-in-xamarin"></a>使用 Xamarin 中的 tvOS 導覽和焦點

_本文涵蓋焦點的概念，以及如何使用它來呈現和處理 tvOS 應用程式內的導覽。_

本文涵蓋[焦點](#Focus-and-Selection)的概念，以及如何使用它來處理 tvOS 應用程式使用者介面中的[導覽](#Navigation)。 我們將探討內建的 tvOS 導覽控制項如何使用焦點、反白顯示和選取，以提供您的 tvOS 應用程式的使用者介面流覽。

[![](navigation-focus-images/intro01.png "tvOS apps User Interface Navigation")](navigation-focus-images/intro01.png#lightbox)

接下來，我們將探討如何搭配[視差](#Focus-and-Parallax)和*分層影像*使用焦點，將目前導覽狀態的視覺線索提供給終端使用者。

最後，我們將探討如何使用[焦點](#Working-with-Focus)、[焦點更新](#Working-with-Focus-Updates)、[焦點引導](#Working-with-Focus-Guides)，將[焦點放在集合](#Working-with-Focus-in-Collections)中，並在 TvOS 應用程式的影像視圖上[啟用視差](#enabling-parallax)。

<a name="Navigation" />

## <a name="navigation"></a>巡覽

TvOS 應用程式的使用者不會與它直接與 iOS 互動，因為它會在裝置的螢幕上使用影像，而是透過[Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)在房間間間接進行。 在設計應用程式的使用者介面時，您必須牢記這一點，使其自然流動，同時讓使用者可以沉浸 Apple TV 體驗。

成功的 tvOS 應用程式會以順暢地支援應用程式用途的方式來實行導覽，並在不需要留意到導覽本身的情況下提供資料結構。 設計您的導覽，使其既自然又熟悉，而不需要支配使用者介面或從內容和應用程式使用者體驗中繪製焦點。

[![](navigation-focus-images/nav01.png "The tvOS settings app")](navigation-focus-images/nav01.png#lightbox)

使用 Apple TV 時，使用者通常會流覽一組堆疊的螢幕，每個畫面都會呈現一組指定的內容。 接著，每個新的畫面可能會使用標準 UI[控制項（例如](~/ios/tvos/user-interface/tab-bars.md)[按鈕](~/ios/tvos/user-interface/buttons.md)、索引標籤列、表格、[集合視圖](~/ios/tvos/user-interface/collection-views.md)或[分割視圖](~/ios/tvos/user-interface/split-views.md)）來導致一或多個內容的子畫面。

有了每個新畫面，使用者就能更深入流覽到這個畫面堆疊。 藉由使用 Siri 遠端上的 [**功能表**] 按鈕，他們可以回溯流覽堆疊，以返回上一個畫面或主功能表。

在設計 tvOS 應用程式的導覽時，Apple 建議您牢記下列事項：

- 配置**您的導覽以快速且輕鬆地尋找內容**-使用者想要以最少的分次次數存取應用程式中的內容，並盡可能按下和撥動。 簡化您的導覽，並將內容組織成最少的畫面數目。
- **使用觸控來建立流暢的介面**-確保使用者可以使用最少的手勢來在可_設定焦點的專案_之間移動，而不會造成摩擦。
- **具有焦點的設計**-由於使用者與房間間的內容互動，因此必須先將焦點移至使用者介面專案，然後再使用 Siri 遠端與其互動。 如果您的應用程式需要太多手勢來達到其目標，使用者將會感到挫折。
- 透過 **[功能表] 按鈕提供回溯導覽**-建立簡單且熟悉的體驗，讓使用者可以使用 [Siri] 遠端的 [**功能表**] 按鈕向後導覽。 按下**功能表**按鈕時，應該一律返回上一個畫面，或返回應用程式的主功能表。 在應用程式的最上層，按下**功能表**按鈕應該會返回 Apple 電視首頁畫面。
- **通常避免顯示 [上一頁] 按鈕**-因為按下 Siri 遙控器上的 [**功能表**] 按鈕會向後流覽螢幕堆疊，請避免顯示會複製此行為的額外控制項。 此規則的例外狀況適用于購買具有破壞性動作（例如刪除內容）的畫面或畫面，也應該顯示 [**取消**] 按鈕。
- **在單一畫面上顯示大型集合，而不是多**個 Siri 遠端，其設計目的是要使用手勢快速輕鬆地移動大型的內容集合。 如果您的應用程式與大型可設定焦點的專案集合搭配使用，請考慮將它們保留在單一畫面，而不是將其分成多個畫面，而需要使用者的更多導覽。
- 再次**使用標準控制項來進行流覽**，以建立簡單且熟悉的使用者體驗，盡可能使用內建的 `UIKit` 控制項，例如頁面控制項、索引標籤列、分段控制項、資料表視圖、集合視圖和分割區應用程式的導覽。 由於使用者已熟悉這些元素，因此可以直覺地流覽您的應用程式。
- 偏好**水準內容導覽**-因為 Apple 電視的本質，在 Siri 遠端的向左輕量會比上下移動更自然。 設計應用程式的內容版面配置時，請考慮此選項。

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>焦點和選取範圍

在 Apple 電視上，當影像、按鈕或其他 UI 元素是目前導覽的目標時，會將其視為_焦點_。

[![](navigation-focus-images/focus01.png "Focus and Selection example")](navigation-focus-images/focus01.png#lightbox)

不同于 iOS 裝置，使用者會直接與裝置的觸控螢幕上的專案互動，而使用者會使用 Siri 遠端與房間間的 tvOS 元素互動。 為了呈現及處理這種使用者互動，Apple TV 使用以_焦點_為基礎的模型。

在[Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)使用筆勢和按鈕按下，使用者會將焦點從一個 UI 元素移到另一個。 焦點移至所需的專案之後，使用者可以按一下以選取內容，或啟用該專案所代表的動作。

隨著焦點的變更，會使用細微的動畫和效果（例如影像上的視差效果）來清楚識別目前具有焦點的使用者介面專案。

Apple 有下列關於使用焦點和選擇的建議：

- **使用內建的 UI 控制項進行動作效果**-藉由在使用者介面中使用 `UIKit` 和焦點 API，焦點模型會自動將預設的動作和視覺效果套用至您的 UI 元素。 這讓您的應用程式對 Apple TV 平臺的使用者具有原生和熟悉性，並允許在可設定的專案之間進行流暢且直覺的移動。
- **將焦點移到預期的方向**-在 Apple 電視上，幾乎每個元素都使用間接操作。 例如，使用者使用 Siri 遠端來移動焦點，而系統會自動滾動介面，讓目前的焦點專案保持可見。 如果您的應用程式會執行這種互動類型，請確定焦點會以使用者的手勢方向移動。 因此，如果使用者在 Siri 遙控器上向右滑動，則應該向右移動（這可能會導致螢幕向左滾動）。 這項規則的唯一例外是使用直接操作的全螢幕專案（向上輕掃會向上移動元素）。
- **請確定焦點的專案是顯而易見**的，因為您的使用者會從 afar 與您的 UI 元素互動，所以目前的焦點專案就很重要。這通常會由內建的 `UIKit` 元素自動處理。 若為自訂控制項，請使用 [專案大小] 或 [陰影] 之類的功能來顯示焦點。
- **使用視差讓焦點專案變得快速回應**-小型、Siri 遠端的迴圈手勢會以溫和、即時的方式移動焦點專案。 這項[視差效果](#Focus-and-Parallax)內建在 `UIKit`_階層式影像_中，讓使用者有權連接到焦點的專案。
- **建立適當大小的可設定焦點專案**-具有大量間距的大型專案，比較小的專案更容易選取和流覽。
- **設計 UI 元素看起來良好（專注或未取得焦點**）-通常 Apple TV 會藉由增加其大小來代表焦點專案。 請確定您應用程式的 UI 元素看起來很適合任何展示大小，如有必要，請提供適用于較大大小元素的資產。
- **代表焦點變更流暢地**-使用動畫，順暢地淡**出項目的焦點和** **未取得焦點**狀態，以防止 jarring 的轉換。
- **不要顯示游標**-使用者預期會使用焦點來流覽您應用程式的 UI，而不是將游標移到螢幕上。 您的使用者介面應一律使用焦點模型來呈現一致的使用者體驗。

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>使用焦點

有時候，您可能會想要建立自訂控制項，使其成為可設定焦點的專案。 如果是，請覆寫 `CanBecomeFocused` 屬性並傳回 `true`，否則傳回 `false`。 例如:

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

您隨時都可以使用 `UIKit` 控制項的 `Focused` 屬性來查看它是否為目前的專案。 如果 `true` UI 專案目前具有焦點，則為，否則為。 例如:

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

雖然您無法透過程式碼直接將焦點移至另一個 UI 專案，但您可以在載入螢幕時，將其 `PreferredFocusedView` 屬性設為 `true`，以指定哪個 UI 元素會先取得焦點。 例如:

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>使用焦點更新 

當使用者使焦點從某個 UI 專案轉移至另一個時（例如，回應 Siri 遠端上的手勢），_焦點更新事件_將會傳送到失去焦點的專案，而且該專案會獲得焦點。

針對從 `UIView` 或 `UIViewController`繼承的自訂元素，您可以覆寫數個方法來使用焦點更新事件：

- **DidUpdateFocus** -當視圖取得或失去焦點時，就會呼叫此方法。
- **ShouldUpdateFocus** -使用此方法來定義允許焦點移動的位置。

若要要求焦點引擎將焦點移回 `PreferredFocusedView` 的 UI 元素，請呼叫 View Controller 的 `SetNeedsUpdateFocus` 方法。

> [!IMPORTANT]
> 只有在對其呼叫的視圖控制器包含目前具有焦點的視圖時，才會呼叫 `SetNeedsUpdateFocus` 的效果。

<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>使用焦點指南

內建在 tvOS 中的焦點引擎，非常適合處理位於水準和垂直方格的專案之間的移動。 一般來說，您只需要在介面設計中加入 UI 元素，而焦點引擎會自動處理這些元素之間的移動，而不需開發人員介入。

不過，有時可能是因為 ui 專案的必要條件，因為 UI 元素不在水準和垂直格線上，而且可能因為彼此之間的對角線而無法存取。 之所以會發生這種情況，是因為焦點引擎是設計用來處理 UI 專案之間的簡單向上、向下、左和右移動。

如需範例，請採取下列 UI 版面配置：

 [![](navigation-focus-images/guide01.png "Working with Focus Guides example")](navigation-focus-images/guide01.png#lightbox)

因為 [**更多資訊**] 按鈕不會落在具有 [**購買**] 按鈕的水準和垂直格線上，所以使用者無法存取。 不過，您可以使用_焦點指南_輕鬆地修正這種情況，將移動提示提供給焦點引擎。 

焦點指南（`UIFocusGuide`）會公開不可見的視圖區域，使其成為焦點引擎的可設定焦點，因此可將焦點重新導向至另一個視圖。

因此，若要解決上述範例，可以將下列程式碼新增至 View Controller，以在 [**更多資訊**] 和 [**購買**] 按鈕之間建立焦點指南：

```csharp
public UIFocusGuide FocusGuide = new UIFocusGuide ();
...

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Add Focus Guide to layout
    View.AddLayoutGuide (FocusGuide);

    // Define Focus Guide that will allow the user to move
    // between the More Info and Buy buttons.
    FocusGuide.LeftAnchor.ConstraintEqualTo (BuyButton.LeftAnchor).Active = true;
    FocusGuide.TopAnchor.ConstraintEqualTo (MoreInfoButton.TopAnchor).Active = true;
    FocusGuide.WidthAnchor.ConstraintEqualTo (BuyButton.WidthAnchor).Active = true;
    FocusGuide.HeightAnchor.ConstraintEqualTo (MoreInfoButton.HeightAnchor).Active = true;
}
```

首先，會使用 `AddLayoutGuide` 方法建立新的 `UIFocusGuide`，並將其加入至視圖的版面配置指南集合。

接下來，焦點指南的頂端、左側、寬度和高度錨點會相對於 [**詳細資訊**] 和 [**購買**] 按鈕進行調整，以便在兩者之間定位。 請參閱：

[![](navigation-focus-images/guide02.png "Example Focus Guide")](navigation-focus-images/guide02.png#lightbox)

也請務必注意，在建立新的條件約束時，會將其 `Active` 屬性設為 `true`，以將它們啟用：

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

在建立新的焦點指南並將其加入至視圖後，可以覆寫 View Controller 的 `DidUpdateFocus` 方法，並新增下列程式碼，以便在 [**更多資訊**] 和 [**購買**] 按鈕之間移動：

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    base.DidUpdateFocus (context, coordinator);

    // Get next focusable item from context
    var nextFocusableItem = context.NextFocusedView;

    // Anything to process?
    if (nextFocusableItem == null) return;

    // Decide the next focusable item based on the current
    // item with focus
    if (nextFocusableItem == MoreInfoButton) {
        // Move from the More Info to Buy button
        FocusGuide.PreferredFocusedView = BuyButton;
    } else if (nextFocusableItem == BuyButton) {
        // Move from the Buy to the More Info button
        FocusGuide.PreferredFocusedView = MoreInfoButton;
    } else {
        // No valid move
        FocusGuide.PreferredFocusedView = null;
    }
}
```

首先，此程式碼會從已傳入的 `UIFocusUpdateContext` （`context`）取得 `NextFocusedView`。 如果 `null`此視圖，則不需要處理，且方法會結束。

接下來，會評估 `nextFocusableItem`。 如果它符合 [**更多資訊**] 或 [**購買**] 按鈕，則會使用焦點指南的 `PreferredFocusedView` 屬性，將焦點傳送至相反的按鈕。 例如:

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

如果這兩個按鈕都不是焦點移動的來源，就會清除 [`PreferredFocusedView`] 屬性：

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>使用集合中的焦點

在決定是否可以在 `UICollectionView` 或 `UITableView`中為個別專案提供可設定焦點時，您會分別覆寫 `UICollectionViewDelegate` 或 `UITableViewDelegate` 的方法。 例如:

```csharp
public class CardHandDelegate : UICollectionViewDelegateFlowLayout
{
    ...
    public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
    {
        if (indexPath == null) {
            return false;
        } else {
            var controller = collectionView as CardHandViewController;
            return !controller.Hand [indexPath.Row].IsFaceDown;
        }
    }
}
```

如果目前的專案可以是焦點，`CanFocusItem` 方法會傳回 `true`，否則會傳回 `false`。

如果您想要 `UICollectionView` 或 `UITableView` 在最後一個專案遺失並重新取得焦點時，將焦點還原至其中，請將 [`RemembersLastFocusedIndexPath`] 屬性設定為 [`true`]。

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>焦點和視差

如上所述，當導覽事件期間，使用者介面專案是目前的專案時，會被視為_焦點_。 通常當專案進入焦點時，它的大小會稍微增加，而且會使用陰影以視覺方式提高許可權。 

如果使用者在 Siri 遙控器上進行緩慢的迴圈手勢，則焦點專案會即時回應這項移動。 當 sway 發生時，會將發光的 sheen 套用至其影像，讓表面看起來像是發光。 在指定的非使用狀態之後，任何不專注的內容都會變暗，而焦點的專案也會更大。 

這些效果共同合作，以提供電視螢幕上的內容，以及從沙發與 Apple TV 互動的使用者之間的心理連接。

在 Apple 電視上，這種視差效果會在整個系統中使用，以將3D 深度和 dynamics 的意義傳達為焦點專案。 tvOS 會使用一系列透明、[階層式影像](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images)，以動態方式移動並調整以建立此效果。

TvOS 應用程式的圖示需要分層影像，並支援動態的主要貨位內容。 雖然並非必要，但 Apple 強烈建議您在應用程式 UI 中的任何其他可設定焦點專案中，執行多層式映射。

### <a name="enabling-parallax"></a>啟用視差

`UIImageView` 控制項（或繼承自 `UIImageView`的任何控制項）都會自動支援視差效果。 根據預設，這項支援已停用，若要加以啟用，請使用下列程式碼：

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

當此屬性設定為 [`true`] 時，影像視圖會在使用者選取並聚焦于視差效果時自動取得。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋了焦點的概念，以及如何使用它來處理 tvOS 應用程式使用者介面中的導覽。 它會檢查內建的 tvOS 導覽控制項如何使用焦點、反白顯示和選取來提供導覽。 接下來，它探討了如何搭配視差和分層影像使用焦點，將目前導覽狀態的視覺線索提供給終端使用者。 最後，它會檢查使用焦點、焦點更新、將焦點放在集合中，以及啟用視差。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
