---
title: 第 10 章的摘要。 XAML 標記延伸
description: 使用 Xamarin.Forms 建立行動應用程式： 第 10 章的摘要。 XAML 標記延伸
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: charlespetzold
ms.author: chape
ms.date: 07/19/2018
ms.openlocfilehash: 74f7e2846a9e8d8390a8322c57db0845718bbba7
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156999"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>第 10 章的摘要。 XAML 標記延伸

一般而言，XAML 剖析器會將轉換做為屬性值設定為基本的.NET 資料類型的標準轉換為基礎的屬性類型的任何字串或[ `TypeConverter` ](xref:Xamarin.Forms.TypeConverter)衍生項目附加至屬性或具有其類型[`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute).

但是，可能會很方便地從不同來源，比方說，在字典中或值的靜態屬性或欄位中，項目，或從某種類型的計算來設定屬性。

這是工作*XAML 標記延伸*。 儘管有此名稱，是 XAML 標記延伸*不*XML 的延伸模組。 XAML 永遠是合法的 XML。

## <a name="the-code-infrastructure"></a>程式碼基礎結構

XAML 標記延伸是實作的類別[ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension)介面。 這種類別通常會有這個字`Extension`其名稱的結尾但通常會出現在 XAML 中沒有該尾碼。

所有實作的 XAML 都支援下列 XAML 標記延伸模組：

- `x:Static` 支援 [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference` 支援 [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type` 支援 [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null` 支援 [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array` 支援 [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

這些四個 XAML 標記延伸模組支援的 XAML，包括 Xamarin.Forms 的許多實作：

- `StaticResource` 支援 [`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource` 支援 [`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding` 受到[ `BindingExtension` ](xref:Xamarin.Forms.Xaml.BindingExtension)&mdash;所述[第 16 章。資料繫結](#chapter16)
- `TemplateBinding` 受到[ `TemplateBindingExtension` ](xref:Xamarin.Forms.Xaml.TemplateBindingExtension)&mdash;未涵蓋在活頁簿

額外的 XAML 標記延伸模組包含在與 Xamarin.Forms [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout):

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;未涵蓋在活頁簿

## <a name="accessing-static-members"></a>存取靜態成員

使用[ `x:Static` ](xref:Xamarin.Forms.Xaml.StaticExtension)將屬性設定為公用靜態屬性、 欄位或列舉成員值的項目。 設定[ `Member` ](xref:Xamarin.Forms.Xaml.StaticExtension.Member)靜態成員的屬性。 通常很容易指定`x:Static`和大括號中的成員名稱。 名稱`Member`屬性不一定要包含在內，只要該成員本身。 此常見的語法所示[ **SharedStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics)範例。 中定義的靜態欄位本身[ `AppConstants` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs)類別。 這項技術可讓您建立透過程式所使用的常數。

使用其他的 XML 命名空間宣告，您可以參考公用靜態屬性、 欄位或列舉成員定義在.NET framework 中，如所示[ **SystemStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics)範例.

## <a name="resource-dictionaries"></a>資源字典

`VisualElement`類別會定義名為的屬性[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources)您可以設定物件的型別[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 內 XAML，您可以儲存在這個字典中的項目，並找出它們與`x:Key`屬性。 資源字典中儲存的項目所共用之項目的所有參考。

### <a name="staticresource-for-most-purposes"></a>StaticResource 大多數的用途

在大部分情況下，您將使用[ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension)若要從資源字典中，參考項目，如所示的標記延伸[ **ResourceSharing** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing)範例. 您可以使用`StaticResourceExtension`項目或`StaticResource`大括弧括住：

[![資源共用的三個螢幕擷取畫面](images/ch10fg03-small.png "資源共用")](images/ch10fg03-large.png#lightbox "資源共用")

請勿混淆`x:Static`標記延伸和`StaticResource`標記延伸。

### <a name="a-tree-of-dictionaries"></a>字典的樹狀結構

當 XAML 剖析器遇到`StaticResource`，它會開始搜尋的相符索引鍵，在視覺樹狀結構，並接著會查看`ResourceDictionary`應用程式的`App`類別。 這允許更深入的視覺化樹狀結構，來覆寫在視覺化樹狀結構中較高的資源字典中的資源字典中的項目。 這示範於[ **ResourceTrees** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees)範例。

### <a name="dynamicresource-for-special-purposes"></a>特殊用途的 DynamicResource

`StaticResource`標記延伸模組會導致項目期間建置視覺化樹狀結構時，要從字典擷取`InitializeComponent`呼叫。 替代`StaticResource`已[ `DynamicResource` ](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)，其中會維護字典索引鍵的連結，並更新目標項目參考的索引鍵的變更時。

之間的差異`StaticResource`並`DynamicResource`所示[ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)範例。

所設定的屬性`DynamicResource`都必須受可繫結屬性中所述[第 11 章可繫結的基礎結構](chapter11.md)。

## <a name="lesser-used-markup-extensions"></a>較少使用的標記延伸

使用[ `x:Null` ](xref:Xamarin.Forms.Xaml.NullExtension)屬性設定為標記延伸`null`。

使用[ `x:Type` ](xref:Xamarin.Forms.Xaml.TypeExtension)標記延伸模組，將屬性設定為.NET`Type`物件。

使用[ `x:Array` ](xref:Xamarin.Forms.Xaml.ArrayExtension)定義陣列。 指定陣列成員的類型，藉由設定 [`Type`] 屬性設`x:Type`標記延伸。

## <a name="a-custom-markup-extension"></a>自訂標記延伸

您可以建立自己的 XAML 標記延伸模組撰寫類別，可實作[ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension)與互動[ `ProvideValue` ](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider))方法。

[ `HslColorExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs)類別可滿足這些需求。 它會建立類型的值`Color`名為屬性的值為基礎`H`， `S`， `L`，和`A`。 這個類別是名為的 Xamarin.Forms 程式庫中的第一個項目[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)打造且使用過去一本書。

[ **CustomExtensionDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo)範例示範如何參考此文件庫，並使用自訂標記延伸模組。

## <a name="related-links"></a>相關連結

- [第 10 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [第 10 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
