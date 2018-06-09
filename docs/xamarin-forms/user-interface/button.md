---
title: Xamarin.Forms 按鈕
description: 按鈕會回應點選或按一下，以引導應用程式來執行特定工作。
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/01/2018
ms.openlocfilehash: d74e0b2aa5be6e8eee2ce5cb54572dd4113d4d7d
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244912"
---
# <a name="xamarinforms-button"></a>Xamarin.Forms 按鈕

_按鈕會回應點選或按一下，以引導應用程式來執行特定工作。_

[ `Button` ](xref:Xamarin.Forms.Button)是所有 Xamarin.Forms 中的最基本互動控制項。 `Button`通常會顯示簡短文字字串，表示命令，但它也可以顯示點陣圖影像，或文字的組合和影像。 使用者按下`Button`手指或初始化該命令的滑鼠按一下它。

大部分的主題，討論如下的對應中的頁面[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)範例。

## <a name="handling-button-clicks"></a>處理按鈕按一下

`Button` 定義[ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)在使用者點選時引發的事件`Button`用手指或滑鼠指標。 在事件引發時的介面從釋放手指或滑鼠按鈕`Button`。 `Button`必須要有其[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)屬性設定為`true`使其回應的點選。

**基本按鈕按一下**頁面[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)範例示範如何具現化`Button`XAML 和控制代碼中其`Clicked`事件。 **BasicButtonClickPage.xaml**檔案包含`StackLayout`兼具`Label`和`Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.BasicButtonClickPage"
             Title="Basic Button Click">
    <StackLayout>

        <Label x:Name="label"
               Text="Click the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Click to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

`Button`傾向於所佔用的允許為它的所有空間。 例如，如果您不需要設定`HorizontalOptions`屬性`Button`為以外的其他項目`Fill`、`Button`會佔用其父代的整個寬度。

根據預設，`Button`是矩形，但您可以使用來提供 it 圓角[ `CornerRadius` ](xref:Xamarin.Forms.Button.CornerRadius)屬性，如下所述的區段中[**按鈕外觀**](#button-appearance).

[ `Text` ](xref:Xamarin.Forms.Button.Text)屬性會指定在顯示的文字`Button`。 [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)事件設定為事件處理常式，名為`OnButtonClicked`。 在程式碼後置檔案中，位於這個處理常式**BasicButtonClickPage.xaml.cs**:

```csharp
public partial class BasicButtonClickPage : ContentPage
{
    public BasicButtonClickPage ()
    {
        InitializeComponent ();
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        await label.RelRotateTo(360, 1000);
    }
}
```

當`Button`點選，`OnButtonClicked`方法執行。 `sender`引數是`Button`負責此事件的物件。 您可以使用來存取`Button`物件，或若要在區別多個`Button`物件共用相同`Clicked`事件。

這個特定`Clicked`處理常式會呼叫旋轉動畫函式`Label`1000年毫秒中的 360 度。 以下是程式在 iOS 和 Android 裝置，或通用 Windows 平台 (UWP) 應用程式執行 Windows 10 desktop:

[![基本按鈕 Click](button-images/BasicButtonClick.png "基本按鈕 Click")](button-images/BasicButtonClick-Large.png#lightbox "按一下基本按鈕")

請注意，`OnButtonClicked`方法包含`async`修飾詞因為`await`在事件處理常式內使用。 A`Clicked`事件處理常式需要`async`修飾詞，只有當處理常式的主體使用`await`。

每個平台呈現`Button`它自己的特定方式。 在[**按鈕外觀**](#button-appearance)  區段中，您會看到如何設定色彩，並讓`Button`框線可見的更多自訂的外觀。 `Button` 實作[ `IFontElement` ](xref:Xamarin.Forms.Internals.IFontElement)介面，使其包含[ `FontFamily` ](xref:Xamarin.Forms.Button.FontFamily)， [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize)，和[ `FontAttributes` ](xref:Xamarin.Forms.Button.FontAttributes)屬性。

## <a name="creating-a-button-in-code"></a>在程式碼中建立的按鈕

通常會具現化`Button`在 XAML 中，但您也可以建立`Button`程式碼中。 這可能是方便您的應用程式需要建立多個按鈕使用可列舉的資料時`foreach`迴圈。

**程式碼按鈕 Click**頁面會示範如何建立一個網頁，其作用相當於**基本按鈕按一下**但完全以 C# 的頁面：

```csharp
public class CodeButtonClickPage : ContentPage
{
    public CodeButtonClickPage ()
    {
        Title = "Code Button Click";

        Label label = new Label
        {
            Text = "Click the Button below",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };

        Button button = new Button
        {
            Text = "Click to Rotate Text!",
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };
        button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);

