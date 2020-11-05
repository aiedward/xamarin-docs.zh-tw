---
title: 第12章的摘要。 樣式
description: 使用：第 Xamarin.Forms 12 章的摘要建立 Mobile Apps。 樣式
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2f4fedcae3942f1662ec5102ae6c42191f65f888
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374858"
---
# <a name="summary-of-chapter-12-styles"></a>第12章的摘要。 樣式

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

在中 Xamarin.Forms ，樣式可讓多個視圖共用屬性設定的集合。 這會減少標記，並讓您維持一致的視覺化主題。

樣式幾乎一律會在標記中定義和使用。 型別的物件 [`Style`](xref:Xamarin.Forms.Style) 會在資源字典中具現化，然後 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 使用 `StaticResource` 或標記延伸，設定為視覺專案的屬性 `DynamicResource` 。

## <a name="the-basic-style"></a>基本樣式

`Style`需要將其 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 設定為適用的視覺物件類型。 當在 `Style` 資源字典中具現化時 (為通用) 它也需要 `x:Key` 屬性。

`Style`具有類型的 content 屬性 [`Setters`](xref:Xamarin.Forms.Style.Setters) ，它是物件的集合 [`Setter`](xref:Xamarin.Forms.Setter) 。 每個都會 `Setter` 將 a 與建立關聯 [`Property`](xref:Xamarin.Forms.Setter.Property) [`Value`](xref:Xamarin.Forms.Setter.Value) 。

在 XAML 中， `Property` 設定是 CLR 屬性的名稱 (例如) 的屬性，但是已設定 `Text` `Button` 樣式的屬性必須由可系結屬性支援。 此外，屬性必須在設定所指示的類別中定義 `TargetType` ，或由該類別繼承。

您可以 `Value` 使用 property 元素來指定設定 `<Setter.Value>` 。 這可讓您將設定 `Value` 為無法以文字字串或物件表示的物件，或設定為 `OnPlatform` 使用或具現化的物件 `x:Arguments` `x:FactoryMethod` 。 您 `Value` 也可以使用 `StaticResource` 字典中另一個專案的運算式來設定屬性。

[**BasicStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle)程式示範基本語法，並示範如何 `Style` 使用 `StaticResource` 標記延伸來參考：

[![基本樣式的三重螢幕擷取畫面](images/ch12fg01-small.png "基本樣式")](images/ch12fg01-large.png#lightbox "基本樣式")

`Style`物件和在物件中建立為設定的任何物件， `Style` 都會在 `Value` 所有參考該物件的視圖之間共用 `Style` 。 `Style`無法包含無法共用的任何事物，例如 `View` 衍生。

無法在中設定事件處理常式 `Style` 。 `GestureRecognizers`無法在中設定屬性， `Style` 因為它不受可系結屬性的支援。

## <a name="styles-in-code"></a>程式碼中的樣式

雖然這不是常見的情況，但您可以在程式碼中具現化和初始化 `Style` 物件。 [**BasicStyleCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode)範例會示範這一點。

## <a name="style-inheritance"></a>樣式繼承

`Style` 具有 [`BasedOn`](xref:Xamarin.Forms.Style.BasedOn) 屬性，您可以將其設定為 `StaticResource` 參考其他樣式的標記延伸。 這可讓樣式繼承自先前的樣式，以及新增或取代屬性設定。 [**StyleInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance)範例會示範這一點。

如果 `Style2` 是根據 `Style1` ，則 `TargetType` 的必須與 `Style2` `Style1` 或衍生自 `Style1` 。 儲存的資源字典 `Style1` 必須與 `Style2` 視覺化樹狀結構中較高的資源字典相同或更高的資源字典。

## <a name="implicit-styles"></a>隱含樣式

如果 `Style` 資源字典中的沒有 `x:Key` 屬性設定，則會自動指派字典索引鍵，而 `Style` 物件會變成 *隱含樣式* 。 沒有 `Style` 設定且類型完全相符的視圖 `TargetType` 會找到該樣式，如 [**ImplicitStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) 範例所示。

隱含樣式可以使用設定衍生自， `Style` `x:Key` 但無法使用其他方式衍生。 您無法明確參考隱含樣式。

您可以使用下列樣式來執行三種類型的階層 `BasedOn` ：

- 從與向下定義的樣式， `Application` `Page` 到在視覺化樹狀結構中較低的版面配置上定義的樣式。
- 從針對基類（例如和）定義的樣式 `VisualElement` `View` 到針對特定類別定義的樣式。
- 從具有明確字典索引鍵的樣式轉換成隱含樣式。

這些階層示範于 [**StyleHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) 範例中。

## <a name="dynamic-styles"></a>動態樣式

您可以參考資源字典中的樣式， `DynamicResource` 而不是 `StaticResource` 。 這會讓樣式成為 *動態樣式* 。 如果資源字典中有另一個具有相同索引鍵的樣式來取代該樣式，則參考該樣式的視圖 `DynamicResource` 會自動變更。 此外，缺少具有指定索引鍵的字典專案將會導致 `StaticResource` 引發例外狀況，但不會引發例外狀況 `DynamicResource` 。

您可以使用這項技術，以動態方式變更樣式或主題，如 [**DynamicStyles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) 範例所示。

不過，您不能將 `BasedOn` 屬性設為內容 `DynamicResource` 延伸，因為不受可系結屬性的 `BasedOn` 支援。 若要以動態方式衍生樣式，請不要設定 `BasedOn` 。 相反地，請將 [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) 屬性設定為您想要從中衍生之樣式的字典索引鍵。 [**DynamicStylesInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh)範例會示範這項技巧。

## <a name="device-styles"></a>裝置樣式

此 [`Device.Styles`](xref:Xamarin.Forms.Device.Styles) 嵌套類別會使用的，為六個樣式定義12個靜態唯讀欄位 `TargetType` `Label` ，您可以將其用於常用的文字使用類型。

其中六個欄位的類型為 `Style` ，您可以直接在程式碼中將其設定為 `Style` 屬性：

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

其他六個欄位的類型為 `string` ，而且可以當做動態樣式的字典索引鍵使用：

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey) 等於 "BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey) 等於 "TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey) 等於 "SubtitleStyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey) 等於 "CaptionStyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey) 等於 "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey) 等於 "ListItemDetailTextStyle"

[**DeviceStylesList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList)範例會說明這些樣式。

## <a name="related-links"></a>相關連結

- [第12章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [第12章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
