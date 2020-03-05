---
title: 第15章的摘要。 互動式介面
description: 使用 Xamarin 建立 Mobile Apps：第15章的摘要。 互動式介面
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 5f96d2f4b619bbb10bb58e9b1b5dc7007c1ce888
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "78292052"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>第15章的摘要。 互動式介面

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

本章探討允許與使用者互動的八個 `View` 衍生。

## <a name="view-overview"></a>View 總覽

Xamarin 包含20個衍生自 `View` 但不 `Layout`的可具現化類別。 先前章節已涵蓋其中六項：

- `Label`： [**第2章。應用程式的剖析**](chapter02.md)
- `BoxView`： [**第3章。滾動堆疊**](chapter03.md)
- `Button`： [**第6章。按一下按鈕**](chapter06.md)
- `Image`：第[ **13 章。點陣圖**](chapter13.md)
- `ActivityIndicator`：第[ **13 章。點陣圖**](chapter13.md)
- `ProgressBar`：第[ **14 章。AbsoluteLayout**](chapter14.md)

這一章的八個觀點可有效地讓使用者與基本 .NET 資料類型互動：

|資料類型|檢視|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry)、 [`Editor`](xref:Xamarin.Forms.Editor)、 [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

您可以將這些 views 視為基礎資料類型的視覺化互動式標記法。 本概念會在下一章第16章中進一步探討[ **。資料**](chapter16.md)系結。

剩餘的六個觀點會在下列章節中討論：

- `WebView`：第[ **16 章。資料**系結](chapter16.md)
- `Picker`：第[ **19 章。集合視圖**](chapter19.md)
- `ListView`：第[ **19 章。集合視圖**](chapter19.md)
- `TableView`：第[ **19 章。集合視圖**](chapter19.md)
- `Map`：第[ **28 章。位置和地圖**](chapter28.md)
- `OpenGLView`：本書並未涵蓋（且不支援 Windows 平臺）

## <a name="slider-and-stepper"></a>滑杆和分檔器

[`Slider`](xref:Xamarin.Forms.Slider)和[`Stepper`](xref:Xamarin.Forms.Stepper)都可讓使用者選擇範圍中的數值。 `Slider` 是連續的範圍，而 `Stepper` 則牽涉到離散值。

### <a name="slider-basics"></a>滑杆基本概念

[`Slider`](xref:Xamarin.Forms.Slider)是水準橫條，代表從最小值到右邊最大值的範圍。 它會定義三個公用屬性：

- `double`類型的[`Value`](xref:Xamarin.Forms.Slider.Value) ，預設值為0
- `double`類型的[`Minimum`](xref:Xamarin.Forms.Slider.Minimum) ，預設值為0
- `double`類型的[`Maximum`](xref:Xamarin.Forms.Slider.Maximum) ，預設值為1

傳回這些屬性的可系結屬性可確保它們是一致的：

- 對於這三個屬性而言，針對可系結屬性指定的[`coerceValue`](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate)方法會確保 `Value` 是在 `Minimum` 與 `Maximum`之間。
- 如果 `Minimum` 設定為大於或等於 `Maximum`的值，則 `MinimumProperty` 上的[`validateValue`](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate)方法會傳回 `false`，類似于 `MaximumProperty`。 從 `validateValue` 方法傳回 `false` 會導致 `ArgumentException` 引發。

當 `Value` 屬性變更時，`Slider` 以程式設計方式或使用者操作 `Slider`時，引發具有[`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs)引數的[`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged)事件。

[**SliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo)範例會示範 `Slider`的簡單用法。

### <a name="common-pitfalls"></a>常見陷阱

在程式碼和 XAML 中，`Minimum` 和 `Maximum` 屬性都會依照您指定的順序來設定。 請務必將這些屬性初始化，讓 `Maximum` 一律大於 `Minimum`。 否則將會引發例外狀況。

初始化 `Slider` 屬性可能會導致 `Value` 屬性變更，並引發 `ValueChanged` 事件。 您應該確定 `Slider` 事件處理常式不會存取頁面初始化期間尚未建立的視圖。

除非 `Value` 屬性變更，否則 `ValueChanged` 事件不會在 `Slider` 初始化期間引發。 您可以直接從程式碼呼叫 `ValueChanged` 處理常式。

### <a name="slider-color-selection"></a>滑杆色彩選取

[**RgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders)套裝程式含三個 `Slider` 元素，可讓您藉由指定其 RGB 值，以互動方式選取色彩：

[![R G B 滑杆的三向螢幕擷取畫面](images/ch15fg03-small.png "RGB 滑杆")](images/ch15fg03-large.png#lightbox "RGB 滑杆")

[**TextFade**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade)範例會使用兩個 `Slider` 專案，跨 `AbsoluteLayout` 移動兩個 `Label` 專案，並將其淡入另一個元素。

### <a name="the-stepper-difference"></a>分檔器差異

[`Stepper`](xref:Xamarin.Forms.Stepper)會定義與 `Slider` 相同的屬性和事件，但 `Maximum` 屬性會初始化為100，而 `Stepper` 會定義第四個屬性：

- `double`類型的[`Increment`](xref:Xamarin.Forms.Stepper.Increment) ，已初始化為1

`Stepper` 以視覺化的方式包含兩個標示為 [ **&ndash;** ] 和 [ **+** ] 的按鈕。 按 **&ndash;** 會 `Increment` 最低 `Minimum`來減少 `Value`。 按 **+** 會 `Increment` 最多 `Maximum`來增加 `Value`。

這是由[**StepperDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo)範例所示範。

## <a name="switch-and-checkbox"></a>切換和核取方塊

[`Switch`](xref:Xamarin.Forms.Switch)可讓使用者指定布林值。

### <a name="switch-basics"></a>切換基本概念

就視覺化而言，`Switch` 包含可以關閉和開啟的切換。 類別會定義一個屬性：

- 類型為 [ 的 `IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)`bool`

`Switch` 定義一個事件：

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled)伴隨[`ToggledEventArgs`](xref:Xamarin.Forms.ToggledEventArgs)物件，`IsToggled` 屬性變更時引發。

[**SwitchDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo)程式會示範 `Switch`。

### <a name="a-traditional-checkbox"></a>傳統核取方塊

有些開發人員可能會偏好較傳統的 `Switch``CheckBox`。 [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫包含衍生自 `ContentView`的 `CheckBox` 類別。 `CheckBox` 是由[CheckBox. xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml)和[CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs)檔案所執行。 `CheckBox` 定義三個屬性（`Text`、`FontSize`和 `IsChecked`）和一個 `CheckedChanged` 事件。

[**CheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo)範例會示範此 `CheckBox`。

## <a name="typing-text"></a>輸入文字

Xamarin 會定義三個可讓使用者輸入和編輯文字的視圖：

- 一行文字的[`Entry`](xref:Xamarin.Forms.Entry)
- 多行文字的[`Editor`](xref:Xamarin.Forms.Editor)
- 針對搜尋目的， [`SearchBar`](xref:Xamarin.Forms.SearchBar)單行文字。

`Entry` 和 `Editor` 衍生自從 `View`衍生的[`InputView`](xref:Xamarin.Forms.InputView)。 `SearchBar` 直接衍生自 `View`。

### <a name="keyboard-and-focus"></a>鍵盤和焦點

在沒有實體鍵盤的手機和平板電腦上，`Entry`、`Editor`和 `SearchBar` 元素都會導致虛擬鍵盤彈出。 此鍵盤在畫面上的存在與輸入焦點有關。 視圖必須將其[`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)和[`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)屬性設定為 `true`，才能取得輸入焦點。

有兩種方法，其中一個唯讀屬性和兩個事件牽涉到輸入焦點。 這些全都由 `VisualElement`定義：

- [`Focus`](xref:Xamarin.Forms.VisualElement.Focus)方法會嘗試將輸入焦點設定為元素，並在成功時傳回 `true`
- [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus)方法會移除元素的輸入焦點
- [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused)唯讀屬性會指出元素是否具有輸入焦點
- [`Focused`](xref:Xamarin.Forms.VisualElement.Focused)事件會指出元素取得輸入焦點的時間
- [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)事件會指出元素何時失去輸入焦點

### <a name="choosing-the-keyboard"></a>選擇鍵盤

`Entry` 和 `Editor` 衍生的[`InputView`](xref:Xamarin.Forms.InputView)類別只會定義一個屬性：

- 類型為 [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) 的 [`Keyboard`](xref:Xamarin.Forms.Keyboard)

這會指出所顯示的鍵盤類型。 某些鍵盤已針對 Uri 或數位進行優化。

`Keyboard` 類別允許以具有類型[`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)之引數的靜態[`Keyboard.Create`](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags))方法來定義鍵盤，這是具有下列位旗標的列舉：

- `None` 設為0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence)設為1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck)設定為2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions)設定為4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All)設定為 \xFFFFFFFF

