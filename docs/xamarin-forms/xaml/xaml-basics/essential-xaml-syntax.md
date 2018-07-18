---
title: 第 2 部分。 基本 XAML 語法
description: 本文章說明基本 XAML 語法的功能屬性項目，並附加屬性。
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: ea448708403478cdaf565bc7abbed2af28c7a9bf
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268845"
---
# <a name="part-2-essential-xaml-syntax"></a>第 2 部分。 基本 XAML 語法

_XAML 是大部分針對具現化，並初始化物件。通常，屬性必須設定為複雜的物件無法輕鬆地表示為 XML 字串，但有時一個類別所定義的屬性必須設定子類別。這些兩個需求所需的屬性項目和附加的屬性的基本 XAML 語法功能。_

## <a name="property-elements"></a>屬性項目

在 XAML 中，類別的屬性通常會設定為 XML 屬性：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

不過，沒有在 XAML 中設定屬性的替代方式。 若要嘗試使用這個替代方案`TextColor`，先刪除現有`TextColor`設定：

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

兩個標記，新增包含的類別名稱和屬性名稱以逗號分隔的開始和結束標記：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

將屬性值設定為這些新的標籤，就像這樣的內容：

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

若要指定這兩種方式`TextColor`屬性的功能相同，但並不是使用兩種方式為相同的屬性，因為會有效地設定此屬性兩次，而且可能會模稜兩可。

以這種新語法，可以導入一些實用術語：

-  `Label` 是*物件項目*。 它是以 XML 元素來表示 Xamarin.Forms 物件。
-  `Text``VerticalOptions`，`FontAttributes`和`FontSize`是*屬性的屬性*。 它們是以 XML 屬性的 Xamarin.Forms 屬性。
-  在這個最後一個片段中，`TextColor`變得*屬性項目*。 它是 Xamarin.Forms 屬性，但它現在是 XML 項目。


定義的屬性項目可能會在第一次似乎 XML 語法的違規情形，但不是。 期限在 XML 中沒有任何特殊意義。 以 XML 解碼器，`Label.TextColor`是只是一般的子元素。

在 XAML 中，不過，此語法是非常特殊。 其中一個屬性元素的規則是都可出現在`Label.TextColor`標記。 屬性的值一律被定義為屬性項目開始和結束標記之間的內容。

您可以使用屬性項目語法上有一個以上屬性：

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

或者，您可以使用所有屬性的屬性項目語法：

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

在一開始，屬性項目語法似乎不必要的 long-winded 取代項目相當相當簡單，並在這些範例中，當然是大小寫。

不過，屬性項目語法便基本屬性的值會表示成簡單字串太複雜。 在屬性項目的標記內，您可以具現化另一個物件，並設定其屬性。 例如，您可以明確設定屬性例如`VerticalOptions`至`LayoutOptions`屬性設定的值：

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

另一個範例：`Grid`有兩個屬性，名為`RowDefinitions`和`ColumnDefinitions`。 這兩個屬性都屬於型別`RowDefinitionCollection`和`ColumnDefinitionCollection`，而這是集合的`RowDefinition`和`ColumnDefinition`物件。 您要用來設定這些集合的屬性項目語法。

以下是的 XAML 檔案的開頭`GridDemoPage`類別，顯示為屬性項目標記`RowDefinitions`和`ColumnDefinitions`集合：

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

請注意，用於定義自動調整大小的儲存格、 資料格的像素寬度和高度，以及星形設定的縮寫的語法。

## <a name="attached-properties"></a>附加屬性

您只看到了，`Grid`需要的屬性項目`RowDefinitions`和`ColumnDefinitions`集合定義的資料列和資料行。 不過，必須也有一些讓程式設計人員指出的資料列和資料行位置的每個子系`Grid`所在。

中的每個子系的標記`Grid`您指定的資料列和資料行的子系使用下列屬性：

-  `Grid.Row`
-  `Grid.Column`

這些屬性的預設值為 0。 您也可以指定是否子系跨越多個資料列或資料行具有這些屬性：

-  `Grid.RowSpan`
-  `Grid.ColumnSpan`

這兩個屬性具有預設值為 1。

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

`Grid.Row`和`Grid.Column`並不需要設定為 0，但通常包含為了加以釐清。

以下是什麼樣子上所有的三個平台：

