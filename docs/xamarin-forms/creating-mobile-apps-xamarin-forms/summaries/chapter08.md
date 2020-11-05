---
title: 第8章的摘要。 程式碼和 XAML 搭配
description: 建立 Mobile Apps Xamarin.Forms ：第8章的摘要。 程式碼和 XAML 搭配
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a88f0ef4e04bf6884f1b49811663f611d3e54a0a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366213"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>第8章的摘要。 程式碼和 XAML 搭配

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

這一章更深入探索 XAML，尤其是程式碼和 XAML 的互動方式。

## <a name="passing-arguments"></a>傳遞引數

在一般情況下，在 XAML 中具現化的類別必須具有公用無參數的函式;結果物件會透過屬性設定初始化。 不過，有兩種方式可以具現化和初始化物件。

雖然這些是一般用途的技術，但它們大多用來與 MVVM View 模型連接。

### <a name="constructors-with-arguments"></a>具有引數的函式

[**ParameteredConstructorDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo)範例會示範如何使用 `x:Arguments` 標記來指定函式引數。 這些引數必須以表示引數類型的元素標記來分隔。 針對基本的 .NET 資料類型，可以使用下列標記：

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

### <a name="can-i-call-methods-from-xaml"></a>我可以從 XAML 呼叫方法嗎？

[**FactoryMethodDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo)範例會示範如何使用專案 `x:FactoryMethod` 來指定用來建立物件的 factory 方法。 這種 factory 方法必須是公用和靜態，且必須建立其定義所在之類型的物件。  (例如， [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) 方法會有資格，因為它是 public 和 static，並且會傳回類型的值 `Color` 。 ) Factory 方法的引數是在標記內指定 `x:Arguments` 。

## <a name="the-xname-attribute"></a>X：Name 屬性

屬性可讓您為 `x:Name` XAML 中具現化的物件提供名稱。 這些名稱的規則與 c # 變數名稱的規則相同。 在函式的呼叫傳回之後 `InitializeComponent` ，程式碼後端檔案可以參考這些名稱來存取對應的 XAML 專案。 XAML 剖析器實際上會將名稱轉換成所產生部分類別中的私用欄位。

[**XamlClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock)範例會示範 `x:Name` 如何使用，以允許程式碼後端檔案將 XAML 中定義的兩個元素保留為 `Label` 目前的日期和時間進行更新。

相同的名稱不能用於同一個頁面上的多個元素。 如果您使用 `OnPlatform` 來為每個平臺建立平行命名物件，這是一個特別的問題。 [**PlatformSpecificLabele**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels)範例會示範更好的方法來進行這類作業。

## <a name="custom-xaml-based-views"></a>自訂以 XAML 為基礎的視圖

有幾種方式可避免在 XAML 中重複標記。 其中一個常見的方法是建立一個衍生自的新 XAML 型類別 [`ContentView`](xref:Xamarin.Forms.ContentView) 。 這項技術會在 [**ColorViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) 範例中示範。 `ColorView`類別衍生自 `ContentView` 以顯示特定的色彩和其名稱，而 `ColorViewListPage` 類別衍生自平常的， `ContentPage` 並且明確地建立17個實例 `ColorView` 。

`ColorView`以 XAML 存取類別需要另一個 XML 命名空間宣告，通常會 `local` 針對相同元件中的類別命名。

## <a name="events-and-handlers"></a>事件和處理常式

事件可以在 XAML 中指派給事件處理常式，但事件處理常式本身必須在程式碼後端檔案中執行。 [**XamlKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad)示範如何在 XAML 中建立鍵盤使用者介面，以及如何 `Clicked` 在程式碼後端檔案中執行處理常式。

## <a name="tap-gestures"></a>分流手勢

任何 `View` 物件都可以取得觸控輸入，並從該輸入產生事件。 `View`類別 [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) 會定義集合屬性，此屬性可包含一或多個衍生自之類別的實例 [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) 。

會 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 產生 [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) 事件。 [**MonkeyTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap)程式示範如何將物件附加 `TapGestureRecognizer` 至四個 `BoxView` 元素，以建立仿造遊戲：

[![猴子點路的三重螢幕擷取畫面](images/ch08fg07-small.png "仿造遊戲")](images/ch08fg07-large.png#lightbox "仿造遊戲")

但是 **MonkeyTap** 程式真的需要音效。  (請參閱 [下一章](chapter09.md)。 ) 

## <a name="related-links"></a>相關連結

- [第8章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [第8章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [第8章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)
