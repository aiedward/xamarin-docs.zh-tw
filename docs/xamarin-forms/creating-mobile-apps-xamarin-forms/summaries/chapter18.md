---
title: 第18章摘要。 MVVM
description: 建立 Mobile Apps 與 Xamarin.Forms ：第18章的摘要。 MVVM
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 15342144a080f3056c1aaa3441f6d1ec850f4dc2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374052"
---
# <a name="summary-of-chapter-18-mvvm"></a>第18章摘要。 MVVM

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

架構應用程式的其中一個最佳方式，就是將使用者介面與基礎程式碼分開，有時也稱為 *商務邏輯* 。 有幾種技術存在，但是針對以 XAML 為基礎的環境量身訂做的技巧，稱為模型 ViewModel 或 MVVM。

## <a name="mvvm-interrelationships"></a>MVVM 相互關係

MVVM 應用程式有三個層級：

- 此模型會提供基礎資料，有時會透過檔案或 web 存取
- 此視圖是使用者介面或展示層，通常是在 XAML 中執行
- ViewModel 連接模型和視圖

模型並不是 ViewModel，而且 ViewModel 不是視圖的未知。 這三層通常會使用下列機制彼此連接：

![View、ViewModel 和 View](images/ch18fg03.png "MVVM")

在許多較小的程式中 (甚至更大的程式) ，通常模型不存在或其功能已整合至 ViewModel。

## <a name="viewmodels-and-data-binding"></a>Viewmodel 和資料系結

若要參與資料系結，ViewModel 必須能夠在 ViewModel 的屬性變更時通知視圖。 ViewModel 藉由在 [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) 命名空間中執行介面來執行這項工作 `System.ComponentModel` 。 這是 .NET 的一部分，而不是 Xamarin.Forms 。  (通常 Viewmodel 嘗試維護平臺獨立性。 ) 

`INotifyPropertyChanged`介面會宣告名為的單一事件 [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) ，指出已變更的屬性。

### <a name="a-viewmodel-clock"></a>ViewModel 時鐘

[`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) [**Xamarin.Forms 書籍**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)庫中的會定義根據計時器而變更之類型的屬性 `DateTime` 。 此類別會在 `INotifyPropertyChanged` 屬性變更時，執行並引發 `PropertyChanged` 事件 `DateTime` 。

[**MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock)範例會將此 ViewModel 具現化，並使用 ViewModel 的資料系結來顯示更新的日期和時間資訊。

### <a name="interactive-properties-in-a-viewmodel"></a>ViewModel 中的互動式屬性

ViewModel 中的屬性可以更具互動方式，如類別所示 [`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) ，這是 [**SimpleMultiplier**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) 範例的一部分。 資料系結會提供兩個元素的被乘數和乘數值 `Slider` ，並使用來顯示產品 `Label` 。 不過，您可以在 XAML 中對這個使用者介面進行大量變更，而不會對 ViewModel 或程式碼後端檔案進行任何變更。

### <a name="a-color-viewmodel"></a>色彩 ViewModel

[`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) [**Xamarin.Forms 書籍**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)庫中的會整合 RGB 和 HSL 色彩模型。 它會在 [**HslSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) 範例中示範：

[![TK 的三重螢幕擷取畫面](images/ch18fg08-small.png "HSL 色彩模型")](images/ch18fg08-large.png#lightbox "HSL 色彩模型")

### <a name="streamlining-the-viewmodel"></a>簡化 ViewModel

您可以使用屬性定義方法來簡化 Viewmodel 中的程式碼 `OnPropertyChanged` [`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) ，以自動取得呼叫的屬性名稱。 [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) [**Xamarin.Forms 書籍**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)庫中的類別會執行這個工作，並提供 viewmodel 的基類。

## <a name="the-command-interface"></a>命令介面

