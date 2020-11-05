---
title: Xamarin.Forms 滑 塊
description: Xamarin.Forms滑杆是水準橫條，可由使用者操作，以從連續範圍選取雙精度值。 本文說明如何使用滑杆類別，從連續值的範圍中選取一個值。
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 364cda6372986113e8a782a061783e0ca5455f3b
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368540"
---
# <a name="no-locxamarinforms-slider"></a>Xamarin.Forms 滑 塊

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)

_使用滑杆從連續值的範圍中進行選取。_

Xamarin.Forms [`Slider`](xref:Xamarin.Forms.Slider) 是可由使用者操作以 `double` 從連續範圍選取值的水準橫條。

會 `Slider` 定義類型的三個屬性 `double` ：

- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) 這是範圍的最小值，預設值是0。
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) 這是範圍的最大值，預設值是1。
- [`Value`](xref:Xamarin.Forms.Slider.Value) 是滑杆的值，其範圍可以介於和之間， `Minimum` `Maximum` 且預設值為0。

所有三個屬性都是由 `BindableProperty` 物件支援。 `Value`屬性具有的預設系結模式 `BindingMode.TwoWay` ，這表示它適合作為應用程式中的系結來源，此應用程式會使用[模型視圖 ViewModel (MVVM) ](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構。

> [!WARNING]
> 在內部， `Slider` 可確保 `Minimum` 小於 `Maximum` 。 如果 `Minimum` 設定或的 `Maximum` 設定 `Minimum` 不小於，則會 `Maximum` 引發例外狀況。 如需有關設定和屬性的詳細資訊，請參閱下列 [**預防措施**](#precautions) 一節 `Minimum` `Maximum` 。

`Slider` `Value` 會強制屬性，使其介於 `Minimum` 和 `Maximum` （含）之間。 如果 `Minimum` 屬性設定為大於屬性的值 `Value` ，則會 `Slider` 將 `Value` 屬性設定為 `Minimum` 。 同樣地，如果 `Maximum` 設定為小於的值 `Value` ，則會 `Slider` 將 `Value` 屬性設定為 `Maximum` 。

`Slider` 定義在 [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) 變更時所引發的事件 `Value` ，不論是透過使用者操作， `Slider` 或是當程式直接設定屬性時所引發的事件 `Value` 。 `ValueChanged`當 `Value` 屬性如前一段落所述強制轉型時，也會引發事件。

[`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs)事件隨附的物件 `ValueChanged` 有兩個屬性，兩者都是類型 `double` ： [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) 和 [`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue) 。 引發事件時，的值 `NewValue` 會與 `Value` 物件的屬性相同 `Slider` 。

`Slider` 也 `DragStarted` 會定義和 `DragCompleted` 事件，這些事件會在拖曳動作的開頭和結尾引發。 與事件不同的 [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) `DragStarted` 是，和 `DragCompleted` 事件只會透過使用者操作來引發 `Slider` 。 當 `DragStarted` 事件引發時， `DragStartedCommand` 會執行型別為的 `ICommand` 。 同樣地，當 `DragCompleted` 事件引發時， `DragCompletedCommand` 會執行型別為的 `ICommand` 。

> [!WARNING]
> 請勿使用、或的不受限制的水準配置選項 `Center` `Start` `End` `Slider` 。 在 Android 和 UWP 上，會折迭 `Slider` 至長度為零的橫條，而在 iOS 上，橫條很短。 保留的預設 `HorizontalOptions` 設定 `Fill` ，並在放置配置時不要使用的寬度 `Auto` `Slider` `Grid` 。

`Slider`也會定義影響其外觀的數個屬性：

- [`MinimumTrackColor`](xref:Xamarin.Forms.Slider.MinimumTrackColorProperty) 是捲動方塊左邊的橫條色彩。
- [`MaximumTrackColor`](xref:Xamarin.Forms.Slider.MaximumTrackColorProperty) 是捲動方塊右側的橫條色彩。
- [`ThumbColor`](xref:Xamarin.Forms.Slider.ThumbColorProperty) 是 thumb 色彩。
- [`ThumbImageSource`](xref:Xamarin.Forms.Slider.ThumbImageSourceProperty) 這是要用於捲動方塊的影像，其類型為 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 。

> [!NOTE]
> `ThumbColor`和 `ThumbImageSource` 屬性是互斥的。 如果同時設定這兩個屬性， `ThumbImageSource` 就會優先使用屬性。

## <a name="basic-slider-code-and-markup"></a>基本滑杆程式碼和標記

[**SliderDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)範例一開始會有三個功能相同，但以不同方式執行的頁面。 第一頁只使用 c # 程式碼，第二個頁面會在程式碼中使用 XAML 搭配事件處理常式，而第三個頁面則可以在 XAML 檔案中使用資料系結來避免事件處理常式。

### <a name="creating-a-slider-in-code"></a>在程式碼中建立滑杆

[**SliderDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)範例中的 **基本滑杆程式碼** 頁面會顯示如何在程式 `Slider` 代碼中建立和兩個 `Label` 物件：

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

`Slider`已初始化為具有360的 `Maximum` 屬性。 的 `ValueChanged` 處理常式會 `Slider` 使用 `Value` 物件的屬性 `slider` 來設定 `Rotation` 第一個的屬性 `Label` ，並使用 `String.Format` 方法與 `NewValue` 事件引數的屬性來設定 `Text` 第二個 `Label` 的屬性。 這兩種取得目前值的方法 `Slider` 都是可互換的。

以下是在 iOS 和 Android 裝置上執行的程式：

[![基本滑杆程式碼](slider-images/BasicSliderCode.png "基本滑杆程式碼")](slider-images/BasicSliderCode-Large.png#lightbox)

第二個會在 `Label` 操作之前顯示「 (未初始化的) 」文字 `Slider` ，這會導致引發第一個 `ValueChanged` 事件。 請注意，每個平臺所顯示的小數位數各有不同。 這些差異與的平臺執行有關，稍後將在本文 `Slider` 的「 [平臺實施差異](#platform-implementation-differences)」一節中討論。

### <a name="creating-a-slider-in-xaml"></a>在 XAML 中建立滑杆

**基本滑杆 XAML** 頁面的功能與 **基本滑杆程式碼** 相同，但實際上是在 XAML 中執行：

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

程式碼後端檔案包含事件的處理常式 `ValueChanged` ：

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

事件處理常式也可以 `Slider` 透過引數取得正在引發事件的 `sender` 。 `Value`屬性包含目前的值：

```csharp
double value = ((Slider)sender).Value;
```

如果 `Slider` 物件在 XAML 檔案中指定了屬性 (例如「滑杆」 `x:Name` ) ，則事件處理常式可以直接參考該物件：

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>資料系結滑杆

[ **基本滑杆** 系結] 頁面會顯示如何使用資料系結來撰寫幾乎相等的程式，以消除 `Value` 事件處理常式： [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)

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

`Rotation`第一個屬性（property `Label` ）系結至的 `Value` 屬性（property），其為 `Slider` `Text` 第二個 `Label` 具有規格的屬性（property） `StringFormat` 。 **基本滑杆** 系結頁面的運作方式與前兩個頁面的運作方式稍有不同：當頁面第一次出現時，第二個頁面會 `Label` 顯示具有值的文字字串。 這是使用資料系結的優點。 若要顯示沒有資料系結的文字，您必須明確地初始化的 `Text` 屬性， `Label` 或從類別的函式 `ValueChanged` 呼叫事件處理常式，以模擬事件的引發。

## <a name="precautions"></a>預防 措施

屬性的值 `Minimum` 必須一律小於屬性的值 `Maximum` 。 下列程式碼片段會導致 `Slider` 引發例外狀況：

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

C # 編譯器會產生依序設定這兩個屬性的程式碼，當 `Minimum` 屬性設定為10時，它會大於預設 `Maximum` 值1。 在此情況下，您可以先設定屬性，以避免發生例外狀況 `Maximum` ：

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

設定 `Maximum` 為20不是問題，因為它大於預設 `Minimum` 值0。 當 `Minimum` 設定時，值小於 `Maximum` 20 的值。

XAML 中存在相同的問題。 以確保永遠大於下列順序的順序設定屬性 `Maximum` `Minimum` ：

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

您可以將 `Minimum` 和 `Maximum` 值設定為負數，但只有順序中的 `Minimum` 永遠小於 `Maximum` ：

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

`Value`屬性一律大於或等於 `Minimum` 值，且小於或等於 `Maximum` 。 如果 `Value` 設定為範圍以外的值，則會將值強制轉型為範圍內的，但不會引發任何例外狀況。 例如，此程式碼 *不* 會引發例外狀況：

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

相反地， `Value` 屬性會強制轉型 `Maximum` 為1的值。

以下是上面所示的程式碼片段：

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

當 `Minimum` 設定為10時， `Value` 也會設定為10。

如果 `ValueChanged` 事件處理常式已在 `Value` 屬性強制轉型為預設值0以外的值時附加，則 `ValueChanged` 會引發事件。 以下是 XAML 的程式碼片段：

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

當 `Minimum` 設為10時， `Value` 也會設定為10，而且 `ValueChanged` 會引發事件。 這可能會在已建立頁面的其餘部分之前發生，而且處理常式可能會嘗試參考頁面上尚未建立的其他元素。 您可能會想要在 `ValueChanged` 處理常式中加入一些程式碼，以檢查 `null` 頁面上其他元素的值。 或者，您可以在 `ValueChanged` 值初始化之後設定事件處理常式 `Slider` 。

## <a name="platform-implementation-differences"></a>平臺實施差異

稍早所示的螢幕擷取畫面會顯示 `Slider` 具有不同小數位數的值。 這與在 `Slider` Android 和 UWP 平臺上的執行方式有關。

### <a name="the-android-implementation"></a>Android 執行

的 Android 執行 `Slider` 是以 Android 為基礎 [`SeekBar`](xref:Android.Widget.SeekBar) ，而且一律會將 [`Max`](xref:Android.Widget.ProgressBar.Max) 屬性設定為1000。 這表示 `Slider` Android 上的只有1001離散值。 如果您將設定為，並將設定為0，並將設定為 `Slider` `Minimum` `Maximum` 5000，則在 `Slider` 操作時， `Value` 屬性的值為0、5、10、15等等。

### <a name="the-uwp-implementation"></a>UWP 的執行

UWP 的執行 `Slider` 是以 uwp 控制項為基礎 [`Slider`](/uwp/api/windows.ui.xaml.controls.slider) 。 `StepFrequency`UWP 的屬性 `Slider` 會設定為 `Maximum` 和屬性的差異 `Minimum` 除以10，但不大於1。

例如，針對預設範圍0到1， `StepFrequency` 屬性會設定為0.1。 當 `Slider` 操作時， `Value` 屬性會限制為0、0.1、0.2、0.3、0.4、0.5、0.6、0.7、0.8、0.9 和1.0。  (這在 [**SliderDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) 範例中的最後一頁很明顯。 ) 當和屬性之間的 `Maximum` 差異 `Minimum` 為10或更大時， `StepFrequency` 就會設定為1，且 `Value` 屬性具有整數值。

### <a name="the-stepslider-solution"></a>StepSlider 解決方案

第 `StepSlider` 27 章會討論更多用途 [。](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)*使用 Xamarin.Forms 建立 Mobile Apps* 之書籍的自訂轉譯器。 `StepSlider`類似于， `Slider` 但會加入 `Steps` 屬性來指定和之間的值數目 `Minimum` `Maximum` 。

## <a name="sliders-for-color-selection"></a>色彩選取的滑杆

[**SliderDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)範例中的最後兩個頁面都會使用三個 `Slider` 實例來選取色彩。 第一個頁面會處理常式代碼後端檔案中的所有互動，而第二個頁面則會顯示如何使用 ViewModel 的資料系結。

### <a name="handling-sliders-in-the-code-behind-file"></a>處理常式代碼後端檔案中的滑杆

**RGB 色彩滑杆** 頁面會具現化， `BoxView` 以顯示色彩、三個 `Slider` 實例來選取色彩的紅色、綠色和藍色元件，以及三個可 `Label` 顯示這些色彩值的元素：

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

A 為 `Style` 所有三個 `Slider` 元素提供0到255的範圍。 專案 `Slider` 會共用相同的 `ValueChanged` 處理常式，此處理程式會在程式碼後端檔案中執行：

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

第一個區段會將 `Text` 其中一個實例的屬性設定 `Label` 為簡短文字字串，指出中的值為 `Slider` 十六進位。 然後， `Slider` 系統會存取所有三個實例，以 `Color` 從 RGB 元件建立值：

[![RGB 色彩滑杆](slider-images/RgbColorSliders.png "RGB 色彩滑杆")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>將滑杆系結至 ViewModel

[ **HSL 色彩滑杆** ] 頁面會顯示如何使用 ViewModel 來執行用來 `Color` 從色調、飽和度和亮度值建立值的計算。 如同所有 Viewmodel，此 `HSLColorViewModel` 類別會實作為 `INotifyPropertyChanged` 介面，並且 `PropertyChanged` 每當其中一個屬性變更時，就會引發事件：

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

Viewmodel 和 `INotifyPropertyChanged` 介面會在「 [資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結」一文中討論。

**HslColorSlidersPage** 會具現化， `HslColorViewModel` 並將它設定為頁面的 `BindingContext` 屬性。 這可讓 XAML 檔案中的所有元素系結至 ViewModel 中的屬性：

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

當操作這些專案時 `Slider` ， `BoxView` 和 `Label` 元素會從 ViewModel 更新：

[![HSL 色彩滑杆](slider-images/HslColorSliders.png "HSL 色彩滑杆")](slider-images/HslColorSliders-Large.png#lightbox)

`StringFormat`標記延伸的元件 `Binding` 會設定為 "F2" 格式，以顯示兩個小數位數。 資料系結中的 (字串格式設定會在文章 [字串格式](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md)中討論 ) 。不過，此程式的 UWP 版本僅限於0、0.1、0.2、... 的值0.9 和1.0。 這是執行 UWP 的直接結果， `Slider` 如上面的「 [平臺實施差異](#platform-implementation-differences)」一節所述。

## <a name="related-links"></a>相關連結

- [滑杆示範範例](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)
- [滑杆 API](xref:Xamarin.Forms.Slider)