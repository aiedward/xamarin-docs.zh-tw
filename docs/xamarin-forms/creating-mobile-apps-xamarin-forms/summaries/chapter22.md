---
title: "章 22 的摘要。 動畫"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: df0647e869fcf37a3a1dfdeb6f3dbf1e7d07ad95
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-22-animation"></a>章 22 的摘要。 動畫

您所見，您可以建立您自己使用 Xamarin.Forms 計時器的動畫或`Task.Delay`，但通常更容易使用 Xamarin.Forms 所提供的動畫功能。 三個類別會實作這些動畫：

- [`ViewExtensions`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)高階方法
- [`Animation`](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)但更難更多功能
- [`AnimationExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)最多用途、 最低層級的方法

一般而言，動畫目標都由可繫結屬性的屬性。 這並不是需求，但這些是動態回應變更的唯一屬性。

這些動畫，任何 XAML 介面，但您可以將動畫整合到使用中討論的技術的 XAML [**章 23。觸發程序和行為**](chapter23.md)。

## <a name="exploring-basic-animations"></a>瀏覽的基本動畫

基本動畫函式是擴充方法位於[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)類別。 這些方法套用至任何物件衍生自`VisualElement`。 最簡單的動畫目標內容所述的轉換[ `Chapter 21. Transforms` ](chapter21.md)。

[ **AnimationTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout)示範如何`Clicked`事件處理常式`Button`可以呼叫[ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)轉動的擴充方法在圓形中的按鈕。

`RotateTo`方法變更`Rotation`屬性`Button`從 0 到 360 一季的第二個 （依預設） 的過程。 如果`Button`點選一次，但是，它不會執行任何動作因為`Rotation`內容已出現 360 度。

### <a name="setting-the-animation-duration"></a>設定動畫持續時間

第二個引數`RotateTo`是以毫秒為單位的時間長度。 如果設定為較大的值，點選`Button`在動畫期間會啟動新的動畫開始在目前的角度。

### <a name="relative-animations"></a>相對的動畫

`RelRotateTo`方法，執行相對旋轉指定的值加入至現有的值。 這個方法可讓`Button`多次，而每一個要點選的時間增加`Rotation`360 度的屬性。

### <a name="awaiting-animations"></a>正在等待動畫

中的所有動畫方法`ViewExtensions`傳回`Task<bool>`物件。 這表示您可以定義一系列使用循序動畫`ContinueWith`或`await`。 `bool`完成傳回的值為`false`動畫完成而不會中斷或`true`如果它已被取消[ `CancelAnimation` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/)方法，這樣會取消所起始的所有動畫中的其他方法`ViewExtensions`相同的項目上設定。

### <a name="composite-animations"></a>複合動畫

您可以混合受期待與非等候建立複合的動畫的動畫。 這些是在動畫`ViewExtensions`目標`TranslatonX`， `TranslationY`，和`Scale`轉換屬性：

- [`TranslateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/)
- [`ScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.ScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)
- [`RelScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)

請注意，`TranslateTo`可能會同時影響`TranslationX`和`TranslationY`屬性。

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAll 和 Task.WhenAny

也可以管理使用同時動畫[ `Task.WhenAll` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.WhenAll%7BTResult%7D/p/System.Collections.Generic.IEnumerable%7BSystem.Threading.Tasks.Task%7BTResult%7D%7D/)，這表示當所有得到多個工作，和[ `Task.WhenAny` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.WhenAny%7BTResult%7D/p/System.Collections.Generic.IEnumerable%7BSystem.Threading.Tasks.Task%7BTResult%7D%7D/)，這表示當數個中的第一個工作已結束。

### <a name="rotation-and-anchors"></a>輪替與錨點

當呼叫`ScaleTo`， `RelScaleTo`， `RotateTo`，和`RelRotateTo`方法，您可以設定[ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/)和[ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)屬性來表示縮放和旋轉的中心。

[ **CircleButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton)示範這項技巧的上方`Button`周圍頁面的中央。

### <a name="easing-functions"></a>Easing 函式

通常是線性從開始值的結束值的動畫。 Easing 函式可能會造成動畫加速或透過其課程變慢。 動畫方法的最後一個選擇性引數是型別[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)，一個類別來定義 11 靜態唯讀欄位的型別`Easing`:

- [`Linear`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/)預設值
- [`SinIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinIn/)[ `SinOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinOut/)，和 [`SinInOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinInOut/)
- [`CubicIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicIn/)[ `CubicOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicOut/)，和 [`CubicInOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicInOut/)
- [`BounceIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceIn/) 和 [`BounceOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceOut/)
- [`SpringIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) 和 [`SpringOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/)

