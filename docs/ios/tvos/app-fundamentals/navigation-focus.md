---
title: "使用導覽和焦點"
description: "本文涵蓋的概念焦點時，使用處理 Xamarin.tvOS 應用程式內瀏覽及呈現方式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: fe1358d330c2a0fd94016853cedeabe094c394da
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="working-with-navigation-and-focus"></a>使用導覽和焦點

_本文涵蓋的概念焦點時，使用處理 Xamarin.tvOS 應用程式內瀏覽及呈現方式。_


本文件涵蓋的概念[焦點](#Focus-and-Selection)如何用來處理和[瀏覽](#Navigation)Xamarin.tvOS 應用程式的使用者介面中。 我們將檢驗內建 tvOS 導覽控制項如何使用焦點、 反白顯示和選取項目來提供 Xamarin.tvOS 應用程式的使用者介面導覽。

[![](navigation-focus-images/intro01.png "tvOS 應用程式使用者介面功能")](navigation-focus-images/intro01.png#lightbox)

接下來，我們將探討如何焦點可以搭配[視差](#Focus-and-Parallax)和*分層映像*目前導覽狀態的視覺提示提供給使用者。

最後，我們會探討使用[焦點](#Working-with-Focus)，[焦點更新](#Working-with-Focus-Updates)，[焦點指南](#Working-with-Focus-Guides)，[集合中的焦點](#Working-with-Focus-in-Collections)和[啟用視差](#Enabling-Parallax)Xamarin.tvOS 應用程式中的映像檢視上。

<a name="Navigation" />

## <a name="navigation"></a>巡覽

Xamarin.tvOS 應用程式的使用者不會互動的介面直接做為使用 iOS 其中他們點選裝置螢幕上的映像但間接從跨空間使用[Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)。 您必須記住這在設計您的應用程式使用者介面，使它流動自然，但會保留 immersed Apple TV 經驗的使用者。

成功 tvOS 應用程式以順利支援應用程式的目的，而不需要呼叫本身的瀏覽注意它會呈現的資料結構的方式實作瀏覽。 設計您的瀏覽，讓不含支配使用者介面或繪製焦點離開內容和應用程式的使用者經驗的感覺自然且熟悉。

[![](navigation-focus-images/nav01.png "TvOS 設定應用程式")](navigation-focus-images/nav01.png#lightbox)

雖然使用 Apple TV，使用者通常巡覽堆疊的螢幕，一組每個呈現指定的內容的集合。 接著，每個新的畫面可能會導致一個或多個子螢幕的內容使用標準的 UI 控制項，例如[按鈕](~/ios/tvos/user-interface/buttons.md)， [ 索引標籤列](~/ios/tvos/user-interface/tab-bars.md)，資料表、[集合檢視](~/ios/tvos/user-interface/collection-views.md)或[分割檢視](~/ios/tvos/user-interface/split-views.md)。

與資料的每個新畫面上，使用者瀏覽更深入到這個螢幕的堆疊。 使用**功能表**按鈕上使用 Siri 遙控器上，它們可以透過瀏覽回溯堆疊，以回到上一個螢幕或主功能表。

Apple 建議記住下列幾點，設計 tvOS 應用程式瀏覽時：

- **版面配置您的瀏覽至請尋找內容快速及簡單**-使用者要存取內容中點選，最少的應用程式內按一下，而且 swipes 越好。 簡化您的瀏覽和組織的最小的數字的螢幕的內容。
- **建立流體介面使用觸控**-確保使用者可以在之間移動_可設定焦點的項目_與使用最少的筆勢可能的最小摩擦。
- **設計具有焦點記住**-因為使用者互動的內容中，跨房間，他們需要將焦點移至使用者介面項目，然後再使用 Siri 遠端互動。 如果需要太多的筆勢，以達成其目標，則使用者會取得您的應用程式 」。
- **提供向後巡覽功能表按鈕透過**-若要建立既方便又熟悉的經驗，讓使用者巡覽向後使用 Siri 遠端**功能表** 按鈕。 按下**功能表**按鈕應該一律傳回上一個螢幕或傳回給應用程式的主功能表。 應用程式的最上層，在按下**功能表**按鈕應會返回 Apple TV 主畫面。
- **通常顯示上一步按鈕的避免**-因為按**功能表**螢幕堆疊中向後巡覽 Siri 遙控器上的按鈕，避免顯示額外的控制項，重複此行為。 此規則的例外是購買螢幕或螢幕的破壞性動作 （例如刪除內容），其中**取消**按鈕應該會顯示出來。
- **顯示在單一畫面上，而不是許多大型集合**-Siri 遠端設計用來進行大量快速內容的集合中移動並輕鬆使用筆勢。 如果您的應用程式適用於大型集合，可設定焦點的項目，請考慮將它們放在單一的畫面，而不中斷需要使用者的組件上的多個瀏覽許多螢幕。
- **使用標準控制項巡覽**-同樣地，若要建立既方便又熟悉的使用者體驗，可能的情況下，使用內建`UIKit`控制項，例如頁面控制項、 索引標籤列、 分割控制項、 資料表檢視、 集合檢視和分割您的應用程式瀏覽的檢視。 使用者已熟悉這些項目，因為它們直覺的方式可以瀏覽您的應用程式。
- **需要內容的水平巡覽**-Apple TV 的本質，因為撥動左到右 Siri 遙控器上的會比上下更自然。 設計您的應用程式的內容配置時，請考慮此選項。

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>焦點和選取範圍

Apple 電視、 影像、 按鈕或其他 UI 項目會被視為_焦點_時的目前的巡覽目標。

[![](navigation-focus-images/focus01.png "焦點和選取範圍的範例")](navigation-focus-images/focus01.png#lightbox)

不同於，iOS 裝置在使用者直接互動上裝置的觸控螢幕，項目時使用 Siri 遙控器的遙控器輕鬆 tvOS 項目從與使用者互動。 若要顯示和處理這種使用者互動，使用 Apple TV_焦點_基礎模型。

使用筆勢和按鈕按下上[Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)，使用者將焦點從一個 UI 項目移至另一個。 一旦焦點已移至所需的項目，使用者按一下選取的內容或啟動該元素所代表的動作。

當焦點變更時，難以察覺的動畫和作用 （例如，在影像上視差效果） 可用來清楚識別目前具有焦點的使用者介面項目。

Apple 已使用焦點和選取範圍的下列建議：

- **使用內建的 UI 控制項之影片效果**-使用`UIKit`焦點 API，在您的使用者介面，焦點模型會自動套用的預設動作和視覺效果的 UI 元素。 這可讓您覺得的應用程式原生和 Apple TV 平台的使用者所熟悉，並允許可設定焦點的項目之間的順暢且直覺式的移動。
- **在預期的方向移動焦點**-在 Apple TV，幾乎每個項目使用間接操作。 例如，使用者將焦點移使用 Siri 遠端，系統會自動捲動以將目前的焦點項目保持可見的介面。 如果您的應用程式會實作這種互動，請確定焦點移使用者筆勢的方向。 因此如果使用者以滑鼠右鍵撥動 Siri 遠端焦點應該將向右移 （這會使得向左捲動至畫面）。 此規則的一個例外狀況是使用直接操作 （其中向上撥動項目向上移動） 的全螢幕項目。
- **請確定已取得焦點的項目是 Obvious** -因為您的使用者互動從 afar UI 項目，很重要的目前的焦點項目凸顯出來。通常這將會自動處理內建的`UIKit`項目。 自訂控制項，使用功能，例如項目大小或陰影顯示焦點。
- **使用來進行已取得焦點的項目回應視差**-小型、 簡單、 即時地移動焦點的項目會造成循環 Siri 遙控器上的手勢。 這[視差效果](#Focus-and-Parallax)內建`UIKit`_分層映像_給予焦點的項目要連接的意義上的使用者。
- **建立適當的大小可設定焦點的項目**-大的項目以很大的間距會更輕鬆地選取及導覽於較小的項目。
- **設計 UI 項目看起來不錯可能是已取得焦點或 Unfocused** -通常 Apple TV 表示已取得焦點的項目，藉由增加其大小。 請確認您的應用程式 UI 項目看起來不錯任何簡報尺寸，必要時，提供資產的大小較大的項目。
- **代表即席焦點變更**-使用項目之間順暢地淡出動畫**Focused**和**Unfocused**保留突兀轉換正在執行的狀態。
- **不會顯示一個資料指標**-使用者希望瀏覽您的應用程式的 UI 使用焦點而不是移動螢幕上的資料指標。 您的使用者介面應該一律使用焦點模型以提供一致的使用者經驗。

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>使用焦點

有些時候可能會想要建立的自訂控制項，可能會變得可設定焦點的項目。 如果因此覆寫`CanBecomeFocused`屬性和傳回`true`，否則會傳回`false`。 例如: 

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

您可以隨時使用`Focused`屬性`UIKit`控制項以查看是否為目前的項目。 如果`true`UI 項目目前具有焦點，否則它並不會。 例如: 

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

雖然您無法直接將焦點移至另一個 UI 項目透過程式碼，您可以指定哪些 UI 項目第一次取得焦點，藉由設定載入螢幕時其`PreferredFocusedView`屬性`true`。 例如: 

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>使用焦點更新 

使用者何時會導致焦點從一個 UI 項目會轉移至另一個 （例如，在回應 Siri 遙控器上的手勢）_焦點更新事件_將傳送至失去焦點的項目並取得焦點的項目。

對於繼承的自訂項目`UIView`或`UIViewController`，您可以覆寫焦點更新事件所使用的數種方法：

- **DidUpdateFocus**集的隨時檢視獲得或失去焦點會呼叫這個方法。
- **ShouldUpdateFocus** -使用這個方法來定義焦點能夠移動。

若要要求焦點引擎移動焦點回`PreferredFocusedView`UI 項目，呼叫`SetNeedsUpdateFocus`檢視控制器方法。

> [!IMPORTANT]
> 呼叫`SetNeedsUpdateFocus`如果呼叫時即是針對檢視控制器包含目前具有焦點的檢視才會有作用。




<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>使用焦點輔助線

內建 tvOS 焦點引擎最適合在處理落在水平及垂直格線的項目之間移動。 一般來說，您必須為不執行任何動作，多個加入至您的設計介面和焦點引擎的 UI 項目將會自動處理這些項目，不用開發人員介入之間移動。

不過，有些時候可能會，因為您的 UI 設計必要條件當 UI 項目不落在水平及垂直格線，且可能無法存取，因為它們是對角線彼此。 這是因為焦點引擎設計來處理簡單，向下、 Left 和 Right 只 UI 項目之間移動。

採用下列 UI 配置的範例：

 [![](navigation-focus-images/guide01.png "使用焦點指南範例")](navigation-focus-images/guide01.png#lightbox)
 
因為**更多資訊**按鈕未落在水平及垂直格線，內含**購買**它會導致無法存取使用者的按鈕。 但是，這可以輕鬆地修正使用_焦點指南_對焦點引擎提供移動提示。 

焦點指南 (`UIFocusGuide`) 會公開為 Focusable 焦點引擎，因此可允許重新導向至另一個檢視的焦點的檢視不可見區域。

因此，若要解決上述範例，下列程式碼可能會加入至檢視在控制器建立焦點指南之間**更多資訊**和**購買**按鈕：

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

接下來，相對於調整焦點指南的頂端、 左邊、 寬度和高度起點**更多資訊**和**購買**放在它們之間的按鈕。 請參閱：

[![](navigation-focus-images/guide02.png "範例焦點指南")](navigation-focus-images/guide02.png#lightbox)

它也是很重要的一點藉由設定建立時，會被啟動新的條件約束及其`Active`屬性`true`:

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

與新的焦點指南建立並加入至檢視，檢視控制器的`DidUpdateFocus`覆寫方法並將下列程式碼加入之間移動**更多資訊**和**購買**按鈕：

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

首先，此程式碼取得的`NextFocusedView`從`UIFocusUpdateContext`，已傳入 (`context`)。 如果此檢視是`null`，則不需要進行任何處理，並結束方法。

下一步`nextFocusableItem`評估。 如果它比對**更多資訊**或**購買**按鈕時，焦點會傳送至使用焦點指南相反按鈕`PreferredFocusedView`屬性。 例如: 

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

按鈕都不做為來源的焦點移位`PreferredFocusedView`清除屬性：

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>使用在集合中的焦點

決定是否可以可設定焦點，在個別的項目時`UICollectionView`或`UITableView`，您將會覆寫的方法`UICollectionViewDelegate`或`UITableViewDelegate`分別。 例如: 

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

`CanFocusItem`方法會傳回`true`如果目前的項目可以是在擁有焦點，否則它會傳回`false`。

如果您想`UICollectionView`或`UITableView`記住並還原焦點到最後一個項目將會遺失，重新取得焦點時，設定`RemembersLastFocusedIndexPath`屬性`true`。

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>焦點和視差

如同前面所述，使用者介面項目會被視為_焦點_時的目前項目在瀏覽事件。 通常，項目進入焦點時，它的大小稍微增加，並以視覺化方式提高使用陰影。 

如果使用者會變慢，循環的筆勢 Siri 遙控器上，已取得焦點的項目將 sway 即時回應此移動。 Sway 過程圖案的光澤會套用到它的映像進行平台會出現在介面中。 指定一段閒置時間之後, 任何失焦內容變暗，集中項目會成長甚至更大。 

這些效果共同運作以提供心理電視螢幕上的內容與使用者互動沙發上從 Apple TV 之間的連接。

Apple 節目這個視差效果是整個系統用來傳達的 3D 深度和 dynamics 將重點放在項目。 tvOS 使用一系列的透明，[分層映像](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images)移動，並動態調整，以建立這個效果。

分層的映像所需的 tvOS 應用程式的圖示，並支援動態的上架內容。 雖然並非必要，Apple 強烈建議在您的應用程式 UI 中的其他任何可設定焦點項目中實作分層映像。

### <a name="enabling-parallax"></a>啟用視差

`UIImageView`控制項 (或繼承自任何控制項`UIImageView`) 會自動支援視差效果。 根據預設，這項支援已停用，若要啟用它，請使用下列程式碼：

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

此屬性設定為`true`，選取使用者，在焦點時，影像檢視系統會自動顯示視差效果。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋焦點時，如何使用它來處理 Xamarin.tvOS 應用程式的使用者介面中導覽的概念。 它會檢查內建 tvOS 導覽控制項如何使用焦點、 反白顯示和選取項目來提供導覽。 接下來，它看起來如何焦點可以使用與視差和層級的映像目前導覽狀態的視覺提示提供給使用者。 最後，它會檢查具有焦點，焦點更新中集合和啟用視差焦點的工作。




## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
