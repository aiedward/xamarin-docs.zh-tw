---
title: 第 15 章的摘要。 互動式介面
description: 使用 Xamarin.Forms 建立行動應用程式：第 15 章的摘要。 互動式介面
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 974ef2a141ec7175c2306d4af63f534a5c77ecb2
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870114"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>第 15 章的摘要。 互動式介面

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

這一章探討八`View`允許使用者互動的衍生項目。

## <a name="view-overview"></a>檢視概觀

Xamarin.Forms 包含 20 的可具現化類別，衍生自`View`而非`Layout`。 六個這些討論過在先前的章節：

- `Label`：[**第 2 章。應用程式剖析**](chapter02.md)
- `BoxView`：[**第 3 章。捲動堆疊**](chapter03.md)
- `Button`：[**第 6 章。按下按鈕**](chapter06.md)
- `Image`：[**第 13 章。點陣圖**](chapter13.md)
- `ActivityIndicator`：[**第 13 章。點陣圖**](chapter13.md)
- `ProgressBar`：[**第 14 章。AbsoluteLayout**](chapter14.md)

在這一章中的八個檢視有效地允許使用者互動具有基本的.NET 資料類型：

|資料類型|檢視|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

您可以將這些檢視為基礎的資料類型的視覺互動的表示法。 這個概念會在下一章中，探索更[**第 16 章。資料繫結**](chapter16.md)。

在以下章節涵蓋剩餘的六種檢視方式：

- `WebView`：[**第 16 章。資料繫結**](chapter16.md)
- `Picker`：[**第 19 章。集合檢視**](chapter19.md)
- `ListView`：[**第 19 章。集合檢視**](chapter19.md)
- `TableView`：[**第 19 章。集合檢視**](chapter19.md)
- `Map`：[**第 28 章。位置與地圖**](chapter28.md)
- `OpenGLView`：未涵蓋在此活頁簿 （和 Windows 平台不支援）

## <a name="slider-and-stepper"></a>滑桿和步進

兩者[ `Slider` ](xref:Xamarin.Forms.Slider)並[ `Stepper` ](xref:Xamarin.Forms.Stepper)允許使用者選擇各式各樣的數字的值。 `Slider`是連續的範圍時`Stepper`包含離散值。

### <a name="slider-basics"></a>滑桿的基本概念

[ `Slider` ](xref:Xamarin.Forms.Slider)是水平軸從左邊的最小值表示某個範圍的值，右邊為最大值。 它會定義三個公用屬性：

- [`Value`](xref:Xamarin.Forms.Slider.Value) 型別的`double`，預設值為 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) 型別的`double`，預設值為 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) 型別的`double`，預設值為 1

支援這些屬性可繫結屬性中，請確定一致：

- 所有的三個屬性，如[ `coerceValue` ](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate)指定為可繫結的屬性可確保方法`Value`之間`Minimum`和`Maximum`。
- [ `validateValue` ](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate)方法`MinimumProperty`會傳回`false`如果`Minimum`所設值大於或等於`Maximum`，類似`MaximumProperty`。 傳回`false`從`validateValue`方法會使`ArgumentException`引發。

`Slider` 會引發[ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged)事件[ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs)引數時`Value`屬性變更，以程式設計方式或當使用者操控`Slider`。

[ **SliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo)範例會示範簡單的使用`Slider`。

### <a name="common-pitfalls"></a>常見的陷阱

程式碼和 XAML，同時`Minimum`和`Maximum`屬性會設定您指定的順序。 請務必將這些屬性讓`Maximum`一律是大於`Minimum`。 否則將會引發例外狀況。

初始化`Slider`屬性可能會導致`Value`若要變更的屬性和`ValueChanged`引發的事件。 您應該確保`Slider`事件處理常式不會存取您尚未在頁面初始化時已建立的檢視。

`ValueChanged`期間引發事件，不會`Slider`初始化除非`Value`屬性變更。 您可以呼叫`ValueChanged`直接從程式碼的處理常式。

### <a name="slider-color-selection"></a>滑桿中色彩選取範圍

[ **RgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders)計畫包含三種`Slider`可讓您以互動方式選取色彩，藉由指定其 RGB 值的項目：

