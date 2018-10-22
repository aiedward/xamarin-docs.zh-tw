---
title: Xamarin.Forms 繫結模式
description: 這篇文章說明如何控制的來源和目標使用繫結模式，指定與 BindingMode 列舉的成員之間的資訊。 每個可繫結的屬性具有預設繫結模式下，當該屬性是資料繫結目標時，作用中指出的模式。
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/01/2018
ms.openlocfilehash: a6eaf08d17f70c43f451361e27555a09c39f26a9
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935663"
---
# <a name="xamarinforms-binding-mode"></a>Xamarin.Forms 繫結模式

中[前一篇文章](basic-bindings.md)，則**替代方案的程式碼繫結**並**替代 XAML 繫結**精選頁面`Label`與其`Scale`屬性繫結至`Value`屬性`Slider`。 因為`Slider`初始值為 0，這造成`Scale`屬性`Label`設為 0，而不是 1，而`Label`消失。

在  [ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)範例中，**反向繫結**頁面是類似的程式在上一篇文章中，不同之處在於上定義的資料繫結`Slider`而非在`Label`:

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

一開始，這看起來似乎回溯： 現在`Label`是資料繫結來源，而`Slider`做為目標。 繫結參考`Opacity`屬性`Label`，其具有預設值為 1。

如您所料，`Slider`會初始化為值 1，從初始`Opacity`的值`Label`。 在左側的 iOS 螢幕擷取畫面所示：

