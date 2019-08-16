---
title: 第 5 部分。 從資料繫結至 MVVM
description: MVVM 模式會強制執行三個軟體層之間的區隔，XAML 使用者介面，稱為檢視;基礎資料，稱為模型;而檢視與模型之間的媒介稱為 ViewModel。
ms.prod: xamarin
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: f81a9d232e1702d112e837a80d35403162e3adca
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529337"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>第 5 部分。 從資料繫結至 MVVM

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_記住，使用 XAML 發明的 Model View ViewModel (MVVM) 架構模式。此模式會強制執行三個軟體層之間的區隔，XAML 使用者介面，稱為檢視;基礎資料，稱為模型;而檢視與模型之間的媒介稱為 ViewModel。檢視和 ViewModel 通常是透過 XAML 檔案中定義的資料繫結連接。檢視 BindingContext 通常是 ViewModel 的執行個體。_

## <a name="a-simple-viewmodel"></a>簡單的 ViewModel

Viewmodel 的簡介，讓我們先看看其中一個情況下的程式。
先前您已看到如何定義新的 XML 命名空間宣告來允許其他組件中的參考類別的 XAML 檔案。 以下是定義的 XML 命名空間宣告程式`System`命名空間：

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

該程式可以使用`x:Static`若要取得目前的日期和時間從靜態`DateTime.Now`屬性並加以設定`DateTime`值`BindingContext`上`StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext`是非常特殊的屬性:當您在專案`BindingContext`上設定時, 該專案的所有子系都會繼承此專案。 這表示所有子系`StackLayout`具有此相同`BindingContext`，而且可以包含簡單的繫結，該物件的屬性。

在  **One-Shot DateTime**程式中，子系的兩個包含該屬性的繫結`DateTime`值，但兩個其他子系包含繫結似乎遺漏繫結路徑。 這表示`DateTime`本身的值會用於`StringFormat`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
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

當然，有一個大問題是，日期和時間已設定時的頁面第一次建置後，而且永遠不會變更：

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "檢視，以顯示日期和時間")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "檢視，以顯示日期和時間")

XAML 檔案可以顯示時鐘，一律會顯示目前的時間，但需要一些程式碼來助一臂之力。當 MVVM、 模型和 ViewModel 等方面的想法是完全以程式碼撰寫的類別。 檢視經常是參考中的 ViewModel 定義透過資料繫結屬性的 XAML 檔案。

適當的模型並不知道 ViewModel，並適當的 ViewModel 是非持續性的檢視。 不過，通常是程式設計師可打造 ViewModel 與特定的使用者介面相關聯的資料型別所公開的資料類型。 比方說，如果模型存取的資料庫包含 8 位元的字元的 ASCII 字串，ViewModel 會需要將兩個字串為 Unicode 字串，以容納獨佔使用在使用者介面中的 Unicode 之間轉換。

在簡單範例中的 MVVM （例如，如下所示），通常沒有模型，模式涉及只檢視和 ViewModel 連結以資料繫結。

以下是只是名為的單一屬性使用在時鐘的 ViewModel `DateTime`，這會更新，但`DateTime`屬性每秒：

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

通常實作 Viewmodel`INotifyPropertyChanged`介面，這表示類別就會引發`PropertyChanged`其中一個屬性變更時的事件。 在 Xamarin.Forms 中的資料繫結機制會將處理常式附加至這個`PropertyChanged`事件，所以可以在屬性變更時通知並持續更新為新值的目標。

根據此 ViewModel 時鐘可以是這麼簡單：

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

請注意如何`ClockViewModel`設定為`BindingContext`的`Label`使用屬性項目標記。 或者，您可以具現化`ClockViewModel`中`Resources`集合並將它設定為`BindingContext`透過`StaticResource`標記延伸。 或者，程式碼後置檔案可以具現化 ViewModel。

`Binding`上的標記延伸`Text`屬性`Label`格式`DateTime`屬性。 以下是顯示：