MVVM 適用于資料系結，而資料系結會使用屬性，因此當您處理或的事件時，MVVM 似乎是或許也裨益的 `Clicked` `Button` `Tapped` `TapGestureRecognizer` 。 為了讓 Viewmodel 能夠處理這類事件， Xamarin.Forms 支援 *命令介面* 。

命令介面會在 `Button` 具有兩個公用屬性的中資訊清單本身：

- [`Command`](xref:Xamarin.Forms.Button.Command) 在 [`ICommand`](xref:System.Windows.Input.ICommand) 命名空間中定義 (類型 `System.Windows.Input`) 
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) 型別為 `Object`

為了支援命令介面，ViewModel 必須定義型別的屬性 `ICommand` ，然後將資料系結至的 `Command` 屬性 `Button` 。 介面會宣告 `ICommand` 兩種方法和一個事件：

- [`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object))具有類型之引數的方法`object`
- [`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object))具有傳回之型別引數 `object` 的方法`bool`
- [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged)事件

就內部而言，ViewModel 會將類型的每個屬性設定為實作為 `ICommand` 介面之類別的實例 `ICommand` 。 透過資料系結，一 `Button` 開始就會呼叫 `CanExecute` 方法，並在方法傳回時停用本身 `false` 。 它也會設定事件的處理常式 `CanExecuteChanged` ，並在 `CanExecute` 每次引發事件時呼叫。 如果已 `Button` 啟用，則會在 `Execute` 每次 `Button` 按一下時呼叫方法。

您可能會有一些早的 Viewmodel Xamarin.Forms ，而且這些可能已經支援命令介面。 針對僅供使用的新 Viewmodel，會 Xamarin.Forms Xamarin.Forms 提供 [`Command`](xref:Xamarin.Forms.Command) 類別和實介面的 [`Command<T>`](xref:Xamarin.Forms.Command`1) 類別 `ICommand` 。 泛型型別是和方法之引數的型 `Execute` 別 `CanExecute` 。

### <a name="simple-method-executions"></a>簡單的方法執行

[**PowersOfThree**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree)範例示範如何在 ViewModel 中使用命令介面。 [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs)類別會定義類型的兩個屬性 `ICommand` ，也會定義它傳遞給最簡單的函[ `Command` ](xref:Xamarin.Forms.Command.%23ctor(System.Action))式的兩個私用屬性。 此套裝程式含來自此 ViewModel 的資料系結至 `Command` 兩個專案的屬性 `Button` 。

您 `Button` 可以使用 XAML 中的物件輕鬆地取代元素 `TapGestureRecognizer` ，而不需要變更程式碼。

### <a name="a-calculator-almost"></a>計算機，幾乎

[**AddingMachine**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine)範例會使用的 `Execute` 和 `CanExecute` 方法 `ICommand` 。 它會使用 [`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) [**Xamarin.Forms 書本套件**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)庫中的類別。 ViewModel 包含六種類型的屬性 `ICommand` 。 這些是從的函[ `Command` 式和的函式，](xref:Xamarin.Forms.Command.%23ctor(System.Action))以及[ `Command` 的](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean}))的函式初始化 `Command` [ `Command<T>` ](/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) `Command<T>` 。 新增電腦的數位索引鍵全都系結至以初始化的屬性 `Command<T>` ，以及的 `string` 引數和 `Execute` `CanExecute` 識別特定的索引鍵。

## <a name="viewmodels-and-the-application-lifecycle"></a>Viewmodel 和應用程式生命週期

`AdderViewModel` **AddingMachine** 範例中使用的也會定義兩個名為和的方法 `SaveState` `RestoreState` 。 當應用程式進入睡眠狀態時，以及重新開機時，會從應用程式呼叫這些方法。

## <a name="related-links"></a>相關連結

- [第18章 (PDF 的完整文字) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [第18章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [使用電子書的企業應用程式模式 Xamarin.Forms](~/xamarin-forms/enterprise-application-patterns/index.md)
