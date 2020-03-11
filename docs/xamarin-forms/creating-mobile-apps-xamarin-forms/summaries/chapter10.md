---
title: 第10章的摘要。 XAML 標記延伸
description: 使用 Xamarin 建立 Mobile Apps：第10章的摘要。 XAML 標記延伸
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 076e9f5155492e5a69d906c587b24495fe39d3f1
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334281"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>第10章的摘要。 XAML 標記延伸

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

一般來說，XAML 剖析器會根據基本 .NET 資料類型的標準轉換，將任何字串集轉換為屬性的類型，或是以[`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute)附加至屬性或其類型的[`TypeConverter`](xref:Xamarin.Forms.TypeConverter)衍生。

但是，從不同的來源設定屬性（例如，字典中的專案、靜態屬性或欄位的值，或從某種類型的計算中），有時會很方便。

這是*XAML 標記延伸*的作業。 儘管名稱，XAML 標記延伸並*不*是 XML 的延伸。 XAML 一律是合法的 XML。

## <a name="the-code-infrastructure"></a>程式碼基礎結構

XAML 標記延伸是一種可執行[`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension)介面的類別。 這類類別通常會在其名稱結尾處加上 `Extension`，但通常會出現在 XAML 中，而不會有該尾碼。

所有 XAML 的執行都支援下列 XAML 標記延伸：

- [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)支援 `x:Static`
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)支援 `x:Reference`
- [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)支援 `x:Type`
- [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)支援 `x:Null`
- [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)支援 `x:Array`

許多 XAML 的執行都支援這四個 XAML 標記延伸，包括 Xamarin. Forms：

- [`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)支援 `StaticResource`
- [`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)支援 `DynamicResource`
- `Binding` 的[`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)&mdash;支援第16章所討論的內容[。資料](chapter16.md)系結
- [`TemplateBindingExtension`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension)不在本書涵蓋的&mdash;`TemplateBinding` 支援

另一個 XAML 標記延伸模組包含在 Xamarin 中，與[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)的連接：

- 本書未涵蓋[`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;

## <a name="accessing-static-members"></a>存取靜態成員

使用[`x:Static`](xref:Xamarin.Forms.Xaml.StaticExtension)元素，將屬性設定為公用靜態屬性、欄位或列舉成員的值。 將[`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member)屬性設定為靜態成員。 在大括弧中指定 `x:Static` 和成員名稱通常會比較容易。 不需要包含 `Member` 屬性的名稱，只是成員本身。 此通用語法會顯示在[**SharedStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics)範例中。 靜態欄位本身會在[`AppConstants`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs)類別中定義。 這項技術可讓您建立透過程式使用的常數。

有了額外的 XML 命名空間宣告，您可以參考 .NET framework 中定義的公用靜態屬性、欄位或列舉成員，如[**SystemStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics)範例中所示。

## <a name="resource-dictionaries"></a>資源字典

`VisualElement` 類別會定義名為[`Resources`](xref:Xamarin.Forms.VisualElement.Resources)的屬性，您可以將其設定為[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)類型的物件。 在 XAML 中，您可以在此字典中儲存專案，並使用 `x:Key` 屬性加以識別。 儲存在資源字典中的專案會在專案的所有參考之間共用。

### <a name="staticresource-for-most-purposes"></a>適用于大部分用途的 StaticResource

在大部分情況下，您會使用[`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)標記延伸來參考資源字典中的專案，如[**ResourceSharing**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing)範例所示。 您可以使用 `StaticResourceExtension` 元素或在大括弧內 `StaticResource`：

[![資源分享的三重螢幕擷取畫面](images/ch10fg03-small.png "資源分享")](images/ch10fg03-large.png#lightbox "資源分享")

請勿混淆 `x:Static` 標記延伸和 `StaticResource` 標記延伸。

### <a name="a-tree-of-dictionaries"></a>字典的樹狀結構

當 XAML 剖析器遇到 `StaticResource`時，它會開始在視覺化樹狀結構中搜尋相符的索引鍵，然後在應用程式的 `App` 類別中尋找 `ResourceDictionary`。 這可讓視覺化樹狀結構中資源字典中的專案更深入地覆寫視覺化樹狀結構中較高的資源字典。 這會在[**ResourceTrees**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees)範例中示範。

### <a name="dynamicresource-for-special-purposes"></a>適用于特殊用途的 DynamicResource

`StaticResource` 標記延伸會在 `InitializeComponent` 呼叫期間建立視覺化樹狀結構時，讓從字典中抓取專案。 `StaticResource` 的替代方法是[`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)，它會維護字典索引鍵的連結，並在索引鍵所參考的專案變更時更新目標。

`StaticResource` 和 `DynamicResource` 之間的差異會在[**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)範例中示範。

`DynamicResource` 所設定的屬性必須由可系結的屬性所支援，如可系結[基礎結構第11章所](chapter11.md)述。

## <a name="lesser-used-markup-extensions"></a>較少使用的標記延伸

使用[`x:Null`](xref:Xamarin.Forms.Xaml.NullExtension)標記延伸模組，將屬性設定為 `null`。

使用[`x:Type`](xref:Xamarin.Forms.Xaml.TypeExtension)標記延伸模組，將屬性設定為 .net `Type` 物件。

使用[`x:Array`](xref:Xamarin.Forms.Xaml.ArrayExtension)定義陣列。 將 [`Type`] 屬性設定為 `x:Type` 標記延伸，以指定陣列成員的類型。

## <a name="a-custom-markup-extension"></a>自訂標記延伸

您可以撰寫使用[`ProvideValue`](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider))方法來執行[`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension)介面的類別，以建立自己的 XAML 標記延伸。

[`HslColorExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs)類別滿足這些需求。 它會根據名為 `H`、`S`、`L`和 `A`的屬性值，建立 `Color` 類型的值。 這個類別是 Xamarin. form 程式庫中名為 FormsBook 的第一個專案，這是在本書的課程中所建立和使用的[**工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)組。

[**CustomExtensionDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo)範例會示範如何參考此程式庫，並使用自訂標記延伸模組。

## <a name="related-links"></a>相關連結

- [第10章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [第10章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
