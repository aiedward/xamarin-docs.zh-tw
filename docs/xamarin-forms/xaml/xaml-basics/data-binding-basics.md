---
title: 第 4 部分。 資料繫結的基本概念
description: 資料繫結允許的連結，讓其中的變更會導致變更另兩個物件的屬性。 這是相當有用的工具，以及資料繫結可以定義完全以程式碼中，XAML 提供捷徑和便利性。 因此，在 Xamarin.Forms 中最重要的標記延伸的其中一個繫結。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: 4e3e090d826aa46d503f8c612250fd5122bc703e
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2018
---
# <a name="part-4-data-binding-basics"></a>第 4 部分。 資料繫結的基本概念

_資料繫結允許的連結，讓其中的變更會導致變更另兩個物件的屬性。這是相當有用的工具，以及資料繫結可以定義完全以程式碼中，XAML 提供捷徑和便利性。因此，在 Xamarin.Forms 中最重要的標記延伸的其中一個繫結。_

## <a name="data-bindings"></a>資料繫結

資料繫結連接屬性的兩個物件，稱為*來源*和*目標*。 程式碼中，兩個步驟所需：`BindingContext`目標物件的屬性必須設定為來源物件，而`SetBinding`方法 (通常用於搭配`Binding`類別) 上的屬性，繫結目標物件必須呼叫物件的來源物件的屬性。

目標屬性必須是可繫結的屬性，表示目標物件必須衍生自`BindableObject`。 線上 Xamarin.Forms 文件會指出屬性可繫結屬性。 屬性的`Label`例如`Text`可繫結屬性與相關聯`TextProperty`。

在標記中，您也必須執行相同的兩個步驟所需的程式碼中，不同之處在於`Binding`標記延伸會取代`SetBinding`呼叫和`Binding`類別。

不過，當您在 XAML 中定義資料繫結，有多種方式可以設定`BindingContext`目標物件。 有時候設定從程式碼後置檔案，有時使用`StaticResource`或`x:Static`標記延伸，有時也做內容`BindingContext`屬性項目標記。

繫結最常使用與基礎資料模型，通常是在 MVVM (模型-檢視-ViewModel) 應用程式架構，實現連線程式的視覺效果中所述[第 5 部分。從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)，但可能會有其他案例。

## <a name="view-to-view-bindings"></a>檢視可檢視繫結

您可以定義資料繫結至連結的相同頁面上的兩個檢視的屬性。 在此情況下，您將設定`BindingContext`目標物件使用的`x:Reference`標記延伸。

以下是包含的 XAML 檔案`Slider`和兩個`Label`檢視，其中旋轉`Slider`值，而另一個則會顯示`Slider`值：

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

`Slider`包含`x:Name`屬性所參考的兩個`Label`檢視使用`x:Reference`標記延伸。

`x:Reference`繫結延伸模組會定義名為的屬性`Name`設為 參考的項目，名稱在此情況下`slider`。 不過，`ReferenceExtension`類別來定義`x:Reference`標記延伸也定義`ContentProperty`屬性`Name`，這表示，不需要明確。 只針對各種不同，第一個`x:Reference`包括 「 名稱 ="但第二個則不會：

```csharp
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

`Binding`本身的標記延伸可以有數個屬性，就像是`BindingBase`和`Binding`類別。 `ContentProperty`如`Binding`是`Path`，但 「 路徑 ="可以省略標記延伸的一部分，如果路徑中的第一個項目`Binding`標記延伸。 第一個範例有 「 路徑 ="，但第二個範例則會省略它：

```csharp
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

屬性都可以在同一行，或是分隔成多行：

```csharp
Text="{Binding Value, 
               StringFormat='The angle is {0:F0} degrees'}"
```

請勿指定任何一種方便。

請注意`StringFormat`中第二個屬性`Binding`標記延伸。 透過 Xamarin.Forms，在繫結不會執行任何隱含類型轉換，以及如果您需要以字串顯示非字串物件必須提供類型轉換器，或使用`StringFormat`。 在幕後，靜態`String.Format`方法用來實作`StringFormat`。 這是可能會造成問題，因為.NET 格式規格包含大括號，也可以用來分隔標記延伸。 這會建立使人混淆的 XAML 剖析器的風險。 若要避免的將整個格式化字串放在單引號中：

