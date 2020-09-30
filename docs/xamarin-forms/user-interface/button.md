---
title: Xamarin.Forms 按鈕
description: 按鈕會回應點按或按一下，以指示應用程式執行特定工作。
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fba8dcb344f79c21815a58cff0d8132967381cca
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556733"
---
# <a name="no-locxamarinforms-button"></a>Xamarin.Forms 按鈕

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)

_按鈕會回應點按或按一下，以指示應用程式執行特定工作。_

[`Button`](xref:Xamarin.Forms.Button)是中最基本的互動式控制項 Xamarin.Forms 。 `Button`通常會顯示指出命令的簡短文字字串，但它也可以顯示點陣圖影像或文字和影像的組合。 使用者按下 `Button` 手指，或使用滑鼠按一下滑鼠來起始該命令。

以下討論的大部分主題都對應于 [**system.windows.forms.datagridview.buttondemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) 範例中的頁面。

## <a name="handling-button-clicks"></a>處理按鈕點按動作

`Button` 定義 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 當使用者 `Button` 利用手指或滑鼠指標來點擊時，所引發的事件。 從的介面釋出手指或滑鼠按鍵時，就會引發此事件 `Button` 。 `Button`必須將其 [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) 屬性設定為， `true` 它才能回應點擊。

[**System.windows.forms.datagridview.buttondemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)範例中的**基本按鈕 Click**頁面會示範如何 `Button` 在 XAML 中具現化，並處理其 `Clicked` 事件。 **BasicButtonClickPage .xaml**檔案包含 `StackLayout` 具有 `Label` 和的 `Button` ：

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

`Button`往往會佔用它所允許的所有空間。 例如，如果您未將的 `HorizontalOptions` 屬性設定 `Button` 為，則 `Fill` `Button` 會佔用其父系的完整寬度。

