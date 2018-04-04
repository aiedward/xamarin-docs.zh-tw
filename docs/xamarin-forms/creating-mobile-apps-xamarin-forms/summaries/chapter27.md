---
title: 章 27 的摘要。 自訂轉譯器
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 7a117373a11a057afabbad3c0c6005a9c2a49c3a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-27-custom-renderers"></a>章 27 的摘要。 自訂轉譯器

Xamarin.Forms 項目，例如`Button`以封裝在名為類別中的平台專屬按鈕呈現`ButtonRenderer`。  以下是[iOS 版本`ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs)、 [Android 版`ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)，而[Windows 執行階段版本`ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.WinRT/ButtonRenderer.cs)。

此章節討論如何撰寫您自己的轉譯器來建立對應至特定平台物件的自訂檢視。

## <a name="the-complete-class-hierarchy"></a>完整的類別階層

有七個包含 Xamarin.Forms 平台專屬的程式碼的組件。
您可以使用這些連結的 GitHub 上檢視來源：

- [**Xamarin.Forms.Platform** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) （極小）
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.WinRT** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT) （下一步的三個大於）
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)
- [**Xamarin.Forms.Platform.WinRT.Tablet**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT.Tablet)
- [**Xamarin.Forms.Platform.WinRT.Phone**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT.Phone)

[ **PlatformClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy)範例會顯示適用於執行的平台的組件類別階層架構。

您會注意到名為的重要類別`ViewRenderer`。 這是建立平台特定轉譯器時從衍生的類別。 中有三種不同的版本，因為它會連結到檢視系統的目標平台：

IOS [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L26)有泛型引數：

- `TView` 限制為 [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeView` 限制為 [`UIKit.UIView`](https://developer.xamarin.com/api/type/UIKit.UIView/)

在 Android [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L14)有泛型引數：

- `TView` 限制為 [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeView` 限制為 [`Android.Views.View`](https://developer.xamarin.com/api/type/Android.Views.View/)

Windows 執行階段[ `ViewRenderer<TElement, TNativeElement>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.WinRT/ViewRenderer.cs#L12)以不同方式具有名為泛型引數：

- `TElement` 限制為 [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeElement` 限制為 [`Windows.UI.Xaml.FrameworkElement`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.frameworkelement.aspx)

在撰寫轉譯器時，您將會被衍生類別從`View`，再撰寫多個`ViewRenderer`類別，其中每個受支援的平台。 每個平台特定實作會參考原生類別衍生自您指定做為型別的`TNativeView`或`TNativeElement`參數。

## <a name="hello-custom-renderers"></a>Hello，自訂轉譯器 ！

[ **HelloRenderers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers)程式參考名為的自訂檢視`HelloView`中其[ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs)類別。

[ `HelloView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs)類別併入**HelloRenderers**專案，並直接衍生自`View`。

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs)類別**HelloRenderers.iOS**專案衍生自`ViewRenderer<HelloView, UILabel>`。 在`OnElementChanged`覆寫時，它會建立原生 iOS`UILabel`和呼叫`SetNativeControl`。

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs)類別**HelloRenderers.Droid**專案衍生自`ViewRenderer<HelloView, TextView>`。 在`OnElementChanged`覆寫時，它會建立 Android`TextView`和呼叫`SetNativeControl`。

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs)類別**HelloRenderers.UWP**和其他 Windows 專案衍生自`ViewRenderer<HelloView, TextBlock>`。 在`OnElementChanged`覆寫時，它會建立 Windows`TextBlock`和呼叫`SetNativeControl`。

所有`ViewRenderer`衍生項目包含`ExportRenderer`屬性產生關聯的組件層級上`HelloView`特定類別`HelloViewRenderer`類別。 這是 Xamarin.Forms 個別的平台專案中所找到的轉譯器：

[![三個螢幕擷取畫面的 Hello 檢視](images/ch27fg02-small.png "自訂轉譯器")](images/ch27fg02-large.png#lightbox "自訂轉譯器")

## <a name="renderers-and-properties"></a>轉譯器與屬性

下一組轉譯器會實作橢圓形繪圖，並位在不同的專案中的[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)方案。

[ `EllipseView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs)類別位於**Xamarin.FormsBook.Platform**平台。 類別是類似於`BoxView`並定義只是單一的屬性：`Color`型別的`Color`。

轉譯器可以在傳輸上設定的屬性值`View`原生物件藉由覆寫至`OnElementPropertyChanged`轉譯器的方法。 這個方法內 （與大部分的轉譯器中），兩個屬性都可：

- `Element`Xamarin.Forms 項目
- `Control`在原生檢視或 widget 或控制項的物件

這些屬性的型別由泛型參數`ViewRenderer`。 在此範例中，`Element`的型別`EllipseView`。

`OnElementPropertyChanged`覆寫可以因此傳輸`Color`值`Element`於原生`Control`物件，可能與某些類型的轉換。 三個轉譯器為：

- iOS: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs)，它會使用[ `EllipseUIView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs)橢圓形的類別。
- Android: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs)，它會使用[ `EllipseDrawableView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs)橢圓形的類別。
- Windows 執行階段： [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs)，其可使用原生 Windows [ `Ellipse` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.ellipse.aspx)類別。

[ **EllipseDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo)類別會顯示其中數`EllipseView`物件：

[![三個螢幕擷取畫面的橢圓形示範](images/ch27fg03-small.png "EllipseView 自訂轉譯器")](images/ch27fg03-large.png#lightbox "EllipseView 自訂轉譯器")

[ **BouncingBall** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall)退`EllipseView`關閉螢幕側邊。

## <a name="renderers-and-events"></a>轉譯器和事件

它也可能會間接產生事件的轉譯器。 [ `StepSlider` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs)類別是類似於一般 Xamarin.Forms`Slider`可讓您指定的離散步驟之間的數字，但`Minimum`和`Maximum`值。

三個轉譯器為：

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- Windows 執行階段： [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

轉譯器偵測到原生控制項，變更，然後呼叫`SetValueFromRenderer`，參考中定義的繫結屬性`StepSlider`，這會導致變更`StepSlider`引發`ValueChanged`事件。

[ **StepSliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo)範例會示範這個新的滑動軸。



## <a name="related-links"></a>相關連結

- [章 27 全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [章 27 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
