---
title: "使用滑桿"
description: "使用滑桿選取連續值的範圍。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/16/2018
ms.openlocfilehash: ce5d8c46282d3831edcf58af63b66d1f6292f75b
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="using-slider"></a>使用滑桿

_使用滑桿選取連續值的範圍。_

Xamarin.Forms [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)是可由使用者選取操作的水平列`double`連續範圍的值。 

`Slider`定義三個屬性的型別`double`:

- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/) 最小範圍的預設值為 0。
- [`Maximum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/) 是範圍的最大的預設值是 1。
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Value/) 滑桿的值，範圍必須介於`Minimum`和`Maximum`且具有預設值為 0。

所有的三個屬性都由`BindableProperty`物件。 `Value`屬性具有預設繫結模式`BindingMode.TwoWay`，這表示它是適合作為繫結來源使用的應用程式中[模型-檢視-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構。 

> [!WARNING]
> 就內部而言，`Slider`可確保`Minimum`是小於`Maximum`。 如果`Minimum`或`Maximum`曾經設定以便`Minimum`是不小於`Maximum`，例外狀況。 請參閱[**預防措施**](#precautions)下面章節，如需有關設定`Minimum`和`Maximum`屬性。

`Slider`轉`Value`屬性，讓它變成之間`Minimum`和`Maximum`(含） 之間。 如果`Minimum`屬性設定為值大於`Value`屬性，`Slider`設定`Value`屬性`Minimum`。 同樣地，如果`Maximum`設定的值小於`Value`，然後`Slider`設定`Value`屬性`Maximum`。 

`Slider` 定義[ `ValueChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Slider.ValueChanged/)時引發的事件`Value`變更時，透過使用者操作`Slider`或程式設定為當`Value`直接屬性。 A`ValueChanged`也引發事件時`Value`前段中所述，會強制轉型屬性。 

