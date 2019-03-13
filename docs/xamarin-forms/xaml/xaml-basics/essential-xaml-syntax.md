---
title: 第 2 部分。 基本 XAML 語法
description: 這篇文章會說明基本 XAML 語法的功能屬性的項目，並附加屬性。
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 75d3c628a513b81af9dea7308ca49c2b490c4aff
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059739"
---
# <a name="part-2-essential-xaml-syntax"></a>第 2 部分。 基本 XAML 語法

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)

_XAML 是大部分被針對具現化並初始化物件。通常，屬性必須設定為複雜的物件無法輕鬆地表示為 XML 字串，但有時一個類別所定義的屬性必須設定的子類別。這些兩種需求需要基本 XAML 語法的功能屬性項目和附加的屬性。_

## <a name="property-elements"></a>Property 項目

在 XAML 中，類別的屬性通常會設為 XML 屬性：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

不過，還有在 XAML 中設定屬性的替代方式。 若要嘗試使用此替代方案`TextColor`，先刪除現有`TextColor`設定：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

開啟空白項目`Label`分隔成開始和結束標記的標記：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

在這些標記中，新增的類別名稱和以句號分隔屬性名稱所組成的開始與結束標記：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

將屬性值設定為這些新的標籤，就像這樣的內容中：

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

若要指定這兩種方式`TextColor`屬性的功能相同，但並不是使用兩種方式為相同的屬性，因為這會有效地設定此屬性兩次，並可能會模稜兩可。

使用此新的語法，可能會造成一些好用的術語：

-  `Label` 已*物件項目*。 它是以 XML 項目表示 Xamarin.Forms 物件。
-  `Text``VerticalOptions`，`FontAttributes`並`FontSize`會*property 屬性*。 它們都是以 XML 屬性的 Xamarin.Forms 屬性。
-  在該最終程式碼片段`TextColor`日益*property 項目*。 它是 Xamarin.Forms 屬性，但它現在是 XML 項目。


定義的屬性項目可能會在第一次似乎是 XML 語法的違規情形，但它不是。 期限在 XML 中沒有任何特殊意義。 以 XML 的解碼器，`Label.TextColor`是只是一般的子元素。

在 XAML，不過，此語法是非常特殊。 其中一個屬性元素的規則是沒有其他項目可以出現在`Label.TextColor`標記。 屬性的值一律定義為屬性項目開始和結束標記之間的內容。

您可以使用屬性元素語法，在多個屬性：

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

或者，您可以使用屬性元素語法的所有屬性：

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

一開始，屬性元素語法看起來像是的不必要的 long-winded 取代為相對很簡單的項目，並在這些範例中這個方法的確如此。

不過，屬性元素語法變得很重要時的屬性值太複雜而無法表示為一個簡單的字串。 在屬性項目的標記內，您可以具現化另一個物件，並設定其屬性。 例如，您可以明確設定屬性這類`VerticalOptions`至`LayoutOptions`屬性設定的值：

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

另一個範例：`Grid`有兩個屬性，名為`RowDefinitions`和`ColumnDefinitions`。 這兩個屬性都屬於型別`RowDefinitionCollection`並`ColumnDefinitionCollection`，而這是集合的`RowDefinition`和`ColumnDefinition`物件。 您要使用屬性元素語法來設定這些集合。

以下是為 XAML 檔案的開頭`GridDemoPage`類別，顯示的屬性項目標記`RowDefinitions`和`ColumnDefinitions`集合：

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

請注意縮寫的語法來定義自動調整大小的儲存格，像素寬度和高度，以及星型的設定儲存格。

## <a name="attached-properties"></a>附加屬性

您已經知道，`Grid`要求的屬性項目`RowDefinitions`和`ColumnDefinitions`集合定義的資料列和資料行。 不過，必須也有一些讓程式設計人員指出的資料列和資料行，每一個子系`Grid`所在。

每一個子系的標記內`Grid`您指定的資料列和資料行的子系使用下列屬性：

-  `Grid.Row`
-  `Grid.Column`

這些屬性的預設值為 0。 您也可以指定是否子系跨越多個資料列或資料行，這些屬性：

-  `Grid.RowSpan`
-  `Grid.ColumnSpan`

這兩個屬性有預設值為 1。

以下是完整的 GridDemoPage.xaml 檔案：

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

`Grid.Row`和`Grid.Column`並不需要設定為 0，但通常包含為了清楚起見。

看起來如下：

