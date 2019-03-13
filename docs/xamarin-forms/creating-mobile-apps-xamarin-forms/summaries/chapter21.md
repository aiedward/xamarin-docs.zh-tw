---
title: 第 21 章的摘要。 轉換
description: 使用 Xamarin.Forms 建立行動應用程式： 第 21 章摘要。 轉換
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 56c4345dffe71f79e9d89c64478a9c6c38fc1d84
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53048813"
---
# <a name="summary-of-chapter-21-transforms"></a>第 21 章的摘要。 轉換

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Xamarin.Forms 檢視中的位置和大小取決於其父代，這通常是螢幕上出現`Layout`或`Layout<View>`衍生項目。 *轉換*是可以修改該位置、 大小或甚至方向的 Xamarin.Forms 功能。

Xamarin.Forms 可支援三種基本類型的轉換：

- *翻譯*&mdash;水平或垂直移位的項目
- *縮放比例*&mdash;變更項目的大小
- *旋轉*&mdash;開啟點或軸周圍的項目

在 Xamarin.Forms 中，若要調整為 dbi100;它會影響的寬度和高度一致的方式。 同時在二維度介面的螢幕，並在 3D 空間中支援顯示器旋轉。 沒有任何扭曲 （或單憑） 的轉換，且沒有一般化的矩陣轉換。

支援具有八個屬性的類型轉換`double`所定義`VisualElement`類別：

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

