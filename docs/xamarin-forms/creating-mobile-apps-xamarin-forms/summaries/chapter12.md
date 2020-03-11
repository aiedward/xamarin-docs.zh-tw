---
title: 第12章的摘要。 樣式
description: 使用 Xamarin 建立 Mobile Apps：第12章的摘要。 樣式
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 408f171a3c7c690b700f7be21a3dcaff503467d9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "65926905"
---
# <a name="summary-of-chapter-12-styles"></a>第12章的摘要。 樣式

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

在 [Xamarin] 表單中，樣式允許多個視圖共用屬性設定的集合。 這會減少標記，並讓您維持一致的視覺效果主題。

在標記中，幾乎一律會定義和使用樣式。 [`Style`](xref:Xamarin.Forms.Style)類型的物件會在資源字典中具現化，然後使用 `StaticResource` 或 `DynamicResource` 標記延伸，設定為視覺元素的[`Style`](xref:Xamarin.Forms.NavigableElement.Style)屬性。

## <a name="the-basic-style"></a>基本樣式

`Style` 需要將其[`TargetType`](xref:Xamarin.Forms.Style.TargetType)設定為其適用之視覺物件的型別。 當 `Style` 在資源字典中具現化時（如一般），它也需要 `x:Key` 屬性。

`Style` 具有[`Setters`](xref:Xamarin.Forms.Style.Setters)類型的 content 屬性，這是[`Setter`](xref:Xamarin.Forms.Setter)物件的集合。 每個 `Setter` 會將[`Property`](xref:Xamarin.Forms.Setter.Property)與[`Value`](xref:Xamarin.Forms.Setter.Value)產生關聯。

在 XAML 中，`Property` 設定是 CLR 屬性的名稱（例如 `Button`的 `Text` 屬性），但樣式屬性必須由可系結的屬性支援。 此外，屬性必須在 `TargetType` 設定所指示的類別中定義，或由該類別繼承。

您可以使用 property 元素 `<Setter.Value>`來指定 `Value` 設定。 這可讓您將 `Value` 設定為無法以文字字串或 `OnPlatform` 物件表示的物件，或設為使用 `x:Arguments` 或 `x:FactoryMethod`具現化的物件。 您也可以使用 `StaticResource` 運算式，將 `Value` 屬性設定為字典中的另一個專案。

[**BasicStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle)程式會示範基本語法，並說明如何使用 `StaticResource` 標記延伸來參考 `Style`：

[![基本樣式的三向螢幕擷取畫面](images/ch12fg01-small.png "基本樣式")](images/ch12fg01-large.png#lightbox "基本樣式")

`Style` 物件以及在 `Style` 物件中建立為 `Value` 設定的任何物件，都會在參考該 `Style`的所有視圖之間共用。 `Style` 不能包含任何無法共用的專案，例如 `View` 的衍生專案。

無法在 `Style`中設定事件處理常式。 無法在 `Style` 中設定 `GestureRecognizers` 屬性，因為它不是由可系結屬性所支援。

## <a name="styles-in-code"></a>程式碼中的樣式

雖然這並不常見，但您可以在程式碼中具現化和初始化 `Style` 物件。 這是由[**BasicStyleCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode)範例所示範。

## <a name="style-inheritance"></a>樣式繼承

`Style` 具有[`BasedOn`](xref:Xamarin.Forms.Style.BasedOn)屬性，您可以將它設定為參考另一個樣式的 `StaticResource` 標記延伸。 這可讓樣式繼承自先前的樣式，並加入或取代屬性設定。 [**StyleInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance)範例會示範這種情況。

如果 `Style2` 是以 `Style1`為基礎，則 `Style2` 的 `TargetType` 必須與 `Style1` 或衍生自 `Style1`相同。 儲存 `Style1` 的資源字典，必須與視覺化樹狀結構中較高 `Style2` 或資源字典的資源字典相同。

## <a name="implicit-styles"></a>隱含樣式

如果資源字典中的 `Style` 沒有 `x:Key` 屬性設定，則會自動指派字典索引鍵，而 `Style` 物件會成為*隱含樣式*。 沒有 `Style` 設定且其類型符合 `TargetType` 的視圖，會找到該樣式，如[**ImplicitStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle)範例所示。

隱含樣式可以衍生自具有 `x:Key` 設定的 `Style`，而不是相反的方式。 您無法明確參考隱含樣式。

您可以使用樣式和 `BasedOn`來執行三種類型的階層：

- 從 `Application` 上定義的樣式，以及向下 `Page` 至視覺化樹狀結構中較低的版面配置所定義的樣式。
- 從為基類定義的樣式（例如 `VisualElement` 和 `View`，到為特定類別定義的樣式。
- 從具有明確字典索引鍵的樣式到隱含樣式。

這些階層會在[**StyleHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy)範例中示範。

## <a name="dynamic-styles"></a>動態樣式

資源字典中的樣式可由 `DynamicResource` 參考，而不是 `StaticResource`。 這會使樣式成為*動態樣式*。 如果在資源字典中使用相同的索引鍵來取代該樣式，則參考該樣式的視圖 `DynamicResource` 會自動變更。 此外，缺少具有指定之索引鍵的字典專案，會導致 `StaticResource` 引發例外狀況，但不會 `DynamicResource`。

您可以使用這項技術，以動態方式變更樣式或主題，如[**DynamicStyles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles)範例所示。

不過，您無法將 `BasedOn` 屬性設定為 `DynamicResource` 組成延伸模組，因為 `BasedOn` 不受可系結屬性支援。 若要動態衍生樣式，請勿設定 `BasedOn`。 相反地，請將[`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey)屬性設定為您要衍生自之樣式的字典索引鍵。 [**DynamicStylesInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh)範例會示範這項技術。

## <a name="device-styles"></a>裝置樣式

[`Device.Styles`](xref:Xamarin.Forms.Device.Styles)的 nested 類別會針對六個樣式定義十二個靜態唯讀欄位，其中包含可用於常用文字使用類型的 `Label` `TargetType`。

其中六個欄位屬於類型 `Style` 您可以直接在程式碼中設定 `Style` 屬性：

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

其他六個欄位的類型是 `string`，而且可以當做動態樣式的字典索引鍵使用：

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey)等於 "BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey)等於 "TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey)等於 "SubtitleStyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey)等於 "CaptionStyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey)等於 "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey)等於 "ListItemDetailTextStyle"

這些樣式是由[**DeviceStylesList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList)範例所說明。

## <a name="related-links"></a>相關連結

- [第12章的全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [第12章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
