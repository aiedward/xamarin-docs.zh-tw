---
title: 章 10 的摘要。 XAML 標記延伸
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 3223bc20448bd8354e84dc67ee64a8dc435f7667
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>章 10 的摘要。 XAML 標記延伸

一般來說，XAML 剖析器將任何字串做為屬性值設為基礎的基本.NET 資料類型的標準轉換屬性的型別轉換或[ `TypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverter/)衍生項目附加至屬性或具有其型別[`TypeConverterAttribute`](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverterAttribute/).

但是，有時候會更方便從不同來源，比方說，項目中的字典或值的靜態屬性或欄位，或從某種類型的計算來設定屬性。

這是作業的*XAML 標記延伸*。 500 個 XAML 標記延伸是*不*to XML 擴充功能。 XAML 一律是合法的 XML。

## <a name="the-code-infrastructure"></a>程式碼基礎結構

XAML 標記延伸是實作的類別[ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/)介面。 這種類別通常會有這個字`Extension`其名稱的結尾但通常會顯示在 XAML 中不含該尾碼。

下列 XAML 標記延伸支援 XAML 的所有實作：

- `x:Static` 支援 [`StaticExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/)
- `x:Reference` 支援 [`ReferenceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/)
- `x:Type` 支援 [`TypeExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/)
- `x:Null` 支援 [`NullExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/)
- `x:Array` 支援 [`ArrayExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/)

這些四個 XAML 標記延伸支援許多實作中的 XAML，包括 Xamarin.Forms:

- `StaticResource` 支援 [`StaticResourceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticResourceExtension/)
- `DynamicResource` 支援 [`DynamicResourceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.DynamicResourceExtension/)
- `Binding` 支援[ `BindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/)&mdash;述[第 16 章。資料繫結](#chapter16)
- `TemplateBinding` 支援[ `TemplateBindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TemplateBindingExtension/)&mdash;未涵蓋在活頁簿

其他的 XAML 標記延伸隨附於與 Xamarin.Forms [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/):

- [`ConstraintExpression`](https://developer.xamarin.com/api/type/Xamarin.Forms.ConstraintExpression/)&mdash;未涵蓋在活頁簿

## <a name="accessing-static-members"></a>存取靜態成員

使用[ `x:Static` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/)將屬性設為公用靜態屬性、 欄位或列舉成員值的項目。 設定[ `Member` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.StaticExtension.Member/)靜態成員的屬性。 通常容易指定`x:Static`以及括號中的成員名稱。 名稱`Member`屬性不需要同時包括，只要該成員本身。 這個一般語法如下所示[ **SharedStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics)範例。 中所定義的靜態欄位本身[ `AppConstants` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs)類別。 這項技術可讓您建立透過程式所使用的常數。

使用其他的 XML 命名空間宣告，您可以參考公用靜態屬性、 欄位或列舉型別成員定義在.NET framework 中，如下所示[ **SystemStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics)範例.

## <a name="resource-dictionaries"></a>資源字典

`VisualElement`類別會定義名為的屬性[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) ，您可以設定為型別的物件[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。 在 XAML 中，您可以儲存在字典中的項目，並識別它們與`x:Key`屬性。 儲存在資源字典中的項目所共用項目的所有參考。

### <a name="staticresource-for-most-purposes"></a>StaticResource 大部分的用途

在大部分情況下，您將使用[ `StaticResource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticResourceExtension/)參考從資源字典的項目所示的標記延伸[ **ResourceSharing** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing)範例. 您可以使用`StaticResourceExtension`項目或`StaticResource`在大括號：

[![三個螢幕擷取畫面的資源共用](images/ch10fg03-small.png "資源共用")](images/ch10fg03-large.png#lightbox "資源共用")

請勿混淆`x:Static`標記延伸和`StaticResource`標記延伸。

### <a name="a-tree-of-dictionaries"></a>字典的樹狀結構

當 XAML 剖析器遇到`StaticResource`，它會開始搜尋相符的索引鍵的視覺化樹狀向上並接著會查看`ResourceDictionary`在應用程式的`App`類別。 這允許更深入的視覺化樹狀結構，來覆寫的視覺化樹狀結構中較高的資源字典中的資源字典中的項目。 這示範於[ **ResourceTrees** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees)範例。

### <a name="dynamicresource-for-special-purposes"></a>特殊用途的 DynamicResource

`StaticResource`標記延伸會導致建置期間以視覺化樹狀結構時要從字典中擷取項目`InitializeComponent`呼叫。 替代`StaticResource`是[ `DynamicResource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.DynamicResourceExtension/)，這會維護字典索引鍵的連結和項目參考的金鑰變更時，更新目標。

之間的差異`StaticResource`和`DynamicResource`示範[ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)範例。

屬性，來設定`DynamicResource`必須受支援可繫結屬性中所述[第 11 章可繫結的基礎結構](chapter11.md)。

## <a name="lesser-used-markup-extensions"></a>使用較小的標記延伸

使用[ `x:Null` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/)標記延伸，將屬性設定為`null`。

使用[ `x:Type` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/)標記延伸，將屬性設定為.NET`Type`物件。

使用[ `x:Array` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/)來定義陣列。 指定陣列成員的類型，藉由設定 [`Type`] 屬性，以`x:Type`標記延伸。

## <a name="a-custom-markup-extension"></a>自訂標記延伸

您可以透過撰寫實作的類別來建立您自己的 XAML 標記延伸[ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/)介面[ `ProvideValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue/p/System.IServiceProvider/)方法。

[ `HslColorExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs)類別就可滿足這些需求。 它會建立型別的值`Color`根據名為屬性的值`H`， `S`， `L`，和`A`。 這個類別是名為 Xamarin.Forms 文件庫中的第一個項目[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) ，是建立和使用本書的過程。

[ **CustomExtensionDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo)範例示範如何參考此文件庫及使用自訂標記延伸。



## <a name="related-links"></a>相關連結

- [章 10 全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [章 10 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
