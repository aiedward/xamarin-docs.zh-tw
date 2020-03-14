---
title: 第 2 部分。 基本 XAML 語法
description: 本文說明屬性元素和附加屬性的基本 XAML 語法功能。
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: f79a07a04eddeea1441f7938fdef210a37fb920a
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305490"
---
# <a name="part-2-essential-xaml-syntax"></a>第 2 部分。 基本 XAML 語法

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

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

不過，還有另一種方法可以在 XAML 中設定屬性。 若要使用 `TextColor`來嘗試這種替代方案，請先刪除現有的 `TextColor` 設定：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

將空白元素的 `Label` 標籤分隔成開始和結束標記，以開啟該標記：

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

這兩種指定 `TextColor` 屬性的方式在功能上是相同的，但請勿針對相同的屬性使用兩種方式，因為這樣會有效地設定屬性兩次，而且可能不明確。

有了這個新的語法，就可以引進一些便利的術語：

- `Label` 是*物件元素*。 它是以 XML 元素表示的 Xamarin. Forms 物件。
- `Text`、`VerticalOptions`、`FontAttributes` 和 `FontSize` 是*屬性屬性*。 它們是以 XML 屬性工作表示的 Xamarin. 表單內容。
- 在最後一個程式碼片段中，`TextColor` 已成為*屬性元素*。 它是一個 Xamarin. form 屬性，但它現在是 XML 元素。

屬性專案的定義一開始可能會違反 XML 語法，但不會發生這種情況。 句點在 XML 中沒有任何特殊意義。 對 XML 解碼器而言，`Label.TextColor` 只是一個一般的子項目。

不過，在 XAML 中，此語法非常特殊。 屬性專案的其中一個規則是，沒有其他專案可以出現在 `Label.TextColor` 標記中。 屬性的值一律會定義為屬性專案開始和結束標記之間的內容。

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

不過，當屬性的值太複雜而無法表示為簡單字串時，屬性元素語法就會變得很重要。 在屬性元素標記中，您可以具現化另一個物件並設定其屬性。 例如，您可以使用屬性設定，將 `VerticalOptions` 的屬性明確設定為 `LayoutOptions` 值：

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

另一個範例： `Grid` 有兩個名為 `RowDefinitions` 和 `ColumnDefinitions`的屬性。 這兩個屬性的類型是 `RowDefinitionCollection` 和 `ColumnDefinitionCollection`，這是 `RowDefinition` 和 `ColumnDefinition` 物件的集合。 您必須使用屬性元素語法來設定這些集合。

以下是 `GridDemoPage` 類別之 XAML 檔案的開頭，其中顯示 `RowDefinitions` 和 `ColumnDefinitions` 集合的屬性元素標記：

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

您剛剛看到 `Grid` 需要 `RowDefinitions` 和 `ColumnDefinitions` 集合的屬性元素，才能定義資料列和資料行。 不過，程式設計人員也必須有一些方法，指出 `Grid` 的每個子系所在的資料列和資料行。

在 `Grid` 的每個子系的標記中，您可以使用下列屬性來指定該子系的資料列和資料行：

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

`Grid.Row` 和 `Grid.Column` 設定都不是必要的，但通常是為了清楚起見而納入。

如下所示：

