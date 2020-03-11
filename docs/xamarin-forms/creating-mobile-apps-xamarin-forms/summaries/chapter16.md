---
title: 第16章的摘要。 資料繫結
description: 使用 Xamarin 建立 Mobile Apps：第16章的摘要。 資料繫結
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 2d61413fb1d8c28a3957da53601d0ad682f35518
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771096"
---
# <a name="summary-of-chapter-16-data-binding"></a>第16章的摘要。 資料繫結

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> 此頁面上的附注指出 Xamarin 從書籍中呈現的材料中分歧的區域。

程式設計人員通常會發現自己撰寫的事件處理常式，會偵測到某個物件的屬性何時變更，並使用它來變更另一個物件中的屬性值。 此程式可以使用*資料*系結的技術來自動化。 資料系結通常是在 XAML 中定義，並成為使用者介面定義的一部分。

這些資料系結通常會將使用者介面物件連接到基礎資料。 這是在第18章中更深入探討的技術[ **。MVVM**](chapter18.md)。 不過，資料系結也可以連接兩個或多個使用者介面元素。 本章中大部分的資料系結早期範例都會示範這項技術。

## <a name="binding-basics"></a>系結基本概念

資料系結牽涉到數個屬性、方法和類別：

- [`Binding`](xref:Xamarin.Forms.Binding)類別衍生自[`BindingBase`](xref:Xamarin.Forms.BindingBase) ，並封裝資料系結的許多特性
- [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)屬性是由[`BindableObject`](xref:Xamarin.Forms.BindableObject)類別所定義
- [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))方法也是由[`BindableObject`](xref:Xamarin.Forms.BindableObject)類別所定義
- [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions)類別會定義三個額外的 `SetBinding` 方法

下列兩個類別支援系結的 XAML 標記延伸：

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)支援 `Binding` 標記延伸
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)支援 `x:Reference` 標記延伸

資料系結牽涉到兩個介面：

- `System.ComponentModel` 命名空間中的[`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)是在屬性變更時用來執行通知
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter)可用來定義在資料系結中，將值從一個類型轉換成另一種的小型類別

資料系結會連接相同物件的兩個屬性，或（更常見）兩個不同的物件。 這兩個屬性稱為「*來源*」和「*目標*」。 一般來說，source 屬性中的變更會導致目標屬性發生變更，但有時方向會相反。 忽略

- *目標*屬性必須受到[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)的支援
- *來源*屬性通常是可執行之類別的成員[`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

當屬性變更值時，執行的類別會引發[`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged)事件 `INotifyPropertyChanged`。 `BindableObject` 會在 `BindableProperty` 所支援的屬性變更值時，執行 `INotifyPropertyChanged` 並自動引發 `PropertyChanged` 事件，但您可以撰寫自己的類別來實 `INotifyPropertyChanged`，而不需要從 `BindableObject`衍生。

## <a name="code-and-xaml"></a>程式碼和 XAML

[**OpacityBindingCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode)範例會示範如何在程式碼中設定資料系結：

- 來源是 `Slider` 的 `Value` 屬性
- 目標是 `Label` 的 `Opacity` 屬性

這兩個物件會藉由將 `Label` 物件的 `BindingContext` 設定為 `Slider` 物件來進行連接。 這兩個屬性的連接方式是在參考 `OpacityProperty` 可系結屬性的 `Label` 上呼叫[`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*)擴充方法，以及以字串表示之 `Slider` 的 `Value` 屬性。

操作 `Slider` 會導致 `Label` 淡入和移出畫面。

[**OpacityBindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml)是與 XAML 中的資料系結集相同的程式。 `Label` 的 `BindingContext` 會設定為參考 `Slider`的 `x:Reference` 標記延伸，而 `Opacity` 的 `Label` 屬性會設定為 `Binding` 標記延伸，其[`Path`](xref:Xamarin.Forms.Binding.Path)屬性會參考 `Value` 的 `Slider`屬性。

## <a name="source-and-bindingcontext"></a>來源和 BindingCoNtext

[**BindingSourceCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode)範例顯示程式碼中的替代方法。 `Binding` 物件的建立方式是將[`Source`](xref:Xamarin.Forms.Binding.Source)屬性設為 `Slider` 物件，並將[`Path`](xref:Xamarin.Forms.Binding.Path)屬性設定為 "Value"。 然後在 `Label` 物件上呼叫 `BindableObject` 的[`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))方法。

[`Binding`](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object))的函式也可以用來定義 `Binding` 物件。

[**BindingSourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml)範例顯示 XAML 中可比較的技術。 `Label` 的 `Opacity` 屬性會設定為 `Binding` 標記延伸， [`Path`](xref:Xamarin.Forms.Binding.Path)設定為 `Value` 屬性， [`Source`](xref:Xamarin.Forms.Binding.Source)設定為內嵌的 `x:Reference` 標記延伸。

總而言之，有兩種方式可以參考系結來源物件：

- 透過目標的 `BindingContext` 屬性
- 透過 `Binding` 物件本身的 `Source` 屬性

如果同時指定這兩者，則會優先使用第二個。 `BindingContext` 的優點是它會透過視覺化樹狀結構傳播。 如果多個目標屬性系結至相同的來源物件，這就*很*方便。

[**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo)程式會使用[`WebView`](xref:Xamarin.Forms.WebView)元素來示範這項技術。 向後導覽的兩個 `Button` 元素會繼承其父系中參考 `WebView`的 `BindingContext`。 這兩個按鈕的 `IsEnabled` 屬性接著會有簡單的 `Binding` 標記延伸，其會根據[`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack)的設定以及`CanGoForward`的唯讀屬性[，以](xref:Xamarin.Forms.WebView.CanGoForward)按鈕 `IsEnabled` 屬性為目標。`WebView`

## <a name="the-binding-mode"></a>系結模式

將 `Binding` 的[`Mode`](xref:Xamarin.Forms.BindingBase.Mode)屬性設定為[`BindingMode`](xref:Xamarin.Forms.BindingMode)列舉的成員：

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) ，讓 source 屬性中的變更影響目標
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) ，以便目標屬性中的變更會影響來源
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，以讓來源和目標中的變更彼此影響
- [`Default`](xref:Xamarin.Forms.BindingMode.Default)在建立目標 `BindableProperty` 時，使用指定的[`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) 。 如果未指定任何，則一般可系結屬性的預設值為 `OneWay`，而 `OneWayToSource` 適用于唯讀的可系結屬性。

> [!NOTE]
> `BindingMode` 列舉現在也包含只有在系結內容變更時才套用系結的 `OnTime`，而當來源屬性變更時，則不會套用。

在 MVVM 案例中，可能是資料系結目標的屬性通常會有 `TwoWay`的 `DefaultBindingMode`。 它們是：

- `Value`和 `Slider` 的 `Stepper` 屬性
- `IsToggled` 的 `Switch` 屬性
- `Entry`、`Editor`和 `SearchBar` 的 `Text` 屬性
- `Date` 的 `DatePicker` 屬性
- `Time` 的 `TimePicker` 屬性

[**BindingModes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes)範例會示範具有資料系結的四個系結模式，其中目標是 `Label` 的 `FontSize` 屬性，而來源是 `Slider`的 `Value` 屬性。 這可讓每個 `Slider` 控制對應 `Label`的字型大小。 但是 `Slider` 的元素不會初始化，因為 `FontSize` 屬性的 `DefaultBindingMode` 是 `OneWay`的。

[**ReverseBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding)範例會在 `Slider` 參考每個 `Label`之 `FontSize` 屬性的 `Value` 屬性（property）上設定系結。 這似乎是回溯的，但在查閱 `Slider` 元素時效果更好，因為 `Slider` 的 `Value` 屬性有 `TwoWay`的 `DefaultBindingMode`。

[![反向系結的三向螢幕擷取畫面](images/ch16fg06-small.png "反向系結")](images/ch16fg06-large.png#lightbox "反向系結")

這類似于在 MVVM 中定義系結的方式，而您會經常使用這種類型的系結。

## <a name="string-formatting"></a>字串格式

當目標屬性的類型是 `string`時，您可以使用 `BindingBase` 所定義的[`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat)屬性，將來源轉換成 `string`。 將 [`StringFormat`] 屬性設定為您要搭配靜態[`String.Format`](xref:System.String.Format(System.String,System.Object))格式使用的 .net 格式字串，以顯示物件。 在標記延伸中使用此格式化字串時，請使用單引號括住，讓大括弧不會被視為內嵌標記延伸。

