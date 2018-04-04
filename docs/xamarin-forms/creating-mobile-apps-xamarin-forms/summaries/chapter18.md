---
title: 第 18 章摘要。 MVVM
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 392e683601726ed1219fd43786a9b50d2c14c9b5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-18-mvvm"></a>第 18 章摘要。 MVVM

其中一個應用程式架構設計人員的最佳方式是藉由分隔的使用者介面的基礎的程式碼，有時也稱為*商務邏輯*。 數個技術的存在，但對於以 XAML 為基礎的環境適合的一個稱為模型-檢視-ViewModel 或 MVVM。

## <a name="mvvm-interrelationships"></a>MVVM 相互關係

MVVM 應用程式具有三個層級：

- 模型會提供基礎資料，有時透過檔案或 web 存取
- 檢視是使用者介面或展示層，通常在 XAML 中實作
- ViewModel 連接模型和檢視

模型並不知道 ViewModel 和 ViewModel 並不知道的檢視。 這三層通常彼此連線使用下列機制：

![檢視、 ViewModel 及檢視](images/ch18fg03.png "MVVM")

在許多較小的程式 （和更大），通常模型不存在或其功能已整合到 ViewModel。

## <a name="viewmodels-and-data-binding"></a>ViewModels 和資料繫結

若要參與資料繫結，ViewModel 必須能夠 ViewModel 屬性變更時通知檢視。 ViewModel 會藉由實作[ `INotifyPropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/)介面中`System.ComponentModel`命名空間。 這是.NET，而不是 Xamarin.Forms 部分。 (通常 ViewModels 嘗試維護平台獨立性。)

`INotifyPropertyChanged`介面會宣告名為單一事件[ `PropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) ，指出已變更的屬性。

### <a name="a-viewmodel-clock"></a>ViewModel 時鐘

[ `DateTimeViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)程式庫定義型別的屬性`DateTime`變更基礎的計時器。 此類別會實作`INotifyPropertyChanged`引發`PropertyChanged`事件每當`DateTime`屬性變更。

[ **MvvmClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock)範例會具現化這個 ViewModel，並使用資料繫結到 ViewModel 顯示更新的日期和時間資訊。

### <a name="interactive-properties-in-a-viewmodel"></a>互動式 ViewModel 屬性

所示，ViewModel 中的內容可以是更具互動性[ `SimpleMultiplierViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs)類別，這是組件的[ **SimpleMultiplier** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier)範例。 資料繫結提供兩個被乘數和乘數值`Slider`項目，並顯示產品`Label`。 不過，您可以在 XAML 中此使用者介面與任何後續變更 ViewModel 或程式碼後置檔案進行廣泛的變更。

### <a name="a-color-viewmodel"></a>色彩 ViewModel

[ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)程式庫整合 RGB 和 HSL 色彩模型。 它會示範[ **HslSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders)範例：

[![三個螢幕擷取畫面的 TK](images/ch18fg08-small.png "HSL 色彩模型")](images/ch18fg08-large.png#lightbox "HSL 色彩模型")

### <a name="streamlining-the-viewmodel"></a>簡化 ViewModel

藉由定義可簡化 ViewModels 中的程式碼`OnPropertyChanged`方法使用[ `CallerMemberName` ](https://developer.xamarin.com/api/type/System.Runtime.CompilerServices.CallerMemberNameAttribute/)屬性，會自動取得呼叫端的屬性名稱。 [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)程式庫的做法，並提供 ViewModels 基底類別。

## <a name="the-command-interface"></a>命令介面

MVVM 搭配資料繫結和資料繫結處理屬性，因此 MVVM 似乎缺點時處理`Clicked`事件`Button`或`Tapped`事件`TapGestureRecognizer`。 若要允許 ViewModels 來處理這類事件，支援 Xamarin.Forms*命令介面*。

命令介面中會出現`Button`與兩個公用屬性：

- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Command/) 型別的[ `ICommand` ](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/) (定義於`System.Windows.Input`命名空間)
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.CommandParameter/) 型別 `Object`

若要支援的命令介面，ViewModel 必須定義型別的屬性`ICommand`也就是然後資料繫結到`Command`屬性`Button`。 `ICommand`介面會宣告兩個方法和一個事件：

- [ `Execute` ](https://developer.xamarin.com/api/member/System.Windows.Input.ICommand.Execute/p/System.Object/)具有類型的引數的方法 `object`
- A [ `CanExecute` ](https://developer.xamarin.com/api/member/System.Windows.Input.ICommand.CanExecute/p/System.Object/)方法的引數的型別`object`傳回 `bool`
- A [ `CanExecuteChanged` ](https://developer.xamarin.com/api/event/System.Windows.Input.ICommand.CanExecuteChanged/)事件

就內部而言，ViewModel 設定每一個屬性的型別`ICommand`實作的類別執行個體`ICommand`介面。 透過資料繫結`Button`一開始呼叫`CanExecute`方法，並會自行停用，如果該方法會傳回`false`。 它也會設定的處理常式`CanExecuteChanged`事件並呼叫`CanExecute`每當引發該事件。 如果`Button`已啟用，它會呼叫`Execute`方法每當`Button`按下。

您可能會有自封透過 Xamarin.Forms，某些 ViewModels，而這些可能已支援命令介面。 Xamarin.Forms 新 ViewModels 適用於只使用 Xamarin.Forms，針對提供[ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)類別和[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command%3CT%3E/)類別可實作`ICommand`介面。 泛型型別是將引數的型別`Execute`和`CanExecute`方法。

### <a name="simple-method-executions"></a>執行簡單的方法

[ **PowersOfThree** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree)範例示範如何使用命令介面中 ViewModel。 [ `PowersViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs)類別會定義兩個屬性的型別`ICommand`而且也會定義兩個私用屬性，就會傳遞至最簡單[`Command`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/)。 程式會包含資料繫結至這個 ViewModel 從`Command`兩個屬性`Button`項目。

`Button`項目可輕鬆地取代`TapGestureRecognizer`XAML 中沒有變更程式碼的物件。

### <a name="a-calculator-almost"></a>計算機，幾乎

[ **AddingMachine** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine)範例會使用這兩者的`Execute`和`CanExecute`方法`ICommand`。 它會使用[ `AdderViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)程式庫。 ViewModel 包含六個型別的屬性`ICommand`。 這些初始化自[`Command`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/)和[`Command`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/System.Func%7BSystem.Boolean%7D/)的`Command`和[`Command<T>`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command%3CT%3E.Command%3CT%3E/p/System.Action%7BT%7D/System.Func%7BT,System.Boolean%7D/)`Command<T>`。 數字鍵加入機器的所有繫結至屬性，以初始化`Command<T>`，和`string`引數`Execute`和`CanExecute`識別特定的金鑰。

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModels 和應用程式生命週期

`AdderViewModel`用於**AddingMachine**範例也會定義兩個方法，名為`SaveState`和`RestoreState`。 當它進入睡眠，而且它重新啟動時，會從應用程式呼叫這些方法。



## <a name="related-links"></a>相關連結

- [第 18 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [第 18 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
