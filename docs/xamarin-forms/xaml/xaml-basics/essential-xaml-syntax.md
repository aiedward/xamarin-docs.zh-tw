---
title: 第 2 部分： 基本 XAML 語法
description: 本文說明屬性元素和附加屬性的基本 XAML 語法功能。
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c400bb342568a0399e2a582496f85ead273b6994
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84572178"
---
# <a name="part-2-essential-xaml-syntax"></a>第 2 部分： 基本 XAML 語法

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML 大多是設計來具現化及初始化物件。但通常，屬性必須設定為不容易以 XML 字串表示的複雜物件，而且有時必須在子類別上設定一個類別所定義的屬性。這兩個需求需要屬性專案和附加屬性的基本 XAML 語法功能。_

## <a name="property-elements"></a>Property 元素

在 XAML 中，類別的屬性通常會設定為 XML 屬性：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

不過，還有另一種方法可以在 XAML 中設定屬性。 若要使用來嘗試這種替代方法 `TextColor` ，請先刪除現有的 `TextColor` 設定：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

將空的元素 `Label` 標記分隔成開始和結束標記，以開啟它：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

在這些標記內，新增包含類別名稱和屬性名稱的開始和結束標記，並以句號分隔：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

將屬性值設定為這些新標記的內容，如下所示：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

這兩種指定屬性的方式 `TextColor` 在功能上是相同的，但請勿針對相同的屬性使用兩種方式，因為這樣會有效地設定屬性兩次，而且可能不明確。

有了這個新的語法，就可以引進一些便利的術語：

- `Label`是*物件元素*。 它是 Xamarin.Forms 以 XML 元素表示的物件。
- `Text`、 `VerticalOptions` `FontAttributes` 和 `FontSize` 是*屬性屬性*。 它們是 Xamarin.Forms 以 XML 屬性工作表示的屬性。
- 在最後一個程式碼片段中， `TextColor` 已成為*屬性元素*。 它是一個 Xamarin.Forms 屬性，但它現在是 XML 元素。

屬性專案的定義一開始可能會違反 XML 語法，但不會發生這種情況。 句點在 XML 中沒有任何特殊意義。 對 XML 解碼器而言， `Label.TextColor` 只是一個一般的子項目。

不過，在 XAML 中，此語法非常特殊。 屬性元素的其中一個規則是，標記中不會出現任何其他專案 `Label.TextColor` 。 屬性的值一律會定義為屬性專案開始和結束標記之間的內容。

您可以在一個以上的屬性上使用屬性元素語法：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center">
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

或者，您可以針對所有屬性使用屬性元素語法：

```xaml
<Label>
    <Label.Text>
        Hello, XAML!
    </Label.Text>
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
    <Label.VerticalOptions>
        Center
    </Label.VerticalOptions>
</Label>
```

一開始，屬性元素語法看起來可能像是一個不必要的長冗長取代，因為它相對相當簡單，而在這些範例中當然是如此。

不過，當屬性的值太複雜而無法表示為簡單字串時，屬性元素語法就會變得很重要。 在屬性元素標記中，您可以具現化另一個物件並設定其屬性。 例如，您可以使用屬性設定，將屬性（例如）明確地設定 `VerticalOptions` 為 `LayoutOptions` 值：

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

另一個範例： `Grid` 具有兩個名為和的屬性 `RowDefinitions` `ColumnDefinitions` 。 這兩個屬性的類型是 `RowDefinitionCollection` 和 `ColumnDefinitionCollection` ，也就是 `RowDefinition` 和 `ColumnDefinition` 物件的集合。 您必須使用屬性元素語法來設定這些集合。

以下是類別的 XAML 檔案開頭 `GridDemoPage` ，其中顯示和集合的屬性元素標記 `RowDefinitions` `ColumnDefinitions` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

請注意定義自動調整大小的儲存格、圖元寬度和高度的資料格，以及星星設定的縮寫語法。

## <a name="attached-properties"></a>附加屬性

您剛剛看到， `Grid` 需要和集合的屬性元素， `RowDefinitions` `ColumnDefinitions` 才能定義資料列和資料行。 不過，程式設計人員也必須有一些方法，來指出的每個子系所在的資料列和資料行 `Grid` 。

在的每個子系的標記內， `Grid` 使用下列屬性指定該子系的資料列和資料行：

- `Grid.Row`
- `Grid.Column`

