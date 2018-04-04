---
title: 第 15 章的摘要。 互動的介面
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: c5b2bc00c4337969322193966f26ce0e151f426e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>第 15 章的摘要。 互動的介面

這一章探討八個`View`衍生項目可讓使用者互動。

## <a name="view-overview"></a>檢視概觀

Xamarin.Forms 包含 20 的可具現化類別衍生自`View`但不是`Layout`。 六種討論過在先前的章節：

- `Label`: [**第 2 章。應用程式的結構**](chapter02.md)
- `BoxView`: [**第 3 章。捲動堆疊**](chapter03.md)
- `Button`: [**第 6 章。按一下按鈕**](chapter06.md)
- `Image`: [**第 13 章。Bitmaps**](chapter13.md)
- `ActivityIndicator`: [**第 13 章。Bitmaps**](chapter13.md)
- `ProgressBar`: [**章 14。AbsoluteLayout**](chapter14.md)

在本章中的八個檢視有效地讓使用者互動基本的.NET 資料類型：

|資料類型|檢視|
|--- |--- |
|`Double`|[`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/), [`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/)|
|`Boolean`|[`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/)|
|`String`|[`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/), [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/), [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)|
|`DateTime`|[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/), [`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/)|

您可以將這些檢視視為視覺互動的基礎資料類型表示法。 這個概念會在下一章，探索更[**第 16 章。資料繫結**](chapter16.md)。

下列章節會涵蓋其餘的六種檢視方式：

- `WebView`: [**第 16 章。資料繫結**](chapter16.md)
- `Picker`: [**第 19 章。集合檢視**](chapter19.md)
- `ListView`: [**第 19 章。集合檢視**](chapter19.md)
- `TableView`: [**第 19 章。集合檢視**](chapter19.md)
- `Map`: [**章 28。位置和對應**](chapter28.md)
- `OpenGLView`： 未涵蓋在本手冊 （和 Windows 平台不支援）

## <a name="slider-and-stepper"></a>滑桿和 stepper

同時[ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)和[ `Stepper` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/)允許使用者選擇從範圍的數字的值。 `Slider`連續範圍內時`Stepper`包含離散值。

### <a name="slider-basics"></a>滑桿基本概念

[ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)是水平軸代表值的範圍從左邊的最小值右邊的最大值。 它會定義三個公用屬性：

- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Value/) 型別的`double`，預設值為 0
- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/) 型別的`double`，預設值為 0
- [`Maximum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/) 型別的`double`，預設值為 1

這些屬性可繫結屬性中，請確定它們是一致：

- 所有的三個屬性， [ `coerceValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+CoerceValueDelegate/)方法指定給可繫結屬性可確保`Value`之間`Minimum`和`Maximum`。
- [ `validateValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+ValidateValueDelegate/)方法`MinimumProperty`傳回`false`如果`Minimum`所設的值大於或等於`Maximum`，類似`MaximumProperty`。 傳回`false`從`validateValue`方法會使`ArgumentException`引發。

`Slider` 引發[ `ValueChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Slider.ValueChanged/)事件[ `ValueChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ValueChangedEventArgs/)引數時`Value`屬性變更，以程式設計方式或當使用者操控`Slider`。

[ **SliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo)範例將示範如何使用簡單`Slider`。

### <a name="common-pitfalls"></a>常見的問題

在程式碼中，在 XAML 中、`Minimum`和`Maximum`您指定的順序必須設定的屬性。 請務必初始化這些屬性，讓`Maximum`大於一律`Minimum`。 否則將會引發例外狀況。

初始化`Slider`屬性可能會導致`Value`要變更屬性和`ValueChanged`来引發的事件。 您應該確保`Slider`事件處理常式不會存取您尚未已在頁面初始化期間建立的檢視。

`ValueChanged`期間不會引發事件`Slider`初始化除非`Value`屬性變更。 您可以呼叫`ValueChanged`直接從程式碼的處理常式。

### <a name="slider-color-selection"></a>滑桿色彩選取範圍

[ **RgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders)程式包含三種`Slider`可讓您以互動方式指定其 RGB 值，藉以選取色彩的項目：

