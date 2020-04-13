---
title: 第18章摘要。 MVVM
description: 使用 Xamarin.表單創建行動應用程式:第 18 章摘要。 MVVM
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 32c16409f30d6b6d502b7cc074eafb182898594a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771067"
---
# <a name="summary-of-chapter-18-mvvm"></a>第18章摘要。 MVVM

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

構建應用程式的最佳方式之一是將使用者介面與基礎代碼(有時稱為*業務邏輯*)分離。 存在幾種技術,但針對基於 XAML 的環境量身定製的技術稱為模型視圖-視圖模型或 MVVM。

## <a name="mvvm-interrelationships"></a>MVVM 相互關係

MVVM 應用程式有三個層:

- 模型提供基礎資料,有時透過檔案或 Web 存取
- 檢視是使用者介面或表示層,通常在 XAML 中實現
- 檢視模型連線模型和檢視

模型對視圖模型一無所知,視圖模型對視圖一無所知。 這三個層通常使用以下機制相互連線:

![檢視、檢視模型和檢視](images/ch18fg03.png "MVVM")

在許多較小的程式(甚至較大的程式)中,通常模型不存在,或者其功能集成到ViewModel 中。

## <a name="viewmodels-and-data-binding"></a>檢視模型和資料繫結

要參與數據綁定,視圖模型必須能夠在ViewModel的屬性發生更改時通知視圖。 ViewModel 通過`System.ComponentModel`在 命名空間[`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)中實現 介面來實現此項。 這是 .NET 的一部分,而不是 Xamarin.Forms。 (通常視圖模型嘗試維護平台獨立性。

介面`INotifyPropertyChanged`聲明名為[`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)的 單個事件,該事件指示已更改的屬性。

### <a name="a-viewmodel-clock"></a>檢視模型時鐘

[`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)庫中定義基於計時器更改的`DateTime`類型 屬性。 每當屬性發生`INotifyPropertyChanged`更改時,`PropertyChanged``DateTime`類都會實現並觸發事件。

[**MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock)範例實體化此檢視模型,並使用到 ViewModel 的數據綁定來顯示更新的日期和時間資訊。

### <a name="interactive-properties-in-a-viewmodel"></a>檢視模型中的互動式屬性

像類(簡單[`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs)[**乘法器**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier)示例的一部分)所示,ViewModel 中的屬性可以更具交互性。 資料繫結提供來自兩個`Slider`元素的多倍值和乘數值,並顯示產品`Label`與 。 但是,您可以在 XAML 中對此使用者介面進行大量更改,而對 ViewModel 或代碼背後的檔不會隨之進行更改。

### <a name="a-color-viewmodel"></a>色彩檢視模型

[`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)庫中集成了 RGB 和 HSL 顏色模型。 在[**HlSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders)範例中展示此:

[![TK 的三重螢幕擷圖](images/ch18fg08-small.png "HSL 顏色模型")](images/ch18fg08-large.png#lightbox "HSL 顏色模型")

### <a name="streamlining-the-viewmodel"></a>精簡檢視模型

通過使用`OnPropertyChanged`[`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute)屬性定義方法,可以簡化 ViewModel 中的代碼,該屬性會自動獲取調用屬性名稱。 [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)庫中的類可以這樣做,併為 ViewModel 提供了一個基本類。

## <a name="the-command-interface"></a>命令介面

MVVM`Clicked`使用數據綁定,並且數據綁定使用屬性,因此,在處理`Button``Tapped`或事件中的 MVVM`TapGestureRecognizer`似乎不足。 為了允許 ViewModel 處理此類事件,Xamarin.Forms 支援*命令介面*。

命令介面在`Button`具有兩個公共屬性的 中表現出來:

- [`Command`](xref:Xamarin.Forms.Button.Command)型[`ICommand`](xref:System.Windows.Input.ICommand)態 (在命名`System.Windows.Input`空間中定義)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)類型`Object`

為了支援命令介面,ViewModel 必須定義`ICommand`類型 屬性,然後將數據綁`Command`定到的`Button`屬性。 介面`ICommand`宣告兩個方法和一個事件:

- 具有[`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object))類型參數的方法`object`
- 具有[`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object))`object`類型 參數的方法,傳回`bool`
- 事件[`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged)

在內部,ViewModel 將每個類型`ICommand`的 屬性設置`ICommand`到實現 介面的類的實例。 通過數據綁定,最初`Button`調`CanExecute`用 方法,並在`false`方法返回 時禁用自身。 它還為`CanExecuteChanged`事件設置處理程式,並在觸發該`CanExecute`事件 時調用該事件。 如果啟用`Button`,則每當單擊`Execute``Button`時 都會調用 方法。

您可能有一些早於 Xamarin.Forms 的 ViewModel,這些模型可能已經支援命令介面。 對於僅用於 Xamarin.Forms 的新 ViewModel,Xamarin.Forms 提供了實現[`Command`](xref:Xamarin.Forms.Command)介面的[`Command<T>`](xref:Xamarin.Forms.Command`1)`ICommand`類和類。 泛型類型是和`Execute``CanExecute`方法的參數類型。

### <a name="simple-method-executions"></a>簡單的方法執行

[**Powersofof3**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree)示例演示如何在 ViewModel 中使用命令介面。 這個[`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs)類別定義兩個類型的`ICommand`屬性,並定義兩個私有屬性,它傳遞給最簡單的[`Command`建構函數](xref:Xamarin.Forms.Command.%23ctor(System.Action))。 該程式包含從此檢視模型到兩`Command``Button`個元素的屬性的數據綁定。

`Button`元素可以很容易地替換為`TapGestureRecognizer`XAML 中的物件,無需更改代碼。

### <a name="a-calculator-almost"></a>計算機,幾乎

[**新增電腦**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine)範例使用`Execute``CanExecute``ICommand`和方法。 它使用[`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)[**Xamarin.FormsBook.工具套件**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)庫中的類。 視圖模型包含類型`ICommand`中的六個屬性。 這些是從的[`Command`建構函數](xref:Xamarin.Forms.Command.%23ctor(System.Action))與[`Command`建構函式](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean}))[`Command<T>`](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__)`Command`, 以及 建構函`Command<T>`數 。 添加電腦的數位鍵都綁定到`Command<T>`使用 初始化的屬性,`string``Execute`以及`CanExecute`到和標識特定鍵的參數。

## <a name="viewmodels-and-the-application-lifecycle"></a>檢視模型與應用程式生命週期

`AdderViewModel`**在「添加機器」** 範例中使用的方法還定義了兩種`SaveState``RestoreState`名為和的方法。 這些方法在應用程式進入睡眠時和再次啟動時從應用程式調用。

## <a name="related-links"></a>相關連結

- [第18章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [第18章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [使用 Xamarin.表單電子書的企業應用程式模式](~/xamarin-forms/enterprise-application-patterns/index.md)
