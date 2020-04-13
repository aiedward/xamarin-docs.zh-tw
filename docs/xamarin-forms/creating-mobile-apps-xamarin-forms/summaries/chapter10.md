---
title: 第10章摘要。 XAML 標記延伸
description: 使用 Xamarin.表單創建行動應用程式:第 10 章摘要。 XAML 標記延伸
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 076e9f5155492e5a69d906c587b24495fe39d3f1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334281"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>第10章摘要。 XAML 標記延伸

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

通常,XAML 解析器根據基本 .NET 資料類型的標準轉換,或附加到屬性或[`TypeConverter`](xref:Xamarin.Forms.TypeConverter)其類型中的 派生器,將任何字串集作為屬性值轉換為[`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute)屬性類型。

但是,有時從其他源(例如,字典中的項、靜態屬性或欄位的值或從某種計算中設置屬性)會很方便。

這是*XAML 標記擴展*的作業。 儘管名稱,XAML 標記擴展*不是*XML 的擴展。 XAML 始終是合法的 XML。

## <a name="the-code-infrastructure"></a>代碼基礎結構

XAML 標記擴展是實現介面[`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension)的 類。 此類通常在其名稱的末尾具有`Extension`單詞,但通常出現在沒有後綴的 XAML 中。

XAML 的所有支援都支援以下 XAML 標籤延伸:

- `x:Static`支援[`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference`支援[`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type`支援[`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null`支援[`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array`支援[`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

這四個 XAML 標籤延伸受 XAML 的許多實作的支援,包括 Xamarin.Forms:

- `StaticResource`支援[`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource`支援[`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding`[`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)第 16 章對此進行討論&mdash;[。資料繫結](chapter16.md)
- `TemplateBinding`[`TemplateBindingExtension`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension)&mdash;

與 Xamarin.表單包含額外的 XAML 標籤[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)延伸, 與

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;書裡未涵蓋

## <a name="accessing-static-members"></a>存取靜態成員

使用[`x:Static`](xref:Xamarin.Forms.Xaml.StaticExtension)元素將屬性設置為公共靜態屬性、欄位或枚舉成員的值。 將[`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member)屬性設置為靜態成員。 通常更容易指定`x:Static`成員名稱,成員名稱以大括弧表示。 屬性的名稱`Member`不需要包含,只需要成員本身。 這個常見語法顯示在[**共享靜態範例中**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics)。 靜態欄位本身在類中[`AppConstants`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs)定義。 此技術允許您建立通過程式使用的常量。

使用額外的 XML 命名空間聲明,可以引用 .NET 框架中定義的公共靜態屬性、欄位或枚舉成員,如[**SystemStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics)範例所示。

## <a name="resource-dictionaries"></a>資源字典

類別`VisualElement`定義一個名為的屬性[`Resources`](xref:Xamarin.Forms.VisualElement.Resources),您可以設定為類型的物件。 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 在 XAML 中,您可以在此字典中儲存項,`x:Key`並標識 屬性。 存儲在資源字典中的項在對該專案的所有引用之間共用。

### <a name="staticresource-for-most-purposes"></a>用於大多數目的的靜態資源

在大多數情況下,[`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)您將使用標記擴展來引用資源字典中的項,如[**資源分享**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing)範例所示。 您可以使用元素`StaticResourceExtension`或`StaticResource`在大括號內:

[![資源分享的三重螢幕截圖](images/ch10fg03-small.png "資源共用")](images/ch10fg03-large.png#lightbox "資源共用")

不要混淆`x:Static`標記擴展`StaticResource`和 標記擴展。

### <a name="a-tree-of-dictionaries"></a>字典樹

當 XAML 解析`StaticResource`器遇到 時,它開始搜尋視覺化樹來尋找符合的鍵,然後在應用程式`ResourceDictionary``App`的類別中尋找 。 這允許在可視化樹中更深的資源字典中的項覆蓋可視化樹中較高的資源字典。 這在[**資源樹**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees)示例中演示。

### <a name="dynamicresource-for-special-purposes"></a>為特殊用途的動態資源

標記`StaticResource`擴展`InitializeComponent`在調用期間生成可視化樹時,會導致從字典中檢索項。 的替代`StaticResource`方法[`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)是 ,它維護指向字典密鑰的連結,並在密鑰引用的項發生更改時更新目標。

和`DynamicResource`之間的差異`StaticResource`在[**DynamicVStatic 範例中**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)演示。

所`DynamicResource`設置的屬性必須由可綁定屬性支援[,如第 11 章"可綁定基礎結構](chapter11.md)"中所述。

## <a name="lesser-used-markup-extensions"></a>使用較少的標記延伸

使用[`x:Null`](xref:Xamarin.Forms.Xaml.NullExtension)標記延伸設定值值`null`。

使用[`x:Type`](xref:Xamarin.Forms.Xaml.TypeExtension)標記擴展將屬性設置為`Type`.NET 物件。

用於[`x:Array`](xref:Xamarin.Forms.Xaml.ArrayExtension)定義陣列。 通過將 [`Type`]`x:Type`屬性設置為 標記擴展,指定陣列成員的類型。

## <a name="a-custom-markup-extension"></a>自訂標記延伸

可以通過編寫[`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension)[`ProvideValue`](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider))使用 方法實現介面的類來創建自己的 XAML 標記擴展。

類[`HslColorExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs)滿足這些要求。 它基於`Color`名為`H``S`、和`L``A`的屬性的值創建類型值。 此類是 Xamarin.Forms 庫中名為[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)的第一個專案,在本書過程中構建和使用。

[**自定義延伸示範**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo)範例展示如何引用此庫並使用自訂標記擴展。

## <a name="related-links"></a>相關連結

- [第十章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [第10章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
