---
title: Xamarin.Forms 繫結值轉換器
description: 本文說明如何藉由實作值轉換器來轉換 Xamarin.Forms 資料繫結內的值 (即所謂的繫結轉換器或繫結值轉換器)。
ms.prod: xamarin
ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 05ad12de77e8895a23cd364b90abfbfb567ac573
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771614"
---
# <a name="xamarinforms-binding-value-converters"></a>Xamarin.Forms 繫結值轉換器

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

資料繫結通常會將資料從來源屬性傳輸至目標屬性，並在某些情況下從目標屬性傳輸至來源屬性。 當來源和目標屬性都屬於相同類型，或其中一種類型可以透過隱含轉換來轉換成其他類型時，傳輸即會簡單明瞭。 若非此情況，則必須採取類型轉換。

在[**字串格式化**](string-formatting.md)一文中，您已看到可如何使用資料繫結的 `StringFormat` 屬性，將任何類型轉換為字串。 對於其他類型的轉換，您需要在實作 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 介面的類別中撰寫一些特殊程式碼。 (通用 Windows 平台包含 `Windows.UI.Xaml.Data` 命名空間中名為 [`IValueConverter`](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/) 的類似類別，但 `IValueConverter` 位於 `Xamarin.Forms` 命名空間中。)實作 `IValueConverter` 的類別稱為「值轉換器」，但也經常稱為「繫結轉換器」或「繫結值轉換器」。

## <a name="the-ivalueconverter-interface"></a>IValueConverter 介面

假設您希望定義來源屬性為 `int` 類型但目標屬性為 `bool` 的資料繫結。 您希望此資料繫結在整數來源等於 0 時產生 `false` 值，否則產生 `true`。  

您可以使用實作 `IValueConverter` 介面的類別來執行此作業：

```csharp
public class IntToBoolConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value != 0;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? 1 : 0;
    }
}
```

將此類別執行個體設為 `Binding` 類別的 [`Converter`](xref:Xamarin.Forms.Binding.Converter) 屬性，或設為 `Binding` 標記延伸的 [`Converter`](xref:Xamarin.Forms.Xaml.BindingExtension.Converter) 屬性。 此類別會成為資料繫結的一部分。

當資料在 `OneWay` 或 `TwoWay` 繫結從來源移到目標時，會呼叫 `Convert` 方法。 `value` 參數是資料繫結來源的物件或值。 此方法必須傳回資料繫結目標類型的值。 這裡示範的方法會將 `value` 參數轉換為 `int`，然後將其與 0 比較，以取得 `bool` 傳回值。

當資料在 `TwoWay` 或 `OneWayToSource` 繫結從目標移到來源時，會呼叫 `ConvertBack` 方法。 `ConvertBack` 會執行相反的轉換：其會假設 `value` 參數目標的 `bool`，並將其轉換成來源的 `int` 傳回值。

如果資料繫結也包含 `StringFormat` 設定，則會在結果格式化為字串前叫用值轉換器。

[**資料繫結示範**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)中的**啟用按鈕**頁面範例，示範如何在資料繫結中使用此值轉換器。 `IntToBoolConverter` 會在頁面的資源字典中具現化。 然後會透過 `StaticResource` 標記延伸來加以參考，以設定兩個資料繫結中的 `Converter` 屬性。 共用頁面上多個資料繫結之間的資料轉換器十分常見：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.EnableButtonsPage"
             Title="Enable Buttons">
    <ContentPage.Resources>
        <ResourceDictionary>
            <local:IntToBoolConverter x:Key="intToBool" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <Entry x:Name="entry1"
               Text=""
               Placeholder="enter search term"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Search"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry1},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />

        <Entry x:Name="entry2"
               Text=""
               Placeholder="enter destination"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Submit"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry2},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />
    </StackLayout>
