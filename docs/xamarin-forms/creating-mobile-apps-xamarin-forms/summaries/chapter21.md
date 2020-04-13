---
title: 第21章摘要。 轉換
description: 使用 Xamarin.表單創建行動應用程式:第 21 章摘要。 轉換
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 40c091d0c5042d172108709f89774e41e9339d4b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760572"
---
# <a name="summary-of-chapter-21-transforms"></a>第21章摘要。 轉換

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Xamarin.Forms 檢視出現在由其父級(通常是`Layout``Layout<View>`或 派生)確定的位置和大小中。 *變換*是 Xamarin.Forms 功能,可以修改該位置、大小甚至方向。

Xamarin.Forms 支援三種基本類型的轉換:

- *平移*&mdash;水平或垂直移動元素
- *調整*&mdash;調整元素的大小
- *旋轉*&mdash;繞點或軸轉動元素

在 Xamarin.窗體中,縮放是各向異性的;它均勻地影響寬度和高度。 螢幕的二維表面和 3D 空間都支援旋轉。 沒有傾斜(或純粹的)變換,也沒有廣義矩陣變換。

轉換受`double``VisualElement`類別定義的八個類型屬性支援:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

所有這些屬性都由可綁定屬性支援。 它們可以是數據綁定和樣式化的目標。 [**第22章.動畫**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md)演示了如何對這些屬性進行動畫處理,但本章中的一些示例演示了如何使用 Xamarin.Forms[計時器](~/xamarin-forms/platform/device.md#devicestarttimer)為其設置動畫。

轉換屬性僅影響元素的呈現方式,*並且不會影響*元素在佈局中的感知方式。

## <a name="the-translation-transform"></a>翻譯轉換

[`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)和[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)屬性的非零值水準或垂直移動元素。

[**翻譯展示**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo)程式允許您使用`Slider`兩 個元素來試驗這些屬性,這些`TranslationX`元素`TranslationY``Frame`控制和的屬性。 轉換還影響該`Frame`的所有子級。

### <a name="text-effects"></a>文字效果

翻譯屬性的一個常見用途是稍微偏移文本的呈現。 這一點在[**文字偏移範例中**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets)示範:

[![文字位移的三重螢幕截圖](images/ch21fg03-small.png "文字位移")](images/ch21fg03-large.png#lightbox "文字位移")

另一個效果是渲染`Label`的多個副本以類似於 3D 塊,如[**BlockText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText)範例中所示。

### <a name="jumps-and-animations"></a>跳躍和動畫

[**ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump)範例使用平移在點擊`Button`時 移動 , 但主要目的是演示`Button`在呈現按鈕的位置 接收用戶輸入。

[**ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide)範例類似,但使用計時器從一個`Button`點 到另一個點設置動畫。

## <a name="the-scale-transform"></a>比例變換

變換[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)可以增加或減小元素的呈現大小。 預設值為 1。 值 0 會導致元素不可見。 負值使元素顯示為旋轉 180 度。 屬性`Scale`不會影響`Width`元素`Height`的或屬性。 這些值保持不變。

您可以使用`Scale`[**SimpleScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo)範例試用該屬性。

[**ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler)範例展示對`Scale``Button`屬性進行動畫處理與`FontSize`為 屬性設置動畫之間的區別。 屬性`FontSize`會影響在佈局`Button`中感知的屬性`Scale`不。

[**ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize)範例計算應用`Scale``Label`於 元素的屬性,以使元素在頁面中仍適合時使其盡可能大。

### <a name="anchoring-the-scale"></a>錨定比例

與前三個樣本中縮放的元素相對於元素的中心的大小都增大或減小。 換句話說,元素在所有方向上的大小都相同地增加或減小。 在縮放期間,只有元素中心的點保留在同一位置。

您可以通過[`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)設定[`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)和 屬性來更改縮放的中心。 這些屬性相對於元素本身。 對於`AnchorX`,值 0 表示元素的左側,1 表示右側。 同樣,`AnchorY`對於 ,0 是頂部,1 是底部。 這兩個屬性的預設值均為 0.5,這是中心。

[**錨定ScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo)範例允許您試驗`AnchorX``AnchorY`和`Scale`屬性以及 屬性。

在 iOS 上`AnchorX`,`AnchorY`使用和 屬性的非預設值通常與電話方向更改不相容。

## <a name="the-rotation-transform"></a>旋轉轉換

屬性[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)以度為單位指定,並指示沿`AnchorX``AnchorY`和定義的元素點順時針旋轉。 [**平面旋轉示範**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo)允許您嘗試這三個屬性。

### <a name="rotated-text-effects"></a>旋轉的文字效果

[**BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle)範例展示了使用 64 個微`BoxView`小的旋轉 元素繪製圓所需的數學。

[**旋轉文字**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText)範例顯示`Label`多個 元素,並旋轉相同的文本字串以顯示為分支。

[**"循環文字"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText)範例顯示一個文字字串,該文字字串顯示為環繞在一個圓圈中。

### <a name="an-analog-clock"></a>類比時鐘

[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫包含一[`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs)個 類,用於計算時鐘指標的角度。 為了避免 ViewModel 中的平台依賴項,`Task.Delay`類使用 而不是計時器來`DateTime`查找新 值。

**Xamarin.FormsBook.Toolkit**中也包含[`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs)一`IValueConverter`個實現 和用於將第二個角度舍入到最近秒的類。

[**最小BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock)使用三個`BoxView`旋轉 元素來繪製類比時鐘。

[**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock)`BoxView`用於 更廣泛的圖形,包括時鐘表面的刻度線,以及與其末端旋轉稍遠的指標:

[![BoxView 時鐘的三重螢幕截圖](images/ch21fg17-small.png "類比時鐘面")](images/ch21fg17-large.png#lightbox "類比時鐘面")

此外[`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs)**,Xamarin.FormsBook.Toolkit**中的一個類會導致第二隻手在向前跳躍之前出現一點回拉,然後移回正確的位置。

### <a name="vertical-sliders"></a>垂直滑塊?

[**垂直滑塊**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders)示例演示`Slider`元素 可以旋轉 90 度,並且仍然正常工作。 但是,很難定位這些旋轉`Slider`的元素,因為在佈局中它們看起來仍然是水準的。

## <a name="3d-ish-rotations"></a>3D-ish 旋轉

屬性[`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)似乎圍繞 3D X 軸旋轉元素,以便元素的頂部和底部似乎向或遠離查看器移動。 類似地,[`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)似乎圍繞 Y 軸旋轉元素,以使元素的左右兩側似乎向或遠離查看器。

屬性`AnchorX`影響`RotationY`但`RotationX`不受影響。 屬性`AnchorY`影響`RotationX`但`RotationY`不受影響。 您可以試用[**三DeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo)範例,以探索這些屬性的互動。

Xamarin.Forms 隱含的 3D 座標系是左撇子。 如果將左手食指指向增加 X 座標(右側)的方向,將中指指向增加 Y 座標(向下)的方向,則拇指指向增加 Z 座標的方向(螢幕外)。

此外,對於三個軸中的任何一個,如果將左手拇指指向增加值的方向,則手指的曲線指示正旋轉角度的旋轉方向。

## <a name="related-links"></a>相關連結

- [第21章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [第21章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
