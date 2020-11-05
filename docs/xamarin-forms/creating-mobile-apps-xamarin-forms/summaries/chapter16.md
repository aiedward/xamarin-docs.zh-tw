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
ms.openlocfilehash: 721d68db48843ee614f16d4c4237f2c753319561
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373480"
---
# <a name="summary-of-chapter-16-data-binding"></a>第16章的摘要。 資料繫結

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

程式設計師通常會在撰寫事件處理常式時，偵測到某個物件的屬性已變更時，並使用該處理常式來變更另一個物件中的屬性值。 您可以使用 *資料* 系結的技巧來自動化此程式。 資料系結通常會在 XAML 中定義，並且成為使用者介面定義的一部分。

這些資料系結通常會將使用者介面物件連接至基礎資料。 這是在第18章中探索更多的技術 [**。MVVM**](chapter18.md)。 不過，資料系結也可以連接兩個或多個使用者介面元素。 本章的大部分早期資料系結範例都會示範這項技巧。

## <a name="binding-basics"></a>系結基本概念

資料系結牽涉到許多屬性、方法和類別：

- [`Binding`](xref:Xamarin.Forms.Binding)類別衍生自 [`BindingBase`](xref:Xamarin.Forms.BindingBase) ，並封裝資料系結的許多特性
- [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)屬性是由類別所定義 [`BindableObject`](xref:Xamarin.Forms.BindableObject)
- [ `SetBinding` ] (x： Xamarin.Forms 。BindableObject. SetBinding (Xamarin.Forms 。BindableProperty， Xamarin.Forms 。System.windows.data.bindingbase.delay) # A3 方法也是由類別所定義 [`BindableObject`](xref:Xamarin.Forms.BindableObject)
- [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions)類別會定義三個額外的 `SetBinding` 方法

下列兩個類別支援系結的 XAML 標記延伸：

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) 支援 `Binding` 標記延伸
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) 支援 `x:Reference` 標記延伸

資料系結牽涉到兩個介面：

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) 在 `System.ComponentModel` 命名空間中，用於在屬性變更時執行通知
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 用來定義在資料系結中，將值從某個類型轉換成另一個類型的小型類別

資料系結會連接相同物件的兩個屬性，或 (更常見的) 兩個不同的物件。 這兩個屬性稱為「 *來源* 」和「 *目標* 」。 一般而言，source 屬性中的變更會導致目標屬性發生變更，但有時方向會反轉。 無論：

- *目標* 屬性必須支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- *source* 屬性通常是實作為類別的成員。 [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

`INotifyPropertyChanged` [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) 當屬性變更值時，implements 會引發事件的類別。 `BindableObject``INotifyPropertyChanged` `PropertyChanged` 當屬性支援變更值時，會執行並自動引發事件 `BindableProperty` ，但您可以撰寫自己的類別， `INotifyPropertyChanged` 而不需要衍生自 `BindableObject` 。

## <a name="code-and-xaml"></a>程式碼和 XAML

[**OpacityBindingCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode)範例示範如何在程式碼中設定資料系結：

- 來源是的 `Value` 屬性 `Slider`
- 目標是的 `Opacity` 屬性 `Label`

這兩個物件的連接方式是將物件的設定 `BindingContext` `Label` 為 `Slider` 物件。 這兩個屬性的連接方式是 [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) 在參考可系結屬性的上呼叫擴充方法 `Label` `OpacityProperty` ，以及 `Value` `Slider` 以字串表示的屬性。

然後操作 `Slider` 會導致 `Label` 淡入或移出視野。

[**OpacityBindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml)與在 XAML 中設定資料系結的程式相同。 的 `BindingContext` `Label` 會設定為 `x:Reference` 參考的標記延伸 `Slider` ，且的 `Opacity` 屬性 `Label` 會設定為 `Binding` 標記延伸，且其屬性會參考的 [`Path`](xref:Xamarin.Forms.Binding.Path) `Value` 屬性 `Slider` 。

## <a name="source-and-bindingcontext"></a>來源和 BindingCoNtext

[**BindingSourceCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode)範例顯示程式碼中的替代方法。 建立 `Binding` 物件的方式是將屬性（property）設定 [`Source`](xref:Xamarin.Forms.Binding.Source) 為 `Slider` 物件，並將屬性（property）設定 [`Path`](xref:Xamarin.Forms.Binding.Path) 為 "Value"。 [ `SetBinding` ] (x： Xamarin.Forms 。BindableObject. SetBinding (Xamarin.Forms 。BindableProperty， Xamarin.Forms 。System.windows.data.bindingbase.delay) # A3 方法的 `BindableObject` 會在物件上呼叫 `Label` 。

[函式 `Binding` ] (x： Xamarin.Forms 。Binding .% 23ctor (System.string， Xamarin.Forms 。BindingMode， Xamarin.Forms 。IValueConverter、System.object、System.string、System.object) # A3 也可以用來定義 `Binding` 物件。

[**BindingSourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml)範例會在 XAML 中顯示可比較的技巧。 的 `Opacity` 屬性 `Label` 會設定為 `Binding` 標記延伸，並設定為 [`Path`](xref:Xamarin.Forms.Binding.Path) `Value` 屬性，並 [`Source`](xref:Xamarin.Forms.Binding.Source) 設定為內嵌 `x:Reference` 標記延伸。

總而言之，有兩種方式可以參考系結來源物件：

- 透過 `BindingContext` 目標的屬性
- 透過 `Source` 物件本身的屬性 `Binding`

如果同時指定兩者，則會優先使用第二個。 的優點 `BindingContext` 是它會透過視覺化樹狀結構傳播。 如果多個目標屬性系結至相同的來源物件，這就 *非常* 有用。

[**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo)程式會使用元素來示範這項技巧 [`WebView`](xref:Xamarin.Forms.WebView) 。 `Button`向前及向後流覽的兩個元素 `BindingContext` 會從其父系繼承參考的 `WebView` 。 `IsEnabled`這兩個按鈕的屬性接著會有簡單的 `Binding` 標記延伸，其 `IsEnabled` 根據的 [`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack) 和唯讀屬性的設定，將目標設為按鈕屬性 [`CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward) `WebView` 。

## <a name="the-binding-mode"></a>系結模式

將的 [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) 屬性設定 `Binding` 為列舉的成員 [`BindingMode`](xref:Xamarin.Forms.BindingMode) ：

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) 因此，source 屬性中的變更會影響目標
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) 因此目標屬性中的變更會影響來源
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) 因此，來源和目標中的變更會影響彼此
- [`Default`](xref:Xamarin.Forms.BindingMode.Default) 在 [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) 建立目標時使用指定的 `BindableProperty` 。 如果未指定，則預設值為 `OneWay` 適用于一般可系結屬性，以及唯讀可系結屬性的預設值 `OneWayToSource` 。

