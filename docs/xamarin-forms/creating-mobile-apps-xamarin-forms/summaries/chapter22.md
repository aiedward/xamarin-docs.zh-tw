---
title: 第22章摘要。 動畫
description: 使用 Xamarin.表單創建行動應用程式:第 22 章摘要。 動畫
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 935be5bd6696600644463eb4ec26410b546f42a0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771001"
---
# <a name="summary-of-chapter-22-animation"></a>第22章摘要。 動畫

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

您已經看到可以使用 Xamarin.Forms 計時`Task.Delay`器或 創建自己的動畫,但通常使用 Xamarin.Forms 提供的動畫工具更容易。 三個類別實現以下動畫:

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions),高級方法
- [`Animation`](xref:Xamarin.Forms.Animation),功能更豐富,但更困難
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions),最通用、最底層的方法

通常,動畫以可綁定屬性支援的屬性為目標。 這不是要求,但這些是唯一對更改動態反應的屬性。

這些動畫沒有 XAML 介面,但您可以使用第 23 章中討論的技術將動畫整合到 XAML 中[**。觸發器與行為**](chapter23.md)。

## <a name="exploring-basic-animations"></a>探索基本動畫

基本動畫函數是在類中找到的[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)擴展方法。 這些方法應用於派生自`VisualElement`的任何物件。 最簡單的動畫以[`Chapter 21. Transforms`](chapter21.md)中的 轉換屬性為目標。

[**動畫 Tryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout)`Clicked`演示`Button`了 a 的事件處理[`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))程式如何調用 擴充方法以旋轉圓圈中的按鈕。

該方法`RotateTo``Rotation`在四分之一秒`Button`( 預設情況下)將的屬性從 0 更改為 360。 但是,`Button`如果再次點擊 ,則它不起`Rotation`作用, 因為屬性已經是 360 度。

### <a name="setting-the-animation-duration"></a>設定動畫期間

的第二個參數`RotateTo`是持續時間(以毫秒為單位)。 如果設置為較大值,則在動畫`Button`期間點擊 啟動從當前角度開始的新動畫。

### <a name="relative-animations"></a>相對動畫

該方法`RelRotateTo`通過將指定值添加到現有值來執行相對旋轉。 此方法允許多次點擊`Button`,每次`Rotation`將 屬性增加 360 度。

### <a name="awaiting-animations"></a>等候動畫

返回`ViewExtensions``Task<bool>`物件中的所有動畫方法。 這意味著您可以使用`ContinueWith``await`或 定義一系列順序動畫。 `bool`完成`false`返回值是動畫完成而不中斷,或者`true`如果[`CancelAnimation`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))該方法 已取消,該方法將取消`ViewExtensions`由在同 一元素上設置的其他方法啟動的所有動畫。

### <a name="composite-animations"></a>複合動畫

您可以混合等待的動畫和非等待動畫來創建復合動畫。 `ViewExtensions`這些是目標 中的`TranslationX`動畫`TranslationY`,`Scale`並轉換屬性:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

請注意,`TranslateTo`這可能會影響`TranslationX``TranslationY`和屬性。

### <a name="taskwhenall-and-taskwhenany"></a>任務.當所有和任務。當任何

也可以使用管理同步動畫,[`Task.WhenAll`](xref:System.Threading.Tasks.Task.WhenAll*)當多個任務全部完成時發出信號,[`Task.WhenAny`](xref:System.Threading.Tasks.Task.WhenAny*)以及 ,當多個任務中的第一個任務完成時,該動畫會發出信號。

### <a name="rotation-and-anchors"></a>旋轉和錨點

呼叫、`ScaleTo``RelScaleTo``RotateTo`與`RelRotateTo`時,可以[`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)設定[`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)和屬性以指示縮放和旋轉的中心。

[**CircleButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton)透過頁面中心旋轉一個`Button`來展示此技術。

### <a name="easing-functions"></a>Easing 函式

通常,動畫是從起始值到結束值的線性。 緩動函數可能會導致動畫在其過程中加速或減慢。 動畫方法的最後一個可選參數是類型[`Easing`](xref:Xamarin.Forms.Easing),一個定義`Easing`類型 11 個靜態隻讀欄位的類:

- [`Linear`](xref:Xamarin.Forms.Easing.Linear),預設值
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn)、[`SinOut`](xref:Xamarin.Forms.Easing.SinOut)和[`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn)、[`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut)和[`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn)和[`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn)和[`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

後`In`綴表示效果位於動畫的開頭`Out`, 表示在動畫的結尾`InOut`,並且 表示效果位於動畫的開頭和結尾。

[**"反彈按鈕"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton)範例展示了緩動功能的使用。

### <a name="your-own-easing-functions"></a>您自己的緩動功能

還可以通過將[`Func<double, double>`](xref:System.Func`2)[`Easing`](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double}))的 來定義自己的緩動函數。 `Easing`還定義了從`Func<double, double>``Easing`到的隱式轉換。 緩動函數的參數始終在 0 到 1 的範圍內,因為動畫從頭到尾線性進行。 該函數*通常*返回 0 到 1 範圍內的值,但可以短暫負數或`SpringIn`大`SpringOut`於 1(與和 函數的情況一樣),或者如果您知道正在執行的操作,則可能會違反規則。

[**"不安縮放"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale)範例示範了自訂緩動功能,[**自定義CubicEase**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase)演示了另一個函數。

[**SwingButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton)示例還演示了自定義緩動函數,以及更改旋轉動畫序列`AnchorX``AnchorY`中和 屬性的技術。

[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫具有[`JiggleButton`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs)一 個類,該類使用自定義緩動函數在單擊按鈕時晃動按鈕。 [**JiggleButtonDemo 示例**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo)演示了它。

### <a name="entrance-animations"></a>進入動畫

首次出現頁面時,會發生一種流行的動畫類型。 可以在[`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing)頁面重寫中啟動此類動畫。 對於這些動畫,最好設置 XAML,以便希望頁面在動畫*之後*顯示,然後從代碼初始化和動畫化佈局。

[**"淡入淡出"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance)範[`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))例 使用擴充方法淡入頁面內容。

[**滑動入口**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance)示例[`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))使用 擴展方法從側面滑動頁面內容。

