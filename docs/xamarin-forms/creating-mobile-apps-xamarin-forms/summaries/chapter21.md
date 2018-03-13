---
title: "第 21 的摘要。 轉換"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: afb8e2fff58583dc8648c55839649c96cb68b6ba
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-21-transforms"></a>第 21 的摘要。 轉換

Xamarin.Forms 檢視位置和大小取決於其父代，這通常是在螢幕上出現`Layout`或`Layout<View>`衍生項目。 *轉換*是可以修改該位置、 大小或甚至方向 Xamarin.Forms 功能。

Xamarin.Forms 可支援三個基本類型的轉換：

- *轉譯*（& c) 2014; #x 水平或垂直移動項目
- *標尺*（& c) 2014年 #x; 變更項目的大小
- *旋轉*（& c) 2014; #x 開啟點或軸周圍的項目

透過 Xamarin.Forms，縮放比例是等方向性;它會影響的寬度和高度一致的方式。 同時在二維度介面的螢幕，並在 3D 空間中支援顯示器旋轉。 沒有任何誤差 （或僅只是） 的轉換，而且沒有通用的矩陣轉換。

轉換所支援的八個型別的屬性`double`所定義`VisualElement`類別：

- [`TranslationX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)
- [`TranslationY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)
- [`Scale`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)
- [`Rotation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)
- [`RotationX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/)
- [`RotationY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/)
- [`AnchorX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/)
- [`AnchorY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)

所有這些屬性可繫結屬性的備份。 它們可以做為目標的資料繫結和樣式。 [**章 22。動畫**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md)示範如何可以動畫顯示這些屬性，但在本章中的某些範例會顯示動畫使用 Xamarin.Forms[計時器](~/xamarin-forms/platform/device.md#Device_StartTimer)。

轉換屬性影響的項目只是如何轉譯時，以及執行*不*會影響項目在版面配置中的認知。

## <a name="the-translation-transform"></a>轉譯轉換

非零值的[ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)和[ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)屬性在水平或垂直移動項目。

[ **TranslationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo)程式可讓您試驗這些屬性具有兩個`Slider`控制的項目`TranslationX`和`TranslationY`屬性`Frame`. 轉換也會影響所有的子系的`Frame`。

### <a name="text-effects"></a>文字效果

轉譯內容的常見用法之一是文字的稍微位移呈現。 這示範於[ **TextOffsets** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets)範例：

[![三個螢幕擷取畫面的文字位移](images/ch21fg03-small.png "文字位移")](images/ch21fg03-large.png#lightbox "文字位移")

另一個是可以呈現多個副本`Label`類似 3D 的區塊，如中所示範[ **BlockText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText)範例。

### <a name="jumps-and-animations"></a>跳躍點和動畫

[ **ButtonJump** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump)範例會使用轉譯移動`Button`只要點選，但其主要目的是為了示範在`Button`收到使用者輸入的位置，其中按鈕會呈現。

[ **ButtonGlide** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide)範例類似，但以動畫方式顯示使用計時器`Button`從另一個點。

## <a name="the-scale-transform"></a>小數位數轉換

[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)轉換可以增加或減少項目的呈現的大小。 預設值為 1。 值為 0 會導致不可見的項目。 負值會導致看起來像旋轉 180 度的項目。 `Scale`屬性不會影響`Width`或`Height`項目的屬性。 這些值會維持不變。

您可以試驗`Scale`屬性使用[ **SimpleScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo)範例。

[ **ButtonScaler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler)範例將示範如何建立動畫的差異`Scale`屬性`Button`和動畫`FontSize`屬性。 `FontSize`屬性會影響如何`Button`會被配置;`Scale`屬性並不會。

[ **ScaleToSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize)範例計算`Scale`屬性套用至`Label`請盡量大臺仍在頁面內的項目。

### <a name="anchoring-the-scale"></a>錨定在標尺

調整在先前的三個範例中的項目所有增加或減少相對於項目的中心的大小。 換句話說，項目增加或減少大小相同的所有方向。 只有在中心的點項目會縮放期間維持相同的位置。

您可以變更縮放設定的中心[ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/)和[ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)屬性。 這些屬性會與項目本身。 如`AnchorX`、 左邊的項目是指值為 0 和 1 指的是右端。 同樣地針對`AnchorY`0 是最上層，1 是下方。 這兩個屬性具有預設值為 0.5，這是中央。

[ **AnchoredScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo)範例可讓您試驗`AnchorX`和`AnchorY`屬性以及`Scale`屬性。

在 iOS 上，使用非預設值`AnchorX`和`AnchorY`屬性通常與不相容電話方向的變更。

## <a name="the-rotation-transform"></a>旋轉轉換

[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)屬性指定以度為單位，表示所定義的項目點的順時針旋轉`AnchorX`和`AnchorY`。 [ **PlaneRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo)可讓您試驗這三個屬性。

### <a name="rotated-text-effects"></a>旋轉的文字效果

[ **BoxViewCircle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle)範例將示範如何繪製圓形，使用 64 小旋轉必要數學`BoxView`項目。

[ **RotatedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText)範例會顯示多個`Label`具有相同的文字字串的項目看起來像是支點旋轉。

[ **CircularText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText)範例會顯示換行是圓形中有出現的文字字串。

### <a name="an-analog-clock"></a>類比時鐘

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)文件庫包含[ `AnalogClockViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs)計算角度的時鐘指針的類別。 若要避免在 ViewModel，類別會使用的平台相依性`Task.Delay`而不是尋找新的計時器`DateTime`值。

也包含在**Xamarin.FormsBook.Toolkit**是[ `SecondTickConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs)類別可實作`IValueConverter`和要捨入為最接近秒的第二個角度做。

[ **MinimalBoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock)使用三個旋轉`BoxView`繪製類比時鐘的項目。

[ **BoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock)使用`BoxView`更廣泛的圖形，包括刻度周圍的時鐘，圖示會標示並將該旋轉小距離從其結束：

[![BoxView 時鐘的三個螢幕擷取畫面](images/ch21fg17-small.png "類比時鐘")](images/ch21fg17-large.png#lightbox "類比時鐘")

此外[ `SecondBackEaseConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs)類別**Xamarin.FormsBook.Toolkit**導致秒針看起來像撤回有點之前，先繼續進行，然後再將移回正確的位置。

### <a name="vertical-sliders"></a>垂直滑桿嗎？

[ **VerticalSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders)範例會示範`Slider`項目可以旋轉 90 度，仍函式。 不過，很難將這些旋轉`Slider`項目因為仍在配置中會出現水平。

## <a name="3d-ish-rotations"></a>3D ish 旋轉

[ `RotationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/)使的項目上下似乎移動朝向或離開檢視器為中心旋轉元素周圍 3D x 軸似乎屬性。 同樣地， [ `RotationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/)似乎旋轉繞 y 軸進行左側和右側的項目似乎移動朝向或離開檢視器的項目。

`AnchorX`屬性會影響`RotationY`但不是`RotationX`。 `AnchorY`屬性會影響`RotationX`但不是`RotationY`。 您可以試驗[ **ThreeDeeRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo)範例來瀏覽這些屬性的互動情形。

慣用左手 Xamarin.Forms 所隱含 3D 座標系統。 如果您指向左手遞增的 X 方向的食指座標 （至右邊），並增加 Y 方向的中指協調 （下），然後在增加 Z 座標 （超出螢幕） 的方向的捲動方塊點。

此外，任何三個軸，如果您增加值的方向中點左側拇指然後手指曲線表示旋轉正數的旋轉角度的方向。



## <a name="related-links"></a>相關連結

- [第 21 全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [第 21 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