[![三重的螢幕擷取畫面的 R G B 滑桿](images/ch15fg03-small.png "RGB 滑桿")](images/ch15fg03-large.png#lightbox "RGB 滑桿")

[ **TextFade** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade)範例會使用兩個`Slider`項目移動兩個`Label`之間的項目`AbsoluteLayout`並淡化到另一個。

### <a name="the-stepper-difference"></a>步進差異

[ `Stepper` ](xref:Xamarin.Forms.Stepper)定義的相同屬性和事件中的當做`Slider`但`Maximum`屬性會初始化為 100 和`Stepper`定義第四個屬性：

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) 型別的`double`，初始化為 1

在視覺上，`Stepper`包含兩個按鈕，分別為 **&ndash;** 並 **+**。 按下 **&ndash;** 減少`Value`由`Increment`最低`Minimum`。 按下 **+** 增加`Value`由`Increment`最大值`Maximum`。

最好的證明[ **StepperDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo)範例。

## <a name="switch-and-checkbox"></a>參數，並核取方塊

[ `Switch` ](xref:Xamarin.Forms.Switch)可讓使用者指定的布林值。

### <a name="switch-basics"></a>切換 基本概念

以視覺化的方式，`Switch`組成可以關閉或開啟的切換。 此類別會定義一個屬性：

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 型別 `bool`

`Switch` 定義一個事件：

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) 伴隨[ `ToggledEventArgs` ](xref:Xamarin.Forms.ToggledEventArgs)物件，當引發`IsToggled`屬性變更。

[ **SwitchDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo)程式示範`Switch`。

### <a name="a-traditional-checkbox"></a>傳統的核取方塊

有些開發人員可能會偏好較傳統`CheckBox`至`Switch`。 [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫包含`CheckBox`衍生自類別`ContentView`。 `CheckBox` 由實作[CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml)並[CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs)檔案。 `CheckBox` 定義三個屬性 (`Text`， `FontSize`，並`IsChecked`) 和`CheckedChanged`事件。

[ **CheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo)範例會示範這`CheckBox`。

## <a name="typing-text"></a>輸入文字

Xamarin.Forms 可定義三個檢視，可讓使用者輸入，並編輯文字：

- [`Entry`](xref:Xamarin.Forms.Entry) 對於某一行的文字
- [`Editor`](xref:Xamarin.Forms.Editor) 為多行文字
- [`SearchBar`](xref:Xamarin.Forms.SearchBar) 對於某一行的文字進行搜尋。

`Entry` 並`Editor`衍生自[ `InputView` ](xref:Xamarin.Forms.InputView)，其衍生自`View`。 `SearchBar` 直接衍生自`View`。

### <a name="keyboard-and-focus"></a>鍵盤和焦點

在手機和平板電腦，而不需要實體鍵盤`Entry`， `Editor`，和`SearchBar`所有的項目會導致顯示虛擬鍵盤。 這個鍵盤在螢幕上的目前狀態與輸入焦點。 檢視必須同時擁有其[ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible)並[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)屬性設為`true`取得輸入的焦點。

具有輸入焦點涉及兩個方法、 一個唯讀屬性，以及兩個事件。 這些全都定義由`VisualElement`:

- [ `Focus` ](xref:Xamarin.Forms.VisualElement.Focus)方法，嘗試設定輸入的焦點的項目並傳回`true`如果成功
- [ `Unfocus` ](xref:Xamarin.Forms.VisualElement.Unfocus)方法會將輸入的焦點移除項目
- [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused)唯讀屬性會指出是否項目具有輸入焦點
- [ `Focused` ](xref:Xamarin.Forms.VisualElement.Focused)事件表示當項目取得輸入的焦點
- [ `Unfocused` ](xref:Xamarin.Forms.VisualElement.Unfocused)事件表示當項目失去輸入的焦點

### <a name="choosing-the-keyboard"></a>選擇 鍵盤

[ `InputView` ](xref:Xamarin.Forms.InputView)從中類別`Entry`和`Editor`衍生定義只有一個屬性：

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) 型別 [`Keyboard`](xref:Xamarin.Forms.Keyboard)

這表示鍵盤顯示的類型。 有些鍵盤最適合的 Uri 或數字。

`Keyboard`類別可讓您定義使用靜態鍵盤[ `Keyboard.Create` ](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags))方法的型別引數[ `KeyboardFlags` ](xref:Xamarin.Forms.KeyboardFlags)，含有下列的位元旗標的列舉類型：

- `None` 設定為 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) 設為 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) 設定為 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) 設為 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) 設定為 \xFFFFFFFF

使用多行時[ `Editor` ](xref:Xamarin.Forms.Editor)段落或多個文字預期等候時間時，呼叫`Keyboard.Create`是不錯的方法，來選取鍵盤。 單一線條[ `Entry` ](xref:Xamarin.Forms.Entry)，下列靜態唯讀屬性的`Keyboard`很有用：

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) 為正數或不含小數點。