        Content = new StackLayout
        {
            Children =
            {
                label,
                button
            }
        };
    }
}
```

所有項目會進行類別的建構函式。 因為`Clicked`處理常式只有一個陳述式，可以將它附加至事件非常簡單：

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

您也可以為不同的方法定義的事件處理常式的當然 (就像`OnButtonClick`方法中的**基本按鈕按一下**)，並將該方法附加到事件：

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>停用按鈕

有時候應用程式處於特定狀態的特定`Button`按一下不是有效的作業。 在這些情況下，`Button`應該設定來停用其`IsEnabled`屬性`false`。 典型的範例是`Entry`伴隨著開啟檔案的檔名的控制項`Button`:`Button`一些文字輸入時，才應該啟用`Entry`。
您可以使用`DataTrigger`這項工作，如中所示[**資料觸發程序**](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers)發行項。

## <a name="using-the-command-interface"></a>使用命令介面

若要回應的應用程式可能會`Button`不需要處理點選`Clicked`事件。 `Button`實作替代的通知機制，稱為_命令_或_指揮_介面。 這包含兩個屬性：

- [`Command`](xref:Xamarin.Forms.Button.Command) 型別的[ `ICommand` ](xref:System.Windows.Input.ICommand)，定義的介面[ `System.Windows.Input` ](xref:System.Windows.Input)命名空間。
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) 型別的屬性[ `Object` ](xref:System.Object)。

這個方法是特別適用於與資料繫結，以及特別是當實作模型-檢視-ViewModel (MVVM) 架構。 這些主題會討論文章[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)，[從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)，和[MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)。

MVVM 應用程式中，ViewModel 定義類型的屬性`ICommand`，然後連線到 XAML`Button`具有資料繫結項目。 Xamarin.Forms 也會定義[ `Command` ]((xref:Xamarin.Forms.Command`1))和[ `Command<T>` ](xref:Xamarin.Forms.Command`1)類別可實作`ICommand`介面，並協助 ViewModel 定義屬性的型別`ICommand`.

命令文件中的更詳細地說明[**命令介面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)但**基本的按鈕命令**頁面[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)範例示範基本的方法。

`CommandDemoViewModel`類別是定義類型的屬性，非常簡單 ViewModel`double`名為`Number`，和兩個屬性的型別`ICommand`名為`MultiplyBy2Command`和`DivideBy2Command`:

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    double number = 1;

    public event PropertyChangedEventHandler PropertyChanged;

    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(() => Number *= 2);

        DivideBy2Command = new Command(() => Number /= 2);
    }

    public double Number
    {
        set
        {
            if (number != value)
            {
                number = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Number"));
            }
        }
        get
        {
            return number;
        }
    }

    public ICommand MultiplyBy2Command { private set; get; }

    public ICommand DivideBy2Command { private set; get; }
}
```

這兩個`ICommand`屬性會初始化兩個類型的物件類別的建構函式中`Command`。 `Command`建構函式包含一些函數 (稱為`execute`建構函式引數)，會加倍，或分母`Number`屬性。

**BasicButtonCommand.xaml**檔案集其`BindingContext`的執行個體`CommandDemoViewModel`。 `Label`元素和兩個`Button`項目包含在三個屬性的繫結`CommandDemoViewModel`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.BasicButtonCommandPage"
             Title="Basic Button Command">

    <ContentPage.BindingContext>
        <local:CommandDemoViewModel />
    </ContentPage.BindingContext>

    <StackLayout>
        <Label Text="{Binding Number, StringFormat='Value is now {0}'}"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Multiply by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding MultiplyBy2Command}" />

        <Button Text="Divide by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding DivideBy2Command}" />
    </StackLayout>
