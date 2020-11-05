---
title: 第15章的摘要。 互動式介面
description: 建立 Mobile Apps 與 Xamarin.Forms ：第15章的摘要。 互動式介面
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 08c5152c5618b7edcfe22bc288a1a0354e3e5fed
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374247"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>第15章的摘要。 互動式介面

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

本章將探討 `View` 允許與使用者互動的八個衍生。

## <a name="view-overview"></a>視圖總覽

Xamarin.Forms 包含可衍生自但未衍生的20個具現化類別 `View` `Layout` 。 前幾章已涵蓋其中六項：

- `Label`： [**第2章。應用程式的剖析**](chapter02.md)
- `BoxView`： [**第3章。滾動堆疊**](chapter03.md)
- `Button`： [**第6章。按一下按鈕**](chapter06.md)
- `Image`：第 [ **13 章。點陣圖**](chapter13.md)
- `ActivityIndicator`：第 [ **13 章。點陣圖**](chapter13.md)
- `ProgressBar`：第 [ **14 章。AbsoluteLayout**](chapter14.md)

本章中的八個觀點，可有效讓使用者與基本 .NET 資料類型互動：

|資料類型|檢視|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

您可以將這些 views 視為基礎資料類型的視覺互動式標記法。 下一章第16章會更進一步探討這個概念 [**。資料**](chapter16.md)系結。

其餘六個觀點將在下列章節中討論：

- `WebView`：第 [ **16 章。資料** 系結](chapter16.md)
- `Picker`：第 [ **19 章。集合視圖**](chapter19.md)
- `ListView`：第 [ **19 章。集合視圖**](chapter19.md)
- `TableView`：第 [ **19 章。集合視圖**](chapter19.md)
- `Map`：第 [ **28 章。位置和地圖**](chapter28.md)
- `OpenGLView`：本書籍未涵蓋 (且不支援 Windows 平臺) 

## <a name="slider-and-stepper"></a>滑杆和分檔器

[`Slider`](xref:Xamarin.Forms.Slider)和都 [`Stepper`](xref:Xamarin.Forms.Stepper) 可讓使用者選擇範圍內的數值。 `Slider`是連續的範圍，而則 `Stepper` 牽涉到離散值。

### <a name="slider-basics"></a>滑杆基本概念

[`Slider`](xref:Xamarin.Forms.Slider)是一條水準橫條，代表從左邊到右邊最大值的值範圍。 它會定義三個公用屬性：

- [`Value`](xref:Xamarin.Forms.Slider.Value) 類型 `double` ，預設值為0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) 類型 `double` ，預設值為0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) 類型 `double` ，預設值為1。

傳回這些屬性的可系結屬性可確保它們一致：

- 對於這三個屬性，為可系結 [`coerceValue`](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate) 屬性指定的方法可確保在 `Value` `Minimum` 和之間 `Maximum` 。
- [`validateValue`](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate) `MinimumProperty` `false` 如果將 `Minimum` 設定為大於或等於的值，則上的方法會傳回 `Maximum` ，而且類似于 `MaximumProperty` 。 `false`從方法傳回 `validateValue` 會導致 `ArgumentException` 引發。

