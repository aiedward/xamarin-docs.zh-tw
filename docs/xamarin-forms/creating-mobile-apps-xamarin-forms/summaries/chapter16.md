---
title: 第 16 章的摘要。 資料繫結
description: 使用 Xamarin.Forms 建立行動應用程式： 第 16 章摘要。 資料繫結
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: df3cee3fe71553b63bf4b050cad141b66ad98a06
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056609"
---
# <a name="summary-of-chapter-16-data-binding"></a>第 16 章的摘要。 資料繫結

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> 在此頁面上的附註表示其中 Xamarin.Forms 有分歧活頁簿中所呈現的題材的區域。

程式設計人員通常會發現自己撰寫一個物件的屬性已變更，當偵測的事件處理常式，然後使用它來的另一個物件中的屬性值變更。 此程序可以使用的技術來自動化*資料繫結*。 資料繫結通常在 XAML 中定義，並成為使用者介面的定義的一部分。

通常，這些資料繫結連接使用者介面物件的基礎資料。 這是一種技術，更探索[**第 18 章。MVVM**](chapter18.md)。 不過，資料繫結也可以連接兩個以上的使用者介面項目。 大部分的這一章中的資料繫結的最早的範例會示範這項技術。

## <a name="binding-basics"></a>繫結的基本概念

在資料繫結，需要數個屬性、 方法和類別：

- [ `Binding` ](xref:Xamarin.Forms.Binding)類別衍生自[ `BindingBase` ](xref:Xamarin.Forms.BindingBase)並且封裝許多特性的資料繫結
- [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)屬性由定義[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)類別
- [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))方法也會定義由[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)類別
- [ `BindableObjectExtensions` ](xref:Xamarin.Forms.BindableObjectExtensions)類別會定義三個額外`SetBinding`方法

下列兩個類別的繫結支援 XAML 標記延伸模組：

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) 支援`Binding`標記延伸
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) 支援`x:Reference`標記延伸

在資料繫結，需要兩個介面：

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) 在 `System.ComponentModel`命名空間是用於實作屬性變更時通知
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 用來定義將從一種類型的值轉換到另一個資料繫結中的小型類別

資料繫結會連接兩個相同的物件，或 （更常見） 的兩個不同物件的屬性。 這兩個屬性指*來源*並*目標*。 一般而言，來源屬性的變更會導致變更發生在目標屬性中，但有時候相反的方向。 不論：

- *目標*屬性都必須受 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- *來源*屬性通常是實作的類別成員 [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

類別若實作`INotifyPropertyChanged`引發[ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged)當屬性值變更時引發事件。 `BindableObject` 會實作`INotifyPropertyChanged`，並會自動引發`PropertyChanged`事件時所支援的屬性`BindableProperty`變更值，但您可以撰寫您自己的類別實作`INotifyPropertyChanged`而不需要衍生自`BindableObject`。

## <a name="code-and-xaml"></a>程式碼和 XAML

[ **OpacityBindingCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode)範例會示範如何在程式碼中設定資料繫結：

- 來源是`Value`屬性 `Slider`
- 目標是`Opacity`屬性 `Label`

藉由設定連接兩個物件`BindingContext`的`Label`物件到`Slider`物件。 藉由呼叫連接的兩個屬性[ `SetBinding` ](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*)擴充方法`Label`參考`OpacityProperty`可繫結屬性與`Value`屬性`Slider`表示為字串。

操作`Slider`造成`Label`淡出畫面。

[ **OpacityBindingXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml)是在 XAML 中設定資料繫結相同的程式。 `BindingContext`的`Label`設為`x:Reference`標記延伸模組參考`Slider`，而`Opacity`屬性`Label`設定為`Binding`標記延伸使用其[ `Path`](xref:Xamarin.Forms.Binding.Path)屬性參考`Value`屬性`Slider`。

## <a name="source-and-bindingcontext"></a>來源和 BindingContext

[ **BindingSourceCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode)範例程式碼中顯示的替代方式。 A`Binding`物件由設定[ `Source` ](xref:Xamarin.Forms.Binding.Source)屬性設`Slider`物件和[ `Path` ](xref:Xamarin.Forms.Binding.Path)為 「 值 」 屬性。 [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))方法`BindableObject`然後呼叫`Label`物件。

[ `Binding`建構函式](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object))也可能有已用來定義`Binding`物件。

[ **BindingSourceXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml)範例會顯示類似的技巧在 XAML 中。 `Opacity`屬性`Label`設為`Binding`使用的標記延伸[ `Path` ](xref:Xamarin.Forms.Binding.Path)設定為`Value`屬性和[ `Source` ](xref:Xamarin.Forms.Binding.Source)設為內嵌`x:Reference`標記延伸。

在 [摘要] 中，有兩種方式來參考繫結來源物件：

- 透過`BindingContext`目標屬性
- 透過`Source`屬性`Binding`物件本身

如果同時指定這兩者，第二個的優先順序較高。 利用`BindingContext`是它會傳播到視覺樹狀結構。 這是*非常*方便多個目標屬性都繫結至相同的來源物件。

