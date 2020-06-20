---
title: 第16章的摘要。 資料繫結
description: 建立 Mobile Apps Xamarin.Forms ：第16章的摘要。 資料繫結
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ece93730100001e8339a5f50cdb7ac437d96fa62
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136730"
---
# <a name="summary-of-chapter-16-data-binding"></a>第16章的摘要。 資料繫結

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> 此頁面上的附注指出 Xamarin.Forms 從書籍中所呈現之材質分歧的區域。

程式設計人員通常會發現自己撰寫的事件處理常式，會偵測到某個物件的屬性何時變更，並使用它來變更另一個物件中的屬性值。 此程式可以使用*資料*系結的技術來自動化。 資料系結通常是在 XAML 中定義，並成為使用者介面定義的一部分。

這些資料系結通常會將使用者介面物件連接到基礎資料。 這是在第18章中更深入探討的技術[**。MVVM**](chapter18.md)。 不過，資料系結也可以連接兩個或多個使用者介面元素。 本章中大部分的資料系結早期範例都會示範這項技術。

## <a name="binding-basics"></a>系結基本概念

資料系結牽涉到數個屬性、方法和類別：

- [`Binding`](xref:Xamarin.Forms.Binding)類別衍生自 [`BindingBase`](xref:Xamarin.Forms.BindingBase) ，並封裝資料系結的許多特性
- [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)屬性是由類別所定義 [`BindableObject`](xref:Xamarin.Forms.BindableObject)
- [ `SetBinding` ] （X： Xamarin.Forms 。BindableObject. SetBinding （ Xamarin.Forms 。BindableProperty， Xamarin.Forms 。System.windows.data.bindingbase.delay））方法也是由類別所定義 [`BindableObject`](xref:Xamarin.Forms.BindableObject)
- [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions)類別會定義三個額外的 `SetBinding` 方法

下列兩個類別支援系結的 XAML 標記延伸：

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)支援 `Binding` 標記延伸
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)支援 `x:Reference` 標記延伸

資料系結牽涉到兩個介面：

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)在 `System.ComponentModel` 命名空間中，當屬性變更時，用來執行通知
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter)是用來定義在資料系結中，將值從一個類型轉換成另一個型別的小型類別

資料系結會連接相同物件的兩個屬性，或（更常見）兩個不同的物件。 這兩個屬性稱為「*來源*」和「*目標*」。 一般來說，source 屬性中的變更會導致目標屬性發生變更，但有時方向會相反。 忽略

