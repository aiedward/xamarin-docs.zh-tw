---
title: Xamarin.Forms 字串格式化
description: 本文說明如何使用 Xamarin.FOrms 資料繫結來將物件作為字串格式化和顯示。 這可透過將 Binding 的 StringFormat 設定為具有預留位置的標準 .NET 格式化字串來達成。
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 87972df2b15a5bc1f6323a6953f809379cae4a50
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644530"
---
# <a name="xamarinforms-string-formatting"></a>Xamarin.Forms 字串格式化

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

有時候，使用資料繫結來顯示物件或值的字串表示相當方便。 例如，您可能想要使用 `Label` 來顯示目前 `Slider` 的值。 在此資料繫結中，`Slider` 是來源，而目標則是 `Label` 的 `Text` 屬性。

當以程式碼顯示字串時，最有效的工具是靜態 [`String.Format`](xref:System.String.Format(System.String,System.Object)) 方法。 格式化字串包含各種類型物件特定的格式化程式碼，且您可在要格式化的值外包含其他文字。 如需關於字串格式化的詳細資訊，請參閱[在 .NET 中將類型格式化](/dotnet/standard/base-types/formatting-types/)。

## <a name="the-stringformat-property"></a>StringFormat 屬性

這項功能會延用至資料繫結：您可將 `Binding` 的 [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) 屬性 (或 `Binding` 標記延伸的 [`StringFormat`](xref:Xamarin.Forms.Xaml.BindingExtension.StringFormat) 屬性) 設定為具有預留位置的標準 .NET 格式化字串：

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

請注意，格式化字串會以單引號字元分隔，讓 XAML 剖析器避免將大括號視作另一個的 XAML 標記延伸。 否則，沒有單引號字元的字串會和您在對 `String.Format` 的呼叫中，用來顯示浮點數值的字串相同。 `F2` 的格式化規格會讓值以兩個小數位數顯示。

只有在目標屬性為類型 `string`，且繫結模式為 `OneWay` 或 `TwoWay` 時，`StringFormat` 屬性才有效。 對於雙向繫結，`StringFormat` 僅適用於從來源傳遞至目標的值。

在接下來的 [Binding Path](binding-path.md) (繫結路徑) 一文中，您會發現資料繫結可能會變得既複雜又難懂。 而您可在對這些資料繫結進行偵錯時，將 `Label` 新增至具有 `StringFormat` 的 XAML 檔案，來顯示一些中繼結果。 即便您只是要使用它來顯示物件的類型，也相當實用。

**字串格式化**頁面示範了 `StringFormat` 屬性的數種用法：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="DataBindingDemos.StringFormattingPage"
             Title="String Formatting">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Blue" />
                <Setter Property="HeightRequest" Value="2" />
                <Setter Property="Margin" Value="0, 5" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <Slider x:Name="slider" />
        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The slider value is {0:F2}'}" />

        <BoxView />

        <TimePicker x:Name="timePicker" />
        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time,
                              StringFormat='The TimeSpan is {0:c}'}" />

        <BoxView />

        <Entry x:Name="entry" />
        <Label Text="{Binding Source={x:Reference entry},
                              Path=Text,
                              StringFormat='The Entry text is &quot;{0}&quot;'}" />

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:DateTime.Now}">
            <Label Text="{Binding}" />
            <Label Text="{Binding Path=Ticks,
                                  StringFormat='{0:N0} ticks since 1/1/1'}" />
            <Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
            <Label Text="{Binding StringFormat='The long date is {0:D}'}" />
        </StackLayout>

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:Math.PI}">
            <Label Text="{Binding}" />
            <Label Text="{Binding StringFormat='PI to 4 decimal points = {0:F4}'}" />
            <Label Text="{Binding StringFormat='PI in scientific notation = {0:E7}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

`Slider` 及 `TimePicker` 上的繫結顯示僅限於 `double` 和 `TimeSpan` 資料類型格式規格的用法。 顯示 `Entry` 檢視之文字的 `StringFormat` 則會示範如何透過使用 `&quot;` HTML 實體，來指定格式化字串中的雙引號。

XAML 檔案中的下一個區段是 `StackLayout`，其 `BindingContext` 已設定為參考靜態 `DateTime.Now` 屬性的 `x:Static` 標記延伸。 第一個繫結沒有屬性：

```xaml
<Label Text="{Binding}" />
```

這只會以預設格式顯示 `BindingContext` 的 `DateTime` 值。 第二個繫結會顯示 `DateTime` 的 `Ticks` 屬性，而其他兩個繫結則會以特定格式自行顯示 `DateTime`。 請注意此 `StringFormat`：

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

若您必須在格式化字串中顯示左或右大括號，直接成對地使用它們即可。

最後一個區段會將 `BindingContext` 設定為 `Math.PI` 的值，並以預設格式及兩種不同類型的數值格式加以顯示。

以下是程式執行情況：

[![字串格式化](string-formatting-images/stringformatting-small.png "字串格式化")](string-formatting-images/stringformatting-large.png#lightbox "字串格式化")

## <a name="viewmodels-and-string-formatting"></a>ViewModel 和字串格式化

當您使用 `Label` 及 `StringFormat` 顯示檢視的值 (同時為 ViewModel 的目標) 時，可以定義從檢視到 `Label` 或從 ViewModel 到 `Label` 的繫結。 一般來說，因為第二種方法可驗證檢視和 ViewModel 之間的繫結是否有效，所以其為最佳的方法。

此方法會在 **Better Color Selector** 範例中顯示，其使用與[**繫結模式**](binding-mode.md)一文所示之 **Simple Color Selector** 程式的 ViewModel 相同：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.BetterColorSelectorPage"
             Title="Better Color Selector">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

現在有三對 `Slider` 和 `Label` 元素均已繫結至 `HslColorViewModel` 物件中的相同來源屬性。 唯一的不同是`Label` 具有會顯示各 `Slider` 值的 `StringFormat` 屬性。

[![Better Color Selector](string-formatting-images/bettercolorselector-small.png "Better Color Selector")](string-formatting-images/bettercolorselector-large.png#lightbox "Better Color Selector")

您可能會不清楚如何以傳統二位數十六進位格式來顯示 RGB (紅、綠、藍) 值。 這是因為這些值都無法直接從 `Color` 結構使用。 其中一種解決方法是計算 ViewModel 中色彩元件的整數值，並將它們作為屬性公開。 接著您可以使用 `X2` 格式化規格將它們格式化。

另一種方法更加常見：您可以撰寫「繫結值轉換器」  ，這會在之後的[**繫結值轉換器**](converters.md)一文中提到。

不過，下一篇文章會更詳細地探索[**繫結路徑**](binding-path.md)，並向您示範如何使用它參考子屬性及集合中的項目。


## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
- [來自 Xamarin.Forms 書籍的資料繫結章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
