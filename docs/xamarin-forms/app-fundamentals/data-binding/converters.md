---
title: Xamarin.Forms 繫結值轉換器
description: 這篇文章說明如何轉型或轉換中的 Xamarin.Forms 資料繫結的值，藉由實作值轉換器 （即所謂的繫結轉換子或繫結值轉換器）。
ms.prod: xamarin
ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 28892692133020de1fa5a6eb007bb3f9bcf2612b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997476"
---
# <a name="xamarinforms-binding-value-converters"></a>Xamarin.Forms 繫結值轉換器

資料繫結通常將資料從傳輸來源屬性至目標屬性，以及從目標屬性某些情況下，來源屬性。 當來源和目標屬性都屬於相同類型，或一種類型可以轉換成其他類型透過隱含的轉換，會直接使用此移轉。 時，不是，型別轉換必須採取的地方。

在  [**字串格式化**](string-formatting.md)文章中，您已看到如何使用`StringFormat`資料繫結至任何類型轉換為字串的屬性。 對於其他類型的轉換中，您需要撰寫一些特殊的程式碼中實作的類別[ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter)介面。 (通用 Windows 平台包含類似的類別，名為[ `IValueConverter` ](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/)中`Windows.UI.Xaml.Data`命名空間，但這`IValueConverter`處於`Xamarin.Forms`命名空間。)類別實作`IValueConverter`稱為*值轉換器*，但它們也經常稱為*繫結轉換器*或是*繫結值轉換器*。

## <a name="the-ivalueconverter-interface"></a>IValueConverter 介面

假設您想要定義資料繫結的型別中的 [來源] 屬性所在`int`但目標屬性為`bool`。 您想要產生此資料繫結`false`值等於 0，整數來源時和`true`否則。  

您可以實作的類別與`IValueConverter`介面：

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

設定這個類別的執行個體[ `Converter` ](xref:Xamarin.Forms.Binding.Converter)屬性`Binding`類別，或者[ `Converter` ](xref:Xamarin.Forms.Xaml.BindingExtension.Converter)屬性`Binding`標記延伸。 這個類別會成為資料繫結的一部分。

`Convert`資料來源的資料會移到中的目標時呼叫方法`OneWay`或`TwoWay`繫結。 `value`參數是物件或資料繫結來源值。 此方法必須傳回的資料繫結目標類型的值。 方法如下所示轉型`value`參數來`int`，然後將它與 0`bool`傳回值。

`ConvertBack`時資料會從目標移到中的來源，方法呼叫`TwoWay`或`OneWayToSource`繫結。 `ConvertBack` 會執行相反的轉換： 它會假設`value`參數是`bool`目標，並將它轉換成`int`傳回來源的值。

如果資料繫結也包含`StringFormat`設定，值轉換器之前叫用結果格式化為字串。

**啟用按鈕**頁面[**資料繫結示範**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)範例會示範如何在資料繫結中使用此值轉換器。 `IntToBoolConverter`具現化頁面的資源字典中。 然後會使用參考`StaticResource`標記延伸，以設定`Converter`兩個資料繫結中的屬性。 它是很常見共用多個頁面上的資料繫結之間的資料轉換器：

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

如果值轉換器會用於您的應用程式的多個頁面中，您可以加以具現化中的資源字典**App.xaml**檔案。

**啟用按鈕**頁面會示範常見時所需`Button`執行的作業，根據使用者輸入的文字`Entry`檢視。 如果沒有已輸入到`Entry`，則`Button`應該停用。 每個`Button`包含資料繫結在其`IsEnabled`屬性。 資料繫結來源`Length`的屬性`Text`屬性對應`Entry`。 如果該`Length`屬性不是 0，則傳回值轉換器`true`而`Button`已啟用：

