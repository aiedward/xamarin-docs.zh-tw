---
title: 第 7 章的摘要。 XAML 與程式碼
description: 使用 Xamarin.Forms 建立行動應用程式： 第 7 章的摘要。 XAML 與程式碼
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 02e4ea44d87360deed361d161759fa3a2808100f
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995153"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>第 7 章的摘要。 XAML 與程式碼

Xamarin.Forms 可支援稱為 Extensible Application Markup Language 的以 XML 為基礎的標記語言或 XAML （發音為"zammel 」）。 XAML 會提供替代 C# 中定義的 Xamarin.Forms 應用程式的使用者介面版面配置，並在定義使用者介面項目之間的繫結和基礎資料。

## <a name="properties-and-attributes"></a>屬性和屬性

Xamarin.Forms 類別和結構變成 XML 項目中 XAML，而且這些類別和結構的屬性變成 XML 屬性。 要在 XAML 中具現化，類別通常必須有公用的無參數建構函式。 在 XAML 中設定任何屬性都必須有公用`set`存取子。

屬性的基本資料類型 (`string`， `double`， `bool`，依此類推)，XAML 剖析器使用標準`TryParse`屬性設定成這些類型的方法。 XAML 剖析器也很容易可以處理列舉類型，以及它可以結合列舉成員，如果列舉型別都將標示`Flags`屬性。

若要協助 XAML 剖析器，更複雜型別 （或這些類型的屬性） 可以包含[ `TypeConverterAttribute` ](xref:Xamarin.Forms.TypeConverterAttribute)所識別的類別，衍生自[ `TypeConverter` ](xref:Xamarin.Forms.TypeConverter)可支援從轉換這些類型的字串值。 例如， [ `ColorTypeConverter` ](xref:Xamarin.Forms.ColorTypeConverter)轉換成色彩名稱和字串，例如 「 #rrggbb"，`Color`值。

## <a name="property-element-syntax"></a>屬性元素語法

在 XAML 中，類別和從中建立的物件會以表示 XML 項目。 這些值稱為*物件項目*。 大部分的屬性，這些物件會表示為 XML 屬性。 這些稱為*property 屬性*。

有時必須設定的屬性不能以簡單的字串表示的物件。 在此情況下，XAML 支援標記，稱為*property 項目*包含以句點分隔的屬性名稱與類別名稱。 物件項目可以接著會出現在屬性項目標記一組中。

## <a name="adding-a-xaml-page-to-your-project"></a>將 XAML 頁面新增至您的專案

當第一次建立時，或您可以加入現有的專案中的 XAML 頁面時，Xamarin.Forms 可攜式類別庫可包含的 XAML 頁面。 在加入新項目對話方塊中，選擇 [XAML] 頁面上，是指的項目或`ContentPage`和 XAML。 (不`ContentView`。)

會建立兩個檔案： 檔案名稱副檔名.xaml 的 XAML 檔案和副檔名的 C# 檔案。 xaml.cs。 C# 檔案通常稱為*程式碼後置*的 XAML 檔案。 程式碼後置檔案是衍生自的部分類別定義`ContentPage`。 在建置階段，在剖析 XAML，和另一個部分類別定義會產生相同的類別。 這個產生的類別包含一個名為方法`InitializeComponent`，從程式碼後置檔案的建構函式呼叫。

在執行階段，在結束時`InitializeComponent`呼叫所有已具現化並初始化就如同它們已在 C# 程式碼中建立的 XAML 檔案的項目。

在 XAML 檔案中的根項目是`ContentPage`。 根標記包含至少兩個 XML 命名空間宣告，一個用於 Xamarin.Forms 項目，而其他定義`x`項目和屬性的所有 XAML 實作內建的前置詞。 也包含根標記`x:Class`屬性，表示衍生自類別的名稱與命名空間`ContentPage`。 這符合程式碼後置檔案中的命名空間和類別的名稱。

XAML 和程式碼的組合所示[ **CodePlusXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)範例。

## <a name="the-xaml-compiler"></a>XAML 編譯器

Xamarin.Forms 有 XAML 編譯器，但其使用是選擇性使用基礎[ `XamlCompilationAttribute` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)。 如果未編譯 XAML，在建置時，剖析 XAML 和 XAML 檔案內嵌在 PCL 中，其中它也會剖析在執行階段。 如果編譯的 XAML，建置程序會將 XAML 轉換成二進位格式，而且執行階段處理更有效率。

## <a name="platform-specificity-in-the-xaml-file"></a>在 XAML 檔案中的平台精確性

在 XAML 中， [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1)類別可用來選取平台相依的標記。 這是泛型類別，必須使用具現化`x:TypeArguments`符合目標類型的屬性。 [ `OnIdiom` ](xref:Xamarin.Forms.OnIdiom`1)類別類似，但使用較少通常是。

使用`OnPlatform`之後發行的活頁簿已變更。 它原本與搭配使用名為的屬性`iOS`， `Android`，和`WinPhone`。 現在搭配子[ `On` ](xref:Xamarin.Forms.On)物件。 設定[ `Platform` ](xref:Xamarin.Forms.On.Platform)屬性設為公用與一致的字串`const`的欄位[ `Device` ](xref:Xamarin.Forms.Device)類別。 設定[ `Value` ](xref:Xamarin.Forms.On.Value)屬性設為與一致值`x:TypeArguments`屬性`OnPlatform`標記。

`OnPlatform` 所示[ **ScaryColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList)範例中，因此呼叫，因為它包含幾乎完全相同的 XAML 的區塊。 此重複的標記存在建議應該可減少它的技術。

## <a name="the-content-property-attributes"></a>該內容屬性的屬性

某些屬性項目會很頻繁，發生這類`<ContentPage.Content>`標記的根項目`ContentPage`，或有`<StackLayout.Children>`括住的子系的標記`StackLayout`。

每個類別可以識別具有一個屬性[ `ContentPropertyAttribute` ](xref:Xamarin.Forms.ContentPropertyAttribute)類別上。 這個屬性，則不需要屬性-元素標記。 `ContentPage` 定義為其內容屬性`Content`，並`Layout<T>`(從中的類別`StackLayout`衍生) 定義為其內容屬性`Children`。 不需要這些屬性項目標記。

屬性項目`Label`是`Text`。

## <a name="formatted-text"></a>格式化文字

[ **TextVariations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations)範例包含數個設定的範例`Text`並`FormattedText`屬性`Label`。 在 XAML 中，`Span`物件會顯示為的子系`FormattedString`物件。

 當多行字串設定為`Text`屬性，行結尾字元都轉換成空格字元，但是多行字串中出現的內容時，會保留行尾字元`Label`或`Label.Text`標記：

 [![共用的文字變化的三個螢幕擷取畫面](images/ch07fg03-small.png "格式化文字變化")](images/ch07fg03-large.png#lightbox "格式化文字變化")



## <a name="related-links"></a>相關連結

- [第 7 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [第 7 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [第 7 章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
