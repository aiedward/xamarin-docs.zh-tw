---
title： "第5部分。 從資料系結到 MVVM 「描述：」 MVVM 模式會強制執行三個軟體層級的分隔，也就是 XAML 使用者介面，稱為 View;基礎資料，稱為模型;而在視圖和模型之間有一個媒介，稱為「ViewModel」。
assetid： xamarin ms. custom： video ms. 48B37D44-4FB1-41B2-9A5E-6D383B041F81 ms. 技術： xamarin-表單作者： davidbritch ms-chap： dabritch ms. date：10/25/2017 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="part-5-from-data-bindings-to-mvvm"></a>第 5 部分： 從資料繫結到 MVVM

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Model View ViewModel （MVVM）架構模式是以 XAML 為出發點。此模式會強制執行三個軟體層級的分隔，也就是 XAML 使用者介面，稱為 View;基礎資料，稱為模型;以及視圖與模型之間的媒介，稱為 ViewModel。View 和 ViewModel 通常會透過 XAML 檔案中定義的資料系結來連接。視圖的 BindingCoNtext 通常是 ViewModel 的實例。_

## <a name="a-simple-viewmodel"></a>簡單的 ViewModel

做為 Viewmodel 的簡介，讓我們先看一個不含一個的程式。
稍早您已瞭解如何定義新的 XML 命名空間宣告，以允許 XAML 檔案參考其他元件中的類別。 以下程式定義命名空間的 XML 命名空間宣告 `System` ：

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

程式可以使用 `x:Static` 從靜態屬性取得目前的日期和時間 `DateTime.Now` ，並將該值設定 `DateTime` 為上的 `BindingContext` `StackLayout` ：

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext`是特殊的屬性：當您在專案 `BindingContext` 上設定時，該專案的所有子系都會繼承該元素。 這表示的所有子系 `StackLayout` 都具有相同的 `BindingContext` ，而且可以包含該物件屬性的簡單系結。

在**一次性的日期時間**程式中，有兩個子系包含該值屬性的系結 `DateTime` ，但另兩個子系包含似乎遺漏系結路徑的系結。 這表示 `DateTime` 值本身會用於 `StringFormat` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="XamlSamples.OneShotDateTimePage"
             Title="One-Shot DateTime Page">

    <StackLayout BindingContext="{x:Static sys:DateTime.Now}"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">

        <Label Text="{Binding Year, StringFormat='The year is {0}'}" />
        <Label Text="{Binding StringFormat='The month is {0:MMMM}'}" />
        <Label Text="{Binding Day, StringFormat='The day is {0}'}" />
        <Label Text="{Binding StringFormat='The time is {0:T}'}" />

    </StackLayout>
</ContentPage>
```

問題在於，當第一次建立頁面時，會將日期和時間設定一次，而且永遠不會變更：

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "View Displaying Date and Time")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "View Displaying Date and Time")

XAML 檔案可以顯示一律會顯示目前時間的時鐘，但它需要一些程式碼來協助您。以 MVVM 的角度來思考時，Model 和 ViewModel 是完全以程式碼撰寫的類別。 此視圖通常是一個 XAML 檔案，它會透過資料系結來參考 ViewModel 中定義的屬性。

適當的模型並不是 ViewModel，而且不會有適當的 ViewModel。 不過，通常程式設計人員會將 ViewModel 所公開的資料類型身分調整到與特定使用者介面相關聯的資料類型。 例如，如果模型存取包含8位字元 ASCII 字串的資料庫，則 ViewModel 必須在這些字串之間轉換成 Unicode 字串，以配合使用者介面中 Unicode 的獨佔使用。

在 MVVM 的簡單範例中（如這裡所示），通常沒有任何模型，而模式只牽涉到與資料系結連結的 View 和 ViewModel。

