---
title: "第 16 章的摘要。 資料繫結"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: cf08874f66c9ab21cd0ede642c8c94821b6c5a2a
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-16-data-binding"></a>第 16 章的摘要。 資料繫結

程式設計人員通常會發現自己撰寫當一個物件的屬性變更時，偵測到的事件處理常式，然後使用它來中另一個物件的屬性值變更。 此程序可以自動化的技術與*資料繫結*。 資料繫結通常定義在 XAML 中，會成為使用者介面的定義的一部分。

通常，這些資料繫結連接使用者介面物件基礎的資料。 這是會探索更中技巧[**第 18 章。MVVM**](chapter18.md). 不過，資料繫結也可以連接兩個以上的使用者介面項目。 大部分的這一章中的資料繫結的最早的範例會示範這項技術。

## <a name="binding-basics"></a>繫結的基本概念

數個屬性、 方法和類別參與資料繫結：

- [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/)類別衍生自[ `BindingBase` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingBase/)並且封裝許多特性的資料繫結
- [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)屬性由定義[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)類別
- [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)方法也有定義由[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)類別
- [ `BindableObjectExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObjectExtensions/)類別會定義三個額外`SetBinding`方法

下列兩個類別支援 XAML 標記延伸的繫結：

- [`BindingExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) 支援`Binding`標記延伸
- [`ReferenceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/) 支援`x:Reference`標記延伸

資料繫結涉及兩個介面：

- [`INotifyPropertyChanged`](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) 在`System.ComponentModel`命名空間會實作屬性變更時通知
- [`IValueConverter`](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) 用來定義將值從一個類型轉換到另一個資料繫結中的小型類別

資料繫結會連接兩個相同的物件或 （更常） 的兩個不同物件的屬性。 這兩個屬性會參照為*來源*和*目標*。 一般而言，來源屬性的變更會導致的變更會出現在 [目標] 屬性，但有時反轉方向。 無論：

- *目標*屬性必須在支援 [`BindableProperty`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- *來源*屬性通常不會實作類別的成員 [`INotifyPropertyChanged`](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/)

類別可實作`INotifyPropertyChanged`引發[ `PropertyChanged` ](https://developer.xamarin.com/api/event/System.ComponentModel.INotifyPropertyChanged.PropertyChanged/)當屬性值變更時引發事件。 `BindableObject` 實作`INotifyPropertyChanged`，並且會自動在`PropertyChanged`事件時所支援的屬性`BindableProperty`變更值，但您可以撰寫您自己的類別，實作`INotifyPropertyChanged`沒有衍生自`BindableObject`。

## <a name="code-and-xaml"></a>程式碼和 XAML

[ **OpacityBindingCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode)範例會示範如何在程式碼中設定資料繫結：

- 來源是`Value`屬性 `Slider`
- 目標是`Opacity`屬性 `Label`

藉由設定連接兩個物件`BindingContext`的`Label`物件`Slider`物件。 藉由呼叫連接兩個屬性[ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObjectExtensions.SetBinding/p/Xamarin.Forms.BindableObject/Xamarin.Forms.BindableProperty/System.String/)上的擴充方法`Label`參考`OpacityProperty`可繫結屬性和`Value`屬性`Slider`以表示字串。

操作`Slider`然後卻造成`Label`進入和離開檢視淡出。

[ **OpacityBindingXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml)是相同的程式，以在 XAML 中設定資料繫結。 `BindingContext`的`Label`設`x:Reference`標記延伸模組參考`Slider`，和`Opacity`屬性`Label`設`Binding`具有標記延伸其[ `Path`](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/)屬性參考`Value`屬性`Slider`。

## <a name="source-and-bindingcontext"></a>來源和其他

[ **BindingSourceCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode)範例顯示程式碼中的替代方式。 A`Binding`物件由設定[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Source/)屬性`Slider`物件和[ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/)為 「 值 」 屬性。 [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)方法`BindableObject`上再呼叫`Label`物件。

[ `Binding`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Binding.Binding/p/System.String/Xamarin.Forms.BindingMode/Xamarin.Forms.IValueConverter/System.Object/System.String/System.Object/)可能也已經使用來定義`Binding`物件。

[ **BindingSourceXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml)範例顯示 XAML 中的可比較的技巧。 `Opacity`屬性`Label`設`Binding`具有標記延伸[ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/)設`Value`屬性和[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Source/)設為內嵌`x:Reference`標記延伸。

在 [摘要] 中，有兩種方式可參考繫結來源物件：

- 透過`BindingContext`目標屬性
- 透過`Source`屬性`Binding`物件本身

如果同時指定這兩者，第二個會優先使用。 優點`BindingContext`會傳播至視覺化樹狀結構。 這是*非常*方便目標的多個屬性都繫結至相同的來源物件。

[ **WebViewDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo)程式會示範如何使用這項技術[ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)項目。 兩個`Button`巡覽的項目前後繼承`BindingContext`從其父代參考`WebView`。 `IsEnabled`兩個按鈕的屬性則有簡單`Binding`目標按鈕的標記延伸`IsEnabled`屬性為基礎的設定[ `CanGoBack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.CanGoBack/)和[ `CanGoForward`](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.CanGoForward/)的唯讀屬性`WebView`。

