---
title: "繫結模式"
description: "控制項的來源和目標之間的資訊流程"
ms.topic: article
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 887dc3cf710fb75d05d02af179bc218c15d31f97
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="binding-mode"></a>繫結模式

在[前一篇文章](basic-bindings.md)、**替代方案的程式碼繫結**和**替代 XAML 繫結**精選的頁面`Label`與其`Scale`屬性繫結至`Value`屬性`Slider`。 因為`Slider`初始值為 0，這造成`Scale`屬性`Label`設為 0，而不是 1，而`Label`消失。

在[ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)範例中，**反向繫結**頁面是類似於先前的文件中的程式，不同之處在於上定義的資料繫結`Slider`而非在`Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.ReverseBindingPage"
             Title="Reverse Binding">
    <StackLayout Padding="10, 0">

        <Label x:Name="label" 
               Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                VerticalOptions="CenterAndExpand"
                Value="{Binding Source={x:Reference label},
                                Path=Opacity}" />
    </StackLayout>
</ContentPage>
```

在一開始，這看起來似乎回溯： 現在`Label`是資料繫結來源，而`Slider`做為目標。 繫結參考`Opacity`屬性`Label`，其具有預設值是 1。

如您所料，`Slider`會初始化為值 1，從初始`Opacity`值`Label`。 在左側 iOS 螢幕擷取畫面所示：

