---
title: 在 Xamarin 中使用 tvOS 導覽和焦點
description: 本文涵蓋焦點的概念，以及如何在 tvOS 應用程式內呈現和處理導覽。
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 844da10aaf5882ce8c381ee2407dbf91e0bc6c89
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437105"
---
# <a name="working-with-tvos-navigation-and-focus-in-xamarin"></a>在 Xamarin 中使用 tvOS 導覽和焦點

_本文涵蓋焦點的概念，以及如何在 tvOS 應用程式內呈現和處理導覽。_

本文涵蓋 [焦點](#Focus-and-Selection) 的概念，以及如何用它來處理 Xamarin. tvOS 應用程式消費者介面中的 [導覽](#Navigation) 。 我們將探討內建的 tvOS 導覽控制項如何使用焦點、反白顯示和選取專案，以提供您的 tvOS 應用程式消費者介面導覽。

[![tvOS apps 消費者介面導覽](navigation-focus-images/intro01.png)](navigation-focus-images/intro01.png#lightbox)

接下來，我們將探討如何搭配 [視差](#Focus-and-Parallax) 和 *分層影像* 使用焦點，以提供目前流覽狀態給終端使用者的視覺線索。

最後，我們將探討如何使用 [焦點](#Working-with-Focus)、 [焦點更新](#Working-with-Focus-Updates)、 [焦點指南](#Working-with-Focus-Guides)、將 [焦點放在集合](#Working-with-Focus-in-Collections) 中，以及在 TvOS 應用程式的影像視圖上 [啟用視差](#enabling-parallax) 。

<a name="Navigation"></a>

## <a name="navigation"></a>巡覽

您的 tvOS 應用程式的使用者將不會像 iOS 一樣直接與其介面互動，因為它們會在裝置的螢幕上點出影像，但會使用 [Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)在房間間間接地進行。 當您設計應用程式的消費者介面時，您必須記住這一點，如此一來，它就可以自然地流動，但會讓使用者可以沉浸在 Apple TV 體驗中。

成功的 tvOS 應用程式會以順暢地支援應用程式用途的方式，以及它所呈現的資料結構來執行導覽，而不會注意到流覽本身。 設計您的流覽，使其自然且熟悉，而不支配消費者介面或將焦點放在內容和應用程式使用者體驗之外。

[![TvOS 設定應用程式](navigation-focus-images/nav01.png)](navigation-focus-images/nav01.png#lightbox)

使用 Apple TV 時，使用者通常會流覽一組堆疊的畫面，每個畫面都會呈現一組指定的內容。 接著，每個新的畫面可能會使用標準 UI 控制項（例如 [按鈕](~/ios/tvos/user-interface/buttons.md)、索引標籤 [列、資料表](~/ios/tvos/user-interface/tab-bars.md)、 [集合視圖](~/ios/tvos/user-interface/collection-views.md) 或 [分割視圖](~/ios/tvos/user-interface/split-views.md)），來導致內容的一個或多個子畫面。

在每個新的資料畫面上，使用者流覽到這個畫面堆疊的深度。 您可以使用 Siri 遠端上的 [ **功能表** ] 按鈕，在堆疊中向後導覽，以返回上一個畫面或主功能表。

針對您的 tvOS 應用程式設計導覽時，Apple 建議您牢記下列事項：

- 配置**您的導覽，讓您可以快速且輕鬆地**在您的應用程式中以最少的點擊次數來存取應用程式中的內容，並盡可能地按一下和撥動。 簡化您的流覽，並將內容組織到最少量的畫面。
- **使用觸控來建立流暢的介面** -確保使用者可以使用最少的手勢數目，在可 _設定的專案_ 之間移動最少的分歧。
- 將**焦點放在設計**上，因為使用者會在房間內與內容互動，所以必須先將焦點移至消費者介面專案，才能使用 Siri 遠端與它互動。 如果您的應用程式需要太多手勢才能達成其目標，則使用者將會對您的應用程式感到挫折。
- 透過**功能表按鈕提供回溯導覽**，以建立簡單且熟悉的體驗，讓使用者可以使用 Siri 遠端的**功能表**按鈕回溯導覽。 按下 **功能表** 按鈕應該一律回到上一個畫面，或返回應用程式的主功能表。 在應用程式的最上層，按下 **功能表** 按鈕應該會返回 Apple TV 首頁畫面。
- **通常會避免顯示 [上一頁] 按鈕** ，因為按下 Siri 遙控器上的 [ **功能表** ] 按鈕，就會向後流覽整個螢幕堆疊，避免顯示覆制此行為的額外控制項。 這項規則的例外狀況是用來購買具有破壞性動作的螢幕或畫面 (例如刪除內容) 也應該顯示 [ **取消** ] 按鈕。
- **在單一畫面上顯示大型集合，而不是許多** -Siri 遠端的設計目的是要利用手勢快速輕鬆地移動大型內容集合。 如果您的應用程式使用大型的可設定專案集合，請考慮將它們保存在單一畫面，而不是將它們分成多個畫面，而這些畫面需要更多使用者的導覽。
- **使用標準控制項來進行流覽** -同樣地，您可以在可能的情況下，使用內建控制項（例如 `UIKit` 頁面控制項、索引標籤列、分割的控制項、資料表視圖、集合視圖，以及應用程式導覽的分割視圖）來建立簡單且熟悉的使用者體驗。 由於使用者已熟悉這些元素，因此可以直覺地流覽您的應用程式。
- 偏好**水準內容導覽**-因為 Apple TV 的本質，所以 Siri 遠端的向左輕量比向上和向下更自然。 設計應用程式的內容版面配置時，請考慮此選項。

<a name="Focus-and-Selection"></a>

## <a name="focus-and-selection"></a>焦點和選取範圍

在 Apple 電視上，如果影像、按鈕或其他 UI 元素是目前導覽的目標，則會被視為 _焦點_ 。

[![焦點和選取範例](navigation-focus-images/focus01.png)](navigation-focus-images/focus01.png#lightbox)

不同于 iOS 裝置，使用者會直接與裝置的觸控觸控上的元素互動，而使用者會使用 Siri 遠端與房間間的 tvOS 元素互動。 為了呈現和處理這種使用者互動，Apple TV 使用以 _焦點_ 為基礎的模型。

在 [Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)使用筆勢和按鈕，使用者可將焦點從某個 UI 專案移至另一個 UI 元素。 當焦點移至所需的專案之後，使用者可以按一下來選取內容，或啟用該專案所代表的動作。

當焦點變更時，會使用微妙的動畫和效果 (例如影像) 的視差效果，用來清楚識別目前擁有焦點的消費者介面專案。

Apple 有下列建議來處理焦點和選取：

- **針對動畫效果使用內建的 UI 控制項** -藉由 `UIKit` 在您的消費者介面中使用焦點 API，焦點模型會自動將預設的動作和視覺效果套用至您的 UI 元素。 這可讓您的應用程式以原生且熟悉的 Apple TV 平臺使用者，並可讓您在可設定的專案之間進行流暢且直覺的移動。
- **以預期的方向移動焦點** -在 Apple 電視上，幾乎每個元素都使用間接操作。 例如，使用者使用 Siri 遠端來移動焦點，系統會自動滾動介面，讓目前焦點的專案可見。 如果您的應用程式會執行這種類型的互動，請確定焦點會以使用者的手勢方向移動。 如此一來，如果使用者在 Siri 遠端焦點上向右滑動，則應該移至右邊的 (這可能會導致畫面滾動至左邊的) 。 這項規則的唯一例外是使用直接操作 (的全螢幕專案，在此情況下，會將元素上移) 。
- **請確定焦點專案很明顯** ，因為您的使用者會從 afar 與您的 UI 元素互動，所以目前焦點的專案是很重要的。這通常會由內建元素自動處理 `UIKit` 。 若是自訂控制項，請使用專案大小或陰影等功能來顯示焦點。
- **使用視差讓焦點專案具有回應能力** -小型、Siri 遙控器上的迴圈手勢，可即時移動焦點專案。 這項 [視差效果](#Focus-and-Parallax) 內建在多 `UIKit` _層式影像_ 中，讓使用者有權連接到焦點專案。
- **建立適當大小的可設定焦點專案** -具有足夠間距的大型專案，比較小的專案更容易選取和流覽。
- **將 UI 專案設計成美觀或未取得焦點** ，通常 Apple TV 會藉由增加其大小來表示焦點專案。 確定您的應用程式 UI 元素看起來很棒，如有必要，請提供適用于較大大小專案的資產。
- **表示焦點變更流暢地** -使用動畫可順暢地淡化 **焦點** 和 **未取得焦點** 狀態的專案，以防止轉換 jarring。
- **不要顯示資料指標** -使用者預期會使用焦點來流覽您應用程式的 UI，而不是藉由將游標移到螢幕周圍。 您的消費者介面應該一律使用焦點模型，以提供一致的使用者體驗。

<a name="Working-with-Focus"></a>

### <a name="working-with-focus"></a>使用焦點

有時候您可能會想要建立自訂控制項，而該控制項可以成為可設定焦點的專案。 如果是，則會覆寫 `CanBecomeFocused` 屬性並傳回 `true` ，否則會傳回 `false` 。 例如：

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

您隨時都可以使用控制項的 `Focused` 屬性 `UIKit` 來查看它是否為目前的專案。 如果 `true` UI 專案目前具有焦點，則為，否則為。 例如：

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

雖然您無法透過程式碼直接將焦點移至另一個 UI 專案，但是您可以在將其屬性設定為的情況下，指定在載入螢幕時，哪個 UI 元素會先取得焦點 `PreferredFocusedView` `true` 。 例如：

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates"></a>

### <a name="working-with-focus-updates"></a>使用焦點更新 

當使用者讓焦點從某個 UI 元素移至另一個 UI 專案時 (例如，為了回應 Siri 遠端) 的 _焦點更新事件_ 將會傳送至遺失焦點的專案，而取得焦點。

針對繼承自或的自訂專案 `UIView` `UIViewController` ，您可以覆寫數個方法來處理焦點更新事件：

- **DidUpdateFocus** -每當視圖獲得或失去焦點時，就會呼叫這個方法。
- **ShouldUpdateFocus** -使用此方法來定義允許移動焦點的位置。

若要要求焦點引擎將焦點移回 `PreferredFocusedView` UI 元素，請呼叫 `SetNeedsUpdateFocus` View 控制器的方法。

> [!IMPORTANT]
> `SetNeedsUpdateFocus`只有當呼叫的 View Controller 包含目前擁有焦點的視圖時，呼叫才會有作用。

<a name="Working-with-Focus-Guides"></a>

### <a name="working-with-focus-guides"></a>使用焦點指南

內建在 tvOS 中的焦點引擎很適合用來處理水準和垂直方格上的專案之間的移動。 一般來說，您只需要將 UI 元素新增至介面設計，焦點引擎就會自動處理這些元素之間的移動，而不需要開發人員介入。

不過，有時可能會因為您的 UI 設計必要條件而無法在水準和垂直方格上進行，而且可能無法存取，因為它們彼此的對角線。 發生這種情況是因為焦點引擎是設計用來處理 UI 專案之間的簡單向上、向下、向左和右移動。

如需範例，請使用下列 UI 版面配置：

 [![使用焦點指南範例](navigation-focus-images/guide01.png)](navigation-focus-images/guide01.png#lightbox)

因為 [ **更多資訊** ] 按鈕不會落在具有 [ **購買** ] 按鈕的水準和垂直方格上，所以使用者無法存取。 不過，您可以使用 _焦點指南_ 輕鬆地修正此情況，以提供移動提示給焦點引擎。 

焦點指南 (`UIFocusGuide`) 會將視野的非可見區域公開為焦點引擎的可設定焦點，進而讓焦點重新導向至另一個視圖。

因此，若要解決上述的範例，可以將下列程式碼新增至 View Controller，以在 [ **詳細資訊** ] 和 [ **購買** ] 按鈕之間建立焦點指南：

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

首先， `UIFocusGuide` 會使用方法來建立新的，並將其加入至視圖的版面配置輔助線集合 `AddLayoutGuide` 。

接下來，焦點指南的上方、左方、寬度和高度錨點會相對於 [ **詳細資訊** ] 和 [ **購買** ] 按鈕進行調整，以便在兩者之間放置。 請參閱：

[![範例焦點指南](navigation-focus-images/guide02.png)](navigation-focus-images/guide02.png#lightbox)

也請務必注意，在建立新的條件約束時，會將其 `Active` 屬性設定為 `true` ：

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

建立新的焦點指南並將其加入至視圖之後，您就可以覆寫視圖控制器的 `DidUpdateFocus` 方法，並加入下列程式碼，以在 [ **詳細資訊** ] 和 [ **購買** ] 按鈕之間移動：

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

首先，此 `NextFocusedView` `UIFocusUpdateContext` 程式碼會從 () 傳入的取得 `context` 。 如果這個視圖為 `null` ，則不需要處理，也不會結束方法。

接下來 `nextFocusableItem` 會評估。 如果它符合 [ **詳細資訊** ] 或 [ **購買** ] 按鈕，則會使用焦點指南的屬性將焦點傳送至相反的按鈕 `PreferredFocusedView` 。 例如：

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

如果兩個按鈕都不是焦點排班的來源， `PreferredFocusedView` 就會清除此屬性：

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections"></a>

### <a name="working-with-focus-in-collections"></a>使用集合中的焦點

當您決定個別專案是否可在或中成為可設定焦點時 `UICollectionView` `UITableView` ，您將覆寫或分別覆寫的方法 `UICollectionViewDelegate` `UITableViewDelegate` 。 例如：

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

`CanFocusItem` `true` 如果目前的專案可以是焦點，則方法會傳回，否則會傳回 `false` 。

如果您想要在 `UICollectionView` 最後一個專案遺失和重新 `UITableView` 獲得焦點時記住或還原焦點，請將 `RemembersLastFocusedIndexPath` 屬性設定為 `true` 。

<a name="Focus-and-Parallax"></a>

## <a name="focus-and-parallax"></a>焦點和視差

如上所述，當您在流覽事件期間成為目前的專案時，會將消費者介面元素視為 _焦點_ 。 一般來說，當專案成為焦點時，它的大小會稍微增加，而且會使用陰影以視覺化方式提升許可權。 

如果使用者在 Siri 遠端進行緩慢的迴圈手勢，則焦點的專案會即時回應此移動。 當 sway 發生時，會將有向的的 sheen 套用至其影像，讓表面看起來更加亮。 在指定的非使用狀態之後，任何專注的內容都會變暗，而且焦點的專案會變得更大。 

這些效果會一起運作，以在電視螢幕上的內容與從沙發與 Apple TV 互動的使用者之間提供精神連接。

在 Apple 電視上，此視差效果是在整個系統中使用，以便將3D 深度和 dynamics 的意義傳達給焦點專案。 tvOS 會使用一系列透明的 [分層影像](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images) ，以動態方式移動和調整以建立此效果。

您的 tvOS 應用程式圖示需要多層式影像，並支援動態的最大貨架內容。 雖然並非必要，但 Apple 強烈建議您在應用程式 UI 中的任何其他可設定焦點專案中，執行多層式影像。

### <a name="enabling-parallax"></a>啟用視差

`UIImageView`控制項 (或繼承自) 的任何控制項， `UIImageView` 都會自動支援視差效果。 此支援預設為停用，若要啟用，請使用下列程式碼：

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

當這個屬性設定為 `true` 時，影像視圖會在使用者選取並以焦點顯示時，自動取得視差效果。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了焦點的概念，以及如何用它來處理 tvOS 應用程式消費者介面中的導覽。 它會檢查內建的 tvOS 導覽控制項如何使用焦點、反白顯示和選取以提供導覽。 接下來，它會探討如何搭配視差和分層影像使用焦點，以提供目前流覽狀態給終端使用者的視覺線索。 最後，它會檢查焦點、焦點更新、專注于集合中，以及啟用視差。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)