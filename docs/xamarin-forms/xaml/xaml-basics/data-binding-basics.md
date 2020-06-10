---
標題：「第4部分。 資料系結基本概念「描述：」資料系結可讓兩個物件的屬性連結，使其中一項變更導致另一項變更。」
ms-chap： xamarin assetid： 342288C3-BB4C-4924-B178-72E112D777BA author： davidbritch ms. 作者： dabritch ms. date： 10/25/2017 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="part-4-data-binding-basics"></a>第 4 部分： 資料繫結的基本概念

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_資料系結可讓兩個物件的屬性連結，使其中一項變更會導致另一項變更。這是非常重要的工具，而資料系結可以完全定義于程式碼中，XAML 則提供快捷方式和便利性。因此，中最重要的標記延伸之一 Xamarin.Forms 就是系結。_

## <a name="data-bindings"></a>資料系結

資料系結會連接兩個物件的屬性，稱為*來源*和*目標*。 在程式碼中，需要兩個步驟： `BindingContext` 目標物件的屬性必須設定為來源物件，而 `SetBinding` 方法（通常與類別一起使用 `Binding` ）必須在目標物件上呼叫，才能將該物件的屬性系結至來源物件的屬性。

目標屬性必須是可系結的屬性，這表示目標物件必須衍生自 `BindableObject` 。 線上 Xamarin.Forms 檔會指出哪些屬性是可系結的屬性。 的屬性 `Label` （例如） `Text` 與可系結的屬性相關聯 `TextProperty` 。

在標記中，您也必須執行程式碼中所需的兩個步驟，但 `Binding` 標記延伸會取代 `SetBinding` 呼叫和 `Binding` 類別。

不過，當您在 XAML 中定義資料系結時，有多種方式可設定 `BindingContext` 目標物件的。 有時候，它是從程式碼後置檔案中設定，有時會使用 `StaticResource` 或 `x:Static` 標記延伸，有時也會做為 `BindingContext` 屬性元素標記的內容。

系結最常用來連接程式的視覺效果與基礎資料模型，通常是在 MVVM （模型視圖-ViewModel）應用程式架構的實現中，如[第5部分所述。從資料系結到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)，但可能會有其他案例。

## <a name="view-to-view-bindings"></a>視圖對視圖系結

您可以定義資料系結，在相同頁面上連結兩個視圖的屬性。 在這種情況下，您可以 `BindingContext` 使用標記延伸來設定目標物件的 `x:Reference` 。

以下是包含 `Slider` 和兩個視圖的 XAML 檔案 `Label` ，其中一個會由值旋轉，另一個則會 `Slider` 顯示 `Slider` 值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderBindingsPage"
             Title="Slider Bindings Page">

    <StackLayout>
        <Label Text="ROTATION"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Slider`包含 `x:Name` `Label` 使用標記延伸的兩個視圖所參考的屬性 `x:Reference` 。

系結延伸模組會 `x:Reference` 定義名 `Name` 為的屬性，以將設定為參考專案的名稱（在此案例中為） `slider` 。 不過， `ReferenceExtension` 定義標記延伸的類別 `x:Reference` 也會定義的 `ContentProperty` 屬性 `Name` ，這表示它不是明確的必要。 僅針對各種不同的，第一個 `x:Reference` 包含 "Name ="，而第二個則不會：

```xaml
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

`Binding`標記延伸本身可以有數個屬性，就像 `BindingBase` 和類別一樣 `Binding` 。 的 `ContentProperty` `Binding` 是 `Path` ，但如果路徑是標記延伸中的第一個專案，則會省略標記延伸的 "path =" 部分 `Binding` 。 第一個範例有 "Path ="，但第二個範例省略它：

```xaml
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

屬性可以全部放在一行，或分成多行：

```xaml
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

執行任何動作都很方便。

請注意 `StringFormat` 第二個標記延伸中的屬性 `Binding` 。 在中，系結 Xamarin.Forms 不會執行任何隱含類型轉換，而且如果您需要將非字串物件顯示為字串，您必須提供類型轉換器或使用 `StringFormat` 。 在幕後， `String.Format` 會使用靜態方法來執行 `StringFormat` 。 這可能是問題，因為 .NET 格式化規格牽涉到大括弧，這也是用來分隔標記延伸。 這會造成混淆 XAML 剖析器的風險。 若要避免這個情況，請以單引號括住整個格式字串：