[![反轉繫結](binding-mode-images/reversebinding-small.png "反向繫結")](binding-mode-images/reversebinding-large.png#lightbox "反向繫結")

但您可能會感到驚訝，`Slider`會繼續運作，如的 Android 和 UWP 螢幕擷取畫面所示。 這似乎建議更好時才能運作的資料繫結`Slider`是繫結目標而不是`Label`因為初始化可以運作，我們可能預期一樣。

之間的差異**反向繫結**範例和較早的範例包括*繫結模式*。

## <a name="the-default-binding-mode"></a>預設繫結模式

成員指定了繫結模式[ `BindingMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingMode/)列舉型別： 

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.Default/) 
- [`TwoWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/) &ndash; 資料來源和目標之間進入這兩種方式
- [`OneWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWay/) &ndash; 資料會從來源移到目標
- [`OneWayToSource`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWayToSource/) &ndash; 資料是從目標到來源

每個可繫結屬性具有預設的繫結模式時建立可繫結的屬性時，才設定，以及從[ `DefaultBindingMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableProperty.DefaultBindingMode/)屬性`BindableProperty`物件。 作用中時，該屬性是資料繫結目標時，此預設繫結模式將指出的模式。

這類的大部分屬性的預設繫結模式`Rotation`， `Scale`，和`Opacity`是`OneWay`。 資料繫結目標屬性時，從來源設定的目標屬性。

不過，預設的繫結模式`Value`屬性`Slider`是`TwoWay`。 這表示當`Value`屬性是資料繫結目標時，將 （如往常般） 的目標集從來源，但來源也設定目標。 這就是允許`Slider`從初始設定`Opacity`值。

這個雙向繫結可能會建立無限迴圈，但是，也不會發生。 可繫結屬性不表示屬性變更，除非屬性實際上有所變更。 這可防止無限迴圈。

### <a name="two-way-bindings"></a>雙向繫結

最可繫結屬性都使用預設繫結模式的`OneWay`但下列屬性的預設繫結模式`TwoWay`:

- `Date` 屬性 `DatePicker`
- `Text` 屬性`Editor`， `Entry`， `SearchBar`，和 `EntryCell`
- `IsRefreshing` 屬性 `ListView`
- `SelectedItem` 屬性 `MultiPage`
- `SelectedIndex` 和`SelectedItem`屬性 `Picker`
- `Value` 屬性`Slider`和 `Stepper`
- `IsToggled` 屬性 `Switch` 
- `On` 屬性 `SwitchCell`
- `Time` 屬性 `TimePicker`

這些特定的屬性定義為`TwoWay`很好的原因： 

資料繫結搭配使用的模型-檢視-ViewModel (MVVM) 應用程式架構，ViewModel 類別時，資料繫結來源和檢視中，例如組成檢視`Slider`，資料繫結目標。 MVVM 繫結類似於**反向繫結**比先前的範例中的繫結的範例。 就很可能是您想要初始化 ViewModel 中對應之屬性的值 頁面上的每個檢視，但在檢視中的變更也會影響 ViewModel 屬性。

使用預設繫結模式的屬性`TwoWay`是最有可能在 MVVM 案例中使用這些屬性。

### <a name="one-way-to-source-bindings"></a>其中之一-單向到來源的繫結

唯讀的可繫結屬性都使用預設繫結模式的`OneWayToSource`。 沒有具有預設繫結模式只有一個讀取/寫入可繫結屬性`OneWayToSource`:

- `SelectedItem` 屬性 `ListView`

基本原理是繫結的`SelectedItem`屬性應該會導致設定繫結來源。 本文稍後的範例會覆寫該行為。

## <a name="viewmodels-and-property-change-notifications"></a>ViewModels 和屬性變更通知

**簡單色彩選取器**頁面會示範如何使用簡單 ViewModel。 資料繫結允許使用者選取色彩，使用三個`Slider`色調、 飽和度和亮度的項目。

ViewModel 是資料繫結來源。 沒有 ViewModel*不*定義可繫結的屬性，但它實作可讓繫結基礎結構的屬性值變更時收到通知的通知機制。 此通知機制[ `INotifyPropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/)介面會定義單一內容，名為[ `PropertyChanged` ](https://developer.xamarin.com/api/event/System.ComponentModel.INotifyPropertyChanged.PropertyChanged/)。 通常實作這個介面的類別引發事件的其中一個公用屬性的值變更時。 如果屬性不會再變更引發事件不需要。 (`INotifyPropertyChanged`介面也由實作`BindableObject`和`PropertyChanged`可繫結的屬性變更值時會引發事件。)

`HslColorViewModel`類別會定義五個屬性： `Hue`， `Saturation`， `Luminosity`，和`Color`相互關聯屬性。 其中的三種色彩的元件變更值，當`Color`重新計算屬性，以及`PropertyChanged`所有四個屬性不會引發事件：

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name; 

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

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

當`Color`屬性變更、 靜態`GetNearestColorName`方法中的`NamedColor`類別 (也包含在**DataBindingDemos**方案) 取得最接近的命名的色彩，並設定`Name`屬性。 這`Name`屬性具有私用`set`存取子，因此無法從中設定在類別外部。

繫結基礎結構當 ViewModel 設定做為繫結來源時，附加至處理常式`PropertyChanged`事件。 如此一來，繫結屬性的變更通知和可以設定目標屬性從變更的值。

**簡單色彩選取器**XAML 檔案會具現化`HslColorViewModel`中頁面的資源字典，並初始化`Color`屬性。 `BindingContext`屬性`Grid`設`StaticResource`繫結參考該資源擴充功能：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SimpleColorSelectorPage">

    <ContentPage.Resources>
        <ResourceDictionary>
            <local:HslColorViewModel x:Key="viewModel" 
                                     Color="MediumTurquoise" />

            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
        
    <Grid BindingContext="{StaticResource viewModel}">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <BoxView Color="{Binding Color}"
                 Grid.Row="0" />

        <StackLayout Grid.Row="1"
                     Margin="10, 0">

            <Label Text="{Binding Name}"
                   HorizontalTextAlignment="Center" />

            <Slider Value="{Binding Hue}" />
    
            <Slider Value="{Binding Saturation}" />

            <Slider Value="{Binding Luminosity}" />
        </StackLayout>
    </Grid>
</ContentPage>
```

`BoxView`， `Label`，和三個`Slider`檢視繼承繫結內容從`Grid`。 這些檢視表是參考 ViewModel 中的來源屬性的所有繫結目標。 如`Color`屬性`BoxView`，而`Text`屬性`Label`，資料繫結是`OneWay`： 從 ViewModel 中的屬性會設定在檢視中的屬性。

`Value`屬性`Slider`，不過， `TwoWay`。 這可讓每個`Slider`設 ViewModel，而且也為每個要設定 ViewModel `Slider`。 

第一次執行此程式， `BoxView`， `Label`，和三個`Slider`項目都從根據初始 ViewModel 設`Color`ViewModel 未具現化時設定的屬性。 在左側 iOS 螢幕擷取畫面所示：

[![簡單的色彩選取器](binding-mode-images/simplecolorselector-small.png "簡單色彩選取器")](binding-mode-images/simplecolorselector-large.png#lightbox "簡單色彩選取器")

以操作滑桿，`BoxView`和`Label`據以更新，如的 Android 和 UWP 螢幕擷取畫面所示。

具現化的資源字典中的 ViewModel 是一個常見的方法。 它也是可具現化中的屬性項目標記 ViewModel`BindingContext`屬性。 在**簡單色彩選取器**XAML 檔案中，請嘗試移除`HslColorViewModel`從資源字典並將它設定為`BindingContext`屬性`Grid`如下所示：

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>
        
    ···

</Grid>
```

繫結內容可以設定各種不同的方式。 某些情況下，會具現化 ViewModel 程式碼後置檔案，並將其設`BindingContext`頁的屬性。 這些是有效的方法。

## <a name="overriding-the-binding-mode"></a>覆寫的繫結模式

如果要針對目標屬性的預設繫結模式不是適用於特定的資料繫結，便可設定來覆寫它[ `Mode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.Mode/)屬性`Binding`(或[ `Mode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Mode/)屬性`Binding`標記延伸) 的成員的其中一個`BindingMode`列舉型別。

不過，設定`Mode`屬性`TwoWay`永遠無法運作如您所預期。 例如，請嘗試修改**替代 XAML 繫結**XAML 檔案以納入`TwoWay`中的繫結定義：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

可能預期的`Slider`將初始化的初始值為`Scale`屬性，也就是 1，但是，也不會發生。 當`TwoWay`初始化繫結時，目標會設定來源的第一次，這表示`Scale`屬性設定為`Slider`預設值為 0。 當`TwoWay`上設定繫結`Slider`，然後在`Slider`一開始會從來源設定。

您可以將繫結模式`OneWayToSource`中**替代 XAML 繫結**範例：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

現在`Slider`會初始化為 1 (預設值的`Scale`) 但操作`Slider`並不會影響`Scale`屬性，因此這不是很有用。

非常有用的應用程式的覆寫預設的繫結模式與`TwoWay`牽涉到`SelectedItem`屬性`ListView`。 預設繫結模式是`OneWayToSource`。 資料繫結上的設定時`SelectedItem`屬性來參考 ViewModel，來源屬性，則該資料來源 屬性會設定從`ListView`選取項目。 不過，在某些情況下，您可能也想`ListView`從 ViewModel 初始化。

**範例設定**頁面示範這項技巧。 此頁面表示應用程式設定，通常會定義於 ViewModel，這類的簡單實作`SampleSettingsViewModel`檔案：

```csharp
public class SampleSettingsViewModel : INotifyPropertyChanged
{
    string name;
    DateTime birthDate;
    bool codesInCSharp;
    double numberOfCopies;
    NamedColor backgroundNamedColor;

    public event PropertyChangedEventHandler PropertyChanged;

    public SampleSettingsViewModel(IDictionary<string, object> dictionary)
    {
        Name = GetDictionaryEntry<string>(dictionary, "Name");
        BirthDate = GetDictionaryEntry(dictionary, "BirthDate", new DateTime(1980, 1, 1));
        CodesInCSharp = GetDictionaryEntry<bool>(dictionary, "CodesInCSharp");
        NumberOfCopies = GetDictionaryEntry(dictionary, "NumberOfCopies", 1.0);
        BackgroundNamedColor = NamedColor.Find(GetDictionaryEntry(dictionary, "BackgroundNamedColor", "White"));
    }

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public DateTime BirthDate
    {
        set { SetProperty(ref birthDate, value); }
        get { return birthDate; }
    }

    public bool CodesInCSharp
    {
        set { SetProperty(ref codesInCSharp, value); }
        get { return codesInCSharp; }
    }

    public double NumberOfCopies
    {
        set { SetProperty(ref numberOfCopies, value); }
        get { return numberOfCopies; }
    }

    public NamedColor BackgroundNamedColor
    {
        set
        {
            if (SetProperty(ref backgroundNamedColor, value))
            {
                OnPropertyChanged("BackgroundColor");
            }
        }
        get { return backgroundNamedColor; }
    }

    public Color BackgroundColor
    {
        get { return BackgroundNamedColor?.Color ?? Color.White; }
    }

    public void SaveState(IDictionary<string, object> dictionary)
    {
        dictionary["Name"] = Name;
        dictionary["BirthDate"] = BirthDate;
        dictionary["CodesInCSharp"] = CodesInCSharp;
        dictionary["NumberOfCopies"] = NumberOfCopies;
        dictionary["BackgroundNamedColor"] = BackgroundNamedColor.Name;
    }

    T GetDictionaryEntry<T>(IDictionary<string, object> dictionary, string key, T defaultValue = default(T))
    {
        return dictionary.ContainsKey(key) ? (T)dictionary[key] : defaultValue;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

每個應用程式設定是儲存在名為方法中的 Xamarin.Forms 屬性字典屬性`SaveState`並從該字典的建構函式中載入。 類別的底端是兩個方法協助簡化 ViewModels 並讓它們比較不容易發生錯誤。 `OnPropertyChanged`底部的方法有選擇性參數設為呼叫端的屬性。 指定的屬性名稱做為字串時，這可避免拼字錯誤。 

`SetProperty`類別中的方法會執行更多： 它會比較已儲存為欄位的值設定為屬性的值，並只呼叫`OnPropertyChanged`當兩個值是否不相等。 

`SampleSettingsViewModel`類別定義的背景色彩的兩個屬性：`BackgroundNamedColor`屬性屬於型別`NamedColor`，類別也隨附於**DataBindingDemos**方案。 `BackgroundColor`屬性屬於型別`Color`，並從取得`Color`屬性`NamedColor`物件。

`NamedColor`類別使用.NET 反映以列舉中的所有靜態公用欄位 Xamarin.Forms`Color`結構，並將它們儲存其可存取靜態集合內的名稱與`All`屬性：

```csharp
public class NamedColor : IEquatable<NamedColor>, IComparable<NamedColor>
{
    // Instance members
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    public bool Equals(NamedColor other)
    {
        return Name.Equals(other.Name);
    }

    public int CompareTo(NamedColor other)
    {
        return Name.CompareTo(other.Name);
    }

    // Static members
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof(Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                                (int)(255 * color.R),
                                                (int)(255 * color.G),
                                                (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        all.Sort();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }

    public static NamedColor Find(string name)
    {
        return ((List<NamedColor>)All).Find(nc => nc.Name == name);
    }

    public static string GetNearestColorName(Color color)
    {
        double shortestDistance = 1000;
        NamedColor closestColor = null;

        foreach (NamedColor namedColor in NamedColor.All)
        {
            double distance = Math.Sqrt(Math.Pow(color.R - namedColor.Color.R, 2) +
                                        Math.Pow(color.G - namedColor.Color.G, 2) +
                                        Math.Pow(color.B - namedColor.Color.B, 2));

            if (distance < shortestDistance)
            {
                shortestDistance = distance;
                closestColor = namedColor;
            }
        }
        return closestColor.Name;
    }
}
```

`App`類別**DataBindingDemos**專案定義的屬性，名為`Settings`型別的`SampleSettingsViewModel`。 這個屬性會初始化時`App`類別具現化，和`SaveState`方法時，會呼叫`OnSleep`方法呼叫：

```csharp
public partial class App : Application
{
    public App()
    {
        InitializeComponent();

        Settings = new SampleSettingsViewModel(Current.Properties);

        MainPage = new NavigationPage(new MainPage());
    }

    public SampleSettingsViewModel Settings { private set; get; }

    protected override void OnStart()
    {
        // Handle when your app starts
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Settings.SaveState(Current.Properties);
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
    }
}
```

如需有關應用程式生命週期方法的詳細資訊，請參閱文章[**應用程式生命週期**](~/xamarin-forms/app-fundamentals/app-lifecycle.md)。

幾乎所有其他項目中，會處理**SampleSettingsPage.xaml**檔案。 `BindingContext`頁面已設定使用`Binding`標記延伸： 繫結來源是靜態`Application.Current`屬性，這是執行個體的`App`在專案中，類別和`Path`設`Settings`屬性，這是`SampleSettingsViewModel`物件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SampleSettingsPage"
             Title="Sample Settings"
             BindingContext="{Binding Source={x:Static Application.Current},
                                      Path=Settings}">

    <StackLayout BackgroundColor="{Binding BackgroundColor}"
                 Padding="10"
                 Spacing="10">

        <StackLayout Orientation="Horizontal">
            <Label Text="Name: "
                   VerticalOptions="Center" />

            <Entry Text="{Binding Name}"
                   Placeholder="your name"
                   HorizontalOptions="FillAndExpand"
                   VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Birth Date: "
                   VerticalOptions="Center" />

            <DatePicker Date="{Binding BirthDate}"
                        HorizontalOptions="FillAndExpand"
                        VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Do you code in C#? "
                   VerticalOptions="Center" />

            <Switch IsToggled="{Binding CodesInCSharp}"
                    VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Number of Copies: "
                   VerticalOptions="Center" />

            <Stepper Value="{Binding NumberOfCopies}"
                     VerticalOptions="Center" />

            <Label Text="{Binding NumberOfCopies}"
                   VerticalOptions="Center" />
        </StackLayout>

        <Label Text="Background Color:" />

        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
                  VerticalOptions="FillAndExpand"
                  RowHeight="40">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     HeightRequest="32"
                                     WidthRequest="32"
                                     VerticalOptions="Center" />

                            <Label Text="{Binding FriendlyName}"
                                   FontSize="24"
                                   VerticalOptions="Center" />
                        </StackLayout>                        
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

頁面的所有子系會繼承繫結內容。 大部分的此頁面上的其他繫結都是中的內容`SampleSettingsViewModel`。 `BackgroundColor`屬性用來設定`BackgroundColor`屬性`StackLayout`，而`Entry`， `DatePicker`， `Switch`，和`Stepper`屬性會結合到 ViewModel 中的其他屬性。

`ItemsSource`屬性`ListView`設為靜態`NamedColor.All`屬性。 這會填滿`ListView`所有`NamedColor`執行個體。 每個項目中`ListView`，項目的繫結內容設定為`NamedColor`物件。 `BoxView`和`Label`中`ViewCell`繫結中的屬性至`NamedColor`。

`SelectedItem`屬性`ListView`的型別`NamedColor`，而且繫結至`BackgroundNamedColor`屬性`SampleSettingsViewModel`:

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

預設繫結模式`SelectedItem`是`OneWayToSource`，它會從選取的項目設定 ViewModel 屬性。 `TwoWay`模式可讓`SelectedItem`從 ViewModel 初始化。 

不過，當`SelectedItem`設定如此一來，`ListView`不會自動捲動以顯示所選的項目。 程式碼後置檔案中的一些程式碼為必要值：

```csharp
public partial class SampleSettingsPage : ContentPage
{
    public SampleSettingsPage()
    {
        InitializeComponent();

        if (colorListView.SelectedItem != null)
        {
            colorListView.ScrollTo(colorListView.SelectedItem, 
                                   ScrollToPosition.MakeVisible, 
                                   false);
        }
    }
}
``` 

第一次執行時，左側 iOS 螢幕擷取畫面會顯示程式。 在建構函式`SampleSettingsViewModel`初始化的背景色彩為白色，而這就是在選取的項目`ListView`:

[![範例設定](binding-mode-images/samplesettings-small.png "範例設定")](binding-mode-images/samplesettings-large.png#lightbox "範例設定")

其他兩個螢幕擷取畫面顯示變更後的設定。 當實驗此頁面，請記得將進入睡眠狀態，或終止上裝置或模擬器正在執行的程式。 結束 Visual Studio 偵錯工具中的程式不會造成`OnSleep`中覆寫`App`呼叫類別。

下一個文件中，您會看到如何指定[**字串格式化**](string-formatting.md)資料繫結上所設定的`Text`屬性`Label`。


## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [資料繫結 Xamarin.Forms 書籍章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
