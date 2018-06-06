---
title: 使用階層式樣式表 (CSS) 樣式 Xamarin.Forms 應用程式
description: Xamarin.Forms 可支援使用階層式樣式表 (CSS) 樣式視覺項目。
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 76ca67f7ac8a8e27e5f502455d48874c775fc172
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794081"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>使用階層式樣式表 (CSS) 樣式 Xamarin.Forms 應用程式

_Xamarin.Forms 可支援使用階層式樣式表 (CSS) 樣式視覺項目。_

Xamarin.Forms 3.0 引進了樣式的應用程式使用 CSS 的能力。 樣式表包含的規則，透過一或多個選取器和宣告區塊組成每個規則的清單。 宣告區塊是由宣告大括號，每個宣告屬性、 冒號和值組成的清單所組成。 當有多個區塊中的宣告時，以分號會插入做為分隔符號。 下列程式碼範例示範一些 Xamarin.Forms 相容 CSS:

```css
^contentpage {
    background-color: lightgray;
}

#listView {
    background-color: lightgray;
}

stacklayout {
    margin: 20;
}

.mainPageTitle {
    font-style: bold;
    font-size: medium;
}

.mainPageSubtitle {
    margin-top: 15;
}

.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}

listview image {
    height: 60;
    width: 60;
}

stacklayout>image {
    height: 200;
    width: 200;
}
```

透過 Xamarin.Forms，CSS 樣式表被剖析，並在執行階段，而不是編譯時期評估和樣式表會使用重新剖析。

