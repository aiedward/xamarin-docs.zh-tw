---
title: '[Xamarin] 按鈕'
description: 按鈕會回應點按或按一下，以指示應用程式執行特定工作。
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: f82d590213076f349b21ebdee2832f2bf474d2f2
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912069"
---
# <a name="xamarinforms-button"></a>[Xamarin] 按鈕

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)

_按鈕會回應點按或按一下，以指示應用程式執行特定工作。_

[`Button`](xref:Xamarin.Forms.Button)是所有 Xamarin. Forms 中最基本的互動式控制項。 `Button` 通常會顯示表示命令的簡短文字字串，但它也可以顯示點陣圖影像或文字與影像的組合。 使用者使用手指按下 `Button`，或使用滑鼠按一下以起始該命令。

以下所討論的大部分主題都會對應至[**system.windows.forms.datagridview.buttondemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)範例中的頁面。

## <a name="handling-button-clicks"></a>處理按鈕點按動作

`Button` 定義當使用者利用手指或滑鼠指標來點按 `Button` 時，所引發的[`Clicked`](xref:Xamarin.Forms.Button.Clicked)事件。 從 `Button`介面放開手指或滑鼠按鍵時，就會引發事件。 `Button` 必須將其[`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)屬性設定為 [`true`]，才能回應點擊。

[**System.windows.forms.datagridview.buttondemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)範例中的**基本按鈕按一下**頁面示範如何在 XAML 中具現化 `Button`，並處理其 `Clicked` 事件。 **BasicButtonClickPage**包含具有 `Label` 和 `Button`的 `StackLayout`：

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

`Button` 傾向于佔用它所允許的所有空間。 例如，如果您未將 `Button` 的 `HorizontalOptions` 屬性設定為 `Fill`以外的專案，`Button` 將會佔用其父系的完整寬度。

根據預設，`Button` 為 [矩形]，但您可以使用 [ [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) ] 屬性為其圓角，如下面的「[**按鈕外觀**](#button-appearance)」一節所述。

[`Text`](xref:Xamarin.Forms.Button.Text) 屬性可指定出現在 `Button` 中的文字。 [`Clicked`](xref:Xamarin.Forms.Button.Clicked)事件會設定為名為 `OnButtonClicked`的事件處理常式。 這個處理常式位於程式碼後置檔案**BasicButtonClickPage.xaml.cs**中：

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

當您按下 `Button` 時，就會執行 `OnButtonClicked` 方法。 `sender` 引數是負責此事件的 `Button` 物件。 您可以使用這個來存取 `Button` 物件，或區別共用相同 `Clicked` 事件的多個 `Button` 物件。

這個特定的 `Clicked` 處理常式會呼叫動畫函式，以1000毫秒為單位旋轉 `Label` 360 度。 以下是在 iOS 和 Android 裝置上執行的程式，以及做為 Windows 10 桌面上的通用 Windows 平臺（UWP）應用程式：

[![基本按鈕按一下](button-images/BasicButtonClick.png "基本按鈕按一下")](button-images/BasicButtonClick-Large.png#lightbox "基本按鈕按一下")

請注意，`OnButtonClicked` 方法包含 `async` 修飾詞，因為 `await` 是在事件處理常式內使用。 只有當處理常式的主體使用 `await`時，`Clicked` 事件處理常式才需要 `async` 修飾詞。

每個平臺都會以自己的特定方式呈現 `Button`。 在 [[**按鈕外觀**](#button-appearance)] 區段中，您將瞭解如何設定色彩，並讓 `Button` 框線顯示，以取得更多自訂的外觀。 `Button` 會執行[`IFontElement`](xref:Xamarin.Forms.Internals.IFontElement)介面，因此它包含[`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)、 [`FontSize`](xref:Xamarin.Forms.Button.FontSize)和[`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)屬性。

## <a name="creating-a-button-in-code"></a>在程式碼中建立按鈕

通常會在 XAML 中具現化 `Button`，但您也可以在程式碼中建立 `Button`。 當您的應用程式需要根據以 `foreach` 迴圈可列舉的資料來建立多個按鈕時，這可能會很方便。

[程式**代碼] 按鈕按一下**[頁面]，示範如何建立功能相當於**基本按鈕按一下**頁面，但完全在C#中的頁面：

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

所有專案都是在類別的函式中完成。 因為 `Clicked` 處理常式只是一個語句長，所以可以將它附加至事件，非常簡單：

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

當然，您也可以將事件處理常式定義為不同的方法（就像 [**基本] 按鈕按一下**中的 `OnButtonClick` 方法），然後將該方法附加至事件：

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>停用按鈕

有時候應用程式處於特定狀態，特定的 `Button` 按一下則不是有效的作業。 在這些情況下，應該藉由將其 `IsEnabled` 屬性設定為 `false`來停用 `Button`。 傳統範例是附有檔案開啟 `Button`之檔案名的 `Entry` 控制項：只有在某些文字已輸入 `Entry`時，才應該啟用 `Button`。
您可以使用此工作的 `DataTrigger`，如[**資料觸發**](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers)程式一文所示。

## <a name="using-the-command-interface"></a>使用命令介面

應用程式可以在不處理 `Clicked` 事件的情況下，回應 `Button` 的點擊。 `Button` 會執行稱為_命令或命令_介面的替代通知機制。 這包含兩個屬性：

- [`ICommand`](xref:System.Windows.Input.ICommand)類型的[`Command`](xref:Xamarin.Forms.Button.Command) ，也就是[`System.Windows.Input`](xref:System.Windows.Input)命名空間中定義的介面。
- [`Object`](xref:System.Object)類型的[`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)屬性。