[![三個螢幕擷取畫面的 R G B 滑桿](images/ch15fg03-small.png "RGB 滑桿")](images/ch15fg03-large.png#lightbox "RGB 滑桿")

[ **TextFade** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade)範例會使用兩個`Slider`項目移動兩個`Label`項目跨越`AbsoluteLayout`和淡入另一個。

### <a name="the-stepper-difference"></a>Stepper 差異

[ `Stepper` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/)定義相同的屬性和事件中的當做`Slider`但`Maximum`屬性會初始化為 100 和`Stepper`定義的第四個屬性：

- [`Increment`](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Increment/) 型別的`double`、 初始化為 1

以視覺化的方式，`Stepper`包含標示為兩個按鈕**&ndash;**和**+**。 按下**&ndash;**減少`Value`由`Increment`的最小`Minimum`。 按下**+**增加`Value`由`Increment`最大`Maximum`。

這示範[ **StepperDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo)範例。

## <a name="switch-and-checkbox"></a>參數，並核取方塊

[ `Switch` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/)可讓使用者指定的布林值。

### <a name="switch-basics"></a>切換基本概念

以視覺化的方式，`Switch`可以關閉開啟的切換符號所組成。 類別會定義一個屬性：

- [`IsToggled`](https://developer.xamarin.com/api/property/Xamarin.Forms.Switch.IsToggled/) 型別 `bool`

`Switch` 定義一個事件：

- [`Toggled`](https://developer.xamarin.com/api/event/Xamarin.Forms.Switch.Toggled/) 伴隨著[ `ToggledEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToggledEventArgs/)物件，當引發`IsToggled`屬性變更。

[ **SwitchDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo)程式會示範`Switch`。

### <a name="a-traditional-checkbox"></a>傳統的核取方塊

有些開發人員可能會想要更傳統`CheckBox`至`Switch`。 [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)文件庫包含`CheckBox`類別衍生自`ContentView`。 `CheckBox` 由實作[CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml)和[CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs)檔案。 `CheckBox` 定義三個屬性 (`Text`， `FontSize`，和`IsChecked`) 和`CheckedChanged`事件。

[ **CheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo)範例會示範這`CheckBox`。

## <a name="typing-text"></a>輸入文字

Xamarin.Forms 會定義可讓使用者輸入和編輯文字的三種檢視：

- [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) 為單行文字
- [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) 針對多行文字
- [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) 為單行文字進行搜尋。

`Entry` 和`Editor`衍生自[ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/)，其衍生自`View`。 `SearchBar` 直接衍生自`View`。

### <a name="keyboard-and-focus"></a>鍵盤和焦點

在手機和平板電腦，而不實際鍵盤， `Entry`， `Editor`，和`SearchBar`所有項目會導致顯示虛擬鍵盤。 有關存在此螢幕上的鍵盤輸入焦點。 檢視必須同時擁有其[ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/)和[ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/)屬性設定為`true`取得輸入的焦點。

具有輸入焦點涉及兩個方法、 一個唯讀屬性，以及兩個事件。 這些所有定義的`VisualElement`:

- [ `Focus` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Focus()/)方法嘗試輸入的焦點設在項目，並傳回`true`如果成功
- [ `Unfocus` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Unfocus()/)方法會將輸入的焦點移除項目
- [ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/)唯讀屬性會指出是否項目具有輸入焦點
- [ `Focused` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.Focused/)事件表示當項目取得輸入的焦點
- [ `Unfocused` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.Unfocused/)事件表示當項目失去輸入的焦點

### <a name="choosing-the-keyboard"></a>選擇鍵盤

[ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/)從中類別`Entry`和`Editor`衍生定義只有一個屬性：

- [`Keyboard`](https://developer.xamarin.com/api/property/Xamarin.Forms.InputView.Keyboard/) 型別 [`Keyboard`](https://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/)

這表示的類型顯示鍵盤。 有些鍵盤會最佳化的 Uri 或數字。

`Keyboard`類別可定義具有靜態鍵盤[ `Keyboard.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Keyboard.Create/p/Xamarin.Forms.KeyboardFlags/)方法的引數的型別[ `KeyboardFlags` ](https://developer.xamarin.com/api/type/Xamarin.Forms.KeyboardFlags/)，具有下列的位元旗標的列舉：

- `None` 設定為 0
- [`CapitalizeSentence`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.CapitalizeSentence/) 設為 1
- [`Spellcheck`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.Spellcheck/) 設定為 2
- [`Suggestions`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.Suggestions/) 將設定為 4
- [`All`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.All/) 設定為 \xFFFFFFFF

當使用多行[ `Editor` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/)當段落或多個文字，請呼叫`Keyboard.Create`是選取鍵盤的好方法。 單一線條[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)，下列唯讀的靜態屬性`Keyboard`很有用：

- [`Default`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Default/)
- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Text/)
- [`Chat`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Chat/)
- [`Url`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Url/)
- [`Email`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Email/)
- [`Telephone`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Telephone/)
- [`Numeric`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Numeric/) 為正數或不含小數點。

[ `KeyboardTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.KeyboardTypeConverter/)所示，在 XAML 中指定這些屬性可讓[ **EntryKeyboards** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards)程式。

### <a name="entry-properties-and-events"></a>項目屬性和事件

單行[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)定義下列屬性：

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) 型別的`string`，在出現的文字 `Entry`
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/) 型別 `Color`
- [`FontFamily`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontFamily/) 型別 `string`
- [`FontSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontSize/) 型別 `double`
- [`FontAttributes`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontAttributes/) 型別 `FontAttributes`
- [`IsPassword`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.IsPassword/) 型別的`bool`，因而導致要遮罩的字元
- [`Placeholder`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Placeholder/) 型別的`string`，dimly 彩色文字會出現在`Entry`輸入的任何內容之前
- [`PlaceholderColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.PlaceholderColor/) 型別 `Color`

`Entry`也會定義兩個事件：

- [`TextChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) 與[ `TextChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextChangedEventArgs/)物件，每次引發`Text`屬性變更
- [`Completed`](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/)當使用者已完成並關閉鍵盤時引發。 使用者指示完成平台專屬的方式

[ **QuadraticEquations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations)範例會示範這兩個事件。

### <a name="the-editor-difference"></a>編輯器差異

多行[ `Editor` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/)定義相同`Text`和`Font`屬性視為`Entry`但不是包含其他屬性。 `Editor` 也會定義相同的兩個屬性視為`Entry`。

[**JustNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes)是自由格式的備忘稿製作程式，儲存並還原的內容`Editor`。

### <a name="the-searchbar"></a>SearchBar

[ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)不是衍生自`InputView`，因此它並沒有`Keyboard`屬性。 但是有所有`Text`， `Font`，和`Placeholder`屬性，`Entry`定義。 此外，`SearchBar`定義三個額外屬性：

- [`CancelButtonColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.CancelButtonColor/) 型別 `Color`
- [`SearchCommand`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommand/) 型別的[ `ICommand` ](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/)用於資料繫結與 MVVM
- [`SearchCommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommandParameter/) 型別的`Object`，搭配使用 `SearchCommand`

平台專屬取消按鈕清除文字。 `SearchBar`也具有 [平台特定的搜尋] 按鈕。 按下任一這些按鈕會引發兩個事件的其中一個，`SearchBar`定義：

- [`TextChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.TextChanged/) 伴隨著[ `TextChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextChangedEventArgs/)物件
- [`SearchButtonPressed`](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.SearchButtonPressed/)

[ **SearchBarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo)範例將示範如何`SearchBar`。

## <a name="date-and-time-selection"></a>日期和時間的選取範圍

[ `DatePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/)和[ `TimePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/)檢視實作平台特定控制項，可讓使用者指定的日期或時間。

### <a name="the-datepicker"></a>日期選擇器

[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) 定義四個屬性：

- [`MinimumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) 型別的`DateTime`、 初始化為 1900 年 1 月 1 日
- [`MaximumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) 型別的`DateTime`、 初始化為 2100 年 12 月 31，
- [`Date`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) 型別的`DateTime`、 初始化為 `DateTime.Today`
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Format/) 型別的`string`、.NET 格式字串初始化為"d"，簡短日期模式中，所產生的日期顯示類似 「 7/20/1969 」 在美國太平洋時區中。

您可以設定`DateTime`屬性在 XAML 中表示為屬性項目屬性，並使用文化特性而異簡短日期格式 ("7/20/1969")。   

[ **DaysBetweenDates** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates)範例計算的使用者所選取的兩個日期之間的天數。

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>Timeupdown （或者 TimeSpanPicker？）

[`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) 定義兩個屬性和任何事件：

- [`Time`](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Time/) 型別`TimeSpan`而不是`DateTime`，從午夜以來指出時間過
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Format/) 型別的`string`、.NET 格式字串初始化為"t"，簡短時間模式，產生如 「 1:45 PM"時間顯示在美國太平洋時區。

[ **SetTimer** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer)程式示範如何使用`TimePicker`計時器的指定時間。 如果您保留在前景，僅適用於程式。

**SetTimer**也示範如何使用[ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/)方法`Page`顯示警示的方塊。



## <a name="related-links"></a>相關連結

- [第 15 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [第 15 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Entry](~/xamarin-forms/user-interface/text/entry.md)
- [編輯器](~/xamarin-forms/user-interface/text/editor.md)
