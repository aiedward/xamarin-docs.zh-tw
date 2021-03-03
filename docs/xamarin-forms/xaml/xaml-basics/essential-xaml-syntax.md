---
title: 第 2 部分： 基本 XAML 語法
description: 本文說明 property 元素和附加屬性的基本 XAML 語法功能。
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 23d24ab7477bb7d9e95e4d78f25f334ae13a8ea2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368514"
---
# <a name="part-2-essential-xaml-syntax"></a>第 2 部分： 基本 XAML 語法

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML 大部分是設計用來具現化和初始化物件。但是，屬性通常必須設定為複雜的物件，而這些物件無法輕鬆地以 XML 字串表示，而且有時候必須在子類別上設定一個類別所定義的屬性。這兩個需求需要屬性元素和附加屬性的基本 XAML 語法功能。_

## <a name="property-elements"></a>Property 元素

在 XAML 中，類別的屬性通常會設定為 XML 屬性：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

不過，您可以使用 XAML 中的另一種方式來設定屬性。 若要嘗試這種替代方法 `TextColor` ，請先刪除現有的 `TextColor` 設定：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

開啟空白元素 `Label` 標記，方法是將其分隔成開始和結束標記：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

在這些標記內，加入包含類別名稱的開頭和結束記號，以及以句號分隔的屬性名稱：

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

這兩種指定屬性的方式 `TextColor` 在功能上是相等的，但請勿針對相同的屬性使用兩種方式，因為這會有效地設定屬性兩次，而且可能不明確。

有了這種新語法，可引進一些便利的術語：

- `Label` 是  *物件元素*。 它是以 Xamarin.Forms XML 元素表示的物件。
- `Text`、  `VerticalOptions` `FontAttributes` 和  `FontSize` 是  *屬性屬性*。 它們是 Xamarin.Forms 以 XML 屬性工作表示的屬性。
- 在最後一個程式碼片段中， `TextColor` 已成為  *property 元素*。 它是一個 Xamarin.Forms 屬性，但它現在是 XML 元素。

屬性專案的定義一開始可能會違反 XML 語法，但卻不是。 在 XML 中，句號沒有特殊意義。 對 XML 解碼器而言， `Label.TextColor` 只是一般的子項目。

不過，在 XAML 中，這個語法非常特殊。 屬性元素的其中一個規則是，標記中不能出現任何其他專案 `Label.TextColor` 。 屬性的值一律定義為屬性-元素開始和結束標記之間的內容。

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

一開始，屬性元素的語法看起來就像是一種不必要的長期冗長取代，因為這一點相當簡單，而在這些情況下，當然是如此。

不過，當屬性的值太複雜而無法表示為簡單字串時，屬性元素語法就會變得很重要。 在屬性元素標記內，您可以具現化另一個物件，並設定其屬性。 例如，您可以使用屬性設定，將屬性（例如）明確設定 `VerticalOptions` 為 `LayoutOptions` 值：

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

另一個範例： `Grid` 具有兩個名為和的屬性 `RowDefinitions` `ColumnDefinitions` 。 這兩個屬性的類型為，而 `RowDefinitionCollection` `ColumnDefinitionCollection` 是 `RowDefinition` 和物件的 `ColumnDefinition` 集合。 您必須使用屬性專案語法來設定這些集合。

以下是類別的 XAML 檔案開頭 `GridDemoPage` ，顯示和集合的屬性元素標記 `RowDefinitions` `ColumnDefinitions` ：

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

請注意定義自動調整大小的儲存格、圖元寬度和高度的儲存格，以及星星設定的縮寫語法。

## <a name="attached-properties"></a>附加屬性

您剛剛看到， `Grid` 和集合的屬性元素需要 `RowDefinitions` 定義資料 `ColumnDefinitions` 列和資料行。 但是，程式設計人員也必須有一些方法，來指出的每個子系所在的資料列和資料行 `Grid` 。

在的每一個子系的標記內， `Grid` 您可以使用下列屬性來指定該子系的資料列和資料行：

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

`Grid.Row`和 `Grid.Column` 設定為0並非必要，但通常是為了清楚起見而納入。

如下所示：

