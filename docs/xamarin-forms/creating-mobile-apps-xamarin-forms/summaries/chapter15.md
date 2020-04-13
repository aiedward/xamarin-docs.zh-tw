---
title: 第15章摘要。 互動式介面
description: 使用 Xamarin.表單創建行動應用程式:第 15 章摘要。 互動式介面
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 5f96d2f4b619bbb10bb58e9b1b5dc7007c1ce888
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292052"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>第15章摘要。 互動式介面

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

本章將探討允許與`View`使用者交互的八種衍生工具。

## <a name="view-overview"></a>檢視概述

Xamarin.forms 包含20個可實例化`View`類`Layout`,這些 類派生自 ,但不派生。 前幾章涵蓋了其中六章:

- `Label`[**第二章。套用的分析**](chapter02.md)
- `BoxView`[**第三章。捲動堆疊**](chapter03.md)
- `Button`[**第6章。按鍵點選次數**](chapter06.md)
- `Image`[**第13章。點陣圖**](chapter13.md)
- `ActivityIndicator`[**第13章。點陣圖**](chapter13.md)
- `ProgressBar`[ **:第14章。絕對佈局**](chapter14.md)

本章中的八個檢視有效地允許使用者與基本的 .NET 資料類型進行交互:

|資料類型|檢視|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

您可以將這些檢視視為基礎資料類型的可視互動式表示形式。 這一概念在下一章第16章中進行了更多的探討[**。資料繫結**](chapter16.md)。

其餘六個視圖由以下章節介紹:

- `WebView`[ **:第16章。資料繫結**](chapter16.md)
- `Picker`[**第19章。集合檢視**](chapter19.md)
- `ListView`[**第19章。集合檢視**](chapter19.md)
- `TableView`[**第19章。集合檢視**](chapter19.md)
- `Map`[ **:第28章。位置與地圖**](chapter28.md)
- `OpenGLView`:本書未涵蓋(不支援 Windows 平臺)

## <a name="slider-and-stepper"></a>滑塊和步進器

[`Slider`](xref:Xamarin.Forms.Slider)和[`Stepper`](xref:Xamarin.Forms.Stepper)都允許用戶從範圍中選擇數值。 是`Slider`連續範圍,`Stepper`而涉及離散值。

### <a name="slider-basics"></a>滑塊基礎知識

是[`Slider`](xref:Xamarin.Forms.Slider)表示從左側的最小值到右側最大值的值範圍的水準條。 它定義了三個公共屬性:

- [`Value`](xref:Xamarin.Forms.Slider.Value)型`double`態 ,預設值 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum)型`double`態 ,預設值 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum)型`double`態 ,預設值為 1

支援這些屬性的可綁定屬性可確保它們一致:

- 對於所有三個屬性,[`coerceValue`](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate)為可綁定屬性指定的方法可`Value`確保 介`Minimum``Maximum`於和 之間。
- 如果[`validateValue`](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate)`Minimum`設置為大於或 等`Maximum`於的值,則`MaximumProperty``MinimumProperty``false`返回上的方法與的值相同, 與的值類似。 從`false`方法`validateValue`傳回會`ArgumentException`引發 。

`Slider`當`Value`[`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged)屬性 以程式[`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs)設計方式 更改或當`Slider`使用者操作 時 ,使用參數觸發事件。

[**SliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo)範例展示了`Slider`簡單使用 。

### <a name="common-pitfalls"></a>常見陷阱

在代碼和 XAML`Minimum`中`Maximum`, 屬性都按您指定的順序設定。 請確定初始化這些屬性,`Maximum`讓您可以`Minimum`大於 。 否則將會引發例外狀況。

初始化`Slider`屬性可能會`Value`導致 屬性`ValueChanged`更改和觸發事件。 應確保`Slider`事件處理程式不訪問頁面初始化期間尚未創建的檢視。

除非`ValueChanged`屬性發生變更`Value`,`Slider`否則在初始化期間不會觸發事件。 可以直接從代碼調用`ValueChanged`處理程式。

### <a name="slider-color-selection"></a>滑動顏色選擇

[**RgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders)程式包含`Slider`三個元素,允許您透過指定其 RGB 值來互動選擇顏色:

