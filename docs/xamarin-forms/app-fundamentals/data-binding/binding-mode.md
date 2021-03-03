---
title: Xamarin.Forms 系結模式
description: 本文說明如何使用繫結模式控制來源和目標的資訊流程，而繫結模式是使用 BindingMode 列舉的成員來指定。 每個可繫結的屬性都具有預設繫結模式，表示當該屬性是資料繫結目標時模式即已生效。
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/01/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1ab0603555c55d969e1bee8ae709df3cac23de73
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373636"
---
# <a name="xamarinforms-binding-mode"></a>Xamarin.Forms 系結模式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/databindingdemos)

在[前一篇文章](basic-bindings.md)中，[替代程式碼繫結] 和 [替代 XAML 繫結] 頁面顯示的 `Label`，其 `Scale` 屬性繫結至 `Slider` 的 `Value` 屬性。 因為 `Slider` 初始值為 0，這導致 `Label` 的 `Scale` 屬性設為 0，而不是 1，並讓 `Label` 消失。

在 [**DataBindingDemos**](/samples/xamarin/xamarin-forms-samples/databindingdemos) 範例中，[反向繫結] 頁面與上一篇文章中的程式類似，不同之處在於其資料繫結是定義於 `Slider` 之上，而非 `Label`：

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

這乍看之下似乎是反向：現在，`Label` 是資料繫結來源，而 `Slider` 則是目標。 繫結參考 `Label` 的 `Opacity` 屬性，其預設值為 1。

如您所料，`Slider` 會從 `Label` 的初始 `Opacity` 值初始化為值 1。 如左側的 iOS 螢幕擷取畫面所示：

[![反向綁定](binding-mode-images/reversebinding-small.png "反向綁定")](binding-mode-images/reversebinding-large.png#lightbox "反向綁定")

但您可能會驚訝 `Slider` ，因為 Android 螢幕擷取畫面所示，您可能會繼續運作。 這似乎表示由於初始化如我們預期般運作，因此當繫結目標是 `Slider` 而非 `Label` 時，資料繫結運作效果較佳。

**反向繫結** 範例和稍早範例之間的差異與「繫結模式」有關。

## <a name="the-default-binding-mode"></a>預設繫結模式

系結模式是以列舉的成員來指定 [`BindingMode`](xref:Xamarin.Forms.BindingMode) ：

- [`Default`](xref:Xamarin.Forms.BindingMode.Default)
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)&ndash;資料會在來源與目標之間進行這兩種方式
- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay)&ndash;資料會從來源移到目標
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource)&ndash;資料會從目標移到來源
- [`OneTime`](xref:Xamarin.Forms.BindingMode.OneWayToSource)&ndash;資料會從來源移到目標，但只有在 `BindingContext` 變更 (新的 Xamarin.Forms 3.0) 

每個可系結屬性都有預設系結模式，會在建立可系結屬性時設定，且可從 [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) 物件的屬性取得 `BindableProperty` 。 這個預設繫結模式表示當該屬性是資料繫結目標時，模式即已生效。

針對 `Rotation`、`Scale` 和 `Opacity` 等大部分屬性，預設繫結模式是 `OneWay`。 當這些屬性是資料繫結目標時，即會從來源設定目標屬性。

不過，`Slider` 的 `Value` 屬性預設繫結模式是 `TwoWay`。 這表示當 `Value` 屬性是資料繫結目標時，即會從來源設定目標 (如往常般)，但也會從目標設定來源。 這樣才可以從初始 `Opacity` 值設定 `Slider`。

這個雙向繫結看似會建立無限迴圈，但其實不會發生這種情況。 除非屬性實際變更，否則可繫結的屬性就不會通知屬性變更。 這可防止無限迴圈。

### <a name="two-way-bindings"></a>雙向繫結

大多數可繫結屬性的預設繫結模式都是 `OneWay`，但下列屬性的預設繫結模式為 `TwoWay`：

