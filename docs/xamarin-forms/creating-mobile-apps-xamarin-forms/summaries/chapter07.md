---
title: 第7章的摘要。 XAML 與程式碼的比較
description: 使用 Xamarin 建立 Mobile Apps：第7章的摘要。 XAML 與程式碼的比較
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: ce4dde3716176daf826678809339afb84c25d84a
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334727"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>第7章的摘要。 XAML 與程式碼的比較

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> 此頁面上的附注指出 Xamarin 從書籍中呈現的材料中分歧的區域。

Xamarin 支援以 XML 為基礎的標記語言，稱為 Extensible Application Markup Language 或 XAML （發音為 "zammel"）。 XAML 在定義 Xamarin 應用C#程式的使用者介面版面配置，以及定義使用者介面專案與基礎資料之間的系結時，提供了的替代方法。

## <a name="properties-and-attributes"></a>屬性和屬性

Xamarin 會成為 XAML 中的 XML 元素，而這些類別和結構的屬性會變成 XML 屬性。 若要在 XAML 中具現化，類別通常必須有公用無參數的函式。 在 XAML 中設定的任何屬性都必須具有公用 `set` 存取子。

對於基本資料類型的屬性（`string`、`double`、`bool`等等），XAML 剖析器會使用標準的 `TryParse` 方法，將屬性設定轉換成這些類型。 XAML 剖析器也可以輕鬆地處理列舉類型，如果列舉類型是以 `Flags` 屬性加上旗標，則可以結合列舉成員。

為了協助 XAML 剖析器，更複雜的型別（或這些型別的屬性）可以包含[`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute)來識別衍生自的類別， [`TypeConverter`](xref:Xamarin.Forms.TypeConverter)支援從字串值轉換成這些類型。 例如， [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter)會將色彩名稱和字串（例如 "#rrggbb"）轉換成 `Color` 值。

## <a name="property-element-syntax"></a>屬性元素語法

在 XAML 中，類別和從它們建立的物件會以 XML 元素表示。 這些稱為*物件元素*。 這些物件的大部分屬性都會表示為 XML 屬性。 這些稱為*屬性屬性*。

有時，屬性必須設定為無法以簡單字串表示的物件。 在這種情況下，XAML 支援稱為*property*專案的標記，其中包含以句點分隔的類別名稱和屬性名稱。 然後，物件專案可以出現在一對屬性元素標記內。

## <a name="adding-a-xaml-page-to-your-project"></a>將 XAML 頁面加入至您的專案

當您第一次建立時，Xamarin 可移植類別庫可以包含 XAML 頁面，或者您也可以將 XAML 頁面加入至現有的專案。 在加入新專案的對話方塊中，選擇參考 XAML 頁面的專案，或 `ContentPage` 和 XAML。 （不是 `ContentView`）。

> [!NOTE]
> 在撰寫這一章之後，Visual Studio 選項已變更。

會建立兩個檔案：副檔名為 .xaml 的 XAML 檔案，以及C#副檔名為 xaml.cs 的檔案。 C#檔案通常稱為 XAML 檔案的*程式碼後*置。 程式碼後置檔案是衍生自 `ContentPage`的部分類別定義。 在建立時，會剖析 XAML，並針對相同的類別產生另一個部分類別定義。 這個產生的類別包含名為 `InitializeComponent` 的方法，從程式碼後置檔案的函式中呼叫。

在執行時間期間，在 `InitializeComponent` 通話結束時，XAML 檔案的所有元素都已經具現化並初始化，就像是在程式碼中C#建立一樣。

XAML 檔案中的根項目是 `ContentPage`。 根標籤包含至少兩個 XML 命名空間宣告，一個用於 Xamarin. Forms 元素，另一個則為所有 XAML 執行內建的元素和屬性定義 `x` 前置詞。 根標籤也包含 `x:Class` 屬性，指出衍生自 `ContentPage`的命名空間和類別名稱。 這會符合程式碼後置檔案中的命名空間和類別名稱。

XAML 和程式碼的組合是由[**CodePlusXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)範例所示範。

## <a name="the-xaml-compiler"></a>XAML 編譯器

Xamarin 具有 XAML 編譯器，但根據使用[`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)，其用途是選擇性的。 如果未編譯 XAML，XAML 會在組建階段進行剖析，並將 XAML 檔案內嵌在 PCL 中，也就是在執行時間進行剖析。 如果 XAML 已編譯，則組建程式會將 XAML 轉換成二進位格式，而執行時間處理會更有效率。

## <a name="platform-specificity-in-the-xaml-file"></a>XAML 檔案中的平臺具體

在 XAML 中， [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)類別可以用來選取平臺相依標記。 這是泛型類別，而且必須使用符合目標型別的 `x:TypeArguments` 屬性來具現化。 [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1)類別類似，但使用的頻率很低。

在發行書籍之後，使用 `OnPlatform` 已經變更。 它原先與名為 `iOS`、`Android`和 `WinPhone`的屬性搭配使用。 它現在會與子[`On`](xref:Xamarin.Forms.On)物件搭配使用。 將[`Platform`](xref:Xamarin.Forms.On.Platform)屬性設定為與[`Device`](xref:Xamarin.Forms.Device)類別的公用 `const` 欄位一致的字串。 將[`Value`](xref:Xamarin.Forms.On.Value)屬性設定為與 `OnPlatform` 標記的 `x:TypeArguments` 屬性一致的值。

`OnPlatform` 會在[**ScaryColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList)範例中示範，因此會呼叫，因為它包含幾乎完全相同的 XAML 區塊。 這個重複性標記的存在，表示應該可以使用技術來減少。

## <a name="the-content-property-attributes"></a>Content 屬性屬性

某些屬性專案會經常發生，例如 `ContentPage`之根項目的 `<ContentPage.Content>` 標記，或是括住 `StackLayout`子系的 `<StackLayout.Children>` 標記。

每個類別都可以使用類別上的[`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute)來識別一個屬性。 針對這個屬性，不需要屬性專案標記。 `ContentPage` 會將其 content 屬性定義為 `Content`，而 `Layout<T>` （`StackLayout` 衍生的類別）會將其 content 屬性定義為 `Children`。 不需要這些屬性元素標記。

`Label` 的屬性元素 `Text`。

## <a name="formatted-text"></a>格式化文字

[**TextVariations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations)範例包含幾個範例，說明如何設定 `Label`的 `Text` 和 `FormattedText` 屬性。 在 XAML 中，`Span` 物件會顯示為 `FormattedString` 物件的子系。

 當多行字串設定為 `Text` 屬性時，會將行尾字元轉換成空白字元，但當多行字串顯示為 `Label` 或 `Label.Text` 標記的內容時，會保留行尾字元：

 [![文字變化共用的三向螢幕擷取畫面](images/ch07fg03-small.png "格式化的文字變化")](images/ch07fg03-large.png#lightbox "格式化的文字變化")

## <a name="related-links"></a>相關連結

- [第7章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [第7章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [第 7 F#章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [XAML 基本知識](~/xamarin-forms/xaml/xaml-basics/index.md)
