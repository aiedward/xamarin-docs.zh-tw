---
title: Xamarin. Forms 滑杆
description: '[Xamarin] 滑杆是一個水準橫條，可由使用者操作以從連續範圍選取雙精度值。 本文說明如何使用滑杆類別，從連續值的範圍中選取值。'
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
ms.openlocfilehash: ddb25a1f01f91b627fc0c370f7f21e2a797b72cb
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545578"
---
# <a name="xamarinforms-slider"></a>Xamarin. Forms 滑杆

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)

_使用滑杆來選取連續值的範圍。_

[Xamarin] [`Slider`](xref:Xamarin.Forms.Slider)是一個水準橫條，可由使用者操作以從連續範圍中選取 `double` 值。

`Slider` 會定義 `double`類型的三個屬性：

- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum)是範圍的最小值，預設值是0。
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum)是範圍的上限，預設值是1。
- [`Value`](xref:Xamarin.Forms.Slider.Value)是滑杆的值，其範圍可以介於 `Minimum` 和 `Maximum` 之間，而且預設值為0。

這三個屬性都是由 `BindableProperty` 物件所支援。 `Value` 屬性具有 `BindingMode.TwoWay`的預設系結模式，這表示它適合做為使用[ViewModel （MVVM）](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構之應用程式中的系結來源。

> [!WARNING]
> 就內部而言，`Slider` 可確保 `Minimum` 小於 `Maximum`。 如果 `Minimum` 或 `Maximum` 已設定為 `Minimum` 不小於 `Maximum`，則會引發例外狀況。 如需設定 `Minimum` 和 `Maximum` 屬性的詳細資訊，請參閱下面的[**預防措施**](#precautions)一節。

`Slider` 會將 `Value` 屬性強制轉型，使其介於 `Minimum` 和 `Maximum`（含）之間。 如果 `Minimum` 屬性設定為大於 `Value` 屬性的值，`Slider` 會將 `Value` 屬性設定為 [`Minimum`]。 同樣地，如果 `Maximum` 設定為小於 `Value`的值，`Slider` 會將 `Value` 屬性設定為 [`Maximum`]。

`Slider` 定義 `Value` 變更時所引發的[`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged)事件（不論是透過使用者操作 `Slider` 或當程式直接設定 `Value` 屬性時）。 當 `Value` 屬性強制轉型時，也會引發 `ValueChanged` 事件，如前一段所述。

伴隨 `ValueChanged` 事件的[`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs)物件有兩個屬性，兩種類型都 `double`： [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue)和[`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue)。 引發事件時，`NewValue` 的值與 `Slider` 物件的 `Value` 屬性相同。

`Slider` 也會定義在拖曳動作的開頭和結尾引發的 `DragStarted` 和 `DragCompleted` 事件。 不同于[`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged)事件，`DragStarted` 和 `DragCompleted` 事件只會透過 `Slider`的使用者操作來引發。 當 `DragStarted` 事件引發時，會執行類型 `ICommand`的 `DragStartedCommand`。 同樣地，當 `DragCompleted` 事件引發時，會執行類型 `ICommand`的 `DragCompletedCommand`。

> [!WARNING]
> 請勿將 `Center`、`Start`或 `End` 的未受限制水準配置選項用於 `Slider`。 在 Android 和 UWP 上，`Slider` 會折迭為長度為零的長條，而在 iOS 上，橫條會非常短。 保留 `Fill`的預設 `HorizontalOptions` 設定，而且在 `Grid` 版面配置中放置 `Slider` 時，請勿使用 `Auto` 的寬度。

`Slider` 也會定義數個會影響其外觀的屬性：

- [`MinimumTrackColor`](xref:Xamarin.Forms.Slider.MinimumTrackColorProperty)是捲動方塊左邊的橫條色彩。
- [`MaximumTrackColor`](xref:Xamarin.Forms.Slider.MaximumTrackColorProperty)是捲動方塊右側的橫條色彩。
- [`ThumbColor`](xref:Xamarin.Forms.Slider.ThumbColorProperty)是捲動方塊色彩。
- [`ThumbImageSource`](xref:Xamarin.Forms.Slider.ThumbImageSourceProperty)是要用於 thumb 的影像，類型[`ImageSource`](xref:Xamarin.Forms.ImageSource)。

> [!NOTE]
> `ThumbColor` 和 `ThumbImageSource` 屬性互斥。 如果同時設定這兩個屬性，`ThumbImageSource` 屬性的優先順序會較高。

## <a name="basic-slider-code-and-markup"></a>基本滑杆程式碼和標記

[**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)範例一開始會有三個功能相同的頁面，但會以不同的方式來執行。 第一個頁面只C#會使用程式碼，第二個會使用 xaml 搭配程式碼中的事件處理常式，而第三個則能夠使用 xaml 檔案中的資料系結來避免事件處理常式。

### <a name="creating-a-slider-in-code"></a>在程式碼中建立滑杆

[**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)範例中的**基本滑杆代碼**頁會顯示如何在程式碼中建立 `Slider` 和兩個 `Label` 物件：

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

`Slider` 已初始化為具有360的 `Maximum` 屬性。 `Slider` 的 `ValueChanged` 處理常式會使用 `slider` 物件的 `Value` 屬性來設定第一個 `Rotation` 的 `Label` 屬性，並使用 `String.Format` 方法與事件引數的 `NewValue` 屬性來設定第二個 `Text` 的 `Label`屬性。 這兩種取得 `Slider` 目前值的方法是可互換的。

以下是在 iOS 和 Android 裝置上執行的程式：

[![基本滑杆程式碼](slider-images/BasicSliderCode.png "基本滑杆程式碼")](slider-images/BasicSliderCode-Large.png#lightbox)

第二個 `Label` 會顯示 "（未初始化）" 文字，直到 `Slider` 操作為止，這會導致第一個 `ValueChanged` 事件引發。 請注意，每個平臺所顯示的小數位數是不同的。 這些差異與 `Slider` 的平臺執行相關，稍後將在本文章中的「平臺」的[差異](#implementations)一節中討論。

### <a name="creating-a-slider-in-xaml"></a>在 XAML 中建立滑杆

**基本滑杆 XAML**頁面的功能與**基本滑杆程式碼**相同，但大部分都是在 XAML 中執行：

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

程式碼後置檔案包含 `ValueChanged` 事件的處理常式：

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

事件處理常式也可以透過 `sender` 引數取得引發事件的 `Slider`。 `Value` 屬性包含目前的值：

```csharp
double value = ((Slider)sender).Value;
```

如果在具有 `x:Name` 屬性的 XAML 檔案中指定 `Slider` 物件的名稱（例如，"slider"），則事件處理常式可以直接參考該物件：

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>資料系結滑杆

[**基本滑杆**系結] 頁面會顯示如何撰寫幾乎相等的程式，藉由使用[資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結來去除 `Value` 事件處理常式：

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

第一個 `Label` 的 `Rotation` 屬性會系結至 `Slider`的 `Value` 屬性，這是第二個 `Text` 中具有 `Label` 規格的 `StringFormat` 屬性。 **基本滑杆**系結頁面的運作方式與前兩頁不同：當頁面第一次出現時，第二個 `Label` 會顯示含有值的文字字串。 這是使用資料系結的優點。 若要顯示沒有資料系結的文字，您必須特別初始化 `Label` 的 `Text` 屬性，或從類別的函式呼叫事件處理常式來模擬 `ValueChanged` 事件的引發。

<a name="precautions" />

## <a name="precautions"></a>措施

`Minimum` 屬性的值必須一律小於 `Maximum` 屬性的值。 下列程式碼片段會導致 `Slider` 引發例外狀況：

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

C#編譯器會產生依序設定這兩個屬性的程式碼，當 `Minimum` 屬性設定為10時，它會大於預設的 `Maximum` 值1。 在此情況下，您可以先設定 `Maximum` 屬性來避免例外狀況：

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

將 `Maximum` 設定為20不是問題，因為它大於預設 `Minimum` 值0。 設定 `Minimum` 時，此值會小於 `Maximum` 值20。

XAML 中存在相同的問題。 以可確保 `Maximum` 一律大於 `Minimum`的順序設定屬性：

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

您可以將 `Minimum` 和 `Maximum` 值設定為負數，但只能依照 `Minimum` 一律小於 `Maximum`的順序：

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

`Value` 屬性一律大於或等於 `Minimum` 值，且小於或等於 `Maximum`。 如果 `Value` 設定為超出該範圍的值，則會將值強制轉型為範圍內的，但不會引發任何例外狀況。 例如，此程式碼*不*會引發例外狀況：

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

相反地，`Value` 屬性會強制轉型為 `Maximum` 值1。

以下是如上所示的程式碼片段：

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

當 `Minimum` 設定為10時，`Value` 也會設定為10。

如果 `ValueChanged` 事件處理常式在 `Value` 屬性強制轉型為預設值0以外的位置時附加，則會引發 `ValueChanged` 事件。 以下是 XAML 的程式碼片段：

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

當 `Minimum` 設定為10時，`Value` 也會設定為10，而 `ValueChanged` 事件則會引發。 這可能會在已建立其餘頁面之前發生，而且處理常式可能會嘗試參考尚未建立之頁面上的其他元素。 您可能想要將一些程式碼新增至 `ValueChanged` 處理常式，以檢查頁面上其他元素的 `null` 值。 或者，您可以在初始化 `Slider` 值之後，設定 `ValueChanged` 事件處理常式。

<a name="implementations" />

## <a name="platform-implementation-differences"></a>平臺執行差異

稍早所示的螢幕擷取畫面會以不同的小數點數目顯示 `Slider` 的值。 這與在 Android 和 UWP 平臺上執行 `Slider` 的方式有關。

### <a name="the-android-implementation"></a>Android 的執行

Android 的 `Slider` 執行是以 Android [`SeekBar`](xref:Android.Widget.SeekBar)為基礎，而且一律會將[`Max`](xref:Android.Widget.ProgressBar.Max)屬性設為1000。 這表示 Android 上的 `Slider` 只有1001的離散值。 如果您將 `Slider` 的 `Minimum` 設定為0，而 `Maximum` 為5000，則在操作 `Slider` 時，`Value` 屬性的值為0、5、10、15等等。

### <a name="the-uwp-implementation"></a>UWP 的執行

UWP 的 `Slider` 的執行是以 UWP [`Slider`](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.slider)控制項為基礎。 UWP `Slider` 的 `StepFrequency` 屬性會設定為 `Maximum` 的差異和 `Minimum` 屬性除以10，但不大於1。

例如，如果預設範圍為0到1，`StepFrequency` 屬性會設定為0.1。 當 `Slider` 操作時，`Value` 屬性限制為0、0.1、0.2、0.3、0.4、0.5、0.6、0.7、0.8、0.9 和1.0。 （這在[**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)範例的最後一頁中很明顯）。當 `Maximum` 和 `Minimum` 屬性之間的差異為10或以上時，`StepFrequency` 會設定為1，而 `Value` 屬性則具有整數值。

### <a name="the-stepslider-solution"></a>StepSlider 解決方案

第27章會討論更多用途的 `StepSlider` [。](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)*使用 Xamarin 建立 Mobile Apps*之書籍的自訂轉譯器。 `StepSlider` 類似 `Slider` 但會加入 `Steps` 屬性，以指定 `Minimum` 和 `Maximum`之間的值數目。

## <a name="sliders-for-color-selection"></a>色彩選取的滑杆

[**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)範例中的最後兩個頁面都使用三個 `Slider` 實例來進行色彩選取。 第一個頁面會處理常式代碼後置檔案中的所有互動，而第二頁則顯示如何搭配 ViewModel 使用資料系結。

### <a name="handling-sliders-in-the-code-behind-file"></a>處理常式代碼後置檔案中的滑杆

[ **RGB 色彩滑杆**] 頁面會具現化 `BoxView` 以顯示色彩、三個 `Slider` 實例，以選取色彩的紅色、綠色和藍色元件，以及三個用於顯示這些色彩值的 `Label` 元素：

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

`Style` 提供所有三個 `Slider` 的元素，範圍介於0到255之間。 `Slider` 的元素會共用相同的 `ValueChanged` 處理常式，並在程式碼後置檔案中執行：

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

第一個區段會將其中一個 `Label` 實例的 `Text` 屬性設定為簡短的文字字串，指出十六進位中 `Slider` 的值。 然後，系統會存取這三個 `Slider` 實例，以從 RGB 元件建立 `Color` 值：

[![RGB 色彩滑杆](slider-images/RgbColorSliders.png "RGB 色彩滑杆")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>將滑杆系結至 ViewModel

[ **HSL 色彩滑杆**] 頁面會顯示如何使用 ViewModel 來執行用來從色調、飽和度和亮度值建立 `Color` 值的計算。 就像所有的 Viewmodel，`HSLColorViewModel` 類別會實 `INotifyPropertyChanged` 介面，而且每當其中一個屬性變更時，就會引發 `PropertyChanged` 事件：

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

Viewmodel 和 `INotifyPropertyChanged` 介面會在[資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結一文中討論。

**HslColorSlidersPage**會具現化 `HslColorViewModel`，並將它設定為頁面的 `BindingContext` 屬性。 這可讓 XAML 檔案中的所有元素系結至 ViewModel 中的屬性：

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

在操作 `Slider` 元素時，`BoxView` 和 `Label` 元素會從 ViewModel 更新：

[![HSL 色彩滑杆](slider-images/HslColorSliders.png "HSL 色彩滑杆")](slider-images/HslColorSliders-Large.png#lightbox)

`Binding` 標記延伸的 `StringFormat` 元件會設定為 "F2" 格式，以顯示兩個小數位數。 （資料系結中的字串格式會在[字串格式](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md)一文中討論）。不過，程式的 UWP 版本限制為0、0.1、0.2、... 的值0.9 和1.0。 這是執行 UWP `Slider` 的直接結果，如上述「[平臺執行差異](#implementations)」一節中所述。

## <a name="related-links"></a>相關連結

- [滑杆示範範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)
- [滑杆 API](xref:Xamarin.Forms.Slider)
