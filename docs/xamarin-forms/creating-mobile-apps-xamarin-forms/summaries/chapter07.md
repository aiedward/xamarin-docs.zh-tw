---
title: 第7章摘要。 XAML 與程式碼
description: 使用 Xamarin.表單創建行動應用程式:第 7 章摘要。 XAML 與程式碼
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: ce4dde3716176daf826678809339afb84c25d84a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334727"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>第7章摘要。 XAML 與程式碼

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> 本頁的註釋指示 Xamarin.Forms 與本書中介紹的材料有分歧的領域。

Xamarin.Forms 支援一種基於 XML 的標記語言,稱為可擴展應用程式標記語言或 XAML(發音為"zammel")。 在定義 Xamarin.Forms 應用程式的使用者介面佈局以及定義使用者介面元素和基礎數據之間的綁定時,XAML 提供了 C# 的替代方法。

## <a name="properties-and-attributes"></a>屬性與屬性

Xamarin.Forms 類和結構成為 XAML 中的 XML 元素,這些類和結構的屬性成為 XML 屬性。 要在 XAML 中實例化,類通常必須具有公共無參數構造函數。 在 XAML 中設置的`set`任何屬性 都必須具有公共訪問器。

對於基本資料類型 (、、、`string``double``bool`依此類推)的屬性,XAML 解析`TryParse`器使用標準方法將屬性設置轉換為這些類型。 XAML 解析器還可以輕鬆地處理枚舉類型,並且如果枚舉類型`Flags`與 屬性一起標記,則可以合併枚舉成員。

為了説明 XAML 解析器,更複雜的類型(或這些類型的屬性)可以[`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute)包括標識[`TypeConverter`](xref:Xamarin.Forms.TypeConverter)派生的 類的類,該類支援從字串值轉換為這些類型。 例如,[`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter)將顏色名稱和字串(如"#rrggbb")轉換為`Color`值。

## <a name="property-element-syntax"></a>屬性元素語法

在 XAML 中,類和從它們創建的物件表示為 XML 元素。 這些稱為*物件元素*。 這些物件的大多數屬性表示為 XML 屬性。 這些稱為*屬性屬性*。

有時,屬性必須設置為無法表示為簡單字串的物件。 在這種情況下,XAML 支援稱為*屬性元素*的標記,該標記由由句點分隔的類名稱和屬性名稱組成。 然後,物件元素可以出現在一對屬性元素標記中。

## <a name="adding-a-xaml-page-to-your-project"></a>新增 XAML 頁面

Xamarin.Forms便攜式類庫在首次創建時可以包含 XAML 頁,也可以將 XAML 頁添加到現有專案。 在添加新項的對話框中,選擇引用 XAML`ContentPage`頁或 XAML 的專案。 (不是`ContentView`.)

> [!NOTE]
> 自撰寫本章以來,可視化工作室選項已更改。

創建兩個檔:具有檔案名副檔名 .xaml 的 XAML 檔案以及副檔名 .xaml.cs的 C# 檔。 C# 檔案通常是 XAML 檔案*的代碼後面*。 代碼後面檔案是從 衍生的部份類別`ContentPage`定義 。 在生成時,將分析 XAML,並為同一類生成另一個部分類定義。 此生成的類包括從代碼背後的`InitializeComponent`文件的構造函數調用名為的方法。

在運行時,在`InitializeComponent`調用結束時,XAML 檔的所有元素都已實例化並初始化,就像它們是在 C# 代碼中創建的一樣。

XAML 檔案中的根元素`ContentPage`是 。 根標記至少包含兩個 XML 命名空間聲明,一個用於 Xamarin.Forms 元素`x`,另一個 定義所有 XAML 實現所固有的元素和屬性的前置碼。 根標記還包含一個`x:Class`屬性,指示派生`ContentPage`自 的類的命名空間和名稱。 這與代碼背後的檔中的命名空間和類名稱匹配。

[**CodePlusXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)範例展示 XAML 和代碼的組合。

## <a name="the-xaml-compiler"></a>XAML 編譯器

Xamarin.forms 具有 XAML 編譯器,但其使用使用以使用[`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)可選的 。 如果未編譯 XAML,則在生成時分析 XAML,並將 XAML 檔嵌入 PCL 中,在 PCL 中也會在運行時對其進行解析。 如果編譯了 XAML,則生成過程將 XAML 轉換為二進位窗體,並且運行時處理效率更高。

## <a name="platform-specificity-in-the-xaml-file"></a>XAML 檔案中的平台專用性

在 XAML[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)中,該類可用於選擇與平台相關的標記。 這是一個泛型類,必須使用與目標類型匹配`x:TypeArguments`的屬性實例化。 類[`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1)相似,但使用頻率要低得多。

自從這本書出版`OnPlatform`以來,其使用方式發生了變化。 它最初與名為`iOS`、`Android``WinPhone`和的屬性結合使用。 它現在與子[`On`](xref:Xamarin.Forms.On)物件一起使用。 將[`Platform`](xref:Xamarin.Forms.On.Platform)屬性設置為`const`[`Device`](xref:Xamarin.Forms.Device)與 類的公共欄位一致的字串。 將[`Value`](xref:Xamarin.Forms.On.Value)屬性設置為`x:TypeArguments``OnPlatform`與 標記的屬性一致的值。

`OnPlatform`在[**「可怕顏色清單」**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList)範例中演示,因此之所以稱為"可怕"範例,是因為它包含幾乎相同的 XAML 塊。 這種重複標記的存在表明,應該提供減少這種標記的技術。

## <a name="the-content-property-attributes"></a>內容屬性屬性

某些屬性元素經常發生`<ContentPage.Content>`,`ContentPage`例如 , 的根元素上的標記`<StackLayout.Children>`或包含`StackLayout`的子元素的 標記。

允許每個類標識一個屬性,該屬性[`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute)具有類上的 a。 對於此屬性,不需要屬性元素標記。 `ContentPage`將屬性`Content`屬性定義為`Layout<T>`與 (`StackLayout`指定的類別)的屬性定義為`Children`。 這些屬性元素標記不是必需的。

的屬性元素`Label`是`Text`。

## <a name="formatted-text"></a>格式化文字

[**Text變體**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations)範例包含設定`Text``FormattedText`和 屬性`Label`的幾個 範例。 在 XAML`Span`中,對`FormattedString`象顯示為 物件的子級。

 將多行字串設定為`Text`屬性時,行尾字元將轉換為空格字元,但當多行字串顯示為`Label`或`Label.Text`標記的內容時,將保留行尾字元:

 [![文字變體共用的三重螢幕截圖](images/ch07fg03-small.png "格式化文字變體")](images/ch07fg03-large.png#lightbox "格式化文字變體")

## <a name="related-links"></a>相關連結

- [第七章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [第七章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [第七章 F# 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [XAML 基本知識](~/xamarin-forms/xaml/xaml-basics/index.md)