當您在預期段落或多個文字時使用多行[`Editor`](xref:Xamarin.Forms.Editor)時，呼叫 `Keyboard.Create` 是選擇鍵盤的好方法。 針對單行[`Entry`](xref:Xamarin.Forms.Entry)，`Keyboard` 的下列靜態唯讀屬性很有用：

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- 包含或不含小數點的正數[`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) 。

[`KeyboardTypeConverter`](xref:Xamarin.Forms.KeyboardTypeConverter)允許在 XAML 中指定這些屬性，如[**EntryKeyboards**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards)程式所示。

### <a name="entry-properties-and-events"></a>專案屬性和事件

單行[`Entry`](xref:Xamarin.Forms.Entry)會定義下列屬性：

- [`Text`](xref:Xamarin.Forms.InputView.Text)類型 `string`，出現在 `Entry` 中的文字
- 類型為 [ 的 `TextColor`](xref:Xamarin.Forms.InputView.TextColor)`Color`
- 類型為 [ 的 `FontFamily`](xref:Xamarin.Forms.Entry.FontFamily)`string`
- 類型為 [ 的 `FontSize`](xref:Xamarin.Forms.Entry.FontSize)`double`
- 類型為 [ 的 `FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes)`FontAttributes`
- `bool`類型的[`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) ，這會造成字元被遮罩
- 類型 `string`的[`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) ，適用于在輸入任何內容之前出現在 `Entry` 中的 dimly 彩色文字
- 類型為 [ 的 `PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)`Color`

`Entry` 也會定義兩個事件：

- 使用[`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs)物件[`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) ，每當 `Text` 屬性變更時就會引發
- [`Completed`](xref:Xamarin.Forms.Entry.Completed)，當使用者完成且鍵盤已關閉時引發。 使用者以平臺特定的方式指出完成

[**QuadraticEquations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations)範例會示範這兩個事件。

### <a name="the-editor-difference"></a>編輯器差異

多行[`Editor`](xref:Xamarin.Forms.Editor)會定義與 `Entry` 相同的 `Text` 和 `Font` 屬性，而不是其他屬性。 `Editor` 也會定義與 `Entry`相同的兩個屬性。

[**JustNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes)是一個自由形式的記事程式，可儲存和還原 `Editor`的內容。

### <a name="the-searchbar"></a>搜尋列

[`SearchBar`](xref:Xamarin.Forms.SearchBar)不是衍生自 `InputView`，因此它沒有 `Keyboard` 屬性。 但它確實具有 `Entry` 定義的所有 `Text`、`Font`和 `Placeholder` 屬性。 此外，`SearchBar` 會定義三個額外的屬性：

- 類型為 [ 的 `CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)`Color`
- 與資料系結和 MVVM 搭配使用[`ICommand`](xref:System.Windows.Input.ICommand)類型的[`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)
- `Object`類型的[`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) ，以搭配使用 `SearchCommand`

