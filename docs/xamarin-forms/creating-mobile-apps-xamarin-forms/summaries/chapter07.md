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
ms.openlocfilehash: d69c5c96138024f6a45d71013b259b2c47a20da3
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370172"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>第7章的摘要。 XAML 與程式碼

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

Xamarin.Forms 支援名為 Extensible Application Markup Language 或 XAML (發音為 "zammel" ) 的 XML 標記語言。 XAML 可在定義應用程式使用者介面的版面配置 Xamarin.Forms ，以及定義使用者介面專案與基礎資料之間的系結時，提供 c # 的替代方案。

## <a name="properties-and-attributes"></a>屬性和屬性

Xamarin.Forms 類別和結構會成為 XAML 中的 XML 元素，而這些類別和結構的屬性會變成 XML 屬性。 若要在 XAML 中具現化，類別通常必須有公用無參數的函式。 在 XAML 中設定的任何屬性都必須有公用 `set` 存取子。

若為基本資料類型的屬性 (`string` 、 `double` 、 `bool` 等等) ，XAML 剖析器會使用標準 `TryParse` 方法將屬性設定轉換為這些類型。 XAML 剖析器也可以輕鬆地處理列舉型別，而且如果列舉型別是以屬性加上旗標，也可以結合列舉成員 `Flags` 。

為了協助 XAML 剖析器，更複雜的型別 (或這些型別的屬性) 可以包含 [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) ，它會識別衍生自的類別， [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) 以支援從字串值轉換成這些類型的類別。 例如，會 [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) 將色彩名稱和字串（例如 "#rrggbb"）轉換成 `Color` 值。

## <a name="property-element-syntax"></a>屬性-元素語法

在 XAML 中，類別和從這些類別建立的物件會以 XML 元素表示。 這些稱為 *物件元素* 。 這些物件的大部分屬性會以 XML 屬性工作表示。 這些稱為 *屬性屬性* 。

有時屬性必須設定為無法以簡單字串表示的物件。 在這種情況下，XAML 支援稱為 *property* 專案的標記，此專案是由以句號分隔的類別名稱和屬性名稱所組成。 物件元素接著可以出現在一對屬性元素標記內。

## <a name="adding-a-xaml-page-to-your-project"></a>將 XAML 頁面新增至您的專案

Xamarin.Forms當您第一次建立時，可移植類別庫可以包含 xaml 頁面，也可以將 xaml 頁面加入至現有的專案。 在對話方塊中，若要加入新的專案，請選擇參考 XAML 頁面的專案，或是 `ContentPage` 和 xaml。  (不是 `ContentView` ) 。

> [!NOTE]
> 自這一章起，Visual Studio 選項已變更。

建立兩個檔案：副檔名為 .xaml 的 XAML 檔案，以及副檔名為 xaml.cs 的 c # 檔案。 C # 檔案通常稱為 XAML 檔案的 *程式碼後端* 。 程式碼後端檔案是衍生自的部分類別定義 `ContentPage` 。 在組建階段，會剖析 XAML，並為相同類別產生另一個部分類別定義。 這個產生的類別包含名為 `InitializeComponent` 的方法，該方法是從程式碼後端檔案的函式所呼叫。

在執行時間期間，在通話結束時 `InitializeComponent` ，會將 XAML 檔案的所有元素都具現化並初始化，就好像它們是在 c # 程式碼中建立一樣。

XAML 檔案中的根項目是 `ContentPage` 。 根標記至少包含兩個 XML 命名空間宣告，一個用於元素，另一個則 Xamarin.Forms 定義所有 XAML 實作為內建之 `x` 元素和屬性的前置詞。 根標記也包含一個 `x:Class` 屬性，這個屬性會指出衍生自之類別的命名空間和名稱 `ContentPage` 。 這會符合程式碼後端檔案中的命名空間和類別名稱。

[**CodePlusXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)範例會示範 XAML 和程式碼的組合。

## <a name="the-xaml-compiler"></a>XAML 編譯器

Xamarin.Forms 具有 XAML 編譯器，但它的使用是根據使用的選擇性選項 [`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) 。 如果未編譯 XAML，則會在組建階段剖析 XAML，並將 XAML 檔案內嵌在 PCL 中，也就是在執行時間進行剖析。 如果 XAML 已編譯，則組建程式會將 XAML 轉換成二進位格式，而且執行時間處理更有效率。

## <a name="platform-specificity-in-the-xaml-file"></a>XAML 檔案中的平臺明確

在 XAML 中， [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) 類別可以用來選取平臺相依標記。 這是泛型類別，而且必須使用 `x:TypeArguments` 符合目標型別的屬性來具現化。 [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1)類別很類似，但使用的頻率較低。

在 `OnPlatform` 發行本書之後，使用已經變更。 它原本是與名為、和的屬性搭配使用 `iOS` `Android` `WinPhone` 。 它現在與子物件一起使用 [`On`](xref:Xamarin.Forms.On) 。 將 [`Platform`](xref:Xamarin.Forms.On.Platform) 屬性設為與類別的公用欄位一致的字串 `const` [`Device`](xref:Xamarin.Forms.Device) 。 將 [`Value`](xref:Xamarin.Forms.On.Value) 屬性設定為與標記的屬性一致的值 `x:TypeArguments` `OnPlatform` 。

`OnPlatform` 會在 [**ScaryColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) 範例中示範，因此呼叫，因為它包含幾乎完全相同的 XAML 區塊。 這項重複的標記是否存在，也會建議您可以使用這些技術來減少。

## <a name="the-content-property-attributes"></a>Content 屬性屬性

某些屬性元素會相當頻繁地發生，例如的 `<ContentPage.Content>` 根項目上的標記 `ContentPage` ，或是包含子系的 `<StackLayout.Children>` 標記 `StackLayout` 。

每個類別都可以使用類別上的來識別一個屬性 [`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute) 。 針對此屬性，不需要屬性元素標記。 `ContentPage` 將其上下文屬性定義為 `Content` ，並 `Layout<T>` (`StackLayout` 衍生) 將其內容屬性定義為的類別 `Children` 。 不需要這些屬性元素標記。

的 property 元素 `Label` 為 `Text` 。

## <a name="formatted-text"></a>格式化文字

[**TextVariations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations)範例包含數個設定 `Text` 和 `FormattedText` 屬性的範例 `Label` 。 在 XAML 中， `Span` 物件會顯示為物件的子系 `FormattedString` 。

 當多行字串設定為屬性時 `Text` ，會將行尾字元轉換成空白字元，但是當多行字串顯示為或標記的內容時，會保留行尾字元 `Label` `Label.Text` ：

 [![文字變化共用的三重螢幕擷取畫面](images/ch07fg03-small.png "格式化的文字變化")](images/ch07fg03-large.png#lightbox "格式化的文字變化")

## <a name="related-links"></a>相關連結

- [第7章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [第7章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [第7章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [XAML 基本知識](~/xamarin-forms/xaml/xaml-basics/index.md)