以下是一個時鐘的 ViewModel，只有一個名為的屬性 `DateTime` ，它會 `DateTime` 每秒更新該屬性：

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    class ClockViewModel : INotifyPropertyChanged
    {
        DateTime dateTime;

        public event PropertyChangedEventHandler PropertyChanged;

        public ClockViewModel()
        {
            this.DateTime = DateTime.Now;

            Device.StartTimer(TimeSpan.FromSeconds(1), () =>
                {
                    this.DateTime = DateTime.Now;
                    return true;
                });
        }

        public DateTime DateTime
        {
            set
            {
                if (dateTime != value)
                {
                    dateTime = value;

                    if (PropertyChanged != null)
                    {
                        PropertyChanged(this, new PropertyChangedEventArgs("DateTime"));
                    }
                }
            }
            get
            {
                return dateTime;
            }
        }
    }
}
```

Viewmodel 通常會執行 `INotifyPropertyChanged` 介面，這表示類別會在 `PropertyChanged` 其其中一個屬性變更時引發事件。 中的資料系結機制會將 Xamarin.Forms 處理常式附加至這個 `PropertyChanged` 事件，以便在屬性變更時收到通知，並使用新的值來保持目標更新。

以這個 ViewModel 為基礎的時鐘可以像這樣簡單：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ClockPage"
             Title="Clock Page">

    <Label Text="{Binding DateTime, StringFormat='{0:T}'}"
           FontSize="Large"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Label.BindingContext>
            <local:ClockViewModel />
        </Label.BindingContext>
    </Label>
</ContentPage>
```

請注意，如何 `ClockViewModel` 將設定為 `BindingContext` `Label` using 屬性專案標記的。 或者，您可以將集合中的具現化， `ClockViewModel` `Resources` 並透過 `BindingContext` 標記延伸將它設定為 `StaticResource` 。 或者，程式碼後置檔案可以具現化 ViewModel。

`Binding`屬性（property）上的標記延伸會 `Text` `Label` 格式化 `DateTime` 屬性（property）。 顯示如下：

[![](data-bindings-to-mvvm-images/clock.png "View Displaying Date and Time via ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "View Displaying Date and Time via ViewModel")

您也可以 `DateTime` 使用句號分隔屬性，來存取 ViewModel 屬性的個別屬性：

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>互動式 MVVM

MVVM 通常會搭配雙向資料系結使用，以根據基礎資料模型進行互動式流覽。

以下是名為的類別， `HslViewModel` 它會將 `Color` 值轉換成 `Hue` 、 `Saturation` 和 `Luminosity` 值，反之亦然：

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    public class HslViewModel : INotifyPropertyChanged
    {
        double hue, saturation, luminosity;
        Color color;

        public event PropertyChangedEventHandler PropertyChanged;

        public double Hue
        {
            set
            {
                if (hue != value)
                {
                    hue = value;
                    OnPropertyChanged("Hue");
                    SetNewColor();
                }
            }
            get
            {
                return hue;
            }
        }

        public double Saturation
        {
            set
            {
                if (saturation != value)
                {
                    saturation = value;
                    OnPropertyChanged("Saturation");
                    SetNewColor();
                }
            }
            get
            {
                return saturation;
            }
        }

        public double Luminosity
        {
            set
            {
                if (luminosity != value)
                {
                    luminosity = value;
                    OnPropertyChanged("Luminosity");
                    SetNewColor();
                }
            }
            get
            {
                return luminosity;
            }
        }

        public Color Color
        {
            set
            {
                if (color != value)
                {
                    color = value;
                    OnPropertyChanged("Color");

                    Hue = value.Hue;
                    Saturation = value.Saturation;
                    Luminosity = value.Luminosity;
                }
            }
            get
            {
                return color;
            }
        }

        void SetNewColor()
        {
            Color = Color.FromHsla(Hue, Saturation, Luminosity);
        }

        protected virtual void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

變更、和 `Hue` `Saturation` `Luminosity` 屬性會導致 `Color` 屬性變更，而變更 `Color` 會導致其他三個屬性變更。 這看起來可能像是無限迴圈，不同之處在于類別不會叫用 `PropertyChanged` 事件，除非屬性已變更。 這會將端點放到另一個無法控制的意見反應迴圈。

下列 XAML 檔案包含， `BoxView` 其屬性系結 `Color` 至 ViewModel 的 `Color` 屬性，以及三個 `Slider` 和三個系結 `Label` 至 `Hue` 、 `Saturation` 和屬性的視圖 `Luminosity` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.HslColorScrollPage"
             Title="HSL Color Scroll Page">
    <ContentPage.BindingContext>
        <local:HslViewModel Color="Aqua" />
    </ContentPage.BindingContext>

    <StackLayout Padding="10, 0">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Hue, Mode=TwoWay}" />

        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Saturation, Mode=TwoWay}" />

        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Luminosity, Mode=TwoWay}" />
    </StackLayout>
