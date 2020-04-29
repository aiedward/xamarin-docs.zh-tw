---
title: 使用階層式樣式表 (CSS) 設定 Xamarin.Forms 應用程式的樣式
description: Xamarin 支援使用階層式樣式表（CSS）來設定視覺元素的樣式。
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2020
ms.openlocfilehash: 182bd088ba169dad6bdbbb4315a033925964f64e
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517556"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>使用階層式樣式表（CSS）設定 Xamarin 應用程式的樣式

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Xamarin 支援使用階層式樣式表（CSS）來設定視覺元素的樣式。_

Xamarin：表單應用程式可以使用 CSS 來進行樣式。 樣式表單包含一份規則清單，其中每個規則都包含一個或多個選取器和一個宣告區塊。 宣告區塊包含以大括弧括住的宣告清單，其中每個宣告都包含一個屬性、一個冒號和一個值。 當區塊中有多個宣告時，會插入分號做為分隔符號。 下列程式碼範例顯示一些符合格式的 Xamarin CSS：

```css
navigationpage {
    -xf-bar-background-color: lightgray;
}

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

在 Xamarin 中，CSS 樣式表單會在執行時間進行剖析和評估，而不是編譯時期，而樣式表單則會在使用時重新剖析。

> [!NOTE]
> 目前，所有可能使用 XAML 樣式設定的樣式，都無法使用 CSS 來執行。 不過，XAML 樣式可以用來補充目前 Xamarin 不支援之屬性的 CSS。 如需 XAML 樣式的詳細資訊，請參閱[使用 XAML 樣式設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

[MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)範例會示範如何使用 CSS 來建立簡單應用程式的樣式，如下列螢幕擷取畫面所示：

[![具有 CSS 樣式的 MonkeyApp 主頁面](css-images/MonkeyAppMainPage.png "具有 CSS 樣式的 MonkeyApp 主頁面")](css-images/MonkeyAppMainPage-Large.png#lightbox "具有 CSS 樣式的 MonkeyApp 主頁面")

[![具有 CSS 樣式的 MonkeyApp 詳細資料頁面](css-images/MonkeyAppDetailPage.png "具有 CSS 樣式的 MonkeyApp 詳細資料頁面")](css-images/MonkeyAppDetailPage-Large.png#lightbox "具有 CSS 樣式的 MonkeyApp 詳細資料頁面")

## <a name="consuming-a-style-sheet"></a>使用樣式表單

將樣式表單加入至方案的程式如下所示：

1. 將空白的 CSS 檔案新增至您的 .NET Standard 程式庫專案。
1. 將 CSS 檔案的 [建立] 動作設定為 [ **EmbeddedResource**]。

### <a name="loading-a-style-sheet"></a>載入樣式表單

有數種方法可以用來載入樣式表單。

> [!NOTE]
> 目前不能在執行時間變更樣式表單，並已套用新的樣式表單。

### <a name="xaml"></a>XAML

在新增至之前，可以使用[`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet)類別來載入樣式表單並加以剖析： [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

[`StyleSheet.Source`](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source)屬性會將樣式表單指定為相對於封閉式 XAML 檔案位置的 URI，如果 uri 以開頭，則會將相對於專案根目錄`/`。

> [!WARNING]
> 如果其組建動作未設定為**EmbeddedResource**，CSS 檔案將無法載入。

或者，您可以在[`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) `CDATA`區段中內嵌樣式表單，在加入之前，使用類別來載入並剖析它：

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

如需資源字典的詳細資訊，請參閱[資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

### <a name="c"></a>C\#

在 c # 中，樣式表單可以從載入， `StringReader`並加入至[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)：

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

`StyleSheet.FromReader`方法的引數是已讀取`TextReader`樣式表單的。

## <a name="selecting-elements-and-applying-properties"></a>選取專案和套用屬性

CSS 會使用選取器來判斷要設為目標的元素。 具有相符選取器的樣式會以定義順序連續套用。 在特定專案上定義的樣式一律會最後套用。 如需支援之選取器的詳細資訊，請參閱[選取器參考](#selector-reference)。

CSS 使用屬性來為選取的專案建立樣式。 每個屬性都有一組可能的值，而某些屬性會影響任何類型的專案，而有些則適用于元素群組。 如需支援屬性的詳細資訊，請參閱[屬性參考](#property-reference)。

> [!IMPORTANT]
> CSS 變數不受支援。

### <a name="selecting-elements-by-type"></a>依類型選取元素

視覺化樹狀結構中的元素可以使用不區分`element`大小寫選取器的類型來選取：

```css
stacklayout {
    margin: 20;
}
```

此選取器會[`StackLayout`](xref:Xamarin.Forms.StackLayout)識別使用樣式表單之頁面上的任何專案，並將其邊界設定為統一的寬度20。

> [!NOTE]
> `element`選取器不會識別指定之類型的子類別。

### <a name="selecting-elements-by-base-class"></a>依基類選取元素

視覺化樹狀結構中的專案可以使用不區分`^base`大小寫選取器的基類來選取：

```css
^contentpage {
    background-color: lightgray;
}
```

此選取器會[`ContentPage`](xref:Xamarin.Forms.ContentPage)識別使用樣式表單的任何專案，並將其背景色彩`lightgray`設定為。

> [!NOTE]
> 此`^base`選取器是 Xamarin 特有的，而且不是 CSS 規格的一部分。

### <a name="selecting-an-element-by-name"></a>依名稱選取元素

您可以使用區分大小寫的`#id`選取器來選取視覺化樹狀結構中的個別元素：

```css
#listView {
    background-color: lightgray;
}
```

此選取器會識別其[`StyleId`](xref:Xamarin.Forms.Element.StyleId)屬性設定為`listView`的元素。 不過，如果未`StyleId`設定屬性，則選取器會切換回使用`x:Name`元素的。 因此`#listView` ，在下列 XAML 範例中，選取器會識別其[`ListView`](xref:Xamarin.Forms.ListView) `x:Name`屬性設定為`listView`的，並將它的背景色彩設定為。 `lightgray`

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

### <a name="selecting-elements-with-a-specific-class-attribute"></a>選取具有特定類別屬性的元素

具有特定類別屬性的元素可以使用區分大小寫的`.class`選取器來選取：

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

將元素的[`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass)屬性設定為 css 類別名稱，即可將 css 類別指派給 XAML 專案。 因此，在下列 XAML 範例中`.detailPageTitle` ，類別所定義的樣式會指派給第一個[`Label`](xref:Xamarin.Forms.Label)，而`.detailPageSubtitle`類別所定義的樣式會指派給第二個。 `Label`

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

### <a name="selecting-child-elements"></a>選取子項目

視覺化樹狀結構中的子項目可以使用不區分`element element`大小寫的選取器來選取：

```css
listview image {
    height: 60;
    width: 60;
}
```

此選取器會[`Image`](xref:Xamarin.Forms.Image)識別任何屬於專案子[`ListView`](xref:Xamarin.Forms.ListView)系的專案，並將其高度和寬度設為60。 因此，在下列 XAML 範例中， `listview image`選取器會識別[`Image`](xref:Xamarin.Forms.Image)為的子系[`ListView`](xref:Xamarin.Forms.ListView)，並將其高度和寬度設定為60。

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
> `element element`選取器不需要子項目是父系的_直接_子系–子專案可能會有不同的父系。 如果上階是指定的第一個元素，則會進行選取。

### <a name="selecting-direct-child-elements"></a>選取直接的子項目

您可以使用不區分`element>element`大小寫的選取器來選取視覺化樹狀結構中的直接子項目：

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

此選取器會[`Image`](xref:Xamarin.Forms.Image)識別屬於專案之直接子[`StackLayout`](xref:Xamarin.Forms.StackLayout)系的任何專案，並將其高度和寬度設定為200。 因此，在下列 XAML 範例中， `stacklayout>image`選取器會識別[`Image`](xref:Xamarin.Forms.Image)這是的直接子系[`StackLayout`](xref:Xamarin.Forms.StackLayout)，並將其高度和寬度設定為200。

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
> `element>element`選取器需要子項目是父系的_直接_子系。

## <a name="selector-reference"></a>選取器參考

下列是 Xamarin 支援的 CSS 選取器：

|選取器|範例|描述|
|---|---|---|
|`.class`|`.header`|選取具有包含 ' header `StyleClass` ' 屬性的所有元素。 請注意，此選取器會區分大小寫。|
|`#id`|`#email`|選取`StyleId`設定為`email`的所有元素。 如果`StyleId`未設定，請回到`x:Name`。 使用 XAML 時， `x:Name`優先于`StyleId`。 請注意，此選取器會區分大小寫。|
|`*`|`*`|選取所有元素。|
|`element`|`label`|選取類型`Label`的所有元素，而不是子類別。 請注意，此選取器不區分大小寫。|
|`^base`|`^contentpage`|選取所有具有`ContentPage`做為基類的元素，包括`ContentPage`本身。 請注意，此選取器不區分大小寫，而且不是 CSS 規格的一部分。|
|`element,element`|`label,button`|選取所有`Button`專案和所有`Label`元素。 請注意，此選取器不區分大小寫。|
|`element element`|`stacklayout label`|選取內`Label`的`StackLayout`所有元素。 請注意，此選取器不區分大小寫。|
|`element>element`|`stacklayout>label`|選取所有`Label`以作為`StackLayout`直接父系的元素。 請注意，此選取器不區分大小寫。|
|`element+element`|`label+entry`|選取緊接`Entry`在之後的`Label`所有元素。 請注意，此選取器不區分大小寫。|
|`element~element`|`label~entry`|選取前面`Entry`加上的`Label`所有專案。 請注意，此選取器不區分大小寫。|

具有相符選取器的樣式會以定義順序連續套用。 在特定專案上定義的樣式一律會最後套用。

> [!TIP]
> 選取器可以結合而不受限制， `StackLayout>ContentView>label.email`例如。

目前不支援下列選取器：

- `[attribute]`
- `@media` 和 `@supports`
- `:` 和 `::`

> [!NOTE]
> 不支援明確性和明確覆寫。

## <a name="property-reference"></a>屬性參考

下列 CSS 屬性受到 Xamarin 的支援（在 [**值] 資料**行中，[類型] 為_斜體_，而字串`gray`常值為）：

|屬性|適用對象|值|範例|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_色彩_ \|`initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_字串_ \|`initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_色彩_ \|`initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_double_ \| `initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_double_ \|`initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_色彩_ \|`initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_double_ \| `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_float_ \| float `auto` 。 \| `initial` 此外，範圍0% 到100% 的百分比可以使用`%`正負號來指定。|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_FLOAT_ \| `initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_FLOAT_ \| `initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_字串_ \|`initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_雙重_\| _namedsize_ namedsize \|  `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_double_ \| `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`letter-spacing`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `SearchHandler`, `Span`, `TimePicker`|_double_ \|`initial`|`letter-spacing: 2.5;`|
|`line-height`|`Label`, `Span`|_double_ \|`initial` |`line-height: 1.8;`|
|`margin`|`View`|_粗細_ \|`initial` |`margin: 6 12;`|
|`margin-left`|`View`|_粗細_ \|`initial` |`margin-left: 3;`|
|`margin-top`|`View`|_粗細_ \|`initial` |`margin-top: 2;`|
|`margin-right`|`View`|_粗細_ \|`initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_粗細_ \|`initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_int_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_double_ \|`initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_double_ \|`initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_double_ \|`initial` |`opacity: .3;`|
|`order`|`VisualElement`|_int_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_粗細_ \|`initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_double_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \|`initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \|`initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \|`initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _double_ \|`initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left`在`right`由右至左的環境中應該避免和。| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _double_、 _double_ \|`initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_double_ \|`initial`|`min-width: 320;`|

> [!NOTE]
> `initial`是所有屬性的有效值。 它會清除從另一個樣式設定的值（重設為預設值）。

目前不支援下列屬性：

- `all: initial`.
- 版面配置屬性（box 或方格）。
- 速記屬性，例如`font`、和。 `border`

此外，也沒有任何`inherit`值，因此不支援繼承。 例如，您無法在配置上設定`font-size`屬性，並預期版面配置中的所有[`Label`](xref:Xamarin.Forms.Label)實例都會繼承值。 其中一個例外狀況是`direction`屬性，其預設值為`inherit`。

以`Span`專案為目標的已知問題，會使範圍無法以專案和名稱（使用`#`符號）作為 CSS 樣式的目標。 `Span`元素衍生自`GestureElement`，其不具有`StyleClass`屬性，因此跨越不支援 CSS 類別目標。 如需詳細資訊，請參閱[無法將 CSS 樣式套用至範圍控制](https://github.com/xamarin/Xamarin.Forms/issues/5979)。

### <a name="xamarinforms-specific-properties"></a>Xamarin. 表單特定屬性

以下是也支援的 Xamarin 特定 CSS 屬性（在 [值]**資料**行中，[類型] 為 [_斜體_] `gray`，而字串常值為）：

|屬性|適用對象|值|範例|
|---|---|---|---|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_色彩_ \|`initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_色彩_ \|`initial` |`-xf-bar-text-color: gray`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-max-length`|`Entry`, `Editor`, `SearchBar`|_int_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-max-track-color`|`Slider`|_色彩_ \|`initial` |`-xf-max-track-color: red;`|
|`-xf-min-track-color`|`Slider`|_色彩_ \|`initial` |`-xf-min-track-color: yellow;`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both`只有在上才支援`ScrollView`。 |`-xf-orientation: horizontal;`|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|_加上引號的文字_ \|`initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_色彩_ \|`initial` |`-xf-placeholder-color: green;`|
|`-xf-spacing`|`StackLayout`|_double_ \|`initial` |`-xf-spacing: 8;`|
|`-xf-thumb-color`|`Slider`, `Switch`|_色彩_ \|`initial` |`-xf-thumb-color: limegreen;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-vertical-text-alignment`|`Label`| `start` \| `center` \| `end` \| `initial`|`-xf-vertical-text-alignment: end;`|
|`-xf-visual`|`VisualElement`|_字串_ \|`initial` |`-xf-visual: material;`|

### <a name="xamarinforms-shell-specific-properties"></a>Xamarin.Forms Shell 特定屬性

下列是也支援的 Xamarin Shell 特定 CSS 屬性（在 [值]**資料**行中，類型為_斜體_，而字串常`gray`值為）：

|屬性|適用對象|值|範例|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|_色彩_ \|`initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|_色彩_ \|`initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|_色彩_ \|`initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|_色彩_ \|`initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|_色彩_ \|`initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|_色彩_ \|`initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|_色彩_ \|`initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|_色彩_ \|`initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|_色彩_ \|`initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|_色彩_ \|`initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|_色彩_ \|`initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>Color

支援下列`color`值：

- `X11`[色彩](https://en.wikipedia.org/wiki/X11_color_names)，符合 CSS 色彩、UWP 預先定義的色彩和 Xamarin 色彩。 請注意，這些色彩值不區分大小寫。
- 十六進位色彩： `#rgb`、 `#argb`、 `#rrggbb`、`#aarrggbb`
- rgb 色彩： `rgb(255,0,0)`、 `rgb(100%,0%,0%)`。 值的範圍為0-255，或 0%-100%。
- rgba 色彩： `rgba(255, 0, 0, 0.8)`、 `rgba(100%, 0%, 0%, 0.8)`。 不透明度值的範圍為 0.0-1.0。
- hsl 色彩： `hsl(120, 100%, 50%)`。 H 值的範圍是0-360，而 s 和 l 則在 0%-100% 的範圍內。
- hsla 色彩： `hsla(120, 100%, 50%, .8)`。 不透明度值的範圍為 0.0-1.0。

### <a name="thickness"></a>Thickness

支援一個、兩個、三個`thickness`或四個值，每個都以空白字元分隔：

- 單一值表示一致的粗細。
- 兩個值表示垂直和水準粗細。
- 三個值表示 top、水準（左和右）和下粗細。
- 四個值表示上、右、下、向左粗細。

> [!NOTE]
> CSS `thickness`值與 XAML [`Thickness`](xref:Xamarin.Forms.Thickness)值不同。 例如，在 XAML 中，兩個值`Thickness`表示水準和垂直粗細，而四個值`Thickness`表示左、上、右、右、下粗細。 此外，XAML `Thickness`值會以逗號分隔。

### <a name="namedsize"></a>NamedSize

以下是支援的`namedsize`不區分大小寫值：

- `default`
- `micro`
- `small`
- `medium`
- `large`

每個`namedsize`值的確切意義與平臺相依，而且與 view 相依。

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>Xamarin 中的 CSS 與 Xamarin. 表單

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin. Forms 3.0 CSS 影片**

## <a name="related-links"></a>相關連結

- [MonkeyAppCSS （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [使用 XAML 樣式設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
