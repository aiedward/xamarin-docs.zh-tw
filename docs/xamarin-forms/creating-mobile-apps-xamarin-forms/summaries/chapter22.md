---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 22. Animation''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2a8a089c210a3fe2f48dbe32bf8cda6179af2a78
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136626"
---
# <a name="summary-of-chapter-22-animation"></a>第22章的摘要。 動畫

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

您已瞭解，您可以使用計時器或來建立自己的動畫 Xamarin.Forms `Task.Delay` ，但使用所提供的動畫工具通常會比較容易 Xamarin.Forms 。 有三個類別會執行這些動畫：

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)，高階方法
- [`Animation`](xref:Xamarin.Forms.Animation)，更多功能但較困難
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions)，這是最具彈性、最低等級的方法

通常，動畫的目標屬性是由可系結屬性所支援。 這不是必要條件，但這些是動態回應變更的唯一屬性。

這些動畫沒有 XAML 介面，但是您可以使用第23章所討論的技巧，將動畫整合到 XAML 中[**。觸發程式和行為**](chapter23.md)。

## <a name="exploring-basic-animations"></a>探索基本動畫

基本動畫函式是在類別中找到的擴充方法 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 。 這些方法適用于衍生自的任何物件 `VisualElement` 。 最簡單的動畫會以中所討論的轉換屬性為目標 [`Chapter 21. Transforms`](chapter21.md) 。

[**AnimationTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout)會示範的 `Clicked` 事件處理常式如何 `Button` 呼叫 [ `RotateTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動））擴充方法，以旋轉圓形中的按鈕。

方法會在 `RotateTo` `Rotation` `Button` 一季秒的課程中，將的屬性從0變更為360（預設為）。 不過，如果 `Button` 再次按下，則不會執行任何操作，因為 `Rotation` 屬性已經是360度。

### <a name="setting-the-animation-duration"></a>設定動畫持續時間

的第二個引數 `RotateTo` 是持續時間（以毫秒為單位）。 如果設定為較大的值， `Button` 在動畫期間點擊會啟動以目前角度開始的新動畫。

### <a name="relative-animations"></a>相對動畫

`RelRotateTo`方法會將指定的值加入至現有的值，藉以執行相對旋轉。 這個方法允許 `Button` 多次使用，而且每次都會將 `Rotation` 屬性增加360度。

### <a name="awaiting-animations"></a>等候動畫

中的所有動畫方法都會傳回 `ViewExtensions` `Task<bool>` 物件。 這表示您可以使用或來定義一連串的連續動畫 `ContinueWith` `await` 。 `bool` `false` 如果動畫完成但未中斷，或 `true` [ `CancelAnimation` ] （x：）已取消，則完成傳回值為 Xamarin.Forms 。ViewExtensions. CancelAnimations （ Xamarin.Forms 。VisualElement））方法，它會取消在 `ViewExtensions` 相同元素上設定之中的其他方法所起始的所有動畫。

### <a name="composite-animations"></a>複合動畫

您可以混合等候和未等待的動畫，以建立複合動畫。 這些是中的動畫 `ViewExtensions` `TranslationX` ，以、 `TranslationY` 和 `Scale` 轉換屬性為目標：

- [ `TranslateTo` ] （x： Xamarin.Forms 。ViewExtensions. TranslateTo （ Xamarin.Forms 。VisualElement，Double，system.string， Xamarin.Forms ，，簡化）
- [ `ScaleTo` ] （x： Xamarin.Forms 。ViewExtensions. ScaleTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。簡化）
- [ `RelScaleTo` ] （x： Xamarin.Forms 。ViewExtensions. RelScaleTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。簡化）

請注意， `TranslateTo` 可能會同時影響 `TranslationX` 和 `TranslationY` 屬性。

### <a name="taskwhenall-and-taskwhenany"></a>System.threading.tasks.task.whenall 和 System.threading.tasks.task.whenany

您也可以使用來管理同時的動畫 [`Task.WhenAll`](xref:System.Threading.Tasks.Task.WhenAll*) ，這會在多個工作全部結束時發出信號，而當 [`Task.WhenAny`](xref:System.Threading.Tasks.Task.WhenAny*) 數個工作的第一個結束時，就會發出信號。

### <a name="rotation-and-anchors"></a>旋轉和錨點

呼叫、、 `ScaleTo` `RelScaleTo` `RotateTo` 和 `RelRotateTo` 方法時，您可以設定 [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) 和 [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) 屬性來指示縮放和旋轉的中心。

[**CircleButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton)會藉由繞著頁面中央的來示範這項技術 `Button` 。

### <a name="easing-functions"></a>Easing 函式

通常動畫是從開始值到結束值的線性。 簡化函數可能會導致動畫在其課程中加速或變慢。 動畫方法的最後一個選擇性引數屬於類型 [`Easing`](xref:Xamarin.Forms.Easing) ，這是一個類別，它會定義11個類型的靜態唯讀欄位 `Easing` ：

- [`Linear`](xref:Xamarin.Forms.Easing.Linear)，預設值
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn)、 [`SinOut`](xref:Xamarin.Forms.Easing.SinOut) 和[`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn)、 [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut) 和[`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn)和[`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn)和[`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

後置詞 `In` 表示效果是在動畫的開頭， `Out` 表示在結尾，而 `InOut` 表示它是在動畫的開頭和結尾。

[**BounceButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton)範例示範如何使用緩時函數。

### <a name="your-own-easing-functions"></a>您自己的緩動函數

您也可以藉由將傳遞至函式，來定義您自己的緩時函數 [`Func<double, double>`](xref:System.Func`2) 。 [ `Easing` ](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})) `Easing`也會定義從到的隱含轉換 `Func<double, double>` `Easing` 。 當動畫從開始到結束時，緩時變函數的引數一律會在0到1的範圍內。 函式*通常*會傳回0到1範圍內的值，但可能會短暫否定或大於1（如同和函式的情況）， `SpringIn` `SpringOut` 或者如果您知道您所執行的動作，則可能會中斷規則。

