---
title: Xamarin.Forms 按鈕
description: 按鈕會回應點選或按一下，以指示應用程式執行特定工作。
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/26/2018
ms.openlocfilehash: cdff3e721aa91733e995e95ead533a3ad7b41a77
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563923"
---
# <a name="xamarinforms-button"></a>Xamarin.Forms 按鈕

_按鈕會回應點選或按一下，以指示應用程式執行特定工作。_

[ `Button` ](xref:Xamarin.Forms.Button)是最基本的互動式控制，在所有的 Xamarin.Forms。 `Button`通常會顯示簡短文字字串，表示為命令，但它也可以顯示點陣圖影像，或組合的文字和影像。 使用者按下`Button`食指或按一下滑鼠來起始該命令。

大部分的下面討論的主題對應中的頁面[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)範例。

## <a name="handling-button-clicks"></a>處理按鈕按一下

`Button` 定義[ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)在使用者點選時引發的事件`Button`用手指或滑鼠指標。 從介面發行手指或滑鼠按鈕時引發事件`Button`。 `Button`必須有其[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)屬性設定為`true`讓它的點選回應。

**基本按鈕 Click**頁面[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)範例示範如何具現化`Button`中 XAML 並處理其`Clicked`事件。 **BasicButtonClickPage.xaml**檔案包含`StackLayout`兼具`Label`和`Button`:

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

`Button`傾向於佔用它所允許的所有空間。 比方說，如果您不需要設定`HorizontalOptions`屬性`Button`為以外`Fill`，則`Button`會佔用其父代的整個寬度。

