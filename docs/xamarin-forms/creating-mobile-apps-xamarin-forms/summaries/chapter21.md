---
title: 第21章的摘要。 轉換
description: 建立 Mobile Apps 與 Xamarin.Forms ：第21章的摘要。 轉換
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cb822c7ac2a05dc9f0d51f51e9737add4395b84d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374020"
---
# <a name="summary-of-chapter-21-transforms"></a>第21章的摘要。 轉換

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

Xamarin.Forms視圖會顯示在螢幕上的位置和大小（由其父系決定）（通常是 `Layout` 或衍生） `Layout<View>` 。 *轉換* 是一 Xamarin.Forms 項功能，可以修改該位置、大小，甚至是方向。

Xamarin.Forms 支援三種基本類型的轉換：

- *轉譯* &mdash; 水準或垂直移動元素
- *調整規模* &mdash; 變更元素的大小
- *旋轉* &mdash; 在點或軸周圍轉換元素

在中 Xamarin.Forms ，縮放比例為 isotropic，它會一致地影響寬度和高度。 螢幕和3D 空間中的二維介面都支援旋轉。 沒有扭曲 (或龐大的) 轉換，而且沒有一般化矩陣轉換。

具有類別所定義之類型的八個屬性支援轉換 `double` `VisualElement` ：

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

