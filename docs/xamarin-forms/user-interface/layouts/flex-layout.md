---
title: Xamarin.Forms FlexLayout
description: 使用 FlexLayout 堆疊或換行的子檢視集合。
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 05/07/2018
ms.openlocfilehash: 7585138cd6c33c2a5dc537ba28101a84e1c4b7ae
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="the-xamarinforms-flexlayout"></a>Xamarin.Forms FlexLayout

_使用 FlexLayout 堆疊或換行的子檢視集合。_

Xamarin.Forms [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout) Xamarin.Forms 3.0 版的新功能。 它會根據 CSS[彈性方塊配置模組](http://www.w3.org/TR/css-flexbox-1/)，稱為_flex 配置_或_彈性方塊_，因此呼叫，因為它包含許多彈性的選項，來排列子系在版面配置。

`FlexLayout` 類似於 Xamarin.Forms [ `StackLayout` ](~/xamarin-forms/user-interface/layouts/stack-layout.md) ，它可以排列其子系水平及垂直堆疊。 不過，`FlexLayout`也能夠包裝其子系，如果有太多，無法納入單一資料列或資料行，而且也有許多選擇可用來列印方向、 對齊方式及調整以各種螢幕大小。

`FlexLayout` 衍生自[ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)和繼承[ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/)型別的屬性`IList<View>`。

`FlexLayout` 定義六個公用的可繫結屬性和五個附加的繫結屬性會影響大小、 方向與其子元素的對齊方式。 (如果您不熟悉連接可繫結的屬性，請參閱文章**[附加屬性](~/xamarin-forms/xaml/attached-properties.md)**。)下列各節中詳細說明這些屬性上**[可繫結的屬性詳細](#bindable-properties)** 和**[附加的繫結屬性詳細](#attached-properties)**. 不過，本文開頭的區段，在某些**[常見使用案例](#common-scenarios)** 的`FlexLayout`非正式地描述其中許多屬性。 發行項的結束時，您會看到如何結合`FlexLayout`與[CSS 樣式表](~/xamarin-forms/user-interface/styles/css/index.md)。

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>常見使用案例

**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 程式範例包含數個頁面的一些常見使用該 demonstate`FlexLayout`並可讓您試驗其屬性。

### <a name="using-flexlayout-for-a-simple-stack"></a>使用簡單的堆疊 FlexLayout

**簡單堆疊**頁面會顯示如何`FlexLayout`可以替換`StackLayout`但更簡單的標記。 在此範例中的所有項目被定義在 XAML 頁面。 `FlexLayout`包含四個子系：

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

[![簡單堆疊頁面](flex-layout-images/SimpleStack.png "簡單堆疊頁面")](flex-layout-images/SimpleStack-Large.png#lightbox)

三個屬性的`FlexLayout`如下所示**SimpleStackPage.xaml**檔案：

- [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction)屬性設定的值為[ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection)列舉型別。 預設值為 `Row`。 將屬性設定為`Column`會導致子系`FlexLayout`排列成單一資料行的項目。

    當中的項目`FlexLayout`會排列在資料行，`FlexLayout`即所謂具有垂直_主座標軸_和水平_交叉軸_。

- [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems)屬性屬於型別[ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems)和指定的項目上的交叉軸的對齊方式。 `Center`選項會讓每個項目水平置中對齊。

    如果您使用`StackLayout`而`FlexLayout`這項工作，您會置中的所有項目指派`HorizontalOptions`屬性每個項目`Center`。 `HorizontalOptions`屬性不適用於子系`FlexLayout`，但單一`AlignItems`屬性可達成相同的目標。 如果需要您可以使用`AlignSelf`附加可繫結的屬性，來覆寫`AlignItems`個別項目的屬性：

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    這項變更，這一個`Label`停駐在左邊緣`FlexLayout`時讀取順序是由左到右。

- [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent)屬性屬於型別[ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)，並指定項目在主要軸上的排列方式。 `SpaceEvenly`選項會配置所有剩餘的垂直空間之間的所有項目，平均和第一個項目，上下最後一個項目。

    如果您使用`StackLayout`，您必須指派`VerticalOptions`屬性每個項目`CenterAndExpand`來達到類似的效果。 但`CenterAndExpand`選項會配置每個項目比第一個項目之前和之後的最後一個項目之間倍空間。 您可以模擬`CenterAndExpand`選項`VerticalOptions`藉由設定`JustifyContent`屬性`FlexLayout`至`SpaceAround`。

這些`FlexLayout`一節中詳細討論屬性**[可繫結的屬性詳細](#bindable-properties)** 下方。

### <a name="using-flexlayout-for-wrapping-items"></a>使用 FlexLayout 包裝項目

**相片包裝**頁面**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 範例會示範如何`FlexLayout`可以包裝它的子系的其他資料列或資料行。 XAML 檔案會具現化`FlexLayout`並指派它的兩個屬性：

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

`Direction`屬性這`FlexLayout`未設定，所以它沒有預設值為`Row`，這表示子系會排列在資料列，而主要軸是水平。

[ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap)屬性是列舉型別的[ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap)。 如果有太多而無法放入一個資料列的項目，這個屬性設定會導致要包裝至下一個資料列的項目。

請注意，`FlexLayout`子系的`ScrollView`。 如果有太多資料列，以符合在頁面上，則`ScrollView`具有預設`Orientation`屬性`Vertical`並允許垂直捲動。

`JustifyContent`屬性會配置在主要軸 （水平軸） 上的剩餘空間，讓每個項目會圍繞著相同的空白空間量。

程式碼後置檔案存取範例相片的集合，並將其加入`Children`集合`FlexLayout`:

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
        int imageDimension = Device.RuntimePlatform == Device.iOS ||
                             Device.RuntimePlatform == Device.Android ? 240 : 120;

        string urlSuffix = String.Format("?width={0}&height={0}&mode=max", imageDimension);

        using (WebClient webClient = new WebClient())
        {
            try
            {
                // Download the list of stock photos
                Uri uri = new Uri("http://docs.xamarin.com/demo/stock.json");
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
                            Source = ImageSource.FromUri(new Uri(filepath + urlSuffix))
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

以下是三個平台，逐漸捲動，即從上到下執行的程式：

[![相片文繞圖頁面](flex-layout-images/PhotoWrapping.png "相片換行頁面")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>頁面配置與 FlexLayout

標準配置正在呼叫的 web 設計[_神聖 grail_ ](https://en.wikipedia.org/wiki/Holy_grail_(web_design))因為它是很理想，但通常很難了解與焙而成的配置格式。 版面配置包含在頁面頂端的頁首和頁尾的底端，這兩個延伸至完整頁面的寬度。 佔用頁面的中心是主要的內容，但通常與左側的內容和補充資訊的單欄式功能表 (有時稱為_擱置在一旁_區域) 右邊。 [CSS 彈性方塊版面配置規格 5.4.1 節](http://www.w3.org/TR/css-flexbox-1/#order-accessibility)描述如何實現神聖 grail 版面配置，以彈性方塊。

**神聖 Grail 配置**頁面**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 範例示範此配置命名為使用其中一個的簡單實作`FlexLayout`巢狀方式置於另一個。 由於此頁面設計適用於 phone 直向模式中，左側和右側內容區域的區域才 50 像素：

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

這裡與其執行所在的三個平台：

[![神聖 Grail 版面配置頁](flex-layout-images/HolyGrailLayout.png "神聖 Grail 版面配置頁")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

瀏覽和另行區域，以呈現`BoxView`在左方和右方。

第一個`FlexLayout`XAML 檔案具有垂直主座標軸和包含三個子系排列在資料行。 這些是標頭、 頁面和頁尾的主體。 巢狀`FlexLayout`水平的主要軸具有三個子系排列在資料列。

此程式將示範三個附加的繫結屬性：

- `Order`附加的繫結屬性設定在第一個`BoxView`。 這個屬性是預設值為 0 的整數。 您可以使用這個屬性來變更配置順序。 通常開發人員偏好才會出現在瀏覽項目之前的標記頁面的內容，和保留項目。 設定`Order`上第一個屬性`BoxView`值至它同層級小於造成它會顯示為資料列中的第一個項目。 同樣地，您可以確保設定項目出現最後一個`Order`屬性設為大於其同層級值。

- `Basis`附加的繫結屬性設定對這兩個`BoxView`項目，讓它們在寬度為 50 像素。 這個屬性的型別是`FlexBasis`，定義類型的靜態屬性的結構`FlexBasis`名為`Auto`，預設值。 您可以使用`Basis`指定像素大小或百分比來指出多少空間項目都會佔用主要軸上。 它會呼叫_基礎_因為它會指定為基礎的所有後續的版面配置項目大小。

- `Grow`屬性設定在巢狀`Layout`以及`Label`代表內容的子系。 這個屬性的型別是`float`且具有預設值為 0。 若設為正值，沿著主要軸所有剩餘的空間配置項目，並具有正值的同層級`Grow`。 有點類似星狀規格中的值，按比例配置空間`Grid`。

    第一個`Grow`設定附加的屬性上的巢狀`FlexLayout`，這表示此`FlexLayout`是所佔用的所有未使用垂直空間內外部`FlexLayout`。 第二個`Grow`附加的屬性上設定`Label`代表內容，指出此內容以佔用內的所有未使用水平空間內部`FlexLayout`。

    另外還有類似`Shrink`附加的子系大小超過大小時，您可以使用的繫結屬性`FlexLayout`但不是想要使用換行。

### <a name="catalog-items-with-flexlayout"></a>FlexLayout 的類別目錄項目

**類別目錄項目**頁面**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 範例是類似於[範例 1 中的 CSS Flex 版面配置 方塊規格區段1.1](http://www.w3.org/TR/css-flexbox-1/#overview)不同之處在於它會顯示可水平捲動的一連串的圖片和描述的三個猴子：

[![類別目錄項目頁面](flex-layout-images/CatalogItems.png "類別目錄項目頁面")](flex-layout-images/CatalogItems-Large.png#lightbox)

每三個猴子是`FlexLayout`中包含`Frame`，指定明確的高度和寬度，而且也是較大的子系`FlexLayout`。 在此 XAML 檔案中，大多數的屬性`FlexLayout`樣式，但有一個都是隱含樣式中所指定的子系：

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

隱含樣式`Image`包含兩個附加的繫結內容的設定`Flexlayout`:

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

`Order`設定&ndash;1 會使得`Image`先顯示在每個巢狀的項目`FlexLayout`不論其位置的子系集合內的檢視。 `AlignSelf`屬性`Center`導致`Image`至內置中對齊`FlexLayout`。 這會覆寫的設定`AlignItems`屬性，其預設值的`Stretch`，亦即，`Label`和`Button`子系會伸展成的整個寬度`FlexLayout`。

在每個，這三個`FlexLayout`檢視時，空白`Label`之前`Button`，但有`Grow`設定為 1。 這表示所有多餘垂直空間，配置給此空白`Label`，其有效地將推入`Button`底部。

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>在詳細資料可繫結的屬性

既然您已看過的一些常見的應用程式`FlexLayout`，屬性`FlexLayout`中更多詳細資料，可以直覺式探索。 
`FlexLayout` 定義您在設定的六個可繫結屬性`FlexLayout`本身，在程式碼或 XAML 中的，以控制 orientatin 和對齊方式。 (其中一個屬性， [ `Position` ](xref:Xamarin.Forms.FlexLayout.Position)，未涵蓋在本文中。)

您可以試驗五剩餘可繫結屬性使用**實驗**頁面**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 範例。 此頁面可讓您新增或移除的子系`FlexLayout`，並設定可繫結的五個屬性的組合。 所有子系`FlexLayout`是`Label`各種色彩和大小，檢視與`Text`屬性設定為它的位置中對應數字`Children`集合。

程式啟動時，五個`Picker`檢視會顯示這些五部的預設值`FlexLayout`屬性。 `FlexLayout`螢幕的底部包含三個子系：

[![[實驗] 頁面中： 預設](flex-layout-images/ExperimentDefault.png "[實驗] 頁面中的預設值")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

每個`Label`檢視已配置的空間會顯示灰色背景`Label`內`FlexLayout`。 背景的`FlexLayout`本身是 Alice 藍色。 會在左邊和右邊的小邊界除了佔用頁面的整個底部區域。

<a name="direction" />

### <a name="the-direction-property"></a>方向屬性

[ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction)屬性屬於型別[ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection)，具有四個成員的列舉：

- `Column`
- `ColumnReverse` （或者 「 資料行反轉 」 在 XAML 中）
- `Row`預設值
- `RowReverse` （或者 「 資料列反轉 」 在 XAML 中）

在 XAML 中，您可以指定列舉成員名稱使用大寫、 小寫的這個屬性的值，或混合大小寫，或者您可以使用兩個 CSS 指標相同的括號內的其他字串。 (中所定義的 「 資料行反轉 」 和 「 資料列反向 」 字串[ `FlexDirectionTypeConverter` ](xref:Xamarin.Forms.FlexDirectionTypeConverter) XAML 剖析器使用的類別。)

以下是**實驗**（從左到右） 顯示頁面`Row`方向，`Column`方向，以及`ColumnReverse`方向：

[![實驗 頁面上： 方向](flex-layout-images/ExperimentDirection.png "實驗頁面-方向")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

請注意，`Reverse`選項，項目開始的右方或下方。

<a name="wrap" />

### <a name="the-wrap-property"></a>自動換行屬性

[ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap)屬性屬於型別[ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap)，具有三個成員的列舉：

- `NoWrap`預設值
- `Wrap`
- `Reverse` （或者 「 換行反轉 」 在 XAML 中）

這些螢幕會顯示從左到右， `NoWrap`，`Wrap`和`Reverse`12 子系的選項：

[![實驗 頁面上： 包裝](flex-layout-images/ExperimentWrap.png "[實驗] 頁面中的換行")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

當`Wrap`屬性設定為`NoWrap`主座標軸會受到限制 （如同這個程式），和寬或高到足以容納所有子系，不是主座標軸`FlexLayout`嘗試縮小項目，為 iOS 螢幕擷取畫面示範。 您可以控制之項目的 shrinkness [ `Shrink` ](#shrink)附加可繫結的屬性。

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>JustifyContent 屬性

[ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent)屬性屬於型別[ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)，具有六個成員的列舉：

- `Start` （或 「 flex-開始 」 在 XAML 中），預設值
- `Center`
- `End` （或者 「 flex 端 」 在 XAML 中）
- `SpaceBetween` （或者 「 空間-之間 」 在 XAML 中）
- `SpaceAround` （或者"空間-aro"XAML 中）
- `SpaceEvenly`

這個屬性會指定如何將項目間距主座標軸，也就是在此範例中水平軸上：

[![實驗 頁面上： 以構成內容](flex-layout-images/ExperimentJustifyContent.png "[實驗] 頁面的左右對齊內容")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

在三個螢幕擷取畫面，`Wrap`屬性設定為`Wrap`。 `Start`預設會顯示在先前的 Android 螢幕擷取畫面。 IOS 螢幕擷取畫面這裡顯示`Center`選項： 的所有項目會移至中央。 三個其他選項以這個字開頭`Space`配置額外的項目未佔用的空間。 `SpaceBetween` 配置的空間項目; 之間，平均`SpaceAround`置於等於每個項目，周圍的空間時`SpaceEvenly`置於等於空間之間每個項目，第一個項目之前和之後的資料列上的最後一個項目。

<a name="align-items" />

### <a name="the-alignitems-property"></a>AlignItems 屬性

[ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems)屬性屬於型別[ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems)，具有四個成員的列舉：

- `Stretch`預設值
- `Center`
- `Start` （或者 「 flex-開始 」 在 XAML 中）
- `End` （或者 「 flex 端 」 在 XAML 中）

這是兩個屬性的其中一個 (其他正在[ `AlignContent` ](#align-content))，表示子系上的交叉軸的對齊方式。 中每個資料列中，子系是自動縮放 （如在先前的螢幕擷取畫面所示），或對齊開始、 center 或每個項目，結尾中的下列三個螢幕擷取畫面所示：

[![實驗 頁面上： 對齊項目](flex-layout-images/ExperimentAlignItems.png "[實驗] 頁面中的對齊項目")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

在 iOS 螢幕擷取畫面中，所有子系的上緣對齊。 Android 螢幕擷取畫面中的項目是垂直置中對齊根據最高的子系。 在 UWP 螢幕擷取畫面中，所有項目底端對齊。

針對任何個別的項目，`AlignItems`設定會覆寫與[ `AlignSelf` ](#align-self)附加可繫結的屬性。

<a name="align-content" />

### <a name="the-aligncontent-property"></a>AlignContent 屬性

[ `AlignContent` ](xref:Xamarin.Forms.FlexLayout.AlignContent)屬性屬於型別[ `FlexAlignContent` ](xref:Xamarin.Forms.FlexAlignContent)，具有七個成員的列舉：

- `Stretch`預設值
- `Center`
- `Start` （或者 「 flex-開始 」 在 XAML 中）
- `End` （或者 「 flex 端 」 在 XAML 中）
- `SpaceBetween` （或者 「 空間-之間 」 在 XAML 中）
- `SpaceAround` （或者"空間-aro"XAML 中）
- `SpaceEvenly`

像`AlignItems`、`AlignContent`屬性也會對齊交叉軸上的子系，但會影響整個資料列或資料行：

[![實驗 頁面上： 對齊內容](flex-layout-images/ExperimentAlignContent.png "[實驗] 頁面中的對齊內容")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

在 iOS screnshot 中，兩個資料列是在最上方。它們在中心; 在 Android 螢幕擷取畫面而且這是在 UWP 螢幕擷取畫面底部。 資料列也以各種方式設定間距：

[![實驗 頁面上： 對齊內容 2](flex-layout-images/ExperimentAlignContent2.png "[實驗] 頁面中的對齊內容 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

`AlignContent`只有一個資料列或資料行時，沒有任何作用。

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>詳細資料中的連接可繫結屬性

`FlexLayout` 定義五個附加的繫結屬性。 這些屬性會設定上的子系`FlexLayout`，只屬於特定子系。

<a name="align-self" />

### <a name="the-alignself-property"></a>AlignSelf 屬性

[ `AlignSelf` ](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty)附加的繫結屬性為類型[ `FlexAlignSelf` ](xref:Xamarin.Forms.FlexAlignContent)，具有五個成員的列舉：

- `Auto`預設值
- `Stretch`
- `Center`
- `Start` （或者 「 flex-開始 」 在 XAML 中）
- `End` （或者 「 flex 端 」 在 XAML 中）

針對任何個別的子系`FlexLayout`、 設定會覆寫這個屬性[ `AlignItems` ](#align-items)屬性上設定`FlexLayout`本身。 預設值為`Auto`表示使用`AlignItems`設定。

如`Label`名`label`（或範例），您可以設定`AlignSelf`如下的程式碼中的屬性：

```csharp
FlexAlign.SetAlignSelf(label, FlexAlignSelf.Center);
```

請注意，有沒有參考`FlexLayout`的父代`Label`。 在 XAML 中，您可以設定的屬性，就像這樣：

```xaml
<Label ... FlexAlign.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>Order 屬性

[ `Order` ](xref:Xamarin.Forms.FlexLayout.OrderProperty)屬性屬於型別`int`。 預設值為 0。

`Order`屬性可讓您變更順序的子系`FlexLayout`排列。 通常的子系`FlexLayout`排列中出現的順序相同`Children`集合。 您可以藉由設定覆寫這個順序`Order`附加在上一個或多個子系為非零的整數值的可繫結屬性。 `FlexLayout`將排列以根據設定的子系`Order`上每一個子系，但具有相同的子系屬性`Order`中出現的順序排列設定`Children`集合。

### <a name="the-basis-property"></a>基礎屬性

[ `Basis` ](xref:Xamarin.Forms.FlexLayout.BasisProperty)附加可繫結的屬性表示的子系的已配置的空間量`FlexLayout`主要軸上。 所指定的大小`Basis`屬性是父代的主座標軸大小`FlexLayout`。 因此，`Basis`當資料行中排列子系，將會排列在資料列或高度的子系時，表示子系的寬度。

`Basis`屬性屬於型別[ `FlexBasis` ](xref:Xamarin.Forms.FlexBasis)，結構。 裝置獨立單位或大小的百分比，就可以指定大小`FlexLayout`。 預設值`Basis`屬性是靜態屬性`FlexBasis.Auto`，這表示子系的要求會使用寬度或高度。

在程式碼中，您可以設定`Basis`屬性`Label`名為`label`40 裝置獨立單位，就像這樣：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

第二個引數`FlexBasis`建構函式稱為`isRelative`，並指出的大小是否是相對 (`true`) 或絕對 (`false`)。 引數的預設值是`false`，因此您也可以使用下列程式碼：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

從的隱含轉換`float`至`FlexBasis`定義，所以您可以更進一步簡化：

```csharp
FlexLayout.SetBasis(label, 40);
```

您可以設定大小的 25%`FlexLayout`父像這樣：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

此分數的值必須在 0 到 1 的範圍內。

在 XAML 中，您可以使用數字以裝置獨立單位的大小：

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

或者，您可以在範圍 0%到 100%的指定百分比：

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

**基礎實驗**頁面**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 範例可讓您試驗`Basis`屬性。 此頁面會顯示換行的資料行，五個`Label`交替的背景和前景色彩的項目。 兩個`Slider`項目可讓您指定`Basis`第二個和第四個值`Label`:

[![基礎實驗頁面](flex-layout-images/BasisExperiment.png "基礎實驗頁面")](flex-layout-images/BasisExperiment-Large.png#lightbox)

在左側 iOS 螢幕擷取畫面顯示兩個`Label`以裝置獨立單位提供高度的項目。 Android 的螢幕會顯示它們提供高度的一部分的總高度`FlexLayout`。 如果`Basis`設到 100%，則子系是高度`FlexLayout`，和會將換行到下一個資料行，並佔用整個高度，該資料行，如 UWP 螢幕擷取畫面所示： 似乎如同五個的子系會排列在資料列但它們實際上排列在五個資料行中。

### <a name="the-grow-property"></a>擴充屬性

[ `Grow` ](xref:Xamarin.Forms.FlexLayout.GrowProperty)附加的繫結屬性為類型`int`。 預設值為 0，，和值必須大於或等於 0。

`Grow`屬性所扮演的角色時當`Wrap`屬性設定為`NoWrap`和子系的資料列有總寬度小於寬度`FlexLayout`，或子系的資料行具有較短的高度比`FlexLayout`。 `Grow`屬性會指出如何分配之間的子系的剩餘空間。

在**成長實驗**頁面上，五個`Label`替代色彩的項目會排列在資料行，以及兩個`Slider`項目可讓您調整`Grow`屬性的第二個和第四個`Label`。 最左邊的 iOS 螢幕擷取畫面顯示預設`Grow`0 的屬性：

[![增大實驗頁面](flex-layout-images/GrowExperiment.png "Grow 實驗頁面")](flex-layout-images/GrowExperiment-Large.png#lightbox)

如果任何一個子系指定正`Grow`值，子系佔用所有剩餘的空間，如 Android 螢幕擷取畫面所示。 也可以在兩個或多個子系之間配置這個空間。 在 UWP 螢幕擷取畫面，`Grow`屬性的第二個`Label`設為 0.5，而`Grow`屬性的第四個`Label`為 1.5，可讓第四個`Label`三倍的第二個剩餘空間`Label`.

子檢視使用該空間的方式取決於特定類型的子系。 如`Label`，文字可以放置在內的總空間`Label`使用屬性`HorizontalTextAlignment`和`VerticalTextAlignment`。

<a name="shrink" />

### <a name="the-shrink-property"></a>壓縮屬性

[ `Shrink` ](xref:Xamarin.Forms.FlexLayout.ShrinkProperty)附加的繫結屬性為類型`int`。 預設值為 1，和值必須大於或等於 0。

`Shrink`屬性所扮演的角色時`Wrap`屬性設定為`NoWrap`和彙總的子系的資料列的寬度大於寬度`FlexLayout`，或彙總單一資料行的子系的高度大於高度`FlexLayout`。 通常`FlexLayout`會顯示這些系 constricting 其大小。 `Shrink`屬性可以指出哪一個子系優先順序中顯示其完整的大小。

**壓縮實驗**頁面會建立`FlexLayout`五個單一資料列與`Label`子系需要更多的空間比`FlexLayout`寬度。 在左側 iOS 螢幕擷取畫面顯示所有`Label`具有預設值為 1 的項目：

[![壓縮實驗頁面](flex-layout-images/ShrinkExperiment.png "壓縮實驗頁面")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

在 Android 螢幕擷取畫面`Shrink`第二個值`Label`設為 0，並可`Label`會顯示在其完整的寬度。 此外，第四個`Label`指定`Shrink`值大於 1，而且它已壓縮。 UWP 螢幕擷取畫面會顯示`Label`給定的項目`Shrink`值為 0 時可讓它們顯示在完整大小，如果有可能。

您可以同時設定`Grow`和`Shrink`值以容納其中彙總子大小有時可能會是小於或有時大於大小的情況下`FlexLayout`。

## <a name="css-styling-with-flexlayout"></a>FlexLayout CSS 樣式

您可以使用[CSS 樣式](~/xamarin-forms/user-interface/styles/css/index.md)引進與 Xamarin.Forms 3.0 功能`FlexLayout`。 **CSS 類別目錄項目**頁面**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 範例重複的版面配置**類別目錄項目** 頁面上，但 css許多樣式的樣式表：

[![CSS 類別目錄項目頁面](flex-layout-images/CssCatalogItems.png "CSS 類別目錄項目 頁面")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

原始**CatalogItemsPage.xaml**檔案擁有五個`Style`中的定義其`Resources`區段 15`Setter`物件。 在**CssCatalogItemsPage.xaml**檔案擁有兩個較小`Style`只四個定義`Setter`物件。 這些樣式補充 Xamarin.Forms CSS 樣式，此功能目前不支援的屬性的 CSS 樣式表：

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

CSS 樣式表中的第一行參考`Resources`> 一節：

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

另請注意，每三個項目中的兩個項目包含`StyleClass`設定：

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

數個`FlexLayout`這裡所附加的繫結屬性。 在`label.empty`選取器，您會看到`flex-grow`屬性樣式空`Label`提供上述一些空白空間`Button`。 `image`選取器包含`order`屬性和`align-self`屬性，兩者都對應至`FlexLayout`附加可繫結的屬性。

您可以直接在設定屬性，您所見`FlexLayout`，您可以設定可繫結的附加的屬性的子系`FlexLayout`。 或者，您可以設定這些屬性間接使用 以 XAML 為基礎的傳統樣式或 CSS 樣式。 重要的是了解並了解這些屬性。 這些屬性會構成`FlexLayout`真正有彈性。 

## <a name="flexlayout-with-xamarinuniversity"></a>與 Xamarin.University FlexLayout

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin.Forms 3.0 Flex 版面配置，藉由[Xamarin 大學](https://university.xamarin.com/)**

## <a name="related-links"></a>相關的連結

- [FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)
