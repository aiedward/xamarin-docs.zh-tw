---
title: 第 8 章的摘要。 程式碼和 XAML 搭配
description: 使用 Xamarin.Forms 建立行動應用程式： 第 8 章摘要。 程式碼和 XAML 搭配
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: charlespetzold
ms.author: chape
ms.date: 07/19/2018
ms.openlocfilehash: 1aa5226e1e6867f77eea4d7679650e8d62f5b981
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156986"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>第 8 章的摘要。 程式碼和 XAML 搭配

這一章探討 XAML 更深入，並特別的程式碼和 XAML 的互動方式。

## <a name="passing-arguments"></a>傳遞引數

在一般案例中，XAML 中具現化的類別必須具有公用的無參數建構函式;結果物件會初始化透過屬性設定。 不過，有兩種方式可以具現化並初始化物件。

雖然這些都是一般用途的技術，它們是最常用於與 MVVM 檢視模型。

### <a name="constructors-with-arguments"></a>引數的建構函式

[ **ParameteredConstructorDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo)範例示範如何使用`x:Arguments`標記即可指定建構函式引數。 這些引數必須分隔的項目標記，指出引數的型別。 針對基本的.NET 資料類型，可用下列標記如下：

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

### <a name="can-i-call-methods-from-xaml"></a>可以從 XAML 呼叫方法嗎？

[ **FactoryMethodDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo)範例示範如何使用`x:FactoryMethod`項目來指定會叫用來建立物件的 factory 方法。 這類的 factory 方法必須是公用、 靜態的而且必須建立在其中定義類型的物件。 (例如[ `Color.FromRgb` ](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double))方法是否符合要求，因為它是公用和靜態，並傳回值的型別`Color`。)Factory 方法的引數中指定`x:Arguments`標記。

## <a name="the-xname-attribute"></a>X:name 屬性

`x:Name`屬性允許為它指定名稱的 XAML 中具現化物件。 這些名稱的規則是與 C# 變數名稱相同。 下列的`InitializeComponent`建構函式呼叫、 程式碼後置檔案可以參考這些名稱來存取對應的 XAML 項目。 XAML 剖析器所產生的部分類別中的私用欄位到實際轉換的名稱。

[ **XamlClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock)範例示範如何使用`x:Name`允許的程式碼後置檔案，以保留兩個`Label`更新目前的日期和時間的 XAML 中定義的項目。

相同的名稱不能在相同頁面上的多個項目。 這是特定的問題，如果您使用`OnPlatform`建立平行具名物件每個平台。 [ **PlatformSpecificLabele** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels)範例會示範更好的方式，像這樣執行一些動作。

## <a name="custom-xaml-based-views"></a>以 XAML 為基礎的自訂檢視

有數種方式可避免重複的 XAML 中的標記。 一個常用的技巧是要建立新的 XAML 型類別衍生自[ `ContentView` ](xref:Xamarin.Forms.ContentView)。 這項技術所示[ **ColorViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList)範例。 `ColorView`類別衍生自`ContentView`若要顯示特定的色彩，而其名稱，而`ColorViewListPage`類別衍生自`ContentPage`如往常般且明確地建立 17 個`ColorView`。

存取`ColorView`XAML 中的類別需要另一個 XML 命名空間宣告，通常名為`local`相同的組件中的類別。

## <a name="events-and-handlers"></a>事件和處理常式

事件可以指派給事件處理常式，在 XAML 中，但必須在程式碼後置檔案中實作事件處理常式本身。 [ **XamlKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad)示範如何建置在 XAML 中的數字鍵台使用者介面，以及如何實作`Clicked`程式碼後置檔案中的處理常式。

## <a name="tap-gestures"></a>點選手勢

任何`View`物件可以取得具備觸控輸入，並從該輸入產生事件。 `View`類別會定義[ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers)集合屬性可以包含一或多個執行個體的類別衍生自[ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer)。

[ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)會產生[ `Tapped` ](xref:Xamarin.Forms.TapGestureRecognizer.Tapped)事件。 [ **MonkeyTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap)程式示範如何附加`TapGestureRecognizer`物件至四個`BoxView`建立販售的遊戲的項目：

[![Monkey 點選的三個螢幕擷取畫面](images/ch08fg07-small.png "仿造遊戲")](images/ch08fg07-large.png#lightbox "仿造遊戲")

但是**MonkeyTap**程式真正需要的音效。 (請參閱[的下一步 一章](chapter09.md)。)

## <a name="related-links"></a>相關連結

- [第 8 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [第 8 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [第 8 章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)
