---
title: "第 7 章的摘要。 XAML 與程式碼"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 1263328a748ac0bacd368da361aeaff57c4cfa20
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>第 7 章的摘要。 XAML 與程式碼

Xamarin.Forms 可支援稱為 「 可延伸的應用程式標記語言的 XML 架構的標記語言或 XAML （唸成"zammel"）。 XAML 會提供一個替代方式為 C# 中定義的 Xamarin.Forms 應用程式的使用者介面版面配置，定義使用者介面項目之間的繫結和基礎資料。

## <a name="properties-and-attributes"></a>屬性和屬性

Xamarin.Forms 類別和結構才會成為 XML 項目在 XAML 中，而這些類別和結構的屬性成為 XML 屬性。 若要在 XAML 中具現化，類別通常必須有公用的無參數建構函式。 在 XAML 中設定任何屬性都必須有公用`set`存取子。

屬性的基本資料類型 (`string`， `double`， `bool`，依此類推)，XAML 剖析器會使用標準`TryParse`將屬性設定為這些類型的方法。 XAML 剖析器可以也很容易處理列舉型別，並且如果標示列舉型別，它可以結合列舉型別成員`Flags`屬性。

若要協助 XAML 剖析器，更複雜的類型 （或這些類型的屬性） 可以包含[ `TypeConverterAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverterAttribute/)可識別類別衍生自[ `TypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverter/)可支援從轉換字串值，以這些型別。 例如， [ `ColorTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColorTypeConverter/)轉換成色彩名稱和字串，例如"#rrggbb"，`Color`值。

## <a name="property-element-syntax"></a>屬性項目語法

在 XAML 中，類別和其所建立的物件會表示為 XML 項目。 這些值稱為*物件項目*。 大部分這些物件的屬性會表示為 XML 屬性。 這種讀取稱為*屬性的屬性*。

有時屬性必須設定為不能以簡單的字串表示的物件。 在這種情況下，XAML 支援標記，稱為*屬性項目*所組成的類別名稱和屬性名稱以逗號分隔。 物件項目然後可以出現在一組屬性項目標記中。

## <a name="adding-a-xaml-page-to-your-project"></a>將 XAML 頁面加入至您的專案

當第一次建立時，或加入現有專案的 XAML 頁面 Xamarin.Forms 可攜式類別庫可包含 XAML 頁面。 在加入新項目對話方塊中，選擇 [XAML] 頁面上，是指的項目或`ContentPage`和 XAML。 (不`ContentView`。)

會建立兩個檔案： XAML 檔案與檔案名稱副檔名.xaml 和擴充功能的 C# 檔案。 xaml.cs。 C# 檔案通常稱為*程式碼後置*之 XAML 檔案。 程式碼後置檔案都是衍生自的部分類別定義`ContentPage`。 在建置時，會剖析 XAML 和另一個部分類別定義會產生相同的類別。 這個產生的類別包含名為的方法`InitializeComponent`，而從程式碼後置檔案的建構函式呼叫。

在執行階段，在結束時`InitializeComponent`呼叫時，所有 XAML 檔案的項目已具現化並初始化就如同它們已建立 C# 程式碼。

XAML 檔案中的根項目是`ContentPage`。 根標記包含至少兩個 XML 命名空間宣告，Xamarin.Forms 項目及其他定義的其中一個`x`前置詞的項目和內建的所有 XAML 實作的屬性。 也包含根標記`x:Class`指出衍生自類別的名稱與命名空間屬性`ContentPage`。 這符合程式碼後置檔案中的命名空間和類別名稱。

示範的 XAML 和程式碼組合是[ **CodePlusXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)範例。

## <a name="the-xaml-compiler"></a>XAML 編譯器

Xamarin.Forms 具 XAML 編譯器，但其使用是選擇性的基礎使用[ `XamlCompilationAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/)。 如果未編譯 XAML，在建置時，會剖析 XAML 和 XAML 檔案內嵌的 PCL 中，其中它也會剖析在執行階段。 如果編譯 XAML，建置程序將 XAML 轉換成二進位格式，而且執行階段處理更有效率。

## <a name="platform-specificity-in-the-xaml-file"></a>XAML 檔案中的平台明確性比較

在 XAML 中、 [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/)類別可以用來選取平台相關的標記。 這是泛型類別，而且必須具現化與`x:TypeArguments`符合目標類型的屬性。 [ `OnIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnIdiom%3CT%3E/)類別非常類似，但使用較。

使用`OnPlatform`之後發行的活頁簿已變更。 它原本與搭配使用名為屬性`iOS`， `Android`，和`WinPhone`。 現在搭配子[ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/)物件。 設定[ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/)屬性設為公用與一致的字串`const`欄位[ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/)類別。 設定[ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Value/)屬性設為與一致值`x:TypeArguments`屬性`OnPlatform`標記。

`OnPlatform` 示範[ **ScaryColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList)範例中，因此呼叫，因為它包含區塊的幾乎完全相同的 XAML。 這個重複的標記存在建議技術應使用它。

## <a name="the-content-property-attributes"></a>該內容屬性的屬性

某些屬性項目發生頻率很高，例如`<ContentPage.Content>`標記上的根項目`ContentPage`，或`<StackLayout.Children>`封入的子系的標記`StackLayout`。

允許的每個類別具有一個屬性的識別[ `ContentPropertyAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPropertyAttribute/)類別上。 這個屬性，不需要屬性項目標記。 `ContentPage` 定義其內容的屬性做為`Content`，和`Layout<T>`(從中類別`StackLayout`衍生) 定義其內容的屬性做為`Children`。 不需要這些屬性項目標記。

Property 元素的`Label`是`Text`。

## <a name="formatted-text"></a>格式化文字

[ **TextVariations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations)範例包含數個設定的範例`Text`和`FormattedText`屬性`Label`。 在 XAML 中、`Span`物件顯示為的子系`FormattedString`物件。

 當多行字串設定為`Text`屬性，行結尾字元都轉換成空格字元，但是多行字串中出現的內容時，會保留行結尾字元`Label`或`Label.Text`標記：

 [![共用的文字變化的三個螢幕擷取畫面](images/ch07fg03-small.png "格式化文字變化")](images/ch07fg03-large.png "格式化文字變化")



## <a name="related-links"></a>相關連結

- [第 7 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [第 7 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [第 7 章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