`In`後置詞表示的影響是在動畫，開頭`Out`在結束時，表示和`InOut`表示它是在開頭和結尾的動畫。

[ **BounceButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton)範例將示範如何使用 easing 函式。

### <a name="your-own-easing-functions"></a>Easing 函式

您也可以定義您自己的 easing 函式藉由傳遞[ `Func<double, double>` ](https://developer.xamarin.com/api/type/System.Func%3CT,TResult%3E/)至[`Easing`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Easing.Easing/p/System.Func%7BSystem.Double,System.Double%7D/)。 `Easing` 也會定義從的隱含轉換`Func<double, double>`至`Easing`。 加/減速函數的引數會在 0 到 1 的範圍內動畫以線性方式進行時從開頭至結尾。 此函式*通常*傳回 0 到 1 的範圍中的值，但可以簡短負值或大於 1 (使用案例`SpringIn`和`SpringOut`函式) 或可能違反規則，如果您知道您所做。

[ **UneasyScale** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale)範例將示範如何自訂 easing 函式和[ **CustomCubicEase** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase)示範另一個。

[ **SwingButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton)範例也會示範自訂 easing 函式，以及變更的技術`AnchorX`和`AnchorY`的旋轉動畫序列內的屬性。

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)媒體櫃具有[ `JiggleButton` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) jiggle 按鈕按一下時其函式，使用自訂 easing 類別。 [ **JiggleButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo)範例會示範它。

### <a name="entrance-animations"></a>進入動畫

當第一次出現的頁面時，就會發生一種常用動畫。 可以在啟動這類動畫[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/)頁面的覆寫。 這些的動畫效果最佳顯示設定適用於您要如何頁面的 XAML*之後*動畫，然後初始化，並以動畫顯示從程式碼的配置。

[ **FadingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance)範例會使用[ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)淡出頁面的內容中的擴充方法。

[ **SlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance)範例會使用[ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/)擴充方法，從側邊的投影片中頁面的內容。

[ **SwingingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance)範例會使用[ `RotateYTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)擴充方法，以動畫方式顯示`RotationY`屬性。 A [ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法也會提供。

### <a name="forever-animations"></a>永遠動畫

另一端，"forever"動畫執行，直到程式結束。 這些通常是為了示範之用。

[ **FadingTextAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation)範例會使用[ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)淡出兩個部份的文字往返的動畫。

[**PalindromeAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation)顯示 palindrome，，然後依序將個別的字母旋轉 180 度所以所有顛倒。 然後整個字串讀取相同的原始字串翻轉 180 度。

[ **CopterAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation)範例旋轉簡單`BoxView`直昇機時上方中央螢幕的周圍。

[**RotatingSpokes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes)涉及`BoxView`支點周圍螢幕的中央，然後將每個支點本身建立有趣的模式：

[![三個螢幕擷取畫面的旋轉 Spokes](images/ch22fg21-small.png "旋轉 Spokes")](images/ch22fg21-large.png#lightbox "旋轉 Spokes")

不過，逐漸增加`Rotation`某個項目的屬性可能不適用於長期來看，做為[ **RotationBreakdown** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown)範例將示範如何。

[ **SpinningImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage)範例會使用[ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)， [ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)，和[ `RotateYTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)讓它看起來像點陣圖，如同要輪替在 3D 空間中。

### <a name="animating-the-bounds-property"></a>建立繫結屬性的動畫

只有擴充方法中的`ViewExtensions`尚未示範是[ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/)，其有效地以動畫方式顯示唯讀[ `Bounds` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Bounds/)屬性，藉由呼叫[`Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/)方法。 這個方法通常會呼叫`Layout`衍生項目，如中所討論的[**章 26。CustomLayouts**](chapter26.md)。

`LayoutTo`方法應該限制為特殊用途。 [ **BouncingBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox)程式會使用它來壓縮和展開`BoxView`如它撞頁面側邊。

[ **XamagonXuzzle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)範例會使用`LayoutTo`移動磚的傳統實作 15-16 個謎題，會顯示變碼的映像，而不是已編號的磚：

[![三個螢幕擷取畫面的 Xamarin Xuzzle](images/ch22fg26-small.png "Xuzzle 拼圖遊戲")](images/ch22fg26-large.png#lightbox "Xuzzle 拼圖遊戲")

### <a name="your-own-awaitable-animations"></a>Awaitable 動畫

[ **TryAwaitableAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation)範例會建立 awaitable 動畫。 可以傳回的重要類別`Task`物件的方法和訊號動畫完成時從[ `TaskCompletionSource` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.TaskCompletionSource%3CTResult%3E/)。

## <a name="deeper-into-animations"></a>更深入到動畫

Xamarin.Forms 動畫系統可能會有點造成混淆。 除了`Easing`類別，動畫系統包含`ViewExtensions`， `Animation`，和`AnimationExtension`classses。

### <a name="viewextensions-class"></a>ViewExtensions 類別

您已經看到[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)。 它定義的九個方法會傳回`Task<bool>`和[ `CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/)。 七個九方法目標轉換屬性。 其他兩個是[ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)，哪一個目標[ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/)屬性，和[ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/)，而它會呼叫[ `Layout`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/)方法。