[![方格版面配置](essential-xaml-syntax-images/griddemo.png)](essential-xaml-syntax-images/griddemo-large.png#lightbox)

這些 `Grid.Row`、`Grid.Column`、`Grid.RowSpan`和 `Grid.ColumnSpan` 屬性都只會從語法中判斷為靜態欄位或 `Grid`屬性，但有趣的是，`Grid` 不會定義任何名稱為 `Row`、`Column`、`RowSpan`或 `ColumnSpan`的專案。

相反地，`Grid` 會定義四個可系結的屬性，名為 `RowProperty`、`ColumnProperty`、`RowSpanProperty`和 `ColumnSpanProperty`。 這些是可系結屬性的特殊類型，稱為*附加屬性*。 它們是由 `Grid` 類別定義，但是是在 `Grid`的子系上設定。

當您想要在程式碼中使用這些附加屬性時，`Grid` 類別會提供名為 `SetRow`、`GetColumn`等等的靜態方法。 但是在 XAML 中，這些附加屬性會使用簡單的屬性名稱，設定為 `Grid` 子系中的屬性。

在 XAML 檔案中，附加屬性一律可辨識為包含類別和屬性名稱的屬性，並以句號分隔。 它們稱為*附加屬性*，因為它們是由一個類別（在此案例中為 `Grid`）所定義，但附加至其他物件（在此案例中為 `Grid`的子系）。 在版面配置期間，`Grid` 可以詢問這些附加屬性的值，以知道要將每個子系放在何處。

`AbsoluteLayout` 類別會定義兩個名為 `LayoutBounds` 和 `LayoutFlags`的附加屬性。 以下是使用 `AbsoluteLayout`的比例定位和調整大小功能所實現的棋盤模式：

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

就像這樣的情況，您可能會問使用 XAML 的智慧功能。 當然，`LayoutBounds` 矩形的重複和變更規律性，表示它可能會在程式碼中更好實現。

這當然是合法的考慮，而且在定義使用者介面時，不會有平衡程式碼和標記的使用方式的問題。 您可以輕鬆地在 XAML 中定義部分視覺效果，然後使用程式碼後置檔案的函式來新增更多視覺效果，以便在迴圈中產生更好的外觀。

## <a name="content-properties"></a>內容屬性

在先前的範例中，`StackLayout`、`Grid`和 `AbsoluteLayout` 物件會設定為 `ContentPage`的 `Content` 屬性，而這些配置的子系實際上是 `Children` 集合中的專案。 不過，這些 `Content` 和 `Children` 屬性在 XAML 檔案中沒有任何地方。

您當然可以將 `Content` 和 `Children` 屬性納入為屬性元素，例如**XamlPlusCode**範例中的：

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

在 Xamarin 中定義的專案，可以在類別的 `ContentProperty` 屬性中加上旗標的一個屬性。 如果您在線上 Xamarin. 表單檔中查閱 `ContentPage` 類別，您會看到此屬性：

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

這表示不需要 `Content` 屬性元素標記。 在開始和結束 `ContentPage` 標記之間出現的任何 XML 內容，都會假設指派給 `Content` 屬性。

 `StackLayout`、`Grid`、`AbsoluteLayout`和 `RelativeLayout` 全都衍生自 `Layout<View>`，如果您在 [Xamarin] 檔中查閱 `Layout<T>`，則會看到另一個 `ContentProperty` 屬性：

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

，可讓配置的內容自動加入至 `Children` 集合，而不需要明確 `Children` 屬性專案標記。

其他類別也有 `ContentProperty` 的屬性定義。 例如，`Label` 的 content 屬性是 `Text`。 查看其他人的 API 檔。

## <a name="platform-differences-with-onplatform"></a>與 OnPlatform 的平臺差異

在單一頁面應用程式中，通常會在頁面上設定 [`Padding`] 屬性，以避免覆寫 iOS 狀態列。 在程式碼中，您可以針對此目的使用 `Device.RuntimePlatform` 屬性：

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

您也可以使用[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)和[`On`](xref:Xamarin.Forms.On)類別，在 XAML 中執行類似的動作。 在靠近頁面頂端的 `Padding` 屬性中，首先包含屬性元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

在這些標記中，包含 `OnPlatform` 標記。 `OnPlatform` 是泛型類別。 您需要指定泛型型別引數，在此案例中為 `Thickness`，這是 `Padding` 屬性的類型。 幸好，有一個 XAML 屬性專門用來定義稱為 `x:TypeArguments`的泛型引數。 這應符合您所設定之屬性的類型：

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

`OnPlatform` 具有名為 `Platforms` 的屬性，這是 `On` 物件的 `IList`。 針對該屬性使用 property 元素標記：

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

現在加入 `On` 元素。 針對每個設定，將 [`Platform`] 屬性和 [`Value`] 屬性設為 [`Thickness`] 屬性的 [標記]：

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

此標記可以簡化。 `OnPlatform` 的 content 屬性 `Platforms`，因此可以移除這些屬性元素的標記：

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

`On` 的 `Platform` 屬性是 `IList<string>`類型，因此如果這些值相同，您可以包含多個平臺：

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

因為 Android 和 UWP 設定為預設值 `Padding`，所以可以移除該標記：

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

這是在 XAML 中設定平臺相依 `Padding` 屬性的標準方式。 如果 `Value` 設定無法以單一字串表示，您可以為它定義屬性元素：

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
> `OnPlatform` 標記延伸也可以在 XAML 中用來自訂以每個平臺為基礎的 UI 外觀。 它提供與 `OnPlatform` 和 `On` 類別相同的功能，但具有更精確的標記法。 如需詳細資訊，請參閱[OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform)。

## <a name="summary"></a>摘要

在屬性專案和附加屬性中，已建立許多基本 XAML 語法。 不過，有時候您需要以間接方式（例如從資源字典）將屬性設定為物件。 這種方法在下一個部分（第3部分）中涵蓋[。XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)。

## <a name="related-links"></a>相關連結

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第1部分。使用 XAML 消費者入門](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第3部分。XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第4部分。資料系結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第5部分：從資料系結到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
