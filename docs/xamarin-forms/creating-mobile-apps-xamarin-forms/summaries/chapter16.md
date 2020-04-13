---
title: 第16章摘要。 資料繫結
description: 使用 Xamarin.表單創建行動應用程式:第 16 章摘要。 資料繫結
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 2d61413fb1d8c28a3957da53601d0ad682f35518
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771096"
---
# <a name="summary-of-chapter-16-data-binding"></a>第16章摘要。 資料繫結

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> 本頁的註釋指示 Xamarin.Forms 與本書中介紹的材料有分歧的領域。

程式師經常發現自己正在編寫事件處理程式,以檢測一個物件的屬性何時發生更改,並使用它更改另一個對象中屬性的值。 這個過程可以通過*數據綁定*技術實現自動化。 數據綁定通常在 XAML 中定義,並成為使用者介面定義的一部分。

通常,這些數據綁定將使用者介面物件連接到基礎數據。 這是第 18 章中本文中本文中本文本文中本文本文中本文本文對此進行深入探討的技術[**。MVVM**](chapter18.md). 但是,數據綁定還可以連接兩個或多個用戶介面元素。 本章中的大部分早期數據綁定範例演示了此技術。

## <a name="binding-basics"></a>繫結基礎知識

資料繫結涉及多個屬性、方法和類別:

- 該[`Binding`](xref:Xamarin.Forms.Binding)類派生[`BindingBase`](xref:Xamarin.Forms.BindingBase)自 並封裝了數據綁定的許多特徵
- 屬性[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)由[`BindableObject`](xref:Xamarin.Forms.BindableObject)類別定義
- 該方法[`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))也由[`BindableObject`](xref:Xamarin.Forms.BindableObject)類別定義
- 該[`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions)類別定義了三種`SetBinding`其他 方法

以下兩個類別支援器 XAML 標籤延伸:

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)支援`Binding`標籤延伸
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)支援`x:Reference`標籤延伸

資料繫結涉及兩個介面:

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)命名`System.ComponentModel`空間中用於在屬性變更時執行通知
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter)定義在資料繫結中將值從一種類型轉換為另一種類型的小類別

數據繫結連接同一物件的兩個屬性,或(更常見)兩個不同的物件。 這兩個屬性稱為*源**和目標。* 通常,源屬性的更改會導致目標屬性中發生更改,但有時方向將反轉。 無論:

- *目標*屬性必須由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- *來源*屬性通常是實作[`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

在屬性更改值`INotifyPropertyChanged`時[`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged)實現 事件的類。 `BindableObject`當`INotifyPropertyChanged``PropertyChanged``BindableProperty`由 值支援的屬性時實現並自動觸發事件,但您可以編寫`INotifyPropertyChanged`自己的實作類別,而無需`BindableObject`派生自 。

## <a name="code-and-xaml"></a>代碼與 XAML

[**不一部份繫結程式的程式**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode)範例如何在代碼中設定資料繫結:

- 源是`Value``Slider`
- 目標是`Opacity``Label`

通過將物件的物件`BindingContext`設置`Label``Slider`為 物件,將兩個對象連接。 這兩個[`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*)`Label`屬性通過在引用`OpacityProperty`可 綁定`Value`屬性和 作為字串表示`Slider`的屬性 上調用擴展方法進行連接。

操作`Slider``Label`然後會導致淡入和淡出視圖。

[**不一致 BindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml)與 XAML 中的數據綁定集是同一程式。 的`BindingContext``Label`設定為參考`x:Reference`的`Slider`標記延伸,並將`Opacity`的屬性`Label`設定`Binding`為 標記延伸[`Path`](xref:Xamarin.Forms.Binding.Path),屬性`Value`參考的屬性參考的`Slider`屬性 。

## <a name="source-and-bindingcontext"></a>來源與繫結上下文

[**綁定原始程式碼**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode)範例在代碼中顯示了替代方法。 通過將`Binding`[`Source`](xref:Xamarin.Forms.Binding.Source)屬性設置`Slider`為 物件[`Path`](xref:Xamarin.Forms.Binding.Path),將 屬性設置為"值"來創建物件。 然後在[`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))`Label`物件`BindableObject`上 調用 方法。

構造函數 也可用於`Binding`定義 物件。 [ `Binding`](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object))

[**綁定SourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml)示例顯示了 XAML 中的可比技術。 `Label`的屬性`Opacity`設置`Binding`為 標記[`Path`](xref:Xamarin.Forms.Binding.Path)擴展,`Value`將設置為[`Source`](xref:Xamarin.Forms.Binding.Source)屬性,並`x:Reference`設置為嵌入 標記擴展。

總之,有兩種方法可以引用綁定源物件:

- 以目標`BindingContext`屬性
- `Source`透過物件本身的屬性`Binding`

如果兩者均指定,則第二個優先。 的優點`BindingContext`是它通過可視化樹傳播。 如果多個目標屬性綁定到同一源物件,則此功能*非常*方便。