</ContentPage>
```

與兩個`Button`所點選的項目，會執行的命令，並變更值的數目：

[![基本按鈕命令](button-images/BasicButtonCommand.png "基本按鈕命令")](button-images/BasicButtonCommand-Large.png#lightbox)

透過這種方法的優點`Clicked`處理常式會確認牽涉到的功能，此頁面的所有邏輯都位於 ViewModel，而不是程式碼後置檔案，達到更好的分隔的使用者介面與商務邏輯。

也可能會`Command`物件來啟用及停用控制`Button`項目。 例如，假設您想要限制 2 之間的數字值的範圍<sup>10</sup>和 2<sup>&ndash;10</sup>。 您可以加入另一個函式的建構函式 (稱為`canExecute`引數)，傳回`true`如果`Button`應該啟用。 以下是要修改`CommandDemoViewModel`建構函式：

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    ···
    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(
            execute: () =>
            {
                Number *= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number < Math.Pow(2, 10));

        DivideBy2Command = new Command(
            execute: () =>
            {
                Number /= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number > Math.Pow(2, -10));
    }
    ···
}
```

呼叫`ChangeCanExecute`方法`Command`所需以便`Command`方法可以呼叫`canExecute`方法，並判斷是否`Button`或不應該停用。 此程式碼變更，數目達到限制，`Button`已停用：