</ContentPage>
```

如果值轉換器用於您應用程式的多個頁面中，您可以在 **App.XAML** 檔案的資源字典中將其具現化。

[啟用按鈕] 頁面示範當 `Button` 根據使用者鍵入 `Entry` 檢視的文字而執行作業時，會出現的常見需求。 如果 `Entry` 中未鍵入任何內容，則應該停用 `Button`。 每個 `Button` 都會在其 `IsEnabled` 屬性中包含資料繫結。 資料繫結來源是對應 `Entry` 之 `Text` 屬性的 `Length` 屬性。 如果該 `Length` 屬性不為 0，則值轉換器會傳回 `true` 並啟用 `Button`：

[![啟用按鈕](converters-images/enablebuttons-small.png "啟用按鈕")](converters-images/enablebuttons-large.png#lightbox "啟用按鈕")

請注意，每個 `Entry` 中的 `Text` 屬性會初始化為空白字串。 根據預設，`Text` 屬性為 `null`，資料繫結在此情況下將無法運作。

某些值轉換器是專為特定應用程式所撰寫，其他值轉換器則為通用。 如果您知道某個值轉換器僅限用於 `OneWay` 繫結，則 `ConvertBack` 方法只能傳回 `null`。

上述的 `Convert` 方法會隱含假設 `value` 引數是 `int` 類型，且傳回值必須是 `bool` 類型。 同樣，`ConvertBack` 方法會假設 `value` 引數是 `bool` 類型，且傳回值為 `int`。 若非此情況，則會發生執行階段例外狀況。

您可以撰寫更通用且可接受數個不同資料類型的值轉換器。 `Convert` 和 `ConvertBack` 方法可以透過 `value` 參數來使用 `as` 或 `is` 運算子，或者呼叫該參數上的 `GetType` 來判斷其類型，然後進行適當的操作。 每個方法的傳回值預期類型，由 `targetType` 參數指定。 某些時候，值轉換器可搭配不同目標類型的資料繫結使用；值轉換器可以使用 `targetType` 引數來執行正確類型的轉換。

如果要執行的轉換因不同文化特性而有別，請使用針對此用途的 `culture` 參數。 `Convert` 和 `ConvertBack` 的 `parameter` 引述將在本文中稍後討論。

## <a name="binding-converter-properties"></a>繫結轉換器屬性

值轉換器類別可以具有屬性和泛型參數。 此特定值轉換器會將來源的 `bool` 轉換為目標的 `T` 類型：

```csharp
public class BoolToObjectConverter<T> : IValueConverter
{
    public T TrueObject { set; get; }

