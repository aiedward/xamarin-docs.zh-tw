---
title： "第18章的摘要。 MVVM 「描述：」使用建立 Mobile Apps Xamarin.Forms ：第18章的摘要。 MVVM "ms. 生產： xamarin ms. 技術： xamarin assetid： 6A774510-7709-4F60-8EF5-29D478176F8F author： davidbritch ms-chap： dabritch ms. date： 11/07/2017 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="summary-of-chapter-18-mvvm"></a>第18章的摘要。 MVVM

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

架構應用程式的其中一個最佳方式，就是將使用者介面與基礎程式碼分開，這有時稱為*商務邏輯*。 有數種技術存在，但針對以 XAML 為基礎的環境量身訂做的是所謂的模型視圖-ViewModel 或 MVVM。

## <a name="mvvm-interrelationships"></a>MVVM 相互關係

MVVM 應用程式有三個層級：

- 此模型會提供基礎資料，有時會透過檔案或 web 存取
- 此視圖是使用者介面或展示層，通常是在 XAML 中執行
- ViewModel 會連接模型和視圖

模型並不是 ViewModel，而且 ViewModel 並不是視圖的未知。 這三個層級通常會使用下列機制彼此連接：

![View、ViewModel 和 View](images/ch18fg03.png "MVVM")

在許多較小的程式（甚至更大）中，通常不存在模型，或其功能已整合到 ViewModel 中。

## <a name="viewmodels-and-data-binding"></a>Viewmodel 和資料系結

若要參與資料系結，ViewModel 必須能夠在 ViewModel 的屬性變更時通知視圖。 ViewModel 會藉由 [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) 在命名空間中執行介面來執行這項工作 `System.ComponentModel` 。 這是 .NET 的一部分，而不是 Xamarin.Forms 。 （通常 Viewmodel 嘗試維護平臺獨立性）。

`INotifyPropertyChanged`介面會宣告名為的單一事件 [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) ，以指出已變更的屬性。

### <a name="a-viewmodel-clock"></a>ViewModel 時鐘

[`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)庫中的 `DateTime` 會定義根據計時器變更之類型的屬性。 當屬性變更時，類別會執行 `INotifyPropertyChanged` 並引發 `PropertyChanged` 事件 `DateTime` 。

[**MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock)範例會具現化此 ViewModel，並使用 ViewModel 的資料系結來顯示更新的日期和時間資訊。

### <a name="interactive-properties-in-a-viewmodel"></a>ViewModel 中的互動式屬性

ViewModel 中的屬性可以更具互動，如類別所示 [`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) ，這是[**SimpleMultiplier**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier)範例的一部分。 資料系結會從兩個元素提供被乘數和乘數值 `Slider` ，並使用來顯示產品 `Label` 。 不過，您可以在 XAML 中對此使用者介面進行大量變更，而不會對 ViewModel 或程式碼後置檔案進行任何後續變更。

### <a name="a-color-viewmodel"></a>色彩 ViewModel

[`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)庫中的會整合 RGB 和 HSL 色彩模型。 它會在[**HslSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders)範例中示範：

[![TK 的三向螢幕擷取畫面](images/ch18fg08-small.png "HSL 色彩模型")](images/ch18fg08-large.png#lightbox "HSL 色彩模型")

### <a name="streamlining-the-viewmodel"></a>簡化 ViewModel

藉由使用屬性定義方法，即可簡化 Viewmodel 中的程式碼 `OnPropertyChanged` [`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) ，以自動取得呼叫屬性名稱。 [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) [**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)中的類別會執行這項工作，並為 viewmodel 提供基類。

## <a name="the-command-interface"></a>命令介面

MVVM 適用于資料系結，而資料系結則適用于屬性，因此在處理或事件的事件時，MVVM 似乎是或許也裨益的 `Clicked` `Button` `Tapped` `TapGestureRecognizer` 。 為了讓 Viewmodel 能夠處理這類事件， Xamarin.Forms 支援*命令介面*。

命令介面會在 `Button` 具有兩個公用屬性的中，以本身的方式進行資訊清單：

- [`Command`](xref:Xamarin.Forms.Button.Command)類型的 [`ICommand`](xref:System.Windows.Input.ICommand) （定義于 `System.Windows.Input` 命名空間）
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)類型為`Object`

若要支援命令介面，ViewModel 必須定義類型的屬性 `ICommand` ，然後將資料系結至的 `Command` 屬性 `Button` 。 介面會宣告 `ICommand` 兩種方法和一個事件：

- [`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object))具有類型之引數的方法`object`
- [`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object))具有型別之引數的方法 `object` 會傳回`bool`
- [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged)事件

就內部而言，ViewModel 會將類型的每個屬性設定 `ICommand` 為執行介面之類別的實例 `ICommand` 。 透過資料系結，一 `Button` 開始會呼叫 `CanExecute` 方法，如果方法傳回，則會停用本身 `false` 。 它也會設定事件的處理常式 `CanExecuteChanged` ，並在 `CanExecute` 每次引發事件時呼叫。 如果已 `Button` 啟用，則會在 `Execute` 每次 `Button` 按一下時呼叫方法。

您可能會有一些早的 Viewmodel Xamarin.Forms ，而且這些可能已經支援命令介面。 若要將新的 Viewmodel 用於 Xamarin.Forms ，請 Xamarin.Forms 提供 [`Command`](xref:Xamarin.Forms.Command) 類別和實介面的 [`Command<T>`](xref:Xamarin.Forms.Command`1) 類別 `ICommand` 。 泛型型別是和方法的引數型別 `Execute` `CanExecute` 。

### <a name="simple-method-executions"></a>簡單方法執行

[**PowersOfThree**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree)範例會示範如何在 ViewModel 中使用命令介面。 [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs)類別會定義類型的兩個屬性 `ICommand` ，也會定義兩個私用屬性，其[ `Command` ](xref:Xamarin.Forms.Command.%23ctor(System.Action))會傳遞至最簡單的函式。 此套裝程式含來自此 ViewModel 的資料系結到 `Command` 兩個 `Button` 元素的屬性。

`Button`元素可以輕鬆地以 `TapGestureRecognizer` XAML 中的物件取代，而不需要變更程式碼。

### <a name="a-calculator-almost"></a>計算機，幾乎

[**AddingMachine**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine)範例會使用的 `Execute` 和 `CanExecute` 方法 `ICommand` 。 它會使用 [`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)庫中的類別。 ViewModel 包含六個類型的屬性 `ICommand` 。 這些會從的函式和的函式[ `Command` ，以及的](xref:Xamarin.Forms.Command.%23ctor(System.Action))的函[ `Command` 式來初始化](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) `Command` [ `Command<T>` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) `Command<T>` 。 新增機器的數位索引鍵全都系結至以初始化的屬性 `Command<T>` ，以及與的 `string` 引數， `Execute` 並 `CanExecute` 識別特定的索引鍵。

## <a name="viewmodels-and-the-application-lifecycle"></a>Viewmodel 和應用程式生命週期

`AdderViewModel`在**AddingMachine**範例中使用的也會定義兩個名為和的方法 `SaveState` `RestoreState` 。 這些方法會在進入睡眠狀態時和重新開機時，從應用程式中呼叫。

## <a name="related-links"></a>相關連結

- [第18章的完整文字（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [第18章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [使用電子書的企業應用程式模式 Xamarin.Forms](~/xamarin-forms/enterprise-application-patterns/index.md)