[![基本按鈕命令-修改](button-images/BasicButtonCommandModified.png "基本按鈕命令-修改")](button-images/BasicButtonCommandModified-Large.png#lightbox)

它是兩個或多個可能`Button`繫結至相同的項目`ICommand`屬性。 `Button`項目可以區別使用[ `CommandParameter` ](xref:Xamarin.Forms.Button.CommandParameter)屬性`Button`。 在此情況下，您會想要使用泛型[ `Command<T>` ](xref:Xamarin.Forms.Command`1)類別。 `CommandParameter`物件隨後會傳遞做為引數`execute`和`canExecute`方法。 這項技術會顯示在詳細[**基本指揮**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)區段[**命令介面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)發行項。

[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)範例還會使用這項技術，在其`MainPage`類別。 **MainPage.xaml**檔案包含`Button`範例的每一頁：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.MainPage"
             Title="Button Demos">
    <ScrollView>
        <FlexLayout Direction="Column"
                    JustifyContent="SpaceEvenly"
                    AlignItems="Center">

            <Button Text="Basic Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonClickPage}" />

            <Button Text="Code Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:CodeButtonClickPage}" />

            <Button Text="Basic Button Command"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonCommandPage}" />

            <Button Text="Press and Release Button"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:PressAndReleaseButtonPage}" />

            <Button Text="Button Appearance"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ButtonAppearancePage}" />

            <Button Text="Toggle Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ToggleButtonDemoPage}" />

            <Button Text="Image Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ImageButtonDemoPage}" />

        </FlexLayout>
    </ScrollView>
</ContentPage>
```

每個`Button`具有其`Command`屬性繫結至屬性，名為`NavigateCommand`，和`CommandParameter`設[ `Type` ](xref:System.Type)對應到專案中的頁面類別的其中一個物件。

確認`NavigateCommand`屬性屬於型別`ICommand`和程式碼後置檔案中定義：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

建構函式初始化`NavigateCommand`屬性`Command<Type>`物件，因為`Type`的型別`CommandParameter`XAML 檔案中設定的物件。 這表示`execute`方法具有類型的引數`Type`，對應至這個`CommandParameter`物件。 函式具現化 頁面，然後瀏覽至它。

請注意，建構函式結束時，會設定其`BindingContext`本身。 這是必要的繫結至 XAML 檔案中的屬性`NavigateCommand`屬性。

## <a name="pressing-and-releasing-the-button"></a>按下並放開按鈕

除了`Clicked`事件，`Button`也會定義[ `Pressed` ](xref:Xamarin.Forms.Button.Pressed)和[ `Released` ](xref:Xamarin.Forms.Button.Released)事件。 `Pressed`手指按時，就會發生事件`Button`，或按下滑鼠指標位於`Button`。 `Released`發行手指或滑鼠按鈕時，就會發生事件。 一般而言，`Clicked`也在相同的時間，以引發事件時`Released`事件，但如果手指或滑鼠指標的投影片的表面遠離`Button`之前發行，`Clicked`事件可能會發生。

`Pressed`和`Released`事件通常沒有用，但它們可以用於特殊用途，如下所示**按下並釋放按鈕**頁面。 XAML 檔案包含`Label`和`Button`附加的處理常式與`Pressed`和`Released`事件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.PressAndReleaseButtonPage"
             Title="Press and Release Button">
    <StackLayout>

        <Label x:Name="label"
               Text="Press and hold the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Press to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Pressed="OnButtonPressed"
                Released="OnButtonReleased" />

    </StackLayout>
</ContentPage>
```

程式碼後置檔案以動畫方式顯示`Label`時`Pressed`事件發生，但會暫停旋轉時`Released`就會發生事件：

```csharp
public partial class PressAndReleaseButtonPage : ContentPage
{
    bool animationInProgress = false;
    Stopwatch stopwatch = new Stopwatch();

    public PressAndReleaseButtonPage ()
    {
        InitializeComponent ();
    }

    void OnButtonPressed(object sender, EventArgs args)
    {
        stopwatch.Start();
        animationInProgress = true;

        Device.StartTimer(TimeSpan.FromMilliseconds(16), () =>
        {
            label.Rotation = 360 * (stopwatch.Elapsed.TotalSeconds % 1);

            return animationInProgress;
        });
    }

    void OnButtonReleased(object sender, EventArgs args)
    {
        animationInProgress = false;
        stopwatch.Stop();
    }
}
```

結果是`Label`只滾動時發生手指接觸`Button`，並停止手指發行時：

[![按下並且放開按鈕](button-images/PressAndReleaseButton.png "按下並且放開按鈕")](button-images/PressAndReleaseButton-Large.png)

這種行為有遊戲的應用程式： 手指上保留`Button`可能會使特定方向移動於螢幕物件。

<a name="button-appearance" />

## <a name="button-appearance"></a>按鈕的外觀

`Button`繼承或定義數個會影響其外觀的內容：

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) 色彩的`Button`文字
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 該文字的背景色彩
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) 為區域周圍的色彩 `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) 字型系列用於文字嗎
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) 文字的大小
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) 指出文字是否為斜體或粗體
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) 框線的寬度
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) 重試回合邊角