</ContentPage>
```

每個上的系結 `Label` 都是預設值 `OneWay` 。 它只需要顯示值。 但每個上的系結 `Slider` 都是 `TwoWay` 。 這可讓 `Slider` 從 ViewModel 初始化。 請注意， `Color` `Aqua` 當 ViewModel 具現化時，屬性會設定為。 但是中的變更 `Slider` 也需要為 ViewModel 中的屬性設定新的值，然後計算新的色彩。

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "MVVM using Two-Way Data Bindings")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM using Two-Way Data Bindings")

## <a name="commanding-with-viewmodels"></a>使用 Viewmodel 命令

在許多情況下，MVVM 模式僅限於資料項目目的操作： ViewModel 中 View parallel data 物件的使用者介面物件。

不過，有時候視圖必須包含可在 ViewModel 中觸發各種動作的按鈕。 但是，ViewModel 不能包含 `Clicked` 按鈕的處理常式，因為這會將 ViewModel 系結至特定的使用者介面範例。

若要讓 Viewmodel 更獨立于特定使用者介面物件，但仍然允許在 ViewModel 內呼叫方法，則會有*命令*介面。 中的下列元素支援此命令介面 Xamarin.Forms ：

- `Button`
- `MenuItem`
- `ToolbarItem`
- `SearchBar`
- `TextCell`（因此也會 `ImageCell` ）
- `ListView`
- `TapGestureRecognizer`

除了 `SearchBar` 和 `ListView` 元素之外，這些元素會定義兩個屬性：

- `Command`類型為`System.Windows.Input.ICommand`
- `CommandParameter`類型為`Object`

會 `SearchBar` 定義 `SearchCommand` 和 `SearchCommandParameter` 屬性，而則會 `ListView` 定義 `RefreshCommand` 類型的屬性 `ICommand` 。

`ICommand`介面會定義兩個方法和一個事件：

- `void Execute(object arg)`
- `bool CanExecute(object arg)`
- `event EventHandler CanExecuteChanged`

ViewModel 可以定義類型的屬性 `ICommand` 。 接著，您可以將這些屬性系結至 `Command` 每個或其他專案的屬性，或系結至 `Button` 執行此介面的自訂視圖。 您可以選擇性地設定 `CommandParameter` 屬性，以識別系結 `Button` 至這個 ViewModel 屬性的個別物件（或其他元素）。 就內部而言， `Button` `Execute` 每當使用者點擊時，就會呼叫方法 `Button` ，傳遞至 `Execute` 方法的 `CommandParameter` 。

`CanExecute`方法和 `CanExecuteChanged` 事件適用于 `Button` 可能目前無效之點的情況，在此情況下， `Button` 應該自行停用。 `Button`會在 `CanExecute` `Command` 第一次設定屬性時呼叫，並在每次 `CanExecuteChanged` 引發事件時呼叫。 如果 `CanExecute` 傳回 `false` ，則 `Button` 會停用本身，而不會產生 `Execute` 呼叫。

如需在 Viewmodel 中加入命令的說明， Xamarin.Forms 會定義兩個實作為的類別：和， `ICommand` `Command` `Command<T>` 其中 `T` 是和的引數類型 `Execute` `CanExecute` 。 這兩個類別會定義數個函式，再加上 `ChangeCanExecute` ViewModel 可以呼叫的方法，以強制 `Command` 物件引發 `CanExecuteChanged` 事件。

以下是適用于輸入電話號碼的簡單鍵盤按鍵 ViewModel。 請注意，在此函式 `Execute` 中，和 `CanExecute` 方法會定義為 lambda 函式：

```csharp
using System;
using System.ComponentModel;
using System.Windows.Input;
using Xamarin.Forms;

