---
title: 第 12 章的摘要。 樣式
description: 使用 Xamarin.Forms 建立行動應用程式： 第 12 章摘要。 樣式
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: charlespetzold
ms.author: chape
ms.date: 07/19/2018
ms.openlocfilehash: 55168f8f401cca711422dc49027b2b055fba5f1f
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526750"
---
# <a name="summary-of-chapter-12-styles"></a>第 12 章的摘要。 樣式

在 Xamarin.Forms 中，樣式可讓多個檢視，以共用屬性設定的集合。 這可減少標記，並可讓您維護一致的視覺化佈景主題。

幾乎一律定義和使用標記樣式。 型別的物件[ `Style` ](xref:Xamarin.Forms.Style)並具現化資源字典中則設為[ `Style` ](xref:Xamarin.Forms.VisualElement.Style)視覺項目使用的屬性`StaticResource`或`DynamicResource`標記延伸模組。

## <a name="the-basic-style"></a>基本的樣式

A`Style`要求其[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)設定適用於在視覺物件的類型。 當`Style`具現化資源字典 （亦即） 中也需要`x:Key`屬性。

`Style`具有 content 屬性的型別[ `Setters` ](xref:Xamarin.Forms.Style.Setters)，這是一堆[ `Setter` ](xref:Xamarin.Forms.Setter)物件。 每個`Setter`產生關聯[ `Property` ](xref:Xamarin.Forms.Setter.Property)具有[ `Value` ](xref:Xamarin.Forms.Setter.Value)。

在 XAML`Property`設定是 CLR 屬性的名稱 (例如`Text`屬性`Button`) 但已設定樣式的屬性都必須受可繫結的屬性。 此外，必須定義屬性中所指定`TargetType`設定，或該類別所繼承。

您可以指定`Value`設定的屬性項目`<Setter.Value>`。 這可讓您設定`Value`物件中文字字串，而無法表示`OnPlatform`物件，或物件使用具現化`x:Arguments`或`x:FactoryMethod`。 `Value`屬性也可以設定與`StaticResource`字典中的另一個項目的運算式。

[ **BasicStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle)程式示範基本的語法，並示範如何參考`Style`使用`StaticResource`標記延伸模組：

[![基本的樣式的三個螢幕擷取畫面](images/ch12fg01-small.png "基本樣式")](images/ch12fg01-large.png#lightbox "基本的樣式")

`Style`物件和中建立的任何物件`Style`物件做`Value`參考的所有檢視之間共用設定`Style`。 `Style`不能包含任何項目不能共用，例如`View`衍生項目。

無法在中設定事件處理常式`Style`。 `GestureRecognizers`無法設定屬性，`Style`因為它不支援可繫結的屬性。

## <a name="styles-in-code"></a>在程式碼中的樣式

雖然不常見，但您可以具現化並初始化`Style`程式碼中的物件。 最好的證明[ **BasicStyleCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode)範例。

## <a name="style-inheritance"></a>樣式繼承

`Style` 已[ `BasedOn` ](xref:Xamarin.Forms.Style.BasedOn)屬性，您可以設定為`StaticResource`標記延伸參考另一種樣式。 這可讓繼承自先前的樣式，以及新增或取代屬性設定的樣式。 [ **StyleInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance)為範例。

如果`Style2`為基礎`Style1`，則`TargetType`的`Style2`必須是相同`Style1`或衍生自`Style1`。 資源字典所在`Style1`儲存必須是相同的資源字典，做為`Style2`或視覺化樹狀結構中較高的資源字典。

## <a name="implicit-styles"></a>隱含樣式

如果`Style`在資源字典沒有`x:Key`屬性設定，它字典索引鍵自動指派，而`Style`物件會變成*隱含樣式*。 檢視不含`Style`設定，並符合其型別`TargetType`完全可找到該樣式中，名為[ **ImplicitStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle)範例會示範。

隱含樣式可以衍生自`Style`與`x:Key`設定，但不是利用其他方式。 您無法明確參考隱含樣式。

您可以實作三種類型的階層，具有樣式和`BasedOn`:

- 從上定義的樣式`Application`和`Page`到視覺化樹狀結構中較低的版面配置上定義的樣式。
- 從這類基底類別定義的樣式`VisualElement`和`View`針對特定的類別定義的樣式。
- 從使用明確的字典索引鍵隱含樣式的樣式。

這些階層會示範[ **StyleHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy)範例。

## <a name="dynamic-styles"></a>動態樣式

您可以參考的資源字典中的樣式`DynamicResource`而非`StaticResource`。 這可讓樣式*動態樣式*。 如果該樣式會取代資源字典中的另一種樣式相同的索引鍵，參考具有該樣式的檢視`DynamicResource`自動變更。 此外，不存在具有指定索引鍵的字典項目將會造成`StaticResource`引發例外狀況，但不是`DynamicResource`。

您可以動態變更樣式或佈景主題為使用這項技術[ **DynamicStyles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles)範例會示範。

不過，您無法設定`BasedOn`屬性，以`DynamicResource`結構的擴充功能因為`BasedOn`不支援可繫結的屬性。 若要以動態方式衍生的樣式，請勿設定`BasedOn`。 相反地，設定[ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey)至您想要衍生自樣式的字典索引鍵的屬性。 [ **DynamicStylesInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh)範例會示範這項技術。

## <a name="device-styles"></a>裝置樣式

[ `Device.Styles` ](xref:Xamarin.Forms.Device.Styles)巢狀的類別定義十二個靜態唯讀欄位具有六個樣式`TargetType`的`Label`，您可以使用常見的文字使用方式。

六個欄位都屬於型別`Style`您可以直接設定`Style`在程式碼中的屬性：

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

其他六個欄位都屬於型別`string`和可用來當做動態樣式的字典索引鍵：

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey) 等於"BodyStyle 」
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey) 等於"TitleStyle 」
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey) 等於"SubtitleStyle 」
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey) 等於"CaptionStyle 」
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey) 等於"ListItemTextStyle 」
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey) 等於"ListItemDetailTextStyle 」

說明這些樣式[ **DeviceStylesList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList)範例。

## <a name="related-links"></a>相關連結

- [第 12 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [第 12 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