[**ShowViewValues**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues)範例會示範如何在 XAML 中使用 `StringFormat`。

[**WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings)範例會示範如何使用系結至 `Width` 的 `Height` 和 `ContentPage`的屬性，來顯示頁面的大小。

## <a name="why-is-it-called-path"></a>為什麼稱之為「Path」？

`Binding` 的[`Path`](xref:Xamarin.Forms.Binding.Path)屬性會呼叫，因為它可以是以句點分隔的一系列屬性和索引子。 [**BindingPathDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos)範例會顯示數個範例。

## <a name="binding-value-converters"></a>系結值轉換器

當系結的來源和目標屬性是不同類型時，您可以使用系結轉換器在類型之間進行轉換。 這是一個類別，它會執行[`IValueConverter`](xref:Xamarin.Forms.IValueConverter)介面並包含兩個方法： [`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo))將來源轉換成目標，並[`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo))將目標轉換成來源。

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的[`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs)類別，是將 `int` 轉換為 `bool`的範例。 它是由[**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler)範例所示範，只有在至少有一個字元輸入 `Entry`時，才會啟用 `Button`。

[`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs)類別會將 `bool` 轉換成 `string`，並定義兩個屬性，以指定要針對 `false` 和 `true` 值傳回的文字。
[`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs)類似。 [**SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText)範例會示範如何使用這兩個轉換器，根據 `Switch` 設定以不同的色彩來顯示不同的文字。

泛型[`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs)可以取代 `BoolToStringConverter` 和 `BoolToColorConverter`，並做為任何類型的一般化 `bool`對物件轉換器。

## <a name="bindings-and-custom-views"></a>系結和自訂視圖

您可以使用資料系結來簡化自訂控制項。 [`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs)程式碼檔案會定義 `Text`、`TextColor`、`FontSize`、`FontAttributes`和 `IsChecked` 屬性，但沒有任何邏輯適用于控制項的視覺效果。
相反地， [`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml)檔案會根據程式碼後置檔案中定義的屬性，在 `Label` 元素的資料系結中包含控制項視覺效果的所有標記。

[**NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo)範例會示範 `NewCheckBox` 的自訂控制項。

## <a name="related-links"></a>相關連結

- [第16章的全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [第16章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