```xaml
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

以下是正在執行的程式：

[![視圖對視圖系結](data-binding-basics-images/sliderbinding.png)](data-binding-basics-images/sliderbinding-large.png#lightbox)

## <a name="the-binding-mode"></a>系結模式

單一視圖可以有數個屬性的資料系結。 不過，每個視圖只能有一個 `BindingContext` ，因此該視圖上的多個資料系結必須全部參考相同物件的屬性。

此問題和其他問題的解決方法包含 `Mode` 屬性，其設定為列舉的成員 `BindingMode` ：

- `Default`
- `OneWay`-值會從來源傳送到目標
- `OneWayToSource`-值會從目標傳送至來源
- `TwoWay`-值在來源與目標之間的傳輸方式
- `OneTime`：資料會從來源移至目標，但只有在 `BindingContext` 變更時

下列程式示範和系結模式的一種常見用法 `OneWayToSource` `TwoWay` 。 四個 `Slider` 視圖主要是用來控制的 `Scale` 、 `Rotate` 、 `RotateX` 和 `RotateY` 屬性 `Label` 。 一開始，好像這四個屬性 `Label` 應該是資料系結目標，因為每個都是由所設定 `Slider` 。 不過， `BindingContext` 的只能 `Label` 是一個物件，而且有四個不同的滑杆。

基於這個理由，所有的系結都會以看似回溯的方式設定： `BindingContext` 四個滑杆的每一個都會設定為 `Label` ，而系結則設定于滑杆的屬性上 `Value` 。 藉由使用 `OneWayToSource` 和 `TwoWay` 模式，這些 `Value` 屬性可以設定來源屬性，這是的 `Scale` 、 `Rotate` 、 `RotateX` 和 `RotateY` 屬性 `Label` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderTransformsPage"
             Padding="5"
             Title="Slider Transforms Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <!-- Scaled and rotated Label -->
        <Label x:Name="label"
               Text="TEXT"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <!-- Slider and identifying Label for Scale -->
        <Slider x:Name="scaleSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="1" Grid.Column="0"
                Maximum="10"
                Value="{Binding Scale, Mode=TwoWay}" />

        <Label BindingContext="{x:Reference scaleSlider}"
               Text="{Binding Value, StringFormat='Scale = {0:F1}'}"
               Grid.Row="1" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for Rotation -->
        <Slider x:Name="rotationSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="2" Grid.Column="0"
                Maximum="360"
                Value="{Binding Rotation, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationSlider}"
               Text="{Binding Value, StringFormat='Rotation = {0:F0}'}"
               Grid.Row="2" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationX -->
        <Slider x:Name="rotationXSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="3" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationX, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationXSlider}"
               Text="{Binding Value, StringFormat='RotationX = {0:F0}'}"
               Grid.Row="3" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationY -->
        <Slider x:Name="rotationYSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="4" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationY, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationYSlider}"
               Text="{Binding Value, StringFormat='RotationY = {0:F0}'}"
               Grid.Row="4" Grid.Column="1"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

其中三個觀點的系結 `Slider` 是 `OneWayToSource` ，這表示 `Slider` 值會在其的屬性（名為）中造成變更 `BindingContext` `Label` `label` 。 這三個 `Slider` 視圖會導致的 `Rotate` 、 `RotateX` 和 `RotateY` 屬性變更 `Label` 。

不過，屬性的系結 `Scale` 為 `TwoWay` 。 這是因為 `Scale` 屬性的預設值為1，而使用系結 `TwoWay` 會導致 `Slider` 初始值設定為1，而不是0。 如果該系結為 `OneWayToSource` ，則 `Scale` 屬性一開始會從預設值設定為 0 `Slider` 。 `Label`不會顯示，而且可能會對使用者造成一些混淆。

 [![反向系結](data-binding-basics-images/slidertransforms.png)](data-binding-basics-images/slidertransforms-large.png#lightbox)

 > [!NOTE]
 > [`VisualElement`](xref:Xamarin.Forms.VisualElement)類別也具有 [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) 和 [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) 屬性，其會分別調整 `VisualElement` X 軸和 y 軸上的。

## <a name="bindings-and-collections"></a>系結和集合

與樣板相比，XAML 和資料系結的強大功能並不會有任何說明 `ListView` 。

`ListView`定義 `ItemsSource` 類型的屬性 `IEnumerable` ，並顯示該集合中的專案。 這些專案可以是任何類型的物件。 根據預設， `ListView` 會使用 `ToString` 每個專案的方法來顯示該專案。 有時候，這只是您想要的結果，但在許多情況下，只會傳回 `ToString` 物件的完整類別名稱。

不過，集合中的專案 `ListView` 可以使用*範本*以任何您想要的方式顯示，這牽涉到衍生自的類別 `Cell` 。 會針對中的每個專案複製範本 `ListView` ，並將範本上設定的資料系結轉移至個別的複製。

通常，您會想要使用類別來建立這些專案的自訂資料格 `ViewCell` 。 程式碼中的這個進程有點雜亂，但是在 XAML 中，它會變得非常簡單。

包含在 XamlSamples 專案中的類別稱為 `NamedColor` 。 每個 `NamedColor` 物件都有 `Name` `FriendlyName` 類型的和屬性 `string` ，以及 `Color` 類型的屬性 `Color` 。 此外， `NamedColor` 具有141靜態唯讀欄位，類型 `Color` 對應于類別中定義的色彩 Xamarin.Forms `Color` 。 靜態的函式會建立一個 `IEnumerable<NamedColor>` 集合，其中包含 `NamedColor` 對應至這些靜態欄位的物件，並將其指派給其公用靜態 `All` 屬性。

使用標記延伸，將靜態屬性設定為的很 `NamedColor.All` `ItemsSource` `ListView` 容易 `x:Static` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

結果顯示會建立專案真正的類型 `XamlSamples.NamedColor` ：

[![系結至集合](data-binding-basics-images/listview1.png)](data-binding-basics-images/listview1-large.png#lightbox)

這不是太多資訊，而 `ListView` 是可滾動和選取。

若要定義專案的範本，您會想要將 `ItemTemplate` 屬性分解為屬性元素，並將它設定為 `DataTemplate` ，然後參考 `ViewCell` 。 在的 `View` 屬性中， `ViewCell` 您可以定義一或多個 views 的版面配置，以顯示每個專案。 以下是一個簡單的範例：

```xaml
<ListView ItemsSource="{x:Static local:NamedColor.All}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.View>
                    <Label Text="{Binding FriendlyName}" />
                </ViewCell.View>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

