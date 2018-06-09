---
title: 第 12 的摘要。 樣式
description: 使用 Xamarin.Forms 建立行動裝置應用程式： 摘要第 12 個。 樣式
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: d69c6c18a28c89742b186474656ee666b1e6a0ee
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241672"
---
# <a name="summary-of-chapter-12-styles"></a>第 12 的摘要。 樣式

透過 Xamarin.Forms，在樣式可讓多個檢視共用屬性設定的集合。 這可減少標記，並可讓維護一致的視覺化佈景主題。

幾乎都定義和使用中標記樣式。 型別的物件[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)是具現化的資源字典中，然後設[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)視覺項目使用的屬性`StaticResource`或`DyanamicResource`標記延伸模組。

## <a name="the-basic-style"></a>基本的樣式

A`Style`要求其[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)設定適用於視覺物件的類型。 當`Style`具現化資源字典中 （如同常見） 也需要`x:Key`屬性。

`Style`具有內容屬性的型別[ `Setters` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Setters/)，這是集合的[ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)物件。 每個`Setter`關聯[ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/)與[ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/)。

在 XAML 中`Property`設定是 CLR 屬性的名稱 (例如`Text`屬性`Button`)，但可繫結的屬性必須在支援樣式的屬性。 此外，必須定義屬性中所指定的類別`TargetType`設定，或該類別所繼承。

您可以指定`Value`使用屬性項目設定`<Setter.Value>`。 這可讓您設定`Value`的物件，在文字字串中，或者無法表示`OnPlatform`物件，或物件具現化使用`x:Arguments`或`x:FactoryMethod`。 `Value`屬性也可以設定與`StaticResource`運算式字典中的另一個項目。

[ **BasicStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle)程式示範基本語法，並示範如何參考`Style`與`StaticResource`標記延伸：

[![基本樣式的三個螢幕擷取畫面](images/ch12fg01-small.png "基本樣式")](images/ch12fg01-large.png#lightbox "基本樣式")

`Style`物件和中建立任何物件`Style`物件當做`Value`設定所參考的所有檢視表共用`Style`。 `Style`不能包含任何項目不能共用，例如`View`衍生項目。

無法設定事件處理常式中`Style`。 `GestureRecognizers`中無法設定屬性`Style`因為不受可繫結的屬性。

## <a name="styles-in-code"></a>程式碼中的樣式

雖然不常見，但您可以具現化，並初始化`Style`程式碼中的物件。 這示範[ **BasicStyleCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode)範例。

## <a name="style-inheritance"></a>樣式繼承

`Style` 具有[ `BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/)屬性可以設為`StaticResource`參考另一個樣式的標記延伸。 這可讓繼承自上一個樣式，以及新增或取代屬性設定的樣式。 [ **StyleInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance)範例為其示範。

如果`Style2`根據`Style1`、`TargetType`的`Style2`必須是相同`Style1`或衍生自`Style1`。 中的資源字典`Style1`儲存必須是相同的資源字典，因為`Style2`或視覺化樹狀結構中較高的資源字典。

## <a name="implicit-styles"></a>隱含的樣式

如果`Style`在資源字典沒有`x:Key`屬性設定，它會自動指派的字典索引鍵和`Style`物件變成*隱含樣式*。 檢視不含`Style`設定和其類型符合`TargetType`完全可找到該樣式中，名為[ **ImplicitStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle)範例將示範如何。

隱含樣式可以衍生自`Style`與`x:Key`設定，但不是反過來。 您無法明確參考隱含樣式。

您可以實作三種類型的階層，具有樣式和`BasedOn`:

- 從定義樣式`Application`和`Page`向視覺化樹狀結構中較低的版面配置上定義的樣式。
- 從這類的基底類別定義的樣式`VisualElement`和`View`針對特定的類別定義的樣式。
- 從具有明確的字典索引鍵隱含樣式的樣式。

在中示範這些階層[ **StyleHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy)範例。

## <a name="dynamic-styles"></a>動態樣式

您可以參考的資源字典中的樣式`DynamicResource`而不是`StaticResource`。 這可讓樣式*動態樣式*。 如果該樣式所取代的資源字典樣式另一個具有相同索引鍵，參考具有該樣式的檢視`DynamicResource`自動變更。 此外，不存在具有指定索引鍵的字典項目將會造成`StaticResource`引發例外狀況，但不是`DynamicResource`。

您可以使用這項技術來動態變更樣式或佈景主題為[ **DynamicStyles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles)範例將示範如何。

不過，您無法設定`BasedOn`屬性`DynamicResource`架構擴充功能因為`BasedOn`不支援可繫結的屬性。 若要以動態方式衍生樣式，請勿設定`BasedOn`。 相反地，設定[ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/)至您想要衍生自樣式的字典索引鍵的屬性。 [ **DynamicStylesInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh)範例會示範這項技術。

## <a name="device-styles"></a>裝置樣式

[ `Device.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)巢狀的類別定義 12 個靜態唯讀欄位的六種樣式的`TargetType`的`Label`，您可以使用文字使用方式的一般類型。

這些欄位的六個屬於類型`Style`，您可以設定直接`Style`程式碼中的屬性：

- [`BodyStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/)
- [`TitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyle/)
- [`SubtitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyle/)
- [`CaptionStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyle/)
- [`ListItemTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyle/)
- [`ListItemDetailTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyle/)

其他六個欄位都屬於型別`string`，可以用做動態樣式的字典索引鍵：

- [`BodyStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyleKey/) 等於"BodyStyle"
- [`TitleStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyleKey/) 等於"TitleStyle"
- [`SubtitleStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyleKey/) 等於"SubtitleStyle"
- [`CaptionStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyleKey/) 等於"CaptionStyle"
- [`ListItemTextStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyleKey/) 等於"ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyleKey/) 等於"ListItemDetailTextStyle"

這些樣式說明[ **DeviceStylesList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList)範例。



## <a name="related-links"></a>相關連結

- [第 12 全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [第 12 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