這些屬性的預設值為0。 您也可以使用下列屬性來指出子系是否跨越一個以上的資料列或資料行：

- `Grid.RowSpan`
- `Grid.ColumnSpan`

這兩個屬性的預設值為1。

以下是完整的 GridDemoPage .xaml 檔案：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>

        <Label Text="Autosized cell"
               Grid.Row="0" Grid.Column="0"
               TextColor="White"
               BackgroundColor="Blue" />

        <BoxView Color="Silver"
                 HeightRequest="0"
                 Grid.Row="0" Grid.Column="1" />

        <BoxView Color="Teal"
                 Grid.Row="1" Grid.Column="0" />

        <Label Text="Leftover space"
               Grid.Row="1" Grid.Column="1"
               TextColor="Purple"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two rows (or more if you want)"
               Grid.Row="0" Grid.Column="2" Grid.RowSpan="2"
               TextColor="Yellow"
               BackgroundColor="Blue"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two columns"
               Grid.Row="2" Grid.Column="0" Grid.ColumnSpan="2"
               TextColor="Blue"
               BackgroundColor="Yellow"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Fixed 100x100"
               Grid.Row="2" Grid.Column="2"
               TextColor="Aqua"
               BackgroundColor="Red"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

    </Grid>
</ContentPage>
```

`Grid.Row`0 的和 `Grid.Column` 設定並非必要，但通常是為了清楚起見而納入。

如下所示：

[![方格版面配置](essential-xaml-syntax-images/griddemo.png)](essential-xaml-syntax-images/griddemo-large.png#lightbox)

除了語法之外，這些 `Grid.Row` 、 `Grid.Column` 、 `Grid.RowSpan` 和 `Grid.ColumnSpan` 屬性似乎是的靜態欄位或屬性 `Grid` ，但有趣的是，不 `Grid` 會定義任何名稱為 `Row` 、 `Column` 、 `RowSpan` 或 `ColumnSpan` 的專案。

相反地，會 `Grid` 定義名為、、和的四個可系結屬性 `RowProperty` `ColumnProperty` `RowSpanProperty` `ColumnSpanProperty` 。 這些是可系結屬性的特殊類型，稱為*附加屬性*。 它們是由類別所定義， `Grid` 但會在的子系上設定 `Grid` 。

當您想要在程式碼中使用這些附加屬性時， `Grid` 類別會提供名為、等的靜態方法 `SetRow` `GetColumn` 。 但是在 XAML 中，這些附加屬性會 `Grid` 使用簡單屬性名稱，設定為子系中的屬性。

在 XAML 檔案中，附加屬性一律可辨識為包含類別和屬性名稱的屬性，並以句號分隔。 它們稱為*附加屬性*，因為它們是由一個類別（在此案例中為）所定義， `Grid` 但附加至其他物件（在此案例中為的子系 `Grid` ）。 在版面配置期間， `Grid` 可以詢問這些附加屬性的值，以知道要將每個子系放在何處。

`AbsoluteLayout`類別會定義兩個名為和的附加屬性 `LayoutBounds` `LayoutFlags` 。 以下是使用的比例定位和調整大小功能所實現的棋盤模式 `AbsoluteLayout` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.AbsoluteDemoPage"
             Title="Absolute Demo Page">

    <AbsoluteLayout BackgroundColor="#FF8080">
        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

  </AbsoluteLayout>
</ContentPage>
```

這是：

