---
title: Xamarin.Forms 滑桿
description: Xamarin.Forms 滑桿是可由使用者選取從連續範圍的雙精度浮點數值操作的水平列。 這篇文章說明如何使用滑桿類別的連續值範圍從選取的值。
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
ms.openlocfilehash: fa339d9fd404cf74aa603d853abde5f9128e57b5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250694"
---
# <a name="xamarinforms-slider"></a>Xamarin.Forms 滑桿

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)

_使用滑桿選取從連續值的範圍。_

Xamarin.Forms [ `Slider` ](xref:Xamarin.Forms.Slider)是可由使用者選取操作的水平列`double`從連續範圍的值。

`Slider`會定義三個屬性的型別`double`:

- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) 最小的範圍，預設值為 0。
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) 是最大值，預設值為 1。
- [`Value`](xref:Xamarin.Forms.Slider.Value) 滑桿的值，範圍必須介於`Minimum`和`Maximum`且具有預設值為 0。

所有的三個屬性都會受到`BindableProperty`物件。 `Value`屬性具有預設繫結模式`BindingMode.TwoWay`，這表示它很適合做為使用應用程式中的繫結來源[Model View ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構。

> [!WARNING]
> 就內部而言，`Slider`可確保`Minimum`是小於`Maximum`。 如果`Minimum`或是`Maximum`曾經設定以便`Minimum`是不小於`Maximum`，會引發例外狀況。 請參閱[**預防措施**](#precautions)如需有關設定下面章節`Minimum`和`Maximum`屬性。

`Slider`強制轉型`Value`屬性，使其之間`Minimum`和`Maximum`（包含頭尾)。 如果`Minimum`屬性設定為值大於`Value`屬性，`Slider`設定`Value`屬性設`Minimum`。 同樣地，如果`Maximum`的值設定小於`Value`，然後`Slider`設定`Value`屬性設`Maximum`。

`Slider` 定義[ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged)時引發的事件`Value`變更，不論是透過使用者操作`Slider`或程式時設定`Value`直接屬性。 A`ValueChanged`引發事件時也當`Value`前段中所述，會強制轉型屬性。

[ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs)隨附的物件`ValueChanged`事件有兩個屬性，這兩個型別`double`: [ `OldValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue)並[ `NewValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue). 在引發事件時，windows 7`NewValue`等同`Value`屬性`Slider`物件。

`Slider` 也會定義`DragStarted`和`DragCompleted`開頭和結尾的拖曳動作所引發的事件。 不同於[ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged)事件`DragStarted`並`DragCompleted`透過使用者操作只會引發事件`Slider`。 當`DragStarted`事件引發時， `DragStartedCommand`，型別的`ICommand`，會執行。 同樣地，當`DragCompleted`事件引發時， `DragCompletedCommand`，型別的`ICommand`，會執行。

> [!WARNING]
> 請勿使用未受限制的水平版面配置選項`Center`， `Start`，或`End`使用`Slider`。 在 Android 和 UWP，`Slider`列長度為零，並在 iOS 上，列摺疊是很短。 保留預設值`HorizontalOptions`設定`Fill`，而未使用的寬度`Auto`當放置`Slider`在`Grid`版面配置。

`Slider`也會定義數個會影響其外觀的屬性：

- [`MinimumTrackColor`](xref:Xamarin.Forms.Slider.MinimumTrackColorProperty) 是一個長條色彩捲動方塊的左邊。
- [`MaximumTrackColor`](xref:Xamarin.Forms.Slider.MaximumTrackColorProperty) 是一個長條色彩捲動方塊的右邊。
- [`ThumbColor`](xref:Xamarin.Forms.Slider.ThumbColorProperty) 是捲動方塊的色彩。
- [`ThumbImage`](xref:Xamarin.Forms.Slider.ThumbImageProperty) 是要用於基本原則是，類型的映像[ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource)。

> [!NOTE]
> `ThumbColor`和`ThumbImage`屬性互斥。 如果設定這兩個屬性，`ThumbImage`屬性將會優先。

## <a name="basic-slider-code-and-markup"></a>滑桿的基本程式碼和標記

[ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)範例開頭的三個頁面的功能相同，但會以不同的方式實作。 第一頁會使用僅為 C# 程式碼、 第二個程式碼中，事件處理常式中使用 XAML 和第三個可使用 XAML 檔案中的資料繫結，以避免事件處理常式。

### <a name="creating-a-slider-in-code"></a>建立程式碼中的滑桿

**滑桿的基本程式碼**頁面[ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)範例示範如何建立`Slider`並將兩個`Label`程式碼中的物件：

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

`Slider`初始化為有`Maximum`360 屬性。 `ValueChanged`處理常式`Slider`會使用`Value`屬性`slider`若要設定的物件`Rotation`屬性的第一個`Label`，並使用`String.Format`方法`NewValue`屬性若要設定的事件引數`Text`屬性的第二個`Label`。 這兩種方法來取得目前的值`Slider`是可互換的。

以下是裝置 iOS、 Android 和通用 Windows 平台 (UWP) 上執行的程式：

[![基本的滑桿的程式碼](slider-images/BasicSliderCode.png "基本的滑桿的程式碼")](slider-images/BasicSliderCode-Large.png#lightbox)

第二個`Label`會顯示"（未初始化） 」 的文字，直到`Slider`操作，因而導致第一個`ValueChanged`引發的事件。 請注意，所顯示的小數位數不同的每個平台。 這些差異相關的平台實作`Slider`稍後一節中的這篇文章討論[平台實作差異](#implementations)。

### <a name="creating-a-slider-in-xaml"></a>在 XAML 中建立滑桿

**基本的滑桿 XAML**頁功能上是相同**滑桿的基本程式碼**但是實作大部分是在 XAML 中：

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

您也可取得事件處理常式`Slider`所引發的事件，透過`sender`引數。 `Value`屬性包含目前的值：

```csharp
double value = ((Slider)sender).Value;
```

如果`Slider`物件所指定的 XAML 檔案中的名稱`x:Name`屬性 (例如，"slider")，則事件處理常式無法參考該物件直接：

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>資料繫結滑桿

**基本的滑桿繫結**頁面會顯示如何撰寫幾乎相當的程式，可減少`Value`使用的事件處理常式[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md):

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

`Rotation`屬性的第一個`Label`繫結至`Value`屬性`Slider`，因為`Text`第二個屬性`Label`具有`StringFormat`規格。 **基本的滑桿繫結**頁面函式稍有不同兩個先前的頁面：第一次出現的頁面中，第二個`Label`顯示文字字串，其值。 這是使用資料繫結的優點。 若要顯示沒有資料繫結的文字，您必須特別初始化`Text`的屬性`Label`，或模擬的引發`ValueChanged`藉由從類別建構函式呼叫的事件處理常式的事件。

<a name="precautions" />

## <a name="precautions"></a>預防措施

值`Minimum`屬性一律必須的值小於`Maximum`屬性。 下列程式碼片段會導致`Slider`引發例外狀況：

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

C# 編譯器會產生在順序中，設定這兩個屬性的程式碼及何時`Minimum`屬性設定為 10，大於預設`Maximum`值為 1。 您可以在此情況下避免例外狀況，藉由設定`Maximum`屬性第一次：

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

設定`Maximum`為 20 並不成問題，因為它大於預設值`Minimum`值為 0。 當`Minimum`設定，這個值是小於`Maximum`值為 20。

在 XAML 中，有相同的問題。 設定屬性的順序可確保`Maximum`一律是大於`Minimum`:

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

您可以設定`Minimum`並`Maximum`負數的數字，但只能在訂單中的值所在`Minimum`是一定小於`Maximum`:

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

`Value`屬性一律會是大於或等於`Minimum`值且小於或等於`Maximum`。 如果`Value`設定的值超出該範圍中，值將會強制轉型為落在範圍內，但會引發任何例外狀況。 比方說，這段程式碼將會*不*引發例外狀況：

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

相反地，`Value`屬性會強制轉型`Maximum`值為 1。

以下是如上所示的程式碼片段：

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

當`Minimum`設為 10，則`Value`也會設定為 10。

如果`ValueChanged`已附加事件處理常式時，`Value`屬性會強制轉型為 0，其預設值以外的項目則`ValueChanged`引發事件。 以下是 XAML 程式碼片段：

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

當`Minimum`設為 10，`Value`也會設定為 10，而`ValueChanged`引發事件時。 這可能是頁面的其餘部分都建構完畢，並處理常式可能會嘗試參考頁面上尚未建立的其他項目之前。 您可能想要新增一些程式碼`ValueChanged`處理常式會檢查`null`頁面上的其他元素的值。 或者，您可以設定`ValueChanged`之後的事件處理常式`Slider`已初始化值。

<a name="implementations" />

## <a name="platform-implementation-differences"></a>平台實作差異

稍早所示的螢幕擷取畫面顯示的值`Slider`不同數目的小數位數。 這涉及如何`Slider`在 Android 和 UWP 平台上實作。

### <a name="the-android-implementation"></a>Android 的實作

Android 的實作`Slider`為基礎的 Android [ `SeekBar` ](https://developer.xamarin.com/api/type/Android.Widget.SeekBar/) ，並一律設定[ `Max` ](https://developer.xamarin.com/api/property/Android.Widget.ProgressBar.Max/)屬性設為 1000年。 這表示`Slider`在 Android 上有只 1,001 離散的值。 如果您設定`Slider`能夠`Minimum`為 0，`Maximum`為 5000，則為`Slider`操作，`Value`屬性具有值 0、 5、 10、 15 及其他等等。

### <a name="the-uwp-implementation"></a>UWP 實作

UWP 實作`Slider`為基礎的 UWP [ `Slider` ](/uwp/api/windows.ui.xaml.controls.slider)控制項。 `StepFrequency`屬性的 UWP`Slider`設定的差異`Maximum`和`Minimum`屬性除以 10，但不是能大於 1。

例如，針對預設範圍 0 到 1`StepFrequency`屬性設定為 0.1。 作為`Slider`操作，`Value`屬性會限制為 0、 0.1、 0.2、 0.3、 0.4、 0.5，0.6，0.7，0.8、 0.9 及 1.0。 (這是最後一頁中明顯[ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)範例。)時之間的差異`Maximum`並`Minimum`屬性可為 10 或更新版本，然後`StepFrequency`設定為 1，而`Value`屬性具有整數值。

### <a name="the-stepslider-solution"></a>StepSlider 解決方案

更靈活`StepSlider`討論[第 27 章。自訂轉譯器](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)活頁簿*使用 Xamarin.Forms 建立行動應用程式*。 `StepSlider`大致`Slider`但會增加`Steps`屬性來指定值之間的數字`Minimum`和`Maximum`。

## <a name="sliders-for-color-selection"></a>色彩選取範圍的滑桿

最後兩個中的分頁[ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)範例都使用三個`Slider`中色彩選取範圍的執行個體。 第一頁會處理在程式碼後置檔案中，所有的互動，而第二個頁面會顯示如何以資料繫結使用 ViewModel。

### <a name="handling-sliders-in-the-code-behind-file"></a>處理程式碼後置檔案中的滑桿

**RGB 色彩滑桿**頁面會具現化`BoxView`以顯示色彩，三個`Slider`執行個體，以選取色彩，以及三個紅色、 綠色和藍色元件`Label`顯示這些色彩的項目值：

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

第一個區段集合`Text`屬性的其中一個`Label`簡短文字字串，表示值的執行個體`Slider`十六進位。 然後，這三`Slider`執行個體存取建立`Color`從 RGB 元件的值：

[![RGB 色彩滑桿](slider-images/RgbColorSliders.png "RGB 色彩滑桿")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>繫結至 ViewModel 的滑桿

**HSL 色彩滑桿**頁面會顯示如何執行用來建立計算使用 ViewModel`Color`色調、 飽和度和亮度值的值。 像所有的 Viewmodel`HSLColorViewModel`類別會實作`INotifyPropertyChanged`介面，而且會引發`PropertyChanged`事件其中一個屬性變更時：

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

Viewmodel 並`INotifyPropertyChanged`一文中討論介面[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

**HslColorSlidersPage.xaml**檔案會具現化`HslColorViewModel`並將它設定為頁面的`BindingContext`屬性。 這可讓繫結至在 ViewModel 屬性的 XAML 檔案中的所有項目：

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

作為`Slider`項目進行操作，`BoxView`和`Label`項目會從 ViewModel 更新：

[![HSL 色彩滑桿](slider-images/HslColorSliders.png "HSL 色彩滑桿")](slider-images/HslColorSliders-Large.png#lightbox)

`StringFormat`元件`Binding`標記延伸設定的 「 F2"格式顯示兩個小數位數。 (文章中有討論資料繫結中的格式字串[字串格式化](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md)。)不過，程式的 UWP 版本僅限於值 0、 0.1、 0.2...0.9 及 1.0。 這是直接的結果之實作的 UWP`Slider`於一節所述[平台實作差異](#implementations)。

## <a name="related-links"></a>相關連結

- [滑桿示範範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)
- [滑桿 API](xref:Xamarin.Forms.Slider)
