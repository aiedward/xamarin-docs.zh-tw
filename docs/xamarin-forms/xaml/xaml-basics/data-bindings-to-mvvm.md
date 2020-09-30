---
title: 第 5 部分： 從資料繫結到 MVVM
description: MVVM 模式會強制將三個軟體層（稱為 View 的 XAML 使用者介面）分開：基礎資料，稱為模型;以及視圖與模型之間的媒介，稱為 ViewModel。
ms.prod: xamarin
ms.custom: video
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8cb59738519af933e509ebf63a923e573667941e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562908"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>第 5 部分： 從資料繫結到 MVVM

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_模型視圖 ViewModel (MVVM) 架構模式是以 XAML 的形式來建立。此模式會強制將三個軟體層（稱為 View 的 XAML 使用者介面）分開：基礎資料，稱為模型;以及視圖與模型之間的媒介，稱為 ViewModel。視圖和 ViewModel 通常會透過 XAML 檔案中定義的資料系結進行連接。視圖的 BindingCoNtext 通常是 ViewModel 的實例。_

## <a name="a-simple-viewmodel"></a>簡單的 ViewModel

作為 Viewmodel 的簡介，讓我們先看看沒有人的程式。
稍早，您已看到如何定義新的 XML 命名空間宣告，以允許 XAML 檔案參考其他元件中的類別。 以下是定義命名空間之 XML 命名空間宣告的程式 `System` ：

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

