---
title: 第27章的摘要。 自訂轉譯器
description: 建立 Mobile Apps 與 Xamarin.Forms ：第27章的摘要。 自訂轉譯器
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6b117688e1bf85266c274a44e6a4b1fda1ef5134
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375144"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>第27章的摘要。 自訂轉譯器

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

專案 Xamarin.Forms （例如） `Button` 會使用封裝在名為之類別中的平臺特定按鈕來呈現 `ButtonRenderer` 。  以下是[iOS `ButtonRenderer` 版](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs)、 [ `ButtonRenderer` Android 版](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)及[UWP 版本的 `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs)。

本章將討論如何撰寫您自己的轉譯器，以建立對應至平臺特定物件的自訂視圖。

## <a name="the-complete-class-hierarchy"></a>完整類別階層

有四個元件包含 Xamarin.Forms 平臺特定程式碼。
您可以使用下列連結，在 GitHub 上查看來源：

- [**Xamarin.Forms.平臺**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (非常小) 
- [**Xamarin.Forms.Platform. iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform. Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform. UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> `WinRT`本書中所述的元件不再是此解決方案的一部分。

[**PlatformClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy)範例會顯示對執行中平臺有效的元件類別階層。

您會看到一個名為的重要類別 `ViewRenderer` 。 這是您建立平臺特定轉譯器時所衍生的類別。 它存在於三個不同的版本中，因為它系結至目標平臺的視圖系統：

IOS [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) 具有泛型引數：

- `TView` 受限於 [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` 受限於 [`UIKit.UIView`](xref:UIKit.UIView)

Android [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) 具有泛型引數：

- `TView` 受限於 [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` 受限於 [`Android.Views.View`](xref:Android.Views.View)

UWP [`ViewRenderer<TElement, TNativeElement>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) 具有不同的命名泛型引數：

- `TElement` 受限於 [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement` 受限於 [`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

撰寫轉譯器時，您將會從衍生類別 `View` ，然後撰寫多個 `ViewRenderer` 類別，每個支援的平臺各一個類別。 每個平臺特定的執行都會參考一個衍生自您指定為或參數之型別的原生類別 `TNativeView` `TNativeElement` 。

## <a name="hello-custom-renderers"></a>您好，自訂轉譯器！

[**HelloRenderers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers)程式會參考 `HelloView` 其類別中名為的自訂視圖 [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) 。

[`HelloView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs)類別包含在 **HelloRenderers** 專案中，而且只會衍生自 `View` 。

[`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) **HelloRenderers iOS** 專案中的類別衍生自 `ViewRenderer<HelloView, UILabel>` 。 在覆 `OnElementChanged` 寫中，它會建立原生 iOS `UILabel` 並呼叫 `SetNativeControl` 。

[`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) **HelloRenderers. >droid** 專案中的類別衍生自 `ViewRenderer<HelloView, TextView>` 。 在覆 `OnElementChanged` 寫中，它會建立 Android `TextView` 並呼叫 `SetNativeControl` 。

[`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) **HelloRenderers** 中的類別和其他 Windows 專案衍生自 `ViewRenderer<HelloView, TextBlock>` 。 在覆 `OnElementChanged` 寫中，它會建立視窗 `TextBlock` 並呼叫 `SetNativeControl` 。

所有 `ViewRenderer` 衍生衍生都包含 `ExportRenderer` 元件層級上的屬性，該屬性會將 `HelloView` 類別與特定類別產生關聯 `HelloViewRenderer` 。 這是在 Xamarin.Forms 個別平臺專案中找出轉譯器的方式：

[![Hello View 的三重螢幕擷取畫面](images/ch27fg02-small.png "自訂轉譯器")](images/ch27fg02-large.png#lightbox "自訂轉譯器")

## <a name="renderers-and-properties"></a>轉譯器和屬性

下一組轉譯器會實作為橢圓形繪圖，並位於 [**Xamarin.Forms Book**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解決方案的各種專案中。

此 [`EllipseView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) 類別位於 **Xamarin.Forms Book** platform 平臺中。 類別類似于 `BoxView` ，而且只會定義單一屬性： `Color` 的型別 `Color` 。

轉譯器可以在轉譯器中覆寫方法，以將上設定的屬性值傳送 `View` 至原生物件 `OnElementPropertyChanged` 。 在此方法中 (和大部分轉譯器) 中，有兩個可用的屬性：

- `Element`， Xamarin.Forms 元素
- `Control`，原生視圖或小工具或控制項物件

這些屬性的類型是由的泛型參數所決定 `ViewRenderer` 。 在此範例中， `Element` 的型別為 `EllipseView` 。

`OnElementPropertyChanged`因此，覆寫可能會將的 `Color` 值傳輸 `Element` 到原生 `Control` 物件，可能是某種類型的轉換。 三個轉譯器為：

- iOS： [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs) ，它使用 [`EllipseUIView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) 橢圓形的類別。
- Android： [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs) ，它使用 [`EllipseDrawableView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) 橢圓形的類別。
- UWP： [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs) 可使用原生 Windows [`Ellipse`](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) 類別。

[**EllipseDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo)類別會顯示這些物件中的數個 `EllipseView` ：

[![橢圓形示範的三重螢幕擷取畫面](images/ch27fg03-small.png "EllipseView 自訂轉譯器")](images/ch27fg03-large.png#lightbox "EllipseView 自訂轉譯器")

[**BouncingBall**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall)會將 `EllipseView` 畫面的側邊彈跳。

## <a name="renderers-and-events"></a>轉譯器和事件

轉譯器也可以間接產生事件。 [`StepSlider`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs)類別類似于一般， Xamarin.Forms `Slider` 但允許在和值之間指定一些離散步驟 `Minimum` `Maximum` 。

三個轉譯器為：

- Ios： [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android： [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

轉譯器會偵測原生控制項的變更，然後呼叫 `SetValueFromRenderer` ，這會參考中定義的可系結屬性 `StepSlider` ，這會造成 `StepSlider` 引發事件的變更 `ValueChanged` 。

[**StepSliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo)範例會示範這個新的滑杆。

## <a name="related-links"></a>相關連結

- [第27章全文 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [第27章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
