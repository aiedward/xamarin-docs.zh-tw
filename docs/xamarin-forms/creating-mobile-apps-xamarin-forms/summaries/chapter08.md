---
title: 第8章摘要。 程式碼和 XAML 搭配
description: 使用 Xamarin.表單創建行動應用程式:第 8 章摘要。 程式碼和 XAML 搭配
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 75f153499edb6d979f9a0269a1439eaf8ca53878
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334233"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>第8章摘要。 程式碼和 XAML 搭配

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

本章更深入地探討 XAML,特別是代碼和 XAML 的交互方式。

## <a name="passing-arguments"></a>傳遞引數

在一般情況下,在 XAML 中實例化的類必須具有公共無參數構造函數;結果的物件通過屬性設置初始化。 但是,還可以通過兩種其他方式實例化並初始化物件。

儘管這些是通用技術,但它們主要用於與 MVVM 視圖模型相關的技術。

### <a name="constructors-with-arguments"></a>具有參數的建構函式

[**參數建構函數演示**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo)範例展示如何`x:Arguments`使用 標記指定構造函數參數。 這些參數必須按指示參數類型的元素標記分隔。 對於基本的 .NET 資料類型,可以使用以下標記:

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

### <a name="can-i-call-methods-from-xaml"></a>我可以從 XAML 調用方法嗎?

[**FactoryMethodDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo)範例示範如何使用`x:FactoryMethod`元素 指定呼叫以建立物件的工廠方法。 這種工廠方法必須是公共的和靜態的,並且必須創建定義該方法的類型的物件。 (例如,[`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double))此方法符合條件,因為它是公共的與靜態的,並傳`Color`回類型的值 。工廠方法的參數在標記中`x:Arguments`指定。

## <a name="the-xname-attribute"></a>x:名稱屬性

該`x:Name`屬性允許為在 XAML 中實例化的物件指定一個名稱。 這些名稱的規則與 C# 變數名稱的規則相同。 在建構函數中`InitializeComponent`返回調用後,代碼後面檔可以引用這些名稱來訪問相應的 XAML 元素。 名稱實際上由 XAML 解析器轉換為生成的部分類中的私有欄位。

[**XamlClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock)範例展示了`x:Name`允許代碼背後的檔案使用時,使用當前日期和時間`Label`更新 XAML 中定義的兩個元素。

同一名稱不能用於同一頁面上的多個元素。 如果使用為每個`OnPlatform`平台創建並行命名物件,則這是一個特殊問題。 [**Platform特例標籤**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels)範例示範了執行類似操作的更好方法。

## <a name="custom-xaml-based-views"></a>基於 XAML 的自訂檢視

有幾種方法可以避免重複 XAML 中的標記。 一種常見的方法是建立一個基於 XAML 的新類別,[`ContentView`](xref:Xamarin.Forms.ContentView)該類 派生自 。 此技術在[**ColorViewList 範例中**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList)進行了演示。 類`ColorView`派`ContentView`生 用於顯示特定顏色及其名稱`ColorViewListPage`,而`ContentPage`類則 像往常一樣派生,並顯式`ColorView`創建 17 個 實例。

在 XAML`ColorView`中存取 類別需要另一`local`個 XML 命名 空間聲明,通常為同一程式集中的類命名。

## <a name="events-and-handlers"></a>事件與處理程式

事件可以分配給 XAML 中的事件處理程式,但事件處理程式本身必須在代碼後面的檔中實現。 [**XamlKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad)展示如何在 XAML 中建構鍵盤使用者介面以及如何在代碼`Clicked`背後檔中實現 處理程式。

## <a name="tap-gestures"></a>點擊手勢

任何`View`物件都可以獲取觸摸輸入並從該輸入生成事件。 類`View`定義[`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers)集合 屬性,該屬性可以包含派[`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer)生自 的類的一個或多個實例。

生成[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)[`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped)事件。 [**MonkeyTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap)程式展示如何將物件附加`TapGestureRecognizer`到`BoxView`四個元素以建立仿遊戲:

[![猴子水龍頭的三重截圖](images/ch08fg07-small.png "模仿遊戲")](images/ch08fg07-large.png#lightbox "模仿遊戲")

但**猴子Tap**程式確實需要聲音。 ( 請參閱[下一章](chapter09.md)。

## <a name="related-links"></a>相關連結

- [第八章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [第八章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [第八章 F# 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [XAML 的傳遞參數](~/xamarin-forms/xaml/passing-arguments.md)
