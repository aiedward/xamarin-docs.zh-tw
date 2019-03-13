---
title: 使用 tvOS 導覽和焦點在 Xamarin 中
description: 本文章涵蓋的概念，焦點，以及如何用來呈現，並在 Xamarin.tvOS 應用程式內處理巡覽。
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 1cfa51b8e5434480d7d15fbf23d78f8b8735f16a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112582"
---
# <a name="working-with-tvos-navigation-and-focus-in-xamarin"></a>使用 tvOS 導覽和焦點在 Xamarin 中

_本文章涵蓋的概念，焦點，以及如何用來呈現，並在 Xamarin.tvOS 應用程式內處理巡覽。_


本文章涵蓋的概念[焦點](#Focus-and-Selection)而且如何用來處理[瀏覽](#Navigation)Xamarin.tvOS 應用程式的使用者介面中。 我們將檢驗如何內建的 tvOS 導覽控制項將用於焦點、 反白顯示和選取項目提供 Xamarin.tvOS 應用程式的使用者介面導覽。

[![](navigation-focus-images/intro01.png "tvOS 應用程式使用者介面導覽")](navigation-focus-images/intro01.png#lightbox)

接下來，我們將探討如何焦點可以搭配[視差](#Focus-and-Parallax)並*分層式映像*提供目前的瀏覽狀態的視覺提示給使用者。

最後，我們將探討使用[焦點](#Working-with-Focus)，[焦點更新](#Working-with-Focus-Updates)，[焦點指南](#Working-with-Focus-Guides)，[集合中的焦點](#Working-with-Focus-in-Collections)和[啟用視差](#Enabling-Parallax)Xamarin.tvOS 應用程式中的映像檢視。

<a name="Navigation" />

## <a name="navigation"></a>巡覽

Xamarin.tvOS 應用程式的使用者不會互動的介面直接做為 ios，他們點選裝置的畫面上的映像但間接從之間的空間使用[Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)。 您必須記住這在設計您的應用程式使用者介面，讓它當然流動，但會保留沉浸在 Apple TV 體驗中的使用者。

成功的 tvOS 應用程式順暢地支援應用程式的目的，它會呈現而不需要呼叫本身的巡覽注意資料結構的方式實作瀏覽。 設計您的瀏覽，讓您感覺自然且熟悉而不會佔用使用者介面或繪製焦點離開內容和應用程式使用者體驗。

[![](navigation-focus-images/nav01.png "TvOS 設定應用程式")](navigation-focus-images/nav01.png#lightbox)

雖然通常使用 Apple TV、 使用者巡覽堆疊的畫面中，一組每個呈現一組指定的內容。 接著，每個新的畫面可能會導致一或多個子內容使用標準的 UI 控制項，例如畫面[按鈕](~/ios/tvos/user-interface/buttons.md)，[索引標籤列](~/ios/tvos/user-interface/tab-bars.md)，資料表[集合檢視](~/ios/tvos/user-interface/collection-views.md)或[分割檢視](~/ios/tvos/user-interface/split-views.md)。

使用資料的每個新畫面，使用者瀏覽更深層對此堆疊的畫面。 藉由使用** 功能表**按鈕上 Siri 遠端，他們可以透過瀏覽回溯堆疊，若要返回上一個畫面或主功能表。

Apple 建議下列並記住，設計您的 tvOS 應用程式的導覽時：

- **版面配置您的瀏覽，請尋找內容快速和簡單**-存取內容，點選，數目最少的應用程式內的使用者要按一下和 swipes 越好。 簡化您的瀏覽和組織的最小的數字的畫面內容。
- **建立流暢介面使用觸控**-確保使用者可以之間移動_可焦點化項目_與最小的摩擦，使用最少的可能筆勢。
- **設計具有焦點，記住**-由於房間對面，使用者正在與內容互動的因此必須將焦點移至使用者介面項目，然後再使用 Siri 遠端與其互動。 如果需要太多的筆勢，才能達成其目標，則使用者會收到挫折與您的應用程式。
- **提供向後巡覽透過功能表按鈕**-使，方便又熟悉的體驗，讓使用者巡覽向後使用 Siri 遠端** 功能表** 按鈕。 按下** 功能表**按鈕應該一律傳回上一個螢幕或傳回給應用程式的主功能表。 在應用程式的最上層，按下** 功能表**按鈕應會返回 Apple TV 主畫面。
- **通常顯示 上一頁 按鈕的避免**-因為按下** 功能表**Siri 遙控器上的按鈕向後巡覽透過螢幕堆疊，請避免顯示額外的控制項，重複此行為。 此規則的例外是購買螢幕或螢幕的破壞性動作 （例如刪除內容），其中**取消**按鈕應該顯示出來。
- **顯示大型集合在單一畫面上，而不是許多**-Siri 遠端用意是要進行內容快速的大型集合中移動，並輕鬆使用筆勢。 如果您的應用程式會使用大量的可設定焦點的項目集合，請考慮將它們放在單一的畫面，而不分成許多畫面需要更多使用者的組件上的導覽。
- **使用標準控制項進行瀏覽**-同樣地，若要建立的方便又熟悉的使用者經驗，可能的情況下，使用內建`UIKit`頁面控制項、 索引標籤列、 分段控制項、 資料表檢視中，集合檢視和分割等控制項您的應用程式導覽的檢視。 使用者已熟悉這些項目，因為它們直接易懂的方式可以瀏覽您的應用程式。
- **偏好水平內容巡覽**-Apple TV 的本質，因為撥動左到右 Siri 遠端是比向上和向下更自然。 設計您的應用程式的內容版面配置時，請考慮此選項。

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>焦點和選取範圍

在 Apple TV、 影像、 按鈕或其他 UI 項目會被視為_焦點_時目前的巡覽的目標。

[![](navigation-focus-images/focus01.png "焦點和選取範圍的範例")](navigation-focus-images/focus01.png#lightbox)

不同於，直接與裝置的觸控式螢幕這項目互動使用者的 iOS 裝置使用 Siri 遠端房間對面 tvOS 項目，從與使用者互動。 呈現，並處理這種使用者互動，使用 Apple TV_焦點_為基礎的模型。

使用筆勢和按鈕上按下[Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)，使用者將焦點從一個 UI 項目移到另一個。 一旦焦點轉移到所需的項目，使用者會按一下選取的內容，或啟用該元素所代表的動作。

當焦點變更時，難以察覺的動畫和作用 （例如映像上視差效果） 來清楚識別目前具有焦點的使用者介面項目。

Apple 具有焦點和選取範圍使用的下列建議：

- **使用內建的 UI 控制項動作效果**-使用`UIKit`和焦點 API，在使用者介面中，焦點模型會自動套用的預設動作和視覺效果至您的 UI 項目。 這可讓您覺得的應用程式，原生和 Apple TV 平台的使用者熟悉，並允許可焦點化項目之間的順暢且直覺式的移動。
- **預期的方向移動焦點**-在 Apple TV，幾乎每個項目會使用間接操作。 比方說，使用者將焦點移使用 Siri 遠端和系統會自動捲動以將目前焦點的項目保持可見的介面。 如果您的應用程式會實作這種類型的互動，請確定將焦點移動使用者筆勢的方向。 因此如果使用者權限撥動 Siri 遠端焦點應該將向右移 （這可能會導致要向左捲動的畫面）。 此規則的一個例外狀況是使用直接操作 （其中向上撥動項目向上移動） 的全螢幕項目。
- **請確定已取得焦點的項目很明顯**-因為您的使用者會與您的 UI 項目從 afar 互動時，務必目前焦點的項目凸顯出來。通常這會自動由處理內建`UIKit`項目。 自訂控制項，使用功能，例如項目大小或陰影顯示焦點。
- **使用要讓已取得焦點的項目回應視差**-小型、 循環的筆勢 Siri 遠端導致溫和的即時移動焦點的項目。 這[視差效果](#Focus-and-Parallax)內建`UIKit`_分層式映像_讓使用者能夠了解連接至焦點的項目。
- **建立適當的大小可焦點化項目**-大的項目，以充足的間距會更輕鬆地選取和瀏覽以上較小的項目。
- **設計 UI 項目看起來很好是焦點或 Unfocused** -通常 Apple TV 表示已取得焦點的項目，藉由增加其大小。 確定您的應用程式的 UI 項目看起來很讚簡報調整大小，如有需要，提供資產以供較大大小的項目。
- **代表即席焦點變更**-順暢淡出的項目之間使用動畫**Focused**並**Unfocused**將突兀無法轉換的狀態。
- **不會顯示資料指標**-使用者期望瀏覽您的應用程式 UI 使用焦點而不是由移動螢幕上的資料指標。 您的使用者介面應該一律使用焦點模式，以提供一致的使用者經驗。

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>使用焦點

有時候可能會想要建立的自訂控制項，可能會變得可設定焦點的項目。 如果因此覆寫`CanBecomeFocused`屬性，並傳回`true`，否則會傳回`false`。 例如: 

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

您可以隨時使用`Focused`屬性`UIKit`控制項，以查看它是否目前的項目。 如果`true`UI 項目目前擁有焦點，否則它並不會。 例如: 

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

雖然您無法直接將焦點移至另一個 UI 項目透過程式碼，您可以指定哪個 UI 項目第一次取得焦點，藉由設定載入螢幕時其`PreferredFocusedView`屬性設`true`。 例如: 

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>使用焦點更新 

當使用者會導致焦點從一個 UI 項目移位到另一個 （例如，在回應上 Siri 遠端筆勢）_焦點更新事件_會傳送至失去焦點的項目並取得焦點的項目。

繼承的自訂項目的`UIView`或`UIViewController`，您可以覆寫幾種方法可以使用 焦點更新事件：

- **DidUpdateFocus** -檢視獲得或失去焦點時將會呼叫這個方法。
- **ShouldUpdateFocus** -使用此方法來定義焦點可以移動。

要求焦點引擎移動焦點回到`PreferredFocusedView`UI 項目，呼叫`SetNeedsUpdateFocus`檢視控制器方法。

> [!IMPORTANT]
> 呼叫`SetNeedsUpdateFocus`如果針對呼叫它的檢視控制器包含目前具有焦點的檢視只會有作用。




<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>使用焦點輔助線

TvOS 內建的焦點引擎是適合用來處理落在水平及垂直格線的項目之間移動。 一般而言，您需要執行任何動作，多個加入至您的介面設計和焦點引擎的 UI 項目會自動處理這些開發人員介入的項目之間移動。

不過，有時候可能會，因為您的 UI 設計，若要當 UI 項目未落在水平及垂直格線，並可能會無法存取，因為它們是對角線彼此。 這是因為焦點引擎設計來處理簡單、 關閉、 Left 和 Right 只 UI 項目之間移動。

採用下列 UI 配置，例如：

 [![](navigation-focus-images/guide01.png "使用焦點指南範例")](navigation-focus-images/guide01.png#lightbox)
 
因為**更多資訊** 按鈕不是落在水平及垂直格線**購買**它會導致無法存取使用者的按鈕。 不過，要解決此問題輕鬆地使用_重點指南_焦點引擎提供移動提示。 

重點指南 (`UIFocusGuide`) 會公開為 Focusable 焦點引擎，因此必須重新導向至另一個檢視的焦點的檢視不可見區域。

因此，若要解決上述範例，下列程式碼新增到要建立焦點指南之間檢視控制器**更多資訊**並**購買**按鈕：

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

首先，新`UIFocusGuide`會建立並加入至檢視的版面配置輔助線集合使用`AddLayoutGuide`方法。

接下來，焦點指南中的頂端、 左邊、 寬度和高度的錨點會調整為相對於**更多資訊**並**購買**將它放在它們之間的按鈕。 請參閱：

[![](navigation-focus-images/guide02.png "範例重點指南")](navigation-focus-images/guide02.png#lightbox)

務必也請注意，新的條件約束啟用的設定建立他們`Active`屬性設`true`:

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

使用新的焦點指南建立並加入至檢視，檢視控制器的`DidUpdateFocus`覆寫方法並將下列程式碼加入之間移動**更多資訊**並**購買**按鈕：

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

首先，此程式碼取得的`NextFocusedView`從`UIFocusUpdateContext`中，已傳遞 (`context`)。 如果此檢視是`null`，然後不需要任何處理，並結束方法。

接下來，`nextFocusableItem`評估。 如果符合其中一個**更多資訊**或是**購買**按鈕，焦點會傳送至使用焦點指南中的 [相對] 按鈕`PreferredFocusedView`屬性。 例如: 

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

按鈕都不是焦點排班中，來源`PreferredFocusedView`清除屬性：

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>使用集合中的焦點

決定是否為個別項目可以可設定焦點中時`UICollectionView`或是`UITableView`，您將會覆寫的方法`UICollectionViewDelegate`或`UITableViewDelegate`分別。 例如: 

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

`CanFocusItem`方法會傳回`true`如果目前的項目可能會在焦點，否則會傳回`false`。

如果您想`UICollectionView`或是`UITableView`記住並焦點還原至最後一個項目將會遺失，而且重新取得焦點時，設定`RemembersLastFocusedIndexPath`屬性設`true`。

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>焦點和視差

如上所述，使用者介面項目會被視為_焦點_時目前的項目在瀏覽事件。 通常，當項目進入焦點時，它的大小會稍微增加，並以視覺化方式提高使用陰影。 

如果使用者在 Siri 遠端進行緩慢、 循環的筆勢，已取得焦點的項目將 sway 即時回應此移動。 Sway 發生時，圖案的整體呈現耀眼會套用至映像進行平台會出現在介面中。 在一段指定的閒置時間之後, 失焦的任何內容變暗，Focused 項目會成長甚至會更長。 

這些效果一起運作以提供電視螢幕上的內容與從沙發 Apple TV 與互動的使用者之間的心智連線。

在 Apple TV 中，此視差效果用在整個系統來表達出 3D 的深度和 dynamics，焦點項目。 tvOS 使用一系列的透明[分層式映像](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images)移動，並動態調整，以建立相同的效果。

層次映像檔所需的 tvOS 應用程式的圖示，並支援動態 Top Shelf 內容。 雖然並非必要，Apple 會強烈建議實作在任何其他可焦點化項目在您的應用程式 UI 中的分層式映像。

### <a name="enabling-parallax"></a>啟用視差

`UIImageView`控制項 (或任何控制項是繼承自`UIImageView`) 會自動支援視差效果。 根據預設，這項支援已停用，若要啟用它，請使用下列程式碼：

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

此屬性設定為`true`，映像檢視也會在選取的使用者，在焦點時自動取得視差效果。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋的概念，焦點，以及如何用它來處理 Xamarin.tvOS 應用程式的使用者介面中的導覽。 它會檢查內建的 tvOS 導覽控制項如何使用焦點、 反白顯示和選取項目來提供導覽。 接下來，它會討論過如何焦點可以搭配視差和分層式映像提供目前的瀏覽狀態的視覺提示給使用者。 最後，它會檢查具有焦點，焦點更新集合和啟用視差中焦點的工作。




## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