所有這些屬性都受到可系結屬性的支援。 它們可以是資料系結和樣式的目標。 第 [**22 章。動畫**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md)會示範如何以動畫顯示這些屬性，但本章節中的一些範例會示範如何使用 Xamarin.Forms [計時器](~/xamarin-forms/platform/device.md#devicestarttimer)建立動畫。

轉換屬性只會影響專案的呈現方式， *不* 會影響專案在配置中的感覺。

## <a name="the-translation-transform"></a>轉譯轉換

和屬性的非零值會 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 以水準或垂直方式移動元素。

[**TranslationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo)程式可讓您使用兩個 `Slider` 可控制 `TranslationX` 和 `TranslationY` 屬性的元素，來試驗這些屬性 `Frame` 。 轉換也會影響該的所有子系 `Frame` 。

### <a name="text-effects"></a>文字效果

轉譯屬性的其中一種常見用法是稍微位移文字轉譯。 [**TextOffsets**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets)範例會示範這一點：

[![文字位移的三重螢幕擷取畫面](images/ch21fg03-small.png "文字位移")](images/ch21fg03-large.png#lightbox "文字位移")

另一個效果是將的多個複本轉譯 `Label` 成類似3d 區塊，例如 [**BlockText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) 範例中所示範的。

### <a name="jumps-and-animations"></a>跳躍和動畫

[**ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump)範例 `Button` 會在每次點擊時使用轉譯來移動，但主要目的是要示範如何在轉譯 `Button` 按鈕的位置接收使用者輸入。

[**ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide)範例類似，但會使用計時器將 `Button` 從一個點到另一個點的動畫。

## <a name="the-scale-transform"></a>調整規模轉換

[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)轉換可以增加或減少專案的呈現大小。 預設值為 1。 值為0會導致元素不可見。 負值會使元素顯示為旋轉180度。 `Scale`屬性不會影響元素的 `Width` 或 `Height` 屬性。 這些值會維持不變。

您可以 `Scale` 使用 [**SimpleScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) 範例來測試屬性。

[**ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler)範例會示範以動畫顯示 `Scale` 屬性 `Button` ，並以動畫顯示內容的差異 `FontSize` 。 `FontSize`屬性會影響在 `Button` 版面配置中的感覺，屬性則不會 `Scale` 。

[**ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize)範例會計算套用 `Scale` 至專案的屬性， `Label` 使其盡可能大，同時仍在頁面中進行調整。

### <a name="anchoring-the-scale"></a>錨定刻度

在前三個樣本中縮放的元素，其大小會相對於元素的中心而增加或減少。 換句話說，元素的大小會以相同方式在所有方向增加或減少。 只有元素中心的點會在調整期間維持在相同的位置。

您可以藉由設定和屬性來變更調整的 [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) 中心 [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) 。 這些屬性是相對於元素本身。 如果 `AnchorX` 是，0值會參考元素的左邊，而1則是參考右邊。 同樣地 `AnchorY` ，0是頂端，1是底部。 這兩個屬性都有預設值0.5，也就是中心。

[**AnchoredScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo)範例可讓您使用和屬性，以及屬性來進行實驗 `AnchorX` `AnchorY` `Scale` 。

在 iOS 上，使用的非預設值 `AnchorX` 和 `AnchorY` 屬性通常與手機方向變更不相容。

## <a name="the-rotation-transform"></a>旋轉轉換

[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)屬性是以度為單位來指定，並指出沿著和定義的元素點的順時針旋轉 `AnchorX` `AnchorY` 。 [**PlaneRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo)可讓您試驗這三個屬性。

### <a name="rotated-text-effects"></a>旋轉文字效果

[**BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle)範例示範使用64微小旋轉元素繪製圓形所需的數學運算 `BoxView` 。

[**RotatedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText)範例會顯示多個 `Label` 具有相同文字字串旋轉的元素，如同輪輻。

[**CircularText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText)範例會顯示顯示在圓形中的文字字串。

### <a name="an-analog-clock"></a>類比時鐘

[ [**Xamarin.Forms Book] 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)組程式庫包含的 [`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) 類別會計算時鐘的角度。 為了避免 ViewModel 中的平臺相依性，類別會使用 `Task.Delay` 而非計時器來尋找新的 `DateTime` 值。

也包含在 **Xamarin.Forms 書籍中。工具** 組是一種實 [`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) 作為的類別， `IValueConverter` 可將第二個角度四捨五入至最接近的秒。

[**MinimalBoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock)會使用三個旋轉 `BoxView` 元素來繪製類比時鐘。

[**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) `BoxView` 適用于更廣泛的圖形，包括時鐘臉部周圍的刻度標記，並從其一端旋轉一點距離：

[![BoxView 時鐘的三重螢幕擷取畫面](images/ch21fg17-small.png "類比時鐘臉部")](images/ch21fg17-large.png#lightbox "類比時鐘臉部")

此外 [`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) ，[ **Xamarin.Forms 書籍** ] 中的類別會讓第二個手先回頭拉出一點，然後再跳到正確的位置。

### <a name="vertical-sliders"></a>垂直滑杆？

[**VerticalSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders)範例會示範 `Slider` 可以旋轉90度的元素，但仍可正常運作。 不過，很難定位這些旋轉的 `Slider` 元素，因為在版面配置中，它們仍會顯示為水準。

## <a name="3d-ish-rotations"></a>3D-低旋轉

[`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)屬性會顯示為圍繞 3D X 軸旋轉元素，讓元素的頂端和底部看起來像是移至檢視器。 同樣地， [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) 似乎會旋轉 Y 軸的元素，讓元素的左邊和右邊看起來像是從檢視器移走或離開。

`AnchorX`屬性會影響 `RotationY` 但不會影響 `RotationX` 。 `AnchorY`屬性會影響 `RotationX` 但不會影響 `RotationY` 。 您可以試驗 [**ThreeDeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) 範例，以探索這些屬性的互動。

所隱含的3D 座標系統 Xamarin.Forms 是左手的。 如果您將左邊的食指指向右邊的 X 座標 (朝右) ，而您的中間手指 (向下) ，則您的 thumb 點會沿著 (畫面) 放大。」。

此外，針對三個軸的任一個，如果您將左側捲動方塊指向增加值的方向，則手指的曲線會指出旋轉朝正面旋轉角度的方向。

## <a name="related-links"></a>相關連結

- [第21章 (PDF 的完整文字) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [第21章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