[ **WebViewDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo)程式會示範如何使用這項技術[ `WebView` ](xref:Xamarin.Forms.WebView)項目。 兩個`Button`瀏覽的項目前和往後繼承`BindingContext`從其父代參考`WebView`。 `IsEnabled`兩個按鈕的屬性則有簡單`Binding`目標按鈕的標記延伸`IsEnabled`屬性為基礎的設定[ `CanGoBack` ](xref:Xamarin.Forms.WebView.CanGoBack)並[ `CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward)唯讀屬性的`WebView`。

## <a name="the-binding-mode"></a>繫結模式

設定[ `Mode` ](xref:Xamarin.Forms.BindingBase.Mode)屬性`Binding`成員[ `BindingMode` ](xref:Xamarin.Forms.BindingMode)列舉型別：

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) 在 [來源] 屬性中的變更會影響目標
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) 目標屬性中的變更會影響來源
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) 來源與目標中的變更會影響彼此
- [`Default`](xref:Xamarin.Forms.BindingMode.Default) 若要使用[ `DefaultBindingMode` ](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode)時指定目標`BindableProperty`所建立。 如果未指定，預設值是`OneWay`一般可繫結的屬性，以及`OneWayToSource`唯讀的可繫結屬性。

> [!NOTE]
> `BindingMode`列舉型別現在也包含`OnTime`套用繫結，而繫結內容變更時才不會在 [來源] 屬性變更。

屬性通常是在 MVVM 案例中的資料繫結的目標可能會有`DefaultBindingMode`的`TwoWay`。 這些是：

- `Value` 屬性`Slider`和 `Stepper`
- `IsToggled` 屬性 `Switch`
- `Text` 屬性`Entry`， `Editor`，及 `SearchBar`
- `Date` 屬性 `DatePicker`
- `Time` 屬性 `TimePicker`

[ **BindingModes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes)範例會示範四個繫結模式，其中的目標是資料繫結`FontSize`屬性`Label`但來源是`Value`屬性`Slider`。 這可讓每個`Slider`來控制對應的字型大小`Label`。 但是`Slider`項目不會初始化，因為`DefaultBindingMode`的`FontSize`屬性是`OneWay`。

[ **ReverseBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding)範例上設定的繫結`Value`屬性`Slider`參考`FontSize`每個屬性`Label`。 這似乎是回溯，但它適用於進一步查閱`Slider`項目因為`Value`屬性`Slider`具有`DefaultBindingMode`的`TwoWay`。

[![反轉繫結的三個螢幕擷取畫面](images/ch16fg06-small.png "反向繫結")](images/ch16fg06-large.png#lightbox "反向繫結")

這是類似於繫結 MVVM 中的定義方式，而且您將使用這種類型的繫結常見問題。

## <a name="string-formatting"></a>格式字串

當目標屬性屬於型別`string`，您可以使用[ `StringFormat` ](xref:Xamarin.Forms.BindingBase.StringFormat)所定義的屬性`BindingBase`將來源轉換成`string`。 設定`StringFormat`屬性，以.NET 格式字串，您會使用靜態[ `String.Format` ](xref:System.String.Format(System.String,System.Object))格式來顯示物件。 當使用這個標記延伸模組內的格式化字串，用單引號因此將不會被誤認為內嵌的標記延伸的大括號。

[ **ShowViewValues** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues)範例示範如何使用`StringFormat`在 XAML 中。

[ **WhatSizeBindings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings)範例會示範如何以繫結至顯示的頁面大小`Width`並`Height`屬性`ContentPage`。

## <a name="why-is-it-called-path"></a>為什麼它被稱為 「 路徑 」？

[ `Path` ](xref:Xamarin.Forms.Binding.Path)屬性`Binding`因此呼叫，因為它可以是一系列的屬性與索引子以句號分隔。 [ **BindingPathDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos)範例會示範幾個範例。

## <a name="binding-value-converters"></a>繫結值轉換器

不同類型的繫結的來源和目標屬性時，您可以使用繫結轉換子的型別之間進行轉換。 這是類別可實作[ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter)介面，並包含兩個方法： [ `Convert` ](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo))將來源轉換到目標，以及[ `ConvertBack` ](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo))若要將目標轉換至來源。

[ `IntToBoolConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫是轉換範例`int`到`bool`。 所示[ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler)範例中，只讓`Button`如果至少一個字元輸入`Entry`。

[ `BoolToStringConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs)類別轉換`bool`來`string`，並定義兩個屬性，以指定應傳回什麼文字`false`並`true`值。
[ `BoolToColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs)類似。 [ **SwitchText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText)範例示範如何使用這些兩個轉換子，以根據不同的色彩顯示不同的文字`Switch`設定。

泛型[ `BoolToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs)可以取代`BoolToStringConverter`並`BoolToColorConverter`而且做為一般化`bool`-至-任何型別的物件轉換子。

## <a name="bindings-and-custom-views"></a>繫結和自訂檢視

您可以簡化使用資料繫結的自訂控制項。 [ `NewCheckBox.cs` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs)程式碼檔案會定義`Text`， `TextColor`， `FontSize`， `FontAttributes`，以及`IsChecked`屬性，但有控制項的視覺效果完全沒有邏輯。
相反地[ `NewCheckBox.cs.xaml` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml)檔案包含透過資料繫結控制項的視覺效果的所有標記，在`Label`項目為基礎的程式碼後置檔案中定義的屬性。

[ **NewCheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo)範例會示範`NewCheckBox`自訂控制項。



## <a name="related-links"></a>相關連結

- [第 16 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [第 16 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
