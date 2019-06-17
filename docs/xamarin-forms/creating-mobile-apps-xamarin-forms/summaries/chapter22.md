---
title: 第 22 章摘要。 動畫
description: 使用 Xamarin.Forms 建立行動應用程式：第 22 章摘要。 動畫
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 7b3695ce145c2ca58238e2c9a601923cbcefa182
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61333097"
---
# <a name="summary-of-chapter-22-animation"></a>第 22 章摘要。 動畫

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

您已了解您可以建立您自己的動畫使用 Xamarin.Forms 計時器或`Task.Delay`，但通常可以更輕鬆使用 Xamarin.Forms 所提供的動畫功能。 三個類別會實作這些動畫：

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)高階方法
- [`Animation`](xref:Xamarin.Forms.Animation)更靈活，但更困難
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions)最具彈性、 最低層級的方法

一般而言，動畫目標設為可繫結屬性所支援的屬性。 這不是需求，但這些是動態地回應變更的唯一屬性。

這些動畫，沒有 XAML 介面，但您可以整合使用所述的技巧的 XAML 中的動畫[**第 23 章。觸發程序和行為**](chapter23.md)。

## <a name="exploring-basic-animations"></a>探索基本動畫

基本動畫功能是擴充方法中找到[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)類別。 這些方法套用至任何物件都衍生自`VisualElement`。 最簡單的動畫目標屬性中所討論的轉換[ `Chapter 21. Transforms` ](chapter21.md)。

[ **AnimationTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout)示範如何`Clicked`事件處理常式`Button`可以呼叫[ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))啟動的擴充方法在圓形中的按鈕。

`RotateTo`方法變更`Rotation`屬性`Button`從 0 到 360 的四分之一秒 （預設）。 如果`Button`點選一次，不過，但是卻因為`Rotation`屬性已經 360 度。

### <a name="setting-the-animation-duration"></a>設定動畫的持續時間

第二個引數`RotateTo`是以毫秒為單位的持續時間。 如果設定為較大的值中，點選`Button`動畫期間開始新的動畫開始，在目前的角度。

### <a name="relative-animations"></a>相對的動畫

`RelRotateTo`方法會藉由將指定的值加入至現有的值來執行相對的旋轉。 這個方法可讓`Button`多次，而每個要點選的時間會增加`Rotation`360 度的屬性。

### <a name="awaiting-animations"></a>正在等待動畫

中的所有動畫方法`ViewExtensions`傳回`Task<bool>`物件。 這表示您可以定義一系列使用循序的動畫`ContinueWith`或`await`。 `bool`完成傳回的值是`false`動畫完成而不會中斷或`true`如果它已取消[ `CancelAnimation` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))方法，這樣會取消所起始的所有動畫中的其他方法`ViewExtensions`設定相同的項目。

### <a name="composite-animations"></a>複合的動畫

您可以混合受期待與非等候建立複合的動畫的動畫。 這些是中的動畫`ViewExtensions`目標`TranslationX`， `TranslationY`，和`Scale`轉換屬性：

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

請注意，`TranslateTo`可能會影響兩者`TranslationX`和`TranslationY`屬性。

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAll 和 Task.WhenAny

您也可管理同時使用的動畫[ `Task.WhenAll` ](xref:System.Threading.Tasks.Task.WhenAll*)，這表示當所有完成多項工作，和[ `Task.WhenAny` ](xref:System.Threading.Tasks.Task.WhenAny*)，這表示當數個第一個工作已結束。

### <a name="rotation-and-anchors"></a>輪替和錨點

呼叫時`ScaleTo`， `RelScaleTo`， `RotateTo`，以及`RelRotateTo`方法，您可以設定[ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX)並[ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY)屬性來表示縮放和旋轉的中心。

[ **CircleButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton)示範這項技巧的上方`Button`周圍頁面的中心。

### <a name="easing-functions"></a>Easing 函式

一般而言，動畫會呈線性關係從開始值的結束值的。 Easing 函式可能會導致動畫加速或減緩透過他們的課程。 動畫方法的最後一個選擇性引數是型別的[ `Easing` ](xref:Xamarin.Forms.Easing)，一個類別來定義第 11 個靜態唯讀欄位的型別`Easing`:

- [`Linear`](xref:Xamarin.Forms.Easing.Linear)預設值
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn)[ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut)，及 [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn)[ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut)，及 [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) 和 [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) 和 [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

`In`後置詞表示的效果是將動畫的起始處`Out`在結束時，表示與`InOut`表示它是在開頭和結尾動畫。

[ **BounceButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton)範例示範如何使用 easing 函式。

### <a name="your-own-easing-functions"></a>Easing 函式

您也可以定義您自己的 easing 函式藉由傳遞[ `Func<double, double>` ](xref:System.Func`2)要[`Easing`建構函式](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double}))。 `Easing` 也會定義的隱含轉換`Func<double, double>`至`Easing`。 Easing 函式的引數一律是在 0 到 1 的範圍，動畫會以線性方式從開頭至結尾。 函式*通常*範圍內的 0 到 1，傳回值，但可能在負值或大於 1 簡短是 (在此情況下，使用`SpringIn`和`SpringOut`函式) 或破壞規則，如果您知道您的所作所為。

[ **UneasyScale** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale)範例會示範自訂的 easing 函式，並[ **CustomCubicEase** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase)示範另一個。

[ **SwingButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton)範例也會示範自訂的 easing 函式，以及變更一項技術`AnchorX`和`AnchorY`旋轉的動畫序列內的屬性。

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)文件庫具有[ `JiggleButton` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) jiggle 按鈕按下時會類別，以使用自訂的 easing 函式。 [ **JiggleButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo)範例會示範它。

### <a name="entrance-animations"></a>開場動畫

當第一次出現的頁面時，就會發生一個熱門的動畫類型。 可以啟動這類動畫[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)頁面的覆寫。 這些動畫，最好設定，請決定要如何頁面為 XAML，才會出現*之後*動畫，然後初始化並從程式碼配置以動畫顯示。

[ **FadingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance)範例會使用[ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))淡入頁面的內容中的擴充方法。

[ **SlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance)範例會使用[ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))投影片 頁面的內容中從側邊的擴充方法。

[ **SwingingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance)範例會使用[ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))擴充方法，以動畫顯示`RotationY`屬性。 A [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法也會提供。

### <a name="forever-animations"></a>不限次數的動畫

在另一個極端而言，"forever"動畫執行，直到程式結束。 這些設定通常被為了供示範之用。

[ **FadingTextAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation)範例會使用[ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))動畫來放大和淡出兩段文字。

[**PalindromeAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation)顯示 palindrome，並接著循序旋轉個別的字母 180 度，使用它們全部顛倒。 然後整個字串已翻轉 180 度讀取原始字串相同。

[ **CopterAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation)範例旋轉簡單`BoxView`直昇機時上方圍繞螢幕的中心。

[**RotatingSpokes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes)是`BoxView`支點圍繞螢幕的中心，並再將旋轉每個輪輻本身，以建立有趣的模式：

[![三重的螢幕擷取畫面的旋轉 Spokes](images/ch22fg21-small.png "旋轉 Spokes")](images/ch22fg21-large.png#lightbox "旋轉 Spokes")

不過，逐漸增加`Rotation`元素的屬性可能不適用於長期來看，作為[ **RotationBreakdown** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown)範例會示範。

[ **SpinningImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage)範例會使用[ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))， [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))，與[ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))讓它看起來像點陣圖，如同要旋轉 3D 空間中。

### <a name="animating-the-bounds-property"></a>建立繫結屬性的動畫

中唯一的擴充方法`ViewExtensions`尚未示範已[ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))，其有效地以動畫顯示唯讀[ `Bounds` ](xref:Xamarin.Forms.VisualElement.Bounds)屬性，藉由呼叫[`Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))方法。 這個方法通常會呼叫`Layout`衍生項目中所述將能[**第 26 章。CustomLayouts**](chapter26.md)。

`LayoutTo`方法應該限制為特殊用途。 [ **BouncingBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox)程式會使用它來壓縮和解壓縮`BoxView`如它撞頁面側邊。

[ **XamagonXuzzle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)範例會使用`LayoutTo`移動磚傳統的實作中會顯示變碼的映像，而不是已編號的圖格的 15-16 謎題：