### <a name="animation-class"></a>動畫類別

[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)類別具有[建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Animation.Animation/p/System.Action%7BSystem.Double%7D/System.Double/System.Double/Xamarin.Forms.Easing/System.Action/)具有五個引數來定義回呼及完成的方法和參數的動畫。

可以新增子動畫[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/)， [ `Insert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Insert/p/System.Double/System.Double/Xamarin.Forms.Animation/)， [ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/Xamarin.Forms.Animation/System.Double/System.Double/)，和與多載[ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/System.Action%7BSystem.Double%7D/System.Double/System.Double/Xamarin.Forms.Easing/System.Double/System.Double/).

然後啟動動畫物件呼叫[ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BSystem.Double,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/)方法。

### <a name="animationextensions-class"></a>AnimationExtensions 類別

[ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)類別包含大部分的擴充方法。 有數個版本的`Animate`方法和泛型[ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate%7BT%7D/p/Xamarin.Forms.IAnimatable/System.String/System.Func%7BSystem.Double,T%7D/System.Action%7BT%7D/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BT,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/)方法是多功能其實您需要唯一的動畫函式。

## <a name="working-with-the-animation-class"></a>使用動畫類別

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)範例將示範如何[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)類別有數個不同的動畫。

### <a name="child-animations"></a>其子動畫

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)範例也示範動畫，請使用 （非常類似） 的子系[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/)和[ `Insert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Insert/p/System.Double/System.Double/Xamarin.Forms.Animation/)方法。

### <a name="beyond-the-high-level-animation-methods"></a>除了高層級的動畫方法

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)範例也示範如何執行超越屬性設為目標的動畫`ViewExtensions`方法。 在一個範例中，一系列的期間取得較長。在另一個範例中，`BackgroundColor`屬性動畫效果。

### <a name="more-of-your-own-awaitable-methods"></a>多個您自己的 awaitable 方法

[ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法`ViewExtensions`無法搭配[ `Easing.SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/)函式。 它會停止時加/減速輸出取得高於 1。

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)文件庫包含[ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)類別[ `TranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12)和[ `TranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49)擴充方法沒有這個問題，以及[ `CancelTranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44)和[ `CancelTranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71)取消那些方法動畫。

[ **SpringSlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance)示範`TranslateXTo`方法。

`MoreExtensions`類別也包含[ `TranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76)結合 X 和 Y 轉譯的擴充方法和[ `CancelTranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113)方法。

### <a name="implementing-a-bezier-animation"></a>實作貝茲動畫

它也可開發動畫移動的貝茲曲線在路徑中的項目。 [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)文件庫包含[ `BezierSpline` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs)封裝的貝茲曲線的結構和[ `BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs)控制項方向的列舉。

[ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)類別包含[ `BezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118)擴充方法和[ `CancelBezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161)方法。

[ **BezierLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop)範例將示範如何建立動畫 Beizer 路徑的項目。

## <a name="working-with-animationextensions"></a>使用 AnimationExtensions

一種類型的標準集合中遺漏是動畫的色彩動畫。 問題的原因是沒有正確的方式來插入兩個`Color`值。 它可進行插補，個別的 RGB 值，但為有效插入的 HSL 值。

基於這個理由， [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫包含兩個`Color`動畫方法： [ `RgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166)和[ `HslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188)。 (另外還有兩種取消方法： [ `CancelRgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183)和[ `CancelHslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206))。

這兩種方法讓使用[ `ColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211)，它會藉由呼叫廣泛的泛型執行動畫[ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate%7BT%7D/p/Xamarin.Forms.IAnimatable/System.String/System.Func%7BSystem.Double,T%7D/System.Action%7BT%7D/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BT,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/)方法中的[ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)。

[ **ColorAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations)範例將示範如何使用這兩種色彩動畫。

## <a name="structuring-your-animations"></a>建構動畫

可能會很有用，表示在 XAML 中的動畫，並且使用它們搭配 MVVM。 這在下一章中說明[**章 23。觸發程序和行為**](chapter23.md)。



## <a name="related-links"></a>相關連結

- [章 22 全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [章 22 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [動畫](~/xamarin-forms/user-interface/animation/index.md)
