---
title: 第7章的摘要。 XAML 與程式碼
description: 建立 Mobile Apps Xamarin.Forms ：第7章的摘要。 XAML 與程式碼
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0b92988e1e838072fca0d8a284455a62db05e757
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136852"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>第7章的摘要。 XAML 與程式碼

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> 此頁面上的附注指出 Xamarin.Forms 從書籍中所呈現之材質分歧的區域。

Xamarin.Forms支援稱為 Extensible Application Markup Language 或 XAML 的 XML 標記語言（發音為 "zammel"）。 XAML 提供 c # 的替代方法，以定義應用程式使用者介面的配置 Xamarin.Forms ，以及定義使用者介面專案與基礎資料之間的系結。

## <a name="properties-and-attributes"></a>屬性和屬性

Xamarin.Forms類別和結構會變成 XAML 中的 XML 元素，而這些類別和結構的屬性會變成 XML 屬性。 若要在 XAML 中具現化，類別通常必須有公用無參數的函式。 在 XAML 中設定的任何屬性都必須具有公用 `set` 存取子。

對於基本資料類型（ `string` 、 `double` 、等）的屬性，XAML 剖析器會 `bool` 使用標準 `TryParse` 方法，將屬性設定轉換成這些類型。 XAML 剖析器也可以輕鬆地處理列舉類型，如果列舉類型是以屬性加上旗標，它可以結合列舉成員 `Flags` 。

為了協助 XAML 剖析器，更複雜的型別（或這些型別的屬性）可以包含 [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) ，以識別衍生自的類別，它 [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) 支援從字串值轉換成這些型別。 例如，會 [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) 將色彩名稱和字串（例如 "#rrggbb"）轉換成 `Color` 值。

## <a name="property-element-syntax"></a>屬性元素語法

在 XAML 中，類別和從它們建立的物件會以 XML 元素表示。 這些稱為*物件元素*。 這些物件的大部分屬性都會表示為 XML 屬性。 這些稱為*屬性屬性*。

有時，屬性必須設定為無法以簡單字串表示的物件。 在這種情況下，XAML 支援稱為*property*專案的標記，其中包含以句點分隔的類別名稱和屬性名稱。 然後，物件專案可以出現在一對屬性元素標記內。

## <a name="adding-a-xaml-page-to-your-project"></a>將 XAML 頁面加入至您的專案

可 Xamarin.Forms 移植的類別庫在第一次建立時可以包含 xaml 頁面，或者您也可以將 xaml 頁面加入至現有的專案。 在加入新專案的對話方塊中，選擇參考 XAML 頁面的專案，或 `ContentPage` 和 xaml。 （不是 `ContentView` ）。

> [!NOTE]
> 在撰寫這一章之後，Visual Studio 選項已變更。

會建立兩個檔案：副檔名為 .xaml 的 XAML 檔案，以及副檔名為 xaml.cs 的 c # 檔案。 C # 檔案通常稱為 XAML 檔案的*程式碼後*置。 程式碼後置檔案是衍生自的部分類別定義 `ContentPage` 。 在建立時，會剖析 XAML，並針對相同的類別產生另一個部分類別定義。 這個產生的類別包含名為 `InitializeComponent` 的方法，它是從程式碼後置檔案的「函式」呼叫。

在執行時間期間，在通話結束時 `InitializeComponent` ，XAML 檔案的所有元素都已經具現化並初始化，就像是在 c # 程式碼中建立一樣。

XAML 檔案中的根項目為 `ContentPage` 。 根標記包含至少兩個 XML 命名空間宣告，一個用於專案，另一個則定義所有 XAML 實作為內建之 Xamarin.Forms `x` 元素和屬性的前置詞。 根標籤也包含 `x:Class` 屬性，指出衍生自之類別的命名空間和名稱 `ContentPage` 。 這會符合程式碼後置檔案中的命名空間和類別名稱。

XAML 和程式碼的組合是由[**CodePlusXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)範例所示範。

## <a name="the-xaml-compiler"></a>XAML 編譯器

Xamarin.Forms具有 XAML 編譯器，但根據使用，其使用是選擇性的 [`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) 。 如果未編譯 XAML，XAML 會在組建階段進行剖析，並將 XAML 檔案內嵌在 PCL 中，也就是在執行時間進行剖析。 如果 XAML 已編譯，則組建程式會將 XAML 轉換成二進位格式，而執行時間處理會更有效率。

## <a name="platform-specificity-in-the-xaml-file"></a>XAML 檔案中的平臺具體

在 XAML 中， [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) 類別可以用來選取平臺相依標記。 這是泛型類別，而且必須使用 `x:TypeArguments` 符合目標型別的屬性具現化。 [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1)類別很類似，但使用的頻率較少。

在 `OnPlatform` 發行書籍之後，使用已經變更。 它原本是與名為、和的屬性搭配使用 `iOS` `Android` `WinPhone` 。 它現在會與子物件搭配使用 [`On`](xref:Xamarin.Forms.On) 。 將 [`Platform`](xref:Xamarin.Forms.On.Platform) 屬性設定為與類別的公用欄位一致的字串 `const` [`Device`](xref:Xamarin.Forms.Device) 。 將 [`Value`](xref:Xamarin.Forms.On.Value) 屬性設定為與標記的屬性一致的值 `x:TypeArguments` `OnPlatform` 。

`OnPlatform`會在[**ScaryColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList)範例中示範，因此會呼叫，因為它包含幾乎完全相同的 XAML 區塊。 這個重複性標記的存在，表示應該可以使用技術來減少。

## <a name="the-content-property-attributes"></a>Content 屬性屬性

有些屬性專案經常會發生，例如的 `<ContentPage.Content>` 根項目上的標記 `ContentPage` ，或是括住子系 `<StackLayout.Children>` 的標記 `StackLayout` 。

每個類別都可以使用類別上的來識別一個具有的屬性 [`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute) 。 針對這個屬性，不需要屬性專案標記。 `ContentPage`將其 content 屬性定義為 `Content` ，並 `Layout<T>` （衍生的類別 `StackLayout` ）將其內容屬性定義為 `Children` 。 不需要這些屬性元素標記。

的屬性元素 `Label` 為 `Text` 。

## <a name="formatted-text"></a>格式化文字

[**TextVariations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations)範例包含幾個設定 `Text` 和 `FormattedText` 屬性的範例 `Label` 。 在 XAML 中， `Span` 物件會顯示為物件的子系 `FormattedString` 。

 當多行字串設定為屬性時 `Text` ，會將行尾字元轉換成空白字元，但當多行字串顯示為或標記的內容時，會保留行尾字元 `Label` `Label.Text` ：

 [![文字變化共用的三向螢幕擷取畫面](images/ch07fg03-small.png "格式化的文字變化")](images/ch07fg03-large.png#lightbox "格式化的文字變化")

## <a name="related-links"></a>相關連結

- [第7章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [第7章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [第7章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [XAML 基本知識](~/xamarin-forms/xaml/xaml-basics/index.md)