namespace XamlSamples
{
    class KeypadViewModel : INotifyPropertyChanged
    {
        string inputString = "";
        string displayText = "";
        char[] specialChars = { '*', '#' };

        public event PropertyChangedEventHandler PropertyChanged;

        // Constructor
        public KeypadViewModel()
        {
            AddCharCommand = new Command<string>((key) =>
                {
                    // Add the key to the input string.
                    InputString += key;
                });

            DeleteCharCommand = new Command(() =>
                {
                    // Strip a character from the input string.
                    InputString = InputString.Substring(0, InputString.Length - 1);
                },
                () =>
                {
                    // Return true if there's something to delete.
                    return InputString.Length > 0;
                });
        }

        // Public properties
        public string InputString
        {
            protected set
            {
                if (inputString != value)
                {
                    inputString = value;
                    OnPropertyChanged("InputString");
                    DisplayText = FormatText(inputString);

                    // Perhaps the delete button must be enabled/disabled.
                    ((Command)DeleteCharCommand).ChangeCanExecute();
                }
            }

            get { return inputString; }
        }

        public string DisplayText
        {
            protected set
            {
                if (displayText != value)
                {
                    displayText = value;
                    OnPropertyChanged("DisplayText");
                }
            }
            get { return displayText; }
        }

        // ICommand implementations
        public ICommand AddCharCommand { protected set; get; }

        public ICommand DeleteCharCommand { protected set; get; }

        string FormatText(string str)
        {
            bool hasNonNumbers = str.IndexOfAny(specialChars) != -1;
            string formatted = str;

            if (hasNonNumbers || str.Length < 4 || str.Length > 10)
            {
            }
            else if (str.Length < 8)
            {
                formatted = String.Format("{0}-{1}",
                                          str.Substring(0, 3),
                                          str.Substring(3));
            }
            else
            {
                formatted = String.Format("({0}) {1}-{2}",
                                          str.Substring(0, 3),
                                          str.Substring(3, 3),
                                          str.Substring(6));
            }
            return formatted;
        }

        protected void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

此 ViewModel 假設屬性系結 `AddCharCommand` 至 `Command` 數個按鈕的屬性（或具有命令介面的任何其他專案），其中每個都是由所識別 `CommandParameter` 。 這些按鈕會將字元新增至 `InputString` 屬性，然後將其格式化為屬性的電話號碼 `DisplayText` 。

另外還有一個名為之型別的第二個屬性 `ICommand` `DeleteCharCommand` 。 這會系結至 [上一頁] 按鈕，但如果沒有要刪除的字元，就應該停用該按鈕。

以下的小鍵盤不像視覺效果複雜。 相反地，標記已縮減為最小值，以更清楚地示範使用命令介面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.KeypadPage"
             Title="Keypad Page">

    <Grid HorizontalOptions="Center"
          VerticalOptions="Center">
        <Grid.BindingContext>
            <local:KeypadViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
        </Grid.ColumnDefinitions>

        <!-- Internal Grid for top row of items -->
        <Grid Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="Auto" />
            </Grid.ColumnDefinitions>

            <Frame Grid.Column="0"
                   OutlineColor="Accent">
                <Label Text="{Binding DisplayText}" />
            </Frame>

