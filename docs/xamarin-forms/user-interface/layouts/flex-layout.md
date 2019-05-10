---
title: Xamarin.Forms FlexLayout
description: 您可以使用 FlexLayout 堆疊或包裝的子檢視集合。
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 512aa2b54ec22acf8308b3452bfeee2318097b57
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978138"
---
# <a name="the-xamarinforms-flexlayout"></a>Xamarin.Forms FlexLayout

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)

_您可以使用 FlexLayout 堆疊或包裝的子檢視集合。_

Xamarin.Forms [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout)是 Xamarin.Forms 3.0 版的新功能。 它根據 CSS[彈性的方塊版面配置模組](http://www.w3.org/TR/css-flexbox-1/)，稱為_flex 配置_或_彈性方塊_，因此呼叫，因為它包含許多彈性的選項，來排列子系在版面配置。

`FlexLayout` 類似於 Xamarin.Forms [ `StackLayout` ](~/xamarin-forms/user-interface/layouts/stack-layout.md) ，因為它可以在堆疊中排列其子系水平和垂直大小。 不過，`FlexLayout`也能夠包裝及其子系，如果有太多，無法納入單一資料列或資料行，而且也有許多選項方向、 對齊和調整以各種螢幕大小。

`FlexLayout` 衍生自[ `Layout<View>` ](xref:Xamarin.Forms.Layout`1)並繼承[ `Children` ](xref:Xamarin.Forms.Layout`1.Children)屬性的型別`IList<View>`。

`FlexLayout` 定義六個公用的可繫結屬性和五個附加的可繫結屬性會影響大小、 方向與其子元素的對齊方式。 (如果您不熟悉附加可繫結的屬性，請參閱文章 **[連接屬性](~/xamarin-forms/xaml/attached-properties.md)**。)在下列各節中詳細說明這些屬性上 **[可繫結的屬性詳細](#bindable-properties)** 並 **[詳細附加的可繫結屬性](#attached-properties)**. 不過，這篇文章開頭部分上一節 **[常見使用案例](#common-scenarios)** 的`FlexLayout`非正式地描述其中許多屬性。 發行項的結束時，您將了解如何結合`FlexLayout`具有[CSS 樣式表](~/xamarin-forms/user-interface/styles/css/index.md)。

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>常見使用案例

**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 程式範例包含數個頁面會示範一些常見的用法`FlexLayout`，並可讓您嘗試使用其屬性。

### <a name="using-flexlayout-for-a-simple-stack"></a>使用簡單的堆疊 FlexLayout

**簡單的堆疊**頁面上會顯示如何`FlexLayout`可以代替`StackLayout`但更簡單的標記。 在此範例中的所有項目會定義在 XAML 頁面。 `FlexLayout`包含四個子系：

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

以下是在 iOS、 Android 和通用 Windows 平台上執行該頁面：

[![簡單堆疊頁](flex-layout-images/SimpleStack.png "簡單堆疊頁面")](flex-layout-images/SimpleStack-Large.png#lightbox)

三個屬性`FlexLayout`如下所示**SimpleStackPage.xaml**檔案：

- [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction)屬性設定為值[ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection)列舉型別。 預設為 `Row`。 將屬性設定為`Column`會導致的子系`FlexLayout`排列成單一資料行的項目。

    當中的項目`FlexLayout`會排列在資料行中，`FlexLayout`即具有垂直_主軸_和水平_交叉軸_。

- [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems)屬性的類型是[ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems)和指定的項目上的交叉軸對齊的方式。 `Center`選項可讓每個項目水平置中對齊。

    如果您使用`StackLayout`而非`FlexLayout`針對此工作，您會置中的所有項目指派`HorizontalOptions`屬性的每個項目`Center`。 `HorizontalOptions`屬性不適合的子系`FlexLayout`，但單一`AlignItems`屬性相同的目標的完成度。 如果您需要您可以使用`AlignSelf`附加可繫結的屬性，來覆寫`AlignItems`個別項目的屬性：

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    這項變更，此一`Label`位於左邊緣`FlexLayout`時的讀取順序是由左到右。

- [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent)屬性的類型是[ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)，和指定的項目主要軸上的排列方式。 `SpaceEvenly`選項會配置所有剩餘的垂直空間之間的所有項目、 平均和第一個項目，上下最後一個項目。

    如果您使用`StackLayout`，您必須指派`VerticalOptions`屬性的每個項目`CenterAndExpand`來達到類似的效果。 但`CenterAndExpand`選項會配置每個項目比第一個項目之前和之後的最後一個項目之間的兩倍空間。 您可以模擬`CenterAndExpand`的選項`VerticalOptions`藉由設定`JustifyContent`屬性`FlexLayout`到`SpaceAround`。

這些`FlexLayout`屬性中一節中的更詳細地討論 **[可繫結的屬性詳細](#bindable-properties)** 如下。

### <a name="using-flexlayout-for-wrapping-items"></a>使用 FlexLayout 包裝項目

**相片包裝** 頁 **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)**  範例會示範如何`FlexLayout`可以換行到額外的資料列或資料行及其子系。 XAML 檔案會具現化`FlexLayout`並指派它的兩個屬性：

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

`Direction`屬性的`FlexLayout`未設定，使其具有的預設設定`Row`，這表示子系會排列在資料列，而主要軸是水平。

[ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap)屬性是列舉型別的[ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap)。 如果有太多的項目，以符合資料列上，此屬性設定會導致要包裝至下一個資料列的項目。

請注意，`FlexLayout`子系的`ScrollView`。 如果有太多的資料列，以符合在頁面上，則`ScrollView`的預設值`Orientation`屬性`Vertical`並允許垂直捲動。

`JustifyContent`屬性配置主要軸 （水平軸） 上的剩餘空間，使每個項目會圍繞著相同的空白空間量。

程式碼後置檔案存取範例相片集合，並將其加入`Children`的集合`FlexLayout`:

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

以下是程式執行，以漸進方式捲動從上到下：

[![相片文繞圖頁面](flex-layout-images/PhotoWrapping.png "相片文繞圖頁面")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>頁面配置與 FlexLayout

在呼叫的 web 設計沒有標準的版面配置[_所見所聞_](https://en.wikipedia.org/wiki/Holy_grail_(web_design))因為它是非常理想，但通常很難了解與完美的配置格式。 版面配置是由在頁面頂端的標頭和頁尾，以在底部，這兩個延伸至完整頁面的寬度所組成。 佔用頁面的中心是主要的內容，但通常與左側的內容和增補資訊的單欄式功能表 (有時稱為_擱置在一旁_區域) 右邊。 [CSS 彈性方塊版面配置規格 5.4.1 節](http://www.w3.org/TR/css-flexbox-1/#order-accessibility)將告訴您如何實現所見所聞版面配置，以彈性方塊。

**所見所聞版面配置** 頁 **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 範例會示範一個簡單的實作，使用其中一個此版面配置的`FlexLayout`巢狀方式置於另一個。 因為此頁面專為手機在直向模式中，左側和右側的內容區域的區域只能有 50 像素：

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

以下執行：

[![所見所聞版面配置頁](flex-layout-images/HolyGrailLayout.png "所見所聞版面配置頁")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

瀏覽] 和 [另行區域，以呈現`BoxView`左側及右側。

第一個`FlexLayout`在 XAML 檔案有垂直的主軸，且包含三個子系排列在資料行。 這些是標頭、 本文 頁面上，以及頁尾。 巢狀`FlexLayout`水平的主要軸具有三個子系排列在資料列。

此程式示範三個附加的可繫結屬性：

- `Order`附加的可繫結屬性設定在第一個`BoxView`。 這個屬性是預設值為 0 的整數。 若要變更版面配置順序，您可以使用這個屬性。 通常開發人員偏好使用才會出現在標記之前的瀏覽項目頁面的內容，並保留項目。 設定`Order`屬性，在第一個`BoxView`設成值小於其同層級會使它顯示為資料列中的第一個項目。 同樣地，您可以確保所設定的項目出現最後一個`Order`屬性設為大於其同層級值。

- `Basis`附加可繫結的屬性已設定兩個`BoxView`為其提供 50 個像素寬度的項目。 此屬性的類型是`FlexBasis`，定義類型的靜態屬性的結構`FlexBasis`名為`Auto`，這是預設值。 您可以使用`Basis`指定像素大小或百分比來指出多少空間項目會佔用主要軸上。 它會呼叫_基礎_因為它會指定所有後續的版面配置之基礎的項目大小。

- `Grow`屬性設定在巢狀`Layout`然後在`Label`代表內容的子系。 此屬性的類型是`float`且具有預設值為 0。 設為正值時，所有剩餘的空間，在主要軸配置該項目，並具有正值的同層級`Grow`。 有點像是星狀規格中的值，按比例配置空間`Grid`。

    第一個`Grow`附加的屬性設定在巢狀`FlexLayout`，這表示這個`FlexLayout`是所佔用的所有未使用垂直空間內外部`FlexLayout`。 第二個`Grow`附加的屬性設定`Label`代表內容，指出此內容以佔用內的所有未使用水平空間內部`FlexLayout`。

    另外還有類似`Shrink`附加可繫結的屬性，您可以使用當子系的大小超過大小`FlexLayout`但不是想要使用換行。

### <a name="catalog-items-with-flexlayout"></a>FlexLayout 類別目錄項目

**類別目錄項目** 頁面 **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 範例是類似於[CSS Flex 版面配置 方塊規格區段1.1版中的範例1](http://www.w3.org/TR/css-flexbox-1/#overview)不同之處在於它會顯示可水平捲動的一連串的圖片和三個猴仔的描述：

[![目錄項目頁面](flex-layout-images/CatalogItems.png "目錄項目頁面")](flex-layout-images/CatalogItems-Large.png#lightbox)

每三個猴仔都`FlexLayout`中包含`Frame`，明確的高度和寬度，且這也是較大的子系`FlexLayout`。 在此 XAML 檔案中，大多數的屬性`FlexLayout`樣式，但當中的隱含樣式中所指定的子系：

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

隱含樣式`Image`包含兩個連結的可繫結內容的設定`Flexlayout`:

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

`Order`設定&ndash;1，會導致`Image`先顯示每個巢狀中的項目`FlexLayout`不論其位置的子系集合內的檢視。 `AlignSelf`的屬性`Center`會導致`Image`內的中央`FlexLayout`。 這會覆寫的設定`AlignItems`屬性，其預設值的`Stretch`，亦即，`Label`並`Button`子系會縮放為完整寬度的`FlexLayout`。

在這三`FlexLayout`檢視，空白`Label`前面`Button`，但有`Grow`設定為 1。 這表示所有的多餘垂直空間，配置給此空白`Label`，其有效地將推入`Button`至底部。

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>在 詳細資料可繫結的屬性

既然您已了解的一些常見應用`FlexLayout`，屬性`FlexLayout`可以更詳細地探索。
`FlexLayout` 定義您在設定的六個可繫結屬性`FlexLayout`本身，程式碼或 XAML，來控制方向和對齊方式。 (這些屬性，其中[ `Position` ](xref:Xamarin.Forms.FlexLayout.Position)，未涵蓋在本文中。)

您可以試驗的五種剩餘可繫結使用的屬性 **實驗** 頁 **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 範例。 此頁面可讓您新增或移除子系從`FlexLayout`，並設定五個可繫結的屬性組合。 所有子系`FlexLayout`都`Label`各種色彩和大小，檢視與`Text`屬性設定為在其位置對應數字`Children`集合。

當程式啟動時，五`Picker`檢視會顯示這些五的預設值`FlexLayout`屬性。 `FlexLayout`螢幕底部包含三個子系：

[![[實驗] 頁面中：預設值](flex-layout-images/ExperimentDefault.png "實驗 頁面上的預設值")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

每個`Label`檢視有所配置的空間會顯示灰色背景`Label`內`FlexLayout`。 背景的`FlexLayout`本身是艾莉斯藍。 在左邊和右邊的小邊界除外，它就會佔用整個下方頁面的區域。

<a name="direction" />

### <a name="the-direction-property"></a>方向屬性

[ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction)屬性的類型是[ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection)，含有四個成員的列舉類型：

- `Column`
- `ColumnReverse` （或者 「 資料行反轉 」 在 XAML 中）
- `Row`預設值
- `RowReverse` （或者 「 資料列反轉 」 在 XAML 中）

在 XAML 中，您可以指定以小寫字母、 大寫字母、 使用的列舉型別成員名稱的這個屬性的值或混合式的案例，或者您可以使用 CSS 指標相同的括弧中顯示的兩個其他字串。 (中所定義的 「 資料行反轉 」 和 「 反轉資料列 」 字串[ `FlexDirectionTypeConverter` ](xref:Xamarin.Forms.FlexDirectionTypeConverter) XAML 剖析器使用的類別。)

以下是**實驗**（從左到右） 顯示頁面`Row`方向`Column`方向和`ColumnReverse`方向：

[![[實驗] 頁面中：方向](flex-layout-images/ExperimentDirection.png "實驗 頁面上的方向")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

請注意，`Reverse`選項，啟動在右方或下方的項目。

<a name="wrap" />

### <a name="the-wrap-property"></a>自動換行屬性

[ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap)屬性的類型是[ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap)，含有三個成員的列舉類型：

- `NoWrap`預設值
- `Wrap`
- `Reverse` （或者 「 換行反轉 」 在 XAML 中）

從左到右，顯示這些畫面`NoWrap`，`Wrap`和`Reverse`12 的子系的選項：

[![[實驗] 頁面中：包裝](flex-layout-images/ExperimentWrap.png "[實驗] 頁面中-換行")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

當`Wrap`屬性設定為`NoWrap`主座標軸會受到限制 （如同這個程式中），以及主座標軸不是寬度或高度不足以容納所有的子系，`FlexLayout`嘗試縮小項目，為 iOS 螢幕擷取畫面示範。 您可以控制將項目的 shrinkness [ `Shrink` ](#shrink)附加可繫結的屬性。

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>JustifyContent 屬性

[ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent)屬性的類型是[ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)，含有六個成員的列舉類型：

- `Start` （或 「 彈性起始 」 在 XAML 中），預設值
- `Center`
- `End` （或者 「 flex 端 」 在 XAML 中）
- `SpaceBetween` （或者 「 空間-between"中 XAML）
- `SpaceAround` （或者 「 空間的因應措施 」 在 XAML 中）
- `SpaceEvenly`

這個屬性會指定如何將項目間距主軸，也就是在此範例中的水平軸上：

[![[實驗] 頁面中：左右對齊內容](flex-layout-images/ExperimentJustifyContent.png "實驗 頁面上的合理解釋的內容")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

在所有三個螢幕擷取畫面`Wrap`屬性設定為`Wrap`。 `Start`預設會顯示在 Android 上的螢幕擷取畫面。 IOS 螢幕擷取畫面這裡顯示`Center`選項： 所有項目會移至中央。 三個其他選項以文字為開頭`Space`配置額外的項目所未佔用的空間。 `SpaceBetween` 配置的空間項目; 之間的平均`SpaceAround` puts 等於每個項目，周圍的空間時`SpaceEvenly`puts 等於之間每個項目，第一個項目之前和最後一個資料列上的項目之後的空間。

<a name="align-items" />

### <a name="the-alignitems-property"></a>AlignItems 屬性

[ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems)屬性的類型是[ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems)，含有四個成員的列舉類型：

- `Stretch`預設值
- `Center`
- `Start` （或者 「 彈性起始 」 在 XAML 中）
- `End` （或者 「 flex 端 」 在 XAML 中）

這是兩個屬性其中之一 (其他正在[ `AlignContent` ](#align-content))，表示子系上的交叉軸對齊的方式。 在每一列中，子系是 （如先前的螢幕擷取畫面所示），便會自動縮放，或對齊開始、 中心或結尾的每個項目，如下列的三個螢幕擷取畫面所示：

[![[實驗] 頁面中：將項目對齊](flex-layout-images/ExperimentAlignItems.png "[實驗] 頁面中-對齊項目")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

在 iOS 螢幕擷取畫面中，所有子系的頂端對齊。 在 Android 螢幕擷取畫面中，項目是垂直置中對齊的最高的子系為基礎。 在 UWP 螢幕擷取畫面中，會對齊所有項目的下的對齊。

針對任何個別的項目`AlignItems`設定，可以使用覆寫[ `AlignSelf` ](#align-self)附加可繫結的屬性。

<a name="align-content" />

### <a name="the-aligncontent-property"></a>AlignContent 屬性

[ `AlignContent` ](xref:Xamarin.Forms.FlexLayout.AlignContent)屬性的類型是[ `FlexAlignContent` ](xref:Xamarin.Forms.FlexAlignContent)，含有七個成員的列舉類型：

- `Stretch`預設值
- `Center`
- `Start` （或者 「 彈性起始 」 在 XAML 中）
- `End` （或者 「 flex 端 」 在 XAML 中）
- `SpaceBetween` （或者 「 空間-between"中 XAML）
- `SpaceAround` （或者 「 空間的因應措施 」 在 XAML 中）
- `SpaceEvenly`

像是`AlignItems`，則`AlignContent`屬性也會對齊交叉軸上的子系，但會影響整個資料列或資料行：

[![[實驗] 頁面中：內容對齊](flex-layout-images/ExperimentAlignContent.png "實驗 頁面上的內容對齊。")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

IOS 螢幕擷取畫面，這兩個資料列會在頂端;它們在 center，在 Android 的螢幕擷取畫面也在 UWP 螢幕擷取畫面底部。 資料列也以各種方式設定間距：

[![[實驗] 頁面中：對齊內容 2](flex-layout-images/ExperimentAlignContent2.png "[實驗] 頁面中-對齊內容 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

`AlignContent`只有一個資料列或資料行時，沒有任何作用。

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>在詳細資料中附加的可繫結屬性

`FlexLayout` 定義五個附加的可繫結屬性。 這些屬性設定上的子系`FlexLayout`和只屬於該特定的子系。

<a name="align-self" />

### <a name="the-alignself-property"></a>AlignSelf 屬性

[ `AlignSelf` ](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty)附加可繫結的屬性屬於型別[ `FlexAlignSelf` ](xref:Xamarin.Forms.FlexAlignContent)，含有五個成員的列舉類型：

- `Auto`預設值
- `Stretch`
- `Center`
- `Start` （或者 「 彈性起始 」 在 XAML 中）
- `End` （或者 「 flex 端 」 在 XAML 中）

針對任何個別的子系`FlexLayout`，此屬性設定會覆寫[ `AlignItems` ](#align-items)上設定屬性`FlexLayout`本身。 預設值`Auto`表示使用`AlignItems`設定。

針對`Label`名為的項目`label`（或範例），您可以設定`AlignSelf`如下的程式碼中的屬性：

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

請注意，有沒有參考`FlexLayout`的父代`Label`。 在 XAML，您可以設定的屬性，像這樣：

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>Order 屬性

[ `Order` ](xref:Xamarin.Forms.FlexLayout.OrderProperty)屬性的類型是`int`。 預設值為 0。

`Order`屬性可讓您變更順序的子系`FlexLayout`排列。 通常的子系`FlexLayout`排列順序相同，它們會出現在`Children`集合。 您可以藉由設定覆寫這個順序`Order`附加可繫結的屬性設定為一個或多個子系的非零的整數值。 `FlexLayout`然後排列的設定及其子系`Order`屬性上每一個子系，但具有相同的子系`Order`設定會以它們出現在順序排列`Children`集合。

### <a name="the-basis-property"></a>基礎屬性

[ `Basis` ](xref:Xamarin.Forms.FlexLayout.BasisProperty)附加可繫結的屬性表示的子系的配置的空間量`FlexLayout`主要軸上。 所指定的大小`Basis`屬性是父代的主座標軸的大小`FlexLayout`。 因此，`Basis`表示子系的寬度，當子系會排列在資料行時，將會排列在資料列或高度的子系。

`Basis`屬性的類型是[ `FlexBasis` ](xref:Xamarin.Forms.FlexBasis)，結構。 可以指定大小，可能是裝置獨立單位或大小的百分比`FlexLayout`。 預設值`Basis`屬性是靜態屬性`FlexBasis.Auto`，這表示子系的要求會使用寬度或高度。

在程式碼中，您可以設定`Basis`屬性`Label`名為`label`40 裝置獨立單位，像這樣：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

第二個引數`FlexBasis`建構函式稱為`isRelative`，並指出的大小是相對 (`true`) 或絕對 (`false`)。 引數的預設值是`false`，因此您也可以使用下列程式碼：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

從的隱含轉換`float`至`FlexBasis`定義，因此您可以更進一步地簡化：

```csharp
FlexLayout.SetBasis(label, 40);
```

您可以設定大小的 25%`FlexLayout`父代，就像這樣：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

這個分數值必須在 0 到 1 的範圍內。

XAML，在中，您可以使用數字以裝置獨立單位大小：

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

或者，您可以指定在範圍內的 0%到 100%的百分比：

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

**為基礎的實驗** 頁 **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 範例可讓您試驗`Basis`屬性。 此頁面會顯示已包裝的資料行，五個`Label`交替的背景和前景色彩的項目。 兩個`Slider`項目可讓您指定`Basis`的第二個和第四個值`Label`:

[![基礎實驗頁面](flex-layout-images/BasisExperiment.png "基礎實驗頁面")](flex-layout-images/BasisExperiment-Large.png#lightbox)

在左側的 iOS 螢幕擷取畫面顯示兩個`Label`以裝置獨立單位提供高度的項目。 Android 的畫面會顯示它們提供高度的一小部分的總高度`FlexLayout`。 如果`Basis`設為 100%，則子系是高度`FlexLayout`，和會將換行至下一個資料行，並佔用該資料行，整個高度，如 UWP 螢幕擷取畫面所示：它會顯示一樣的五個的子系會排列在資料列中，但其實際排列在五個資料行中。

### <a name="the-grow-property"></a>成長屬性

[ `Grow` ](xref:Xamarin.Forms.FlexLayout.GrowProperty)附加可繫結屬性的類型是`int`。 預設值為 0，且值必須大於或等於 0。

`Grow`屬性所扮演的角色時`Wrap`屬性設定為`NoWrap`而且資料列的子系小於的寬度的總寬度`FlexLayout`，或子系的資料行具有較短的高度比`FlexLayout`。 `Grow`屬性會指出如何分配剩餘的空間之間的子系。

在 **成長的實驗**頁面上，五個`Label`替代色彩的項目會排列在資料行，以及兩個`Slider`項目可讓您調整`Grow`屬性的第二個和第四個`Label`。 最左邊的 iOS 螢幕擷取畫面顯示的預設`Grow`0 的屬性：

[![增加實驗頁面](flex-layout-images/GrowExperiment.png "增大實驗頁面")](flex-layout-images/GrowExperiment-Large.png#lightbox)

如果任何一個子系指定正`Grow`值，則該子佔用所有剩餘的空間，如 Android 的螢幕擷取畫面所示。 也可以在兩個或多個子系之間配置此空間。 在 UWP 螢幕擷取畫面中，`Grow`第二個屬性`Label`設為 0.5，而`Grow`的第四個屬性`Label`1.5，提供第四個`Label`三倍的第二個的剩餘空間`Label`.

子檢視使用該空間的方式取決於特定類型的子系。 針對`Label`，可以定位文字內的總空間`Label`使用的屬性`HorizontalTextAlignment`和`VerticalTextAlignment`。

<a name="shrink" />

### <a name="the-shrink-property"></a>壓縮屬性

[ `Shrink` ](xref:Xamarin.Forms.FlexLayout.ShrinkProperty)附加可繫結屬性的類型是`int`。 預設值為 1，且值必須大於或等於 0。

`Shrink`屬性所扮演的角色時`Wrap`屬性設定為`NoWrap`並彙總的子系的資料列的寬度大於寬度`FlexLayout`，或子系的單一資料行的彙總高度大於高度`FlexLayout`。 通常`FlexLayout`會顯示這些系 constricting 其大小。 `Shrink`屬性可指出哪個子活動會提供要顯示在其完整大小的優先順序。

**壓縮實驗**頁面上會建立`FlexLayout`五個單一資料列`Label`需要更多的空間的子系`FlexLayout`寬度。 在左側的 iOS 螢幕擷取畫面顯示所有`Label`預設值為 1 的項目：

[![壓縮實驗頁面](flex-layout-images/ShrinkExperiment.png "壓縮實驗頁面")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

在 Android 螢幕擷取畫面`Shrink`第二個值`Label`設定為 0，且`Label`會顯示在其完整的寬度。 此外，第四個`Label`有`Shrink`值大於一，而且它已壓縮。 UWP 螢幕擷取畫面會顯示`Label`所指定的項目`Shrink`值為 0，如果該讓它們顯示在其完整的大小，有可能。

您可以同時設定`Grow`並`Shrink`值，以容納其中彙總的子系大小有時可能會小於或有時大於大小的情況下`FlexLayout`。

## <a name="css-styling-with-flexlayout"></a>FlexLayout CSS 樣式

您可以使用[CSS 樣式](~/xamarin-forms/user-interface/styles/css/index.md)引進與 Xamarin.Forms 3.0 功能`FlexLayout`。 **CSS 類別目錄項目** 頁面 **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 範例重複的版面配置 **類別目錄項目** 頁面上，但與 CSS許多樣式的樣式表：

[![CSS 目錄項目頁面](flex-layout-images/CssCatalogItems.png "CSS 目錄項目 頁面")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

原始**CatalogItemsPage.xaml**檔案具有五個`Style`中的定義其`Resources`15 區段`Setter`物件。 在  **CssCatalogItemsPage.xaml**擁有兩個較小的檔案`Style`定義具有四個只`Setter`物件。 這些樣式補充 Xamarin.Forms CSS 樣式功能目前不支援的屬性的 CSS 樣式表：

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

在第一行中參考的 CSS 樣式表`Resources`區段：

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

也請注意，每三個項目中的兩個項目包含`StyleClass`設定：

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

這些是指中的選取器**CatalogItemsStyles.css**樣式表：

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

數個`FlexLayout`附加可繫結的屬性會參考以下。 在 `label.empty`選取器中，您會看到`flex-grow`屬性，其設定樣式的空`Label`提供上述的一些空白空間`Button`。 `image`選取器包含`order`屬性並`align-self`屬性，這兩者都對應至`FlexLayout`附加可繫結的屬性。

您已了解您可以直接在設定屬性`FlexLayout`您可以設定可繫結的附加的屬性的子系`FlexLayout`。 或者，您可以設定這些屬性會間接使用以 XAML 為基礎的傳統樣式或 CSS 樣式。 重點是了解，並了解這些屬性。 這些屬性就是讓`FlexLayout`真正有彈性。

## <a name="flexlayout-with-xamarinuniversity"></a>使用 Xamarin.University FlexLayout

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin.Forms 3.0 Flex Layout 影片**

## <a name="related-links"></a>相關連結

- [FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)
