---
title: 第12章摘要。 樣式
description: 使用 Xamarin.表單創建行動應用程式:第 12 章摘要。 樣式
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 408f171a3c7c690b700f7be21a3dcaff503467d9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "65926905"
---
# <a name="summary-of-chapter-12-styles"></a>第12章摘要。 樣式

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

在 Xamarin.Forms 中,樣式允許多個檢視共用屬性設置的集合。 這減少了標記,並能夠保持一致的可視主題。

樣式幾乎總是在標記中定義和使用。 類型[`Style`](xref:Xamarin.Forms.Style)物件在資源字典中實例化,然後[`Style`](xref:Xamarin.Forms.NavigableElement.Style)`StaticResource`使用`DynamicResource`或標記擴展設置為可視元素的屬性。

## <a name="the-basic-style"></a>基本樣式

要求`Style`將其[`TargetType`](xref:Xamarin.Forms.Style.TargetType)設置為它應用於的可視對象的類型。 在資源`Style`字典中實例化 時(如常見),它還需要`x:Key`一個 屬性。

具有`Style`類型[`Setters`](xref:Xamarin.Forms.Style.Setters)的內容屬性,它是[`Setter`](xref:Xamarin.Forms.Setter)物件的集合。 每個`Setter`使用[`Property`](xref:Xamarin.Forms.Setter.Property)的[`Value`](xref:Xamarin.Forms.Setter.Value)與關聯 。

在 XAML`Property`中,設定是 CLR 屬性`Text``Button`的名稱(如的屬性),但樣式屬性必須由可綁定屬性備份。 此外,屬性必須在`TargetType`設置指示的類中定義,或者由該類繼承。

可以使用 屬性`Value``<Setter.Value>`元素 指定設置。 `Value`這允許您設定為無法在文字字串中表示的物件`OnPlatform`、物件,或者設置為使用`x:Arguments``x:FactoryMethod`或 實例化的物件。 也可以`Value`使用表達式設置該屬性到字典`StaticResource`中的另一個項。

[**BasicStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle)程式演示了基本語法,並演示`Style`如何`StaticResource`引用 標記延伸:

[![基本樣式的三重螢幕截圖](images/ch12fg01-small.png "基本樣式")](images/ch12fg01-large.png#lightbox "基本樣式")

物件`Style`和`Style`在 物件中`Value`創建為 設置的任何物件`Style`在引用該 的所有檢視之間共用。 `Style`不能包含任何無法共用的項,如`View`派生。

無法在中設定事件處理程式`Style`。 無法`GestureRecognizers`在 中設置`Style`該屬性 ,因為它不受可綁定屬性的支援。

## <a name="styles-in-code"></a>代碼中的樣式

儘管這並不常見,但您可以在代碼中實例化和初始化`Style`物件。 [**基本樣式代碼**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode)示例演示了這一點。

## <a name="style-inheritance"></a>樣式繼承

`Style`具有一[`BasedOn`](xref:Xamarin.Forms.Style.BasedOn)個屬性,可以設置為引用`StaticResource`另 一種樣式的標記擴展。 這允許樣式從以前的樣式繼承,並添加或替換屬性設置。 [**樣式繼承**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance)示例演示了這一點。

如果`Style2``Style1``TargetType`,`Style2`必須與`Style1`或 衍生的`Style1`手生 。 存儲的資源字典`Style1`必須與可視化樹中較高的資源字`Style2`典 或資源字典相同。

## <a name="implicit-styles"></a>隱式樣式

如果`Style`資源字典中`x:Key`沒有 屬性設定,則會自動為其分配字典金鑰`Style`,並且物件將成為*隱式樣式*。 沒有`Style`設置且其類型`TargetType`與 該樣式匹配的視圖將找到該樣式,如[**隱式樣式**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle)範例所示。

隱式樣式可以派生自`Style`具有`x:Key`設置 的 ,但不能派生到相反。 不能顯式引用隱式樣式。

您可以使用 樣式`BasedOn`和 實用三種類型的層次結構:

- 從 在`Application``Page`和 下定義樣式到在可視化樹中較低的佈局上定義的樣式。
- 從為基類定義的樣式,如`VisualElement``View`和到為特定類定義的樣式。
- 從具有顯式字典鍵的樣式到隱式樣式。

這些層次結構在[**樣式層次結構**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy)示例中演示。

## <a name="dynamic-styles"></a>動態樣式

資源字典中的樣式可以引用`DynamicResource``StaticResource`而不是引用。 這使得樣式成為*動態樣式*。 如果在資源字典中用同一鍵替換該樣式,則引用該樣式的`DynamicResource`視圖將自動更改。 此外,缺少具有指定鍵的字典條目將導致`StaticResource`引發異常,但不會`DynamicResource`引發異常。

您可以使用此技術動態更改樣式或主題,動態[**樣式**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles)範例示範。

但是,無法將`BasedOn`屬性設置為化妝擴展`DynamicResource`名`BasedOn`,因為 該屬性不受可綁定屬性的支援。 要動態派生樣式,請不要設定`BasedOn`。 相反,將[`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey)屬性設置為要派生的樣式的字典鍵。 [**動態樣式繼承**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh)示例演示了此技術。

## <a name="device-styles"></a>裝置樣式

巢狀[`Device.Styles`](xref:Xamarin.Forms.Device.Styles)類別為六種樣式定義十二個靜態唯讀欄位,`TargetType``Label`其中一個可用於常見類型的文本用法。

其中六個字段的類型`Style`,可以直接設置為代碼`Style`中 的屬性:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

其他六個字段的類型`string`,可用作動態樣式的字典鍵:

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey)等於"身體風格"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey)等於"標題樣式"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey)等於"字幕樣式"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey)等於"標題樣式"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey)等於"清單項目文本樣式"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey)等於"清單項目細節文本樣式"

設備[**樣式清單**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList)示例說明了這些樣式。

## <a name="related-links"></a>相關連結

- [第12章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [第12章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