## <a name="the-binding-mode"></a>繫結模式

設定[ `Mode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.Mode/)屬性`Binding`成員[ `BindingMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingMode/)列舉型別：

- [`OneWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWay/) [來源] 屬性中的變更會影響目標
- [`OneWayToSource`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWayToSource/) 使目標屬性的變更會影響來源
- [`TwoWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/) 使來源和目標的變更會影響彼此
- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.Default/) 若要使用[ `DefaultBindingMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableProperty.DefaultBindingMode/)時指定目標`BindableProperty`所建立。 如果沒有指定，預設值是`OneWay`一般的繫結屬性和`OneWayToSource`唯讀可繫結的屬性。

屬性通常是在 MVVM 案例中的資料繫結的目標可能會有`DefaultBindingMode`的`TwoWay`。 這些是：

- `Value` 屬性`Slider`和 `Stepper`
- `IsToggled` 屬性 `Switch`
- `Text` 屬性`Entry`， `Editor`，和 `SearchBar`
- `Date` 屬性 `DatePicker`
- `Time` 屬性 `TimePicker`

[ **BindingModes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes)範例將示範四個繫結模式如何與資料繫結目標位置是`FontSize`屬性`Label`且來源是`Value`屬性`Slider`。 這可讓每個`Slider`來控制對應的字型大小`Label`。 但是`Slider`元素不會初始化，因為`DefaultBindingMode`的`FontSize`屬性是`OneWay`。

[ **ReverseBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding)範例上設定的繫結`Value`屬性`Slider`參考`FontSize`每個屬性`Label`。 這似乎是回溯，但它更適用於初始化`Slider`元素因為`Value`屬性`Slider`具有`DefaultBindingMode`的`TwoWay`。

[![反轉繫結的三個螢幕擷取畫面](images/ch16fg06-small.png "反向繫結")](images/ch16fg06-large.png#lightbox "反向繫結")

這是類似於 MVVM 中已定義的繫結，以及您將使用這種類型的繫結常見問題。

## <a name="string-formatting"></a>字串格式

當目標屬性屬於型別`string`，您可以使用[ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.StringFormat/)屬性所定義`BindingBase`將來源轉換成`string`。 設定`StringFormat`.NET 格式字串，您會使用靜態屬性[ `String.Format` ](https://developer.xamarin.com/api/member/System.String.Format/p/System.String/System.Object/)格式來顯示物件。 時使用此格式的字串內的標記延伸，請用單引號因此將不會被誤認為內嵌的標記延伸的大括號。

[ **ShowViewValues** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues)範例示範如何使用`StringFormat`在 XAML 中。

[ **WhatSizeBindings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings)範例將示範如何使用繫結以顯示頁面大小`Width`和`Height`屬性`ContentPage`。

## <a name="why-is-it-called-path"></a>為什麼它被稱為 「 路徑 」？

[ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/)屬性`Binding`因此稱為，因為它可以是一系列的屬性與索引子以句號分隔。 [ **BindingPathDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos)範例示範數個範例。

## <a name="binding-value-converters"></a>繫結值轉換器

不同類型的繫結的來源和目標屬性時，您可以使用繫結轉換器的類型之間進行轉換。 這是一個類別，實作[ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/)介面，並包含兩個方法： [ `Convert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IValueConverter.Convert/p/System.Object/System.Type/System.Object/System.Globalization.CultureInfo/)轉換至目標，來源和[ `ConvertBack` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IValueConverter.ConvertBack/p/System.Object/System.Type/System.Object/System.Globalization.CultureInfo/)要轉換的目標到來源。

[ `IntToBoolConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫是轉換的範例`int`至`bool`。 它示範[ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler)範例中，只讓`Button`如果至少一個字元輸入`Entry`。

[ `BoolToStringConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs)類別轉換`bool`至`string`並定義兩個屬性，以指定應該針對傳回什麼文字`false`和`true`值。
[ `BoolToColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs)類似。 [ **SwitchText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText)範例將示範如何使用這些兩個轉換工具來顯示不同的色彩為基礎的不同文字`Switch`設定。

泛型[ `BoolToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs)可以取代`BoolToStringConverter`和`BoolToColorConverter`和做為一般化`bool`-到-任何型別的物件轉換子。

## <a name="bindings-and-custom-views"></a>繫結和自訂檢視

您可以簡化使用資料繫結的自訂控制項。 [ `NewCheckBox.cs` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs)程式碼檔案會定義`Text`， `TextColor`， `FontSize`， `FontAttributes`，和`IsChecked`屬性，但沒有邏輯在為控制項的視覺效果。
改為[ `NewCheckBox.cs.xaml` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml)檔案包含透過資料繫結控制項的視覺效果的標記上`Label`元素為基礎的程式碼後置檔案中定義的屬性。

[ **NewCheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo)範例將示範如何`NewCheckBox`自訂控制項。



## <a name="related-links"></a>相關連結

- [第 16 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [第 16 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