`Slider`[`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) 當 `Value` 屬性變更時，以程式設計方式或使用者操作時，以引數引發事件 `Slider` 。

[**SliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo)範例示範的簡單用法 `Slider` 。

### <a name="common-pitfalls"></a>常見陷阱

在程式碼和 XAML 中， `Minimum` 和 `Maximum` 屬性都是以您指定的順序來設定。 請務必初始化這些屬性，使其 `Maximum` 一律大於 `Minimum` 。 否則將會引發例外狀況。

初始化 `Slider` 屬性可能會導致 `Value` 屬性變更，以及 `ValueChanged` 引發事件。 您應確定 `Slider` 事件處理常式不會存取頁面初始化期間尚未建立的視圖。

`ValueChanged` `Slider` 除非屬性變更，否則不會在初始化期間引發事件 `Value` 。 您可以 `ValueChanged` 直接從程式碼呼叫處理常式。

### <a name="slider-color-selection"></a>滑杆色彩選取範圍

[**RgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders)套裝程式含三個 `Slider` 元素，可讓您藉由指定 RGB 值以互動方式選取色彩：

[![R G B 滑杆的三重螢幕擷取畫面](images/ch15fg03-small.png "RGB 滑杆")](images/ch15fg03-large.png#lightbox "RGB 滑杆")

[**TextFade**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade)範例會使用兩個專案， `Slider` 在中移動兩個專案 `Label` `AbsoluteLayout` ，並將其中一個元素淡入另一個。

### <a name="the-stepper-difference"></a>分檔器差異

[`Stepper`](xref:Xamarin.Forms.Stepper)會定義與相同的屬性和事件， `Slider` 但 `Maximum` 屬性會初始化為100，並 `Stepper` 定義第四個屬性：

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) 的類型 `double` ，初始化為1

以視覺化方式， `Stepper` 包含兩個標示為和的按鈕 **&ndash;** **+** 。 按 **&ndash;** 下 `Value` `Increment` 最小值 `Minimum` 。 按 **+** `Value` `Increment` 最多可增加到最多 `Maximum` 。

[**StepperDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo)範例會示範這一點。

## <a name="switch-and-checkbox"></a>切換和核取方塊

[`Switch`](xref:Xamarin.Forms.Switch)可讓使用者指定布林值。

### <a name="switch-basics"></a>切換基本概念

以視覺化方式， `Switch` 包含可關閉和開啟的切換。 類別會定義一個屬性：

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 型別為 `bool`

`Switch` 定義一個事件：

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) 伴隨著在 [`ToggledEventArgs`](xref:Xamarin.Forms.ToggledEventArgs) 屬性變更時引發的物件 `IsToggled` 。

[**SwitchDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo)程式示範 `Switch` 。

### <a name="a-traditional-checkbox"></a>傳統的核取方塊

有些開發人員可能會偏好較傳統的 `CheckBox` `Switch` 。 [**Xamarin.Forms Book 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫包含 `CheckBox` 衍生自的類別 `ContentView` 。 `CheckBox` 是由 [CheckBox. xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) 和 [CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) 檔案所執行。 `CheckBox` 定義 (`Text` 、 `FontSize` 和 `IsChecked`) 和事件的三個屬性 `CheckedChanged` 。

[**CheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo)範例會示範這一點 `CheckBox` 。

## <a name="typing-text"></a>輸入文字

Xamarin.Forms 定義三個可讓使用者輸入和編輯文字的視圖：

- [`Entry`](xref:Xamarin.Forms.Entry) 單行文字
- [`Editor`](xref:Xamarin.Forms.Editor) 適用于多行文字
- [`SearchBar`](xref:Xamarin.Forms.SearchBar) 適用于搜尋用途的單一文字行。

`Entry` 衍生自的 `Editor` 衍生自 [`InputView`](xref:Xamarin.Forms.InputView) `View` 。 `SearchBar` 直接衍生自 `View` 。

### <a name="keyboard-and-focus"></a>鍵盤和焦點

在沒有實體鍵盤的手機和平板電腦上， `Entry` 、 `Editor` 和 `SearchBar` 元素都會導致虛擬鍵盤彈出。 畫面上的這個鍵盤是否存在與輸入焦點相關。 View 必須 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) 將其和 [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) 屬性設定為， `true` 才能取得輸入焦點。

有兩個方法（一個唯讀屬性）和兩個事件都牽涉到輸入焦點。 這些都是由下列各項所定義 `VisualElement` ：

- [`Focus`](xref:Xamarin.Forms.VisualElement.Focus)方法會嘗試將輸入焦點設定為元素，並 `true` 在成功時傳回
- [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus)方法會從元素中移除輸入焦點
- [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused)唯讀屬性，指出元素是否有輸入焦點
- 此 [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) 事件會指出元素取得輸入焦點的時間
- 此 [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) 事件會指出元素何時失去輸入焦點

### <a name="choosing-the-keyboard"></a>選擇鍵盤

從中 [`InputView`](xref:Xamarin.Forms.InputView) 衍生的類別 `Entry` `Editor` 只會定義一個屬性：

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) 型別為 [`Keyboard`](xref:Xamarin.Forms.Keyboard)

這表示顯示的鍵盤類型。 某些鍵盤最適合用於 Uri 或數位。

`Keyboard`類別可讓您使用靜態 [ `Keyboard.Create` ] (x：來定義鍵盤 Xamarin.Forms 。鍵盤. 建立 (Xamarin.Forms 。KeyboardFlags) # A3 方法，其具有類型的引數，也就 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 是具有下列位旗標的列舉：

- `None` 設定為0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) 設定為1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) 設定為2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) 設定為4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) 設定為 \xFFFFFFFF

在 [`Editor`](xref:Xamarin.Forms.Editor) 預期段落或更多文字時使用多行時，呼叫 `Keyboard.Create` 是選取鍵盤的絕佳方法。 針對單行 [`Entry`](xref:Xamarin.Forms.Entry) ，下列的靜態唯讀屬性很 `Keyboard` 有用：

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) 適用于具有或不含小數點的正數。

[`KeyboardTypeConverter`](xref:Xamarin.Forms.KeyboardTypeConverter)允許在 XAML 中指定這些屬性，如 [**EntryKeyboards**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards)程式所示。

### <a name="entry-properties-and-events"></a>專案屬性和事件

這一行會 [`Entry`](xref:Xamarin.Forms.Entry) 定義下列屬性：

- [`Text`](xref:Xamarin.Forms.InputView.Text) 型別為的 `string` 文字，出現在 `Entry`
- [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) 型別為 `Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily) 型別為 `string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize) 型別為 `double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes) 型別為 `FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) 類型的 `bool` ，會造成遮罩字元
- [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 型別的 `string` ，用於 dimly 出現在 `Entry` 輸入任何內容之前的彩色文字
- [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) 型別為 `Color`

`Entry`也會定義兩個事件：

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 使用 [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) 物件時，每當 `Text` 屬性變更時就會引發
- [`Completed`](xref:Xamarin.Forms.Entry.Completed)，當使用者完成且鍵盤關閉時引發。 使用者以平臺特定的方式指出完成

[**QuadraticEquations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations)範例會示範這兩個事件。

### <a name="the-editor-difference"></a>編輯器差異

多行 [`Editor`](xref:Xamarin.Forms.Editor) `Text` 會定義和屬性相同的和屬性， `Font` `Entry` 但不會定義其他屬性。 `Editor` 也會定義和相同的兩個屬性 `Entry` 。

[**JustNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) 是一種自由形式的記事程式，可儲存和還原的內容 `Editor` 。

### <a name="the-searchbar"></a>SearchBar

不是 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 衍生自 `InputView` ，因此沒有 `Keyboard` 屬性。 但是它的確有定義的 `Text` 、 `Font` 和 `Placeholder` 屬性 `Entry` 。 此外，還會 `SearchBar` 定義三個額外的屬性：

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) 型別為 `Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)[`ICommand`](xref:System.Windows.Input.ICommand)用於資料系結和 MVVM 的型別
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) 型別 `Object` 為的，用於 `SearchCommand`