[![](essential-xaml-syntax-images/griddemo.png "格線版面配置")](essential-xaml-syntax-images/griddemo-large.png#lightbox "格線版面配置")

僅從語法中，這些`Grid.Row`， `Grid.Column`， `Grid.RowSpan`，和`Grid.ColumnSpan`靜態欄位或屬性的屬性會出現`Grid`，但有趣的是，`Grid`不會定義名為的任何項目`Row`， `Column`， `RowSpan`，或`ColumnSpan`。

相反地，`Grid`會定義四個可繫結的屬性，名為`RowProperty`， `ColumnProperty`， `RowSpanProperty`，和`ColumnSpanProperty`。 這些是特殊類型的可繫結的屬性，稱為*附加屬性*。 會定義的`Grid`類別，但設定的子系`Grid`。

當您想要使用這些項目附加在程式碼中的屬性`Grid`類別提供靜態方法，名為`SetRow`， `GetColumn`，依此類推。 在 XAML 中，這些附加的屬性會設定為的子系內的屬性，但`Grid`使用簡單的屬性名稱。

附加的屬性都可辨識在 XAML 檔案中做為包含類別和屬性名稱，以句點分隔的屬性。 在呼叫*附加屬性*因為會定義一個類別的 (在此情況下， `Grid`)，但連接至其他物件 (在此案例中的子系`Grid`)。 在配置期間`Grid`可以查閱這些附加的屬性，知道每個子系的位置的值。

`AbsoluteLayout`類別會定義兩個附加的屬性，名為`LayoutBounds`和`LayoutFlags`。 以下是以棋盤式圖樣，實現使用依比例的定位和調整大小功能`AbsoluteLayout`:

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

而以下是：

[![](essential-xaml-syntax-images/absolutedemo-large.png "絕對版面配置")](essential-xaml-syntax-images/absolutedemo-large.png#lightbox "絕對版面配置")

什麼像這樣的項目，您可能問題的常識使用 XAML。 當然，重複和的規律`LayoutBounds`矩形所示，可能會在程式碼中進一步實現。

這當然是合法的問題，並定義您的使用者介面時，平衡使用程式碼和標記沒有問題。 它是在 XAML 中定義一些視覺效果，然後使用程式碼後置檔案的建構函式將可能會更好的迴圈中產生一些更多視覺效果變得更加容易。

## <a name="content-properties"></a>內容屬性

在上一個範例中， `StackLayout`， `Grid`，並`AbsoluteLayout`物件都會設為`Content`屬性`ContentPage`，以及這些配置的子系是實際上中的項目`Children`集合。 但這些`Content`和`Children`屬性是沒地方 XAML 檔案中。

您當然可以包含`Content`並`Children`做為屬性項目，例如在屬性**XamlPlusCode**範例：

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

真正的問題是： 為什麼是這些屬性的項目*不*XAML 檔案中所需？

用於 XAML 中定義在 Xamarin.Forms 中的項目可以有一個屬性中加上旗標`ContentProperty`類別上的屬性。 如果您查閱`ContentPage`類別在線上的 Xamarin.Forms 文件，您會看到這個屬性：

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

這表示`Content`屬性項目標記不需要。 任何出現的開始和結束之間的 XML 內容`ContentPage`標記會假設為指派給`Content`屬性。

 `StackLayout``Grid`， `AbsoluteLayout`，並`RelativeLayout`都是衍生自`Layout<View>`，而且如果您查閱`Layout<T>`中的 Xamarin.Forms 文件中，您會看到另一個`ContentProperty`屬性：

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

可讓內容會自動加入至版面配置`Children`集合，而不需要明確`Children`屬性項目標記。

其他類別也具有`ContentProperty`屬性定義。 例如，內容屬性的`Label`是`Text`。 請檢查其他人的 API 文件。

## <a name="platform-differences-with-onplatform"></a>OnPlatform 平台差異

在單一頁面應用程式，通常會設定`Padding`以避免覆寫 iOS 狀態列 頁面上的屬性。 在程式碼中，您可以使用`Device.RuntimePlatform`針對此用途的屬性：

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

您也可以執行類似在 XAML 中使用`OnPlatform`和`On`類別。 第一個包含屬性項目`Padding`屬性頁面的頂端附近：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

兩個標記，包括`OnPlatform`標記。 `OnPlatform` 是泛型類別。 您必須指定泛型型別引數時，在此情況下， `Thickness`，這是種`Padding`屬性。 所幸還有專為定義呼叫的泛型引數的 XAML 屬性`x:TypeArguments`。 這應該符合您所設定之屬性的型別：

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

`OnPlatform` 有一個叫做屬性`Platforms`也就是說`IList`的`On`物件。 使用該屬性的屬性項目標記：

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

現在加入`On`項目。 針對每個設定`Platform`屬性和`Value`屬性來標記`Thickness`屬性：

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

可以簡化此標記。 內容屬性`OnPlatform`是`Platforms`，因此可以移除這些屬性-元素標記：

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

`Platform`的屬性`On`別的`IList<string>`，因此，如果值相同，您可以包含多個平台：

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

因為 Android 及 UWP 設定的預設值為`Padding`，可以移除標記：

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

這是設定平台相依的標準方式`Padding`在 XAML 中的屬性。 如果`Value`設定無法以單一字串表示，您可以為它定義屬性的項目：

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

## <a name="summary"></a>總結

Property 項目和附加的屬性，大部分的基本 XAML 語法建立的。 不過，有時候您需要從資源字典中以間接方式，例如，設定屬性物件。 在下一步 部分中，部分涵蓋此方法的[3。XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)。



## <a name="related-links"></a>相關連結

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：開始使用 XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 3 部分：XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 4 部分：資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第 5 部分：從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
