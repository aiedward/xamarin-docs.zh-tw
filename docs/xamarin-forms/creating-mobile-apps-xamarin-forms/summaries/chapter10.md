---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 10. XAML markup extensions''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8f23034df684e778677e4f2e480e1c41807536fb
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136808"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>第10章的摘要。 XAML 標記延伸

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

一般來說，XAML 剖析器會根據基本 .NET 資料類型的標準轉換，或是 [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) 附加至屬性或其類型的衍生，將任何字串集轉換為屬性的類型 [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) 。

但是，從不同的來源設定屬性（例如，字典中的專案、靜態屬性或欄位的值，或從某種類型的計算中），有時會很方便。

這是*XAML 標記延伸*的作業。 儘管名稱，XAML 標記延伸並*不*是 XML 的延伸。 XAML 一律是合法的 XML。

## <a name="the-code-infrastructure"></a>程式碼基礎結構

XAML 標記延伸是用來執行介面的類別 [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) 。 這種類別的 `Extension` 名稱結尾通常會有一個單字，但通常會出現在 XAML 中，而沒有該尾碼。

所有 XAML 的執行都支援下列 XAML 標記延伸：

- `x:Static`支援者[`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference`支援者[`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type`支援者[`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null`支援者[`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array`支援者[`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

許多 XAML 的實現都支援這四個 XAML 標記延伸，包括 Xamarin.Forms ：

- `StaticResource`支援者[`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource`支援者[`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding`如第 [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) &mdash; 16 章所述，支援[。資料](chapter16.md)系結
- `TemplateBinding`[`TemplateBindingExtension`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) &mdash; 不在本書涵蓋的支援

與的連接中包含額外的 XAML 標記延伸 Xamarin.Forms [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ：

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;未涵蓋在本書中

## <a name="accessing-static-members"></a>存取靜態成員

使用元素，將 [`x:Static`](xref:Xamarin.Forms.Xaml.StaticExtension) 屬性設定為公用靜態屬性、欄位或列舉成員的值。 將 [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) 屬性設定為靜態成員。 `x:Static`在大括弧中指定和成員名稱通常較容易。 `Member`不需要包含屬性的名稱，只是成員本身。 此通用語法會顯示在[**SharedStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics)範例中。 靜態欄位本身定義于 [`AppConstants`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) 類別中。 這項技術可讓您建立透過程式使用的常數。

有了額外的 XML 命名空間宣告，您可以參考 .NET framework 中定義的公用靜態屬性、欄位或列舉成員，如[**SystemStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics)範例中所示。

## <a name="resource-dictionaries"></a>資源字典

`VisualElement`類別會定義名為 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 的屬性，您可以將它設定為類型的物件 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 在 XAML 中，您可以在此字典中儲存專案，並使用屬性來識別它們 `x:Key` 。 儲存在資源字典中的專案會在專案的所有參考之間共用。

### <a name="staticresource-for-most-purposes"></a>適用于大部分用途的 StaticResource

在大部分情況下，您會使用 [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) 標記延伸來參考資源字典中的專案，如[**ResourceSharing**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing)範例所示。 您可以使用 `StaticResourceExtension` 元素或 `StaticResource` 大括弧內：

[![資源分享的三重螢幕擷取畫面](images/ch10fg03-small.png "資源共用")](images/ch10fg03-large.png#lightbox "資源共用")

請勿混淆 `x:Static` 標記延伸和 `StaticResource` 標記延伸。

### <a name="a-tree-of-dictionaries"></a>字典的樹狀結構

當 XAML 剖析器遇到時 `StaticResource` ，它會開始在視覺化樹狀結構中搜尋相符的索引鍵，然後在 `ResourceDictionary` 應用程式的類別中尋找 `App` 。 這可讓視覺化樹狀結構中資源字典中的專案更深入地覆寫視覺化樹狀結構中較高的資源字典。 這會在[**ResourceTrees**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees)範例中示範。

### <a name="dynamicresource-for-special-purposes"></a>適用于特殊用途的 DynamicResource

`StaticResource`標記延伸會導致在呼叫期間建立視覺化樹狀結構時，從字典中抓取專案 `InitializeComponent` 。 的替代方法 `StaticResource` 是 [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) ，它會維護字典索引鍵的連結，並在索引鍵所參考的專案變更時更新目標。

和之間的 `StaticResource` 差異 `DynamicResource` 會在[**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)範例中示範。

由設定的屬性 `DynamicResource` 必須由可系結的屬性支援，如第[11 章的](chapter11.md)可系結基礎結構中所述。

## <a name="lesser-used-markup-extensions"></a>較少使用的標記延伸

使用 [`x:Null`](xref:Xamarin.Forms.Xaml.NullExtension) 標記延伸將屬性設定為 `null` 。

使用 [`x:Type`](xref:Xamarin.Forms.Xaml.TypeExtension) 標記延伸將屬性設定為 .net `Type` 物件。

用 [`x:Array`](xref:Xamarin.Forms.Xaml.ArrayExtension) 來定義陣列。 將 [ `Type` ] 屬性設定為標記延伸，以指定陣列成員的類型 `x:Type` 。

## <a name="a-custom-markup-extension"></a>自訂標記延伸

您可以撰寫使用方法來執行介面的類別，以建立自己的 XAML 標記延伸 [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) [`ProvideValue`](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) 。

[`HslColorExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs)類別滿足這些需求。 它會 `Color` 根據名為、、和的屬性值來建立類型的值 `H` `S` `L` `A` 。 這個類別是程式庫中名為 FormsBook 的第一個專案， Xamarin.Forms 這是在本書的課程中所建立和使用的[**工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)組。

[**CustomExtensionDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo)範例會示範如何參考此程式庫，並使用自訂標記延伸模組。

## <a name="related-links"></a>相關連結

- [第10章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [第10章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