六個這些屬性的效果 (不包括`FontFamily`和`FontAttributes`) 中會示範**按鈕外觀**頁面。 另一個屬性[ `Image` ](xref:Xamarin.Forms.Button.Image)，一節所述[**使用的點陣圖按鈕使用**](#image-button)。

中的檢視和資料繫結的所有**按鈕外觀**XAML 檔案中所定義的頁面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ButtonAppearancePage"
             Title="Button Appearance">
    <StackLayout>
        <Button x:Name="button"
                Text="Button"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                TextColor="{Binding Source={x:Reference textColorPicker},
                                    Path=SelectedItem.Color}"
                BackgroundColor="{Binding Source={x:Reference backgroundColorPicker},
                                          Path=SelectedItem.Color}"
                BorderColor="{Binding Source={x:Reference borderColorPicker},
                                      Path=SelectedItem.Color}" />

        <StackLayout BindingContext="{x:Reference button}"
                     Padding="10">

            <Slider x:Name="fontSizeSlider"
                    Maximum="48"
                    Minimum="1"
                    Value="{Binding FontSize}" />

            <Label Text="{Binding Source={x:Reference fontSizeSlider},
                                  Path=Value,
                                  StringFormat='FontSize = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="borderWidthSlider"
                    Minimum="-1"
                    Maximum="12"
                    Value="{Binding BorderWidth}" />

            <Label Text="{Binding Source={x:Reference borderWidthSlider},
                                  Path=Value,
                                  StringFormat='BorderWidth = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="cornerRadiusSlider"
                    Minimum="-1"
                    Maximum="24"
                    Value="{Binding CornerRadius}" />

            <Label Text="{Binding Source={x:Reference cornerRadiusSlider},
                                  Path=Value,
                                  StringFormat='CornerRadius = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Grid.Resources>
                    <Style TargetType="Label">
                        <Setter Property="VerticalOptions" Value="Center" />
                    </Style>
                </Grid.Resources>

                <Label Text="Text Color:"
                       Grid.Row="0" Grid.Column="0" />

                <Picker x:Name="textColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="0" Grid.Column="1" />

                <Label Text="Background Color:"
                       Grid.Row="1" Grid.Column="0" />

                <Picker x:Name="backgroundColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="1" Grid.Column="1" />

                <Label Text="Border Color:"
                       Grid.Row="2" Grid.Column="0" />

                <Picker x:Name="borderColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="2" Grid.Column="1" />
            </Grid>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

`Button`頁面的頂端有其三個`Color`屬性繫結至`Picker`位於頁面底部的項目。 中的項目`Picker`項目是從色彩`NamedColor`專案中包含的類別。 三個`Slider`項目包含雙向繫結`FontSize`， `BorderWidth`，和`CornerRadius`屬性`Button`。

此程式可讓您試驗所有這些屬性的組合：

[![按鈕外觀](button-images/ButtonAppearance.png "按鈕外觀")](button-images/ButtonAppearance-Large.png)

若要查看`Button`框線，您必須設定`BorderColor`為以外的其他項目`Default`，而`BorderWidth`設為正值。

在 iOS 上，您會注意到大型的框線寬度擅自的內部`Button`，且會影響顯示的文字。 如果您選擇使用框線與 iOS `Button`，您可能會想要開始與結束`Text`且空間，以保留其可見性屬性。

在 UWP 中，選取`CornerRadius`超過高度的一半`Button`引發例外狀況。

## <a name="creating-a-toggle-button"></a>建立切換按鈕

子類別可以`Button`使其運作方式類似-關閉交換器： 點選一次 按鈕來切換按鈕上，點選一次以關閉它。

下列`ToggleButton`類別衍生自`Button`並定義新的事件，名為`Toggled`和名為的布林值屬性`IsToggled`。 這些都是相同的兩個屬性，定義透過 Xamarin.Forms [ `Switch` ](xref:Xamarin.Forms.Switch):

```csharp
class ToggleButton : Button
{
    public event EventHandler<ToggledEventArgs> Toggled;

    public static BindableProperty IsToggledProperty =
        BindableProperty.Create("IsToggled", typeof(bool), typeof(ToggleButton), false,
                                propertyChanged: OnIsToggledChanged);

    public ToggleButton()
    {
        Clicked += (sender, args) => IsToggled ^= true;
    }

    public bool IsToggled
    {
        set { SetValue(IsToggledProperty, value); }
        get { return (bool)GetValue(IsToggledProperty); }
    }

    protected override void OnParentSet()
    {
        base.OnParentSet();
        VisualStateManager.GoToState(this, "ToggledOff");
    }

    static void OnIsToggledChanged(BindableObject bindable, object oldValue, object newValue)
    {
        ToggleButton toggleButton = (ToggleButton)bindable;
        bool isToggled = (bool)newValue;

        // Fire event
        toggleButton.Toggled?.Invoke(toggleButton, new ToggledEventArgs(isToggled));

        // Set the visual state
        VisualStateManager.GoToState(toggleButton, isToggled ? "ToggledOn" : "ToggledOff");
    }
}
```