平臺特定的 [取消] 按鈕會清除文字。 `SearchBar`也有平臺特定的搜尋按鈕。 按下其中一個按鈕會引發兩個定義的事件之一 `SearchBar` ：

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 伴隨 [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) 物件
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

[**SearchBarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo)範例會示範 `SearchBar` 。

## <a name="date-and-time-selection"></a>日期和時間選擇

[`DatePicker`](xref:Xamarin.Forms.DatePicker)和 [`TimePicker`](xref:Xamarin.Forms.TimePicker) views 會執行平臺特定的控制項，讓使用者指定日期或時間。

### <a name="the-datepicker"></a>DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker) 定義四個屬性：

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) 的類型 `DateTime` ，初始化為1900年1月1日
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) 的類型 `DateTime` ，初始化為2100年12月31日
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) 的型別 `DateTime` ，初始化為 `DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) 在類型 `string` 中，.net 格式化字串初始化為 "d" （簡短日期模式），而產生的日期顯示方式就像美國的 "7/20/1969"。

您可以 `DateTime` 在 XAML 中設定屬性，方法是將屬性工作表示為屬性元素，並使用不因文化特性而異的簡短日期格式 ( "7/20/1969" ) 。   

[**DaysBetweenDates**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates)範例會計算使用者所選取的兩個日期之間的天數。

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>TimePicker (或 TimeSpanPicker？ ) 

[`TimePicker`](xref:Xamarin.Forms.TimePicker) 定義兩個屬性和無事件：

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) 的類型為 `TimeSpan` ，而非 `DateTime` 表示從午夜起經過的時間
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) 在類型 `string` 中，初始化為 "t" 的 .net 格式化字串（短時間模式）會導致時間顯示，例如「美國 1:45 PM」。

[**SetTimer**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer)程式示範如何使用 `TimePicker` 指定計時器的時間。 只有當您將程式保留在前景時，此程式才能運作。

**SetTimer** 也會示範如何使用的 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) 方法 `Page` 來顯示警示方塊。

## <a name="related-links"></a>相關連結

- [第15章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [第15章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [滑桿](~/xamarin-forms/user-interface/slider.md)
- [進入](~/xamarin-forms/user-interface/text/entry.md)
- [編輯器](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