這種方法特別適用于與資料系結的連接，特別是在執行模型 ViewModel （MVVM）架構時。 這些主題會在[資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結、資料系結[至 mvvm](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)和[mvvm](~/xamarin-forms/enterprise-application-patterns/mvvm.md)等文章中討論。

在 MVVM 應用程式中，viewmodel 會定義 `ICommand` 類型的屬性，然後連接到具有資料系結的 XAML `Button` 元素。 [Xamarin] 也會定義執行 `ICommand` 介面的[`Command`](xref:Xamarin.Forms.Command)和[`Command<T>`](xref:Xamarin.Forms.Command`1)類別，並協助 viewmodel 定義 `ICommand`類型的屬性。

[**命令介面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)一文中有更詳細的說明，但[**system.windows.forms.datagridview.buttondemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)範例中的 [**基本] 按鈕命令**頁面會顯示基本的方法。

`CommandDemoViewModel` 類別是非常簡單的 viewmodel，會定義名為 `Number``double` 類型的屬性，以及名為 `MultiplyBy2Command` 和 `DivideBy2Command`之類型 `ICommand` 的兩個屬性：

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

這兩個 `ICommand` 屬性會在類別的函式中以 `Command`類型的兩個物件進行初始化。 `Command` 的函式包含一個小函式（稱為 `execute` 的「函式」引數），其會加倍或一半 `Number` 的屬性。

**BasicButtonCommand**會將其 `BindingContext` 設定為 `CommandDemoViewModel`的實例。 `Label` 專案和兩個 `Button` 元素都包含 `CommandDemoViewModel`中三個屬性的系結：

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

當兩個 `Button` 的元素被按下時，就會執行命令，而數位會變更值：

[![基本按鈕命令](button-images/BasicButtonCommand.png "基本按鈕命令")](button-images/BasicButtonCommand-Large.png#lightbox)

這種方法在 `Clicked` 處理常式上的優點是，所有牽涉到此頁面功能的邏輯都位於 viewmodel 中，而不是程式碼後置檔案中，以更好的方式區隔使用者介面與商務邏輯。

`Command` 物件也可以控制 `Button` 元素的啟用和停用。 例如，假設您想要將數位值的範圍限制在 2<sup>10</sup>到 2<sup>&ndash;10</sup>之間。 如果應該啟用 `Button`，您可以將另一個函式新增至會傳回 `true` 的函式（稱為 `canExecute` 引數）。 以下是 `CommandDemoViewModel` 的程式的修改：

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

呼叫 `Command` 的 `ChangeCanExecute` 方法是必要的，因此 `Command` 方法可以呼叫 `canExecute` 方法，並判斷是否應該停用 `Button`。 隨著這段程式碼的變更，當數位達到限制時，就會停用 `Button`：

[![基本按鈕命令-已修改](button-images/BasicButtonCommandModified.png "基本按鈕命令-已修改")](button-images/BasicButtonCommandModified-Large.png#lightbox)

兩個或多個 `Button` 元素可以系結至相同的 `ICommand` 屬性。 `Button` 元素可以使用 `Button`的[`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)屬性加以區別。 在此情況下，您會想要使用泛型[`Command<T>`](xref:Xamarin.Forms.Command`1)類別。 然後，`CommandParameter` 物件會當做引數傳遞至 `execute` 和 `canExecute` 方法。 這項技術會在[**命令介面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)一文的[**基本**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)命令區段中詳細顯示。

[**System.windows.forms.datagridview.buttondemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)範例也會在其 `MainPage` 類別中使用這項技術。 **MainPage**包含範例中每個頁面的 `Button`：

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

每個 `Button` 都有其 `Command` 屬性系結至名為 `NavigateCommand`的屬性，而且 `CommandParameter` 會設定為對應至專案中其中一個頁面類別的[`Type`](xref:System.Type)物件。

該 `NavigateCommand` 屬性的類型為 `ICommand`，並定義于程式碼後置檔案中：

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

此函式會將 `NavigateCommand` 屬性初始化為 `Command<Type>` 物件，因為 `Type` 是 XAML 檔案中設定之 `CommandParameter` 物件的型別。 這表示 `execute` 方法具有對應于這個 `CommandParameter` 物件之類型 `Type` 的引數。 函式會具現化頁面，然後流覽至該網頁。

請注意，此函式會將其 `BindingContext` 設定為其本身來結束。 若要將 XAML 檔案中的屬性系結至 `NavigateCommand` 屬性，這是必要的。

## <a name="pressing-and-releasing-the-button"></a>按下並放開按鈕

除了 `Clicked` 事件，`Button` 也會定義 [`Pressed`](xref:Xamarin.Forms.Button.Pressed) 和 [`Released`](xref:Xamarin.Forms.Button.Released) 事件。 當手指按下 `Button`時，或是按下滑鼠按鍵，並將游標置於 `Button`上方時，就會發生 `Pressed` 事件。 釋放手指或滑鼠按鍵時，就會發生 `Released` 事件。 一般來說，`Clicked` 事件也會與 `Released` 事件同時引發，但如果手指或滑鼠指標滑出 `Button` 的表面，則在釋放之前，`Clicked` 事件可能不會發生。

`Pressed` 和 `Released` 事件通常不會使用，但可用於特殊用途，如 [**按下] 和 [發行] 按鈕**頁面中所示。 XAML 檔案包含 `Label` 和 `Button`，其中含有針對 `Pressed` 和 `Released` 事件附加的處理常式：

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

當發生 `Pressed` 事件時，程式碼後置檔案會以動畫呈現 `Label`，但會在發生 `Released` 事件時暫停旋轉：

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

結果是 `Label` 只會在手指與 `Button`接觸時旋轉，並在手指放開時停止：

[![按下並放開按鈕](button-images/PressAndReleaseButton.png "按下並放開按鈕")](button-images/PressAndReleaseButton-Large.png)

這種行為有適用于遊戲的應用程式：在 `Button` 上保留的手指可能會使螢幕上物件以特定方向移動。

<a name="button-appearance" />

## <a name="button-appearance"></a>按鈕外觀

`Button` 會繼承或定義數個會影響其外觀的屬性：

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)是 `Button` 文字的色彩
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)是該文字的背景色彩
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor)是圍繞著 `Button` 的區域色彩
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)是用於文字的字型系列
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)是文字的大小
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)指出文字是斜體或粗體
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth)是框線的寬度
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius)是 `Button` 的圓角半徑
- `CharacterSpacing` 是 `Button` 文字字元之間的間距

> [!NOTE]
> `Button` 類別也具有[`Margin`](xref:Xamarin.Forms.View.Margin)和[`Padding`](xref:Xamarin.Forms.Button.Padding)屬性，可控制 `Button`的版面配置行為。 如需詳細資訊，請參閱[邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

這六個屬性的效果（不包括 `FontFamily` 和 `FontAttributes`）會在 [**按鈕外觀**] 頁面中示範。 另一個屬性（ [`Image`](xref:Xamarin.Forms.Button.ImageSource)）將在[**使用點陣圖搭配按鈕**](#image-button)一節中討論。

[**按鈕外觀**] 頁面中的所有視圖和資料系結都會定義于 XAML 檔案中：

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

頁面頂端的 `Button` 有其三個 `Color` 屬性系結至頁面底部的 `Picker` 元素。 `Picker` 元素中的專案是專案中所包含 `NamedColor` 類別的色彩。 三個 `Slider` 元素包含 `Button`的 `FontSize`、`BorderWidth`和 `CornerRadius` 屬性的雙向系結。

此程式可讓您試驗所有這些屬性的組合：

[![按鈕外觀](button-images/ButtonAppearance.png "按鈕外觀")](button-images/ButtonAppearance-Large.png)

若要查看 `Button` 框線，您必須將 `BorderColor` 設定為 `Default`以外的值，並將 `BorderWidth` 設為正值。

在 iOS 上，您會發現大型框線寬度 intrude 在 `Button` 的內部，並干擾文字的顯示。 如果您選擇使用具有 iOS `Button`的框線，您可能會想要開始和結束具有空格的 `Text` 屬性，以保留其可見度。

在 UWP 上，選取超過一半 `Button` 的 `CornerRadius` 會引發例外狀況。

## <a name="button-visual-states"></a>按鈕視覺狀態

[`Button`](xref:Xamarin.Forms.Button)具有 `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) ，可以在使用者按下時，用來起始對 `Button` 的視覺變更，前提是已啟用。

下列 XAML 範例顯示如何定義 `Pressed` 狀態的視覺狀態：

```xaml
<Button Text="Click me!"
        ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Button>
```

`Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState)指定按下[`Button`](xref:Xamarin.Forms.Button)時，其[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性會從其預設值1變更為0.8。 `Normal` `VisualState` 指定當 `Button` 處於正常狀態時，其 `Scale` 屬性會設定為1。 因此，整體的效果是當按下 `Button` 時，它的重新調整會稍微小一點，而釋放 `Button` 時，就會重新調整為其預設大小。

如需視覺狀態的詳細資訊，請參閱「 [Xamarin 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)」。

## <a name="creating-a-toggle-button"></a>建立切換按鈕

您可以將 `Button` 子類別化，使其運作方式類似于關閉參數：按住按鈕一次，即可切換按鈕，並再次將其關閉。

下列 `ToggleButton` 類別衍生自 `Button`，並定義名為 `Toggled` 的新事件和名為 `IsToggled`的布林屬性。 這些是由 Xamarin [`Switch`](xref:Xamarin.Forms.Switch)所定義的兩個相同屬性：

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

`ToggleButton` 的函式會將處理常式附加至 `Clicked` 事件，讓它可以變更 `IsToggled` 屬性的值。 `OnIsToggledChanged` 方法會引發 `Toggled` 事件。

`OnIsToggledChanged` 方法的最後一行會呼叫具有兩個文字字串 "ToggledOn" 和 "ToggledOff" 的靜態 `VisualStateManager.GoToState` 方法。 您可以閱讀有關此方法的資訊，以及您的應用程式如何回應 [ [**Xamarin] 視覺狀態管理員**](~/xamarin-forms/user-interface/visual-state-manager.md)一文中的視覺狀態。

因為 `ToggleButton` 會呼叫 `VisualStateManager.GoToState`，所以類別本身不需要包含任何額外的設備，就可以根據其 `IsToggled` 狀態來變更按鈕的外觀。 這是主控 `ToggleButton`之 XAML 的責任。

[**切換按鈕示範**] 頁面包含 `ToggleButton`的兩個實例，包括根據視覺狀態設定按鈕 `Text`、`BackgroundColor`和 `TextColor` 的視覺狀態管理員標記：

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

`Toggled` 的事件處理常式位於程式碼後置檔案中。 它們會負責根據按鈕的狀態來設定 `Label` 的 `FontAttributes` 屬性：

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

以下是在 iOS、Android 和 UWP 上執行的程式：

[![切換按鈕示範](button-images/ToggleButtonDemo.png "切換按鈕示範")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>使用具有按鈕的點陣圖

`Button` 類別定義一個[`ImageSource`](xref:Xamarin.Forms.Button.Image)屬性，可讓您在 `Button`上顯示點陣圖影像，不論是單獨或結合文字。 您也可以指定文字和影像的相片順序。

`ImageSource` 屬性是[`ImageSource`](xref:Xamarin.Forms.ImageSource)類型，這表示可以從檔案、內嵌資源、URI 或資料流程載入點陣圖。

> [!NOTE]
> 雖然 `Button` 可以載入動畫 GIF，但它只會顯示 GIF 的第一個框架。

Xamarin 支援的每個平臺都可以針對應用程式執行所在之各種裝置的不同圖元解析度，以多種大小來儲存影像。 這些點陣圖會以一種方式命名或儲存，讓作業系統能夠挑選最符合裝置的影片顯示解析度。

若為 `Button`上的點陣圖，最佳大小通常介於32和64裝置獨立單位之間，視您想要的大小時而定。 此範例中使用的影像是以48裝置獨立單位的大小為基礎。

在 iOS 專案中， **Resources**資料夾包含此影像的三種大小：

- 儲存為 **/Resources/MonkeyFace.png**的48圖元正方形點陣圖
- 儲存為 **/Resource/MonkeyFace@2x.png** 的96圖元正方形點陣圖
- 儲存為 **/Resource/MonkeyFace@3x.png** 的144圖元正方形點陣圖

這三個位圖都有**BundleResource**的**組建動作**。

針對 Android 專案，點陣圖全部具有相同的名稱，但儲存在**Resources**資料夾的不同子資料夾中：

- 儲存為 **/Resources/drawable-hDPI/MonkeyFace.png**的72圖元正方形點陣圖
- 儲存為 **/Resources/drawable-xhDPI/MonkeyFace.png**的96圖元正方形點陣圖
- 儲存為 **/Resources/drawable-xxhDPI/MonkeyFace.png**的144圖元正方形點陣圖
- 儲存為 **/Resources/drawable-xxxhDPI/MonkeyFace.png**的192圖元正方形點陣圖

這些是針對**AndroidResource**的**組建動作**而提供的。

在 UWP 專案中，點陣圖可以儲存在專案中的任何位置，但通常會儲存在自訂資料夾或**資產**現有資料夾中。 UWP 專案包含下列點陣圖：

- 儲存為 **/Assets/MonkeyFace.scale-100.png**的48圖元正方形點陣圖
- 儲存為 **/Assets/MonkeyFace.scale-200.png**的96圖元正方形點陣圖
- 儲存為 **/Assets/MonkeyFace.scale-400.png**的192圖元正方形點陣圖

它們都有提供**內容**的**組建動作**。

您可以使用 `Button`的[`ContentLayout`](xref:Xamarin.Forms.Button.ContentLayout)屬性，指定 `Text` 和 `ImageSource` 屬性在 `Button` 上的相片順序。 這個屬性的類型是[`ButtonContentLayout`](xref:Xamarin.Forms.Button.ButtonContentLayout)，這是 `Button`中的內嵌類別。 此[函](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double))式有兩個引數：

- [`ImagePosition`](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition)列舉的成員： `Left`、`Top`、`Right`或 `Bottom`，表示點陣圖相對於文字的顯示方式。
- 點陣圖和文字之間間距的 `double` 值。

預設值為 `Left` 和10個單位。 名為[`Position`](xref:Xamarin.Forms.Button.ButtonContentLayout.Position)和[`Spacing`](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) `ButtonContentLayout` 的兩個唯讀屬性會提供這些屬性的值。

在程式碼中，您可以建立 `Button` 並設定 `ContentLayout` 屬性，如下所示：

```csharp
Button button = new Button
{
    Text = "button text",
    ImageSource = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

在 XAML 中，您只需要指定列舉成員或間距，或以逗號分隔的任何順序：

```xaml
<Button Text="button text"
        ImageSource="image filename"
        ContentLayout="Right, 20" />
```

[**影像按鈕示範**] 頁面會使用 `OnPlatform`，為 IOS、ANDROID 和 UWP 點陣圖檔案指定不同的檔案名。 如果您想要針對每個平臺使用相同的檔案名，並避免使用 `OnPlatform`，則必須將 UWP 點陣圖儲存在專案的根目錄中。

[**影像按鈕示範**] 頁面上的第一個 `Button` 會設定 `Image` 屬性，而不是 `Text` 屬性：

```xaml
<Button>
    <Button.ImageSource>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.ImageSource>
</Button>
```

如果 UWP 點陣圖儲存在專案的根目錄中，可以大幅簡化此標記：

```xaml
<Button ImageSource="MonkeyFace.png" />
```

為了避免**ImageButtonDemo xaml**檔案中有許多重複的標記，也會定義隱含的 `Style` 來設定 `ImageSource` 屬性。 此 `Style` 會自動套用至五個其他 `Button` 元素。 以下是完整的 XAML 檔案：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">

        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="ImageSource">
                    <OnPlatform x:TypeArguments="ImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.ImageSource>
                <OnPlatform x:TypeArguments="ImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.ImageSource>
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

最後四個 `Button` 元素會利用 `ContentLayout` 屬性來指定文字和點陣圖的位置和間距：

[![影像按鈕示範](button-images/ImageButtonDemo.png "影像按鈕示範")](button-images/ImageButtonDemo-Large.png#lightbox)

您現在已經看過各種可以處理 `Button` 事件和變更 `Button` 外觀的方式。

## <a name="related-links"></a>相關連結

- [System.windows.forms.datagridview.buttondemos 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)
- [Button API](xref:Xamarin.Forms.Button)
