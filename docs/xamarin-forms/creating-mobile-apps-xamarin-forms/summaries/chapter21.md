---
title: 第21章的摘要。 轉換
description: 使用 Xamarin 建立 Mobile Apps：第21章的摘要。 轉換
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 40c091d0c5042d172108709f89774e41e9339d4b
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760572"
---
# <a name="summary-of-chapter-21-transforms"></a>第21章的摘要。 轉換

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

[Xamarin] 表單檢視會出現在畫面上的位置和大小，取決於其父系，這通常是 `Layout` 或 `Layout<View>` 的衍生。 *轉換*是一種 Xamarin 形式的功能，可以修改該位置、大小，甚至是方向。

Xamarin 支援三種基本類型的轉換：

- *翻譯*&mdash; 以水準或垂直方式移動元素
- *調整*&mdash; 變更元素的大小
- *旋轉*&mdash; 在點或軸上關閉元素

在 [Xamarin] 中，縮放比例為 isotropic;它會一致地影響寬度和高度。 螢幕的二維表面和3D 空間都支援旋轉。 沒有扭曲（或龐大）轉換，也沒有一般化矩陣轉換。

`VisualElement` 類別定義的 `double` 類型的八個屬性支援轉換：

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

所有這些屬性都是由可系結屬性所支援。 它們可以是資料系結和樣式的目標。 第[**22 章。動畫**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md)示範如何以動畫顯示這些屬性，但本章節中的一些範例會示範如何使用 [Xamarin][計時器](~/xamarin-forms/platform/device.md#devicestarttimer)來建立動畫。

轉換屬性只會影響元素的轉譯方式，而*不*會影響元素在配置中的感知方式。

## <a name="the-translation-transform"></a>翻譯轉換

[`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)和[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)屬性的非零值會以水準或垂直方式轉移元素。

[**TranslationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo)程式可讓您使用兩個 `Slider` 元素來試驗這些屬性，以控制 `Frame`的 `TranslationX` 和 `TranslationY` 屬性。 轉換也會影響該 `Frame`的所有子系。

### <a name="text-effects"></a>文字效果

翻譯屬性的其中一個常見用法是在呈現文字時稍微位移一次。 這會在[**TextOffsets**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets)範例中示範：

[![文字位移的三重螢幕擷取畫面](images/ch21fg03-small.png "文字位移")](images/ch21fg03-large.png#lightbox "文字位移")

另一個效果是轉譯 `Label` 的多個複本，使其類似于3D 區塊，例如[**BlockText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText)範例中所示。

### <a name="jumps-and-animations"></a>跳躍和動畫

[**ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump)範例會使用轉譯來在按下時移動 `Button`，但主要的目的是要示範 `Button` 在呈現按鈕的位置接收使用者輸入。

[**ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide)範例很類似，但會使用計時器來以動畫顯示從某個點到另一個點的 `Button`。

## <a name="the-scale-transform"></a>縮放轉換

[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)轉換可以增加或減少元素的轉譯大小。 預設值為 1。 值為0會導致元素不可見。 負值會使元素看起來會旋轉180度。 `Scale` 屬性不會影響元素的 `Width` 或 `Height` 屬性。 這些值保持不變。

您可以使用[**SimpleScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo)範例來試驗 `Scale` 屬性。

[**ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler)範例會示範將 `Button` 的 `Scale` 屬性製作動畫，並以動畫顯示 `FontSize` 屬性的差異。 `FontSize` 屬性會影響 `Button` 在版面配置中的感知方式;`Scale` 屬性不是。

[**ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize)範例會計算套用至 `Label` 元素的 `Scale` 屬性，讓它盡可能大，同時仍在頁面中進行調整。

### <a name="anchoring-the-scale"></a>錨定尺規

在前三個樣本中縮放的元素，其大小已增加或減少，相對於元素的中心。 換句話說，元素的大小會以相同方式增加或減少。 在縮放期間，只有元素中心的點會保留在相同的位置。

您可以藉由設定 [ [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) ] 和 [ [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) ] 屬性來變更調整的中心。 這些屬性是相對於元素本身。 若為 `AnchorX`，值為0會參考元素的左邊，而1則參考右側。 同樣地，對於 `AnchorY`，0是頂端，而1是底部。 這兩個屬性都有0.5 的預設值，也就是中心。

[**AnchoredScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo)範例可讓您使用 `AnchorX` 和 `AnchorY` 屬性，以及 `Scale` 屬性來進行實驗。

在 iOS 上，使用 `AnchorX` 和 `AnchorY` 屬性的非預設值，通常與手機方向變更不相容。

## <a name="the-rotation-transform"></a>旋轉轉換

[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)屬性是以度為單位指定，表示在 `AnchorX` 和 `AnchorY`所定義的專案點周圍順時針旋轉。 此[**PlaneRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo)可讓您試驗這三個屬性。

### <a name="rotated-text-effects"></a>旋轉的文字效果

[**BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle)範例會示範使用64微小旋轉 `BoxView` 元素繪製圓形所需的數學運算。

[**RotatedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText)範例會顯示具有旋轉的相同文字字串的多個 `Label` 專案，就像輪輻一樣。

[**CircularText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText)範例會顯示一個文字字串，以在圓形中換行。

### <a name="an-analog-clock"></a>類比時鐘

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫包含會計算時鐘手的角度的[`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs)類別。 為了避免 ViewModel 中的平臺相依性，類別會使用 `Task.Delay`，而不是用來尋找新 `DateTime` 值的計時器。

**FormsBook**中也包含了一個[`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs)類別，它會執行 `IValueConverter` 並將第二個角度四捨五入到最接近的秒。

[**MinimalBoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock)使用三個旋轉 `BoxView` 元素來繪製類比時鐘。

[**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock)會使用 `BoxView` 來取得更廣泛的圖形，包括時鐘表面周圍的刻度，以及從其末端旋轉一點的距離：

[![BoxView 時鐘的三向螢幕擷取畫面](images/ch21fg17-small.png "類比時鐘臉部")](images/ch21fg17-large.png#lightbox "類比時鐘臉部")

此外， **FormsBook**中的[`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs)類別，會使第二個手在向前切入之前出現一小部分，然後再移回正確的位置。

### <a name="vertical-sliders"></a>垂直滑杆？

[**VerticalSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders)範例會示範 `Slider` 元素可以旋轉90度，而且仍能運作。 不過，很難以定位這些旋轉的 `Slider` 元素，因為在版面配置中，它們仍會顯示為水準。

## <a name="3d-ish-rotations"></a>3D-ish 旋轉

[ [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) ] 屬性會顯示在 3d X 軸周圍旋轉元素，讓元素的頂端和底部看起來就像是從檢視器移至或離開。 同樣地， [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)似乎會旋轉 Y 軸周圍的元素，讓元素的左邊和右邊看起來就像是從檢視器移至或離開。

`AnchorX` 屬性會影響 `RotationY` 但不會 `RotationX`。 `AnchorY` 屬性會影響 `RotationX` 但不會 `RotationY`。 您可以試驗[**ThreeDeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo)範例，以探索這些屬性的互動。

Xamarin 所隱含的3D 座標系統是左手體。 如果您將左側的食指指向沿著增加 X 座標的方向（向右），而中間手指在增加 Y 座標的方向（向下），則您的 thumb 會以增加 Z 座標的方向（從螢幕）來表示。

此外，針對三個軸中的任何一個，如果您將左側的捲軸指向增加值的方向，則手指的曲線會指出正旋轉角度的旋轉方向。

## <a name="related-links"></a>相關連結

- [第21章的全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [第21章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