[![啟用按鈕](converters-images/enablebuttons-small.png "啟用按鈕")](converters-images/enablebuttons-large.png#lightbox "啟用按鈕")

請注意，`Text`中每個屬性`Entry`會初始化為空字串。 `Text`屬性是`null`，以及將資料繫結將無法在此情況下。

某些值轉換器會寫入專門針對特定的應用程式，而其他人已經被一般化。 如果您知道只會在使用值轉換器`OneWay`繫結，則`ConvertBack`方法可以只傳回`null`。

`Convert`隱含如上所示的方法會假設`value`引數是類型`int`傳回的值必須是型別`bool`。 同樣地，`ConvertBack`方法會假設`value`引數是類型`bool`傳回的值，而且`int`。 如果不是大小寫，則會發生執行階段例外狀況。

您可以撰寫更一般化，並接受數個不同的資料類型的值轉換器。 `Convert`並`ConvertBack`方法可以使用`as`或`is`運算子`value`參數，或可以呼叫`GetType`內容判斷其類型，然後再將該參數上適當的。 每個方法的傳回值的預期型別由指定`targetType`參數。 某些情況下，值轉換器可搭配資料繫結的不同目標類型;可以使用值轉換器`targetType`引數來執行轉換為正確的型別。

如果因不同文化特性的轉換作業，請使用`culture`針對此用途的參數。 `parameter`引數`Convert`和`ConvertBack`將在本文稍後討論。

## <a name="binding-converter-properties"></a>繫結轉換子屬性

值轉換器的類別可以有屬性和泛型參數。 這個特定的值轉換子轉換`bool`類型的物件來源`T`目標：

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

**交換器指標**頁面會示範如何使用它來顯示值`Switch`檢視。 雖然是很常見的資源字典中的資源，值轉換器具現化，此頁面會示範另一個方法： 每個值轉換器會具現化之間`Binding.Converter`屬性項目標記。 `x:TypeArguments`表示泛型引數，並`TrueObject`和`FalseObject`都設為該類型的物件：

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

三個到最後一個`Switch`並`Label`配對，泛型引數設定為`Style`，和整個`Style`物件提供的值`TrueObject`和`FalseObject`。 這些覆寫的隱含樣式`Label`設定資源字典中，因此該樣式中的屬性明確指派給`Label`。 切換`Switch`會導致對應`Label`以反映變更：

[![切換指標](converters-images/switchindicators-small.png "切換指標")](converters-images/switchindicators-large.png#lightbox "切換指標")

您也可使用[ `Triggers` ](~/xamarin-forms/app-fundamentals/triggers.md)根據其他檢視使用者介面中實作類似的變更。

## <a name="binding-converter-parameters"></a>繫結轉換子參數

`Binding`類別會定義[ `ConverterParameter` ](xref:Xamarin.Forms.Binding.ConverterParameter)屬性，而`Binding`標記延伸也會定義[ `ConverterParameter` ](xref:Xamarin.Forms.Xaml.BindingExtension.ConverterParameter)屬性。 如果設定這個屬性，則值會傳遞至`Convert`並`ConvertBack`方法做為`parameter`引數。 即使執行個體的值轉換器會共用數個資料繫結，`ConverterParameter`不同於執行稍有不同的轉換。

使用`ConverterParameter`示範中色彩選取範圍的程式。 在此情況下，`RgbColorViewModel`有三個屬性的型別`double`名為`Red`， `Green`，並`Blue`，用來建構`Color`值：

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

`Red`， `Green`，和`Blue`屬性範圍介於 0 和 1 之間。 不過，您可能會偏好，元件顯示為兩位數十六進位值。

若要顯示這些十六進位值為 XAML，它們必須乘以 255，轉換為整數，然後格式規格的 「 X2"`StringFormat`屬性。 值轉換器可以處理的前兩個工作 （乘以 255 和轉換成整數）。 您可以使用指定的乘法因數來讓值轉換器，以盡可能一般化，`ConverterParameter`屬性，這表示它進入`Convert`並`ConvertBack`方法為`parameter`引數：

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

`Convert`將從轉換`double`要`int`同時乘以`parameter`值;`ConvertBack`除以整數`value`引數`parameter`，並傳回`double`結果。 (在程式中，如下所示，值轉換器可僅與字串格式化，因此`ConvertBack`未使用。)

型別`parameter`引數是可能會不同，取決於是否定義程式碼或 XAML 中的資料繫結。 如果`ConverterParameter`屬性`Binding`設定程式碼中，很可能設為數值：

```csharp
binding.ConverterParameter = 255;
```

`ConverterParameter`屬性的類型是`Object`，因此 C# 編譯器會將常值 255 解譯為整數，並將屬性設定為該值。

不過，XAML 中`ConverterParameter`可能會設定如下：

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

因為，255 的看起來會像數字，但`ConverterParameter`別的`Object`，XAML 剖析器會將 255 視為字串。

基於這個理由，如上所示的值轉換器包含個別`GetParameter`方法來處理案例`parameter`型別`double`， `int`，或`string`。  

**RGB 色彩選取器**頁面會具現化`DoubleToIntConverter`其下列的兩個隱含樣式定義的資源字典中：

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

值`Red`並`Green`屬性會顯示與`Binding`標記延伸。 `Blue`屬性，不過，具現化`Binding`類別，以示範如何明確`double`值設定為`ConverterParameter`屬性。

結果如下：

[![RGB 色彩選取器](converters-images/rgbcolorselector-small.png "RGB 色彩選取器")](converters-images/rgbcolorselector-large.png#lightbox "RGB 色彩選取器")


## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [資料繫結 Xamarin.Forms 書籍章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
