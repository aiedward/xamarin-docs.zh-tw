---
title: 第18章的摘要。 MVVM
description: 使用 Xamarin 建立 Mobile Apps：第18章的摘要。 MVVM
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 32c16409f30d6b6d502b7cc074eafb182898594a
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771067"
---
# <a name="summary-of-chapter-18-mvvm"></a>第18章的摘要。 MVVM

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

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

若要參與資料系結，ViewModel 必須能夠在 ViewModel 的屬性變更時通知視圖。 ViewModel 會藉由在 `System.ComponentModel` 命名空間中執行[`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)介面來完成這項工作。 這是 .NET 的一部分，而不是 Xamarin. Forms。 （通常 Viewmodel 嘗試維護平臺獨立性）。

`INotifyPropertyChanged` 介面會宣告名為[`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)的單一事件，指出已變更的屬性。

### <a name="a-viewmodel-clock"></a>ViewModel 時鐘

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)庫中的[`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs)會定義根據計時器變更 `DateTime` 類型的屬性。 此類別會執行 `INotifyPropertyChanged`，而且每當 `DateTime` 屬性變更時，就會引發 `PropertyChanged` 事件。

[**MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock)範例會具現化此 ViewModel，並使用 ViewModel 的資料系結來顯示更新的日期和時間資訊。

### <a name="interactive-properties-in-a-viewmodel"></a>ViewModel 中的互動式屬性

ViewModel 中的屬性可以更具互動方式，如[`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs)類別所示，這是[**SimpleMultiplier**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier)範例的一部分。 資料系結會從兩個 `Slider` 元素提供被乘數和乘數值，並以 `Label`顯示產品。 不過，您可以在 XAML 中對此使用者介面進行大量變更，而不會對 ViewModel 或程式碼後置檔案進行任何後續變更。

### <a name="a-color-viewmodel"></a>色彩 ViewModel

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)庫中的[`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs)會整合 RGB 和 HSL 色彩模型。 它會在[**HslSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders)範例中示範：

[![TK 的三向螢幕擷取畫面](images/ch18fg08-small.png "HSL 色彩模型")](images/ch18fg08-large.png#lightbox "HSL 色彩模型")

### <a name="streamlining-the-viewmodel"></a>簡化 ViewModel

藉由使用[`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute)屬性定義 `OnPropertyChanged` 方法，即可簡化 viewmodel 中的程式碼，以自動取得呼叫屬性名稱。 [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)庫中的[`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs)類別會執行此工作，並提供 viewmodel 的基類。

## <a name="the-command-interface"></a>命令介面

MVVM 適用于資料系結，而資料系結則適用于屬性，因此在處理 `Button` 的 `Clicked` 事件或 `TapGestureRecognizer`的 `Tapped` 事件時，MVVM 似乎是或許也裨益的。 為了讓 Viewmodel 能夠處理這類事件，Xamarin. Forms 支援*命令介面*。

命令介面會在具有兩個公用屬性的 `Button` 中，以資訊清單本身：

- [`ICommand`](xref:System.Windows.Input.ICommand)類型的[`Command`](xref:Xamarin.Forms.Button.Command) （定義于 `System.Windows.Input` 命名空間）
- 類型為 [ 的 `CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)`Object`

若要支援命令介面，ViewModel 必須定義 `ICommand` 類型的屬性，然後將資料系結至 `Button`的 `Command` 屬性。 `ICommand` 介面會宣告兩種方法和一個事件：

- 具有類型之引數的[`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object))方法 `object`
- 具有傳回之類型 `object` 引數的[`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object))方法 `bool`
- [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged)事件

就內部而言，ViewModel 會將類型的每個屬性 `ICommand` 設定為執行 `ICommand` 介面之類別的實例。 透過資料系結，`Button` 一開始會呼叫 `CanExecute` 方法，並在方法傳回 `false`時停用本身。 它也會設定 `CanExecuteChanged` 事件的處理常式，並在每次引發事件時呼叫 `CanExecute`。 如果已啟用 `Button`，每當按一下 `Button` 時，就會呼叫 `Execute` 方法。

您可能有一些 Viewmodel 會早 Xamarin，而且這些可能已經支援命令介面。 若要讓新的 Viewmodel 只能搭配 Xamarin 使用，則 Xamarin 會提供一個[`Command`](xref:Xamarin.Forms.Command)類別，以及一個[`Command<T>`](xref:Xamarin.Forms.Command`1)類別來執行 `ICommand` 介面。 泛型型別是 `Execute` 和 `CanExecute` 方法的引數類型。

### <a name="simple-method-executions"></a>簡單方法執行

[**PowersOfThree**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree)範例會示範如何在 ViewModel 中使用命令介面。 [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs)類別會定義 `ICommand` 類型的兩個屬性，也會定義兩個私用屬性，其會傳遞至最簡單的[`Command`](xref:Xamarin.Forms.Command.%23ctor(System.Action))的函式。 套裝程式含來自此 ViewModel 的資料系結到兩個 `Button` 元素的 `Command` 屬性。

不需要變更程式碼，就可以輕鬆地將 `Button` 元素取代為 XAML 中的 `TapGestureRecognizer` 物件。

### <a name="a-calculator-almost"></a>計算機，幾乎

[**AddingMachine**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine)範例會使用 `ICommand`的 `Execute` 和 `CanExecute` 方法。 它會使用[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)庫中的[`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)類別。 ViewModel 包含六個 `ICommand`類型的屬性。 這些會從[`Command`](xref:Xamarin.Forms.Command.%23ctor(System.Action))的函式，以及 `Command` 的[`Command`](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean}))和 `Command<T>`的[`Command<T>`](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__)函式，初始化。 新增機器的數位索引鍵全都系結至以 `Command<T>`初始化的屬性，以及 `Execute` 的 `string` 引數，並 `CanExecute` 識別特定的索引鍵。

## <a name="viewmodels-and-the-application-lifecycle"></a>Viewmodel 和應用程式生命週期

**AddingMachine**範例中使用的 `AdderViewModel` 也會定義兩個名為 `SaveState` 和 `RestoreState`的方法。 這些方法會在進入睡眠狀態時和重新開機時，從應用程式中呼叫。

## <a name="related-links"></a>相關連結

- [第18章的完整文字（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [第18章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [使用 Xamarin 的企業應用程式模式電子書](~/xamarin-forms/enterprise-application-patterns/index.md)
