---
title: 第 5 部分。 從資料繫結至 MVVM
description: MVVM 模式會強制執行三個軟體層之間的區隔 — XAML 使用者介面，稱為檢視;基礎資料，稱為模型;而在檢視和模型之間的媒介稱為 ViewModel。
ms.prod: xamarin
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: e83f8a585c4badc31bffaea53bb2f183e7b11fc9
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268858"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>第 5 部分。 從資料繫結至 MVVM

_請注意，使用 XAML 發明模型-檢視-ViewModel (MVVM) 架構模式。此模式會強制執行三個軟體層之間的區隔 — XAML 使用者介面，稱為檢視;基礎資料，稱為模型;而在檢視和模型之間的媒介稱為 ViewModel。檢視和 ViewModel 通常是透過定義在 XAML 檔案中的資料繫結連接。檢視 Bindingparameters 通常是 ViewModel 的執行個體。_

## <a name="a-simple-viewmodel"></a>簡單 ViewModel

ViewModels 簡介，讓我們先看看其中一個情況下的程式。
先前您可了解如何定義新的 XML 命名空間宣告來允許其他組件中的參考類別的 XAML 檔案。 以下是程式定義的 XML 命名空間宣告`System`命名空間：

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

程式可以使用`x:Static`靜態取得目前的日期和時間`DateTime.Now`屬性並將其設定`DateTime`值設定為`BindingContext`上`StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` 是非常特殊的屬性： 當您將`BindingContext`上一個項目，它會繼承該項目的所有子系。 這表示，所有的子系`StackLayout`有此相同`BindingContext`，而且可以包含該物件的內容的簡單繫結。

在**One-Shot DateTime**程式中，子系的兩個包含該屬性的繫結`DateTime`值，但兩個其他子系包含似乎已遺失繫結路徑的繫結。 這表示`DateTime`本身的值會用於`StringFormat`:

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

當然，大問題是它的日期和時間設定，當頁面第一次建置之後，而且永遠不會變更：

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "顯示日期和時間 檢視")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "檢視顯示日期和時間")

XAML 檔案後可以顯示 clock 永遠會顯示目前的時間，但它需要幫助的一些程式碼。當 MVVM、 模型和 ViewModel 方面的想法是完全以程式碼撰寫的類別。 檢視通常是參考中定義的屬性 ViewModel 透過資料繫結的 XAML 檔案。

適當的模型並不知道 ViewModel，並適當 ViewModel 並不知道的檢視。 不過，通常是程式設計人員可調整 ViewModel 與特定的使用者介面相關聯的資料型別所公開的資料類型。 例如，如果模型來存取資料庫，其中包含 8 位元的字元的 ASCII 字串，ViewModel 需要轉換為 Unicode 字串，以容納獨佔使用在使用者介面中的 Unicode 字串之間。

在簡單範例中的 MVVM （例如，如下所示），通常沒有模型，模式牽涉到只檢視和與資料繫結連結 ViewModel。

以下是與只單一屬性名為 clock ViewModel `DateTime`，這會更新，但`DateTime`屬性每秒：

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

通常實作 ViewModels`INotifyPropertyChanged`介面，這表示在類別引發`PropertyChanged`只要其中一個屬性變更事件。 資料繫結機制，在 Xamarin.Forms 中的附加至這個處理常式`PropertyChanged`事件，所以可以在屬性變更時通知並保留目標會以新值更新。

根據此 ViewModel 時鐘很簡單，因為這樣：

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

請注意如何`ClockViewModel`設`BindingContext`的`Label`使用屬性項目標記。 或者，您可以具現化`ClockViewModel`中`Resources`集合並將它設定為`BindingContext`透過`StaticResource`標記延伸。 或者，程式碼後置檔案可以具現化 ViewModel。

`Binding`標記延伸上的`Text`屬性`Label`格式`DateTime`屬性。 以下是顯示：