            <Button Text="&#x21E6;"
                    Command="{Binding DeleteCharCommand}"
                    Grid.Column="1"
                    BorderWidth="0" />
        </Grid>

        <Button Text="1"
                Command="{Binding AddCharCommand}"
                CommandParameter="1"
                Grid.Row="1" Grid.Column="0" />

        <Button Text="2"
                Command="{Binding AddCharCommand}"
                CommandParameter="2"
                Grid.Row="1" Grid.Column="1" />

        <Button Text="3"
                Command="{Binding AddCharCommand}"
                CommandParameter="3"
                Grid.Row="1" Grid.Column="2" />

        <Button Text="4"
                Command="{Binding AddCharCommand}"
                CommandParameter="4"
                Grid.Row="2" Grid.Column="0" />

        <Button Text="5"
                Command="{Binding AddCharCommand}"
                CommandParameter="5"
                Grid.Row="2" Grid.Column="1" />

        <Button Text="6"
                Command="{Binding AddCharCommand}"
                CommandParameter="6"
                Grid.Row="2" Grid.Column="2" />

        <Button Text="7"
                Command="{Binding AddCharCommand}"
                CommandParameter="7"
                Grid.Row="3" Grid.Column="0" />

        <Button Text="8"
                Command="{Binding AddCharCommand}"
                CommandParameter="8"
                Grid.Row="3" Grid.Column="1" />

        <Button Text="9"
                Command="{Binding AddCharCommand}"
                CommandParameter="9"
                Grid.Row="3" Grid.Column="2" />

        <Button Text="*"
                Command="{Binding AddCharCommand}"
                CommandParameter="*"
                Grid.Row="4" Grid.Column="0" />

        <Button Text="0"
                Command="{Binding AddCharCommand}"
                CommandParameter="0"
                Grid.Row="4" Grid.Column="1" />

        <Button Text="#"
                Command="{Binding AddCharCommand}"
                CommandParameter="#"
                Grid.Row="4" Grid.Column="2" />
    </Grid>
</ContentPage>
```

`Command` `Button` 出現在這個標記中第一個的屬性會系結至 `DeleteCharCommand` ; 其餘的會系結至，其 `AddCharCommand` 與 `CommandParameter` 臉部上出現的字元相同 `Button` 。 以下是作用中的程式：

[![](data-bindings-to-mvvm-images/keypad.png "Calculator using MVVM and Commands")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "Calculator using MVVM and Commands")

### <a name="invoking-asynchronous-methods"></a>叫用非同步方法

命令也可以叫用非同步方法。 `async` `await` 在指定方法時，可以使用和關鍵字來達成此目的 `Execute` ：

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

這表示 `DownloadAsync` 方法是 `Task` ，而且應該等候：

```csharp
async Task DownloadAsync ()
{
    await Task.Run (() => Download ());
}

void Download ()
{
    ...
}
```

## <a name="implementing-a-navigation-menu"></a>執行導覽功能表

包含這一系列文章中所有原始程式碼的[XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)程式，都會使用 ViewModel 作為首頁。 這個 ViewModel 是具有三個屬性（名為、和）的簡短類別定義， `Type` `Title` `Description` 其中包含每個範例頁面的類型、標題和簡短描述。 此外，ViewModel 會定義名為的靜態屬性 `All` ，這是程式中所有頁面的集合：

```csharp
public class PageDataViewModel
{
    public PageDataViewModel(Type type, string title, string description)
    {
        Type = type;
        Title = title;
        Description = description;
    }

    public Type Type { private set; get; }

    public string Title { private set; get; }

    public string Description { private set; get; }

