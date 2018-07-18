---
title: 第 18 章的摘要。 MVVM
description: 使用 Xamarin.Forms 建立行動應用程式： 摘要的第 18 章。 MVVM
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: b512b15ea40d963cd35379cf3162856d132e38dc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995258"
---
# <a name="summary-of-chapter-18-mvvm"></a>第 18 章的摘要。 MVVM

一種架構應用程式的最佳方式是從基礎的程式碼，這有時稱為分隔的使用者介面*商務邏輯*。 有數種技術，但會以 XAML 為基礎的環境量身訂做的一個稱為 Model View ViewModel 或 MVVM。

## <a name="mvvm-interrelationships"></a>MVVM 的關係

MVVM 應用程式有三個層級：

- 模型提供基礎資料，有時檔案或 web 存取
- 檢視是使用者介面或展示層，通常在 XAML 中實作
- ViewModel 連接模型和檢視

模型是非持續性的 ViewModel，而非持續性檢視的 ViewModel。 這三層通常彼此連線使用下列方法：

![檢視、 ViewModel 及檢視](images/ch18fg03.png "MVVM")

在許多較小的程式 （和更大的項目），通常模型不存在，或其功能已整合到 ViewModel。

## <a name="viewmodels-and-data-binding"></a>Viewmodel 和資料繫結

若要參與資料繫結，ViewModel 必須能夠 ViewModel 的屬性已變更時通知檢視。 ViewModel 會藉由實作[ `INotifyPropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged)介面中`System.ComponentModel`命名空間。 這是.NET，而不是 Xamarin.Forms 的一部分。 (通常 Viewmodel 嘗試維護平台獨立性。)

`INotifyPropertyChanged`介面會宣告名為單一事件[ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) ，指出已變更的屬性。

### <a name="a-viewmodel-clock"></a>ViewModel 時鐘

[ `DateTimeViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)程式庫會定義型別的屬性`DateTime`變更根據計時器。 此類別會實作`INotifyPropertyChanged`以及引發`PropertyChanged`事件時`DateTime`屬性變更。

[ **MvvmClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock)範例會具現化此 ViewModel，並使用資料繫結至 ViewModel 顯示更新的日期和時間資訊。

### <a name="interactive-properties-in-a-viewmodel"></a>在 ViewModel 的互動式內容

在 ViewModel 的屬性可以是更具互動性，如所示[ `SimpleMultiplierViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs)類別，這是組件的[ **SimpleMultiplier** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier)範例。 資料繫結提供兩個被乘數和乘數值`Slider`項目，並顯示產品`Label`。 不過，您也可以在 XAML 中這個使用者介面與任何後續變更 ViewModel 或程式碼後置檔案進行廣泛的變更。

### <a name="a-color-viewmodel"></a>色彩 ViewModel

[ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)程式庫整合的 RGB 和 HSL 色彩模型。 中會示範[ **HslSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders)範例：

[![三重的螢幕擷取畫面的 TK](images/ch18fg08-small.png "HSL 色彩模型")](images/ch18fg08-large.png#lightbox "HSL 色彩模型")

### <a name="streamlining-the-viewmodel"></a>簡化 ViewModel

Viewmodel 中的程式碼可以透過定義可簡化`OnPropertyChanged`方法使用[ `CallerMemberName` ](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute)屬性，它會自動取得呼叫端的屬性名稱。 [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)採用程式庫，並提供 Viewmodel 的基底類別。

## <a name="the-command-interface"></a>命令介面

MVVM 搭配資料繫結和資料繫結處理屬性，因此 MVVM 似乎不足時處理`Clicked`事件的`Button`或是`Tapped`事件的`TapGestureRecognizer`。 若要允許來處理這類事件的 Viewmodel，支援 Xamarin.Forms*命令介面*。

命令介面資訊清單本身中`Button`與兩個公用屬性：

- [`Command`](xref:Xamarin.Forms.Button.Command) 型別的[ `ICommand` ](xref:System.Windows.Input.ICommand) (定義於`System.Windows.Input`命名空間)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) 型別 `Object`

若要支援的命令介面，ViewModel 必須定義類型的屬性`ICommand`也就是再資料繫結至`Command`屬性`Button`。 `ICommand`介面會宣告兩個方法和一個事件：

- [ `Execute` ](xref:System.Windows.Input.ICommand.Execute(System.Object))與類型的引數的方法 `object`
- A [ `CanExecute` ](xref:System.Windows.Input.ICommand.CanExecute(System.Object))方法的引數的型別`object`傳回 `bool`
- A [ `CanExecuteChanged` ](xref:System.Windows.Input.ICommand.CanExecuteChanged)事件

就內部而言，ViewModel 設定每個屬性的型別`ICommand`可實作類別的執行個體`ICommand`介面。 透過資料繫結`Button`一開始會呼叫`CanExecute`方法，並會自行停用，如果此方法會傳回`false`。 它也會設定的處理常式`CanExecuteChanged`事件並呼叫`CanExecute`每當引發該事件。 如果`Button`已啟用，它會呼叫`Execute`方法時`Button`按下。

您可能需要比 Xamarin.Forms，某些 Viewmodel，而且這些可能已經支援的命令介面。 Xamarin.Forms 會提供新的 Viewmodel 適用於只使用 Xamarin.Forms，針對[ `Command` ](xref:Xamarin.Forms.Command)類別和[ `Command<T>` ](xref:Xamarin.Forms.Command`1)類別實作`ICommand`介面。 泛型型別是引數的型別`Execute`和`CanExecute`方法。

### <a name="simple-method-executions"></a>簡單的方法執行

[ **PowersOfThree** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree)範例示範如何使用命令介面中的 ViewModel。 [ `PowersViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs)類別會定義兩個屬性的型別`ICommand`而且也會定義兩個私用屬性，就會傳遞至簡單[`Command`建構函式](xref:Xamarin.Forms.Command.%23ctor(System.Action))。 程式包含資料繫結，從以這個 ViewModel`Command`兩個屬性`Button`項目。

`Button`項目可以輕鬆地取代`TapGestureRecognizer`XAML 中不必變更程式碼的物件。

### <a name="a-calculator-almost"></a>計算機，幾乎

[ **AddingMachine** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine)範例會使用這兩`Execute`並`CanExecute`方法`ICommand`。 它會使用[ `AdderViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)程式庫。 ViewModel 包含六個的屬性型別的`ICommand`。 這些初始化自[`Command`建構函式](xref:Xamarin.Forms.Command.%23ctor(System.Action))並[`Command`建構函式](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean}))的`Command`而[`Command<T>`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command%3CT%3E.Command%3CT%3E/p/System.Action%7BT%7D/System.Func%7BT,System.Boolean%7D/)`Command<T>`。 數字鍵加入機器的所有繫結至使用初始化的屬性`Command<T>`，並`string`引數`Execute`和`CanExecute`識別特定的索引鍵。

## <a name="viewmodels-and-the-application-lifecycle"></a>Viewmodel 和應用程式生命週期

`AdderViewModel`用於**AddingMachine**範例也會定義兩個方法，名為`SaveState`和`RestoreState`。 當它進入睡眠狀態，並重新啟動時，會從應用程式呼叫這些方法。



## <a name="related-links"></a>相關連結

- [第 18 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [第 18 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
