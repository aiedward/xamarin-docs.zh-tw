---
title: 第 4 部分。 資料繫結的基本概念
description: 資料繫結可讓連結，以便變更一個變更造成另兩個物件的屬性。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: e0ad67db0671996e594f9c5d48b329a5d676fc1d
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563429"
---
# <a name="part-4-data-binding-basics"></a>第 4 部分。 資料繫結的基本概念

_資料繫結可讓連結，以便變更一個變更造成另兩個物件的屬性。這是相當有用的工具，而且雖然資料繫結可以定義完全以程式碼中，XAML 提供捷徑] 與 [便利性。因此，最重要的標記延伸模組，在 Xamarin.Forms 中的其中一個繫結。_

## <a name="data-bindings"></a>資料繫結

資料繫結連接屬性的兩個物件，稱為*來源*並*目標*。 在程式碼，則需要兩個步驟：`BindingContext`目標物件的屬性必須設定為來源物件，而`SetBinding`方法 (通常用於搭配`Binding`類別) 上的屬性繫結目標物件必須呼叫物件的來源物件的屬性。

目標屬性必須是可繫結的屬性，表示目標物件必須衍生自`BindableObject`。 線上 Xamarin.Forms 文件會指出哪一個屬性是可繫結的屬性。 屬性`Label`這類`Text`可繫結的屬性相關聯`TextProperty`。

在標記中，您也必須執行相同的兩個步驟所需的程式碼中，不同之處在於`Binding`標記延伸模組會取代`SetBinding`呼叫和`Binding`類別。

不過，當您在 XAML 中定義資料繫結，有多種方式可以設定`BindingContext`目標物件。 有時由有時使用程式碼後置檔案中設定`StaticResource`或`x:Static`標記延伸的內容有時候`BindingContext`屬性項目標記。

繫結是最常使用與基礎資料模型，通常是在 MVVM (模型-檢視-ViewModel) 應用程式架構，實現連線程式的視覺效果中所述[第 5 部分。從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)，但可能會有其他案例。

## <a name="view-to-view-bindings"></a>檢視來檢視繫結

您可以定義資料繫結至連結的相同頁面上的兩個檢視的屬性。 在此案例中，您設定`BindingContext`的目標物件使用`x:Reference`標記延伸。

以下是 XAML 檔案，其中包含`Slider`並將兩個`Label`檢視，其中會旋轉`Slider`值，而另一個顯示`Slider`值：

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

`x:Reference`繫結延伸模組會定義名為的屬性`Name`設為所參考的項目的名稱在此情況下`slider`。 不過，`ReferenceExtension`類別來定義`x:Reference`標記延伸也會定義`ContentProperty`屬性`Name`，這表示它不是明確需要。 只針對各種不同，第一個`x:Reference`包含 「 名稱 ="但第二個則不然：

```csharp
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

`Binding`標記延伸模組本身可以有數個屬性，如同`BindingBase`和`Binding`類別。 `ContentProperty` For`Binding`是`Path`，但 「 路徑 ="可以省略標記延伸模組的一部分，如果路徑中的第一個項目`Binding`標記延伸。 第一個範例中有"路徑 ="，但是第二個範例省略它：

```csharp
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

屬性都可以在同一行，或是分成多行：

```csharp
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

執行任何很方便。

請注意`StringFormat`中，第二個屬性`Binding`標記延伸。 在 Xamarin.Forms 中，繫結不會執行任何隱含類型轉換，而且如果您要顯示為字串的非字串物件您必須提供類型轉換器，或是使用`StringFormat`。 在幕後，靜態`String.Format`方法用來實作`StringFormat`。 這是潛在問題，因為.NET 格式規格包含大括號，也可以用來分隔標記延伸。 這會建立令人困惑，XAML 剖析器的風險。 若要避免這種情況，將放在單引號中的整個的格式化字串：

```csharp
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

以下是執行中的程式：

[![](data-binding-basics-images/sliderbinding.png "檢視來檢視繫結")](data-binding-basics-images/sliderbinding-large.png#lightbox "檢視來檢視繫結 ")

## <a name="the-binding-mode"></a>繫結模式

單一檢視可以有數個屬性上的資料繫結。 不過，每個檢視只能有一個`BindingContext`，因此在該檢視上的多個資料繫結必須全部參考相同物件的屬性。

這和其他問題的解決方案涉及`Mode`屬性，它會設定為隸屬`BindingMode`列舉型別：

- `Default`
- `OneWay` — 值都會從來源傳送到目標
- `OneWayToSource` — 值會從目標傳送到來源
- `TwoWay` — 值會傳送來源與目標之間的這兩種方式

下列程式會示範常見用法之一`OneWayToSource`和`TwoWay`繫結模式。 四個`Slider`檢視要控制`Scale`， `Rotate`， `RotateX`，並`RotateY`屬性`Label`。 首先，它看起來好像這四個屬性的`Label`應該是資料繫結目標，因為每個正在設定`Slider`。 不過，`BindingContext`的`Label`可以只有一個物件，而且有四個不同的滑桿。

基於這個理由，所有繫結中設定看似回溯方式：`BindingContext`的每四個滑桿設`Label`，並繫結上設定`Value`滑桿的屬性。 藉由使用`OneWayToSource`並`TwoWay`模式，這些`Value`屬性可以設定來源屬性，也就是`Scale`， `Rotate`， `RotateX`，和`RotateY`屬性`Label`:

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

在其中的三個繫結`Slider`檢視`OneWayToSource`，亦即，`Slider`中的屬性值會導致的變更其`BindingContext`，這是`Label`名為`label`。 這三`Slider`檢視會導致變更`Rotate`， `RotateX`，和`RotateY`的屬性`Label`。

不過，繫結`Scale`屬性是`TwoWay`。 這是因為`Scale`屬性具有預設值是 1，並使用`TwoWay`繫結原因`Slider`初始設定在 1，而不是 0 的值。 如果該繫結`OneWayToSource`，則`Scale`屬性一開始會設定為從 0`Slider`預設值。 `Label`就不會顯示，和，可能會對使用者造成混淆。

 [![](data-binding-basics-images/slidertransforms.png "回溯繫結")](data-binding-basics-images/slidertransforms-large.png#lightbox "回溯繫結")

 > [!NOTE]
 > [ `VisualElement` ](xref:Xamarin.Forms.VisualElement)類別也有[ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX)並[ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY)屬性，調整`VisualElement`x 軸和 y 軸上分別。

## <a name="bindings-and-collections"></a>繫結和集合

執行任何動作說明 XAML 和資料繫結，優於樣板化的強大`ListView`。

`ListView` 定義`ItemsSource`型別的屬性`IEnumerable`，而且會顯示該集合中的項目。 這些項目可以是任何類型的物件。 根據預設，`ListView`使用`ToString`方法顯示該項目的每個項目。 有時候這是只是您想，但在許多情況下，`ToString`傳回只有物件的完整類別名稱。

不過中的項目`ListView`您想要透過使用的任何方式可以顯示集合*範本*，其中包含的類別，衍生自`Cell`。 每個項目中複製範本`ListView`，而且已設定的範本的資料繫結會傳送至個別的複製程式碼。

通常，您會想要建立這些項目使用的自訂資料格`ViewCell`類別。 此程序有點混亂的程式碼中，但在 XAML 中會變得非常簡單。

包含在 XamlSamples 專案是類別，稱為`NamedColor`。 每個`NamedColor`物件具有`Name`並`FriendlyName`屬性的型別`string`，和`Color`型別的屬性`Color`。 颾魤 ㄛ `NamedColor` 141 靜態唯讀欄位型別的`Color`對應至 Xamarin.Forms 中定義的色彩`Color`類別。 靜態建構函式建立`IEnumerable<NamedColor>`集合，其中包含`NamedColor`物件對應至這些靜態欄位，並將它指派給它的公用靜態`All`屬性。

設定靜態`NamedColor.All`屬性，以`ItemsSource`的`ListView`很容易使用`x:Static`標記延伸模組：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

結果顯示可讓您建立的項目是真正的型別`XamlSamples.NamedColor`:

[![](data-binding-basics-images/listview1.png "繫結至集合")](data-binding-basics-images/listview1-large.png#lightbox "繫結至集合")

它不是太多的資訊，但`ListView`是可捲動和選取。

若要定義的項目範本，您會想要細分`ItemTemplate`屬性做為屬性項目，並將它設定為`DataTemplate`，然後參考`ViewCell`。 若要`View`屬性`ViewCell`您可以定義一或多個檢視，以顯示每個項目的版面配置。 以下是簡單的範例：

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

`Label`元素設定為`View`屬性`ViewCell`。 (`ViewCell.View`因為不需要標記`View`屬性是內容屬性`ViewCell`。)此標記會顯示`FriendlyName`每個屬性`NamedColor`物件：

[![](data-binding-basics-images/listview2.png "繫結至集合，以使用 DataTemplate")](data-binding-basics-images/listview2-large.png#lightbox "繫結至集合，以使用 DataTemplate")

好多了。 現在就只需要為裝飾項目範本的詳細資訊和實際的色彩。 若要支援此範本，某些值和物件中已定義頁面的資源字典：

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

請注意，使用`OnPlatform`若要定義的大小`BoxView`與高度`ListView`資料列。 雖然所有的三個平台的值相同，但很可能為了適合微調顯示，其他值標記。

## <a name="binding-value-converters"></a>繫結值轉換器

先前**ListView 示範**XAML 檔案就會顯示個別`R`， `G`，並`B`Xamarin.Forms 屬性`Color`結構。 這些屬性都屬於型別`double`和從 0 到 1 的範圍內。 如果您想要顯示的十六進位值，您不可以只使用`StringFormat`與格式規格 」 X2"。 僅適用於整數和，除了`double`需要先乘以 255 的值。

這個小問題已解決*值轉換器*，也稱為*繫結轉換子*。 這是類別可實作`IValueConverter`介面，這表示它有兩個方法，名為`Convert`和`ConvertBack`。 `Convert`值從來源傳送到目標; 時，會呼叫方法`ConvertBack`方法稱為傳輸從目標到來源中`OneWayToSource`或`TwoWay`繫結：

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

`ConvertBack`方法不是扮演 role 中此程式因為繫結只有其中一種方式從來源到目標。

繫結參考繫結的轉換器，且`Converter`屬性。 繫結轉換子也可以接受使用指定的參數`ConverterParameter`屬性。 對於一些彈性，這是指定乘數的方式。 繫結轉換子會檢查有效的轉換子參數`double`值。

轉換器具現化資源字典中，因此可以共用多個繫結：

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

三個資料繫結參考此單一執行個體。 請注意，`Binding`標記延伸模組包含內嵌`StaticResource`標記延伸模組：

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

結果如下：

[![](data-binding-basics-images/listview3.png "繫結至含 DataTemplate 和轉換器的集合")](data-binding-basics-images/listview3-large.png#lightbox "繫結至含 DataTemplate 和轉換器的集合")

`ListView`已經相當成熟中處理動態如果可能會發生在基礎資料中，但僅限於您採取特定步驟的變更。 如果項目集合指派給`ItemsSource`屬性`ListView`在執行階段期間的變更 — 是，如果項目可以加入或移除集合中，使用`ObservableCollection`這些項目的類別。 `ObservableCollection` 會實作`INotifyCollectionChanged`介面，並`ListView`將會安裝的處理常式`CollectionChanged`事件。

如果項目本身的屬性變更在執行階段，然後在集合中的項目應該實作`INotifyPropertyChanged`屬性值使用的介面和訊號變更`PropertyChanged`事件。 這示範於本系列的下一個部分[第 5 部分。從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

## <a name="summary"></a>總結

資料繫結會提供連結頁面內的兩個物件之間，或視覺物件之間的屬性和基礎資料的強大機制。 但當應用程式開始使用資料來源，逐漸成形的有用的範例為熱門的應用程式的架構模式。 這會涵蓋在[第 5 部分。從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。



## <a name="related-links"></a>相關連結

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：開始使用 XAML （範例）](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 2 部分：基本 XAML 語法 （範例）](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 3 部分：XAML 標記延伸模組 （範例）](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 5 部分：從資料繫結至 MVVM （範例）](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