平臺特定的 [取消] 按鈕會清除文字。 `SearchBar` 也具有平臺特定的 [搜尋] 按鈕。 按下其中一個按鈕，會引發 `SearchBar` 定義的兩個事件之一：

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)伴隨[`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs)物件
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

[**SearchBarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo)範例會示範 `SearchBar`。

## <a name="date-and-time-selection"></a>選擇日期和時間

[ [`DatePicker`](xref:Xamarin.Forms.DatePicker) ] 和 [ [`TimePicker`](xref:Xamarin.Forms.TimePicker) ] 視圖會執行平臺特定控制項，讓使用者指定日期或時間。

### <a name="the-datepicker"></a>DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker)定義四個屬性：

- `DateTime`類型的[`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) ，已初始化為1900年1月1日
- `DateTime`類型的[`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) ，已初始化為2100年12月31日
- `DateTime`類型的[`Date`](xref:Xamarin.Forms.DatePicker.Date) ，已初始化為 `DateTime.Today`
- 類型 `string`的[`Format`](xref:Xamarin.Forms.DatePicker.Format) ，初始化為 "d" （簡短日期模式）的 .net 格式字串，產生的日期顯示類似美國的 "7/20/1969"。

您可以在 XAML 中設定 `DateTime` 屬性，方法是將屬性工作表示為屬性專案，並使用文化特性不變的簡短日期格式（"7/20/1969"）。   

[**DaysBetweenDates**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates)範例會計算使用者選取的兩個日期之間的天數。

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>TimePicker （或它是 TimeSpanPicker？）

[`TimePicker`](xref:Xamarin.Forms.TimePicker)定義兩個屬性，而且沒有任何事件：

- [`Time`](xref:Xamarin.Forms.TimePicker.Time)的類型 `TimeSpan`，而不是 `DateTime`，表示從午夜起經過的時間
- 類型 `string`的[`Format`](xref:Xamarin.Forms.TimePicker.Format) ，初始化為 "t" 的 .net 格式字串，短時間模式，會導致在美國顯示如 "1:45 PM" 的時間。

[**SetTimer**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer)程式會示範如何使用 `TimePicker` 來指定計時器的時間。 此程式僅適用于您將它保留在前景的情況。

**SetTimer**也會示範如何使用 `Page` 的[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))方法來顯示警示方塊。

## <a name="related-links"></a>相關連結

- [第15章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [第15章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Slider](~/xamarin-forms/user-interface/slider.md)
- [Entry](~/xamarin-forms/user-interface/text/entry.md)
- [編輯器](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