- `DatePicker` 的 `Date` 屬性
- `Editor`、`Entry`、`SearchBar` 及 `EntryCell` 的 `Text` 屬性
- `ListView` 的 `IsRefreshing` 屬性
- `MultiPage` 的 `SelectedItem` 屬性
- `Picker` 的 `SelectedIndex` 和 `SelectedItem` 屬性
- `Slider`和 `Stepper` 的 `Value` 屬性
- `Switch` 的 `IsToggled` 屬性
- `SwitchCell` 的 `On` 屬性
- `TimePicker` 的 `Time` 屬性

基於一項非常重要的理由，我們將這些特定屬性定義為 `TwoWay`：

當您搭配使用資料繫結與 Model-View-ViewModel (MVVM) 應用程式架構時，ViewModel 類別是資料繫結來源，而檢視 (其中包含 `Slider` 這類檢視) 是資料繫結的目標。 比起先前範例中的繫結，MVVM 繫結更類似於 **反向繫結** 範例。 您很可能想要使用 ViewModel 中的對應屬性值來初始化頁面上的每個檢視，但在檢視中的變更也會影響 ViewModel 屬性。

預設繫結模式為 `TwoWay` 的屬性，是最有可能在 MVVM 案例中使用的屬性。

### <a name="one-way-to-source-bindings"></a>單向至來源的繫結

唯讀可繫結屬性的預設繫結模式為 `OneWayToSource`。 只有一個讀取/寫入可繫結屬性的預設繫結模式為 `OneWayToSource`：

- `ListView` 的 `SelectedItem` 屬性

其原理是 `SelectedItem` 屬性的繫結應該會導致設定繫結來源。 本文稍後的範例會覆寫該行為。

### <a name="one-time-bindings"></a>一次性繫結

有數個屬性的預設繫結模式為 `OneTime`，包含 `Entry` 的 `IsTextPredictionEnabled` 屬性。

繫結模式為 `OneTime` 的目標屬性只有在繫結內容變更時才會更新。 針對這些目標屬性的繫結，這麼做可以簡化繫結基礎結構，因為不需要監視來源屬性中的變更。

## <a name="viewmodels-and-property-change-notifications"></a>ViewModel 和屬性變更通知

[Simple Color Selector] \(簡易色彩選取器\) 頁面會示範如何使用簡易的 ViewModel。 資料繫結可讓使用者透過色調、飽和度和亮度三個 `Slider` 項目來選取色彩。

ViewModel 是資料繫結來源。 ViewModel「不會」定義可繫結的屬性，但它會實作通知機制，以在屬性值變更時通知繫結基礎結構。 此通知機制是 [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) 介面，會定義名為的單一事件 [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) 。 實作這個介面的類別通常會在其中一個公用屬性變更值時引發事件。 如果屬性從不變更，就不需要引發事件  (`BindableObject` 也會實作 `INotifyPropertyChanged` 介面，並在可繫結的屬性值變更時引發 `PropertyChanged` 事件)。

`HslColorViewModel` 類別會定義五個屬性：`Hue`、`Saturation`、`Luminosity` 和 `Color` 則相互關聯。 當這三個色彩元件其中之一變更值時，即會重新計算 `Color` 屬性，並引發所有四個屬性的 `PropertyChanged` 事件：

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

當 `Color` 屬性變更時，`NamedColor` 類別中的靜態 `GetNearestColorName` 方法 (也包含在 **DataBindingDemos** 方案中) 會取得最接近的具名色彩，並設定 `Name` 屬性。 這個 `Name` 屬性具有私用 `set` 存取子，因此無法從類別外部設定。

當 ViewModel 設定為繫結來源時，繫結基礎結構會將處理常式附加至 `PropertyChanged` 事件。 如此一來，繫結就可以收到屬性變更的通知，然後從變更的值來設定目標屬性。

不過，當目標屬性 (或目標屬性上的 `Binding` 定義) 具有 `OneTime` 的 `BindingMode` 時，繫結基礎結構就不需要將處理常式附加到 `PropertyChanged` 事件。 只有在 `BindingContext` 變更時 (而非來源屬性本身變更時)，才會更新目標屬性。

[Simple Color Selector] \(簡易色彩選取器\) XAML 檔案會具現化頁面資源字典中的 `HslColorViewModel`，並初始化 `Color` 屬性。 `Grid` 的 `BindingContext` 屬性會設為 `StaticResource` 繫結延伸模組以參考該資源：

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