[![](essential-xaml-syntax-images/griddemo.png "格線版面配置")](essential-xaml-syntax-images/griddemo-large.png#lightbox "格線版面配置")

可完全語法，從這些`Grid.Row`， `Grid.Column`， `Grid.RowSpan`，和`Grid.ColumnSpan`屬性看起來像是靜態欄位或屬性的`Grid`，但有趣，`Grid`並未定義任何名為的項目`Row`， `Column`， `RowSpan`，或`ColumnSpan`。

相反地，`Grid`定義四個可繫結的內容，名為`RowProperty`， `ColumnProperty`， `RowSpanProperty`，和`ColumnSpanProperty`。 這些是特殊類型的可繫結的屬性稱為*附加屬性*。 它們由定義`Grid`類別，但設定的子系`Grid`。

當您想要使用這些連接屬性中的程式碼，`Grid`類別提供靜態方法，名為`SetRow`， `GetColumn`，依此類推。 在 XAML 中，這些附加的屬性的設定中的子系的屬性，但`Grid`使用簡單的屬性名稱。

附加的屬性一律會識別在 XAML 檔案做為包含類別和屬性名稱以逗號分隔的屬性。 它們稱為*附加屬性*因為它們由一個類別定義 (在此情況下， `Grid`) 但附加其他物件 (在此情況下的子系`Grid`)。 在配置期間`Grid`可以查閱知道每個子系的放置位置這些附加屬性的值。

`AbsoluteLayout`類別會定義兩個附加的屬性，名為`LayoutBounds`和`LayoutFlags`。 以下是使用成比例的位置，才能實現棋盤式圖樣和調整大小功能`AbsoluteLayout`:

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

並在這個範例中：

[![](essential-xaml-syntax-images/absolutedemo-large.png "絕對配置")](essential-xaml-syntax-images/absolutedemo-large.png#lightbox "絕對版面配置")

結果類似這樣，您可能問題的常識使用 XAML。 當然，重複和規律的`LayoutBounds`矩形建議，可能會在程式碼中進一步實現。

這當然是合法的問題，而且沒有定義使用者介面時，平衡使用的程式碼和標記沒有問題。 所以可以輕鬆地以 XAML 定義一些視覺效果，然後使用建構函式的程式碼後置檔案將一些可能會更好產生迴圈中的多個視覺效果。

## <a name="content-properties"></a>內容屬性

在上一個範例中， `StackLayout`， `Grid`，和`AbsoluteLayout`物件都會設為`Content`屬性`ContentPage`，這些配置的子系是實際中的項目`Children`集合。 但這些`Content`和`Children`屬性是無處可去 XAML 檔案中。

您當然可以包含`Content`和`Children`屬性做為屬性項目，例如 in **XamlPlusCode**範例：

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

實際的問題是： 為何這些屬性的項目*不*所需的 XAML 檔案中？

允許在 XAML 中使用 Xamarin.Forms 中定義的項目有一個屬性中標示`ContentProperty`類別上的屬性。 如果您查閱`ContentPage`類別在線上的 Xamarin.Forms 文件，您會看到這個屬性：

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

這表示`Content`屬性項目標記並非必要項。 任何出現的開始和結束之間的 XML 內容`ContentPage`標記會假設為指派給`Content`屬性。

 `StackLayout``Grid`， `AbsoluteLayout`，和`RelativeLayout`都是衍生自`Layout<View>`，而且如果您查閱`Layout<T>`中的 Xamarin.Forms 文件中，您會看到另一個`ContentProperty`屬性：

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

可讓內容的配置會自動加入至`Children`集合不含明確`Children`屬性項目標記。

其他類別也具有`ContentProperty`屬性定義。 例如，內容屬性的`Label`是`Text`。 查看其他人的應用程式開發介面文件。

## <a name="platform-differences-with-onplatform"></a>使用 OnPlatform 的平台差異

在單一頁面應用程式，通常會設定`Padding`以避免覆寫 iOS 狀態列 頁面上的屬性。 在程式碼中，您可以使用`Device.RuntimePlatform`針對此用途的屬性：

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

您也可以執行在 XAML 中使用類似的項目`OnPlatform`和`On`類別。 第一次包含的屬性項目`Padding`接近頁面頂端的屬性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

兩個標記，包括`OnPlatform`標記。 `OnPlatform` 是泛型類別。 您必須指定泛型型別引數時，在此情況下， `Thickness`，這是類型`Padding`屬性。 幸運的是，沒有 XAML 屬性，定義泛型引數呼叫皆是特別為了`x:TypeArguments`。 這應該符合您正在設定屬性的型別：

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

`OnPlatform` 具有內容，名為`Platforms`也就是`IList`的`On`物件。 使用屬性項目標記，該屬性：

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

現在，加入`On`項目。 針對每個設定`Platform`屬性和`Value`屬性來標記`Thickness`屬性：

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

可以簡化這個標記。 內容屬性`OnPlatform`是`Platforms`，所以可以移除這些屬性項目標籤：

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

`Platform`屬性`On`的型別`IList<string>`，所以如果值相同，您可以包含多個平台：

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

因為 Android 和 UWP 都設定為預設值的`Padding`，可以移除的標記：

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

這是設定平台相依的標準方式`Padding`在 XAML 中的屬性。 如果`Value`設定無法由單一的字串表示，您可以為它定義屬性項目：

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

屬性項目和附加的屬性，大部分的基本 XAML 語法建立的。 不過，有時候您需要從資源字典中以間接方式，例如，設定屬性的物件。 這種方法在下一個部分中，部分涵蓋[3。XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)。



## <a name="related-links"></a>相關連結

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：開始使用 XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 3 部分：XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 4 部分：資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第 5 部分：從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