[![R G B 滑動的三重螢幕擷取](images/ch15fg03-small.png "RGB 滑動")](images/ch15fg03-large.png#lightbox "RGB 滑動")

[**TextFade**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade)範例使用`Slider`兩個元素`Label`在中移動`AbsoluteLayout`兩個元素,並將其中一個淡入另一個元素。

### <a name="the-stepper-difference"></a>步進器差異

定義[`Stepper`](xref:Xamarin.Forms.Stepper)的屬性與事件與`Slider``Maximum`但 屬性初始化為 100`Stepper`並定義第四 個屬性的相同屬性與事件:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment)型`double`態 ,初始化為 1

在視覺上,`Stepper`由兩個標記為**&ndash;****+** 和的按鈕組成。 按**&ndash;** 一個`Value``Increment``Minimum`最小 。 按**+**`Value``Increment`一個最大`Maximum`值 。

[**StepperDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo)範例展示這一點。

## <a name="switch-and-checkbox"></a>切換與選取的盒

[`Switch`](xref:Xamarin.Forms.Switch)允許使用者指定布爾值。

### <a name="switch-basics"></a>切換基礎知識

在視覺上,`Switch`由可關閉和打開的切換組成。 類別定義屬性:

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)類型`bool`

`Switch`定義事件:

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled)伴隨物件[`ToggledEventArgs`](xref:Xamarin.Forms.ToggledEventArgs),`IsToggled`當 屬性更改時觸發。

[**SwitchDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo)程式`Switch`示範 了 。

### <a name="a-traditional-checkbox"></a>傳統選擇框

一些開發人員可能更喜歡更傳統的`CheckBox`比`Switch`. [**Xamarin.FormsBook.Toolkit 庫**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)包含派`CheckBox``ContentView`生自 的類。 `CheckBox`由[CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml)和[CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs)文件實現。 `CheckBox`定義三個屬性`Text`(`FontSize``IsChecked`和`CheckedChanged`) 和一個事件。

[**CheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo)範例範`CheckBox`例了 此 。

## <a name="typing-text"></a>輸入文字

Xamarin.Forms 定義了三個檢視,允許使用者輸入和編輯文本:

- [`Entry`](xref:Xamarin.Forms.Entry)對一行文字
- [`Editor`](xref:Xamarin.Forms.Editor)對多行文字
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)用於搜尋目的的一行文本。

`Entry`派生`Editor`[`InputView`](xref:Xamarin.Forms.InputView)自 派`View`生自 。 `SearchBar`直接派生自`View`。

### <a name="keyboard-and-focus"></a>鍵盤和焦點

在沒有物理鍵盤的手機和平板電腦上,`Entry``Editor``SearchBar`和 元素都會導致虛擬鍵盤彈出。 此鍵盤在螢幕上的存在與輸入對焦有關。 視圖必須同時設置其[`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)[`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)和`true`屬性以 獲取輸入焦點。

兩種方法,一個唯讀屬性和兩個事件涉及輸入焦點。 這些都由定義`VisualElement`:

- 該方法[`Focus`](xref:Xamarin.Forms.VisualElement.Focus)嘗試將輸入焦點設定為元素,如果成功,`true`則傳回
- 此方法[`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus)從元素中移除輸入焦點
- 唯[`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused)讀屬性指示元素是否具有輸入焦點
- 事件[`Focused`](xref:Xamarin.Forms.VisualElement.Focused)指示元素何時取得輸入焦點
- 事件[`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)指示元素何時失去輸入焦點

### <a name="choosing-the-keyboard"></a>選擇鍵盤

`Entry`[`InputView`](xref:Xamarin.Forms.InputView)衍生`Editor`的類別僅定義一個屬性:

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard)類型[`Keyboard`](xref:Xamarin.Forms.Keyboard)

這表示顯示的鍵盤類型。 某些鍵盤針對URI或數位進行了優化。

類別`Keyboard`允許使用具有類型的[`Keyboard.Create`](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags))[`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)參數的靜態方法定義鍵盤,以及具有以下位標誌的枚舉:

- `None`設定為 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence)設定為 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck)設定為 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions)設定為 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All)設定為 #xFFFFFFFFFFFF

當需要段落或更多文本[`Editor`](xref:Xamarin.Forms.Editor)時使用多行,調`Keyboard.Create`用 是選擇鍵盤的好方法。 對於單行[`Entry`](xref:Xamarin.Forms.Entry),`Keyboard`以下 靜態唯讀屬性很有用:

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric)對於帶或不帶小數點的正數。