根據預設，`Button`是矩形，但您可以使用來提供 it 圓角邊角[ `CornerRadius` ](xref:Xamarin.Forms.Button.CornerRadius)屬性，如以下一節所述[**按鈕外觀**](#button-appearance).

[ `Text` ](xref:Xamarin.Forms.Button.Text)屬性會指定出現在文字`Button`。 [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)事件設定為事件處理常式命名為`OnButtonClicked`。 在程式碼後置檔案中，位於這個處理常式**BasicButtonClickPage.xaml.cs**:

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

當`Button`點選時，`OnButtonClicked`方法執行。 `sender`引數是`Button`負責此事件的物件。 您可以使用來存取`Button`物件，或若要區別多個`Button`共用相同的物件`Clicked`事件。

這個特定`Clicked`處理常式會呼叫旋轉的動畫函式`Label`在 1000年毫秒的 360 度。 以下是程式執行的通用 Windows 平台 (UWP) 應用程式在 iOS 和 Android 裝置，以及在 Windows 10 desktop:

[![基本按鈕 Click](button-images/BasicButtonClick.png "基本按鈕 Click")](button-images/BasicButtonClick-Large.png#lightbox "按一下基本按鈕")

請注意，`OnButtonClicked`方法包含`async`修飾詞因為`await`用於事件處理常式。 A`Clicked`事件處理常式需要`async`修飾詞，只有當處理常式的主體使用`await`。

每個平台呈現`Button`自己特定的方式。 在[**按鈕外觀**](#button-appearance)區段中，您將了解如何設定色彩，並讓`Button`框線可見，更客製化的外觀。 `Button` 會實作[ `IFontElement` ](xref:Xamarin.Forms.Internals.IFontElement)介面，使其包含[ `FontFamily` ](xref:Xamarin.Forms.Button.FontFamily)， [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize)，以及[ `FontAttributes` ](xref:Xamarin.Forms.Button.FontAttributes)屬性。

## <a name="creating-a-button-in-code"></a>在程式碼中建立按鈕

通常會具現化`Button`在 XAML 中，但您也可以建立`Button`在程式碼中。 這可能是方便您的應用程式需要建立多個按鈕是以可列舉的資料為基礎時`foreach`迴圈。

**程式碼按鈕 Click**頁面會示範如何建立一個網頁，其作用相當於**基本按鈕 Click**頁面，但完全在C#:

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

所有項目是在類別的建構函式。 因為`Clicked`處理常式只有一個陳述式，可以將它附加至事件非常簡單：

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

當然，您也可以定義事件處理常式為不同的方法 (如同`OnButtonClick`方法中的**基本按鈕 Click**)，並將該方法附加到事件：

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>停用按鈕

有時候應用程式處於特定狀態在特定`Button`按一下不是有效的作業。 在這些情況下，`Button`應該設定停用其`IsEnabled`屬性設`false`。 典型的範例是`Entry`伴隨著檔案開啟的檔案名稱的控制項`Button`:`Button`一些文字輸入時，才應該啟用`Entry`。
您可以使用`DataTrigger`這項工作，如中所示[**資料觸發程序**](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers)文章。

## <a name="using-the-command-interface"></a>使用命令介面

可以讓應用程式以回應`Button`不需要處理的點選`Clicked`事件。 `Button`實作替代的通知機制，稱為_命令_或是_命令_介面。 這包含兩個屬性：

- [`Command`](xref:Xamarin.Forms.Button.Command) 型別的[ `ICommand` ](xref:System.Windows.Input.ICommand)，在定義的介面[ `System.Windows.Input` ](xref:System.Windows.Input)命名空間。
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) 屬性型別的[ `Object` ](xref:System.Object)。

特別是當實作 Model View ViewModel (MVVM) 架構時，這個方法會與資料繫結，並特別適合。 在文章中討論這些主題[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)，[從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)，並[MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)。

MVVM 應用程式中的 ViewModel 定義類型的屬性`ICommand`，然後連線到 XAML`Button`具有資料繫結項目。 Xamarin.Forms 也會定義[ `Command` ]((xref:Xamarin.Forms.Command`1))並[ `Command<T>` ](xref:Xamarin.Forms.Command`1)類別實作`ICommand`介面，並協助 ViewModel 定義屬性的型別`ICommand`.

命令文件中的更詳細地說明[**的命令介面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)但**基本按鈕命令**頁面[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)範例示範基本的方法。

`CommandDemoViewModel`類別是非常簡單的 ViewModel 定義型別的屬性`double`名為`Number`，和兩個屬性的型別`ICommand`名為`MultiplyBy2Command`和`DivideBy2Command`:

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

這兩個`ICommand`屬性會初始化該類別的建構函式具有兩個物件的型別中`Command`。 `Command`建構函式包括一些函式 (稱為`execute`建構函式引數)，將增加一倍，或部份就`Number`屬性。

**BasicButtonCommand.xaml**檔中設定其`BindingContext`的執行個體`CommandDemoViewModel`。 `Label`元素和兩個`Button`項目包含在三個屬性的繫結`CommandDemoViewModel`:

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

透過這種方法的優點`Clicked`處理常式會確認牽涉到的功能，此頁面的所有邏輯都位於 ViewModel，而不是程式碼後置檔案，達到更佳隔離的使用者介面與商務邏輯。

此外，也可以針對`Command`物件來控制啟用及停用`Button`項目。 例如，假設您想要限制 2 之間的數字值的範圍<sup>10</sup>和 2 個<sup>&ndash;10</sup>。 您可以加入建構函式中的另一個函式 (稱為`canExecute`引數)，會傳回`true`如果`Button`應該啟用。 以下是修改`CommandDemoViewModel`建構函式：

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

呼叫`ChangeCanExecute`方法`Command`所需以便`Command`方法可以呼叫`canExecute`方法，並判斷是否`Button`或不應該停用。 透過此程式碼變更，數目達到限制，`Button`已停用：

[![基本按鈕命令-修改](button-images/BasicButtonCommandModified.png "基本按鈕命令-修改")](button-images/BasicButtonCommandModified-Large.png#lightbox)

您有兩個或多個`Button`繫結至相同的項目`ICommand`屬性。 `Button`項目可以使用辨別[ `CommandParameter` ](xref:Xamarin.Forms.Button.CommandParameter)屬性`Button`。 在此情況下，您會想要使用此泛[ `Command<T>` ](xref:Xamarin.Forms.Command`1)類別。 `CommandParameter`然後物件會傳遞做為引數`execute`和`canExecute`方法。 這項技術會顯示詳細[**基本命令**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)一節[**命令介面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)文章。

[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)範例也會使用這項技術，在其`MainPage`類別。 **MainPage.xaml**檔案包含`Button`每一頁的範例：

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

每個`Button`有其`Command`屬性繫結至屬性，名為`NavigateCommand`，而`CommandParameter`設定為[ `Type` ](xref:System.Type)對應到其中一個專案中的頁面類別的物件。

該`NavigateCommand`屬性的類型是`ICommand`和程式碼後置檔案中定義：

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

建構函式初始化`NavigateCommand`屬性，以`Command<Type>`物件，因為`Type`種`CommandParameter`XAML 檔案中設定的物件。 這表示`execute`方法具有類型的引數`Type`，對應至這個`CommandParameter`物件。 函式具現化頁面上，，然後巡覽至它。

請注意，建構函式結束時，會設定其`BindingContext`本身。 這是必要的繫結至 XAML 檔案中的屬性`NavigateCommand`屬性。

## <a name="pressing-and-releasing-the-button"></a>按下再放開按鈕

除了`Clicked`事件，`Button`也會定義[ `Pressed` ](xref:Xamarin.Forms.Button.Pressed)並[ `Released` ](xref:Xamarin.Forms.Button.Released)事件。 `Pressed`按手指時，就會發生事件`Button`，或按下滑鼠指標位在`Button`。 `Released`發行手指或滑鼠按鈕時，就會發生事件。 一般而言，`Clicked`事件也會引發與同時`Released`事件，但如果手指或滑鼠指標的投影片的表面遠離`Button`之前所發行，`Clicked`事件可能不會發生。

`Pressed`並`Released`事件不常用，但它們可以用於特殊用途，如所示**按下並放開按鈕**頁面。 XAML 檔案中包含`Label`並`Button`使用附加的處理常式`Pressed`和`Released`事件：

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

以動畫顯示的程式碼後置檔案`Label`時`Pressed`事件就會發生，但暫停旋轉時`Released`就會發生事件：

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

結果是`Label`只會旋轉手指接觸時`Button`，並停止手指放開時：

[![按下再放開按鈕](button-images/PressAndReleaseButton.png "按下再放開按鈕")](button-images/PressAndReleaseButton-Large.png)

這種行為有適用於遊戲的應用程式： 手指上保留`Button`可能會讓特定的方向移動在畫面物件。

<a name="button-appearance" />

## <a name="button-appearance"></a>按鈕的外觀

`Button`繼承，或是定義數個會影響其外觀的屬性：

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) 色彩`Button`文字
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 該文字的背景色彩
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) 區域周圍的色彩 `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) 使用文字的字型系列
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) 文字的大小
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) 指出文字是否為斜體或粗體
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) 框線的寬度
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) 會四捨五入邊角