> [!NOTE]
> 資料格的系結來源和資料格的子系是 `ListView.ItemsSource` 集合。

`Label`元素會設定為的 `View` 屬性 `ViewCell` 。 （ `ViewCell.View` 不需要標記，因為 `View` 屬性是的 content 屬性 `ViewCell` ）。此標記會顯示 `FriendlyName` 每個物件的屬性 `NamedColor` ：

[![使用 DataTemplate 系結至集合](data-binding-basics-images/listview2.png)](data-binding-basics-images/listview2-large.png#lightbox)

好多了。 現在只需要以更多資訊和實際色彩來裝飾專案範本。 為了支援此範本，已在頁面的資源字典中定義一些值和物件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <OnPlatform x:Key="boxSize"
                        x:TypeArguments="x:Double">
                <On Platform="iOS, Android, UWP" Value="50" />
            </OnPlatform>

            <OnPlatform x:Key="rowHeight"
                        x:TypeArguments="x:Int32">
                <On Platform="iOS, Android, UWP" Value="60" />
            </OnPlatform>

            <local:DoubleToIntConverter x:Key="intConverter" />

        </ResourceDictionary>
    </ContentPage.Resources>

    <ListView ItemsSource="{x:Static local:NamedColor.All}"
              RowHeight="{StaticResource rowHeight}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <StackLayout Padding="5, 5, 0, 5"
                                 Orientation="Horizontal"
                                 Spacing="15">

                        <BoxView WidthRequest="{StaticResource boxSize}"
                                 HeightRequest="{StaticResource boxSize}"
                                 Color="{Binding Color}" />

                        <StackLayout Padding="5, 0, 0, 0"
                                     VerticalOptions="Center">

                            <Label Text="{Binding FriendlyName}"
                                   FontAttributes="Bold"
                                   FontSize="Medium" />

                            <StackLayout Orientation="Horizontal"
                                         Spacing="0">
                                <Label Text="{Binding Color.R,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat='R={0:X2}'}" />

                                <Label Text="{Binding Color.G,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', G={0:X2}'}" />

                                <Label Text="{Binding Color.B,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', B={0:X2}'}" />
                            </StackLayout>
                        </StackLayout>
                    </StackLayout>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

請注意，使用 `OnPlatform` 來定義的大小和資料 `BoxView` 列的高度 `ListView` 。 雖然所有平臺的值都相同，但您可以輕鬆地調整標記，讓其他值微調顯示。

## <a name="binding-value-converters"></a>繫結值轉換器

先前的**ListView 示範**XAML 檔案會顯示結構的個別 `R` 、 `G` 和 `B` 屬性 Xamarin.Forms `Color` 。 這些屬性的類型為 `double` ，而範圍從0到1。 如果您想要顯示十六進位值，則不能直接使用搭配「 `StringFormat` X2」格式規格。 這只適用于整數，而 `double` 值必須乘以255。

這個小問題是透過*值轉換器*（也稱為系結*轉換器*）來解決。 這是可實作為介面的類別 `IValueConverter` ，這表示它有兩個名為和的方法 `Convert` `ConvertBack` 。 `Convert`當值從來源傳送到目標時，會呼叫方法; `ConvertBack` 會呼叫方法，以便從目標傳送至或系結中的來源 `OneWayToSource` `TwoWay` ：

```csharp
using System;
using System.Globalization;
using Xamarin.Forms;

namespace XamlSamples
{
    class DoubleToIntConverter : IValueConverter
    {
        public object Convert(object value, Type targetType,
                              object parameter, CultureInfo culture)
        {
            double multiplier;

            if (!Double.TryParse(parameter as string, out multiplier))
                multiplier = 1;

            return (int)Math.Round(multiplier * (double)value);
        }

        public object ConvertBack(object value, Type targetType,
                                  object parameter, CultureInfo culture)
        {
            double divider;

            if (!Double.TryParse(parameter as string, out divider))
                divider = 1;

            return ((double)(int)value) / divider;
        }
    }
}
```

方法不會 `ConvertBack` 在此程式中扮演角色，因為系結只是從來源到目標的一種方式。

系結會參考具有屬性的系結轉換器 `Converter` 。 系結轉換器也可以接受使用屬性指定的參數 `ConverterParameter` 。 針對某些多功能，這就是指定乘數的方式。 系結轉換器會檢查轉換器參數是否有有效的 `double` 值。

轉換器會在資源字典中具現化，因此可在多個系結之間共用：

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

有三個數據系結參考這個單一實例。 請注意， `Binding` 標記延伸包含內嵌 `StaticResource` 標記延伸：

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

結果如下：

[![使用 DataTemplate 和轉換器系結至集合](data-binding-basics-images/listview3.png)](data-binding-basics-images/listview3-large.png#lightbox)

在 `ListView` 處理可能會在基礎資料中動態發生的變更，但只有在您採取特定步驟時，才會非常複雜。 如果在執行時間期間指派給屬性的專案集合 `ItemsSource` `ListView` （也就是可以在集合中加入或移除專案），請使用 `ObservableCollection` 類別做為這些專案。 `ObservableCollection`會執行 `INotifyCollectionChanged` 介面，而且 `ListView` 會安裝事件的處理常式 `CollectionChanged` 。

如果專案本身的屬性在執行時間變更，則集合中的專案應該 `INotifyPropertyChanged` 使用事件來執行介面，並對屬性值發出信號變更 `PropertyChanged` 。 這會在本系列的下一個部分中示範，[第5部分。從資料系結到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

## <a name="summary"></a>摘要

資料系結提供強大的機制，可在頁面中的兩個物件之間，或是在視覺物件與基礎資料之間連結屬性。 但是當應用程式開始使用資料來源時，熱門的應用程式架構模式就會開始成為一個有用的範例。 第5部分涵蓋這項功能[。從資料系結到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

## <a name="related-links"></a>相關連結

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第1部分。使用 XAML 消費者入門（範例）](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第2部分。基本 XAML 語法（範例）](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第3部分。XAML 標記延伸（範例）](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第5部分：從資料系結到 MVVM （範例）](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