此程式可以用 `x:Static` 來從靜態屬性取得目前的日期和時間 `DateTime.Now` ，並將該值設定 `DateTime` 為 `BindingContext` 上的 `StackLayout` ：

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` 是特殊屬性：當您在專案上設定時 `BindingContext` ，該專案的所有子系會繼承此屬性。 這表示的所有子系 `StackLayout` 都有相同的 `BindingContext` ，而且可以包含該物件屬性的簡單系結。

在 **一次性的日期時間** 程式中，有兩個子系包含該值的屬性系結 `DateTime` ，但其他兩個子系包含似乎遺漏系結路徑的系結。 這表示 `DateTime` 值本身會用於 `StringFormat` ：

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

問題是在第一次建立頁面時，日期和時間會設定一次，而且永遠不會變更：

[![顯示日期和時間的視圖](data-bindings-to-mvvm-images/oneshotdatetime.png)](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "顯示日期和時間的視圖")

XAML 檔案可以顯示永遠顯示目前時間的時鐘，但需要一些程式碼來協助您。在 MVVM 的考慮下，模型和 ViewModel 是完全以程式碼撰寫的類別。 此視圖通常是透過資料系結參考 ViewModel 中定義之屬性的 XAML 檔案。

ViewModel 不會有適當的模型，而且不會有適當的 ViewModel。 不過，程式設計人員通常會將 ViewModel 公開的資料類型調適至與特定使用者介面相關聯的資料類型。 例如，如果模型存取包含8位字元 ASCII 字串的資料庫，則 ViewModel 需要在這些字串之間轉換成 Unicode 字串，以配合使用者介面中 Unicode 的獨佔使用。

在 MVVM (的簡單範例中，例如此處顯示的) ，通常沒有任何模型，而且模式只牽涉到與資料系結連結的 View 和 ViewModel。

以下是一個時鐘的 ViewModel，只有一個名為的單一屬性 `DateTime` ，它會 `DateTime` 每秒更新該屬性：

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

Viewmodel 通常會實作為 `INotifyPropertyChanged` 介面，這表示 `PropertyChanged` 每當類別的其中一個屬性變更時，該類別就會引發事件。 中的資料系結機制 Xamarin.Forms 會將處理常式附加至這個 `PropertyChanged` 事件，以便在屬性變更時收到通知，並以新值更新目標。

以這個 ViewModel 為依據的時鐘可以像這樣簡單：

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

請注意，如何 `ClockViewModel` 設定為 `BindingContext` `Label` 使用屬性元素標記的。 或者，您可以在集合中具現化， `ClockViewModel` `Resources` 並透過 `BindingContext` 標記延伸將它設定為 `StaticResource` 。 或者，程式碼後端檔案可以具現化 ViewModel。

`Binding`屬性（property）上的標記延伸會 `Text` `Label` 格式化 `DateTime` 屬性（property）。 顯示如下：

[![透過 ViewModel 顯示日期和時間的視圖](data-bindings-to-mvvm-images/clock.png)](data-bindings-to-mvvm-images/clock-large.png#lightbox "透過 ViewModel 顯示日期和時間的視圖")

您也可以藉 `DateTime` 由分隔屬性與句點來存取 ViewModel 屬性的個別屬性：

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>互動式 MVVM

MVVM 通常會根據基礎資料模型，與互動式視圖的雙向資料系結搭配使用。

以下是名為的類別， `HslViewModel` 可將 `Color` 值轉換為 `Hue` 、 `Saturation` 和 `Luminosity` 值，反之亦然：

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

變更 `Hue` 、 `Saturation` 和 `Luminosity` 屬性會導致 `Color` 屬性變更，而變更 `Color` 會導致其他三個屬性變更。 這看起來可能像無限迴圈，但除非屬性已變更，否則類別不會叫用 `PropertyChanged` 事件。 這會使無法控制的意見反應迴圈結束。

下列 XAML 檔案包含 `BoxView` ，其屬性系結 `Color` 至 ViewModel 的 `Color` 屬性，以及三個 `Slider` 和三個系結 `Label` 至 `Hue` 、 `Saturation` 和屬性的視圖 `Luminosity` ：

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

每個的系結 `Label` 都是預設值 `OneWay` 。 它只需要顯示值。 但每一個系結 `Slider` 都是 `TwoWay` 。 這可讓 `Slider` 從 ViewModel 初始化。 請注意， `Color` `Aqua` 當 ViewModel 具現化時，屬性會設定為。 但是，的變更 `Slider` 也需要為 ViewModel 中的屬性設定新的值，然後再計算新的色彩。

[![使用雙向資料系結的 MVVM](data-bindings-to-mvvm-images/hslcolorscroll.png)](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "使用雙向資料系結的 MVVM")

## <a name="commanding-with-viewmodels"></a>使用 Viewmodel 命令

在許多情況下，MVVM 模式僅限於運算元據項： ViewModel 中 View parallel data 物件的使用者介面物件。

不過，有時此視圖必須包含可在 ViewModel 中觸發各種動作的按鈕。 但是，ViewModel 不能包含 `Clicked` 按鈕的處理常式，因為這會將 ViewModel 系結至特定的使用者介面架構。

若要讓 Viewmodel 更獨立于特定的使用者介面物件，但仍允許在 ViewModel 內呼叫方法，則會有 *命令* 介面存在。 下列元素支援這個命令介面 Xamarin.Forms ：

- `Button`
- `MenuItem`
- `ToolbarItem`
- `SearchBar`
- `TextCell` (，因此也 `ImageCell`) 
- `ListView`
- `TapGestureRecognizer`

除了 `SearchBar` 和 `ListView` 元素之外，這些元素也會定義兩個屬性：

- `Command` 型別為  `System.Windows.Input.ICommand`
- `CommandParameter` 型別為  `Object`

`SearchBar`定義 `SearchCommand` 和 `SearchCommandParameter` 屬性（property），而 `ListView` 定義 `RefreshCommand` 型別的屬性（property） `ICommand` 。

`ICommand`介面會定義兩個方法和一個事件：

- `void Execute(object arg)`
- `bool CanExecute(object arg)`
- `event EventHandler CanExecuteChanged`

ViewModel 可以定義型別的屬性 `ICommand` 。 然後，您可以將這些屬性系結至 `Command` 每個或其他專案的屬性 `Button` ，或可能是實作為此介面的自訂視圖。 您可以選擇性地設定 `CommandParameter` 屬性，以識別系結 `Button` 至這個 ViewModel 屬性 (或其他元素) 的個別物件。 就內部而言， `Button` `Execute` 每當使用者按下時，就會呼叫方法 `Button` ，並傳遞給 `Execute` 方法 `CommandParameter` 。

`CanExecute`方法和 `CanExecuteChanged` 事件是用於點一下 `Button` 可能目前不正確案例，在此情況下， `Button` 應該自行停用。 `Button` `CanExecute` `Command` 第一次設定屬性時，以及每次 `CanExecuteChanged` 引發事件時呼叫。 如果 `CanExecute` 傳回 `false` ，則 `Button` 會自行停用，而不會產生 `Execute` 呼叫。

如需將命令新增至 Viewmodel 的說明，請 Xamarin.Forms 定義兩個實作為的類別 `ICommand` ： `Command` 和， `Command<T>` 其中 `T` 是與的引數類型 `Execute` `CanExecute` 。 這兩個類別定義了數個函式，再加上 `ChangeCanExecute` ViewModel 可呼叫以強制 `Command` 物件引發 `CanExecuteChanged` 事件的方法。

以下是適用于輸入電話號碼之簡單鍵盤的 ViewModel。 請注意， `Execute` 和方法在函式 `CanExecute` 中會直接定義為 lambda 函式：

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

這個 ViewModel 會假設屬性系結 `AddCharCommand` 至 `Command` 多個按鈕的屬性 (或有命令介面) 的任何其他內容，每一個都是由所識別 `CommandParameter` 。 這些按鈕 `InputString` 會在屬性中新增字元，然後將其格式化為屬性的電話號碼 `DisplayText` 。

另外還有一個名為之型別的第二個屬性 `ICommand` `DeleteCharCommand` 。 這會系結至反向間距按鈕，但如果沒有要刪除的字元，則應該停用該按鈕。

下列小數位台不像視覺效果一樣複雜。 相反地，標記已縮減為最小值，以示範更清楚的命令介面使用：

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

`Command`此標記中第一個出現的屬性會系結至，而其餘的會系結 `Button` `DeleteCharCommand` 至 `AddCharCommand` 與 `CommandParameter` 出現在臉部上的字元相同的 `Button` 。 以下是實際執行的程式：

[![使用 MVVM 和命令的計算機](data-bindings-to-mvvm-images/keypad.png)](data-bindings-to-mvvm-images/keypad-large.png#lightbox "使用 MVVM 和命令的計算機")

### <a name="invoking-asynchronous-methods"></a>叫用非同步方法

命令也可以叫用非同步方法。 `async` `await` 在指定方法時使用和關鍵字可達成此目的 `Execute` ：

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

這表示該 `DownloadAsync` 方法是 `Task` ，且應該等候：

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

包含這一系列文章中所有原始程式碼的 [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples) 程式會使用 ViewModel 做為其首頁。 這個 ViewModel 是具有三個名為、和的屬性的簡短類別定義 `Type` `Title` `Description` ，其中包含每個範例頁面的類型、標題和簡短描述。 此外，ViewModel 會定義名為的靜態屬性， `All` 它是程式中所有頁面的集合：

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

的 XAML 檔案 `MainPage` `ListBox` 會定義其 `ItemsSource` 屬性設定為該屬性的 `All` ，其中包含 `TextCell` 用於顯示 `Title` `Description` 每個頁面的和屬性：

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

這些頁面會顯示在可滾動清單中：

[![可滾動的頁面清單](data-bindings-to-mvvm-images/mainpage.png)](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "可滾動的頁面清單")

當使用者選取專案時，就會觸發程式碼後端檔案中的處理常式。 處理常式會將的 `SelectedItem` 屬性設定 `ListBox` 為 `null` ，然後將選取的頁面具現化，並加以導覽：

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

**Xamarin 演進2016： MVVM 透過和 Prism 變得簡單 Xamarin.Forms**

## <a name="summary"></a>摘要

XAML 是一種功能強大的工具，可用來在應用程式中定義使用者介面 Xamarin.Forms ，尤其是在使用資料系結和 MVVM 時。 結果是使用者介面的清晰、簡潔且可能 toolable 的標記法，並在程式碼中提供所有背景支援。

## <a name="related-links"></a>相關連結

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第1部分。使用 XAML 消費者入門](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第2部分。基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第3部分。XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第4部分。資料系結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)

## <a name="related-videos"></a>相關影片

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-MVVM-with-XAML-6-of-11/player]

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-Navigation-with-XAML-7-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]