`BoxView`、`Label` 和三個 `Slider` 檢視會繼承 `Grid` 的繫結內容。 這些檢視都是參考 ViewModel 中來源屬性的繫結目標。 若是 `BoxView` 的 `Color` 屬性和 `Label` 的 `Text` 屬性，資料繫結為 `OneWay`：檢視中的屬性會從 ViewModel 屬性來設定。

不過，`Slider` 的 `Value` 屬性則為 `TwoWay`。 這可讓每個 `Slider` 從 ViewModel 設定，而 ViewModel 則從每個 `Slider` 來設定。

第一次執行程式時，`BoxView`、`Label` 和三個 `Slider` 項目都會從 ViewModel 來設定，並以 ViewModel 具現化時設定的初始 `Color` 屬性為依據。 如左側的 iOS 螢幕擷取畫面所示：

[![簡單的色彩選取器](binding-mode-images/simplecolorselector-small.png "簡單的色彩選取器")](binding-mode-images/simplecolorselector-large.png#lightbox "簡單的色彩選取器")

當您操作滑杆時， `BoxView` 和 `Label` 會據以更新，如 Android 螢幕擷取畫面所示。

具現化資源字典中的 ViewModel 是一個常見的方法。 您也可以針對 `BindingContext` 屬性，具現化屬性項目標記內的 ViewModel。 在 [Simple Color Selector] \(簡易色彩選取器\) XAML 檔案中，嘗試從資源字典中移除 `HslColorViewModel`，並將它設定為 `Grid` 的 `BindingContext` 屬性，如下所示：

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>

    ···

</Grid>
```

您可以藉由各種不同的方式來設定繫結內容。 有時候，程式碼後置檔案會具現化 ViewModel，並將它設定為頁面的 `BindingContext` 屬性。 這些是所有有效的方法。

## <a name="overriding-the-binding-mode"></a>覆寫繫結模式

如果目標屬性的預設系結模式不適用於特定的資料系結，則可以藉由將 [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) (的屬性 `Binding` 或 [`Mode`](xref:Xamarin.Forms.Xaml.BindingExtension.Mode) `Binding`) 標記延伸的屬性設定為列舉的其中一個成員 `BindingMode` ，來覆寫它。

不過，將 `Mode` 屬性設為 `TwoWay` 不一定一律如預期般有效。 例如，嘗試修改 [替代 XAML 繫結] XAML 檔案，以在繫結定義中包含 `TwoWay`：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

您可能預期 `Slider` 會初始化為 `Scale` 屬性的初始值，也就是 1，但實際並不會發生。 當 `TwoWay` 繫結初始化時，會先從來源設定目標，這表示 `Scale` 屬性會設為 `Slider` 預設值 0。 當 `Slider` 設定了 `TwoWay` 繫結時，一開始會從來源設定 `Slider`。

您可以將 [替代 XAML 繫結] 範例中的繫結模式設定為 `OneWayToSource`：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

現在，`Slider` 即會初始化為 1 (`Scale` 的預設值) 但操作 `Slider` 時不會影響 `Scale` 屬性，因此並不太實用。

> [!NOTE]
> [`VisualElement`](xref:Xamarin.Forms.VisualElement)類別也會定義 [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) 和 [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) 屬性，以 `VisualElement` 水準和垂直方向來調整不同的大小。

若要使用 `TwoWay` 覆寫預設繫結模式，其中一個實用的應用程式牽涉到 `ListView` 的 `SelectedItem` 屬性。 預設繫結模式為 `OneWayToSource`。 當 `SelectedItem` 屬性設定了資料繫結以參考 ViewModel 中的來源屬性時，則會從 `ListView` 選取項目設定該來源屬性。 不過，在某些情況下，您可能也希望從 ViewModel 初始化 `ListView`。

[範例設定] 頁面會示範這項技術。 此頁面表示簡易的應用程式設定實作，其通常定義於 ViewModel 中，例如這個 `SampleSettingsViewModel` 檔案：

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
        if (object.Equals(storage, value))
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

每個應用程式設定都是一個屬性，此屬性會儲存至方法中的屬性字典，該方法會在函式中 Xamarin.Forms 名為 `SaveState` 和從該字典載入。 此類別底端的兩個方法可協助簡化 ViewModel，並使其較不容易發生錯誤。 `OnPropertyChanged` 方法底部有設為呼叫屬性的選擇性參數。 將屬性名稱指定為字串時，這可避免拼字錯誤。

類別中的 `SetProperty` 方法可執行更多作業：它會比較設為屬性以及儲存為欄位的值，並只會在兩個值不相等時呼叫 `OnPropertyChanged`。

`SampleSettingsViewModel` 類別定義背景色彩的兩個屬性：`BackgroundNamedColor` 屬性屬於 `NamedColor` 類型，其也是隨附於 **DataBindingDemos** 解決方案的類別。 `BackgroundColor` 屬性屬於 `Color` 類型，並取自 `NamedColor` 物件的 `Color` 屬性。

`NamedColor`類別會使用 .net 反映來列舉結構中的所有靜態公用欄位 Xamarin.Forms `Color` ，並將其名稱儲存在可從靜態屬性存取的集合中 `All` ：

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

**DataBindingDemos** 專案中的 `App` 類別會定義一個名為 `Settings` 的屬性，其類型為 `SampleSettingsViewModel`。 當具現化 `App` 類別時會初始化這個屬性，而在呼叫 `OnSleep` 方法時會呼叫 `SaveState` 方法：

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

如需應用程式生命週期方法的詳細資訊，請參閱 [**應用程式生命週期**](~/xamarin-forms/app-fundamentals/app-lifecycle.md)一文。

幾乎所有其他項目都是在 **SampleSettingsPage.xaml** 檔案中處理。 您可使用 `Binding` 標記延伸來設定頁面的 `BindingContext`：繫結來源是靜態 `Application.Current` 屬性，這是專案中 `App` 類別的執行個體，而 `Path` 設為 `Settings` 屬性，其為 `SampleSettingsViewModel` 物件：

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

頁面的所有子系會繼承繫結內容。 此頁面上的其他繫結大部分都是 `SampleSettingsViewModel` 中的屬性。 `BackgroundColor` 屬性用來設定 `StackLayout` 的 `BackgroundColor` 屬性，而 `Entry`、`DatePicker`、`Switch` 和 `Stepper` 屬性會全部繫結到 ViewModel 中的其他屬性。

`ListView` 的 `ItemsSource` 屬性設定為靜態 `NamedColor.All` 屬性。 這會將 `ListView` 填滿所有 `NamedColor` 執行個體。 針對 `ListView` 中的每個項目，項目的繫結內容會設定為 `NamedColor` 物件。 `ViewCell` 中的 `BoxView` 和 `Label` 會繫結至 `NamedColor` 中的屬性。

`ListView` 的 `SelectedItem` 屬性屬於 `NamedColor` 類型，並繫結至 `SampleSettingsViewModel` 的 `BackgroundNamedColor` 屬性：

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

`SelectedItem` 的預設繫結模式是 `OneWayToSource`，其可從選取的項目來設定 ViewModel 屬性。 `TwoWay` 模式可讓 `SelectedItem` 從 ViewModel 初始化。

不過，以此方式設定 `SelectedItem` 時，`ListView` 不會自動捲動以顯示所選的項目。 在程式碼後置檔案中有些程式碼是必要項目：

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

第一次執行程式時，iOS 螢幕擷取畫面左側會顯示程式。 `SampleSettingsViewModel` 中的建構函式會將背景色彩初始化為白色，而這就是 `ListView` 中選取的項目：

[![範例設定](binding-mode-images/samplesettings-small.png "範例設定")](binding-mode-images/samplesettings-large.png#lightbox "範例設定")

另一個螢幕擷取畫面顯示已更改的設定。 當試驗此頁面時，請記得讓程式進入睡眠狀態，或終止裝置或模擬器正在執行的程式。 終止 Visual Studio 偵錯工具中的程式，並不會導致呼叫 `App` 類別中的 `OnSleep` 覆寫。

在下一篇文章中，您將了解如何指定資料繫結的 [**字串格式化**](string-formatting.md)，而該資料繫結是在 `Label` 的 `Text` 屬性上設定。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
- [書籍中的資料系結章節 Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)