允許在[`KeyboardTypeConverter`](xref:Xamarin.Forms.KeyboardTypeConverter)XAML 中指定這些屬性,如[**入門鍵盤**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards)程式所示。

### <a name="entry-properties-and-events"></a>項目屬性與事件

單行[`Entry`](xref:Xamarin.Forms.Entry)定義以下屬性:

- [`Text`](xref:Xamarin.Forms.InputView.Text)類型`string`,出現在`Entry`
- [`TextColor`](xref:Xamarin.Forms.InputView.TextColor)類型`Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily)類型`string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize)類型`double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes)類型`FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword)類型`bool`,導致字元被遮罩
- [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)類型`string`,對於在鍵入任何`Entry`內容 之前出現的淺色文本
- [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)類型`Color`

還`Entry`定義了兩個事件:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)物件,[`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs)每當`Text`屬性變更時觸發
- [`Completed`](xref:Xamarin.Forms.Entry.Completed),當使用者完成並且鍵盤被取消時觸發。 使用者以特定於平臺的方式指示完成

[**二次方程式**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations)示例演示了這兩個事件。

### <a name="the-editor-difference"></a>編輯器差異

多行[`Editor`](xref:Xamarin.Forms.Editor)定義與其他屬性相同`Text`的`Font``Entry`屬性, 而不是其他屬性。 `Editor`將兩個屬性定義為`Entry`。

[**JustNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes)是自由形式的筆記記錄程式,用於儲存和復原的內容`Editor`。

### <a name="the-searchbar"></a>搜尋列

[`SearchBar`](xref:Xamarin.Forms.SearchBar)不派生自`InputView`,因此它`Keyboard`沒有 屬性。 `Text`但它具有`Font``Entry`定義的所有和`Placeholder`屬性。 此外,`SearchBar`還定義了三個附加屬性:

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)類型`Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)[`ICommand`](xref:System.Windows.Input.ICommand)類型,用於資料繫結和 MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)型`Object`態 ,用於`SearchCommand`

特定於平臺的取消按鈕將擦除文本。 還`SearchBar`具有特定於平臺的搜索按鈕。 按下這些按鈕中的任何一個都會引發`SearchBar`定義以下兩個事件之一:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)隨附物體[`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs)
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

[**SearchBarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo)範例`SearchBar`展示 。

## <a name="date-and-time-selection"></a>日期與時間選擇

[`DatePicker`](xref:Xamarin.Forms.DatePicker)和[`TimePicker`](xref:Xamarin.Forms.TimePicker)檢視實現特定於平臺的控制項,允許使用者指定日期或時間。

### <a name="the-datepicker"></a>日期選擇器

[`DatePicker`](xref:Xamarin.Forms.DatePicker)定義四個屬性:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate)類型`DateTime`,初始化為 1900 年 1 月 1 日
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate)類型`DateTime`,初始化為 2100 年 12 月 31 日
- [`Date`](xref:Xamarin.Forms.DatePicker.Date)型`DateTime`態 ,初始化為`DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format)類型`string`, .NET 格式字串初始化為"d",短日期模式,導致日期顯示像"7/20/1969"在美國。

您可以通過將屬性`DateTime`表示 為屬性元素並使用區域性不變的短日期格式("7/20/1969")來設置 XAML 中的屬性。   

[**"天間日期"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates)範例計算使用者選擇的兩個日期之間的天數。

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>時間拾取器(還是時間跨度拾取器?

[`TimePicker`](xref:Xamarin.Forms.TimePicker)定義兩個屬性,沒有事件:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time)是類型`TimeSpan`,`DateTime`而不是 ,指示自午夜以來經過的時間
- [`Format`](xref:Xamarin.Forms.TimePicker.Format)類型`string`, .NET 格式字串初始化為"t",時間模式短,導致時間顯示像"1:45 PM"在美國。

[**SetTimer**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer)程式演示如何`TimePicker`使用 指定計時器的時間。 僅當將其保留在前臺時,程式才有效。

**SetTimer**還演示[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))了`Page`使用 顯示警報框的方法。

## <a name="related-links"></a>相關連結

- [第15章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [第15章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Slider](~/xamarin-forms/user-interface/slider.md)
- [Entry](~/xamarin-forms/user-interface/text/entry.md)
- [編輯器](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
