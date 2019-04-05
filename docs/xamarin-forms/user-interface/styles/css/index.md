---
title: 使用階層式樣式表 (CSS) 樣式設定 Xamarin.Forms 應用程式
description: Xamarin.Forms 可支援使用階層式樣式表 (CSS) 樣式視覺項目。
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2018
ms.openlocfilehash: ca87e5997fa05e014be56f85087ce3e7ffe1ab12
ms.sourcegitcommit: 97dca3face7c4ad5555dfaca88f5b45a70ca556d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2019
ms.locfileid: "57972685"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>使用階層式樣式表 (CSS) 樣式設定 Xamarin.Forms 應用程式

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)

_Xamarin.Forms 可支援使用階層式樣式表 (CSS) 樣式視覺項目。_

Xamarin.Forms 應用程式可以使用 CSS 樣式設定。 樣式表包含規則，其中包含一或多個選取器和宣告區塊的每個規則的清單。 宣告區塊包含大括號中的宣告與每個宣告，其中包含屬性、 一個冒號，以及值的清單。 在區塊中的多個宣告時，用分號會插入做為分隔符號。 下列程式碼範例示範一些 Xamarin.Forms 相容 CSS:

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

在 Xamarin.Forms 中，CSS 樣式表被剖析，並在執行階段，而不是編譯時期評估和樣式表是在使用重新剖析。

