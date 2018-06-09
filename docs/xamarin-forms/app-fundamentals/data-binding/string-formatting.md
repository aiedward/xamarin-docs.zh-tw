---
title: Xamarin.Forms 字串格式
description: 本文說明如何使用 Xamarin.FOrms 資料繫結來格式化，並以字串形式顯示物件。 達到此目的的繫結的 StringFormat 設為標準.NET 格式字串以預留位置。
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: bdac74e4ec14797ec373f86b8a94c7af4d480951
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240297"
---
# <a name="xamarinforms-string-formatting"></a>Xamarin.Forms 字串格式

有時候很方便使用的資料繫結來顯示物件或值的字串表示。 例如，您可能想要使用`Label`要顯示的目前值`Slider`。 在此資料繫結`Slider`是來源，而目標為`Text`屬性`Label`。

顯示在程式碼中的字串，最強大的工具時，靜態[ `String.Format` ](https://developer.xamarin.com/api/member/System.String.Format/p/System.String/System.Object/)方法。 格式字串包含格式化各種類型的物件，以特定的程式碼，您可以包含其他文字，以及正在格式化的值。 請參閱[.NET 中格式化型別](/dotnet/standard/base-types/formatting-types/)如需有關字串格式的文件。

## <a name="the-stringformat-property"></a>StringFormat 屬性

這項工具會延用至資料繫結： 您設定[ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.StringFormat/)屬性`Binding`(或[ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.StringFormat/)屬性`Binding`標記延伸) 至標準.NET 格式字串的預留位置：

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

請注意，格式字串以可協助避免視為另一個 XAML 標記延伸的大括號 XAML 剖析器的單引號 （撇號） 字元分隔。 否則，字串的單引號字元是您要用於呼叫中顯示的浮點數的值相同的字串沒有`String.Format`。 格式規格`F2`會使顯示兩個小數位數的值。

`StringFormat`屬性才有意義的目標屬性的型別時`string`，而且繫結模式`OneWay`或`TwoWay`。 雙向繫結，如`StringFormat`僅適用於從來源傳送到目標的值。

您會發現在下一個發行項上[繫結路徑](binding-path.md)，可能會變成相當複雜且迂迴的資料繫結。 當偵錯這些資料繫結，您可以加入`Label`到 XAML 檔案中，使用`StringFormat`顯示某些中繼結果。 即使您可以使用它來顯示物件的類型，可幫助。

**字串格式化**頁面說明幾種用途的`StringFormat`屬性：

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

上的繫結`Slider`和`TimePicker`示範如何使用格式規格的特殊`double`和`TimeSpan`資料型別。 `StringFormat`所顯示的文字`Entry`檢視示範如何使用格式字串中指定雙引號`&quot;`HTML 實體。

XAML 檔案中的下一個區段是`StackLayout`與`BindingContext`設`x:Static`參考靜態的標記延伸`DateTime.Now`屬性。 第一個繫結具有任何屬性：

```xaml
<Label Text="{Binding}" />
```

這只會顯示`DateTime`值`BindingContext`預設格式。 第二個繫結顯示`Ticks`屬性`DateTime`，而其他兩個繫結顯示`DateTime`本身會向特定的格式設定。 請注意這`StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

如果您需要顯示格式字串中的左或右大括號，只要使用其中一組。

最後一個區段設定`BindingContext`值`Math.PI`並顯示與預設格式和兩個不同類型的數字格式化。

以下是所有三個平台上執行的程式：

[![字串格式](string-formatting-images/stringformatting-small.png "字串格式化")](string-formatting-images/stringformatting-large.png#lightbox "字串格式")

## <a name="viewmodels-and-string-formatting"></a>ViewModels 和格式字串

當您使用`Label`和`StringFormat`若要顯示的值也是 ViewModel 目標的檢視，您可以定義檢視來從繫結`Label`或從到 ViewModel `Label`。 一般情況下，第二種方法是最佳方法，因為它會驗證正在檢視與 ViewModel 之間的繫結。

這種方法如下所示**更好的色彩選取器**範例，您可使用做為相同的 ViewModel**簡單色彩選取器**所示的程式[**繫結模式**](binding-mode.md)發行項：

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

現在，有三組`Slider`和`Label`繫結至相同的項目來源中的屬性`HslColorViewModel`物件。 唯一的差別是`Label`具有`StringFormat`顯示每個屬性`Slider`值。

[![更好的色彩選取器](string-formatting-images/bettercolorselector-small.png "更色彩選取器")](string-formatting-images/bettercolorselector-large.png#lightbox "更色彩選取器")

您可能會想知道如何您無法在傳統的兩位數十六進位格式中顯示 RGB （紅色、 綠色、 藍色） 值。 這些整數值無法直接使用從`Color`結構。 一個解決方式是計算 ViewModel 中的色彩元件的整數值，並將其公開為屬性。 您無法再格式化使用`X2`格式規格。

另一個方法是多個一般： 您可以撰寫*繫結值轉換器*的更新版本的文件中所述[**繫結值轉換器**](converters.md)。

不過下, 一個發行項中，瀏覽[**繫結路徑**](binding-path.md)更多詳細資料，並示範如何使用它參考子屬性和集合中的項目。


## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [資料繫結 Xamarin.Forms 書籍章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