> [!NOTE]
> 目前，所有與 XAML 樣式的樣式不能執行 CSS。 不過，XAML 樣式可以用來補充 CSS 屬性目前不支援的 Xamarin.Forms。 如需 XAML 樣式的詳細資訊，請參閱[樣式 Xamarin.Forms 應用程式使用 XAML 樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

[MonkeyAppCSS](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)範例示範如何使用 CSS 樣式是簡單的應用程式，以及下列螢幕擷取畫面所示：

[![CSS 樣式 MonkeyApp 主要頁面](css-images/MonkeyAppMainPage.png "MonkeyApp 主頁面的 CSS 樣式")](css-images/MonkeyAppMainPage-Large.png#lightbox "MonkeyApp 主頁面的 CSS 樣式")

[![MonkeyApp 詳細資料頁面的 CSS 樣式](css-images/MonkeyAppDetailPage.png "MonkeyApp 詳細資料頁面的 CSS 樣式")](css-images/MonkeyAppDetailPage-Large.png#lightbox "MonkeyApp 詳細資料頁面的 CSS 樣式")

> [!NOTE]
> 不是目前無法設定樣式的背景色彩[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)使用樣式表。 因此，在應用程式範例[ `NavigationPage.BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)屬性在程式碼中設定。

## <a name="consuming-a-style-sheet"></a>使用的樣式表

將樣式表加入至解決方案的程序如下所示：

1. 標準.NET 程式庫專案中加入空的 CSS 檔案。
1. 設定建置動作的 CSS 檔案**EmbeddedResource**。

### <a name="loading-a-style-sheet"></a>載入樣式表

有一些可以用來載入樣式表的方法。

### <a name="xaml"></a>XAML

要載入及剖析的樣式表[ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet)類別加入之前[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)頁面：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    ...
</ContentPage>
```

[ `StyleSheet.Source` ](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source)屬性為相對於封入的 XAML 檔案的位置，或專案根目錄的相對 URI 指定的樣式表 URI 開頭`/`。

> [!WARNING]
> CSS 檔案將無法載入它是否建置動作未設定為**EmbeddedResource**。

或者，樣式表可以載入和剖析與[ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet)類別的內嵌在`CDATA`> 一節：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet>
            <![CDATA[
            ^contentpage {
                background-color: lightgray;
            }
            ]]>
        </StyleSheet>
    </ContentPage.Resources>
    ...
</ContentPage>
```

### <a name="c"></a>C#

在 C# 中，在樣式表可以做為內嵌資源載入而且加入至[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)頁面：

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        this.Resources.Add(StyleSheet.FromAssemblyResource(
            IntrospectionExtensions.GetTypeInfo(typeof(MyPage)).Assembly,
            "MyProject.Assets.styles.css"));
    }
}
```

第一個引數`StyleSheet.FromAssemblyResource`方法是包含樣式表的組件，因此第二個引數是`string`代表的資源識別碼。 您可以從取得的資源識別元**屬性**視窗時選取的 CSS 檔案。

或者，載入樣式表，從`StringReader`並加入至[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)頁面：

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        using (var reader = new StringReader("^contentpage { background-color: lightgray; }"))
        {
            this.Resources.Add(StyleSheet.FromReader(reader));
        }
    }
}
```

引數`StyleSheet.FromReader`方法`TextReader`，具有讀取樣式表。

## <a name="selecting-elements-and-applying-properties"></a>選取項目，並將套用屬性

CSS 會使用選取器來決定要為目標的項目。 定義順序依序套用樣式的選取器比對。 一律最後套用特定的項目上定義的樣式。 如需有關支援的選取器的詳細資訊，請參閱[選擇器參考](#selector-reference)。

CSS 樣式的選取項目使用屬性。 每個屬性有一組可能的值，且部分屬性可能會影響任何類型的項目，而其他則套用至項目群組。 如需支援的屬性的詳細資訊，請參閱[屬性參考](#property-reference)。

### <a name="selecting-elements-by-type"></a>選取項目的型別

視覺化樹狀結構中的項目可以選取不區分大小寫的型別`element`選取器：

```css
stacklayout {
    margin: 20;
}
```

這個選取器識別任何[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)取用樣式表，並將其邊界設定為 20 粗細的頁面上的項目。

> [!NOTE]
> `element`選取器不會識別指定之類型的子類別。

### <a name="selecting-elements-by-base-class"></a>由基底類別中選取項目

視覺化樹狀結構中的項目可以選取基底類別具有不區分大小寫`^base`選取器：

```css
^contentpage {
    background-color: lightgray;
}
```

這個選取器識別任何[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)項目使用樣式表，並設定其背景色彩為`lightgray`。

> [!NOTE]
> `^base`選取器專屬於透過 Xamarin.Forms，並不是 CSS 規格的一部分。

### <a name="selecting-an-element-by-name"></a>依據名稱選取項目

視覺化樹狀結構中的個別項目可以選取與區分大小寫`#id`選取器：

```css
#listView {
    background-color: lightgray;
}
```

這個選取器識別項目的其[ `StyleId` ](xref:Xamarin.Forms.Element.StyleId)屬性設定為`listView`。 不過，如果`StyleId`屬性未設定，選取器將會切換回使用`x:Name`的項目。 因此，在下列的 XAML 範例中，`#listView`會識別選取器[ `ListView` ](xref:Xamarin.Forms.ListView)其`x:Name`屬性設為`listView`，並會將它的背景色彩設定為`lightgray`。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView x:Name="listView" ...>
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

### <a name="selecting-elements-with-a-specific-class-attribute"></a>選取項目具有特定的類別屬性

選取項目具有特定的類別屬性來使用區分大小寫`.class`選取器：

```css
.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}
```

CSS 類別可以藉由設定指派給 XAML 項目[ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass) CSS 類別名稱元素的屬性。 因此，在下列的 XAML 範例中，樣式所定義`.detailPageTitle`類別指派給第一個[ `Label` ](xref:Xamarin.Forms.Label)，而所定義的樣式`.detailPageSubtitle`類別指派給第二個`Label`。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            <Label ... StyleClass="detailPageTitle" />
            <Label ... StyleClass="detailPageSubtitle"/>
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

### <a name="selecting-child-elements"></a>選取項目子系

視覺化樹狀結構中的子項目可供選取以不區分大小寫`element element`選取器：

```css
listview image {
    height: 60;
    width: 60;
}
```

這個選取器識別任何[ `Image` ](xref:Xamarin.Forms.Image)子系的項目[ `ListView` ](xref:Xamarin.Forms.ListView)項目，並將其高度和寬度設定為 60。 因此，在下列的 XAML 範例中，`listview image`會識別選取器[ `Image` ](xref:Xamarin.Forms.Image)是子系的[ `ListView` ](xref:Xamarin.Forms.ListView)，並將它的高度和寬度設定為 60。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView ...>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid>
                            ...
                            <Image ... />
                            ...
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> `element element`選取器不需要為子項目_直接_父系-子元素的子系可能有不同的父代。 假設上階是指定的第一個項目，就會發生選取項目。

### <a name="selecting-direct-child-elements"></a>選取 直接子項目

直接選取視覺化樹狀中的子項目的來使用不區分大小寫`element>element`選取器：

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

這個選取器識別任何[ `Image` ](xref:Xamarin.Forms.Image)的項目直屬子系的[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)項目，並將其高度和寬度設定為 200。 因此，在下列的 XAML 範例中，`stacklayout>image`會識別選取器[ `Image` ](xref:Xamarin.Forms.Image)是的直接子系[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，並將它的高度和寬度設定為 200。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            ...
            <Image ... />
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

> [!NOTE]
> `element>element`選取器需要子元素是_直接_父系的子系。

## <a name="selector-reference"></a>選取器參考

Xamarin.Forms 支援下列 CSS 選取器：

|選取器|範例|描述|
|---|---|---|
|`.class`|`.header`|選取所有項目與`StyleClass`屬性包含 '標頭'。 請注意這個選取器會區分大小寫。|
|`#id`|`#email`|選取所有項目與`StyleId`設`email`。 如果`StyleId`未設定，回到`x:Name`。 當使用 XAML，`x:Name`最好透過`StyleId`。 請注意這個選取器會區分大小寫。|
|`*`|`*`|選取所有項目。|
|`element`|`label`|選取類型的所有項目`Label`，但不是子類別。 請注意，這個選取器不區分大小寫。|
|`^base`|`^contentpage`|選取所有項目與`ContentPage`的基底類別，包括`ContentPage`本身。 請注意這個選取器是不區分大小寫，而且不是 CSS 規格的一部分。|
|`element,element`|`label,button`|選取所有`Button`項目和所有`Label`項目。 請注意，這個選取器不區分大小寫。|
|`element element`|`stacklayout label`|選取所有`Label`內的項目`StackLayout`。 請注意，這個選取器不區分大小寫。|
|`element>element`|`stacklayout>label`|選取所有`Label`的項目`StackLayout`的直接父系。 請注意，這個選取器不區分大小寫。|
|`element+element`|`label+entry`|選取所有`Entry`直接項目之後`Label`。 請注意，這個選取器不區分大小寫。|
|`element~element`|`label~entry`|選取所有`Entry`元素前面加上`Label`。 請注意，這個選取器不區分大小寫。|

定義順序依序套用樣式的選取器比對。 一律最後套用特定的項目上定義的樣式。

> [!TIP]
> 選取器可以結合且不受限制，例如`StackLayout>ContentView>label.email`。

目前不支援下列的選取器︰

- `[attribute]`
- `@media` 和 `@supports`
- `:` 和 `::`

> [!NOTE]
> 具體要求，並明確覆寫，不支援。

## <a name="property-reference"></a>屬性參考

Xamarin.Forms 可支援下列的 CSS 屬性 (在**值**資料行中，類型為_斜體_，而字串常值則`gray`):

|屬性|適用於|值|範例|
|---|---|---|---|
|`background-color`|`VisualElement`|_色彩_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_字串_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`|_色彩_ \| `initial`|`border-color: #9acd32;`|
|`border-width`|`Button`|_double_ \| `initial` |`border-width: .5;`|
|`color`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`|_色彩_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_字串_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_double_ \| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_double_ \| `initial` |`min-height: 250;`|
|`margin`|`View`|_粗細_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_粗細_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_粗細_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_粗細_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_粗細_ \| `initial` |`margin-bottom: 6;`|
|`min-height`|`VisualElement`|_double_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_double_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_double_ \| `initial` |`opacity: .3;`|
|`padding`|`Layout`, `Page`|_粗細_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Layout`, `Page`|_double_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Layout`, `Page`| _double_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Layout`, `Page`| _double_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Layout`, `Page`| _double_ \| `initial` |`padding-bottom: 6;`|
|`text-align`| `Entry`、`EntryCell`、`Label``SearchBar`|`left` \| `right` \| `center` \| `start` \| `end` \| `initial`. `left` 和`right`應該避免在由右至左環境中。| `text-align: right;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial `|`visibility: hidden;`|
|`width`|`VisualElement`|_double_ \| `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` 是有效的所有屬性值。 它會清除 （重設為預設值） 所設定的值已從另一個樣式。

目前不支援下列屬性︰

- `all: initial`.
- 版面配置屬性 （ 方塊中或方格）。
- 速記屬性，例如`font`，和`border`。

此外，還有任何`inherit`繼承值，因此不受支援。 因此您不能例如，設定`font-size`版面配置屬性，並預期所有[ `Label` ](xref:Xamarin.Forms.Label)繼承值配置中的執行個體。 其中一個例外是`direction`屬性，其預設值的`inherit`。

### <a name="color"></a>色彩

下列`color`值才受到支援：

- `X11` [色彩](https://en.wikipedia.org/wiki/X11_color_names/)，其符合 CSS 色彩、 UWP 預先定義的色彩和 Xamarin.Forms 的色彩。 請注意，這些色彩值不區分大小寫。
- 十六進位色彩： `#rgb`， `#argb`， `#rrggbb`， `#aarrggbb`
- rgb 色彩： `rgb(255,0,0)`， `rgb(100%,0%,0%)`。 值會是範圍 0-255 或 0%-100%的範圍。
- rgba 色彩： `rgba(255, 0, 0, 0.8)`， `rgba(100%, 0%, 0%, 0.8)`。 不透明度值為 0.0 1.0 的範圍。
- hsl 色彩： `hsl(120, 100%, 50%)`。 H 值位於 0-360 的範圍，而 s 和 l 中範圍 0%-100%。
- hsla 色彩： `hsla(120, 100%, 50%, .8)`。 不透明度值為 0.0 1.0 的範圍。

### <a name="thickness"></a>Thickness

其中一個、 兩個、 三個或四個`thickness`值才受到支援，每個以空格分隔：

- 單一值，表示粗細。
- 兩個值會指出垂直然後水平寬度。
- 三個值會指出上方，接著橫向 （左和右），然後下粗細。
- 四個值會指出頂端，權限，底部，然後左邊的粗細。

> [!NOTE]
> CSS`thickness`值不同 XAML [ `Thickness` ](/api/type/Xamarin.Forms.Thickness/)值。 例如，在 XAML 中兩個值`Thickness`指出水平，則垂直的粗細，四個值時`Thickness`表示左邊，然後頂層，接權限，然後下粗細。 此外，XAML`Thickness`的值為逗號分隔。

### <a name="namedsize"></a>NamedSize

不區分大小寫下列`namedsize`值才受到支援：

- `default`
- `micro`
- `small`
- `medium`
- `large`

每個的確切意義`namedsize`該值是平台相依，而且檢視而定。

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>與 Xamarin.University Xamarin.Forms 中的 CSS

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin.Forms 3.0 CSS、 藉由[Xamarin 大學](https://university.xamarin.com/)**

## <a name="related-links"></a>相關連結

- [MonkeyAppCSS （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)
- [使用 XAML 樣式設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
