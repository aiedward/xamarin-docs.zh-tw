---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 21. Transforms''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 32393108f84ea3a57079c86b6a9a8e628ceca03a
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136665"
---
# <a name="summary-of-chapter-21-transforms"></a>第21章的摘要。 轉換

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Xamarin.Forms視圖會顯示在畫面上的位置，並以其父系決定的大小，這通常是 `Layout` 或 `Layout<View>` 衍生的。 *轉換*是一 Xamarin.Forms 項功能，可以修改該位置、大小，甚至是方向。

Xamarin.Forms支援三種基本類型的轉換：

- *翻譯* &mdash;水準或垂直移動元素
- *調整* &mdash;變更元素的大小
- *旋轉* &mdash;在點或軸周圍轉換元素

在中 Xamarin.Forms ，縮放比例為 isotropic，它會一致地影響寬度和高度。 螢幕的二維表面和3D 空間都支援旋轉。 沒有扭曲（或龐大）轉換，也沒有一般化矩陣轉換。

轉換是由類別所定義的八個型別屬性所支援 `double` `VisualElement` ：

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

所有這些屬性都是由可系結屬性所支援。 它們可以是資料系結和樣式的目標。 第[**22 章。動畫**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md)會示範如何以動畫顯示這些屬性，但本章節中的一些範例會示範如何使用計時器來建立動畫 Xamarin.Forms [ ](~/xamarin-forms/platform/device.md#devicestarttimer)。

轉換屬性只會影響元素的轉譯方式，而*不*會影響元素在配置中的感知方式。

## <a name="the-translation-transform"></a>翻譯轉換

和屬性的非零值會 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 以水準或垂直方式轉移元素。

[**TranslationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo)程式可讓您使用 `Slider` 控制 `TranslationX` 和屬性的兩個元素，來試驗這些屬性 `TranslationY` `Frame` 。 轉換也會影響該的所有子系 `Frame` 。

### <a name="text-effects"></a>文字效果

翻譯屬性的其中一個常見用法是在呈現文字時稍微位移一次。 這會在[**TextOffsets**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets)範例中示範：

[![文字位移的三重螢幕擷取畫面](images/ch21fg03-small.png "文字位移")](images/ch21fg03-large.png#lightbox "文字位移")

另一個效果是轉譯的多個複本 `Label` ，使其與3d 區塊相似，例如在[**BlockText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText)範例中示範。

### <a name="jumps-and-animations"></a>跳躍和動畫

[**ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump)範例會使用轉譯在 `Button` 每次按下時移動，但主要的目的是要示範在 `Button` 呈現按鈕的位置接收使用者輸入。

[**ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide)範例很類似，但會使用計時器以動畫方式 `Button` 從某個點到另一個點。

## <a name="the-scale-transform"></a>縮放轉換

[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)轉換可以增加或減少元素的轉譯大小。 預設值為 1。 值為0會導致元素不可見。 負值會使元素看起來會旋轉180度。 `Scale`屬性不會影響元素的 `Width` 或 `Height` 屬性。 這些值保持不變。

您可以 `Scale` 使用[**SimpleScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo)範例來實驗屬性。

[**ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler)範例會示範動畫的 `Scale` 屬性 `Button` 和動畫屬性之間的差異 `FontSize` 。 `FontSize`屬性會影響在 `Button` 版面配置中的感知方式; `Scale` 屬性則不會。

[**ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize)範例會計算套用 `Scale` 至元素的屬性， `Label` 讓它盡可能大，同時仍在頁面中進行調整。

### <a name="anchoring-the-scale"></a>錨定尺規

在前三個樣本中縮放的元素，其大小已增加或減少，相對於元素的中心。 換句話說，元素的大小會以相同方式增加或減少。 在縮放期間，只有元素中心的點會保留在相同的位置。

您可以藉由設定和屬性來變更調整的 [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) 中心 [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) 。 這些屬性是相對於元素本身。 `AnchorX`若為，值為0會參考元素的左邊，而1則參考右側。 同樣地 `AnchorY` ，0是頂端，而1是底部。 這兩個屬性都有0.5 的預設值，也就是中心。

[**AnchoredScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo)範例可讓您使用和屬性，以及屬性來進行實驗 `AnchorX` `AnchorY` `Scale` 。

在 iOS 上，使用和屬性的非預設值 `AnchorX` `AnchorY` 通常與行動電話方向變更不相容。

## <a name="the-rotation-transform"></a>旋轉轉換

[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)屬性會以度為單位來指定，並指出沿著和所定義專案點的順時針旋轉 `AnchorX` `AnchorY` 。 此[**PlaneRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo)可讓您試驗這三個屬性。

### <a name="rotated-text-effects"></a>旋轉的文字效果

[**BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle)範例會示範使用64小型旋轉元素繪製圓形所需的數學運算 `BoxView` 。

[**RotatedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText)範例會顯示多個 `Label` 具有相同文字字串旋轉的專案，如輪輻。

[**CircularText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText)範例會顯示一個文字字串，以在圓形中換行。

### <a name="an-analog-clock"></a>類比時鐘

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫包含的 [`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) 類別會計算時鐘的角度。 為了避免 ViewModel 中的平臺相依性，類別會使用 `Task.Delay` 而非計時器來尋找新的 `DateTime` 值。

也包含在**FormsBook**中，這個類別會執行 [`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) `IValueConverter` 並將第二個角度四捨五入到最接近的秒。

[**MinimalBoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock)使用三個旋轉 `BoxView` 元素來繪製類比時鐘。

[**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock)會使用 `BoxView` 更廣泛的圖形，包括時鐘表面周圍的刻度，以及從其末端旋轉一點的距離：

[![BoxView 時鐘的三向螢幕擷取畫面](images/ch21fg17-small.png "類比時鐘臉部")](images/ch21fg17-large.png#lightbox "類比時鐘臉部")

此外 [`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) ， **FormsBook**中的類別會使第二個手在向前切入之前出現一小部分，然後再移回正確的位置。

### <a name="vertical-sliders"></a>垂直滑杆？

[**VerticalSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders)範例會示範 `Slider` 元素可以旋轉90度，而且仍能正常運作。 不過，很難以定位這些旋轉的 `Slider` 元素，因為在版面配置中，它們仍會顯示為水準。

## <a name="3d-ish-rotations"></a>3D-ish 旋轉

[`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)屬性會顯示在 3D X 軸周圍旋轉元素，讓元素的頂端和底部看起來就像是從檢視器移至或離開。 同樣地， [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) 似乎會旋轉 Y 軸周圍的元素，讓元素的左邊和右邊看起來就像是從檢視器移至或離開。

`AnchorX`屬性會影響 `RotationY` 而非 `RotationX` 。 `AnchorY`屬性會影響 `RotationX` 而非 `RotationY` 。 您可以試驗[**ThreeDeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo)範例，以探索這些屬性的互動。

所隱含的3D 座標系統 Xamarin.Forms 是左手的。 如果您將左側的食指指向沿著增加 X 座標的方向（向右），而中間手指在增加 Y 座標的方向（向下），則您的 thumb 會以增加 Z 座標的方向（從螢幕）來表示。

此外，針對三個軸中的任何一個，如果您將左側的捲軸指向增加值的方向，則手指的曲線會指出正旋轉角度的旋轉方向。

## <a name="related-links"></a>相關連結

- [第21章的全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [第21章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