[**擺動入口**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance)示例[`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))使用 擴展`RotationY`方法為 屬性設置動畫。 方法[`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))也可用。

### <a name="forever-animations"></a>永遠的動畫

另一個極端,"永遠"動畫運行,直到程序終止。 這些通常用於演示目的。

[**"淡入文字動畫"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation)範例[`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))使用 動畫淡入淡出兩段文字。

[**Palindrome動畫**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation)顯示一個迴廊,然後按順序旋轉單個字母 180 度,以便它們全部顛倒。 然後,整個字串翻轉 180 度,讀取與原始字串相同。

[**Copter動畫**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation)示例旋轉一個`BoxView`簡單的 直升機,同時旋轉它圍繞螢幕中心。

[**旋轉分支**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes)圍繞螢幕`BoxView`中心 旋轉輻條,然後旋轉每個輻條本身以創建有趣的模式:

[![旋轉輻條的三重螢幕截圖](images/ch22fg21-small.png "旋轉輻條")](images/ch22fg21-large.png#lightbox "旋轉輻條")

但是,如`Rotation`[**旋轉細分**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown)示例所示,逐步增加元素的屬性可能長期不起作用。

[**旋轉影像**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage)範例[`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))使用[`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)),[`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))並使 它看起來像點陣圖在 3D 空間中旋轉。

### <a name="animating-the-bounds-property"></a>為邊界屬性設定動畫

尚未演示`ViewExtensions`的唯一擴展方法[`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))是 ,該方法通過[`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)[`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))調用 方法有效地為唯讀屬性設置動畫。 該方法通常由`Layout`導數調用,如[**第26章所述。自訂佈局**](chapter26.md)。

該方法`LayoutTo`應限於特殊用途。 [**BounceBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox)程式使用它來壓縮和擴展`BoxView`, 因為它從頁面的側面反彈。

[**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)範例`LayoutTo`用於 在經典 15-16 謎題的實現中移動切片,該謎題顯示一個雜亂的圖像,而不是編號的切片:

[![夏馬林·許茲爾的三重截圖](images/ch22fg26-small.png "徐茲爾益智遊戲")](images/ch22fg26-large.png#lightbox "徐茲爾益智遊戲")

### <a name="your-own-awaitable-animations"></a>您您您可以等待動畫

[**"試用動畫"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation)範例可建立可等待的動畫。 在動畫完成時,可以從方法和`Task`訊號傳回物件[`TaskCompletionSource`](xref:System.Threading.Tasks.TaskCompletionSource`1)的關鍵類別是 。