> [!NOTE]
> `Button`類別也有[ `Margin` ](xref:Xamarin.Forms.View.Margin)並[ `Padding` ](xref:Xamarin.Forms.Button.Padding)控制版面配置行為的屬性`Button`。 如需詳細資訊，請參閱 <<c0> [ 邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

六個這些屬性的效果 (不包括`FontFamily`並`FontAttributes`) 中會示範**按鈕外觀**頁面。 另一個屬性[ `Image` ](xref:Xamarin.Forms.Button.Image)，一節所述[**點陣圖使用按鈕**](#image-button)。

中的檢視和資料繫結的所有 **按鈕外觀** XAML 檔案中所定義的頁面：

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

`Button`頁面的頂端具有三`Color`屬性繫結至`Picker`在頁面底部的項目。 中的項目`Picker`項目是從色彩`NamedColor`專案中包含的類別。 三個`Slider`項目包含雙向繫結`FontSize`， `BorderWidth`，以及`CornerRadius`的屬性`Button`。

此程式可讓您試驗所有這些屬性的組合：

[![按鈕外觀](button-images/ButtonAppearance.png "按鈕外觀")](button-images/ButtonAppearance-Large.png)

若要查看`Button`框線，您必須設定`BorderColor`成以外`Default`，和`BorderWidth`設為正值。

在 iOS 上，您會注意到大型的框線寬度會打擾到的內部`Button`，且會影響顯示的文字。 如果您選擇要使用 ios 的框線`Button`，您可能需要的開頭與結尾`Text`空間以保留其可見性屬性。

在 UWP 上選取`CornerRadius`超過高度的一半`Button`引發例外狀況。

## <a name="creating-a-toggle-button"></a>建立切換按鈕

子類別可以`Button`使其運作方式類似 on-off 開關： 點選一次 按鈕來切換按鈕上，點選一次以關閉它。

下列`ToggleButton`類別衍生自`Button`，並定義新的事件，名為`Toggled`和布林值屬性，名為`IsToggled`。 這些是相同的兩個屬性，定義由 Xamarin.Forms [ `Switch` ](xref:Xamarin.Forms.Switch):

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

`ToggleButton`建構函式會將附加的處理常式`Clicked`事件，讓它可以變更的值`IsToggled`屬性。 `OnIsToggledChanged`方法會引發`Toggled`事件。

最後一行`OnIsToggledChanged`方法會呼叫靜態`VisualStateManager.GoToState`方法有兩個文字字串"ToggledOn 」 和 「 ToggledOff"。 您可以閱讀這個方法和您的應用程式將如何回應文件中的視覺狀態的相關[ **Xamarin.Forms Visual State Manager**](~/xamarin-forms/user-interface/visual-state-manager.md)。

因為`ToggleButton`對進行呼叫`VisualStateManager.GoToState`，此類別本身不需要包含任何其他的機制，來變更按鈕的外觀，根據其`IsToggled`狀態。 也就是裝載 XAML 的責任`ToggleButton`。

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

`Toggled`事件處理常式是在程式碼後置檔案中。 他們會負責設定`FontAttributes`屬性`Label`根據按鈕的狀態：

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

以下是在 iOS、 Android 及 UWP 上執行的程式：

[![切換按鈕示範](button-images/ToggleButtonDemo.png "切換按鈕示範")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>使用的點陣圖按鈕

`Button`類別會定義[ `Image` ](xref:Xamarin.Forms.Button.Image)屬性，可讓您在上顯示點陣圖影像`Button`，單獨或搭配文字。 您也可以指定的文字和影像的排列方式。

`Image`屬性的類型是[ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource)，這表示點陣圖都必須儲存在個別的平台專案中，而不是在.NET Standard 程式庫專案的資源。

Xamarin.Forms 所支援的每個平台可讓儲存在不同的像素解析度的各種裝置上可能執行的應用程式的多個大小的影像。 這些是名為多個點陣圖，或將其儲存在一種作業系統可以挑選最符合項目，該裝置的視訊顯示器解析度。

在點陣圖`Button`、 32 和 64 裝置獨立單位之間通常是最佳的大小、 您想要視大小而定。 此範例中使用的映像為基礎的 48 裝置獨立單位的大小。

在 iOS 專案中，**資源**資料夾包含三種大小的這個映像：

- 48 像素的正方形點陣圖，儲存為 **/Resources/MonkeyFace.png**
- 96 像素的正方形點陣圖，儲存為 **/Resource/MonkeyFace@2x.png**
- 144 像素的正方形點陣圖，儲存為 **/Resource/MonkeyFace@3x.png**

提供給所有的三個點陣圖**建置動作**的**BundleResource**。

針對 Android 專案中，所有的點陣圖有相同的名稱，但不同的子資料夾中儲存**資源**資料夾：

- 72 像素的正方形點陣圖，儲存為 **/Resources/drawable-hdpi/MonkeyFace.png**
- 96 像素的正方形點陣圖，儲存為 **/Resources/drawable-xhdpi/MonkeyFace.png**
- 144 像素的正方形點陣圖，儲存為 **/Resources/drawable-xxhdpi/MonkeyFace.png**
- 192 個像素正方形點陣圖儲存為 **/Resources/drawable-xxxhdpi/MonkeyFace.png**

這些已給予**建置動作**的**AndroidResource**。

在 UWP 專案中，點陣圖可以儲存在專案中，但它們通常會儲存在自訂的資料夾或**資產**現有的資料夾。 UWP 專案中包含這些點陣圖：

- 48 像素的正方形點陣圖，儲存為 **/Assets/MonkeyFace.scale-100.png**
- 96 像素的正方形點陣圖，儲存為 **/Assets/MonkeyFace.scale-200.png**
- 192 個像素正方形點陣圖儲存為 **/Assets/MonkeyFace.scale-400.png**

所有已給予他們**建置動作**的**內容**。

您可以指定如何`Text`並`Image`屬性上排列`Button`使用[ `ContentLayout` ](xref:Xamarin.Forms.Button.ContentLayout)屬性`Button`。 此屬性的類型是[ `ButtonContentLayout` ](xref:Xamarin.Forms.Button.ButtonContentLayout)，這是中的內嵌的類別`Button`。 [建構函式](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double))有兩個引數：

- 成員[ `ImagePosition` ](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition)列舉型別： `Left`， `Top`， `Right`，或`Bottom`指出點陣圖相對於文字的顯示方式。
- A`double`點陣圖與文字之間的間距值。

預設值為`Left`和 10 個單位。 兩個唯讀屬性的`ButtonContentLayout`名為[ `Position` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Position)並[ `Spacing` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing)提供這些屬性的值。

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

在 XAML，您需要指定只列舉成員或間距，或同時依任何順序並以逗號分隔：

```xaml
<Button Text="button text"
        Image="image filename"
        ContentLayout="Right, 20" />
```

**映像按鈕示範**頁面上使用`OnPlatform`iOS、 Android 和 UWP 點陣圖檔指定不同的檔案名稱。 如果您想要針對所有的三個平台使用相同的檔案名稱，並避免使用`OnPlatform`，您必須將 UWP 點陣圖儲存在專案的根目錄。

第一個`Button`上**映像按鈕示範**頁面上設定`Image`屬性而非`Text`屬性：

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

如果 UWP 點陣圖會儲存在專案的根目錄，可大幅簡化此標記：

```xaml
<Button Image="MonkeyFace.png" />
```

若要避免重複的標記，在大量**ImageButtonDemo.xaml**檔案，隱含`Style`也會定義設定`Image`屬性。 這`Style`會自動套用至五個其他`Button`項目。 以下是完整的 XAML 檔案：

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

最後四個`Button`元素的使用`ContentLayout`屬性來指定位置和間距的文字和點陣圖：

[![影像按鈕示範](button-images/ImageButtonDemo.png "影像按鈕示範")](button-images/ImageButtonDemo-Large.png#lightbox)

您現在已了解您可以處理的各種方式`Button`事件和變更`Button`外觀。

## <a name="related-links"></a>相關連結

- [ButtonDemos 範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)
- [按鈕 API](xref:Xamarin.Forms.Button)
