---
title: Xamarin FlexLayout
description: 使用 FlexLayout 來堆疊或包裝子視圖的集合。
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 187befd88c115133a92aa90a711438e7754518d5
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "68648809"
---
# <a name="the-xamarinforms-flexlayout"></a>Xamarin FlexLayout

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)

_使用 FlexLayout 來堆疊或包裝子視圖的集合。_

Xamarin. Forms [`FlexLayout`](xref:Xamarin.Forms.FlexLayout)是 Xamarin. forms 版本3.0 中的新功能。 它是以 [CSS 彈性方塊配置][模組](http://www.w3.org/TR/css-flexbox-1/)為基礎，通常稱為 [_彈性版面_配置] 或 [_彈性箱_]，因為它包含許多彈性選項，可在版面配置中排列子系。

`FlexLayout` 類似于 Xamarin [`StackLayout`](~/xamarin-forms/user-interface/layouts/stack-layout.md) ，它可以在堆疊中以水準和垂直方式排列其子系。 不過，如果單一資料列或資料行中有太多要容納的子系，則 `FlexLayout` 也能夠包裝其子系，而且也有許多方向、對齊和適應各種螢幕大小的選項。

`FlexLayout` 衍生自[`Layout<View>`](xref:Xamarin.Forms.Layout`1) ，並繼承 `IList<View>` 類型的[`Children`](xref:Xamarin.Forms.Layout`1.Children)屬性。

`FlexLayout` 定義六個公用可系結屬性和五個附加可系結的屬性，這些屬性會影響其子項目的大小、方向和對齊方式。 （如果您不熟悉附加的可系結屬性，請參閱 **[附加屬性](~/xamarin-forms/xaml/attached-properties.md)** 一文）。如需詳細資訊，請參閱以下各節中 **[的](#bindable-properties)** 可系結屬性和 **[附加的](#attached-properties)** 可系結屬性詳細說明這些屬性。 不過，本文一開始會先討論一些常見的 `FlexLayout` **[使用案例](#common-scenarios)** ，其中會以更非正式的方式來描述許多屬性。 在文章的結尾，您將瞭解如何結合 `FlexLayout` 與[CSS 樣式表單](~/xamarin-forms/user-interface/styles/css/index.md)。

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>常見使用案例

**[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 程式範例包含數個頁面會示範一些常見的用法`FlexLayout`，並可讓您嘗試使用其屬性。

### <a name="using-flexlayout-for-a-simple-stack"></a>針對簡單堆疊使用 FlexLayout

[**簡單堆疊**] 頁面會顯示 `FlexLayout` 可以如何取代 `StackLayout` 但具有較簡單的標記。 這個範例中的所有專案都是在 XAML 頁面中定義。 @No__t_0 包含四個子系：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.SimpleStackPage"
             Title="Simple Stack">

    <FlexLayout Direction="Column"
                AlignItems="Center"
                JustifyContent="SpaceEvenly">

        <Label Text="FlexLayout in Action"
               FontSize="Large" />

        <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />

        <Button Text="Do-Nothing Button" />

        <Label Text="Another Label" />
    </FlexLayout>
</ContentPage>
```

以下是在 iOS、Android 和通用 Windows 平臺上執行的該頁面：

[![[簡單堆疊] 頁面](flex-layout-images/SimpleStack.png "[簡單堆疊] 頁面")](flex-layout-images/SimpleStack-Large.png#lightbox)

**SimpleStackPage**中會顯示三個 `FlexLayout` 屬性：

- [@No__t_1](xref:Xamarin.Forms.FlexLayout.Direction)屬性會設定為[`FlexDirection`](xref:Xamarin.Forms.FlexDirection)列舉的值。 預設為 `Row`。 將屬性設定為 `Column` 會將 `FlexLayout` 的子系排列在專案的單一資料行中。

    當 `FlexLayout` 中的專案在資料行中排列時，`FlexLayout` 會被視為具有垂直_主要軸_和水準_交叉軸_。

- [@No__t_1](xref:Xamarin.Forms.FlexLayout.AlignItems)屬性是[`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems)類型，並指定如何在交叉軸上對齊專案。 @No__t_0 選項會使每個專案水準置中。

    如果您使用 `StackLayout` 而不是這項工作的 `FlexLayout`，您會將每個專案的 `HorizontalOptions` 屬性指派給 `Center`，以將所有專案置中。 @No__t_0 屬性不適用於 `FlexLayout` 的子系，但是單一 `AlignItems` 屬性會達成相同的目標。 如有需要，您可以使用 `AlignSelf` 附加可系結屬性來覆寫個別專案的 `AlignItems` 屬性：

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    有了這項變更之後，當讀取順序是由左至右時，這個 `Label` 會放在 `FlexLayout` 的左邊緣。

- [@No__t_1](xref:Xamarin.Forms.FlexLayout.JustifyContent)屬性是[`FlexJustify`](xref:Xamarin.Forms.FlexJustify)類型，並指定如何在主要軸上排列專案。 @No__t_0 選項會在所有專案之間，以及在第一個專案和最後一個專案的上方，平均配置所有剩餘的垂直空間。

    如果您使用 `StackLayout`，就必須將每個專案的 `VerticalOptions` 屬性指派給 `CenterAndExpand`，以達到類似的效果。 但是 `CenterAndExpand` 選項會在每個專案之間，配置比第一個專案和最後一個專案還多兩倍的空間。 您可以藉由將 `FlexLayout` 的 `JustifyContent` 屬性設定為 [`SpaceAround`]，模仿 `VerticalOptions` 的 `CenterAndExpand` 選項。

這些 `FlexLayout` 屬性會在下面 **[詳細說明](#bindable-properties)** 可系結屬性一節中的詳細討論。

### <a name="using-flexlayout-for-wrapping-items"></a>使用 FlexLayout 來包裝專案

**[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 範例的 [**相片包裝**] 頁面會示範 `FlexLayout` 如何將其子系包裝至其他資料列或資料行。 XAML 檔案會具現化 `FlexLayout`，並指派它的兩個屬性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.PhotoWrappingPage"
             Title="Photo Wrapping">
    <Grid>
        <ScrollView>
            <FlexLayout x:Name="flexLayout"
                        Wrap="Wrap"
                        JustifyContent="SpaceAround" />
        </ScrollView>

        <ActivityIndicator x:Name="activityIndicator"
                           IsRunning="True"
                           VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

未設定此 `FlexLayout` 的 `Direction` 屬性，因此它具有 `Row` 的預設值，這表示子系是以資料列排列，而主要軸是水準的。

[@No__t_1](xref:Xamarin.Forms.FlexLayout.Wrap)屬性是[`FlexWrap`](xref:Xamarin.Forms.FlexWrap)的列舉類型。 如果有太多專案要放入資料列，則此屬性設定會使專案換行至下一個資料列。

請注意，`FlexLayout` 是 `ScrollView` 的子系。 如果頁面上有太多資料列，則 `ScrollView` 具有 `Vertical` 的預設 `Orientation` 屬性，並允許垂直捲動。

[@No__t_0] 屬性會配置主要軸（水準軸）上的剩餘空間，讓每個專案都以相同的空白空間量括住。

程式碼後置檔案會存取範例相片的集合，並將其新增至 `FlexLayout` 的 `Children` 集合：

```csharp
public partial class PhotoWrappingPage : ContentPage
{
    // Class for deserializing JSON list of sample bitmaps
    [DataContract]
    class ImageList
    {
        [DataMember(Name = "photos")]
        public List<string> Photos = null;
    }

    public PhotoWrappingPage ()
    {
        InitializeComponent ();

        LoadBitmapCollection();
    }

    async void LoadBitmapCollection()
    {
        using (WebClient webClient = new WebClient())
        {
            try
            {
                // Download the list of stock photos
                Uri uri = new Uri("https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json");
                byte[] data = await webClient.DownloadDataTaskAsync(uri);

                // Convert to a Stream object
                using (Stream stream = new MemoryStream(data))
                {
                    // Deserialize the JSON into an ImageList object
                    var jsonSerializer = new DataContractJsonSerializer(typeof(ImageList));
                    ImageList imageList = (ImageList)jsonSerializer.ReadObject(stream);

                    // Create an Image object for each bitmap
                    foreach (string filepath in imageList.Photos)
                    {
                        Image image = new Image
                        {
                            Source = ImageSource.FromUri(new Uri(filepath))
                        };
                        flexLayout.Children.Add(image);
                    }
                }
            }
            catch
            {
                flexLayout.Children.Add(new Label
                {
                    Text = "Cannot access list of bitmap files"
                });
            }
        }

        activityIndicator.IsRunning = false;
        activityIndicator.IsVisible = false;
    }
}
```

以下是執行的程式，以漸進方式從上到下進行滾動：

[![[相片包裝] 頁面](flex-layout-images/PhotoWrapping.png "[相片包裝] 頁面")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>使用 FlexLayout 的頁面配置

Web 設計中有一種標準版面配置，稱為「[_聖聖杯_](https://en.wikipedia.org/wiki/Holy_grail_(web_design))」，因為它是非常理想的版面配置格式，但通常很難實現完美。 版面配置包含頁面頂端的標題和底端的頁尾，同時延伸至頁面的完整寬度。 佔用頁面的中心是主要內容，但通常會在右側的內容和補充資訊（有時稱為「_空白區域」_ ）左邊加上單欄式功能表。 [CSS 彈性方塊版面配置規格的章節5.4.1 版](http://www.w3.org/TR/css-flexbox-1/#order-accessibility)說明如何使用彈性方塊來實現聖聖杯配置。

**[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 範例的 [**聖聖杯版面**配置] 頁面會使用一個嵌套在另一個中的 `FlexLayout`，顯示此配置的簡單實作為。 因為此頁面是針對直向模式的電話所設計，所以內容區域左邊和右邊的區域僅限於50圖元寬：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.HolyGrailLayoutPage"
             Title="Holy Grail Layout">

    <FlexLayout Direction="Column">

        <!-- Header -->
        <Label Text="HEADER"
               FontSize="Large"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center" />

        <!-- Body -->
        <FlexLayout FlexLayout.Grow="1">

            <!-- Content -->
            <Label Text="CONTENT"
                   FontSize="Large"
                   BackgroundColor="Gray"
                   HorizontalTextAlignment="Center"
                   VerticalTextAlignment="Center"
                   FlexLayout.Grow="1" />

            <!-- Navigation items-->
            <BoxView FlexLayout.Basis="50"
                     FlexLayout.Order="-1"
                     Color="Blue" />

            <!-- Aside items -->
            <BoxView FlexLayout.Basis="50"
                     Color="Green" />

        </FlexLayout>

        <!-- Footer -->
        <Label Text="FOOTER"
               FontSize="Large"
               BackgroundColor="Pink"
               HorizontalTextAlignment="Center" />
    </FlexLayout>
</ContentPage>
```

它正在執行：

[![聖聖杯版面配置頁面](flex-layout-images/HolyGrailLayout.png "聖聖杯版面配置頁面")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

左側和右側會以 `BoxView` 呈現導覽和區域。

XAML 檔案中的第一個 `FlexLayout` 具有垂直主要軸，並包含三個在資料行中排列的子系。 這些是標頭、頁面主體和頁尾。 嵌套的 `FlexLayout` 具有水準主要軸，其中有三個子系排列在資料列中。

這個程式會示範三個附加的可系結屬性：

- @No__t_0 附加的可系結屬性是在第一個 `BoxView` 上設定。 此屬性是預設值為0的整數。 您可以使用這個屬性來變更版面配置順序。 開發人員通常會偏好頁面的內容顯示在流覽專案之前的標記中，而不是專案。 將第一個 `BoxView` 的 `Order` 屬性設定為小於其其他兄弟的值，會使其顯示為數據列中的第一個專案。 同樣地，您可以將 `Order` 屬性設定為大於其同級的值，以確保專案最後出現。

- @No__t_0 附加的可系結屬性會設定在兩個 `BoxView` 專案上，以提供50圖元的寬度。 這個屬性的類型是 `FlexBasis`，此結構會定義名為 `FlexBasis` 之類型的靜態屬性，預設值為 `Auto`。 您可以使用 `Basis` 來指定圖元大小或百分比，指出專案在主要軸上所占的空間量。 因為它指定的專案大小是所有後續版面配置的基礎，所以稱為_基礎_。

- @No__t_0 屬性是在嵌套的 `Layout` 上，以及代表內容的 `Label` 子系上設定。 這個屬性的類型是 `float`，預設值是0。 當設定為正值時，沿著主要軸的所有剩餘空間都會配置給該專案，並配置給具有正值 `Grow` 的同級。 空間會依比例分配給值，與 `Grid` 中的星號規格有點相似。

    第一個 `Grow` 附加屬性是在 nested `FlexLayout` 上設定，表示此 `FlexLayout` 會佔用外部 `FlexLayout` 內所有未使用的垂直空間。 第二個 `Grow` 附加屬性是在代表內容的 `Label` 上設定，表示此內容會佔用內部 `FlexLayout` 內所有未使用的水準空間。

    還有一個類似的 `Shrink` 附加可系結屬性，當子系的大小超過 `FlexLayout` 但不想換行時，您可以使用它。

### <a name="catalog-items-with-flexlayout"></a>使用 FlexLayout 的類別目錄專案

**[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 範例中的 [**類別目錄專案**] 頁面類似于[CSS 彈性版面配置方塊規格的第1.1 節中的範例 1](http://www.w3.org/TR/css-flexbox-1/#overview) ，不同之處在于它會顯示水準滾動系列的圖片和三個猴仔的描述。:

[![[類別目錄專案] 頁面](flex-layout-images/CatalogItems.png "[類別目錄專案] 頁面")](flex-layout-images/CatalogItems-Large.png#lightbox)

這三個猴仔中的每一個都是包含在 `Frame` 中的 `FlexLayout`，並提供明確的高度和寬度，而這也是較大 `FlexLayout` 的子系。 在此 XAML 檔案中，`FlexLayout` 子系的大部分屬性都是以樣式指定，但其中一個是隱含樣式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CatalogItemsPage"
             Title="Catalog Items">
    <ContentPage.Resources>
        <Style TargetType="Frame">
            <Setter Property="BackgroundColor" Value="LightYellow" />
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="Margin" Value="10" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="Margin" Value="0, 4" />
        </Style>

        <Style x:Key="headerLabel" TargetType="Label">
            <Setter Property="Margin" Value="0, 8" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="Blue" />
        </Style>

        <Style TargetType="Image">
            <Setter Property="FlexLayout.Order" Value="-1" />
            <Setter Property="FlexLayout.AlignSelf" Value="Center" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="White" />
            <Setter Property="BackgroundColor" Value="Green" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}"
                           WidthRequest="240"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

@No__t_0 的隱含樣式包括 `Flexlayout` 的兩個附加可系結屬性的設定：

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

@No__t_11 的 `Order` 設定會使 `Image` 元素在每個嵌套 `FlexLayout` 視圖中先顯示，而不論其在子集合中的位置為何。 @No__t_1 的 `AlignSelf` 屬性會導致 `Image` 在 `FlexLayout` 中置中。 這會覆寫 `AlignItems` 屬性的設定，其預設值為 `Stretch`，這表示 `Label` 和 `Button` 子系會延展至 `FlexLayout` 的完整寬度。

在三個 `FlexLayout` 視圖的每一個中，空白 `Label` 會在 `Button` 之前，但其 `Grow` 設定為1。 這表示會將所有額外的垂直空間配置給這個空白 `Label`，這樣會有效地將 `Button` 推送至底部。

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>可系結的屬性詳細資料

既然您已瞭解 `FlexLayout` 的一些常見應用程式，就可以更詳細地探索 `FlexLayout` 的屬性。
`FlexLayout` 會定義六個可系結的屬性，您可以在程式碼或 XAML 中，于 `FlexLayout` 本身設定，以控制方向和對齊。 （其中一個屬性[`Position`](xref:Xamarin.Forms.FlexLayout.Position)，本文並未涵蓋）。

您可以使用 **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 範例的**實驗**頁面，試驗五個剩餘的可系結屬性。 此頁面可讓您在 `FlexLayout` 中新增或移除子系，以及設定五個可系結屬性的組合。 @No__t_0 的所有子系都是各種色彩和大小的 `Label` 視圖，而且 `Text` 屬性會設定為對應于 `Children` 集合中位置的數位。

當程式啟動時，五個 `Picker` views 會顯示這五個 `FlexLayout` 屬性的預設值。 靠近畫面底部的 `FlexLayout` 包含三個子系：

[![實驗頁面：預設值](flex-layout-images/ExperimentDefault.png "實驗頁面-預設")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

每個 `Label` 視圖都有灰色背景，顯示配置給該 `Label` 在 `FlexLayout` 內的空間。 @No__t_0 本身的背景為 Alice 藍色。 它會佔用頁面的整個底部區域，但左邊和右邊的小邊界除外。

<a name="direction" />

### <a name="the-direction-property"></a>Direction 屬性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.Direction)屬性的類型是[`FlexDirection`](xref:Xamarin.Forms.FlexDirection)，這是具有四個成員的列舉：

- `Column`
- `ColumnReverse` （或 XAML 中的「資料行反向」）
- `Row`，預設值
- `RowReverse` （在 XAML 中則為「資料列反轉」）

在 XAML 中，您可以使用小寫、大寫或混合大小寫中的列舉成員名稱來指定這個屬性的值，也可以使用括弧中所顯示的兩個額外字串，與 CSS 指標相同。 （「資料行反向」和「資料列反轉」字串是在 XAML 剖析器所使用的[`FlexDirectionTypeConverter`](xref:Xamarin.Forms.FlexDirectionTypeConverter)類別中定義）。

以下是顯示（由左至右）、`Row` 方向、`Column` 方向和 `ColumnReverse` 方向的**實驗**頁面：

[![實驗頁面：方向](flex-layout-images/ExperimentDirection.png "實驗頁面方向")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

請注意，針對 `Reverse` 選項，專案會從右邊或底部開始。

<a name="wrap" />

### <a name="the-wrap-property"></a>Wrap 屬性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.Wrap)屬性的類型是[`FlexWrap`](xref:Xamarin.Forms.FlexWrap)，這是具有三個成員的列舉：

- `NoWrap`，預設值
- `Wrap`
- `Reverse` （或在 XAML 中「換行」）

從左至右，這些畫面會顯示12個子系的 `NoWrap`、`Wrap` 和 `Reverse` 選項：

[![實驗頁面： Wrap](flex-layout-images/ExperimentWrap.png "實驗頁面-Wrap")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

當 `Wrap` 屬性設定為 `NoWrap` 而主要軸受到限制（如此程式所示），而且主要軸的寬度不是足以容納所有子系時，`FlexLayout` 會嘗試讓專案變小，如 iOS 螢幕擷取畫面所示。 您可以使用[`Shrink`](#shrink)附加的可系結屬性來控制專案的 shrinkness。

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>JustifyContent 屬性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.JustifyContent)屬性的類型是[`FlexJustify`](xref:Xamarin.Forms.FlexJustify)，這是具有六個成員的列舉：

- `Start` （或 XAML 中的「flex-開始」），預設值為
- `Center`
- `End` （或在 XAML 中以「flex-結束」）
- `SpaceBetween` （或「XAML 中的空格」）
- `SpaceAround` （或在 XAML 中以空格括住）
- `SpaceEvenly`

這個屬性會指定主要軸上的專案間距如何，這是此範例中的水準軸：

[![實驗頁面：對齊內容](flex-layout-images/ExperimentJustifyContent.png "實驗頁面-調整內容")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

在這三個螢幕擷取畫面中，`Wrap` 屬性都設定為 [`Wrap`]。 @No__t_0 預設值會顯示在先前的 Android 螢幕擷取畫面中。 這裡的 iOS 螢幕擷取畫面顯示 `Center` 選項：所有專案都會移至中央。 以 [字組] 開頭的其他三個選項，`Space` 設定項目未佔用的額外空間。 `SpaceBetween` 會在專案之間平均配置空間; `SpaceAround` 會在每個專案周圍放置相等的空間，而 `SpaceEvenly` 會在每個專案和第一個專案之前，以及在資料列的最後一個專案之後放置相等的空間。

<a name="align-items" />

### <a name="the-alignitems-property"></a>AlignItems 屬性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.AlignItems)屬性的類型是[`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems)，這是具有四個成員的列舉：

- `Stretch`，預設值
- `Center`
- `Start` （或 XAML 中的「flex-開始」）
- `End` （或在 XAML 中以「flex-結束」）

這是兩個屬性（另一個[`AlignContent`](#align-content)）中的其中一個，指出子系在交叉軸上的對齊方式。 在每個資料列中，子系會伸展（如先前的螢幕擷取畫面所示），或在每個專案的開始、置中或結束時對齊，如下列三個螢幕擷取畫面所示：

[![實驗頁面：對齊專案](flex-layout-images/ExperimentAlignItems.png "實驗頁面-對齊專案")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

在 iOS 螢幕擷取畫面中，會對齊所有子系的頂端。 在 Android 螢幕擷取畫面中，專案會根據最高的子系垂直置中。 在 UWP 螢幕擷取畫面中，會對齊所有專案的底端。

針對任何個別專案，可以使用[`AlignSelf`](#align-self)附加的可系結屬性來覆寫 `AlignItems` 設定。

<a name="align-content" />

### <a name="the-aligncontent-property"></a>AlignContent 屬性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.AlignContent)屬性的類型是[`FlexAlignContent`](xref:Xamarin.Forms.FlexAlignContent)，這是具有七個成員的列舉：

- `Stretch`，預設值
- `Center`
- `Start` （或 XAML 中的「flex-開始」）
- `End` （或在 XAML 中以「flex-結束」）
- `SpaceBetween` （或「XAML 中的空格」）
- `SpaceAround` （或在 XAML 中以空格括住）
- `SpaceEvenly`

如同 `AlignItems`，`AlignContent` 屬性也會對齊跨軸上的子系，但會影響整個資料列或資料行：

[![實驗頁面：對齊內容](flex-layout-images/ExperimentAlignContent.png "實驗頁面-對齊內容")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

在 iOS 螢幕擷取畫面中，這兩個數據列位於頂端;在 Android 螢幕擷取畫面中，它們是在中央;而在 UWP 螢幕擷取畫面中，它們位於底部。 您也可以利用各種方式來分隔資料列：

[![實驗頁面：對齊內容2](flex-layout-images/ExperimentAlignContent2.png "實驗頁面-對齊內容2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

只有一個資料列或資料行時，`AlignContent` 不會有任何作用。

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>附加的可系結屬性詳細資料

`FlexLayout` 定義五個附加的可系結屬性。 這些屬性是在 `FlexLayout` 的子系上設定，而且僅適用于該特定的子系。

<a name="align-self" />

### <a name="the-alignself-property"></a>AlignSelf 屬性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty)附加的可系結屬性屬於類型[`FlexAlignSelf`](xref:Xamarin.Forms.FlexAlignContent)，這是具有五個成員的列舉：

- `Auto`，預設值
- `Stretch`
- `Center`
- `Start` （或 XAML 中的「flex-開始」）
- `End` （或在 XAML 中以「flex-結束」）

對於 `FlexLayout` 的任何個別子系，此屬性設定會覆寫 `FlexLayout` 本身上設定的[`AlignItems`](#align-items)屬性。 @No__t_0 的預設設定表示使用 `AlignItems` 設定。

針對名為 `label` （或範例）的 `Label` 元素，您可以在程式碼中設定 `AlignSelf` 屬性，如下所示：

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

請注意，沒有 `Label` 的 `FlexLayout` 父系的參考。 在 XAML 中，您可以設定屬性，如下所示：

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>Order 屬性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.OrderProperty)屬性的類型為 `int`。 預設值為 0。

@No__t_0 屬性可讓您變更 `FlexLayout` 子系的排列順序。 通常，`FlexLayout` 的子系排列順序，與它們出現在 `Children` 集合中的順序相同。 您可以在一或多個子系上，將 [`Order` 附加的可系結屬性] 設定為非零的整數值，以覆寫此順序。 然後，`FlexLayout` 會根據每個子系上 `Order` 屬性的設定來排列其子系，但具有相同 `Order` 設定的子系會依照出現在 `Children` 集合中的順序排列。

### <a name="the-basis-property"></a>基本屬性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.BasisProperty)附加的可系結屬性會指出配置給主要軸上 `FlexLayout` 子系的空間量。 [@No__t_0] 屬性所指定的大小是沿著父 `FlexLayout` 主要軸的大小。 因此，當子系排列在資料列時，`Basis` 會指出子系的寬度，或當子系在資料行中排列時的高度。

@No__t_0 屬性的類型為[`FlexBasis`](xref:Xamarin.Forms.FlexBasis)的結構。 大小可以用裝置獨立單位或 `FlexLayout` 大小的百分比來指定。 @No__t_0 屬性的預設值為靜態屬性 `FlexBasis.Auto`，這表示會使用子系的要求寬度或高度。

在程式碼中，您可以將名為 `label` `Label` 的 `Basis` 屬性設定為40裝置獨立單位，如下所示：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

@No__t_0 的函式的第二個引數名為 `isRelative`，並指出大小是否為相對（`true`）或絕對（`false`）。 引數的預設值為 `false`，因此您也可以使用下列程式碼：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

已定義從 `float` 到 `FlexBasis` 的隱含轉換，因此您可以更進一步地簡化它：

```csharp
FlexLayout.SetBasis(label, 40);
```

您可以將大小設為 `FlexLayout` 父系的 25%，如下所示：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

這個小數值的範圍必須介於0到1之間。

在 XAML 中，您可以使用數位作為裝置獨立單位的大小：

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

或者，您可以指定範圍介於 0% 到 100% 之間的百分比：

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

**[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 範例的 [**基礎實驗**] 頁面可讓您使用 `Basis` 屬性進行實驗。 頁面會以交替背景和前景色彩顯示五個 `Label` 元素的已包裝資料行。 兩個 `Slider` 的元素可讓您指定第二個和第四個 `Label` 的 `Basis` 值：

[![[基礎實驗] 頁面](flex-layout-images/BasisExperiment.png "[基礎實驗] 頁面")](flex-layout-images/BasisExperiment-Large.png#lightbox)

左側的 iOS 螢幕擷取畫面顯示兩個 `Label` 元素在與裝置無關的單位中具有高度。 [Android] 畫面會顯示它們的高度，是 `FlexLayout` 總高度的一小部分。 如果 `Basis` 設定為 100%，則子系是 `FlexLayout` 的高度，並會換行至下一個資料行，並佔用該資料行的整個高度，如同 UWP 螢幕擷取畫面所示：它會顯示為五個子系排列在資料列中但實際上是以五個數據行排列。

### <a name="the-grow-property"></a>成長屬性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.GrowProperty)附加可系結屬性的類型為 `int`。 預設值為0，且值必須大於或等於0。

當 [`Wrap`] 屬性設定為 [`NoWrap`]，而 [子系] 的資料列寬度小於 `FlexLayout` 的寬度，或子系的資料行具有比 `FlexLayout` 更短的高度時，`Grow` 屬性會扮演角色。 @No__t_0 屬性會指出如何分配子系之間的剩餘空間。

在 [**擴大實驗**] 頁面中，交替色彩的五個 `Label` 元素會排列在一個資料行中，而兩個 `Slider` 元素可讓您調整第二個和第四個 `Label` 的 `Grow` 屬性。 最左邊的 iOS 螢幕擷取畫面顯示預設 `Grow` 的屬性0：

[![[擴大實驗] 頁面](flex-layout-images/GrowExperiment.png "[擴大實驗] 頁面")](flex-layout-images/GrowExperiment-Large.png#lightbox)

如果有任何一個子系指定了正 `Grow` 值，則該子系會佔用所有剩餘的空間，如同 Android 螢幕擷取畫面所示。 這個空間也可以配置在兩個以上的子系中。 在 UWP 螢幕擷取畫面中，第二個 `Label` 的 `Grow` 屬性會設定為0.5，而第四個 `Label` 的 `Grow` 屬性是1.5，這會提供第四個 `Label` 三倍的剩餘空間做為第二個 `Label`。

子視圖使用該空間的方式，取決於特定類型的子系。 若為 `Label`，可以使用屬性 `HorizontalTextAlignment` 和 `VerticalTextAlignment`，將文字放在 `Label` 的總空間內。

<a name="shrink" />

### <a name="the-shrink-property"></a>Shrink 屬性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.ShrinkProperty)附加可系結屬性的類型為 `int`。 預設值為1，且值必須大於或等於0。

當 [`Wrap`] 屬性設定為 [`NoWrap`]，而子系的資料列的匯總寬度大於 `FlexLayout` 的寬度，或子系的單一資料行的匯總高度大於的高度時，`Shrink` 屬性會扮演角色`FlexLayout`。 一般來說，`FlexLayout` 會藉由 constricting 其大小來顯示這些子系。 @No__t_0 屬性可以指出哪些子系在以其完整大小顯示時具有優先權。

[**縮小實驗**] 頁面會建立一個 `FlexLayout`，其中包含五個 `Label` 子系的單一資料列，而這需要的空間比 `FlexLayout` 的寬度多。 左側的 iOS 螢幕擷取畫面會顯示預設值為1的所有 `Label` 元素：

[![[壓縮實驗] 頁面](flex-layout-images/ShrinkExperiment.png "[壓縮實驗] 頁面")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

在 Android 螢幕擷取畫面中，第二個 `Label` 的 `Shrink` 值會設為0，而且 `Label` 會以完整寬度顯示。 此外，第四個 `Label` 會獲得大於一的 `Shrink` 值，且已壓縮。 UWP 螢幕擷取畫面會顯示指定 `Shrink` 值為0的 `Label` 專案，以允許它們以完整大小顯示（如果可能的話）。

您可以同時設定 `Grow` 和 `Shrink` 值，以配合匯總子大小有時可能小於或有時大於 `FlexLayout` 大小的情況。

## <a name="css-styling-with-flexlayout"></a>使用 FlexLayout 的 CSS 樣式

您可以使用與 `FlexLayout` 連接的 Xamarin. Forms 3.0 中所引進的[CSS 樣式](~/xamarin-forms/user-interface/styles/css/index.md)功能。 **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 範例的 [ **CSS 類別目錄專案**] 頁面會複製 [**類別目錄專案**] 頁面的版面配置，但有許多樣式的 css 樣式表單：

[![[CSS 類別目錄專案] 頁面](flex-layout-images/CssCatalogItems.png "[CSS 類別目錄專案] 頁面")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

原始**CatalogItemsPage**檔的 `Resources` 區段中有五個 `Style` 定義，其中有15個 `Setter` 物件。 在**CssCatalogItemsPage**中，已縮減為只有四個 `Setter` 物件的兩個 `Style` 定義。 這些樣式會針對 [Xamarin form CSS 樣式] 功能目前不支援的屬性補充 CSS 樣式表單：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CssCatalogItemsPage"
             Title="CSS Catalog Items">
    <ContentPage.Resources>
        <StyleSheet Source="CatalogItemsStyles.css" />

        <Style TargetType="Frame">
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey" StyleClass="header" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey" StyleClass="header" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey" StyleClass="header" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

CSS 樣式表單會在 `Resources` 區段的第一行中參考：

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

另外也請注意，這三個專案中的兩個元素都包含 `StyleClass` 設定：

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

這些會參考**CatalogItemsStyles**樣式表單中的選取器：

```css
frame {
    width: 300;
    height: 480;
    background-color: lightyellow;
    margin: 10;
}

label {
    margin: 4 0;
}

label.header {
    margin: 8 0;
    font-size: large;
    color: blue;
}

label.empty {
    flex-grow: 1;
}

image {
    height: 180;
    order: -1;
    align-self: center;
}

button {
    font-size: large;
    color: white;
    background-color: green;
}
```

這裡會參考數個 `FlexLayout` 附加的可系結屬性。 在 `label.empty` 選取器中，您會看到 `flex-grow` 屬性，其樣式為空的 `Label`，以在 `Button` 上方提供一些空格。 @No__t_0 選取器包含 `order` 屬性和 `align-self` 屬性，兩者都對應至 `FlexLayout` 附加的可系結屬性。

您已瞭解您可以直接在 `FlexLayout` 上設定屬性，而且可以在 `FlexLayout` 的子系上設定附加的可系結屬性。 或者，您可以使用傳統的 XAML 樣式或 CSS 樣式，間接設定這些屬性。 重要的是瞭解並瞭解這些屬性。 這些屬性可讓 `FlexLayout` 真正的彈性。

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout 與 Xamarin. 大學

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin. 表單 3.0 Flex 版面配置影片**

## <a name="related-links"></a>相關連結

- [FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)