[![絕對版面配置](essential-xaml-syntax-images/absolutedemo-large.png)](essential-xaml-syntax-images/absolutedemo-large.png#lightbox)

就像這樣的情況，您可能會問使用 XAML 的智慧功能。 當然，矩形的重複和變更規律性 `LayoutBounds` 會建議在程式碼中更容易實現。

這當然是合法的考慮，而且在定義使用者介面時，不會有平衡程式碼和標記的使用方式的問題。 您可以輕鬆地在 XAML 中定義部分視覺效果，然後使用程式碼後置檔案的函式來新增更多視覺效果，以便在迴圈中產生更好的外觀。

## <a name="content-properties"></a>內容屬性

在先前的範例中， `StackLayout` 、和 `Grid` `AbsoluteLayout` 物件會設定為的 `Content` 屬性 `ContentPage` ，而這些配置的子系實際上是集合中的專案 `Children` 。 不過 `Content` ，這些和 `Children` 屬性在 XAML 檔案中沒有任何地方。

您當然可以將 `Content` 和 `Children` 屬性包含為屬性元素，例如**XamlPlusCode**範例中的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout.Children>
                <Slider VerticalOptions="CenterAndExpand"
                        ValueChanged="OnSliderValueChanged" />

                <Label x:Name="valueLabel"
                       Text="A simple Label"
                       FontSize="Large"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand" />

                <Button Text="Click Me!"
                      HorizontalOptions="Center"
                      VerticalOptions="CenterAndExpand"
                      Clicked="OnButtonClicked" />
            </StackLayout.Children>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

真正的問題是：為什麼 XAML 檔案中*不*需要這些屬性元素？

在中定義以供在 XAML 中使用的專案 Xamarin.Forms ，可以在類別的屬性中加上旗標的一個屬性 `ContentProperty` 。 如果您在 `ContentPage` 線上檔中查閱類別 Xamarin.Forms ，您會看到此屬性：

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

這表示 `Content` 不需要屬性元素標記。 會假設在開始和結束標記之間出現的任何 XML 內容都要 `ContentPage` 指派給 `Content` 屬性。

 `StackLayout`、 `Grid` 、 `AbsoluteLayout` 和 `RelativeLayout` 全都衍生自 `Layout<View>` ，如果您 `Layout<T>` 在檔中查閱 Xamarin.Forms ，將會看到另一個 `ContentProperty` 屬性：

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

，允許自動將版面配置的內容新增至集合， `Children` 而不需要明確的 `Children` 屬性專案標記。

其他類別也有 `ContentProperty` 屬性定義。 例如，的 content 屬性 `Label` 是 `Text` 。 查看其他人的 API 檔。

## <a name="platform-differences-with-onplatform"></a>與 OnPlatform 的平臺差異

在單一頁面應用程式中，通常會在 `Padding` 頁面上設定屬性，以避免覆寫 iOS 狀態列。 在程式碼中，您可以 `Device.RuntimePlatform` 針對此目的使用屬性：

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

您也可以使用和類別，在 XAML 中執行類似的動作 [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) [`On`](xref:Xamarin.Forms.On) 。 在靠近頁面頂端的屬性中，第一個包含屬性專案 `Padding` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

在這些標記中，包含 `OnPlatform` 標記。 `OnPlatform`是泛型類別。 您需要指定泛型型別引數（在此案例中為），也 `Thickness` 就是屬性的型別 `Padding` 。 幸好，有一個 XAML 屬性專門用來定義名為的泛型引數 `x:TypeArguments` 。 這應符合您所設定之屬性的類型：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">

        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

`OnPlatform`具有名為的屬性 `Platforms` ，它 `IList` 是 `On` 物件的。 針對該屬性使用 property 元素標記：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>

            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

現在加入 `On` 元素。 針對每一個設定屬性的屬性和屬性的 `Platform` `Value` 標記 `Thickness` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>
                <On Platform="iOS" Value="0, 20, 0, 0" />
                <On Platform="Android" Value="0, 0, 0, 0" />
                <On Platform="UWP" Value="0, 0, 0, 0" />
            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

此標記可以簡化。 的 content 屬性 `OnPlatform` 為 `Platforms` ，因此可以移除這些屬性元素的標記：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android" Value="0, 0, 0, 0" />
            <On Platform="UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

的 `Platform` 屬性屬於 `On` 類型 `IList<string>` ，因此如果值相同，您可以包含多個平臺：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android, UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

因為 Android 和 UWP 已設定為的預設值 `Padding` ，所以可以移除該標記：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

這是在 XAML 中設定平臺相依屬性的標準方式 `Padding` 。 如果 `Value` 設定無法以單一字串表示，您可以為它定義屬性元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS">
                <On.Value>
                    0, 20, 0, 0
                </On.Value>
            </On>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

> [!NOTE]
> `OnPlatform`標記延伸也可以在 XAML 中用來自訂以每個平臺為基礎的 UI 外觀。 它提供與和類別相同的 `OnPlatform` 功能 `On` ，但具有更精確的標記法。 如需詳細資訊，請參閱[OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

## <a name="summary"></a>摘要

在屬性專案和附加屬性中，已建立許多基本 XAML 語法。 不過，有時候您需要以間接方式（例如從資源字典）將屬性設定為物件。 這種方法在下一個部分（第3部分）中涵蓋[。XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)。

## <a name="related-links"></a>相關連結

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第1部分。使用 XAML 消費者入門](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第3部分。XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第4部分。資料系結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第5部分：從資料系結到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