`ToggleButton`建構函式附加至處理常式`Clicked`事件，讓它可以變更的值`IsToggled`屬性。 `OnIsToggledChanged`方法引發`Toggled`事件。

最後一行`OnIsToggledChanged`方法會呼叫靜態`VisualStateManager.GoToState`方法具有兩個文字字串"ToggledOn"和"ToggledOff"。 您可以讀取此方法，及您的應用程式將如何回應文件中的視覺狀態相關[ **Xamarin.Forms Visual State Manager**](~/xamarin-forms/user-interface/visual-state-manager.md)。

因為`ToggleButton`呼叫`VisualStateManager.GoToState`，此類別本身不需要包含任何額外的功能，若要變更按鈕的外觀，根據其`IsToggled`狀態。 也就是 XAML 中主控的責任`ToggleButton`。

**切換按鈕示範**頁面包含兩個執行個體`ToggleButton`，包括設定的 Visual State Manager 標記`Text`， `BackgroundColor`，和`TextColor`根據視覺狀態的按鈕：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ToggleButtonDemoPage"
             Title="Toggle Button Demo">

    <ContentPage.Resources>
        <Style TargetType="local:ToggleButton">
            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            <Setter Property="HorizontalOptions" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <local:ToggleButton Toggled="OnItalicButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Italic Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Italic On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <local:ToggleButton Toggled="OnBoldButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Bold Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Bold On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <Label x:Name="label"
               Text="Just a little passage of some sample text that can be formatted in italic or boldface by toggling the two buttons."
               FontSize="Large"
               HorizontalTextAlignment="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

`Toggled`事件處理常式是程式碼後置檔案中。 他們負責設定`FontAttributes`屬性`Label`根據按鈕的狀態：

```csharp
public partial class ToggleButtonDemoPage : ContentPage
{
    public ToggleButtonDemoPage ()
    {
        InitializeComponent ();
    }

    void OnItalicButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Italic;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Italic;
        }
    }

    void OnBoldButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Bold;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Bold;
        }
    }
}
```

以下是在 iOS、 Android 和 UWP 上執行的程式：

[![切換按鈕示範](button-images/ToggleButtonDemo.png "切換按鈕示範")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>使用的點陣圖按鈕

`Button`類別會定義[ `Image` ](xref:Xamarin.Forms.Button.Image)屬性，可讓您在上顯示點陣圖影像`Button`，單獨或結合文字。 您也可以指定文字和影像的排列方式。

`Image`屬性屬於型別[ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource)，表示點陣圖必須將儲存為資源在個別的平台專案中，而不是在.NET 標準程式庫專案。

Xamarin.Forms 支援每個平台可讓儲存在多個的大小，針對不同的像素解析度的各種裝置上可能執行的應用程式的映像。 這些多個點陣圖會命名為，或是儲存在一種作業系統可以挑選最佳的相符項目，用於裝置的視訊顯示器解析度。

在點陣圖`Button`、 32 和 64 裝置獨立單位之間通常是最佳的大小、 您想要視大小而定。 此範例中使用的映像為基礎的 48 裝置獨立單位的大小。

在 iOS 專案中，**資源**資料夾包含三種大小的此映像：

- 48 像素的方形點陣圖，儲存為 **/Resources/MonkeyFace.png**
- 儲存為 96 像素正方形點陣圖 **/Resource/MonkeyFace@2x.png**
- 144 像素的方形點陣圖，儲存為 **/Resource/MonkeyFace@3x.png**

提供給所有三個點陣圖**建置動作**的**BundleResource**。

針對 Android 專案中，所有的點陣圖有相同的名稱，但不同的子資料夾中儲存**資源**資料夾：

- 72 像素的方形點陣圖，儲存為 **/Resources/drawable-hdpi/MonkeyFace.png**
- 儲存為 96 像素正方形點陣圖 **/Resources/drawable-xhdpi/MonkeyFace.png**
- 144 像素的方形點陣圖，儲存為 **/Resources/drawable-xxhdpi/MonkeyFace.png**
- 儲存為 192 像素正方形點陣圖 **/Resources/drawable-xxxhdpi/MonkeyFace.png**

這些給予**建置動作**的**AndroidResource**。

在 UWP 專案中，點陣圖可以任何位置儲存在專案中，但是它們通常會儲存在自訂的資料夾或**資產**現有的資料夾。 UWP 專案中包含這些點陣圖：

- 48 像素的方形點陣圖，儲存為 **/Assets/MonkeyFace.scale-100.png**
- 儲存為 96 像素正方形點陣圖 **/Assets/MonkeyFace.scale-200.png**
- 儲存為 192 像素正方形點陣圖 **/Assets/MonkeyFace.scale-400.png**

所有已給予他們**建置動作**的**內容**。

您可以指定如何`Text`和`Image`屬性排列在`Button`使用[ `ContentLayout` ](xref:Xamarin.Forms.Button.ContentLayout)屬性`Button`。 這個屬性的型別是[ `ButtonContentLayout` ](xref:Xamarin.Forms.Button.ButtonContentLayout)，這是中的內嵌的類別`Button`。 [建構函式](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double))有兩個引數：

