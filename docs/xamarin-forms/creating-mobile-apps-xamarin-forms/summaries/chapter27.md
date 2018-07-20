---
title: 第 27 章的摘要。 自訂轉譯器
description: 使用 Xamarin.Forms 建立行動應用程式： 第 27 章摘要。 自訂轉譯器
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: charlespetzold
ms.author: chape
ms.date: 07/18/2018
ms.openlocfilehash: c8b149cfeb814e2a1e0a0d1b38cca24ea096d112
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156521"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>第 27 章的摘要。 自訂轉譯器

> [!NOTE] 
> 在此頁面上的附註表示其中 Xamarin.Forms 有分歧活頁簿中所呈現的題材的區域。

這類的 Xamarin.Forms 元素`Button`封裝在一個名為類別的平台特定按鈕以呈現`ButtonRenderer`。  以下是[iOS 版本`ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs)，則[Android 版本`ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)，而[UWP 版本`ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs)。

此章節會討論如何撰寫您自己的轉譯器，建立對應至平台特定物件的自訂檢視。

## <a name="the-complete-class-hierarchy"></a>完整的類別階層

有四個包含 Xamarin.Forms 的平台特定程式碼的組件。
您可以使用這些連結的 GitHub 上檢視來源：

- [**Xamarin.Forms.Platform** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) （非常小）
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> `WinRT`活頁簿中所述的組件不再是此解決方案的一部分。 

[ **PlatformClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy)範例會顯示適用於執行的平台的組件的類別階層。

您會注意到重要類別，名為`ViewRenderer`。 這是建立平台特定轉譯器時從衍生的類別。 它存在於三個不同的版本，因為它會繫結至的目標平台檢視系統所示：

IOS [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25)有泛型引數：

- `TView` 受限於 [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` 受限於 [`UIKit.UIView`](https://developer.xamarin.com/api/type/UIKit.UIView/)

Android [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17)有泛型引數：

- `TView` 受限於 [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` 受限於 [`Android.Views.View`](https://developer.xamarin.com/api/type/Android.Views.View/)

UWP [ `ViewRenderer<TElement, TNativeElement>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6)以不同的方式具有名為泛型引數：

- `TElement` 受限於 [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement` 受限於 [`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

在撰寫的轉譯器時，您將會衍生類別從`View`，然後撰寫多個`ViewRenderer`類別，一個用於每個支援的平台。 每個平台特定實作會參考原生類別衍生自您指定做為型別的`TNativeView`或`TNativeElement`參數。

## <a name="hello-custom-renderers"></a>Hello，自訂轉譯器 ！

[ **HelloRenderers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers)程式參考名為的自訂檢視`HelloView`在其[ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs)類別。

[ `HelloView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs)類別納入**HelloRenderers**專案，然後直接衍生自`View`。

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs)類別**HelloRenderers.iOS**專案衍生自`ViewRenderer<HelloView, UILabel>`。 在 `OnElementChanged`覆寫時，它會建立原生 iOS `UILabel` ，並呼叫`SetNativeControl`。

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs)類別**HelloRenderers.Droid**專案衍生自`ViewRenderer<HelloView, TextView>`。 在 `OnElementChanged`覆寫時，它會建立一個 Android `TextView` ，並呼叫`SetNativeControl`。

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs)類別**HelloRenderers.UWP**及其他 Windows 專案衍生自`ViewRenderer<HelloView, TextBlock>`。 在 `OnElementChanged`覆寫時，它會建立 Windows `TextBlock` ，並呼叫`SetNativeControl`。

所有`ViewRenderer`衍生項目包含`ExportRenderer`屬性產生關聯的組件層級`HelloView`特定類別`HelloViewRenderer`類別。 這是 Xamarin.Forms 個別平台專案中所找到的轉譯器：

[![Hello 檢視三個螢幕擷取畫面](images/ch27fg02-small.png "自訂轉譯器")](images/ch27fg02-large.png#lightbox "自訂轉譯器")

## <a name="renderers-and-properties"></a>轉譯器和屬性

下一組轉譯器會實作橢圓形繪圖，並位在不同的專案中的[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解決方案。

[ `EllipseView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs)類別位於**Xamarin.FormsBook.Platform**平台。 就如同`BoxView`，並定義只是單一屬性：`Color`型別的`Color`。

轉譯器可以在傳輸上設定的屬性值`View`原生的物件，藉由覆寫`OnElementPropertyChanged`轉譯器的方法。 這個方法內 （與大部分的轉譯器中），兩個屬性都可：

- `Element`Xamarin.Forms 元素
- `Control`小工具或控制物件的原生檢視

這些屬性的型別由泛型參數`ViewRenderer`。 在此範例中，`Element`別的`EllipseView`。

`OnElementPropertyChanged`因此可以將傳輸覆寫`Color`的值`Element`原生`Control`物件，並可能將某種類型的轉換。 三個的轉譯器為：

- iOS: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs)，它會使用[ `EllipseUIView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs)橢圓形的類別。
- Android: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs)，它會使用[ `EllipseDrawableView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs)橢圓形的類別。
- UWP: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs)，其可使用原生 Windows [ `Ellipse` ](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse)類別。

[ **EllipseDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo)類別會顯示數個這些`EllipseView`物件：

[![橢圓形示範的三個螢幕擷取畫面](images/ch27fg03-small.png "EllipseView 自訂轉譯器")](images/ch27fg03-large.png#lightbox "EllipseView 自訂轉譯器")

[ **BouncingBall** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall)彈出`EllipseView`關閉螢幕側邊。

## <a name="renderers-and-events"></a>轉譯器和事件

您也可針對至間接產生事件的轉譯器。 [ `StepSlider` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs)類別是類似於一般的 Xamarin.Forms`Slider`但可讓您指定數個離散步驟之間`Minimum`和`Maximum`值。

三個的轉譯器為：

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

轉譯器偵測到原生控制項，變更，然後呼叫`SetValueFromRenderer`，會參考中定義的可繫結屬性`StepSlider`，變更會造成`StepSlider`引發`ValueChanged`事件。

[ **StepSliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo)範例會示範這個新的滑桿。



## <a name="related-links"></a>相關連結

- [第 27 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [第 27 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