[ `ValueChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ValueChangedEventArgs/)物件隨附`ValueChanged`事件有兩個屬性，這兩個型別`double`: [ `OldValue` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ValueChangedEventArgs.OldValue/)和[ `NewValue` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ValueChangedEventArgs.NewValue/). 在事件引發時，值`NewValue`相同`Value`屬性`Slider`物件。

> [!WARNING]
> 請勿使用未受限制的水平配置選項`Center`， `Start`，或`End`與`Slider`。 在 Android 和 UWP`Slider`摺疊成橫條的長度為零，並在 iOS、 列是很短。 保留預設值`HorizontalOptions`設定`Fill`，而未使用的寬度`Auto`當放置`Slider`中`Grid`版面配置。

## <a name="basic-slider-code-and-markup"></a>基本的滑桿程式碼和標記

[ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)範例開始使用三個頁面的功能相同，但會以不同的方式實作。 第一頁會使用僅包含 C# 程式碼、 與事件處理常式程式碼中，使用 XAML，第二個和第三個是能夠避免此事件處理常式的 XAML 檔案中使用資料繫結。

### <a name="creating-a-slider-in-code"></a>建立程式碼中的滑桿

**基本滑桿程式碼**頁面[ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)範例會示範建立顯示`Slider`和兩個`Label`程式碼中的物件：

```csharp
public class BasicSliderCodePage : ContentPage
{
    public BasicSliderCodePage()
    {
        Label rotationLabel = new Label
        {
            Text = "ROTATING TEXT",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Label displayLabel = new Label
        {
            Text = "(uninitialized)",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Slider slider = new Slider
        {
            Maximum = 360
        };
        slider.ValueChanged += (sender, args) =>
        {
            rotationLabel.Rotation = slider.Value;
            displayLabel.Text = String.Format("The Slider value is {0}", args.NewValue);
        };

        Title = "Basic Slider Code";
        Padding = new Thickness(10, 0);
        Content = new StackLayout
        {
            Children =
            {
                rotationLabel,
                slider,
                displayLabel
            }
        };
    }
}
```

`Slider`會初始化為具有`Maximum`360 的屬性。 `ValueChanged`處理常式的`Slider`使用`Value`屬性`slider`物件來設定`Rotation`屬性的第一個`Label`並用`String.Format`方法`NewValue`屬性若要設定的事件引數`Text`屬性的第二個`Label`。 這兩種方法來取得目前的值`Slider`是可互換的。 

以下是執行 iOS、 Android 和通用 Windows 平台 (UWP) 上的裝置的程式：

[![基本的滑桿的程式碼](slider-images/BasicSliderCode.png "基本滑桿的程式碼")](slider-images/BasicSliderCode-Large.png#lightbox)

第二個`Label`顯示 「 （未初始化） 」 的文字，直到`Slider`操作的情況下，第一個`ValueChanged`来引發的事件。 請注意，所顯示的小數位數的三個平台不同。 這些差異則與相關的平台實作`Slider`和本文稍後的一節會討論[平台實作差異](#implementations)。

### <a name="creating-a-slider-in-xaml"></a>在 XAML 中建立滑桿

**基本滑桿 XAML**頁面功能上是相同**基本滑桿程式碼**大部分是在 XAML 而實作：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderXamlPage"
             Title="Basic Slider XAML"
             Padding="10, 0">
    <StackLayout>
        <Label x:Name="rotatingLabel" 
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider Maximum="360"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

程式碼後置檔案包含的處理常式`ValueChanged`事件：

```csharp
public partial class BasicSliderXamlPage : ContentPage
{
    public BasicSliderXamlPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double value = args.NewValue;
        rotatingLabel.Rotation = value;
        displayLabel.Text = String.Format("The Slider value is {0}", value);
    }
}
```

您也可為事件處理常式，以取得`Slider`引發的事件，透過`sender`引數。 `Value`屬性包含目前的值：

```csharp
double value = ((Slider)sender).Value;
```

如果`Slider`XAML 檔案中的名稱提供給物件`x:Name`屬性 (例如，"slider")，則事件處理常式無法參考該物件直接：

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>資料繫結滑桿

**基本滑桿繫結**頁面會顯示如何撰寫幾乎相當的程式，以消除`Value`事件處理常式使用[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderBindingsPage"
             Title="Basic Slider Bindings"
             Padding="10, 0">
    <StackLayout>
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference slider}, 
                                  Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360" />

        <Label x:Name="displayLabel"
               Text="{Binding Source={x:Reference slider}, 
                              Path=Value, 
                              StringFormat='The Slider value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Rotation`屬性的第一個`Label`繫結至`Value`屬性`Slider`，如為`Text`屬性的第二個`Label`與`StringFormat`規格。 **基本滑桿繫結**頁面函式稍有不同的兩個先前的頁面： 第一次出現的頁面中，第二個`Label`顯示值的文字字串。 這是使用資料繫結的好處。 若要顯示沒有資料繫結的文字，您必須明確初始化`Text`屬性`Label`或模擬的引發`ValueChanged`藉由從類別建構函式呼叫事件處理常式的事件。

<a name="precautions" />

## <a name="precautions"></a>預防措施

值`Minimum`屬性必須永遠是的值小於`Maximum`屬性。 下列程式碼片段會導致`Slider`引發例外狀況：

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

C# 編譯器產生在順序中，設定這兩個屬性的程式碼及何時`Minimum`屬性設為 10，大於預設`Maximum`值為 1。 您可以在此情況下避免例外狀況，藉由設定`Maximum`屬性第一次：

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

設定`Maximum`為 20 不成問題，因為它大於預設`Minimum`設定為 0。 當`Minimum`已經設定，這個值是小於`Maximum`20 的值。

在 XAML 中，有相同的問題。 設定的屬性，可確保訂單`Maximum`大於一律`Minimum`:

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

您可以設定`Minimum`和`Maximum`負數，但只能在順序中的值其中`Minimum`是一定小於`Maximum`:

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

`Value`屬性一律會是大於或等於`Minimum`值且小於或等於`Maximum`。 如果`Value`設為超出該範圍的值，此值會被強制為範圍內，但會引發任何例外狀況。 例如，此程式碼將*不*引發例外狀況：

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

相反地，`Value`屬性強制為`Maximum`值為 1。

以下是程式碼片段，如上所示： 

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

當`Minimum`設定為 10，則`Value`也會設為 10。 

如果`ValueChanged`附加事件處理常式在時間為止，`Value`屬性會強制轉型為 0 時，其預設值以外的值則`ValueChanged`引發事件。 以下是 XAML 程式碼片段： 

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

當`Minimum`設為 10，`Value`也會設為 10，而`ValueChanged`引發事件。 已建構的頁面其餘部分，再處理常式可能會嘗試參考尚未建立其他項目頁面上的，也可能會發生。 您可以加入某些程式碼以`ValueChanged`處理常式，以檢查`null`頁面上的其他元素的值。 或者，您可以設定`ValueChanged`之後的事件處理常式`Slider`值尚未初始化。

<a name="implementations" />

## <a name="platform-implementation-differences"></a>平台實作的差異

稍早所示的螢幕擷取畫面顯示的值`Slider`有不同數目的小數位數。 這與如何`Slider`Android 和 UWP 平台上實作。

### <a name="the-android-implementation"></a>Android 的實作 

Android 的實作`Slider`為基礎的 Android [ `SeekBar` ](https://developer.xamarin.com/api/type/Android.Widget.SeekBar/)和一律設定[ `Max` ](https://developer.xamarin.com/api/property/Android.Widget.ProgressBar.Max/)屬性設為 1000年。 這表示`Slider`在 Android 上有只 1,001 離散的值。 如果您設定`Slider`有`Minimum`的 0 和`Maximum`為 5000，則為`Slider`操作，`Value`屬性具有值 0、 5、 10、 15 和其他等等。 

### <a name="the-uwp-implementation"></a>UWP 實作

UWP 實作`Slider`根據 UWP [ `Slider` ](/uwp/api/windows.ui.xaml.controls.slider)控制項。 `StepFrequency`屬性 UWP`Slider`設定的差異為`Maximum`和`Minimum`屬性除以 10，但不是能大於 1。 

例如，針對預設範圍 0 到 1 的`StepFrequency`屬性設定為 0.1。 做為`Slider`操作，`Value`屬性會限制為 0、 0.1、 0.2、 0.3、 0.4、 0.5、 0.6、 0.7、 0.8、 0.9 及 1.0。 (這是中的最後一頁明顯[ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)範例。)當之間的差異`Maximum`和`Minimum`屬性可為 10 或更新版本，然後`StepFrequency`設為 1，而`Value`屬性具有整數值。 

### <a name="the-stepslider-solution"></a>StepSlider 方案

更多功能`StepSlider`討論[章 27。自訂轉譯器](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)活頁簿*建立行動應用程式使用 Xamarin.Forms*。 `StepSlider`類似於`Slider`但會增加`Steps`屬性指定值之間的數字`Minimum`和`Maximum`。

## <a name="sliders-for-color-selection"></a>色彩選取滑桿

最後兩個頁面[ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)範例都使用三個`Slider`色彩選取的執行個體。 第一頁會處理在程式碼後置檔案中，所有的互動，而第二個的頁面會顯示如何使用資料繫結與 ViewModel。 

### <a name="handling-sliders-in-the-code-behind-file"></a>處理程式碼後置檔案中的滑桿 

**RGB 色彩滑桿**頁面會具現化`BoxView`顯示色彩，三個`Slider`選取色彩，以及三個紅色、 綠色和藍色元件的執行個體`Label`顯示這些色彩的項目值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.RgbColorSlidersPage"
             Title="RGB Color Sliders">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="Maximum" Value="255" />
            </Style>
            
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView x:Name="boxView"
                 Color="Black"
                 VerticalOptions="FillAndExpand" />

        <Slider x:Name="redSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="redLabel" />

        <Slider x:Name="greenSlider" 
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="greenLabel" />

        <Slider x:Name="blueSlider" 
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="blueLabel" />
    </StackLayout>
</ContentPage>
```

A`Style`提供這三個`Slider`一個 0 到 255 的範圍的項目。 `Slider`項目共用相同`ValueChanged`處理常式，實作程式碼後置檔案中：

```csharp
public partial class RgbColorSlidersPage : ContentPage
{
    public RgbColorSlidersPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (sender == redSlider)
        {
            redLabel.Text = String.Format("Red = {0:X2}", (int)args.NewValue);
        }
        else if (sender == greenSlider)
        {
            greenLabel.Text = String.Format("Green = {0:X2}", (int)args.NewValue);
        }
        else if (sender == blueSlider)
        {
            blueLabel.Text = String.Format("Blue = {0:X2}", (int)args.NewValue);
        }

        boxView.Color = Color.FromRgb((int)redSlider.Value,
                                      (int)greenSlider.Value,
                                      (int)blueSlider.Value);
    }
}
```

第一個區段設定`Text`屬性的其中一個`Label`簡短文字字串，表示值的執行個體`Slider`十六進位。 然後，這三個`Slider`會存取執行個體，以建立`Color`從 RGB 元件的值：

[![RGB 色彩滑桿](slider-images/RgbColorSliders.png "RGB 色彩滑桿")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>繫結至 ViewModel 的滑桿

**HSL 色彩滑桿**頁面會顯示如何使用以執行用來建立計算 ViewModel`Color`值從色調、 飽和度和亮度的值。 像所有的 ViewModels`HSLColorViewModel`類別會實作`INotifyPropertyChanged`介面，而且會引發`PropertyChanged`只要其中一個屬性變更事件：

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get 
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));
            }
        }
        get
        {
            return color;
        }
    }
}
```

ViewModels 和`INotifyPropertyChanged`文章中討論介面[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

**HslColorSlidersPage.xaml**檔案會具現化`HslColorViewModel`並將其設為頁面`BindingContext`屬性。 這可讓繫結到 ViewModel 中屬性的 XAML 檔案中的所有項目：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SliderDemos"
             x:Class="SliderDemos.HslColorSlidersPage"
             Title="HSL Color Sliders">

    <ContentPage.BindingContext>
        <local:HslColorViewModel Color="Chocolate" />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</ContentPage> 
```

做為`Slider`管理項目，`BoxView`和`Label`ViewModel 即更新項目：

[![HSL 色彩滑桿](slider-images/HslColorSliders.png "HSL 色彩滑桿")](slider-images/HslColorSliders-Large.png#lightbox)

`StringFormat`元件`Binding`標記延伸設為"F2"的格式顯示兩個小數位數。 (文章中有討論資料繫結中的格式化字串[字串格式化](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md)。)不過，UWP 版本的程式僅限於值為 0、 0.1、 0.2...0.9 及 1.0。 這是直接結果的 UWP 實作`Slider`上面所述的區段中[平台實作差異](#implementations)。

## <a name="related-links"></a>相關連結

- [滑桿示範範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)
- [滑桿應用程式開發介面](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)