所有這些屬性是由可繫結屬性支援。 它們可以是資料繫結的目標和樣式。 [**第 22 章。動畫**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md)示範如何可以動畫顯示這些屬性，但在這一章中的一些範例會顯示動畫使用 Xamarin.Forms[計時器](~/xamarin-forms/platform/device.md#Device_StartTimer)。

轉換屬性會影響如何轉譯時，項目，且請勿*不*影響項目在版面配置中的發現如何。

## <a name="the-translation-transform"></a>轉譯轉換

非零值的[ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)並[ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY)屬性在水平或垂直移位的項目。

[ **TranslationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo)計劃可讓您試驗這些屬性具有兩個`Slider`控制的項目`TranslationX`並`TranslationY`屬性`Frame`. 轉換也會影響所有的子系的`Frame`。

### <a name="text-effects"></a>文字效果

翻譯內容的常見用法是文字的稍微位移呈現。 這示範於[ **TextOffsets** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets)範例：

[![三重的螢幕擷取畫面的文字位移](images/ch21fg03-small.png "文字位移")](images/ch21fg03-large.png#lightbox "文字位移")

另一個的作用是呈現多個副本`Label`類似的 3D 的區塊，例如所示[ **BlockText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText)範例。

### <a name="jumps-and-animations"></a>跳躍點和動畫

[ **ButtonJump** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump)移動範例會使用的轉譯`Button`只要點選它，但主要的目的是要示範`Button`接收位置的使用者輸入，按鈕會呈現。

[ **ButtonGlide** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide)範例類似，但會使用計時器來以動畫顯示`Button`從到另一個點。

## <a name="the-scale-transform"></a>縮放轉換

[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)轉換可以增加或減少所呈現的項目大小。 預設值為 1。 值為 0 會導致不可見的項目。 負值會導致出現旋轉 180 度的項目。 `Scale`屬性不會影響`Width`或`Height`項目的屬性。 這些值會維持不變。

您可以試驗`Scale`屬性使用[ **SimpleScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo)範例。

[ **ButtonScaler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler)範例示範如何以動畫顯示的差異`Scale`屬性`Button`並以動畫顯示`FontSize`屬性。 `FontSize`屬性會影響如何`Button`認知配置;`Scale`屬性則否。

[ **ScaleToSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize)範例會計算`Scale`屬性套用至`Label`，將它盡可能同時仍然符合在頁面中的項目。

### <a name="anchoring-the-scale"></a>錨定的小數位數

調整先前的三個範例中的項目所有增加或減少相對於項目的中心的大小。 換句話說，項目增加或減少大小在所有方向相同。 只有點中央的項目會在調整期間維持相同的位置。

您可以變更藉由設定縮放的中心[ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX)並[ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY)屬性。 這些屬性是相對於項目本身。 針對`AnchorX`、 0 的值是指項目的左邊和右邊則是指 1。 同樣地針對`AnchorY`，0 是最上方，而 1 是下方。 這兩個屬性有預設值為 0.5，這是 「 中心 」。

[ **AnchoredScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo)範例可讓您試驗`AnchorX`並`AnchorY`屬性以及`Scale`屬性。

在 iOS 上，使用非預設值`AnchorX`和`AnchorY`屬性通常與不相容電話方向的變更。

## <a name="the-rotation-transform"></a>旋轉轉換

[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)屬性以度數為單位指定，並指出順時針旋轉的點所定義之項目的`AnchorX`和`AnchorY`。 [ **PlaneRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo)可讓您試驗這三個屬性。

### <a name="rotated-text-effects"></a>旋轉的文字效果

[ **BoxViewCircle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle)範例會示範需要繪製圓形，使用 64 小旋轉數學`BoxView`項目。

[ **RotatedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText)範例會顯示多個`Label`具有相同的文字字串的項目旋轉至看起來像是支點。

[ **CircularText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText)範例顯示包裝在圓形中出現的文字字串。

### <a name="an-analog-clock"></a>類比時鐘

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫包含[ `AnalogClockViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs)計算角度的時鐘指針的類別。 若要避免在 ViewModel，而此類別會使用的平台相依性`Task.Delay`而不是用於尋找新計時器`DateTime`值。

也包含在**Xamarin.FormsBook.Toolkit**是[ `SecondTickConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs)可實作類別`IValueConverter`，並提供要捨入到最接近的秒的第二個角度。

[ **MinimalBoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock)會使用三個旋轉`BoxView`繪製類比時鐘的項目。

[ **BoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock)使用`BoxView`更廣泛的圖形，包括刻度標記周圍時鐘表面的並將旋轉，以小距離從其結束：

[![BoxView 時鐘的三個螢幕擷取畫面](images/ch21fg17-small.png "類比時鐘表面")](images/ch21fg17-large.png#lightbox "類比時鐘表面")

此外[ `SecondBackEaseConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs)類別**Xamarin.FormsBook.Toolkit**會導致出現之前往前，跳提取回有點秒針然後再將移回其正確的位置。

### <a name="vertical-sliders"></a>垂直滑桿嗎？

[ **VerticalSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders)範例示範`Slider`項目可以旋轉 90 度，並仍能運作。 不過，很難放置這些旋轉`Slider`項目因為仍在配置中似乎是水平。

## <a name="3d-ish-rotations"></a>3D ish 輪替

[ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX) ; 屬性看似旋轉 3D 的 x 軸周圍的項目，使得頂端和底部項目似乎移入或遠離檢視器。 同樣地， [ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY)似乎旋轉繞 y 軸進行左邊和右邊的項目似乎移入或遠離檢視器的項目。

`AnchorX`屬性會影響`RotationY`而非`RotationX`。 `AnchorY`屬性會影響`RotationX`而非`RotationY`。 您可以試驗[ **ThreeDeeRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo)範例，以探索這些屬性的互動。

慣用左手 Xamarin.Forms 所隱含 3D 座標系統。 如果您指向遞增的 X 方向的左側的食指協調 （至右邊），並將中指的方向遞增的 Y 座標 （下），然後增加 （不是螢幕） 的 Z 座標方向的捲動方塊點。

此外，任何三個軸，如果您指向左側的 thumb 的遞增值，方向則手指的曲線表示正旋轉的角度旋轉的方向。



## <a name="related-links"></a>相關連結

- [第 21 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [第 21 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