- *目標*屬性必須由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- *來源*屬性通常是執行之類別的成員。[`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

`INotifyPropertyChanged`當屬性變更值時，implements 的類別會引發 [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) 事件。 `BindableObject``INotifyPropertyChanged` `PropertyChanged` 當屬性所支援的值變更時，會執行並自動引發事件 `BindableProperty` ，但您可以撰寫自己的類別， `INotifyPropertyChanged` 而不需要衍生自 `BindableObject` 。

## <a name="code-and-xaml"></a>程式碼和 XAML

[**OpacityBindingCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode)範例會示範如何在程式碼中設定資料系結：

- 來源是的 `Value` 屬性`Slider`
- 目標是的 `Opacity` 屬性。`Label`

這兩個物件會藉由將 `BindingContext` 物件的設定 `Label` 為物件來連接 `Slider` 。 這兩個屬性的連接方式是在參考可系結屬性的， [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) `Label` `OpacityProperty` 以及 `Value` `Slider` 以字串表示之的屬性上，呼叫擴充方法。

操作 `Slider` 之後，會使 `Label` 淡入和不顯示。

[**OpacityBindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml)是與 XAML 中的資料系結集相同的程式。 的 `BindingContext` `Label` 會設定為 `x:Reference` 參考的標記延伸 `Slider` ，而的 `Opacity` 屬性 `Label` 會設定為 `Binding` 標記延伸，其 [`Path`](xref:Xamarin.Forms.Binding.Path) 屬性會參考的 `Value` 屬性 `Slider` 。

## <a name="source-and-bindingcontext"></a>來源和 BindingCoNtext

[**BindingSourceCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode)範例顯示程式碼中的替代方法。 建立 `Binding` 物件的方式是將 [`Source`](xref:Xamarin.Forms.Binding.Source) 屬性設為 `Slider` 物件，並將 [`Path`](xref:Xamarin.Forms.Binding.Path) 屬性設定為 "Value"。 [ `SetBinding` ] （X： Xamarin.Forms 。BindableObject. SetBinding （ Xamarin.Forms 。BindableProperty， Xamarin.Forms 。System.windows.data.bindingbase.delay））的方法， `BindableObject` 然後在物件上呼叫 `Label` 。

[構造函式 `Binding` ] （x： Xamarin.Forms 。Binding .% 23ctor （ Xamarin.Forms system.string，）。BindingMode， Xamarin.Forms 。IValueConverter （System.string，system.object，System.object））也可以用來定義 `Binding` 物件。

[**BindingSourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml)範例顯示 XAML 中可比較的技術。 的 `Opacity` 屬性 `Label` 會設定為 `Binding` 標記延伸， [`Path`](xref:Xamarin.Forms.Binding.Path) 並將設定為 `Value` 屬性，並 [`Source`](xref:Xamarin.Forms.Binding.Source) 將設定為內嵌的 `x:Reference` 標記延伸。

總而言之，有兩種方式可以參考系結來源物件：

- 透過 `BindingContext` 目標的屬性
- 透過 `Source` 物件本身的屬性 `Binding`

如果同時指定這兩者，則會優先使用第二個。 的優點 `BindingContext` 是它會透過視覺化樹狀結構傳播。 如果多個目標屬性系結至相同的來源物件，這就*很*方便。

[**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo)程式會使用元素來示範這項技術 [`WebView`](xref:Xamarin.Forms.WebView) 。 `Button`向後導覽的兩個元素會繼承 `BindingContext` 其父系中參考的 `WebView` 。 `IsEnabled`然後，這兩個按鈕的屬性會有簡單的 `Binding` 標記延伸，其會根據的 `IsEnabled` [`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack) 和唯讀屬性的設定，以按鈕屬性為目標 [`CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward) `WebView` 。

## <a name="the-binding-mode"></a>系結模式

將的 [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) 屬性設定 `Binding` 為列舉的成員 [`BindingMode`](xref:Xamarin.Forms.BindingMode) ：

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay)因此，source 屬性中的變更會影響目標
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource)如此一來，目標屬性中的變更就會影響來源
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)因此，來源和目標中的變更會彼此影響
- [`Default`](xref:Xamarin.Forms.BindingMode.Default)表示在 [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) 建立目標時使用指定的 `BindableProperty` 。 如果未指定，則預設值為 `OneWay` 適用于一般可系結屬性，而 `OneWayToSource` 適用于唯讀可系結屬性。

> [!NOTE]
> 此 `BindingMode` 列舉現在也包含 `OnTime` ，只有在系結內容變更時才會套用系結，而當來源屬性變更時，則不會套用。

在 MVVM 案例中，可能是資料系結目標的屬性通常具有 `DefaultBindingMode` 的 `TwoWay` 。 它們是：

- `Slider`和 `Stepper` 的 `Value` 屬性
- `Switch` 的 `IsToggled` 屬性
- `Text``Entry`、和的 `Editor` 屬性`SearchBar`
- `DatePicker` 的 `Date` 屬性
- `TimePicker` 的 `Time` 屬性

[**BindingModes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes)範例會示範具有資料系結的四個系結模式，其中目標是的 `FontSize` 屬性 `Label` ，而來源是的 `Value` 屬性 `Slider` 。 這可讓每個 `Slider` 控制項都控制對應的字型大小 `Label` 。 但 `Slider` 因為屬性的是，所以不會初始化元素 `DefaultBindingMode` `FontSize` `OneWay` 。

[**ReverseBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding)範例會在 `Value` `Slider` 參考每個的屬性之的屬性上設定系結 `FontSize` `Label` 。 這似乎是回溯的，但在查閱元素時效果較佳， `Slider` 因為的 `Value` 屬性 `Slider` 具有 `DefaultBindingMode` 的 `TwoWay` 。

[![反向系結的三向螢幕擷取畫面](images/ch16fg06-small.png "反向系結")](images/ch16fg06-large.png#lightbox "反向系結")

這類似于在 MVVM 中定義系結的方式，而您會經常使用這種類型的系結。

## <a name="string-formatting"></a>字串格式

當目標屬性的型別為時 `string` ，您可以使用所 [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) 定義的屬性， `BindingBase` 將來源轉換成 `string` 。 將 `StringFormat` 屬性設定為您要搭配靜態 [`String.Format`](xref:System.String.Format(System.String,System.Object)) 格式使用以顯示物件的 .net 格式字串。 在標記延伸中使用此格式化字串時，請使用單引號括住，讓大括弧不會被視為內嵌標記延伸。

[**ShowViewValues**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues)範例會示範如何 `StringFormat` 在 XAML 中使用。

[**WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings)範例會示範如何以系結至的和屬性，來顯示頁面的大小 `Width` `Height` `ContentPage` 。

## <a name="why-is-it-called-path"></a>為什麼稱之為「Path」？

的 [`Path`](xref:Xamarin.Forms.Binding.Path) 屬性 `Binding` 是，因此會呼叫，因為它可以是以句點分隔的一系列屬性和索引子。 [**BindingPathDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos)範例會顯示數個範例。

## <a name="binding-value-converters"></a>系結值轉換器

當系結的來源和目標屬性是不同類型時，您可以使用系結轉換器在類型之間進行轉換。 這是一個可實作為介面的類別，其中 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 包含兩個方法：將 [`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) 來源轉換成目標，並 [`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) 將目標轉換成來源。

[`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別是將轉換成 `int` 的範例 `bool` 。 它是由[**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler)範例所示範，只有 `Button` 在至少有一個字元輸入時，才會啟用 `Entry` 。

[`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs)類別會將轉換 `bool` 成 `string` ，並定義兩個屬性，以指定應該針對和值傳回的文字 `false` `true` 。
[`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs)類似。 [**SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText)範例會示範如何使用這兩個轉換器，根據設定以不同的色彩來顯示不同的文字 `Switch` 。

泛型 [`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) 可以取代 `BoolToStringConverter` 和 `BoolToColorConverter` ，並做為 `bool` 任何類型的一般化對物件轉換器。

## <a name="bindings-and-custom-views"></a>系結和自訂視圖

您可以使用資料系結來簡化自訂控制項。 [`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs)程式碼檔案會定義 `Text` 、 `TextColor` 、 `FontSize` 、 `FontAttributes` 和 `IsChecked` 屬性，但控制項的視覺效果完全沒有邏輯。
相反地，檔案會 [`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) `Label` 根據程式碼後置檔案中定義的屬性，針對專案的資料系結，包含控制項視覺效果的所有標記。

[**NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo)範例會示範 `NewCheckBox` 自訂控制項。

## <a name="related-links"></a>相關連結

- [第16章的全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [第16章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