[![三重的螢幕擷取畫面的 Xamarin Xuzzle](images/ch22fg26-small.png "Xuzzle 拼圖遊戲")](images/ch22fg26-large.png#lightbox "Xuzzle 拼圖遊戲")

### <a name="your-own-awaitable-animations"></a>可等候的動畫

[ **TryAwaitableAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation)範例會建立 awaitable 動畫。 可以傳回的重要類別`Task`是物件的方法和動畫完成時的訊號[ `TaskCompletionSource` ](xref:System.Threading.Tasks.TaskCompletionSource`1)。

## <a name="deeper-into-animations"></a>更深入到動畫

Xamarin.Forms 動畫系統可以是稍嫌複雜。 除了`Easing`類別，動畫系統組成`ViewExtensions`， `Animation`，和`AnimationExtension`類別。

### <a name="viewextensions-class"></a>ViewExtensions 類別

您所見[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)。 它會定義九個方法會傳回`Task<bool>`並[ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))。 七個九個方法的目標轉換屬性。 其他兩種方式[ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))，目標[ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity)屬性，以及[ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))，其會呼叫[ `Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))方法。

### <a name="animation-class"></a>動畫類別

[ `Animation` ](xref:Xamarin.Forms.AnimationExtensions)類別具有[建構函式](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action))具有五個引數，定義回呼和已完成的方法，以及動畫的參數。

可以新增其子動畫，而[ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation))， [ `Insert` ](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation))， [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))，以及多載[ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double)).

然後啟動動畫物件呼叫[ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法。

### <a name="animationextensions-class"></a>AnimationExtensions 類別

[ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions)類別包含大部分的擴充方法。 有數個版本的`Animate`方法和一般[ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*)方法是多功能其實您所需的唯一動畫函數。

## <a name="working-with-the-animation-class"></a>使用動畫類別

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)範例會示範[ `Animation` ](xref:Xamarin.Forms.Animation)具有數個不同的動畫類別。

### <a name="child-animations"></a>其子動畫

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)範例也會示範動畫，請使用 （非常類似） 的子系[ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation))和[ `Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation))方法。

### <a name="beyond-the-high-level-animation-methods"></a>高階動畫方法之外

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)範例也會示範如何執行超越由目標屬性的動畫`ViewExtensions`方法。 在一個範例中，一系列的期間取得較長的;另舉一例，`BackgroundColor`屬性顯示動畫。

### <a name="more-of-your-own-awaitable-methods"></a>多個您自己的 awaitable 方法

[ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))方法`ViewExtensions`不適用於[ `Easing.SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut)函式。 它會停止時的加/減速的輸出取得高於 1。

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫包含[ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)類別[ `TranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12)及[ `TranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49)擴充方法沒有這個問題，以及[ `CancelTranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44)並[ `CancelTranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71)取消那些方法動畫。

[ **SpringSlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance)示範`TranslateXTo`方法。

`MoreExtensions`類別也包含[ `TranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76)擴充方法，以結合 X 和 Y 的轉譯，以及[ `CancelTranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113)方法。

### <a name="implementing-a-bezier-animation"></a>實作貝茲動畫

它也可開發移動路徑的貝茲曲線元素的動畫。 [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫包含[ `BezierSpline` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs)封裝的貝茲曲線的結構和[ `BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs)控制項方向的列舉。

[ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)類別包含[ `BezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118)擴充方法和[ `CancelBezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161)方法。

[ **BezierLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop)範例示範如何以動畫顯示沿著 Beizer 路徑的項目。

## <a name="working-with-animationextensions"></a>使用 AnimationExtensions

一種類型的標準集合中遺漏是動畫的色彩動畫。 問題在於不正確的方法，兩個之間進行插補`Color`值。 可以插入個別的 RGB 值，但為有效插 HSL 值。

基於這個理由， [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫包含兩個`Color`動畫的方法： [ `RgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166)並[ `HslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188)。 (另外還有兩種取消方法： [ `CancelRgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183)並[ `CancelHslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206))。

這兩種方法讓使用[ `ColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211)，會藉由呼叫廣泛的泛型執行動畫[ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*)中的方法[ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions)。

[ **ColorAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations)範例示範如何使用這兩種色彩的動畫類型。

## <a name="structuring-your-animations"></a>建構您的動畫

有時候，它是 express 在 XAML 中的動畫，並使用它們搭配 MVVM 很有用。 在下一章中，說明這一點[**第 23 章。觸發程序和行為**](chapter23.md)。



## <a name="related-links"></a>相關連結

- [第 22 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [第 22 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [動畫](~/xamarin-forms/user-interface/animation/index.md)