[**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo)程式[`WebView`](xref:Xamarin.Forms.WebView)使用 元素展示此技術。 向`Button`後與向前瀏覽的兩個元素`BindingContext`從參考的父元素`WebView`繼承 。 然後`IsEnabled`,兩個按鈕的屬性具有`Binding`簡單的 標記擴展,這些擴展`IsEnabled`[`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack)基於 按鈕[`CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward)`WebView`屬性的設置 和唯讀屬性。

## <a name="the-binding-mode"></a>繫結模式

設定[`Mode`](xref:Xamarin.Forms.BindingBase.Mode)為`Binding`枚舉的成員: [`BindingMode`](xref:Xamarin.Forms.BindingMode)

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay)以便來源屬性中的變更影響目標
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource)以目標屬性中改變影響來源
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)以便源和目標的變化相互影響
- [`Default`](xref:Xamarin.Forms.BindingMode.Default)使用創建目標[`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode)時指定的`BindableProperty`。 如果未指定,則預設值為`OneWay`普通可綁定屬性和`OneWayToSource`唯讀可綁定屬性。

> [!NOTE]
> 現在`BindingMode`,枚舉還`OnTime`包括 僅在綁定上下文更改時而不是在源屬性更改時應用綁定。

在 MVVM 專案中可能成為資料連結的目標的屬性通常具有`DefaultBindingMode``TwoWay`的 。 它們是：

- `Slider`和 `Stepper` 的 `Value` 屬性
- `Switch` 的 `IsToggled` 屬性
- `Text`屬性`Entry``Editor`, 與`SearchBar`
- `DatePicker` 的 `Date` 屬性
- `TimePicker` 的 `Time` 屬性

[**綁定模式**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes)範`FontSize`例 演示了具有資料綁定的四種綁定模式,其中目標`Label`為 屬性`Value``Slider`,源是的屬性。 這允許每個`Slider`控制件相應的`Label`字型大小。 但是,`Slider`元素沒有初始化`DefaultBindingMode`,`FontSize`因為屬性`OneWay`的是 。

[**反向繫結**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding)定`Value`範`Slider`例設定`FontSize``Label`引用每個 屬性的屬性上的綁定。 這似乎是`Slider`向後的,但它在初始化元素時效果更好`Value`, 因為`Slider`的屬性`DefaultBindingMode``TwoWay`具有 。

[![反向繫結的三重螢幕截圖](images/ch16fg06-small.png "反向繫結")](images/ch16fg06-large.png#lightbox "反向繫結")

這類似於在 MVVM 中定義綁定的方式,您將經常使用這種類型的綁定。

## <a name="string-formatting"></a>字串格式

當目標屬性`string`為類型時,可以使用[`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat)`BindingBase`定義的 屬性將源`string`轉換為 。 將`StringFormat`屬性設定為 .NET 格式字串,用於[`String.Format`](xref:System.String.Format(System.String,System.Object))靜態 格式以顯示物件。 在標記擴展中使用此格式字串時,使用單個引弧括起來,這樣大括弧就不會被誤認為是嵌入的標記擴展。

[**"ShowViewValue"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues)範例展示`StringFormat`如何 在 XAML 中使用。

[**WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings)範例展示具有 綁`Width``Height`定的頁面大小`ContentPage`。

## <a name="why-is-it-called-path"></a>為什麼它被稱為「路徑」?

[`Path`](xref:Xamarin.Forms.Binding.Path)屬性`Binding`之所以如此調用,是因為它可以是一系列屬性和索引器,由句點分隔。 [**綁定路徑演示範例**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos)顯示了幾個範例。

## <a name="binding-value-converters"></a>繫結值轉換器

當綁定的源屬性和目標屬性不同時,可以使用綁定轉換器在類型之間進行轉換。 這是一個實現介面的[`IValueConverter`](xref:Xamarin.Forms.IValueConverter)類,包含兩種方法:[`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo))將源轉換為目標,[`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo))並將目標轉換為源。

[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫[`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs)中的 類是將`int``bool`轉換為 的範例。 它由[**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler)範例範例,此選項`Button`時只會啟用 如果至少一個字元已`Entry`鍵入到中 。

類[`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs)將轉換為`bool``string`a 並定義兩個屬性以指定應`false`為其返回的`true`文本和 值。
是[`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs)類似的。 [**SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText)範例展示使用這兩個轉換器根據`Switch`設定以不同顏色顯示不同文本。

泛型[`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs)可以`BoolToStringConverter`替換`BoolToColorConverter`和 ,並用作任何類型的`bool`廣義 到物件轉換器。

## <a name="bindings-and-custom-views"></a>繫結與自訂檢視

您可以使用資料綁定簡化自訂控制項。 代碼[`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs)`Text`檔案定義`TextColor``FontSize``FontAttributes`、、 和`IsChecked`屬性,但對於控制項的可視物件,沒有任何邏輯。
相反,[`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml)該檔包含控制式視覺物件的所有標記,透過基於代碼後面檔中定義的屬性的元素`Label`上 的數據綁定進行資料綁定。

[**NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo)範例展示`NewCheckBox`自訂控制項。

## <a name="related-links"></a>相關連結

- [第16章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [第16章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
