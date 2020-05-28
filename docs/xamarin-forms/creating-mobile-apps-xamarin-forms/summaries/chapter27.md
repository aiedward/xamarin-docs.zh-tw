---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 27. Custom renderers''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4b0a3e14126dc8c92a1d1b60db7dbb2afd8e8c12
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136587"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>第27章的摘要。 自訂轉譯器

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> 此頁面上的附注指出 Xamarin.Forms 從書籍中所呈現之材質分歧的區域。

專案 Xamarin.Forms （例如） `Button` 會使用封裝在名為的類別中的平臺特定按鈕來呈現 `ButtonRenderer` 。  以下是的[iOS 版本 `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs)、的[ `ButtonRenderer` Android 版本](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)， [ `ButtonRenderer` 以及的 UWP 版本](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs)。

本章將討論如何撰寫自己的轉譯器，以建立對應至平臺特定物件的自訂視圖。

## <a name="the-complete-class-hierarchy"></a>完整的類別階層

有四個元件包含 Xamarin.Forms 平臺特定的程式碼。
您可以使用下列連結來查看 GitHub 上的來源：

- [**Xamarin.Forms.平臺**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform)（非常小）
- [**Xamarin.Forms.平臺 iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform. Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> `WinRT`本書中提及的元件不再屬於此解決方案的一部分。 

[**PlatformClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy)範例會顯示對執行平臺有效之元件的類別階層。

您會注意到一個名為的重要類別 `ViewRenderer` 。 這是您在建立平臺特定轉譯器時衍生自的類別。 它存在於三個不同的版本中，因為它會系結至目標平臺的視圖系統：

IOS [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) 具有泛型引數：

- `TView`限制為[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`限制為[`UIKit.UIView`](xref:UIKit.UIView)

Android [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) 有泛型引數：

- `TView`限制為[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`限制為[`Android.Views.View`](xref:Android.Views.View)

UWP [`ViewRenderer<TElement, TNativeElement>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) 具有不同的命名泛型引數：

- `TElement`限制為[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement`限制為[`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

撰寫轉譯器時，您會從衍生類別 `View` ，然後 `ViewRenderer` 針對每個支援的平臺撰寫多個類別。 每個平臺特定的執行都會參考衍生自您指定為或參數之類型的原生類別 `TNativeView` `TNativeElement` 。

## <a name="hello-custom-renderers"></a>您好，自訂轉譯器！

[**HelloRenderers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers)程式會參考 `HelloView` 其類別中名為的自訂視圖 [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) 。

[`HelloView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs)類別會包含在**HelloRenderers**專案中，而且只會衍生自 `View` 。

[`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) **HelloRenderers**中的類別衍生自 `ViewRenderer<HelloView, UILabel>` 。 在覆 `OnElementChanged` 寫中，它會建立原生 iOS `UILabel` 並呼叫 `SetNativeControl` 。

[`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) **HelloRenderers. Droid**專案中的類別衍生自 `ViewRenderer<HelloView, TextView>` 。 在覆 `OnElementChanged` 寫中，它會建立 Android `TextView` 並呼叫 `SetNativeControl` 。

[`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) **HelloRenderers**中的類別和其他 Windows 專案衍生自 `ViewRenderer<HelloView, TextBlock>` 。 在覆 `OnElementChanged` 寫中，它會建立視窗 `TextBlock` 並呼叫 `SetNativeControl` 。

所有的 `ViewRenderer` 衍生都包含 `ExportRenderer` 元件層級上的屬性，可將 `HelloView` 類別與特定 `HelloViewRenderer` 類別產生關聯。 這就是 Xamarin.Forms 在個別平臺專案中尋找轉譯器的方式：

[![Hello View 的三重螢幕擷取畫面](images/ch27fg02-small.png "自訂轉譯器")](images/ch27fg02-large.png#lightbox "自訂轉譯器")

## <a name="renderers-and-properties"></a>轉譯器和屬性

下一組轉譯器會實作為橢圓形繪圖，並位於[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)的各種專案中。

[`EllipseView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs)類別位於**FormsBook**平臺中。 類別類似于 `BoxView` ，而且只會定義單一屬性： `Color` 類型為 `Color` 。

轉譯器可以藉由覆寫轉譯器中的方法，將上設定的屬性值傳送 `View` 至原生物件 `OnElementPropertyChanged` 。 在此方法中（以及大部分的轉譯器中），有兩個可用的屬性：

- `Element`， Xamarin.Forms 元素
- `Control`、原生視圖或 widget 或控制項物件

這些屬性的類型是由的泛型參數所決定 `ViewRenderer` 。 在此範例中， `Element` 的類型為 `EllipseView` 。

因此，覆 `OnElementPropertyChanged` 寫可以將的 `Color` 值傳輸 `Element` 至原生 `Control` 物件，可能會有某種類型的轉換。 這三個轉譯器為：

- iOS： [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs) ，它會使用 [`EllipseUIView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) 橢圓形的類別。
- Android： [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs) ，它會使用 [`EllipseDrawableView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) 橢圓形的類別。
- UWP： [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs) ，可以使用原生 Windows [`Ellipse`](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) 類別。

[**EllipseDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo)類別會顯示下列幾個 `EllipseView` 物件：

[![橢圓形示範的三向螢幕擷取畫面](images/ch27fg03-small.png "EllipseView 自訂轉譯器")](images/ch27fg03-large.png#lightbox "EllipseView 自訂轉譯器")

[**BouncingBall**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall)會 `EllipseView` 從畫面側邊退回。

## <a name="renderers-and-events"></a>轉譯器和事件

轉譯器也可以間接產生事件。 [`StepSlider`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs)類別與一般類似， Xamarin.Forms `Slider` 但允許在和值之間指定一些離散步驟 `Minimum` `Maximum` 。

這三個轉譯器為：

- IOS[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- 面向[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

轉譯器會偵測原生控制項的變更，然後呼叫 `SetValueFromRenderer` ，這會參考中定義的可系結屬性 `StepSlider` ，這項變更會導致 `StepSlider` 引發 `ValueChanged` 事件。

[**StepSliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo)範例會示範這個新的滑杆。

## <a name="related-links"></a>相關連結

- [第27章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [第27章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
