---
title: 第 8 章的摘要。 程式碼和 XAML 中互聯
description: 使用 Xamarin.Forms 建立行動裝置應用程式： 第 8 章的摘要。 程式碼和 XAML 中互聯
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 70c2babef55ee6cb886b0e7da26ee57b086fa558
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241015"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>第 8 章的摘要。 程式碼和 XAML 中互聯

這一章探討 XAML 更深入，以及特別是程式碼和 XAML 的互動方式。

## <a name="passing-arguments"></a>傳遞引數

在一般情況下，在 XAML 中具現化的類別必須具有公用的無參數建構函式。結果物件是透過屬性設定來初始化。 不過，有兩種方式可以具現化並初始化物件。

雖然這些是一般用途的技術，它們主要用與 MVVM 檢視模型。

### <a name="constructors-with-arguments"></a>引數的建構函式

[ **ParameteredConstructorDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo)範例示範如何使用`x:Arguments`標記指定的建構函式引數。 這些引數必須加以分隔，表示引數的型別項目標記。 針對基本的.NET 資料類型，可用下列標記如下：

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

### <a name="can-i-call-methods-from-xaml"></a>可以呼叫的方法從 XAML 嗎？

[ **FactoryMethodDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo)範例示範如何使用`x:FactoryMethod`項目來指定會叫用來建立物件的 factory 方法。 這類的 factory 方法，必須是 public 和 static，而且必須建立在定義類型的物件。 (例如[ `Color.FromRgb` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Double/System.Double/System.Double/)) 方法評估是否符合，因為它是 public 和 static，傳回值的型別`Color`。)Factory 方法的引數中指定`x:Arguments`標記。

## <a name="the-xname-attribute"></a>X:name 屬性

`x:Name`屬性，允許在 XAML 中提供的名稱來具現化物件。 這些名稱的規則是與 C# 變數名稱相同。 下列的傳回`InitializeComponent`建構函式呼叫、 程式碼後置檔案可以參考這些名稱來存取對應的 XAML 項目。 名稱實際上會轉換成產生的部分類別中的私用欄位 XAML 剖析器。

[ **XamlClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock)範例將示範如何使用`x:Name`允許的程式碼後置檔案，以保留兩個`Label`定義在 XAML 中更新為目前的日期和時間的項目。

相同的名稱不能用於在相同頁面上的多個項目。 這是對特定問題，如果您使用`OnPlatform`建立平行具名物件的每個平台。 [ **PlatformSpecificLabele** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels)範例將示範如何進行類似的更好的方法。

## <a name="custom-xaml-based-views"></a>以 XAML 為基礎的自訂檢視

有幾種方式以避免重複 XAML 中的標記。 若要建立新的 XAML 型類別衍生自的一個常用的技巧是[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)。 在示範這項技術[ **ColorViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList)範例。 `ColorView`類別衍生自`ContentView`來顯示特定的色彩和它的名稱，請同時`ColorViewListPage`類別衍生自`ContentPage`如往常般且明確地建立 17 個`ColorView`。

存取`ColorView`在 XAML 中的類別需要另一個 XML 命名空間宣告，通常名為`local`相同組件中的類別。

## <a name="events-and-handlers"></a>事件和處理常式

事件可以指派給事件處理常式，在 XAML 中，但必須在程式碼後置檔案中實作本身的事件處理常式。 [ **XamlKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad)示範如何建立在 XAML 中的數字鍵台使用者介面以及如何實作`Clicked`程式碼後置檔案中的處理常式。

## <a name="tap-gestures"></a>點選 筆勢

任何`View`物件可以取得具備觸控輸入，並從該輸入產生事件。 `View`類別會定義[ `GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/)集合屬性，其中可以包含一或多個執行個體的類別衍生自[ `GestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)。

[ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)產生[ `Tapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.TapGestureRecognizer.Tapped/)事件。 [ **MonkeyTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap)程式示範如何附加`TapGestureRecognizer`物件以四個`BoxView`建立販售遊戲的項目：

[![猴子點選的三個螢幕擷取畫面](images/ch08fg07-small.png "Imitation 遊戲")](images/ch08fg07-large.png#lightbox "Imitation 遊戲")

但是**MonkeyTap**程式真正需要的聲音。 (請參閱[下一章](chapter09.md)。)



## <a name="related-links"></a>相關連結

- [第 8 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [第 8 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [第 8 章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