依預設， `Button` 是矩形，但您可以使用屬性為其圓角 [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) ，如下面的區段 [**按鈕外觀**](#button-appearance)所述。

[`Text`](xref:Xamarin.Forms.Button.Text)屬性會指定出現在中的文字 `Button` 。 [`Clicked`](xref:Xamarin.Forms.Button.Clicked)事件會設定為名為的事件處理常式 `OnButtonClicked` 。 此處理程式位於程式碼後置檔案 **BasicButtonClickPage.xaml.cs**：

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

方法會在點選 `Button``OnButtonClicked` 時執行。 `sender`引數是 `Button` 負責此事件的物件。 您可以使用此專案來存取 `Button` 物件，或區別多個 `Button` 共用相同事件的物件 `Clicked` 。

這個特定 `Clicked` 的處理常式會呼叫 `Label` 在1000毫秒內旋轉360度的動畫函數。 以下是在 iOS 和 Android 裝置上執行的程式，以及 Windows 10 桌面上 (UWP) 應用程式的通用 Windows 平臺：

[![基本按鈕按一下](button-images/BasicButtonClick.png "基本按鈕按一下")](button-images/BasicButtonClick-Large.png#lightbox "基本按鈕按一下")

請注意，此 `OnButtonClicked` 方法包含 `async` 修飾詞，因為 `await` 會在事件處理常式中使用。 `Clicked` `async` 只有當處理常式的主體使用時，事件處理常式才需要修飾元 `await` 。

每個平臺都會 `Button` 以自己的特定方式呈現。 在 [ [**按鈕外觀**](#button-appearance) ] 區段中，您將瞭解如何設定色彩，並讓 `Button` 框線顯示為更多自訂外觀。 `Button` 會實作為 [`IFontElement`](xref:Xamarin.Forms.Internals.IFontElement) 介面，因此它包含 [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) 、 [`FontSize`](xref:Xamarin.Forms.Button.FontSize) 和 [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) 屬性。

## <a name="creating-a-button-in-code"></a>在程式碼中建立按鈕

通常 `Button` 會在 XAML 中具現化，但您也可以在程式 `Button` 代碼中建立。 當您的應用程式需要根據可透過迴圈來列舉的資料建立多個按鈕時，這可能會很方便 `foreach` 。

程式 **代碼按鈕的 click** 頁面會示範如何建立功能相當於 **基本按鈕 Click** 頁面但完全以 c # 撰寫的頁面：

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

所有專案都是在類別的函式中完成。 因為 `Clicked` 處理常式的長度只是一個語句，所以可以直接附加至事件：

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

當然，您也可以將事件處理常式定義為個別的方法 (就像 [ `OnButtonClick` 基本] **按鈕** 中的方法一樣，按一下 [) ]，然後將該方法附加至事件：

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>停用按鈕

有時候，應用程式會處於特定狀態，其中特定的 click 不是有效的作業 `Button` 。 在這些情況下，您 `Button` 應該將其屬性設定 `IsEnabled` 為來停用 `false` 。 典型的範例是 `Entry` 檔案名的控制項（附有檔案開啟） `Button` ：只有在 `Button` 某些文字已輸入至時，才應該啟用 `Entry` 。
您可以 `DataTrigger` 針對這項工作使用，如 [**資料觸發**](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) 程式文章所示。

## <a name="using-the-command-interface"></a>使用命令介面

應用程式可能會在 `Button` 不處理事件的情況下回應點擊 `Clicked` 。 會 `Button` 執行另一個稱為命令或_命令_介面的_commanding_通知機制。 這包括兩個屬性：

- [`Command`](xref:Xamarin.Forms.Button.Command) 型別為的 [`ICommand`](xref:System.Windows.Input.ICommand) 介面，此介面定義于 [`System.Windows.Input`](xref:System.Windows.Input) 命名空間中。
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) 型別的屬性 [`Object`](xref:System.Object) 。

這種方法特別適用于與資料系結的連接，特別是在實 ViewModel (MVVM) 架構的模型視圖時。 這些主題會在 [資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結、資料系結 [至 mvvm](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)以及 [mvvm](~/xamarin-forms/enterprise-application-patterns/mvvm.md)等文章中討論。

在 MVVM 應用程式中，viewmodel 會定義類型 `ICommand` 的屬性，這些屬性接著會連接到具有資料系結的 XAML `Button` 元素。 Xamarin.Forms 也 [`Command`](xref:Xamarin.Forms.Command) 會定義和 [`Command<T>`](xref:Xamarin.Forms.Command`1) 類別，以執行 `ICommand` 介面並協助 viewmodel 定義類型的屬性 `ICommand` 。

[**命令介面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)中會更詳細地說明命令，但[**system.windows.forms.datagridview.buttondemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)範例中的 [**基本] 按鈕命令**頁面會顯示基本的方法。

`CommandDemoViewModel`類別是非常簡單的 viewmodel，它會定義名為之型別的屬性 `double` `Number` ，以及 `ICommand` 名為和的兩個型別屬性 `MultiplyBy2Command` `DivideBy2Command` ：

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

這兩個 `ICommand` 屬性會在類別的函式中，以兩個類型的物件初始化 `Command` 。 這些函式包含一個小函式 (稱為「函式」 `Command` `execute` 引數，) 會將屬性加倍或一半 `Number` 。

**BasicButtonCommand .xaml**檔案會將其設定 `BindingContext` 為的實例 `CommandDemoViewModel` 。 `Label`元素與兩個 `Button` 元素包含下列三個屬性的系結 `CommandDemoViewModel` ：

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

當兩個 `Button` 元素被點擊時，會執行命令，並將數值變更為：

[![基本按鈕命令](button-images/BasicButtonCommand.png "基本按鈕命令")](button-images/BasicButtonCommand-Large.png#lightbox)

這種方法的優點在於， `Clicked` 所有牽涉到此頁面功能的邏輯都位於 viewmodel，而不是程式碼後置檔案中，可讓使用者介面與商務邏輯更緊密地區隔。

物件也可以 `Command` 控制啟用和停用 `Button` 元素。 例如，假設您想要限制介於 2<sup>10</sup>到<sup> &ndash; 2 10 之間</sup>的數值範圍。 您可以將另一個函式新增至函式 (稱為 `canExecute` 引數) `true` 如果應該啟用，則會傳回 `Button` 。 以下是對函數的修改 `CommandDemoViewModel` ：

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

的 `ChangeCanExecute` 方法呼叫 `Command` 是必要的，因此 `Command` 方法可以呼叫 `canExecute` 方法，並判斷是否 `Button` 應該停用。 當此程式碼變更時，當數位達到限制時， `Button` 就會停用：

[![基本按鈕命令-已修改](button-images/BasicButtonCommandModified.png "基本按鈕命令-已修改")](button-images/BasicButtonCommandModified-Large.png#lightbox)

有兩個或多個元素可以系結 `Button` 至相同的 `ICommand` 屬性。 您 `Button` 可以使用的屬性來辨別元素 [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) `Button` 。 在此情況下，您會想要使用泛型 [`Command<T>`](xref:Xamarin.Forms.Command`1) 類別。 `CommandParameter`然後，會將物件當作引數傳遞至 `execute` 和 `canExecute` 方法。 這項技術會在[**命令介面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)文章的[**基本命令**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)區段中詳細地顯示。

[**System.windows.forms.datagridview.buttondemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)範例也會在其類別中使用這項技術 `MainPage` 。 **MainPage .xaml**檔案包含 `Button` 每個範例頁面的：

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

每個 `Button` `Command` 屬性都會系結至名為的屬性 `NavigateCommand` ，而且 `CommandParameter` 會設定為 [`Type`](xref:System.Type) 對應至專案中其中一個頁面類別的物件。

該 `NavigateCommand` 屬性的型別為 `ICommand` ，而且是在程式碼後端檔案中定義：

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

`NavigateCommand` `Command<Type>` 由於 `Type` 是 XAML 檔案中設定的物件類型，因此，此函式會將屬性初始化為物件 `CommandParameter` 。 這表示該 `execute` 方法具有 `Type` 對應至這個物件之類型的引數 `CommandParameter` 。 此函式會將頁面具現化，然後流覽至該頁面。

請注意，此函式會將其設定 `BindingContext` 為其本身，以結束。 XAML 檔案中的屬性必須系結至 `NavigateCommand` 屬性。

## <a name="pressing-and-releasing-the-button"></a>按下並放開按鈕

除了 `Clicked` 事件之外， `Button` 也會定義 [`Pressed`](xref:Xamarin.Forms.Button.Pressed) 和 [`Released`](xref:Xamarin.Forms.Button.Released) 事件。 `Pressed`當手指按下 `Button` ，或按下滑鼠按鍵時，就會發生此事件 `Button` 。 `Released`釋放手指或滑鼠按鍵時，就會發生此事件。 一般而言，事件 `Clicked` 也會同時引發 `Released` ，但如果手指或滑鼠指標從的表面滑離 `Button` ，則 `Clicked` 可能不會發生此事件。

`Pressed`和 `Released` 事件不常使用，但可用於特殊用途，如 [**按下和放開] 按鈕**頁面所示。 XAML 檔案包含和，並 `Label` `Button` 附加給和事件的處理 `Pressed` 程式 `Released` ：

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

當事件發生時，程式碼後置於檔案的動畫 `Label` `Pressed` ，但在事件發生時暫停旋轉 `Released` ：

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

結果就是 `Label` 只有當手指與相同時，才會旋轉 `Button` ，並在手指釋放時停止：

[![按下並放開按鈕](button-images/PressAndReleaseButton.png "按下並放開按鈕")](button-images/PressAndReleaseButton-Large.png)

這種行為具有適用于遊戲的應用程式：保留在上的手指 `Button` 可能會讓螢幕上的物件以特定方向移動。

## <a name="button-appearance"></a>按鈕外觀

`Button`會繼承或定義會影響其外觀的數個屬性：

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)這是文字的色彩 `Button`
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 這是文字的背景色彩
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) 是周圍區域的色彩 `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) 是用於文字的字型系列
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) 這是文字的大小
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) 指出文字是否為斜體或粗體
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) 框線的寬度
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) 是的圓角半徑。 `Button`
- [`CharacterSpacing`](xref:Xamarin.Forms.Button.CharacterSpacing) 這是文字字元之間的間距 `Button` 。
- `TextTransform` 決定文字的大小寫 `Button` 。

> [!NOTE]
> `Button`類別也有 [`Margin`](xref:Xamarin.Forms.View.Margin) 和 [`Padding`](xref:Xamarin.Forms.Button.Padding) 屬性，可控制的版面配置行為 `Button` 。 如需詳細資訊，請參閱[邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

其中六個屬性 (`FontFamily` 不包括和) 的效果 `FontAttributes` 會在 [ **按鈕外觀** ] 頁面中示範。 另一個屬性， [`Image`](xref:Xamarin.Forms.Button.ImageSource) 會在 [**使用點陣圖搭配按鈕**](#using-bitmaps-with-buttons)的區段中討論。

在 [ **按鈕外觀** ] 頁面中的所有視圖和資料系結都是在 XAML 檔案中定義：

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

`Button`頁面頂端的三個屬性系結 `Color` 至 `Picker` 頁面底部的元素。 專案中的專案 `Picker` 是專案中包含之 `NamedColor` 類別的色彩。 三個 `Slider` 元素包含的 `FontSize` 、 `BorderWidth` 和屬性的雙向系結 `CornerRadius` `Button` 。

此程式可讓您試驗所有這些屬性的組合：

[![按鈕外觀](button-images/ButtonAppearance.png "按鈕外觀")](button-images/ButtonAppearance-Large.png)

若要查看 `Button` 框線，您必須將設為以外的 `BorderColor` `Default` 值，並將設 `BorderWidth` 為正值。

在 iOS 上，您會發現，大型框線寬度會 intrude 到的內部， `Button` 並干擾文字的顯示。 如果您選擇使用具有 iOS 的框線 `Button` ，您可能會想要以空格來開始和結束 `Text` 屬性，以保留其可見度。

在 UWP 上，選取 `CornerRadius` 超過的一半會 `Button` 引發例外狀況。

## <a name="button-visual-states"></a>按鈕視覺狀態

[`Button`](xref:Xamarin.Forms.Button) 的具有， `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) 可在使用者按下時，用來起始對的視覺變更 `Button` ，但前提是已啟用它。

下列 XAML 範例顯示如何定義狀態的視覺狀態 `Pressed` ：

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

`Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) 指定當 [`Button`](xref:Xamarin.Forms.Button) 按下時，其 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性將會從預設值1變更為0.8。 `Normal` `VisualState` 指定當 `Button` 處於正常狀態時，其 `Scale` 屬性會設定為1。 因此，整體效果是當 `Button` 按下時，重新調整會稍微減少一點，當 `Button` 釋放時，會重新調整為預設大小。

如需視覺狀態的詳細資訊，請參閱 [ Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="creating-a-toggle-button"></a>建立切換按鈕

您可以將子類別化， `Button` 使其運作方式類似于關閉切換：按下按鈕一次來切換按鈕，並再次將其切換為關閉。

下列 `ToggleButton` 類別衍生自 `Button` 並定義名為的新事件 `Toggled` ，以及名為的布林值屬性 `IsToggled` 。 以下是所定義的兩個屬性 Xamarin.Forms [`Switch`](xref:Xamarin.Forms.Switch) ：

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

此函式 `ToggleButton` 會將處理常式附加至 `Clicked` 事件，以便變更屬性的值 `IsToggled` 。 `OnIsToggledChanged`方法會引發 `Toggled` 事件。

方法的最後一行會 `OnIsToggledChanged` `VisualStateManager.GoToState` 使用兩個文字字串 "ToggledOn" 和 "ToggledOff" 來呼叫靜態方法。 您可以閱讀此方法的相關資訊，以及您的應用程式如何回應 [** Xamarin.Forms 視覺狀態管理員**](~/xamarin-forms/user-interface/visual-state-manager.md)文章中的視覺狀態。

由於 `ToggleButton` 進行呼叫，因此 `VisualStateManager.GoToState` 類別本身不需要包含任何額外的設備，即可根據按鈕的狀態來變更按鈕的外觀 `IsToggled` 。 這是裝載之 XAML 的責任 `ToggleButton` 。

**切換按鈕示範**頁面包含兩個實例 `ToggleButton` ，包括 `Text` `BackgroundColor` 根據視覺狀態設定按鈕、和的視覺狀態管理員標記 `TextColor` ：

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

`Toggled`事件處理常式位於程式碼後端檔案中。 它們會根據按鈕的狀態，負責設定的 `FontAttributes` 屬性 `Label` ：

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

## <a name="using-bitmaps-with-buttons"></a>使用具有按鈕的點陣圖

`Button`類別定義的 [`ImageSource`](xref:Xamarin.Forms.Button.Image) 屬性可讓您在上顯示點陣圖影像 `Button` ，不論是單獨或結合文字。 您也可以指定文字和影像的相片順序。

`ImageSource`屬性的類型為 [`ImageSource`](xref:Xamarin.Forms.ImageSource) ，表示可以從檔案、內嵌資源、URI 或資料流程載入點陣圖。

> [!NOTE]
> 雖然 `Button` 可以載入動畫 gif，但它只會顯示 gif 的第一個畫面格。

支援的每個平臺可 Xamarin.Forms 針對應用程式可能執行的各種裝置，以多種不同的圖元解析度儲存影像。 這些多個點陣圖的命名或儲存方式，可讓作業系統選擇最符合裝置的影片顯示解析度。

若是上的點陣圖 `Button` ，最好的大小通常是在32和64裝置獨立單位之間，視您想要的大小而定。 此範例中使用的映射是以48裝置獨立單位的大小為基礎。

在 iOS 專案中， **Resources** 資料夾包含此影像的三種大小：

- 儲存為./Resources/的 48-圖元正方形點陣圖 **MonkeyFace.png**
- 96-圖元的正方形點陣圖，儲存為 **/Resource/MonkeyFace@2x.png**
- 144-圖元的正方形點陣圖，儲存為 **/Resource/MonkeyFace@3x.png**

這三個位圖都會獲得**套件套件**的**組建動作**。

針對 Android 專案，所有點陣圖都有相同的名稱，但會儲存在 **Resources** 資料夾的不同子資料夾中：

- 儲存為/Resources/drawable-hDPI/的 72-圖元正方形點陣圖 **MonkeyFace.png**
- 儲存為/Resources/drawable-xhDPI/的 96-圖元正方形點陣圖 **MonkeyFace.png**
- 儲存為/Resources/drawable-xxhDPI/的 144-圖元正方形點陣圖 **MonkeyFace.png**
- 儲存為/Resources/drawable-xxxhDPI/的 192-圖元正方形點陣圖 **MonkeyFace.png**

這些會獲得**AndroidResource**的**組建動作**。

在 UWP 專案中，點陣圖可以儲存在專案中的任何位置，但通常儲存在自訂資料夾或 **資產** 現有的資料夾中。 UWP 專案包含這些點陣圖：

- 儲存為/Assets/的 48-圖元正方形點陣圖 **MonkeyFace.scale-100.png**
- 儲存為/Assets/的 96-圖元正方形點陣圖 **MonkeyFace.scale-200.png**
- 儲存為/Assets/的 192-圖元正方形點陣圖 **MonkeyFace.scale-400.png**

它們都有提供**內容**的**組建動作**。

您可以使用的屬性，指定如何 `Text` `ImageSource` 排列和屬性的相片順序 `Button` [`ContentLayout`](xref:Xamarin.Forms.Button.ContentLayout) `Button` 。 這個屬性的類型是 [`ButtonContentLayout`](xref:Xamarin.Forms.Button.ButtonContentLayout) ，它是中的內嵌類別 `Button` 。 [函式] (x： Xamarin.Forms 。ButtonContentLayout .% 23ctor (Xamarin.Forms 。ButtonContentLayout. ImagePosition，System.object) # A3 有兩個引數：

- 列舉的成員 [`ImagePosition`](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) ： `Left` 、 `Top` 、或， `Right` `Bottom` 指出點陣圖相對於文字的顯示方式。
- `double`點陣圖和文字之間間距的值。

預設值為 `Left` 和10個單位。 命名的兩個唯讀屬性 `ButtonContentLayout` [`Position`](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) ，並 [`Spacing`](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) 提供這些屬性的值。

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

在 XAML 中，您只需要指定列舉成員或間距，或以逗號分隔的任何順序來指定兩者：

```xaml
<Button Text="button text"
        ImageSource="image filename"
        ContentLayout="Right, 20" />
```

**影像按鈕示範**頁面使用 `OnPlatform` 來指定 iOS、Android 和 UWP 點陣圖檔案的不同檔案名。 如果您想要針對每個平臺使用相同的檔案名，並避免使用 `OnPlatform` ，則必須將 UWP 點陣圖儲存在專案的根目錄中。

`Button`**影像按鈕示範**頁面上的第一個會設定 `Image` 屬性，但不會設定 `Text` 屬性：

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

如果 UWP 點陣圖儲存在專案的根目錄中，則可以大幅簡化此標記：

```xaml
<Button ImageSource="MonkeyFace.png" />
```

為了避免 **ImageButtonDemo** 中有許多重複的標記， `Style` 也會定義隱含來設定 `ImageSource` 屬性。 這 `Style` 會自動套用至其他五個 `Button` 元素。 以下是完整的 XAML 檔案：

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

最後四個 `Button` 元素利用 `ContentLayout` 屬性來指定文字和點陣圖的位置和間距：

[![影像按鈕示範](button-images/ImageButtonDemo.png "影像按鈕示範")](button-images/ImageButtonDemo-Large.png#lightbox)

您現在已瞭解可處理 `Button` 事件及變更外觀的各種方式 `Button` 。

## <a name="related-links"></a>相關連結

- [System.windows.forms.datagridview.buttondemos 範例](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)
- [Button API](xref:Xamarin.Forms.Button)