[ `KeyboardTypeConverter` ](xref:Xamarin.Forms.KeyboardTypeConverter)可讓您在 XAML 中指定這些屬性，如所示[ **EntryKeyboards** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards)程式。

### <a name="entry-properties-and-events"></a>項目屬性和事件

單行[ `Entry` ](xref:Xamarin.Forms.Entry)定義下列屬性：

- [`Text`](xref:Xamarin.Forms.Entry.Text) 型別的`string`，在出現的文字 `Entry`
- [`TextColor`](xref:Xamarin.Forms.Entry.TextColor) 型別 `Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily) 型別 `string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize) 型別 `double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes) 型別 `FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) 型別的`bool`，因而導致會遮罩處理的字元
- [`Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) 型別的`string`，如中所顯示的 dimly 彩色文字`Entry`輸入的任何內容之前
- [`PlaceholderColor`](xref:Xamarin.Forms.Entry.PlaceholderColor) 型別 `Color`

`Entry`也會定義兩個事件：

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) 具有[ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs)物件，每次引發`Text`屬性變更
- [`Completed`](xref:Xamarin.Forms.Entry.Completed)當使用者已完成和鍵盤關閉時引發。 使用者會指出完成平台專屬的方式

[ **QuadraticEquations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations)範例會示範這兩個事件。

### <a name="the-editor-difference"></a>編輯器的差異

Multiline [ `Editor` ](xref:Xamarin.Forms.Editor)定義相同`Text`並`Font`屬性為`Entry`但不是包含其他屬性。 `Editor` 也會定義相同的兩個屬性`Entry`。

[**JustNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes)是自由格式的資訊採取程式儲存並還原的內容`Editor`。

### <a name="the-searchbar"></a>SearchBar

[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)不是衍生自`InputView`，因此它並沒有`Keyboard`屬性。 但它的確有全部`Text`， `Font`，並`Placeholder`屬性，`Entry`定義。 颾魤 ㄛ`SearchBar`定義三個額外屬性：

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) 型別 `Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) 型別的[ `ICommand` ](xref:System.Windows.Input.ICommand)用於資料繫結與 MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) 型別的`Object`，搭配 `SearchCommand`

特定平台會取消按鈕清除文字。 `SearchBar`也有一個平台特有的搜尋 按鈕。 按下其中一個這些按鈕引發的兩個事件的其中一個，`SearchBar`定義：

- [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged) 伴隨[ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs)物件
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

[ **SearchBarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo)範例會示範`SearchBar`。

## <a name="date-and-time-selection"></a>日期和時間的選取項目

[ `DatePicker` ](xref:Xamarin.Forms.DatePicker)並[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)檢視實作平台特定的控制，讓使用者指定的日期或時間。

### <a name="the-datepicker"></a>DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker) 定義四個屬性：

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) 型別的`DateTime`，初始化為 1900 年 1 月 1 日
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) 型別的`DateTime`，初始化為 2100 年 12 月 31，
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) 型別的`DateTime`，初始化為 `DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) 型別的`string`、.NET 格式字串初始化為"d"，簡短日期模式中，導致日期顯示像是 「 7/20/1969 」 在美國。

您可以設定`DateTime`屬性中 XAML 表示的屬性做為屬性項目，並使用文化特性而異簡短日期格式 (「 7/20/1969 」)。   

[ **DaysBetweenDates** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates)範例計算的使用者所選取的兩個日期之間的天數。

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>TimePicker （或它是否為 TimeSpanPicker？）

[`TimePicker`](xref:Xamarin.Forms.TimePicker) 會定義兩個屬性和任何事件：

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) 屬於類型`TimeSpan`而非`DateTime`，指出時間從午夜開始經過
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) 型別的`string`、.NET 格式字串初始化為"t"、 簡短時間模式，導致 「 下午 1:45 」 等的時間顯示在美國。

[ **SetTimer** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer)程式示範如何使用`TimePicker`計時器的指定時間。 如果您將它保留在前景，僅適用於該程式。

**SetTimer**也會示範使用[ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))方法`Page`来顯示的警示方塊。



## <a name="related-links"></a>相關連結

- [第 15 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [第 15 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Slider](~/xamarin-forms/user-interface/slider.md)
- [Entry](~/xamarin-forms/user-interface/text/entry.md)
- [編輯器](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
