---
title： "第12章的摘要。 樣式「描述：」使用下列方式建立 Mobile Apps Xamarin.Forms ：第12章的摘要。 樣式 "ms. 生產： xamarin assetid： 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3 author： davidbritch ms-chap： dabritch ms. date： 07/19/2018 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="summary-of-chapter-12-styles"></a>第12章的摘要。 樣式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

在中 Xamarin.Forms ，樣式允許多個視圖共用屬性設定的集合。 這會減少標記，並讓您維持一致的視覺效果主題。

在標記中，幾乎一律會定義和使用樣式。 類型的物件 [`Style`](xref:Xamarin.Forms.Style) 會在資源字典中具現化，然後 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 使用 `StaticResource` 或標記延伸，將設定為視覺專案的屬性 `DynamicResource` 。

## <a name="the-basic-style"></a>基本樣式

`Style`需要將其 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 設定為其適用之視覺物件的型別。 當 `Style` 在資源字典中具現化時（如一般），它也需要 `x:Key` 屬性。

`Style`具有類型的 content 屬性 [`Setters`](xref:Xamarin.Forms.Style.Setters) ，這是物件的集合 [`Setter`](xref:Xamarin.Forms.Setter) 。 每個都會 `Setter` 將與產生關聯 [`Property`](xref:Xamarin.Forms.Setter.Property) [`Value`](xref:Xamarin.Forms.Setter.Value) 。

在 XAML 中， `Property` 設定是 CLR 屬性的名稱（例如的 `Text` 屬性 `Button` ），但樣式屬性必須由可系結的屬性支援。 此外，屬性必須在設定所指示的類別中定義 `TargetType` ，或由該類別繼承。

您可以 `Value` 使用 property 元素來指定設定 `<Setter.Value>` 。 這可讓您將設定 `Value` 為無法以文字字串或物件表示的物件，或設為 `OnPlatform` 使用或具現化的物件 `x:Arguments` `x:FactoryMethod` 。 `Value`屬性也可以使用 `StaticResource` 運算式設定為字典中的另一個專案。

[**BasicStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle)程式會示範基本語法，並說明如何 `Style` 使用標記延伸來參考 `StaticResource` ：

[![基本樣式的三向螢幕擷取畫面](images/ch12fg01-small.png "基本樣式")](images/ch12fg01-large.png#lightbox "基本樣式")

`Style`物件和在物件中建立為設定的任何物件， `Style` `Value` 都會在所有參考該物件的視圖之間共用 `Style` 。 `Style`不能包含任何無法共用的專案，例如 `View` 衍生。

無法在中設定事件處理常式 `Style` 。 `GestureRecognizers`無法在中設定屬性， `Style` 因為它不是由可系結屬性支援。

## <a name="styles-in-code"></a>程式碼中的樣式

雖然這並不常見，但您可以在程式碼中具現化及初始化 `Style` 物件。 這是由[**BasicStyleCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode)範例所示範。

## <a name="style-inheritance"></a>樣式繼承

`Style`具有 [`BasedOn`](xref:Xamarin.Forms.Style.BasedOn) 屬性，您可以將其設定為 `StaticResource` 參考另一個樣式的標記延伸。 這可讓樣式繼承自先前的樣式，並加入或取代屬性設定。 [**StyleInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance)範例會示範這種情況。

如果 `Style2` 是以為基礎 `Style1` ，則 `TargetType` 的必須與 `Style2` `Style1` 或衍生自 `Style1` 。 儲存的資源字典 `Style1` 必須與 `Style2` 視覺化樹狀結構中較高的資源字典或資源字典相同。

## <a name="implicit-styles"></a>隱含樣式

如果 `Style` 資源字典中沒有 `x:Key` 屬性設定，則會自動指派字典索引鍵，而 `Style` 物件會成為*隱含樣式*。 沒有 `Style` 設定且其類型符合的視圖 `TargetType` 會找到該樣式，如[**ImplicitStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle)範例所示。

隱含樣式可以衍生自 `Style` ， `x:Key` 但使用設定，而不是相反的方式。 您無法明確參考隱含樣式。

您可以使用樣式和來執行三種類型的階層 `BasedOn` ：

- 從定義于 `Application` 和向下的樣式， `Page` 到視覺化樹狀結構中較低的版面配置所定義的樣式。
- 從針對基類（例如和）定義的樣式， `VisualElement` `View` 到為特定類別定義的樣式。
- 從具有明確字典索引鍵的樣式到隱含樣式。

這些階層會在[**StyleHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy)範例中示範。

## <a name="dynamic-styles"></a>動態樣式

您可以參考資源字典中的樣式， `DynamicResource` 而不是 `StaticResource` 。 這會使樣式成為*動態樣式*。 如果在資源字典中，以相同索引鍵的另一個樣式取代該樣式，則參考該樣式的視圖 `DynamicResource` 會自動變更。 此外，如果沒有具有指定之索引鍵的字典專案，就會 `StaticResource` 引發例外狀況，而不是 `DynamicResource` 。

您可以使用這項技術，以動態方式變更樣式或主題，如[**DynamicStyles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles)範例所示。

不過，您無法將 `BasedOn` 屬性設定為 `DynamicResource` 構成延伸模組，因為不受可系結 `BasedOn` 屬性支援。 若要動態衍生樣式，請不要設定 `BasedOn` 。 相反地，請將 [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) 屬性設定為您要衍生自之樣式的字典索引鍵。 [**DynamicStylesInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh)範例會示範這項技術。

## <a name="device-styles"></a>裝置樣式

此 [`Device.Styles`](xref:Xamarin.Forms.Device.Styles) 嵌套類別會針對六個樣式定義十二個靜態唯讀欄位，其中包含 `TargetType` `Label` 可用於一般文字使用類型的。

其中六個欄位的類型是 `Style` ，您可以直接 `Style` 在程式碼中設定屬性：

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

其他六個欄位的類型是 `string` ，而且可以當做動態樣式的字典索引鍵使用：

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