[![](data-bindings-to-mvvm-images/clock.png "檢視，以顯示日期和時間透過 ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "顯示日期和時間透過 ViewModel 的檢視")

您也可存取的個別屬性`DateTime`使用句號分隔屬性 ViewModel 屬性：

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>互動式 MVVM

MVVM 經常搭配雙向資料繫結為基礎的資料模型為基礎的互動式檢視。

以下是名為的類別`HslViewModel`可將轉換`Color`值插入`Hue`， `Saturation`，和`Luminosity`值，反之亦然：

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

變更為`Hue`， `Saturation`，並`Luminosity`屬性的原因`Color`屬性變更和變更`Color`會導致其他三個屬性，來變更。 這看起來像是無限迴圈，不同之處在於不叫用類別`PropertyChanged`事件除非屬性實際上已變更。 這麼一來否則無法控制的意見反應迴圈。

下列 XAML 檔案中包含`BoxView`其`Color`屬性的繫結至`Color`ViewModel，與三個屬性`Slider`和三個`Label`檢視繫結至`Hue`， `Saturation`，以及`Luminosity`屬性：

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

在每個繫結`Label`是預設值`OneWay`。 它只需要顯示的值。 但在每個繫結`Slider`是`TwoWay`。 這可讓`Slider`從 ViewModel 初始化。 請注意，`Color`屬性設定為`Aqua`ViewModel 具現化時。 但變更`Slider`也需要在 ViewModel，接著再計算新的色彩設定屬性的新值。

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "使用雙向資料繫結的 MVVM")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM 使用雙向資料繫結")

## <a name="commanding-with-viewmodels"></a>Viewmodel 的命令執行

在許多情況下, MVVM 模式僅限於運算元據項:ViewModel 中 View parallel data 物件的使用者介面物件。

不過，有時候必須檢視包含觸發程序中的 ViewModel 的各種動作的按鈕。 但不是能包含 ViewModel`Clicked`按鈕處理常式因為，會將繫結至特定的使用者介面典範 ViewModel。

若要允許更獨立於特定的使用者介面物件，但仍然允許在 ViewModel，要呼叫方法的 Viewmodel*命令*介面存在。 在 Xamarin.Forms 中的下列項目支援此命令介面：

- `Button`
- `MenuItem`
- `ToolbarItem`
- `SearchBar`
- `TextCell` (因此也`ImageCell`)
- `ListView`
- `TapGestureRecognizer`

除了`SearchBar`和`ListView`項目，這些項目會定義兩個屬性：

- `Command` 型別  `System.Windows.Input.ICommand`
- `CommandParameter` 型別  `Object`

`SearchBar`定義`SearchCommand`並`SearchCommandParameter`屬性，而`ListView`定義`RefreshCommand`型別的屬性`ICommand`。

`ICommand`介面會定義兩個方法和一個事件：

- `void Execute(object arg)`
- `bool CanExecute(object arg)`
- `event EventHandler CanExecuteChanged`

ViewModel 可以定義類型的屬性`ICommand`。 您接著可以繫結至這些屬性`Command`每個屬性`Button`或其他項目或可能是實作這個介面的自訂檢視。 您可以選擇性地設定`CommandParameter`屬性來識別個別`Button`物件 （或其他項目） 的繫結至這個 ViewModel 屬性。 就內部而言，`Button`呼叫`Execute`方法，每當使用者點選`Button`，以傳遞`Execute`方法及其`CommandParameter`。

`CanExecute`方法和`CanExecuteChanged`情況下用其中`Button`點選 在此情況下可能是目前無效，`Button`應該停用。 `Button`呼叫`CanExecute`時`Command`會先設定屬性，每當`CanExecuteChanged`引發事件。 如果`CanExecute`會傳回`false`，則`Button`停用本身並不會產生`Execute`呼叫。

Xamarin.Forms 可定義實作的兩個類別新增至您的 Viewmodel 命令的說明，請`ICommand`:`Command`並`Command<T>`所在`T`是引數的型別`Execute`和`CanExecute`。 這兩個類別定義數個建構函式加上`ChangeCanExecute`ViewModel 可以呼叫來強制方法`Command`物件來引發`CanExecuteChanged`事件。

以下是簡單的字鍵台，以供輸入電話號碼的 ViewModel。 請注意，`Execute`和`CanExecute`方法定義為 lambda 函式的權限的建構函式：

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

此 ViewModel 假設`AddCharCommand`屬性的繫結至`Command`屬性的數個按鈕 （或任何其他項目具有命令介面），其中每一個由`CommandParameter`。 這些按鈕加入到字元`InputString`屬性，然後格式化的電話號碼為`DisplayText`屬性。

另外還有第二個類型的屬性`ICommand`名為`DeleteCharCommand`。 這繫結到後間距 按鈕，但應該停用 按鈕，如果沒有任何要刪除的字元。

因為它可能會以下的數字鍵台不是以視覺化方式複雜。 相反地，標記已減少為最小值，以更清楚地示範的命令介面：

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

`Command`屬性的第一個`Button`出現在此標記會繫結至`DeleteCharCommand`; 其餘部分會繫結至`AddCharCommand`具有`CommandParameter`也就是相同的字元，會出現在`Button`臉部。 以下是作用中的程式：

[![](data-bindings-to-mvvm-images/keypad.png "使用 MVVM 和命令的計算機")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "使用 MVVM 和命令的計算機")

### <a name="invoking-asynchronous-methods"></a>叫用非同步方法

命令也可以叫用非同步方法。 這利用來達成`async`及`await`關鍵字指定時`Execute`方法：

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

這表示`DownloadAsync`方法是`Task`且應該等候：

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

## <a name="implementing-a-navigation-menu"></a>實作導覽功能表

[XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)包含在這一系列的文章中的所有原始程式碼的程式會使用 ViewModel 其首頁。 此 ViewModel 是簡短的類別具有名為的三個屬性的定義`Type`， `Title`，和`Description`包含的範例網頁、 標題和簡短描述每個型別。 此外，ViewModel 定義靜態屬性，名為`All`也就是在程式中的所有分頁的集合：

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

XAML 檔案`MainPage`定義`ListBox`其`ItemsSource`屬性設定為可`All`屬性，其中包含`TextCell`顯示`Title`並`Description`每個頁面的屬性：

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

頁面會顯示可捲動的清單：

[![](data-bindings-to-mvvm-images/mainpage.png "可捲動清單中的頁數")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "可捲動的頁面清單")

當使用者選取項目，就會觸發的程式碼後置檔案中的處理常式。 處理常式集合`SelectedItem`的屬性`ListBox`回到`null`然後具現化選取的頁面，並瀏覽至它：

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

## <a name="video"></a>視訊

> [!VIDEO https://youtube.com/embed/DYRLcqG2BAY]

**Xamarin 演進 2016:MVVM 透過 Xamarin 和 Prism 變得簡單**

## <a name="summary"></a>總結

XAML 是功能強大的工具，來定義使用者介面，在 Xamarin.Forms 應用程式，特別是當資料繫結以及 MVVM 的使用。 結果是具有程式碼中的所有背景支援的使用者介面的全新、 優雅且可能可表示法。


## <a name="related-links"></a>相關連結

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第 1 部分：開始使用 XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 2 部分：基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 3 部分：XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 4 部分：資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
