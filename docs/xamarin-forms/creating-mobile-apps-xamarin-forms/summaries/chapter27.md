---
title: 第27章摘要。 自訂轉譯器
description: 使用 Xamarin.表單創建行動應用程式:第 27 章摘要。 自訂轉譯器
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: fd4014fa4db4e90596c100d454cf0467512240a4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760496"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>第27章摘要。 自訂轉譯器

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> 本頁的註釋指示 Xamarin.Forms 與本書中介紹的材料有分歧的領域。

Xamarin.Forms 元素(`Button`如 )使用封裝在`ButtonRenderer`名為 的 類中的平臺特定按鈕進行呈現。  這裡是[iOS`ButtonRenderer`版本的](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs),的 Android[版本`ButtonRenderer`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)和的[UWP`ButtonRenderer`版本](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs)。

本章討論如何編寫自己的呈現器以創建映射到特定於平臺物件的自定義視圖。

## <a name="the-complete-class-hierarchy"></a>完整的類別結構

有四個程式集包含 Xamarin.Forms 特定於平台的代碼。
您可以使用以下連結在 GitHub 上看來源:

- [**Xamarin.Forms.平臺**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform)(非常小)
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> 本書`WinRT`中提到的程式集不再是此解決方案的一部分。 

[**PlatformClassClass層次結構**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy)範例顯示對執行平臺有效的程式集的類層次結構。

您會注意到一個名為`ViewRenderer`的重要 類。 這是創建特定於平臺的渲染器時派生的類。 它存在於三個不同的版本中,因為它與目標平台的視圖系統相關聯:

iOS[`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25)具有一般參數:

- `TView`約束到[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`約束到[`UIKit.UIView`](xref:UIKit.UIView)

Android[`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17)具有通用參數:

- `TView`約束到[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`約束到[`Android.Views.View`](xref:Android.Views.View)

UWP[`ViewRenderer<TElement, TNativeElement>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6)具有不同名稱的泛型參數:

- `TElement`約束到[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement`約束到[`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

編寫渲染器時,將從 派生一個`View`類 ,然後編`ViewRenderer`寫多個 類,每個受支持的平臺一個。 每個特定於平台的實現將引用從指定為`TNativeView`或`TNativeElement`參數的類型派生的本機類。

## <a name="hello-custom-renderers"></a>你好,自定義渲染器!

[**HelloRenderers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers)程式引用`HelloView`[`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs)其 類中命名的自定義視圖。

該[`HelloView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs)類別包含在**HelloRenderers**專案中,只是`View`派生自 。

**HelloRenderers.iOS**專案`ViewRenderer<HelloView, UILabel>`[`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs)中的 類別的類別 。 在重寫`OnElementChanged`中,它建立本機`UILabel`iOS 和`SetNativeControl`呼叫 。

**HelloRenderers.Droid**專案`ViewRenderer<HelloView, TextView>`[`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs)中的 類別的類別 。 在重寫`OnElementChanged`中,它創建一`TextView`個`SetNativeControl`Android和調用。

**HelloRenderers.UWP**和其他 Windows`ViewRenderer<HelloView, TextBlock>`[`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs)專案中的 類別的類別 。 在重寫`OnElementChanged`中,它會建立`TextBlock`Windows`SetNativeControl`和呼叫 。

所有導`ViewRenderer`數都`ExportRenderer`包含 程式集級別`HelloView`上將`HelloViewRenderer`類與特定 類關聯的屬性。 這是 Xamarin.Forms 在單一平台專案中定位渲染器的方式:

[![Hello 檢視的三重螢幕截圖](images/ch27fg02-small.png "自訂轉譯器")](images/ch27fg02-large.png#lightbox "自訂轉譯器")

## <a name="renderers-and-properties"></a>成像器與屬性

下一組渲染器實現橢圓繪圖,並位於[**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解決方案的各個專案中。

該[`EllipseView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs)課程位於**Xamarin.FormsBook.平台**平臺中。 類別的屬性`BoxView`:`Color`類別`Color`:

渲染器可以通過重寫呈現器`View``OnElementPropertyChanged`中 的方法將 設置在 本機物件的屬性值轉移到本機物件。 在此方法(在大多數渲染器中),有兩個屬性可用:

- `Element`,Xamarin.Forms 元素
- `Control`,本機檢視或小部件或控件物件

這些屬性的類型由到`ViewRenderer`的泛型參數確定。 這個選項,`Element`是類型`EllipseView`。

因此`OnElementPropertyChanged`,`Color`重寫可以將的`Element`值 傳輸到`Control`本機 物件,可能通過某種轉換。 三個渲染器是:

- iOS: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs),它[`EllipseUIView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs)使用 橢圓的類。
- Android: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs),它[`EllipseDrawableView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs)使用 橢圓的類。
- UWP: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs),可以使用本機[`Ellipse`](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse)Windows 類。

[**橢圓展示**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo)類別顯示`EllipseView`以下 幾個物件:

[![橢圓演示的三重螢幕截圖](images/ch27fg03-small.png "橢圓檢視自訂成像器")](images/ch27fg03-large.png#lightbox "橢圓檢視自訂成像器")

[**彈跳球**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall)`EllipseView`從 螢幕的側面反彈。

## <a name="renderers-and-events"></a>成像器和事件

渲染器也可以間接生成事件。 類[`StepSlider`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs)與正常的 Xamarin.Forms`Slider`類似,`Minimum`但`Maximum`允許在和 值之間指定多個離散步驟。

三個渲染器是:

- Ios:[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android:[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP:[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

渲染器檢測對本機控制件的更改,然後`SetValueFromRenderer`調用,該屬性引用 中定義的可`StepSlider`綁定屬性,該更改會導致`StepSlider`觸發`ValueChanged`事件。

[**StepSliderDemo 範例**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo)演示了此新滑塊。

## <a name="related-links"></a>相關連結

- [第27章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [第27章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