[**UneasyScale**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale)範例會示範自訂的緩動函式，而[**CustomCubicEase**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase)會示範另一個功能。

[**SwingButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton)範例也會示範自訂的緩動函式，以及變更 `AnchorX` `AnchorY` 一系列旋轉動畫中和屬性的技術。

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫有一個 [`JiggleButton`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) 類別，它會使用自訂的緩動函式，在按一下按鈕時加以 jiggle。 [**JiggleButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo)範例會示範它。

### <a name="entrance-animations"></a>進入動畫

當頁面第一次出現時，就會出現一種常用的動畫類型。 這類動畫可以在頁面的覆 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 寫中啟動。 針對這些動畫，最好是設定 XAML，讓您在動畫*之後*顯示頁面，然後從程式碼初始化和建立版面配置的動畫。

[**FadingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance)範例會使用 [ `FadeTo` ] （x： Xamarin.Forms 。ViewExtensions. FadeTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動））擴充方法，以淡入頁面的內容。

[**SlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance)範例會使用 [ `TranslateTo` ] （x： Xamarin.Forms 。ViewExtensions. TranslateTo （ Xamarin.Forms 。VisualElement，Double，system.string， Xamarin.Forms ，，緩動））擴充方法，以從側邊滑入頁面的內容。