[![反轉繫結](binding-mode-images/reversebinding-small.png "反向繫結")](binding-mode-images/reversebinding-large.png#lightbox "反向繫結")

但您可能會很訝異，`Slider`會繼續運作，如 Android 和 UWP 的螢幕擷取畫面所示。 這似乎顯示資料繫結運作效果較佳`Slider`是繫結目標而不是`Label`由於像預期可能的運作方式初始化。

之間的差異**反向繫結**範例和較早的範例包括*繫結模式*。

## <a name="the-default-binding-mode"></a>預設繫結模式

繫結模式指定的成員[ `BindingMode` ](xref:Xamarin.Forms.BindingMode)列舉型別：

- [`Default`](xref:Xamarin.Forms.BindingMode.Default)
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) &ndash; 資料來源和目標之間進入這兩種方式
- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) &ndash; 資料會從來源移到目標
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; 資料會從目標到來源
- [`OneTime`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; 資料會從來源移到目標，但只有在`BindingContext`變更 （新增使用 Xamarin.Forms 3.0）

每個可繫結的屬性已繫結模式所設定的可繫結屬性建立時，預設值，並可從[ `DefaultBindingMode` ](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode)屬性`BindableProperty`物件。 作用中時，該屬性是資料繫結目標時，此預設繫結模式將指出的模式。

這類的大部分屬性的預設繫結模式`Rotation`， `Scale`，並`Opacity`是`OneWay`。 當這些屬性是資料繫結目標時，則會設定從來源目標屬性。

不過的預設繫結模式`Value`的屬性`Slider`是`TwoWay`。 這表示當`Value`屬性資料繫結目標，則目標設定從來源 （如往常般） 但來源也會從目標設定。 這可讓`Slider`若要設定從初始`Opacity`值。

這個雙向繫結可能會建立無限迴圈，但是，不會發生。 除非屬性會實際變更，可繫結的屬性就發出信號屬性變更。 這可防止無限迴圈。

### <a name="two-way-bindings"></a>雙向繫結

最可繫結的屬性具有預設繫結模式`OneWay`下列屬性具有預設繫結模式，但`TwoWay`:

- `Date` 屬性 `DatePicker`
- `Text` 屬性`Editor`， `Entry`， `SearchBar`，及 `EntryCell`
- `IsRefreshing` 屬性 `ListView`
- `SelectedItem` 屬性 `MultiPage`
- `SelectedIndex` 和`SelectedItem`屬性 `Picker`
- `Value` 屬性`Slider`和 `Stepper`
- `IsToggled` 屬性 `Switch`
- `On` 屬性 `SwitchCell`
- `Time` 屬性 `TimePicker`

這些特定的屬性會定義為`TwoWay`的很好的理由：

資料繫結會搭配 Model View ViewModel (MVVM) 應用程式架構，ViewModel 類別時，資料繫結來源，以及檢視，其中包含檢視這類`Slider`，是資料繫結的目標。 MVVM 繫結類似於**反向繫結**大於先前的範例中的繫結的範例。 就很可能是您想要使用 ViewModel 中的對應屬性的值來初始化 頁面上的每個檢視，但在檢視中的變更也會影響 ViewModel 屬性。

使用預設繫結模式的屬性`TwoWay`是最有可能會使 MVVM 案例中使用這些屬性。

### <a name="one-way-to-source-bindings"></a>單次-單向到來源的繫結

唯讀的可繫結屬性具有預設繫結模式的`OneWayToSource`。 只有一個讀取/寫入可繫結屬性具有預設繫結模式`OneWayToSource`:

- `SelectedItem` 屬性 `ListView`

基本原理是繫結的`SelectedItem`屬性應該會導致設定繫結來源。 在本文稍後的範例會覆寫該行為。

### <a name="one-time-bindings"></a>一次性繫結

數個屬性具有預設繫結模式的`OneTime`。 這些是：

- `IsTextPredictionEnabled` 屬性 `Entry`
- `Text``BackgroundColor`，並`Style`的屬性`Span`。

目標屬性的繫結模式`OneTime`繫結內容變更時，才會更新。 如需這些目標屬性的繫結，這樣會簡化繫結基礎結構，因為不需要監視的來源屬性中的變更。

## <a name="viewmodels-and-property-change-notifications"></a>Viewmodel 和屬性變更通知

**簡單的色彩選取器**頁面會示範如何使用簡單的 ViewModel。 資料繫結可讓使用者選取色彩，使用三個`Slider`色調、 飽和度和亮度的項目。

ViewModel 是資料繫結來源。 ViewModel 沒有*不*定義可繫結的屬性，但它實作可讓繫結基礎結構的屬性值變更時接獲通知的通知機制。 此通知機制[ `INotifyPropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged)介面，其會定義單一屬性，名為[ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged)。 通常會實作這個介面的類別就會引發事件的其中一個公用的屬性變更值時。 如果此屬性永遠不會變更引發事件不需要。 (`INotifyPropertyChanged`也會實作介面`BindableObject`和`PropertyChanged`可繫結的屬性值變更時會引發事件。)

`HslColorViewModel`類別會定義五個屬性： `Hue`， `Saturation`， `Luminosity`，和`Color`相互關聯屬性。 其中的三個色彩元件變更的值，當`Color`屬性會重新計算，和`PropertyChanged`所有四個屬性不會引發事件：

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

當`Color`屬性變更、 靜態`GetNearestColorName`方法中的`NamedColor`類別 (也包含在**DataBindingDemos**方案) 取得最接近的命名的色彩，並設定`Name`屬性。 這`Name`屬性具有私用`set`存取子，因此無法從中設定的類別之外。

當 ViewModel 設定做為繫結來源時，繫結基礎結構會將附加處理常式`PropertyChanged`事件。 如此一來，繫結屬性，變更就可以收到，然後從變更的值設定的目標屬性。

不過，當目標屬性 (或`Binding`目標屬性的定義) 已`BindingMode`的`OneTime`，不需要將連接上的處理常式的繫結基礎結構`PropertyChanged`事件。 目標屬性更新時，才`BindingContext`變更和未本身的 [來源] 屬性變更時。

**簡單的色彩選取器**XAML 檔案會具現化`HslColorViewModel`在頁面的資源字典，並初始化`Color`屬性。 `BindingContext`的屬性`Grid`設定為`StaticResource`繫結來參考該資源的延伸模組：

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

`BoxView`， `Label`，和三個`Slider`檢視繼承繫結內容從`Grid`。 這些檢視會參考在 ViewModel 的來源屬性的所有繫結目標。 針對`Color`屬性`BoxView`，，`Text`屬性`Label`，資料繫結`OneWay`: 檢視中的屬性會設定從 ViewModel 的屬性。

`Value`的屬性`Slider`，不過， `TwoWay`。 這可讓每個`Slider`從 ViewModel，以及從每個設定 ViewModel 設定`Slider`。

當第一次執行程式時， `BoxView`， `Label`，和三個`Slider`項目都會從 ViewModel 根據初始設定`Color`ViewModel 具現化時設定的屬性。 這是由左邊的 iOS 螢幕擷取畫面所示：

[![簡單的色彩選取器](binding-mode-images/simplecolorselector-small.png "簡單色彩選取器")](binding-mode-images/simplecolorselector-large.png#lightbox "簡單色彩選取器")

您操作滑桿`BoxView`和`Label`會隨之更新、 Android 及 UWP 螢幕擷取畫面所示。

資源字典中的 ViewModel 具現化是一個常見的方法。 您也可在屬性項目標記的 ViewModel 具現化`BindingContext`屬性。 在 **簡單色彩選取器**XAML 檔案中，請嘗試移除`HslColorViewModel`從資源字典並將它設定為`BindingContext`屬性`Grid`如下所示：

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>

    ···

</Grid>
```

繫結內容可以設定各種不同的方式。 某些情況下，程式碼後置檔案 ViewModel 具現化，並將它設定為`BindingContext`頁的屬性。 這些是所有有效的方法。

## <a name="overriding-the-binding-mode"></a>覆寫繫結模式

如果目標屬性的預設繫結模式不是適用於特定的資料繫結，就可以設定覆寫它[ `Mode` ](xref:Xamarin.Forms.BindingBase.Mode)屬性`Binding`(或[ `Mode` ](xref:Xamarin.Forms.Xaml.BindingExtension.Mode)的屬性`Binding`標記延伸) 至其中的成員`BindingMode`列舉型別。

不過，設定`Mode`屬性設`TwoWay`不一定永遠行得通，跟您預期的一樣。 例如，請嘗試修改**替代 XAML 繫結**XAML 檔案，以包含`TwoWay`繫結定義中：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

它可能會有可`Slider`會初始化為初始值的`Scale`屬性，也就是 1，但是，不會發生。 當`TwoWay`初始化繫結，目標會設定來源的第一次，這表示`Scale`屬性設定為`Slider`預設值為 0。 當`TwoWay`上設定繫結`Slider`，然後在`Slider`值最初會設定從來源。

您可以將繫結模式設定為`OneWayToSource`中**替代 XAML 繫結**範例：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

現在`Slider`初始化為 1 (預設值`Scale`) 但操作`Slider`不會影響`Scale`屬性，因此這不是很有用。

類別會覆寫的預設繫結模式很有幫助應用程式`TwoWay`涉及`SelectedItem`屬性`ListView`。 預設繫結模式是`OneWayToSource`。 當設定資料繫結上`SelectedItem`屬性來參考在 ViewModel，在 來源 屬性，則該資料來源 屬性會設定從`ListView`選取項目。 不過，在某些情況下，您可能也想`ListView`從 ViewModel 初始化。

**範例設定**頁面會示範這項技術。 此頁面表示應用程式設定，通常會定義在 ViewModel，這類的簡單實作`SampleSettingsViewModel`檔案：

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

每個應用程式設定是儲存在名為方法中的 Xamarin.Forms 屬性字典屬性`SaveState`並從該字典的建構函式載入。 此類別的底端兩個方法可協助簡化 Viewmodel，並將其設定為較不容易發生錯誤。 `OnPropertyChanged`底部的方法有選擇性參數，設定為呼叫端的屬性。 指定的屬性名稱做為字串時，這可避免拼字錯誤。

`SetProperty`類別中的方法會執行更多： 它會比較儲存為欄位的值設為屬性的值，且只會呼叫`OnPropertyChanged`當兩個值是否不相等。

`SampleSettingsViewModel`類別定義的背景色彩的兩個屬性：`BackgroundNamedColor`屬性屬於型別`NamedColor`，類別也隨附於**DataBindingDemos**解決方案。 `BackgroundColor`屬性的類型是`Color`，和取自`Color`屬性`NamedColor`物件。

`NamedColor`類別來列舉所有的靜態公用欄位在 Xamarin.Forms 中使用.NET 反映`Color`結構，並將其儲存以名稱存取靜態集合中`All`屬性：

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

`App`類別內**DataBindingDemos**專案會定義一個名為屬性`Settings`型別的`SampleSettingsViewModel`。 這個屬性會初始化時`App`具現化類別，而`SaveState`方法時，會呼叫`OnSleep`方法呼叫：

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

幾乎所有其他項目會在處理**SampleSettingsPage.xaml**檔案。 `BindingContext`頁面的設定使用`Binding`標記延伸模組： 繫結來源是靜態`Application.Current`屬性，這是執行個體的`App`類別，在專案中，而`Path`設為`Settings`屬性，這是`SampleSettingsViewModel`物件：

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

頁面的所有子系會繼承繫結內容。 在此頁面上的其他繫結的大部分都是中的屬性`SampleSettingsViewModel`。 `BackgroundColor`屬性用來設定`BackgroundColor`屬性`StackLayout`，而`Entry`， `DatePicker`， `Switch`，和`Stepper`屬性所有繫結到 ViewModel 的其他屬性。

`ItemsSource`的屬性`ListView`設定為靜態`NamedColor.All`屬性。 這會填滿`ListView`所有`NamedColor`執行個體。 中的每個項目的`ListView`，項目的繫結內容設定為`NamedColor`物件。 `BoxView`並`Label`中`ViewCell`繫結中的屬性至`NamedColor`。

`SelectedItem`的屬性`ListView`屬於型別`NamedColor`，並繫結至`BackgroundNamedColor`屬性`SampleSettingsViewModel`:

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

預設繫結模式`SelectedItem`是`OneWayToSource`，可從選取的項目設定的 ViewModel 屬性。 `TwoWay`模式可讓`SelectedItem`從 ViewModel 初始化。

不過，當`SelectedItem`如此一來，設定`ListView`不會自動捲動以顯示所選的項目。 在程式碼後置檔案中的一些程式碼是必要項目：

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

第一次執行時，iOS 螢幕擷取畫面，左側會顯示的程式。 建構函式`SampleSettingsViewModel`初始化的背景色彩為白色，而這就是在選取的項目`ListView`:

[![範例設定](binding-mode-images/samplesettings-small.png "範例設定")](binding-mode-images/samplesettings-large.png#lightbox "範例設定")

其他兩個螢幕擷取畫面顯示已變更的設定。 當試驗此頁面，請記得將進入睡眠狀態或終止在裝置或模擬器正在執行的程式。 結束 Visual Studio 偵錯工具中的程式並不會造成`OnSleep`覆寫中`App`要呼叫的類別。

在下一篇文章中，您會看到如何指定[**字串格式化**](string-formatting.md)上所設定的資料繫結`Text`屬性`Label`。


## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [資料繫結 Xamarin.Forms 書籍章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