    static PageDataViewModel()
    {
        All = new List<PageDataViewModel>
        {
            // Part 1. Getting Started with XAML
            new PageDataViewModel(typeof(HelloXamlPage), "Hello, XAML",
                                  "Display a Label with many properties set"),

            new PageDataViewModel(typeof(XamlPlusCodePage), "XAML + Code",
                                  "Interact with a Slider and Button"),

            // Part 2. Essential XAML Syntax
            new PageDataViewModel(typeof(GridDemoPage), "Grid Demo",
                                  "Explore XAML syntax with the Grid"),

            new PageDataViewModel(typeof(AbsoluteDemoPage), "Absolute Demo",
                                  "Explore XAML syntax with AbsoluteLayout"),

            // Part 3. XAML Markup Extensions
            new PageDataViewModel(typeof(SharedResourcesPage), "Shared Resources",
                                  "Using resource dictionaries to share resources"),

            new PageDataViewModel(typeof(StaticConstantsPage), "Static Constants",
                                  "Using the x:Static markup extensions"),

            new PageDataViewModel(typeof(RelativeLayoutPage), "Relative Layout",
                                  "Explore XAML markup extensions"),

            // Part 4. Data Binding Basics
            new PageDataViewModel(typeof(SliderBindingsPage), "Slider Bindings",
                                  "Bind properties of two views on the page"),

            new PageDataViewModel(typeof(SliderTransformsPage), "Slider Transforms",
                                  "Use Sliders with reverse bindings"),

            new PageDataViewModel(typeof(ListViewDemoPage), "ListView Demo",
                                  "Use a ListView with data bindings"),

            // Part 5. From Data Bindings to MVVM
            new PageDataViewModel(typeof(OneShotDateTimePage), "One-Shot DateTime",
                                  "Obtain the current DateTime and display it"),

            new PageDataViewModel(typeof(ClockPage), "Clock",
                                  "Dynamically display the current time"),

            new PageDataViewModel(typeof(HslColorScrollPage), "HSL Color Scroll",
                                  "Use a view model to select HSL colors"),

            new PageDataViewModel(typeof(KeypadPage), "Keypad",
                                  "Use a view model for numeric keypad logic")
        };
    }

    public static IList<PageDataViewModel> All { private set; get; }
}
```

的 XAML 檔案 `MainPage` 會定義， `ListBox` 其 `ItemsSource` 屬性會設定為該 `All` 屬性，其中包含 `TextCell` 用於顯示 `Title` `Description` 每個頁面之和屬性的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage"
             Padding="5, 0"
             Title="XAML Samples">

    <ListView ItemsSource="{x:Static local:PageDataViewModel.All}"
              ItemSelected="OnListViewItemSelected">
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding Title}"
                          Detail="{Binding Description}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

頁面會顯示在可滾動清單中：

[![](data-bindings-to-mvvm-images/mainpage.png "Scrollable list of pages")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "Scrollable list of pages")

當使用者選取專案時，就會觸發程式碼後置檔案中的處理常式。 處理常式會將的 `SelectedItem` 屬性設定 `ListBox` 回 `null` ，然後將選取的頁面具現化並加以流覽：

```csharp
private async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
{
    (sender as ListView).SelectedItem = null;

    if (args.SelectedItem != null)
    {
        PageDataViewModel pageData = args.SelectedItem as PageDataViewModel;
        Page page = (Page)Activator.CreateInstance(pageData.Type);
        await Navigation.PushAsync(page);
    }
}
```

## <a name="video"></a>影片

> [!VIDEO https://youtube.com/embed/DYRLcqG2BAY]

**Xamarin 演進2016：使用和 Prism 的 MVVM 變得簡單 Xamarin.Forms**

## <a name="summary"></a>摘要

XAML 是一種功能強大的工具，可在應用程式中定義使用者介面 Xamarin.Forms ，特別是在使用資料系結和 MVVM 時。 結果是全新、精緻且可能 toolable 的使用者介面標記法，其中包含程式碼中的所有背景支援。

## <a name="related-links"></a>相關連結

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第1部分。使用 XAML 消費者入門](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第2部分。基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第3部分。XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第4部分。資料系結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)

## <a name="related-videos"></a>相關影片

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-MVVM-with-XAML-6-of-11/player]

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-Navigation-with-XAML-7-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