> [!NOTE]
> `BindingMode`列舉現在也包含在系結 `OnTime` 內容變更時套用系結，而不是在來源屬性變更時套用。

在 MVVM 案例中，可能是資料系結目標的屬性通常會有 `DefaultBindingMode` 的 `TwoWay` 。 這些警告是：

- `Slider`和 `Stepper` 的 `Value` 屬性
- `Switch` 的 `IsToggled` 屬性
- `Text``Entry`、和的 `Editor` 屬性`SearchBar`
- `DatePicker` 的 `Date` 屬性
- `TimePicker` 的 `Time` 屬性

[**BindingModes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes)範例會使用資料系結來示範四個系結模式，其中目標是的 `FontSize` 屬性 `Label` ，而來源是的 `Value` 屬性 `Slider` 。 這可讓每個都 `Slider` 控制對應的字型大小 `Label` 。 但 `Slider` 因為屬性的是，所以不會初始化元素 `DefaultBindingMode` `FontSize` `OneWay` 。

[**ReverseBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding)範例會在 `Value` `Slider` 參考 `FontSize` 每個屬性之屬性的屬性上設定系結 `Label` 。 這似乎是回溯的，但在查閱元素時效果最好， `Slider` 因為的 `Value` 屬性 `Slider` 具有 `DefaultBindingMode` 的 `TwoWay` 。

[![反向綁定的三重螢幕擷取畫面](images/ch16fg06-small.png "反向綁定")](images/ch16fg06-large.png#lightbox "反向綁定")

這類似于 MVVM 中定義系結的方式，而且您會經常使用這種類型的系結。

## <a name="string-formatting"></a>字串格式化

當目標屬性的類型為時 `string` ，您可以使用所 [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) 定義的屬性， `BindingBase` 將來源轉換成 `string` 。 將 `StringFormat` 屬性設為 .net 格式化字串，您可以使用靜態 [`String.Format`](xref:System.String.Format(System.String,System.Object)) 格式來顯示物件。 在標記延伸中使用此格式化字串時，請使用單引號括住它，如此一來，就不會有內嵌標記延伸的大括弧。

[**ShowViewValues**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues)範例示範如何 `StringFormat` 在 XAML 中使用。

[**WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings)範例示範如何以系結至的和屬性來顯示頁面的大小 `Width` `Height` `ContentPage` 。

## <a name="why-is-it-called-path"></a>為什麼它稱為「路徑」？

的 [`Path`](xref:Xamarin.Forms.Binding.Path) 屬性是所謂的， `Binding` 因為它可以是一系列以句號分隔的屬性和索引子。 [**BindingPathDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos)範例會顯示數個範例。

## <a name="binding-value-converters"></a>系結值轉換器

當系結的來源和目標屬性是不同的型別時，您可以使用系結轉換器來轉換型別。 這是實作為 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 介面且包含兩種方法的類別：將 [`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) 來源轉換成目標，以及將 [`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) 目標轉換為來源。

[`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) [**Xamarin.Forms 書籍**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別是將轉換成 `int` 的範例 `bool` 。 它是由 [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) 範例所示範，只有 `Button` 當至少有一個字元輸入時，才會啟用 `Entry` 。

[`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs)類別會將轉換 `bool` 成 `string` ，並定義兩個屬性，以指定應該針對和值傳回的文字 `false` `true` 。
[`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs)很類似。 [**SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText)範例會示範如何使用這兩個轉換器，根據設定以不同的色彩顯示不同的文字 `Switch` 。

泛型 [`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) 可以取代 `BoolToStringConverter` 和 `BoolToColorConverter` ，做為 `bool` 任何型別的一般化對物件轉換器。

## <a name="bindings-and-custom-views"></a>系結和自訂視圖

您可以使用資料系結來簡化自訂控制項。 [`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs)程式碼檔案會定義 `Text` 、、 `TextColor` `FontSize` 、 `FontAttributes` 和 `IsChecked` 屬性，但控制項的視覺效果完全沒有邏輯。
相反地，檔案會 [`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) `Label` 根據程式碼後端檔案中定義的屬性，在專案的資料系結中包含控制項視覺效果的所有標記。

[**NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo)範例會示範 `NewCheckBox` 自訂控制項。

## <a name="related-links"></a>相關連結

- [第16章 (PDF 的完整文字) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [第16章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