```csharp
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

以下是執行的程式：

[![](data-binding-basics-images/sliderbinding.png "檢視可檢視繫結")](data-binding-basics-images/sliderbinding-large.png#lightbox "檢視來檢視繫結 ")

## <a name="the-binding-mode"></a>繫結模式 

單一檢視可以有數個屬性上的資料繫結。 不過，每個檢視只能有一個`BindingContext`，因此該檢視表上的多個資料繫結都必須參考相同物件的屬性。

這個資料庫和其他問題的解決方法包括`Mode`屬性設定為隸屬`BindingMode`列舉型別：

- `Default` 
- `OneWay` — 值會從來源傳送到目標
- `OneWayToSource` — 值會從目標傳送到來源
- `TwoWay` — 值會傳送來源和目標之間的這兩種方式

下列程式會示範的常見用法之一`OneWayToSource`和`TwoWay`繫結模式。 四個`Slider`檢視要控制`Scale`， `Rotate`， `RotateX`，和`RotateY`屬性`Label`。 首先，它看起來好像這四個屬性的`Label`應該是資料繫結的目標，因為每個已設定`Slider`。 不過，`BindingContext`的`Label`可以只有一個物件，而且有四個不同的滑桿。

因此，所有繫結中設定看似回溯的方式：`BindingContext`每四個滑桿設定為`Label`，和繫結上設定`Value`滑桿的屬性。 使用`OneWayToSource`和`TwoWay`模式中，這些`Value`屬性可以設定來源的屬性，也就是`Scale`， `Rotate`， `RotateX`，和`RotateY`屬性`Label`:

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

上的三種繫結`Slider`檢視`OneWayToSource`，亦即，`Slider`值的屬性中會變更其`BindingContext`，也就是`Label`名為`label`。 這三個`Slider`檢視導致變更`Rotate`， `RotateX`，和`RotateY`屬性`Label`。

不過，繫結`Scale`屬性是`TwoWay`。 這是因為`Scale`屬性具有預設值是 1，並使用`TwoWay`繫結原因`Slider`初始設為 1，而非 0 的值。 如果該繫結`OneWayToSource`、`Scale`屬性一開始會設定為從 0`Slider`預設值。 `Label`不可見，及卻可能會導致一些混淆使用者。

 [![](data-binding-basics-images/slidertransforms.png "向後繫結")](data-binding-basics-images/slidertransforms-large.png#lightbox "向後繫結")

## <a name="bindings-and-collections"></a>繫結和集合

執行任何動作說明強大的 XAML 和資料繫結，優於樣板化`ListView`。

`ListView` 定義`ItemsSource`型別的屬性`IEnumerable`，而且會顯示在該集合中的項目。 這些項目可以是任何類型的物件。 根據預設，`ListView`使用`ToString`方法的每個項目以顯示項目。 這有時是只想，但在許多情況下，`ToString`傳回只有完整類別名稱的物件。

不過中的項目`ListView`集合可以顯示您想要透過使用的任何方式*範本*，其中包含衍生自類別`Cell`。 每個項目中，已再製範本`ListView`，並已設定於範本的資料繫結會傳送到個別的複製程式碼。

通常，您想要建立自訂的儲存格，使用這些項目的`ViewCell`類別。 此程序會有點混亂的程式碼中，但在 XAML 中會變得非常簡單。

包含在 XamlSamples 專案是一種類別稱為`NamedColor`。 每個`NamedColor`物件具有`Name`和`FriendlyName`型別的屬性`string`，和`Color`型別的屬性`Color`。 此外， `NamedColor` 141 靜態唯讀欄位的型別`Color`Xamarin.Forms 中定義的色彩對應`Color`類別。 靜態建構函式建立`IEnumerable<NamedColor>`集合，其中包含`NamedColor`物件對應至這些靜態欄位，並將它指派給它的公用靜態`All`屬性。

設定靜態`NamedColor.All`屬性`ItemsSource`的`ListView`很容易使用`x:Static`標記延伸：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

結果顯示建立的項目是真正的型別`XamlSamples.NamedColor`:

[![](data-binding-basics-images/listview1.png "繫結至集合")](data-binding-basics-images/listview1-large.png#lightbox "繫結至集合")

並不多的資訊，但`ListView`是可捲動和可選取。

若要定義的項目範本，您要細分`ItemTemplate`屬性做為屬性項目，並將它設定為`DataTemplate`，它會再參考`ViewCell`。 若要`View`屬性`ViewCell`您可以定義的一個或多個檢視，以顯示每個項目配置。 以下是簡單的範例：

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

`Label`元素設定為`View`屬性`ViewCell`。 (`ViewCell.View`標記不需要因為`View`屬性是內容屬性`ViewCell`。)這個標記會顯示`FriendlyName`每個屬性`NamedColor`物件：

[![](data-binding-basics-images/listview2.png "繫結至具有 DataTemplate 集合")](data-binding-basics-images/listview2-large.png#lightbox "繫結至含 DataTemplate 的集合")

更好。 現在需要是打扮項目範本的詳細資訊與實際的色彩。 若要支援此範本，有些值和物件中已定義頁面的資源字典：

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

請注意，使用`OnPlatform`定義的大小`BoxView`和高度`ListView`資料列。 雖然所有的三個平台的值相同，可以輕鬆地調整為其他值以微調顯示標記。 

## <a name="binding-value-converters"></a>繫結值轉換器

先前**ListView 示範**XAML 檔案會顯示個別`R`， `G`，和`B`屬性 Xamarin.Forms`Color`結構。 這些屬性屬於類型`double`和範圍從 0 到 1。 如果您想要顯示的十六進位值，您只是無法使用`StringFormat`與格式規格 」 X2"。 只適用於整數和，除了`double`需要先乘以 255 的值。

這個小問題已解決*值轉換器*，也稱為*繫結轉換器*。 這是一個類別，實作`IValueConverter`介面，這表示它有兩個方法，名為`Convert`和`ConvertBack`。 `Convert`呼叫方法時，值會從來源傳送到目標;`ConvertBack`方法稱為傳輸從目標到來源`OneWayToSource`或`TwoWay`繫結：

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

`ConvertBack`方法不是扮演 role 在此程式因為繫結只有其中一種方式從來源到目標。 

繫結參考繫結轉換器，並`Converter`屬性。 繫結轉換也可以接受參數，指定為`ConverterParameter`屬性。 對於某些維持通用性，這是指定乘數的方式。 繫結轉換器會檢查有效的轉換器參數`double`值。

轉換器具現化資源字典中，因此可以共用多個繫結：

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

三個資料繫結參考此單一執行個體。 請注意，`Binding`標記延伸包含內嵌`StaticResource`標記延伸：

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

以下是結果：

[![](data-binding-basics-images/listview3.png "繫結至具有 DataTemplate 和轉換器集合")](data-binding-basics-images/listview3-large.png#lightbox "繫結至含 DataTemplate 和轉換器的集合")

`ListView`功能已經相當成熟中處理變更，以動態方式可能會發生在基礎資料，但是只有採取某些步驟。 如果項目集合指派給`ItemsSource`屬性`ListView`執行階段期間的變更 — 是，如果項目可以加入或從集合中移除 — 使用`ObservableCollection`這些項目的類別。 `ObservableCollection` 實作`INotifyCollectionChanged`介面，和`ListView`將安裝的處理常式`CollectionChanged`事件。

如果在執行階段變更的項目本身的屬性，則集合中的項目應該實作`INotifyPropertyChanged`屬性值使用的介面和信號變更`PropertyChanged`事件。 這示範於本系列的下一個部分[第 5 部分。從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

## <a name="summary"></a>總結

資料繫結會提供連結視覺物件或頁面內的兩個物件之間的屬性和基礎資料的強大機制。 但當應用程式開始使用資料來源時，開始出現時的實用範例為熱門應用程式的架構模式。 這在講述[第 5 部分。從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。



## <a name="related-links"></a>相關連結

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：開始使用 XAML （範例）](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 2 部分：基本 XAML 語法 （範例）](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 3 部分：XAML 標記延伸 （範例）](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 5 部分：從資料繫結至 MVVM （範例）](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