    public T FalseObject { set; get; }

    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? TrueObject : FalseObject;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return ((T)value).Equals(TrueObject);
    }
}
```

[交換器指標] 頁面會示範其可如何用於顯示 `Switch` 檢視的值。 雖然將轉換器具現化為資源字典中的資源很常見，但是此頁面會示範另一個方法：每個值轉換器都在 `Binding.Converter` 屬性元素標籤之間具現化。 `x:TypeArguments` 指出泛型引數，且 `TrueObject` 和 `FalseObject` 都設為該類型的物件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SwitchIndicatorsPage"
             Title="Switch Indicators">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>

            <Style TargetType="Switch">
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Subscribe?" />
            <Switch x:Name="switch1" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch1}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Of course!"
                                                         FalseObject="No way!" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Allow popups?" />
            <Switch x:Name="switch2" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Yes"
                                                         FalseObject="No" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
                <Label.TextColor>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Color"
                                                         TrueObject="Green"
                                                         FalseObject="Red" />
                        </Binding.Converter>
                    </Binding>
                </Label.TextColor>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Learn more?" />
            <Switch x:Name="switch3" />
            <Label FontSize="18"
                   VerticalOptions="Center">
                <Label.Style>
                    <Binding Source="{x:Reference switch3}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Style">
                                <local:BoolToObjectConverter.TrueObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Indubitably!" />
                                        <Setter Property="FontAttributes" Value="Italic, Bold" />
                                        <Setter Property="TextColor" Value="Green" />
                                    </Style>                                    
                                </local:BoolToObjectConverter.TrueObject>

                                <local:BoolToObjectConverter.FalseObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Maybe later" />
                                        <Setter Property="FontAttributes" Value="None" />
                                        <Setter Property="TextColor" Value="Red" />
                                    </Style>
                                </local:BoolToObjectConverter.FalseObject>
                            </local:BoolToObjectConverter>
                        </Binding.Converter>
                    </Binding>
                </Label.Style>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

在最後三個 `Switch` 和 `Label` 配對中，泛型引數設定為 `Style`，並會為 `TrueObject` 和 `FalseObject` 的值提供完整 `Style` 物件。 這些會覆寫設定於資源字典中 `Label` 的隱含樣式，因此該樣式中的屬性會明確指派給 `Label`。 切換 `Switch` 會導致對應的 `Label` 反映變更：

[![切換指標](converters-images/switchindicators-small.png "切換指標")](converters-images/switchindicators-large.png#lightbox "切換指標")

您也可以使用 [`Triggers`](~/xamarin-forms/app-fundamentals/triggers.md)，根據其他檢視，在使用者介面中實作類似的變更。

## <a name="binding-converter-parameters"></a>繫結轉換器參數

`Binding` 類別會定義 [`ConverterParameter`](xref:Xamarin.Forms.Binding.ConverterParameter) 屬性，而 `Binding` 標記延伸也會定義 [`ConverterParameter`](xref:Xamarin.Forms.Xaml.BindingExtension.ConverterParameter) 屬性。 如果設定此屬性，則值會傳遞至 `Convert` 和 `ConvertBack` 方法作為 `parameter` 引數。 即使值轉換器的執行個體在數個資料繫結中共用，在執行稍有不同的轉換時，`ConverterParameter` 也可能會不同。

`ConverterParameter` 的使用方式會以色彩選取程式來示範。 在此情況下，`RgbColorViewModel` 具有 `double` 類別的三個屬性，名為 `Red`、`Green` 和 `Blue`，用於建構 `Color` 值：

```csharp
public class RgbColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Red
    {
        set
        {
            if (color.R != value)
            {
                Color = new Color(value, color.G, color.B);
            }
        }
        get
        {
            return color.R;
        }
    }

    public double Green
    {
        set
        {
            if (color.G != value)
            {
                Color = new Color(color.R, value, color.B);
            }
        }
        get
        {
            return color.G;
        }
    }

    public double Blue
    {
        set
        {
            if (color.B != value)
            {
                Color = new Color(color.R, color.G, value);
            }
        }
        get
        {
            return color.B;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Red"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Green"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Blue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

`Red`、`Green` 和 `Blue` 屬性的範圍介於 0 和 1 之間。 不過，建議您以兩位數十六進位值來顯示元件。

若要在 XAML 中顯示這些十六進位值，這些值必須乘以 255、轉換為整數，並在 `StringFormat` 屬性中以 "X2" 規格進行格式化。 值轉換器可以處理前兩個工作 (乘以 255 和轉換為整數)。 您可以使用 `ConverterParameter` 屬性指定乘法因數來讓值轉換器盡可能通用，這表示其會輸入 `Convert` 和 `ConvertBack` 方法作為 `parameter` 引數：

```csharp
public class DoubleToIntConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)Math.Round((double)value * GetParameter(parameter));
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value / GetParameter(parameter);
    }

    double GetParameter(object parameter)
    {
        if (parameter is double)
            return (double)parameter;

        else if (parameter is int)
            return (int)parameter;

        else if (parameter is string)
            return double.Parse((string)parameter);

        return 1;
    }
}
```

`Convert` 會將 `double` 轉換為 `int` 同時乘以 `parameter` 值；`ConvertBack` 會將整數 `value` 參數除以 `parameter` 並傳回 `double` 結果。 (在如下所示的程式中，值轉換器僅可在字串格式化時使用，因此並未使用 `ConvertBack`。)

根據資料繫結是在程式碼或 XAML 中定義，`parameter` 引數的類型可能會不同。 如果 `ConverterParameter` 屬性 `Binding` 設定於程式碼中，則可能會設為數值：

```csharp
binding.ConverterParameter = 255;
```

`ConverterParameter` 屬性為 `Object` 類型，因此 C# 編譯器會將常值 255 解譯為整數，並將屬性設定為該值。

不過，在 XAML 中，`ConverterParameter` 可能會設定如下：

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

255 看起來像是數字，但因為 `ConverterParameter` 為 `Object` 類型，所以 XAML 剖析器會將 255 視為字串。

基於這個理由，上述值轉換器會包含個別的 `GetParameter` 方法，來處理 `parameter` 為 `double`、`int` 或 `string` 類型的案例。  

[RGB 色彩選取器] 頁面會遵循兩個隱含樣式的定義，在資源字典中將 `DoubleToIntConverter` 具現化：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.RgbColorSelectorPage"
             Title="RGB Color Selector">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <local:DoubleToIntConverter x:Key="doubleToInt" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:RgbColorViewModel Color="Gray" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Red}" />
            <Label Text="{Binding Red,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0:X2}'}" />

            <Slider Value="{Binding Green}" />
            <Label Text="{Binding Green,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0:X2}'}" />

            <Slider Value="{Binding Blue}" />
            <Label>
                <Label.Text>
                    <Binding Path="Blue"
                             StringFormat="Blue = {0:X2}"
                             Converter="{StaticResource doubleToInt}">
                        <Binding.ConverterParameter>
                            <x:Double>255</x:Double>
                        </Binding.ConverterParameter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

`Red` 和 `Green` 屬性的值會以 `Binding` 標記延伸顯示。 不過，`Blue` 屬性會將 `Binding` 類別具現化，以示範可以如何將明確的 `double` 值設定為 `ConverterParameter` 屬性。

結果如下：

[![RGB 色彩選取器](converters-images/rgbcolorselector-small.png "RGB 色彩選取器")](converters-images/rgbcolorselector-large.png#lightbox "RGB 色彩選取器")

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
- [來自 Xamarin.Forms 書籍的資料繫結章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