[**SwingingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance)範例會使用 [ `RotateYTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateYTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動））擴充方法，以建立屬性的動畫 `RotationY` 。 A [ `RotateXTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateXTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動））方法也可供使用。

### <a name="forever-animations"></a>永久動畫

另一方面，「永遠」動畫會一直執行，直到程式終止為止。 這些通常是供示範之用。

[**FadingTextAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation)範例會使用 [ `FadeTo` ] （x： Xamarin.Forms 。ViewExtensions. FadeTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動））動畫，以淡入和放大兩段文字。

[**PalindromeAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation)會顯示 palindrome，然後依序將個別字母旋轉180度，讓它們全部顛倒。 然後，整個字串會翻轉180度，以讀取與原始字串相同的字元。

[**CopterAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation)範例會旋轉簡單的 `BoxView` 直升機，同時將其繞著螢幕中央。

[**RotatingSpokes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes)會 `BoxView` 繞著畫面中央的輪輻，然後旋轉每個輪輻本身來建立有趣的模式：

[![旋轉輪輻的三向螢幕擷取畫面](images/ch22fg21-small.png "旋轉輪輻")](images/ch22fg21-large.png#lightbox "旋轉輪輻")

不過，逐漸增加專案的 `Rotation` 屬性可能無法在長期使用，如[**RotationBreakdown**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown)範例所示。

[**SpinningImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage)範例會使用 [ `RotateTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩時）、[ `RotateXTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateXTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動）和 [ `RotateYTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateYTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。簡化），使其看起來像是在3D 空間中旋轉點陣圖。

### <a name="animating-the-bounds-property"></a>以動畫顯示界限屬性

尚未示範的唯一擴充方法 `ViewExtensions` 是 [ `LayoutTo` ] （x： Xamarin.Forms 。ViewExtensions. LayoutTo （ Xamarin.Forms 。VisualElement， Xamarin.Forms 。矩形，System.object， Xamarin.Forms 。緩時）：藉 [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) 由呼叫 [ `Layout` ] （x：，有效地繪製唯讀屬性的動畫 Xamarin.Forms 。VisualElement。版面配置（ Xamarin.Forms 。矩形））方法。 這個方法通常由衍生呼叫， `Layout` 如第[**26 章所討論。CustomLayouts**](chapter26.md)。

`LayoutTo`方法應該限制為特殊用途。 [**BouncingBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox)程式會使用它來壓縮和展開， `BoxView` 因為它會在頁面的側邊。

[**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)範例會使用在 `LayoutTo` 傳統15-16 謎題的執行中移動磚，以顯示已加密的影像，而不是編號的磚：

[![Xamarin Xuzzle 的三重螢幕擷取畫面](images/ch22fg26-small.png "Xuzzle 謎題遊戲")](images/ch22fg26-large.png#lightbox "Xuzzle 謎題遊戲")

### <a name="your-own-awaitable-animations"></a>您自己的可等候動畫

[**TryAwaitableAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation)範例會建立可等候動畫。 可以 `Task` 從方法傳回物件，並在動畫完成時發出信號的重要類別是 [`TaskCompletionSource`](xref:System.Threading.Tasks.TaskCompletionSource`1) 。

## <a name="deeper-into-animations"></a>更深入的動畫

Xamarin.Forms動畫系統可能有點令人困惑。 除了 `Easing` 類別以外，動畫系統 `ViewExtensions` 也包含、 `Animation` 和 `AnimationExtension` 類別。

### <a name="viewextensions-class"></a>ViewExtensions 類別

您已經見過 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 。 它會定義九個傳回 `Task<bool>` 和 [ `CancelAnimations` ] （x：）的方法 Xamarin.Forms 。ViewExtensions. CancelAnimations （ Xamarin.Forms 。VisualElement））。 九種方法中的七個目標是轉換屬性。 其他兩個則是 [ `FadeTo` ] （x： Xamarin.Forms 。ViewExtensions. FadeTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動），其以屬性為目標 [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) ，而 [ `LayoutTo` ] （x： Xamarin.Forms 。ViewExtensions. LayoutTo （ Xamarin.Forms 。VisualElement， Xamarin.Forms 。矩形，System.object， Xamarin.Forms 。緩動），這會呼叫 [ `Layout` ] （x： Xamarin.Forms 。VisualElement。版面配置（ Xamarin.Forms 。矩形））方法。

### <a name="animation-class"></a>動畫類別

[`Animation`](xref:Xamarin.Forms.AnimationExtensions)類別具有 [函式] （x： Xamarin.Forms 。動畫。% 23ctor （system.string {system.string}，System.object，double Xamarin.Forms ，，緩動、system.string），具有五個引數可定義回呼和完成的方法，以及動畫的參數。

您可以使用 [ `Add` ] （x：）加入子動畫 Xamarin.Forms 。動畫。 Add （System.object， Xamarin.Forms double，，，動畫），[ `Insert` ] （x： Xamarin.Forms 。動畫。 Insert （system.string，double，， Xamarin.Forms 。動畫），[ `WithConcurrent` ] （x： Xamarin.Forms 。WithConcurrent （ Xamarin.Forms 。動畫、system.string、system.string）和多載的 [ `WithConcurrent` ] （x： Xamarin.Forms 。WithConcurrent （system.string {system.string}，System.object，double， Xamarin.Forms ，，緩動、system.string、double）。

接著，會呼叫 [ `Commit` ] （x： Xamarin.Forms . 動畫. Commit （）來啟動動畫物件 Xamarin.Forms 。System.windows.media.animation.ianimatable>、System.string、system.object、system.string、 Xamarin.Forms 。緩動，system.string {system.string，system.string}，system.string {system.string}）方法的值。

### <a name="animationextensions-class"></a>Petzold.animationextensions 類別

[`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions)類別包含大部分的擴充方法。 方法有數個版本 `Animate` ，泛型 [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) 方法的用途很多，因為它其實是您唯一需要的動畫功能。

## <a name="working-with-the-animation-class"></a>使用動畫類別

[**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)範例會示範 [`Animation`](xref:Xamarin.Forms.Animation) 具有數個不同動畫的類別。

### <a name="child-animations"></a>子動畫

[**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)範例也會示範子動畫，以使用（非常相似的） [ `Add` ] （x： Xamarin.Forms 。動畫。 Add （System.object， Xamarin.Forms double，，，動畫）和 [ `Insert` ] （x： Xamarin.Forms 。動畫。 Insert （system.string，double，， Xamarin.Forms 。動畫））方法。

### <a name="beyond-the-high-level-animation-methods"></a>超越高階動畫方法

[**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)範例也會示範如何執行超過方法目標屬性的動畫 `ViewExtensions` 。 在其中一個範例中，一連串的期間會變長;在另一個範例中， `BackgroundColor` 屬性會以動畫顯示。

### <a name="more-of-your-own-awaitable-methods"></a>您自己的可等候方法更多

[ `TranslateTo` ] （X： Xamarin.Forms 。ViewExtensions. TranslateTo （ Xamarin.Forms 。VisualElement，Double，system.string， Xamarin.Forms ，，緩動））的方法 `ViewExtensions` 不適用於 [`Easing.SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) 函數。 當緩動輸出超過1時，它就會停止。

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫包含 [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) 具有 [`TranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) 和 [`TranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) 擴充方法的類別，其中沒有這個問題，以及用 [`CancelTranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) [`CancelTranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) 來取消這些動畫的和方法。

[**SpringSlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance)會示範 `TranslateXTo` 方法。

`MoreExtensions`類別也包含 [`TranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) 結合 X 和 Y 轉譯的擴充方法，以及 [`CancelTranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) 方法。

### <a name="implementing-a-bezier-animation"></a>執行貝塞爾動畫

您也可以開發動畫，沿著貝茲曲線的路徑移動元素。 [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫包含的 [`BezierSpline`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) 結構會封裝貝茲曲線和 [`BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) 列舉以控制方向。

[`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)類別包含 [`BezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) 擴充方法和 [`CancelBezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) 方法。

[**BezierLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop)範例示範如何沿著 Beizer 路徑建立元素的動畫。

## <a name="working-with-animationextensions"></a>使用 Petzold.animationextensions

標準集合中遺漏的一種動畫類型是色彩動畫。 問題在於，沒有適當的方法可以在兩個值之間插補 `Color` 。 您可以插補個別的 RGB 值，但就像有效的會插入 HSL 值一樣。

因此， [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別包含兩個 `Color` 動畫方法： [`RgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166) 和 [`HslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188) 。 （還有兩個取消方法： [`CancelRgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) 和 [`CancelHslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206) ）。

這兩種方法都使用 [`ColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211) ，它會藉由呼叫中的廣泛泛型方法來執行動畫 [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) 。

[**ColorAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations)範例會示範如何使用這兩種類型的色彩動畫。

## <a name="structuring-your-animations"></a>結構化您的動畫

在 XAML 中表示動畫，並搭配 MVVM 使用它們，有時會很有用。 這會在下一章第[**23 章中討論。觸發程式和行為**](chapter23.md)。

## <a name="related-links"></a>相關連結

- [第22章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [第22章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [動畫](~/xamarin-forms/user-interface/animation/index.md)