- 成員[ `ImagePosition` ](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition)列舉型別： `Left`， `Top`， `Right`，或`Bottom`指出點陣圖相對於文字的顯示方式。
- A`double`點陣圖與文字之間的間距的值。

預設值為`Left`和 10 個單位。 兩個唯讀屬性`ButtonContentLayout`名為[ `Position` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Position)和[ `Spacing` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing)提供這些屬性的值。

在程式碼中，您可以建立`Button`並設定`ContentLayout`屬性如下：

```csharp
Button button = new Button
{
    Text = "button text",
    Image = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

在 XAML 中，您需要指定只列舉成員或間距，或以任何順序同時以逗號分隔：

```xaml
<Button Text="button text"
        Image="image filename"
        ContentLayout="Right, 20" />
```

**映像按鈕示範**頁面使用`OnPlatform`來指定不同的檔名，針對 iOS、 Android 和 UWP 點陣圖檔案。 如果您想要用於所有三個平台使用相同的檔案名稱，以避免使用`OnPlatform`，您必須將 UWP 點陣圖儲存在專案的根目錄。

第一個`Button`上**映像按鈕示範**頁面上設定`Image`屬性，但不是`Text`屬性：

```xaml
<Button>
    <Button.Image>
        <OnPlatform x:TypeArguments="FileImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.Image>
</Button>
```

如果 UWP 點陣圖儲存專案的根目錄中，這個標記可以大幅簡化：

```xaml
<Button Image="MonkeyFace.png" />
```

若要避免重複的標記中的許多**ImageButtonDemo.xaml**檔案，隱含`Style`也會定義設定`Image`屬性。 這`Style`自動套用至五個其他`Button`項目。 以下是完整的 XAML 檔案：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">

        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="Image">
                    <OnPlatform x:TypeArguments="FileImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.Image>
                <OnPlatform x:TypeArguments="FileImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.Image>
        </Button>

        <Button Text="Default" />

        <Button Text="Left - 10"
                ContentLayout="Left, 10" />

        <Button Text="Top - 10"
                ContentLayout="Top, 10" />

        <Button Text="Right - 20"
                ContentLayout="Right, 20" />

        <Button Text="Bottom - 20"
                ContentLayout="Bottom, 20" />
    </FlexLayout>
</ContentPage>
```

最後四個`Button`項目請使用`ContentLayout`屬性，以指定的位置和間距的文字和點陣圖：

[![影像按鈕示範](button-images/ImageButtonDemo.png "影像按鈕示範")](button-images/ImageButtonDemo-Large.png#lightbox)

您現在已經瞭解您可以處理的各種方式`Button`事件和變更`Button`外觀。

## <a name="related-links"></a>相關連結

- [ButtonDemos 範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)
- [應用程式開發介面 按鈕](xref:Xamarin.Forms.Button)