[![方格版面配置](essential-xaml-syntax-images/griddemo.png)](essential-xaml-syntax-images/griddemo-large.png#lightbox)

這些、、和屬性只會在語法中 `Grid.Row` `Grid.Column` `Grid.RowSpan` `Grid.ColumnSpan` 看起來是靜態欄位或屬性，但是有趣的是，不會 `Grid` `Grid` 定義名為 `Row` 、 `Column` 、或的任何內容 `RowSpan` `ColumnSpan` 。

相反地，會 `Grid` 定義四個名為 `RowProperty` 、、 `ColumnProperty` `RowSpanProperty` 和 `ColumnSpanProperty` 的可系結屬性。 這些是可系結屬性的特殊類型，稱為 *附加屬性*。 它們是由類別所定義， `Grid` 但會在的子系上進行設定 `Grid` 。

當您想要在程式碼中使用這些附加屬性時， `Grid` 類別會提供名為、等等的靜態方法 `SetRow` `GetColumn` 。 但是在 XAML 中，這些附加屬性會使用簡單的屬性名稱，設定為的子系中的屬性 `Grid` 。

在 XAML 檔案中，附加屬性一律可辨識為包含類別和屬性名稱（以句號分隔）的屬性。 它們稱為 *附加屬性* ，因為在此情況下，它們是由一個類別 (定義， `Grid`) 但附加至其他物件 (在此案例中為) 的子系 `Grid` 。 在版面配置期間， `Grid` 可以查閱這些附加屬性的值，以瞭解放置每個子系的位置。

`AbsoluteLayout`類別會定義兩個名為 `LayoutBounds` 和 `LayoutFlags` 的附加屬性。 以下是使用的比例定位和調整大小功能所實現的棋盤模式 `AbsoluteLayout` ：

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

以下是：

[![絕對版面配置](essential-xaml-syntax-images/absolutedemo-large.png)](essential-xaml-syntax-images/absolutedemo-large.png#lightbox)

對於這類的事情，您可能會問到使用 XAML 的明智之處。 當然，矩形的重複和變更規律性會 `LayoutBounds` 建議您最好在程式碼中實現。

這當然是合理的考慮，在定義使用者介面時，不會有平衡程式碼和標記使用的問題。 您很容易就能在 XAML 中定義某些視覺效果，然後使用程式碼後端檔案的函式，來加入一些可能更能在迴圈中產生的視覺效果。

## <a name="content-properties"></a>內容屬性

在先前的範例中， `StackLayout` 、 `Grid` 和 `AbsoluteLayout` 物件會設定為的 `Content` 屬性 `ContentPage` ，而這些配置的子系實際上是集合中的專案 `Children` 。 但是這些 `Content` 和 `Children` 屬性在 XAML 檔案中並不存在。

您當然可以將 `Content` 和 `Children` 屬性包含為 property 元素，例如在 **XamlPlusCode** 範例中：

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

真正的問題是：為什麼 XAML 檔案中 *不* 需要這些屬性元素？

中定義用於 XAML 的專案，可以在 Xamarin.Forms 類別的屬性中加上旗標的一個屬性 `ContentProperty` 。 如果您 `ContentPage` 在線上檔中查閱類別 Xamarin.Forms ，您會看到此屬性：

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

這表示 `Content` 不需要屬性元素標記。 在開始與結束標記之間出現的任何 XML 內容 `ContentPage` 都會假設指派給 `Content` 屬性。

 `StackLayout`、 `Grid` 、 `AbsoluteLayout` 和 `RelativeLayout` 都是衍生自 `Layout<View>` ，而且如果您 `Layout<T>` 在檔中查閱，  Xamarin.Forms 將會看到另一個 `ContentProperty` 屬性：

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

這可讓版面配置的內容自動新增至集合， `Children` 而不需要明確 `Children` 的屬性元素標記。

其他類別也有 `ContentProperty` 屬性定義。 例如，的 content 屬性 `Label` 是 `Text` 。 查看其他人的 API 檔。

## <a name="platform-differences-with-onplatform"></a>OnPlatform 的平臺差異

在單一頁面應用程式中，通常會在 `Padding` 頁面上設定屬性，以避免覆寫 iOS 狀態列。 在程式碼中，您可以使用 `Device.RuntimePlatform` 屬性做為此用途：

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

您也可以使用和類別，在 XAML 中進行類似的動作 [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) [`On`](xref:Xamarin.Forms.On) 。 首先，將屬性的屬性元素包含在 `Padding` 頁面頂端附近：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

在這些標記內，包含 `OnPlatform` 標記。 `OnPlatform` 是泛型類別。 您必須指定泛型型別引數，在此案例中 `Thickness` 為屬性的類型 `Padding` 。 幸運的是，有一個 XAML 屬性專門用來定義名為的泛型引數 `x:TypeArguments` 。 這應該符合您要設定的屬性類型：

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

`OnPlatform` 具有一個名為的屬性 `Platforms` ，它是 `IList` `On` 物件的。 使用該屬性的屬性元素標記：

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

現在新增 `On` 元素。 針對每一個，將屬性和屬性設定 `Platform` `Value` 為屬性的標記 `Thickness` ：

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

可以簡化此標記。 的 content 屬性 `OnPlatform` 為 `Platforms` ，因此可以移除這些屬性元素標記：

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

的 `Platform` 屬性 `On` 是類型 `IList<string>` ，因此如果值相同，您可以包含多個平臺：

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

因為 Android 和 UWP 是設定為預設值，所以 `Padding` 可以移除該標記：

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

這是在 XAML 中設定平臺相依屬性的標準方式 `Padding` 。 如果 `Value` 設定無法以單一字串表示，您可以為其定義屬性元素：

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
> `OnPlatform`標記延伸也可以在 XAML 中用來自訂每個平臺的 UI 外觀。 它提供與與類別相同的 `OnPlatform` 功能 `On` ，但有更精確的標記法。 如需詳細資訊，請參閱 [OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

## <a name="summary"></a>總結

使用 property 元素和附加屬性，已建立許多基本 XAML 語法。 不過，有時候您需要以間接方式（例如，從資源字典）將屬性設定為物件。 這種方法涵蓋于下一個部分，第 [3 部分。XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)。

## <a name="related-links"></a>相關連結

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第1部分。使用 XAML 消費者入門](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第3部分。XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第4部分。資料系結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第5部分。從資料系結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)