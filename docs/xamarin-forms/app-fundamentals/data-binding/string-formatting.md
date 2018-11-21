---
title: Xamarin.Forms 的字串格式化
description: 這篇文章說明如何使用 Xamarin.FOrms 資料繫結來 格式化並顯示為字串的物件。 這是藉由設定繫結的 StringFormat 以預留位置的標準.NET 格式字串來達成。
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 8efd93204b848113e0ed95c8066a5506eb517ac6
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52170945"
---
# <a name="xamarinforms-string-formatting"></a>Xamarin.Forms 的字串格式化

有時候很方便地使用資料繫結至顯示的物件或值的字串表示。 例如，您可能想要`Label`若要顯示的目前值`Slider`。 在此資料繫結`Slider`是來源，而且目標會`Text`屬性`Label`。

在程式碼中顯示字串，最強大的工具時，靜態[ `String.Format` ](xref:System.String.Format(System.String,System.Object))方法。 格式化字串，包含格式化各種類型的物件，以特定的程式碼，您可以包含其他文字，以及要格式化的值。 請參閱[在.NET 中格式化類型](/dotnet/standard/base-types/formatting-types/)如需有關字串格式化的文件。

## <a name="the-stringformat-property"></a>StringFormat 屬性

這項功能會延用至資料繫結： 您設定[ `StringFormat` ](xref:Xamarin.Forms.BindingBase.StringFormat)屬性`Binding`(或[ `StringFormat` ](xref:Xamarin.Forms.Xaml.BindingExtension.StringFormat)屬性`Binding`標記延伸) 至標準.NET 格式字串與一個預留位置：

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

請注意格式化的字串用來協助避免視為另一個 XAML 標記延伸的大括號，XAML 剖析器的單引號 （撇號） 字元分隔。 否則，該字串沒有單引號字元是相同的字串，您會使用的呼叫中顯示的浮點值`String.Format`。 格式規格`F2`造成會顯示具有兩個小數位數的值。

`StringFormat`屬性才有意義的目標屬性的型別時`string`，並繫結模式`OneWay`或`TwoWay`。 針對雙向繫結，`StringFormat`僅適用於從來源傳遞至目標的值。

如您所見的下一篇文章中，在[繫結路徑](binding-path.md)，可能會變成相當複雜且迂迴的資料繫結。 當偵錯這些資料繫結，您可以加入`Label`到 XAML 檔中，使用`StringFormat`顯示某些中繼結果。 即使您可以使用它來顯示物件的類型，可幫助。

**字串格式化**頁面上說明的幾種用途`StringFormat`屬性：

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

上的繫結`Slider`並`TimePicker`示範如何使用格式規格的特殊`double`和`TimeSpan`資料型別。 `StringFormat`會顯示從文字`Entry`檢視示範如何使用格式字串中指定雙引號`&quot;`HTML 實體。

在下一節中的 XAML 檔案`StackLayout`具有`BindingContext`設為`x:Static`標記延伸參考靜態`DateTime.Now`屬性。 第一個繫結具有任何屬性：

```xaml
<Label Text="{Binding}" />
```

這只會顯示`DateTime`的值`BindingContext`預設格式。 第二個繫結會顯示`Ticks`的屬性`DateTime`，而其他兩個繫結顯示`DateTime`本身具有特定格式。 請注意這`StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

如果您要顯示在格式化字串中的向左或右大括號，只要使用其中一組。

最後一個區段集合`BindingContext`的值`Math.PI`並顯示預設的格式設定和兩種不同的數字格式化。

以下是執行的程式：

[![字串格式化](string-formatting-images/stringformatting-small.png "字串格式化")](string-formatting-images/stringformatting-large.png#lightbox "字串格式化")

## <a name="viewmodels-and-string-formatting"></a>Viewmodel 和格式字串

當您使用`Label`並`StringFormat`若要顯示檢視，同時也是 ViewModel 的目標的值，您可以定義從檢視，以繫結`Label`或從以 ViewModel `Label`。 一般情況下，第二種方法是最佳方法，因為它會驗證正在 ViewModel 與檢視之間的繫結。

這種方法所示**更好的色彩選取器**範例中，它會使用相同的 ViewModel 作為**簡單色彩選取器**所示的程式[**繫結模式**](binding-mode.md)文章：

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

現在有三組`Slider`並`Label`繫結至相同的項目來源中的屬性`HslColorViewModel`物件。 唯一的差別在於`Label`已經`StringFormat`屬性來顯示每個`Slider`值。

[![更好色彩選取器](string-formatting-images/bettercolorselector-small.png "更色彩選取器")](string-formatting-images/bettercolorselector-large.png#lightbox "進一步色彩選取器")

您可能想知道您無法在如何在傳統的兩位數字十六進位格式中顯示 RGB （紅色、 綠色、 藍色） 值。 這些整數值無法直接使用從`Color`結構。 一個解決方案是計算的 ViewModel 中的色彩元件的整數值，並將它們公開為屬性。 您無法再格式化使用`X2`格式規格。

另一個方法是更多一般： 您可以撰寫*繫結值轉換器*之後的文章中所述[**繫結值轉換器**](converters.md)。

下一篇文章中，不過，探索[**繫結路徑**](binding-path.md)更詳細說明，並顯示如何使用它來參考子屬性和集合中的項目。


## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [資料繫結 Xamarin.Forms 書籍章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