> [!NOTE]
> 目前，所有可透過 XAML 樣式的樣式無法執行與 CSS。 不過，XAML 樣式可用來補充 CSS Xamarin.Forms 目前不支援的屬性。 如需有關 XAML 樣式的詳細資訊，請參閱[樣式設定 Xamarin.Forms 應用程式使用 XAML 樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

[MonkeyAppCSS](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)範例示範如何使用 CSS 樣式是簡單的應用程式，以及下列的螢幕擷取畫面所示：

[![CSS 樣式 MonkeyApp 主頁面](css-images/MonkeyAppMainPage.png "MonkeyApp 主頁面上，使用 CSS 樣式")](css-images/MonkeyAppMainPage-Large.png#lightbox "MonkeyApp 主頁面上，使用 CSS 樣式")

[![使用 CSS 樣式的 MonkeyApp 詳細資料頁面](css-images/MonkeyAppDetailPage.png "MonkeyApp 詳細資料頁面，使用 CSS 樣式")](css-images/MonkeyAppDetailPage-Large.png#lightbox "MonkeyApp 與 CSS 樣式的詳細資料頁面")

## <a name="consuming-a-style-sheet"></a>使用樣式表

將樣式表新增至解決方案的程序如下所示：

1. .NET Standard 程式庫專案中加入空白的 CSS 檔案。
1. 將 CSS 檔案的建置動作設定**EmbeddedResource**。

### <a name="loading-a-style-sheet"></a>載入樣式表

有許多可用來載入樣式表的方法。

### <a name="xaml"></a>XAML

可載入和剖析與樣式表[ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet)才能新增到類別[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

[ `StyleSheet.Source` ](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source)屬性為相對於封入的 XAML 檔案的位置，或相對於專案根目錄的 URI 指定的樣式表 URI 的開頭`/`。

> [!WARNING]
> CSS 檔案將無法載入其建置動作未設定為如果**EmbeddedResource**。

或者，可載入和剖析與樣式表[ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet)類別，才能新增到[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)、 藉由內嵌在`CDATA`區段：

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

如需有關資源字典的詳細資訊，請參閱[資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

### <a name="c"></a>C#

在C#，可以做為內嵌資源載入並加入樣式表[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

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

第一個引數`StyleSheet.FromAssemblyResource`方法是包含樣式表的組件，而第二個引數是`string`代表的資源識別碼。 資源識別項可以取自**屬性**CSS 檔案已被選取時，視窗。

或者，載入樣式表，從`StringReader`，並加入[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

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

引數`StyleSheet.FromReader`方法是`TextReader`，具有讀取樣式表。

## <a name="selecting-elements-and-applying-properties"></a>選取項目，並將套用屬性

CSS 會使用選取器，來決定要為目標的項目。 定義順序連續，套用符合選取器的樣式。 特定的項目上定義的樣式一律套用在上一次。 如需有關支援的選取器的詳細資訊，請參閱 <<c0> [ 選取器參考](#selector-reference)。

CSS 會使用屬性來設定所選的元素的樣式。 每個屬性都有一組可能的值，以及某些屬性可能會影響任何類型的項目，而其他則套用至項目群組。 如需有關支援的屬性的詳細資訊，請參閱 <<c0> [ 屬性參考](#property-reference)。

### <a name="selecting-elements-by-type"></a>選取 依類型的項目

視覺化樹狀結構中的項目可以選取具有不區分大小寫的型別`element`選取器：

```css
stacklayout {
    margin: 20;
}
```

此選取器會識別任何[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)使用樣式表中，並將其邊界設定為 20 個粗細一致的頁面上的項目。

> [!NOTE]
> `element`選取器不會識別指定之型別的子類別。

### <a name="selecting-elements-by-base-class"></a>基底類別所選取項目

視覺化樹狀結構中的項目可以使用不區分大小寫的基底類別選取`^base`選取器：

```css
^contentpage {
    background-color: lightgray;
}
```

此選取器會識別任何[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)項目使用樣式表，並設定其背景的色彩`lightgray`。

> [!NOTE]
> `^base`選取器因 Xamarin.Forms，而不是 CSS 規格的一部分。

### <a name="selecting-an-element-by-name"></a>依名稱選取項目

視覺化樹狀結構中的個別項目可以選取使用區分大小寫`#id`選取器：

```css
#listView {
    background-color: lightgray;
}
```

此選取器識別項目其[ `StyleId` ](xref:Xamarin.Forms.Element.StyleId)屬性設定為`listView`。 不過，如果`StyleId`屬性未設定，選取器會切換回使用`x:Name`的項目。 因此，在下列 XAML 範例中，`#listView`選取器會識別[ `ListView` ](xref:Xamarin.Forms.ListView)其`x:Name`屬性設為`listView`，並會將它的背景色彩設定為`lightgray`。

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

### <a name="selecting-elements-with-a-specific-class-attribute"></a>選取具有特定類別屬性的項目

可以選取具有特定類別屬性的項目，使用區分大小寫`.class`選取器：

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

CSS 類別可以藉由設定指派給 XAML 項目[ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass)的 CSS 類別名稱的項目屬性。 因此，在下列 XAML 範例中，樣式來定義`.detailPageTitle`類別會指派給第一個[ `Label` ](xref:Xamarin.Forms.Label)，而所定義的樣式`.detailPageSubtitle`類別會指派給第二個`Label`。

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

視覺化樹狀結構中的子項目可以選取具有不區分大小寫`element element`選取器：

```css
listview image {
    height: 60;
    width: 60;
}
```

此選取器會識別任何[ `Image` ](xref:Xamarin.Forms.Image)的子系的項目[ `ListView` ](xref:Xamarin.Forms.ListView)項目，並將其高度和寬度設定為 60。 因此，在下列 XAML 範例中，`listview image`選取器會識別[ `Image` ](xref:Xamarin.Forms.Image)子資料夾的[ `ListView` ](xref:Xamarin.Forms.ListView)，並將其高度和寬度設定為 60。

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
> `element element`選取器不需要子元素_直接_父系-子元素的子系可能會有不同的父代。 前提是上階是指定的第一個項目，就會發生選取項目。

### <a name="selecting-direct-child-elements"></a>選取的直接子系項目

直接在視覺化樹狀結構中的子項目可以選取具有不區分大小寫`element>element`選取器：

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

此選取器會識別任何[ `Image` ](xref:Xamarin.Forms.Image)項目直接子系[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)項目，並將其高度和寬度設定為 200。 因此，在下列 XAML 範例中，`stacklayout>image`選取器會識別[ `Image` ](xref:Xamarin.Forms.Image)也就是說的直接子系[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，並將其高度和寬度設定為 200。

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
> `element>element`選取器可讓您要求的子項目都_直接_父系的子系。

## <a name="selector-reference"></a>選取器參考

Xamarin.Forms 也支援下列 CSS 選取器：

|選取器|範例|描述|
|---|---|---|
|`.class`|`.header`|選取所有項目`StyleClass`包含 'header' 的屬性。 請注意，此選取器是區分大小寫。|
|`#id`|`#email`|選取所有項目`StyleId`設定為`email`。 如果`StyleId`未設定，後援`x:Name`。 使用 XAML 時,`x:Name`最好透過`StyleId`。 請注意，此選取器是區分大小寫。|
|`*`|`*`|選取所有項目。|
|`element`|`label`|選取類型的所有項目`Label`，但不是子類別。 請注意，此選取器不區分大小寫。|
|`^base`|`^contentpage`|選取所有項目`ContentPage`的基底類別，包括`ContentPage`本身。 請注意，此選取器不區分大小寫，而且不是 CSS 規格的一部分。|
|`element,element`|`label,button`|選取所有`Button`項目和所有`Label`項目。 請注意，此選取器不區分大小寫。|
|`element element`|`stacklayout label`|選取所有`Label`內的項目`StackLayout`。 請注意，此選取器不區分大小寫。|
|`element>element`|`stacklayout>label`|選取所有`Label`項目`StackLayout`為直接父系。 請注意，此選取器不區分大小寫。|
|`element+element`|`label+entry`|選取所有`Entry`直接項目之後`Label`。 請注意，此選取器不區分大小寫。|
|`element~element`|`label~entry`|選取所有`Entry`加上項目`Label`。 請注意，此選取器不區分大小寫。|

定義順序連續，套用符合選取器的樣式。 特定的項目上定義的樣式一律套用在上一次。

> [!TIP]
> 選取器可以結合且不受限制，例如`StackLayout>ContentView>label.email`。

目前不支援下列選取器︰

- `[attribute]`
- `@media` 和 `@supports`
- `:` 和 `::`

> [!NOTE]
> 明確性，以及明確覆寫不支援。

## <a name="property-reference"></a>屬性參考

Xamarin.Forms 所支援下列的 CSS 屬性 (在**值**資料行中，類型為_斜體_，而字串常值則`gray`):

|屬性|適用於|值|範例|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_色彩_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_字串_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_色彩_ \| `initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_雙精度浮點數_ \| `initial` |`border-radius: 10;`|
|`border-width`|`Button`、 `ImageButton`|_雙精度浮點數_ \| `initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_色彩_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_雙精度浮點數_ \| `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_浮點數_ \| `auto` \| `initial`。 此外，可以使用指定的百分比在範圍 0%到 100%`%`正負號。|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_浮點數_ \| `initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_浮點數_ \| `initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_字串_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_雙精度浮點_\| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_雙精度浮點數_ \| `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`line-height`|`Label`、 `Span`|_雙精度浮點數_ \| `initial` |`line-height: 1.8;`|
|`margin`|`View`|_粗細_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_粗細_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_粗細_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_粗細_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_粗細_ \| `initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_Int_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_雙精度浮點數_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_雙精度浮點數_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_雙精度浮點數_ \| `initial` |`opacity: .3;`|
|`order`|`VisualElement`|_Int_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_粗細_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_雙精度浮點數_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _雙精度浮點數_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _雙精度浮點數_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _雙精度浮點數_ \| `initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _雙精度浮點數_ \| `initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left` 和`right`應該避免在由右至左環境中。| `text-align: right;`|
|`text-decoration`|`Label`、 `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _雙精度浮點_， _double_ \| `initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial `|`visibility: hidden;`|
|`width`|`VisualElement`|_雙精度浮點數_ \| `initial`|`min-width: 320;`|

也支援下列 Xamarin.Forms 特定 CSS 屬性 (在**值**資料行中，類型為_斜體_，而字串常值則`gray`):

|屬性|適用於|值|範例|
|---|---|---|---|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|_加上引號的文字_ \| `initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_色彩_ \| `initial` |`-xf-placeholder-color: green;`|
|`-xf-max-length`|`Entry`、 `Editor`|_Int_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-bar-background-color`|`NavigationPage`、 `TabbedPage`|_色彩_ \| `initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`、 `TabbedPage`|_色彩_ \| `initial` |`-xf-bar-text-color: gray`|
|`-xf-orientation`|`ScrollView`、 `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both` 僅支援`ScrollView`。 |`-xf-orientation: horizontal;`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-min-track-color`|`Slider`|_色彩_ \| `initial` |`-xf-min-track-color: yellow;`|
|`-xf-max-track-color`|`Slider`|_色彩_ \| `initial` |`-xf-max-track-color: red;`|
|`-xf-thumb-color`|`Slider`|_色彩_ \| `initial` |`-xf-thumb-color: limegreen;`|
|`-xf-spacing`|`StackLayout`|_雙精度浮點數_ \| `initial` |`-xf-spacing: 8;`|

> [!NOTE]
> `initial` 是有效的值給所有屬性。 它會清除 （重設為預設值） 所設定的值是從另一種樣式。

目前不支援下列屬性︰

- `all: initial`.
- （ 方塊中或 方格），就會配置屬性。
- 速記屬性，例如`font`，和`border`。

此外，還有任何`inherit`值，因此繼承不受支援。 因此您不能例如，設定`font-size`版面配置上的屬性，並預期所有[ `Label` ](xref:Xamarin.Forms.Label)版面配置，以繼承值中的執行個體。 唯一例外的是`direction`屬性，其預設值的`inherit`。

### <a name="color"></a>色彩

下列`color`支援值：

- `X11` [色彩](https://en.wikipedia.org/wiki/X11_color_names/)，其符合 CSS 色彩、 預先定義的色彩，UWP 和 Xamarin.Forms 的色彩。 請注意，這些色彩值不區分大小寫。
- 十六進位色彩： `#rgb`， `#argb`， `#rrggbb`， `#aarrggbb`
- rgb 色彩： `rgb(255,0,0)`， `rgb(100%,0%,0%)`。 值為在範圍 0-255 或 0%-100%。
- rgba 色彩： `rgba(255, 0, 0, 0.8)`， `rgba(100%, 0%, 0%, 0.8)`。 不透明度值會是範圍 0.0-1.0。
- hsl 色彩： `hsl(120, 100%, 50%)`。 H 的值會是範圍 0-360，s 和 l 則是在範圍 0%-100%。
- hsla 色彩： `hsla(120, 100%, 50%, .8)`。 不透明度值會是範圍 0.0-1.0。

### <a name="thickness"></a>Thickness

一個、 兩個、 三個或四個`thickness`支援值，並以泛空白字元：

- 單一值，表示粗細一致。
- 兩個值，表示垂直則水平的粗細。
- 三個值會指出前，水平 （左邊和右邊），然後下粗細。
- 四個值會指出上方，然後權限，則底部，左側的粗細。

> [!NOTE]
> CSS`thickness`值不同於 XAML [ `Thickness` ](xref:Xamarin.Forms.Thickness)值。 例如，在兩個值的 XAML`Thickness`指出水平的則垂直的粗細，四個值同時`Thickness`表示左、 上方，然後權限，然後下的粗細。 此外，XAML`Thickness`值會以逗號分隔。

### <a name="namedsize"></a>NamedSize

不區分大小寫下`namedsize`支援值：

- `default`
- `micro`
- `small`
- `medium`
- `large`

每個的確切意義`namedsize`值是平台相依性和檢視相依性。

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>Xamarin.Forms 中搭配 Xamarin.University CSS

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin.Forms 3.0 CSS、 藉由[Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>相關連結

- [MonkeyAppCSS （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [使用 XAML 樣式設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