[![](data-bindings-to-mvvm-images/clock.png "檢視顯示日期和時間透過 ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "檢視顯示日期和時間透過 ViewModel")

它也可存取的個別屬性`DateTime`以句號分隔屬性 ViewModel 屬性：

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>互動式 MVVM

MVVM 經常搭配雙向資料繫結為基礎的資料模型為基礎的互動式檢視。

以下是名為類別`HslViewModel`將轉換`Color`值放入`Hue`， `Saturation`，和`Luminosity`值，且反之亦然：

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

變更為`Hue`， `Saturation`，和`Luminosity`屬性原因`Color`屬性，即可變更，以及變更`Color`導致其他三個屬性變更。 這看起來像是無限迴圈，不同之處在於類別不會叫用`PropertyChanged`事件除非屬性實際上已變更。 這就不會再否則無法控制的意見反應迴圈。

下列 XAML 檔案包含`BoxView`其`Color`屬性繫結至`Color`ViewModel 與三個屬性`Slider`和三個`Label`檢視繫結至`Hue`， `Saturation`，和`Luminosity`屬性：

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

在每個繫結`Label`是預設值`OneWay`。 它只需要顯示的值。 但在每個繫結`Slider`是`TwoWay`。 這可讓`Slider`從 ViewModel 初始化。 請注意，`Color`屬性設定為`Aqua`ViewModel 具現化。 而是在變更`Slider`也需要在 ViewModel，然後計算新的色彩中設定屬性的新值。

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "使用雙向資料繫結 MVVM")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM 使用雙向資料繫結")

## <a name="commanding-with-viewmodels"></a>與 ViewModels 指揮

在許多情況下，MVVM 模式是限制為資料項目的操作： 在檢視中的使用者介面物件的平行 ViewModel 中的資料物件。

不過，有時候需要檢視包含觸發 ViewModel 中的各種動作的按鈕。 但不是能包含 ViewModel`Clicked`按鈕處理常式，會將繫結至特定的使用者介面典範 ViewModel 因為。

若要允許更獨立於特定的使用者介面物件，但是仍然允許 ViewModel 內呼叫的方法 ViewModels*命令*介面存在。 此命令介面支援 Xamarin.Forms 中的下列元素：

-  `Button`
-  `MenuItem`
-  `ToolbarItem`
-  `SearchBar`
-  `TextCell` (因此也`ImageCell`)
-  `ListView`
-  `TapGestureRecognizer`

除了`SearchBar`和`ListView`項目，這些元素會定義兩個屬性：

-  `Command` 型別  `System.Windows.Input.ICommand`
-  `CommandParameter` 型別  `Object`

`SearchBar`定義`SearchCommand`和`SearchCommandParameter`屬性，而`ListView`定義`RefreshCommand`型別的屬性`ICommand`。

`ICommand`介面會定義兩個方法和一個事件：

-  `void Execute(object arg)`
-  `bool CanExecute(object arg)`
-  `event EventHandler CanExecuteChanged`

ViewModel 可以定義類型的屬性`ICommand`。 您接著可以繫結至這些屬性`Command`每個屬性`Button`或其他項目或可能是實作這個介面的自訂檢視。 您可以選擇設定`CommandParameter`屬性來識別個別`Button`物件 （或其他項目） 的繫結至這個 ViewModel 屬性。 就內部而言，`Button`呼叫`Execute`方法，當使用者點選`Button`，並傳遞至`Execute`方法及其`CommandParameter`。

`CanExecute`方法和`CanExecuteChanged`事件用的情況下其中`Button`點選在此情況下可能會為目前無效，`Button`應該停用。 `Button`呼叫`CanExecute`時`Command`先設定屬性，每當`CanExecuteChanged`引發事件。 如果`CanExecute`傳回`false`、`Button`停用，並不會產生`Execute`呼叫。

Xamarin.Forms 中加入至您 ViewModels 命令的說明，請定義兩個類別可實作`ICommand`:`Command`和`Command<T>`其中`T`是引數的型別`Execute`和`CanExecute`。 這兩個類別定義數個建構函式加上一個`ChangeCanExecute`ViewModel 可以呼叫來強制方法`Command`物件引發`CanExecuteChanged`事件。

以下是簡單字鍵台，用來進行輸入電話號碼的 ViewModel。 請注意，`Execute`和`CanExecute`方法定義建構函式中的 lambda 函式權限為：

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

此 ViewModel 假設`AddCharCommand`屬性繫結至`Command`屬性的數個按鈕 （或任何其他項目有命令介面），其中每一個都由`CommandParameter`。 這些按鈕加入到字元`InputString`屬性，然後格式化電話號碼為`DisplayText`屬性。

另外還有第二個型別的屬性`ICommand`名為`DeleteCharCommand`。 這繫結至後間距按鈕，但是如果沒有要刪除的字元，應該停用按鈕。

因為它可能會以下的數字鍵台不是以視覺化方式複雜。 相反地，標記已經降至最小，示範如何更清楚地使用命令介面：

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

`Command`屬性的第一個`Button`出現在這個標記會繫結至`DeleteCharCommand`; 其餘部分會繫結至`AddCharCommand`與`CommandParameter`出現的字元也就是相同`Button`字體。 以下是程式中的動作：

[![](data-bindings-to-mvvm-images/keypad.png "MVVM 與命令所使用的計算機")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "MVVM 與命令所使用的計算機")

### <a name="invoking-asynchronous-methods"></a>叫用非同步方法

命令也可以呼叫非同步方法。 這藉由使用`async`和`await`時指定關鍵字`Execute`方法：

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

這表示`DownloadAsync`方法`Task`且應該等候：

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

[XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)包含在這一系列的文章中的所有原始碼程式用於其首頁 ViewModel。 此 ViewModel 是簡短類別具有名為三個屬性的定義`Type`， `Title`，和`Description`，其中包含的範例頁面、 標題和簡短描述每個型別。 此外，ViewModel 定義名為的靜態屬性`All`也就是在程式中的所有分頁的集合：

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

XAML 檔案`MainPage`定義`ListBox`其`ItemsSource`屬性設定為，`All`屬性，其中包含`TextCell`顯示`Title`和`Description`每一頁的屬性：

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

[![](data-bindings-to-mvvm-images/mainpage.png "可捲動的頁面清單")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "可捲動的頁面清單")

當使用者選取項目時，會觸發程式碼後置檔案中的處理常式。 處理常式設定`SelectedItem`屬性`ListBox`回到`null`然後具現化選取的頁面，並瀏覽至它：

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

**Xamarin 發展 2016年： 變得簡單角柱 Xamarin.Forms 與 MVVM**

## <a name="summary"></a>總結

XAML 是功能強大的工具，在 Xamarin.Forms 應用程式，特別是當資料繫結中定義使用者介面，而且 MVVM 可用。 結果是在程式碼中的所有背景支援使用者介面的清楚、 簡潔，和潛在 toolable 表示法。


## <a name="related-links"></a>相關連結

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：開始使用 XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 2 部分：基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 3 部分：XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 4 部分：資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