## <a name="deeper-into-animations"></a>更深入動畫

Xamarin.Forms 動畫系統可能有點令人困惑。 `Easing`除了類之外,動畫系統還包括`ViewExtensions`、`Animation``AnimationExtension`類。

### <a name="viewextensions-class"></a>檢視延伸類別

你已經看過[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)了。 它定義了返回`Task<bool>`[`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))和 的九種方法。 九種方法中有七個針對轉換屬性。 另外兩個是[`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)),它[`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)針對[`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))屬性,[`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))和調用 方法。

### <a name="animation-class"></a>動畫類

類[`Animation`](xref:Xamarin.Forms.AnimationExtensions)具有一個[建構函數](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action)),具有五個參數來定義回調和完成方法以及動畫的參數。

子動畫可以使用[`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation))、、[`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation))[`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))和重載添加。 [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double))

然後,動畫物件從對 方法的呼叫[`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))開始 。

### <a name="animationextensions-class"></a>動畫延伸類

類[`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions)主要包含擴充方法。 方法有多個版本,泛型`Animate`[`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*)方法用途廣泛,因此實際上是唯一您需要的動畫函數。

## <a name="working-with-the-animation-class"></a>使用動畫類

[**併發動畫**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)示例演示具有[`Animation`](xref:Xamarin.Forms.Animation)多個 不同動畫的類。

### <a name="child-animations"></a>子動畫

[**併發動畫示例**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)還演示了子動畫,它們使用 (非常[`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation))相似)[`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation))和方法。

### <a name="beyond-the-high-level-animation-methods"></a>超越進階動畫方法

[**併發動畫示例**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)還演示如何執行超出方法目標屬性`ViewExtensions`的 動畫。 在一個示例中,一系列週期變長;在另一個範例中,`BackgroundColor`對屬性進行動畫處理。

### <a name="more-of-your-own-awaitable-methods"></a>更多您自己的等待方法

的方法[`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))`ViewExtensions`不[`Easing.SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)與 函數配合使用。 當緩動輸出高於 1 時,它停止。

[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)[`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)庫包含[`TranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12)包含 沒有此問題[`TranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49)的類 和擴展[`CancelTranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44)方法,以及[`CancelTranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71)取消這些動畫 的方法。

[**彈簧滑動入口**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance)演示`TranslateXTo`了 該方法。

該`MoreExtensions`類還包含一[`TranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76)個結合 X 和[`CancelTranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113)Y 轉換的擴充方法以及一個方法。

### <a name="implementing-a-bezier-animation"></a>實現貝氏曲線

還可以開發一個動畫,該動畫沿貝塞爾樣條線的路徑移動元素。 [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫包含一[`BezierSpline`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs)個 結構,該結構封裝了貝塞爾樣條[`BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs)線和 用於控制方向的枚舉。

類別[`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)包含擴[`BezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118)充方法與[`CancelBezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161)方法 。

[**BezierLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop)範例展示沿 Beizer 路徑為元素設定動畫。

## <a name="working-with-animationextensions"></a>使用動畫延伸

標準集合中缺少的一種動畫類型是顏色動畫。 問題是,在兩`Color`個值之間沒有正確的插值方法。 可以插值單個 RGB 值,但同樣有效插入 HSL 值。

因此[`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)[**,Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別包含`Color`兩種[`RgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166)動畫[`HslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188)方法: 和 。 (還有兩種取消方法:[`CancelRgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183)[`CancelHslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)和 。

這兩種方法都使用[`ColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211), 通過[`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*)[`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions)在 中調用廣泛的泛型方法來執行動畫。

[**"顏色動畫"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations)範例展示使用這兩種類型的顏色動畫。

## <a name="structuring-your-animations"></a>建構動畫

有時在 XAML 中表達動畫並將其與 MVVM 結合使用非常有用。 下一章第23章將對此進行介紹[**。觸發器與行為**](chapter23.md)。

## <a name="related-links"></a>相關連結

- [第22章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [第22章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [動畫](~/xamarin-forms/user-interface/animation/index.md)
