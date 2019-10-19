---
title: 使用 XAML 標記延伸
description: 本文說明如何使用 Xamarin XAML 標記延伸，藉由允許從各種來源設定元素屬性，來增強 XAML 的能力和彈性。
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 03aaf471479a5113aade6bd3f34034afadfb538c
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "69887915"
---
# <a name="consuming-xaml-markup-extensions"></a>使用 XAML 標記延伸

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

XAML 標記延伸可讓您從各種來源設定元素屬性，以增強 XAML 的強大功能和彈性。 數個 XAML 標記延伸是 XAML 2009 規格的一部分。 這些會以慣用的 `x` 命名空間前置詞出現在 XAML 檔案中，而且通常會使用此前置詞來參考。 本文討論下列標記延伸：

- [`x:Static`](#static) –參考靜態屬性、欄位或列舉成員。
- [`x:Reference`](#reference) –在頁面上參考指名的元素。
- [`x:Type`](#type) –將屬性設定為 `System.Type` 物件。
- [`x:Array`](#array) –構造特定類型之物件的陣列。
- [`x:Null`](#null) –將屬性設定為 `null` 值。
- [`OnPlatform`](#onplatform) –自訂以每個平臺為基礎的 UI 外觀。
- [`OnIdiom`](#onidiom) –根據應用程式執行所在裝置的用法來自訂 UI 外觀。
- [`DataTemplate`](#datatemplate-markup-extension) -將類型轉換成[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。
- [`FontImage`](#fontimage-markup-extension) -在可以顯示 `ImageSource` 的任何視圖中顯示字型圖示。

其他的 XAML 標記延伸在過去已經受到其他 XAML 的支援，而且 Xamarin 也支援。 其他文章中有更完整的說明：

- `StaticResource` &ndash; 從資源字典參考物件，如[**資源字典**](~/xamarin-forms/xaml/resource-dictionaries.md)一文所述。
- `DynamicResource` &ndash; 回應資源字典中物件的變更，如[**動態樣式**](~/xamarin-forms/user-interface/styles/dynamic.md)一文所述。
- `Binding` &ndash; 在兩個物件的屬性之間建立連結，如[**資料**](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結一文所述。
- `TemplateBinding` &ndash; 會從控制項範本執行資料系結，如[**從控制項範本**](~/xamarin-forms/app-fundamentals/templates/control-templates/template-binding.md)系結文章中所述。

[@No__t_1](xref:Xamarin.Forms.RelativeLayout)版面配置會使用自訂標記延伸[`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)。 此標記延伸會在[**RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md)一文中說明。

<a name="static" />

## <a name="xstatic-markup-extension"></a>x:Static 標記延伸

[@No__t_2](xref:Xamarin.Forms.Xaml.StaticExtension)類別支援 `x:Static` 標記延伸。 類別具有名為[`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member)類型 `string` 的單一屬性，您可以將其設定為公用常數、靜態屬性、靜態欄位或列舉成員的名稱。

使用 `x:Static` 的一種常見方式是先定義具有一些常數或靜態變數的類別，例如[**MarkupExtensions**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)程式中的這個小型 `AppConstants` 類別：

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

[ **X:Static 示範**] 頁面會示範使用 `x:Static` 標記延伸的數種方式。 最詳細的方法會具現化 `Label.FontSize` 屬性-元素標記之間的 `StaticExtension` 類別：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.StaticDemoPage"
             Title="x:Static Demo">
    <StackLayout Margin="10, 0">
        <Label Text="Label No. 1">
            <Label.FontSize>
                <x:StaticExtension Member="local:AppConstants.NormalFontSize" />
            </Label.FontSize>
        </Label>

        ···

    </StackLayout>
</ContentPage>
```

XAML 剖析器也可讓 `StaticExtension` 類別縮寫為 `x:Static`：

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

這可以更進一步簡化，但變更會引進一些新的語法：其中包含將 `StaticExtension` 類別和成員設定放在大括弧中。 產生的運算式會直接設定為 `FontSize` 屬性：

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

請注意，大括弧內*沒有*引號。 @No__t_1 的 `Member` 屬性不再是 XML 屬性。 它會改為標記延伸之運算式的一部分。

就像您可以在使用它做為物件專案時 `x:StaticExtension` `x:Static` 縮寫，也可以在大括弧內的運算式中將它縮寫：

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

@No__t_0 類別具有參考屬性 `Member` 的 `ContentProperty` 屬性，這會將此屬性標記為類別的預設內容屬性。 對於以大括弧表示的 XAML 標記延伸，您可以消除運算式的 `Member=` 部分：

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

這是最常見的 `x:Static` 標記延伸格式。

**靜態示範**頁面包含其他兩個範例。 XAML 檔案的根標記包含 .NET `System` 命名空間的 XML 命名空間宣告：

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

這可讓 `Label` 字型大小設定為靜態欄位 `Math.PI`。 這會產生相當小的文字，因此 `Scale` 屬性會設定為 `Math.E`：

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

最後一個範例會顯示 `Device.RuntimePlatform` 值。 @No__t_0 靜態屬性是用來在兩個 `Span` 物件之間插入新行字元：

```xaml
<Label HorizontalTextAlignment="Center"
       FontSize="{x:Static local:AppConstants.NormalFontSize}">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Runtime Platform: " />
            <Span Text="{x:Static sys:Environment.NewLine}" />
            <Span Text="{x:Static Device.RuntimePlatform}" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

以下是執行的範例：

[![x:Static 示範](consuming-images/staticdemo-small.png "x:Static 示範")](consuming-images/staticdemo-large.png#lightbox "x:Static 示範")

<a name="reference" />

## <a name="xreference-markup-extension"></a>x:Reference 標記延伸

[@No__t_2](xref:Xamarin.Forms.Xaml.ReferenceExtension)類別支援 `x:Reference` 標記延伸。 類別具有名為[`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name)類型 `string` 的單一屬性，您可以將其設定為頁面上的專案名稱，該專案已提供 `x:Name` 的名稱。 這個 `Name` 屬性是 `ReferenceExtension` 的 content 屬性，因此當 `x:Reference` 出現在大括弧中時，不需要 `Name=`。

@No__t_0 標記延伸僅適用于資料系結，這會在[**資料**](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結一文中更詳細地說明。

[ **X:Reference 示範**] 頁面會顯示兩個 `x:Reference` 與資料系結的用法，第一個是用來設定 `Binding` 物件的 `Source` 屬性，第二個則是用來設定兩個數據系結的 `BindingContext` 屬性。:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ReferenceDemoPage"
             x:Name="page"
             Title="x:Reference Demo">

    <StackLayout Margin="10, 0">

        <Label Text="{Binding Source={x:Reference page},
                              StringFormat='The type of this page is {0}'}"
               FontSize="18"
               VerticalOptions="CenterAndExpand"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="Center" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='{0:F0}&#x00B0; rotation'}"
               Rotation="{Binding Value}"
               FontSize="24"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

這兩個 `x:Reference` 運算式都會使用 `ReferenceExtension` 類別名稱的縮寫版本，並消除運算式的 `Name=` 部分。 在第一個範例中，`x:Reference` 標記延伸模組內嵌在 `Binding` 標記延伸中。 請注意，`Source` 和 `StringFormat` 設定會以逗號分隔。 以下是程式執行情況：

[![x:Reference 示範](consuming-images/referencedemo-small.png "x:Reference 示範")](consuming-images/referencedemo-large.png#lightbox "x:Reference 示範")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type 標記延伸

@No__t_0 標記延伸是C# [`typeof`](/dotnet/csharp/language-reference/keywords/typeof/)關鍵字的 XAML 對應項。 它受到[`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)類別的支援，它會定義一個名為[`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName)的屬性，其類型 `string` 設定為類別或結構名稱。 @No__t_0 標記延伸會傳回該類別或結構的[`System.Type`](xref:System.Type)物件。 `TypeName` 是 `TypeExtension` 的 content 屬性，因此當 `x:Type` 出現大括弧時，不需要 `TypeName=`。

在 Xamarin 中，有數個屬性具有 `Type` 類型的引數。 範例包括 `Style` 的[`TargetType`](xref:Xamarin.Forms.Style.TargetType)屬性，以及用來在泛型類別中指定引數的[x:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments)屬性。 不過，XAML 剖析器會自動執行 `typeof` 作業，而且在這些情況下不會使用 `x:Type` 標記延伸。

*需要 `x:Type`* 的一個位置是具有 `x:Array` 標記延伸，如下一[節](#array)所述。

當您在建立功能表，其中每個功能表項目都對應至特定類型的物件時，`x:Type` 標記延伸也很有用。 您可以將 `Type` 物件與每個功能表項目建立關聯，然後在選取功能表項目時，具現化物件。

這是 [**標記延伸**] 程式中 `MainPage` 中的導覽功能表運作方式。 **MainPage**包含一個 `TableView`，其中每個 `TextCell` 對應至程式中的特定頁面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.MainPage"
             Title="Markup Extensions"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection>
                <TextCell Text="x:Static Demo"
                          Detail="Access constants or statics"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:StaticDemoPage}" />

                <TextCell Text="x:Reference Demo"
                          Detail="Reference named elements on the page"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ReferenceDemoPage}" />

                <TextCell Text="x:Type Demo"
                          Detail="Associate a Button with a Type"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:TypeDemoPage}" />

                <TextCell Text="x:Array Demo"
                          Detail="Use an array to fill a ListView"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ArrayDemoPage}" />

                ···                          

        </TableRoot>
    </TableView>
</ContentPage>
```

以下是**標記延伸**中的開啟主頁面：

[![主頁面](consuming-images/mainpage-small.png "主頁面")](consuming-images/mainpage-large.png#lightbox "主頁面")

每個 `CommandParameter` 屬性都會設定為參考其中一個其他頁面的 `x:Type` 標記延伸。 @No__t_0 屬性系結至名為 `NavigateCommand` 的屬性。 此屬性定義于 `MainPage` 程式碼後置檔案中：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

@No__t_0 屬性是一個 `Command` 物件，它會使用類型的引數 `Type` &mdash; `CommandParameter` 的值，來實執行命令。 方法會使用 `Activator.CreateInstance` 來具現化頁面，然後流覽至該網頁。 此函式會先將頁面的 `BindingContext` 設定為其本身，讓 `Command` 上的 `Binding` 能夠正常執行。 如需此類型程式碼的詳細資訊，請參閱[**資料**](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結一文，特別是[**命令**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)文章。

**X:Type 示範**頁面會使用類似的技術來具現化 Xamarin. form 專案，並將其新增至 `StackLayout`。 XAML 檔案一開始是由三個 `Button` 元素組成，其 `Command` 屬性會設定為 `Binding`，而 `CommandParameter` 屬性會設定為三個 Xamarin 的類型。表單檢視：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.TypeDemoPage"
             Title="x:Type Demo">

    <StackLayout x:Name="stackLayout"
                 Padding="10, 0">

        <Button Text="Create a Slider"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Slider}" />

        <Button Text="Create a Stepper"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Stepper}" />

        <Button Text="Create a Switch"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Switch}" />
    </StackLayout>
</ContentPage>
```

程式碼後置檔案會定義並初始化 `CreateCommand` 屬性：

```csharp
public partial class TypeDemoPage : ContentPage
{
    public TypeDemoPage()
    {
        InitializeComponent();

        CreateCommand = new Command<Type>((Type viewType) =>
        {
            View view = (View)Activator.CreateInstance(viewType);
            view.VerticalOptions = LayoutOptions.CenterAndExpand;
            stackLayout.Children.Add(view);
        });

        BindingContext = this;
    }

    public ICommand CreateCommand { private set; get; }
}
```

當按下 `Button` 時所執行的方法會建立引數的新實例、設定其 `VerticalOptions` 屬性，並將它加入至 `StackLayout`。 然後，這三個 `Button` 元素會與動態建立的視圖共用頁面：

[![x:Type 示範](consuming-images/typedemo-small.png "x:Type 示範")](consuming-images/typedemo-large.png#lightbox "x:Type 示範")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array 標記延伸

@No__t_0 標記延伸可讓您在標記中定義陣列。 它受到[`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)類別的支援，它會定義兩個屬性：

- `Type` 類型的 `Type`，表示陣列中元素的類型。
- `IList` 類型的 `Items`，也就是專案本身的集合。 這是 `ArrayExtension` 的 content 屬性。

@No__t_0 標記延伸本身永遠不會以大括弧括住。 相反地，`x:Array` 開始 和 結束 標記來分隔專案清單。 將 `Type` 屬性設定為 `x:Type` 標記延伸。

[ **X:Array 示範**] 頁面會顯示如何使用 `x:Array`，藉由將 `ItemsSource` 屬性設定為數組，將專案加入至 `ListView`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ArrayDemoPage"
             Title="x:Array Demo Page">
    <ListView Margin="10">
        <ListView.ItemsSource>
            <x:Array Type="{x:Type Color}">
                <Color>Aqua</Color>
                <Color>Black</Color>
                <Color>Blue</Color>
                <Color>Fuchsia</Color>
                <Color>Gray</Color>
                <Color>Green</Color>
                <Color>Lime</Color>
                <Color>Maroon</Color>
                <Color>Navy</Color>
                <Color>Olive</Color>
                <Color>Pink</Color>
                <Color>Purple</Color>
                <Color>Red</Color>
                <Color>Silver</Color>
                <Color>Teal</Color>
                <Color>White</Color>
                <Color>Yellow</Color>
            </x:Array>
        </ListView.ItemsSource>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <BoxView Color="{Binding}"
                             Margin="3" />    
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>        
```

@No__t_0 會針對每個色彩專案建立簡單的 `BoxView`：

[![x:Array 示範](consuming-images/arraydemo-small.png "x:Array 示範")](consuming-images/arraydemo-large.png#lightbox "x:Array 示範")

有數種方式可指定此陣列中的個別 `Color` 專案。 您可以使用 `x:Static` 標記延伸：

```xaml
<x:Static Member="Color.Blue" />
```

或者，您可以使用 `StaticResource` 從資源字典中取出色彩：

```xaml
<StaticResource Key="myColor" />
```

在本文結尾處，您會看到一個也會建立新色彩值的自訂 XAML 標記延伸：

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

定義一般類型的陣列（例如字串或數位）時，請使用傳遞的函式[**引數**](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)一文中所列的標記來分隔值。

<a name="null" />

## <a name="xnull-markup-extension"></a>x:Null 標記延伸

[@No__t_2](xref:Xamarin.Forms.Xaml.NullExtension)類別支援 `x:Null` 標記延伸。 它沒有屬性，而只是C# [`null`](/dotnet/csharp/language-reference/keywords/null/)關鍵字的 XAML 對等用法。

@No__t_0 的標記延伸很少，而且很少使用，但是如果您覺得有需要，您會很高興它存在。

**X:Null 示範**頁面說明 `x:Null` 可能很方便的一個案例。 假設您為 `Label` 定義了隱含的 `Style`，其中包含 `Setter` 將 `FontFamily` 屬性設定為平臺相依的系列名稱：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.NullDemoPage"
             Title="x:Null Demo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="48" />
                <Setter Property="FontFamily">
                    <Setter.Value>
                        <OnPlatform x:TypeArguments="x:String">
                            <On Platform="iOS" Value="Times New Roman" />
                            <On Platform="Android" Value="serif" />
                            <On Platform="UWP" Value="Times New Roman" />
                        </OnPlatform>
                    </Setter.Value>
                </Setter>
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ContentPage.Content>
        <StackLayout Padding="10, 0">
            <Label Text="Text 1" />
            <Label Text="Text 2" />

            <Label Text="Text 3"
                   FontFamily="{x:Null}" />

            <Label Text="Text 4" />
            <Label Text="Text 5" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>   
```

然後您會發現，針對其中一個 `Label` 專案，您想要隱含 `Style` 中的所有屬性設定，但不包括 `FontFamily`，而您想要將它設為預設值。 您可以針對該目的定義另一個 `Style`，但較簡單的方法只是將特定 `Label` 的 `FontFamily` 屬性設定為 `x:Null`，如中央 `Label` 中所示。

以下是程式執行情況：

[![x:Null 示範](consuming-images/nulldemo-small.png "x:Null 示範")](consuming-images/nulldemo-large.png#lightbox "x:Null 示範")

請注意，有四個 `Label` 元素具有 serif 字型，但中央 `Label` 具有預設的 sans-serif 字型。

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>OnPlatform 標記延伸

@No__t_0 標記延伸可讓您自訂每個平臺的 UI 外觀。 它提供與[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)和[`On`](xref:Xamarin.Forms.On)類別相同的功能，但具有更精確的標記法。

[@No__t_2](xref:Xamarin.Forms.Xaml.OnPlatformExtension)類別支援 `OnPlatform` 標記延伸，其定義下列屬性：

- `object` 類型的 `Default`，您會將其設定為要套用至代表平臺之屬性的預設值。
- `object` 類型的 `Android`，您會將其設定為要在 Android 上套用的值。
- `object` 類型的 `GTK`，您會將其設定為要在 GTK 平臺上套用的值。
- `object` 類型的 `iOS`，您會將其設定為要在 iOS 上套用的值。
- `object` 類型的 `macOS`，您會將其設定為要在 macOS 上套用的值。
- `object` 類型的 `Tizen`，您會將其設定為要套用至 Tizen 平臺的值。
- `object` 類型的 `UWP`，您會將其設定為要套用至通用 Windows 平臺的值。
- `object` 類型的 `WPF`，您會將其設定為要在 Windows Presentation Foundation 平臺上套用的值。
- 您設定為 `IValueConverter` 實作為 `IValueConverter` 類型的 `Converter`。
- `object` 類型的 `ConverterParameter`，您會將其設定為要傳遞至 `IValueConverter` 執行的值。

> [!NOTE]
> XAML 剖析器允許[`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension)類別縮寫為 `OnPlatform`。

@No__t_0 屬性是 `OnPlatformExtension` 的 content 屬性。 因此，對於以大括弧表示的 XAML 標記運算式，您可以去除運算式的 `Default=` 部分，前提是它是第一個引數。

> [!IMPORTANT]
> XAML 剖析器預期會將正確類型的值提供給使用 `OnPlatform` 標記延伸的屬性。 如果需要型別轉換，`OnPlatform` 標記延伸會嘗試使用由 Xamarin 提供的預設轉換器來執行。 不過，預設的轉換器無法執行某些類型轉換，而在這些情況下，`Converter` 屬性應該設定為 `IValueConverter` 的執行。

[ **OnPlatform 示範**] 頁面會顯示如何使用 `OnPlatform` 標記延伸：

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

在此範例中，所有三個 `OnPlatform` 運算式都會使用 `OnPlatformExtension` 類別名稱的縮寫版本。 這三個 `OnPlatform` 標記延伸會將[`BoxView`](xref:Xamarin.Forms.BoxView)的[`Color`](xref:Xamarin.Forms.BoxView.Color)、 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)和[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)屬性，設定為 iOS、Android 和 UWP 上的不同值。 標記延伸也會在未指定的平臺上提供這些屬性的預設值，同時消除運算式的 `Default=` 部分。 請注意，設定的標記延伸模組屬性是以逗號分隔。

以下是程式執行情況：

[![OnPlatform 示範](consuming-images/onplatformdemo-small.png "OnPlatform 示範")](consuming-images/onplatformdemo-large.png#lightbox "OnPlatform 示範")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>OnIdiom 標記延伸

@No__t_0 標記延伸可讓您根據應用程式執行所在裝置的用法來自訂 UI 外觀。 它受到[`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension)類別的支援，它會定義下列屬性：

- `object` 類型的 `Default`，您會將其設定為要套用至代表裝置慣用語之屬性的預設值。
- `object` 類型的 `Phone`，您會將其設定為要套用至手機的值。
- `object` 類型的 `Tablet`，您會將其設定為要在平板電腦上套用的值。
- `object` 類型的 `Desktop`，您會將其設定為要套用至桌面平臺的值。
- `object` 類型的 `TV`，您會將其設定為要套用至電視平臺的值。
- `object` 類型的 `Watch`，您會將其設定為要在監看式平臺上套用的值。
- 您設定為 `IValueConverter` 實作為 `IValueConverter` 類型的 `Converter`。
- `object` 類型的 `ConverterParameter`，您會將其設定為要傳遞至 `IValueConverter` 執行的值。

> [!NOTE]
> XAML 剖析器允許[`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension)類別縮寫為 `OnIdiom`。

@No__t_0 屬性是 `OnIdiomExtension` 的 content 屬性。 因此，對於以大括弧表示的 XAML 標記運算式，您可以去除運算式的 `Default=` 部分，前提是它是第一個引數。

> [!IMPORTANT]
> XAML 剖析器預期會將正確類型的值提供給使用 `OnIdiom` 標記延伸的屬性。 如果需要型別轉換，`OnIdiom` 標記延伸會嘗試使用由 Xamarin 提供的預設轉換器來執行。 不過，預設的轉換器無法執行某些類型轉換，而在這些情況下，`Converter` 屬性應該設定為 `IValueConverter` 的執行。

[ **OnIdiom 示範**] 頁面會顯示如何使用 `OnIdiom` 標記延伸：

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

在此範例中，所有三個 `OnIdiom` 運算式都會使用 `OnIdiomExtension` 類別名稱的縮寫版本。 這三個 `OnIdiom` 標記延伸模組會將[`BoxView`](xref:Xamarin.Forms.BoxView)的[`Color`](xref:Xamarin.Forms.BoxView.Color)、 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)和[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)屬性，設定為電話、平板電腦和桌面慣用語上的不同值。 標記延伸也會針對未指定的慣用語上的這些屬性提供預設值，同時消除運算式的 `Default=` 部分。 請注意，設定的標記延伸模組屬性是以逗號分隔。

以下是程式執行情況：

[![OnIdiom 示範](consuming-images/onidiomdemo-small.png "OnIdiom 示範")](consuming-images/onidiomdemo-large.png#lightbox "OnIdiom 示範")

## <a name="datatemplate-markup-extension"></a>DataTemplate 標記延伸

@No__t_0 標記延伸可讓您將類型轉換成[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 @No__t_0 類別（其定義類型 `string` 的 `TypeName` 屬性）支援，其設定為要轉換成 `DataTemplate` 的類型名稱。 @No__t_0 屬性是 `DataTemplateExtension` 的 content 屬性。 因此，對於以大括弧表示的 XAML 標記運算式，您可以消除運算式的 `TypeName=` 部分。

> [!NOTE]
> XAML 剖析器允許 `DataTemplateExtension` 類別縮寫為 `DataTemplate`。

此標記延伸的一般用法是在 Shell 應用程式中，如下列範例所示：

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

在此範例中，`MonkeysPage` 會從[`ContentPage`](xref:Xamarin.Forms.ContentPage)轉換成[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，這會設定為 `ShellContent.ContentTemplate` 屬性的值。 這可確保只會在流覽至頁面時（而不是在應用程式啟動時）建立 `MonkeysPage`。

如需 Shell 應用程式的詳細資訊，請參閱[Xamarin. Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="fontimage-markup-extension"></a>FontImage 標記延伸

@No__t_0 標記延伸可讓您在任何可以顯示 `ImageSource` 的視圖中顯示字型圖示。 它提供與 `FontImageSource` 類別相同的功能，但具有更精確的標記法。

@No__t_1 類別支援 `FontImage` 標記延伸，其定義下列屬性：

- `string` 類型的 `FontFamily`，字型圖示所屬的字型系列。
- `string` 類型的 `Glyph`，字型圖示的 unicode 字元值。
- `Color` 類型的 `Color`，顯示字型圖示時要使用的色彩。
- 類型 `double` 的 `Size`，這是轉譯字型圖示的大小（以與裝置無關的單位）。

> [!NOTE]
> XAML 剖析器允許 `FontImageExtension` 類別縮寫為 `FontImage`。

@No__t_0 屬性是 `FontImageExtension` 的 content 屬性。 因此，對於以大括弧表示的 XAML 標記運算式，您可以去除運算式的 `Glyph=` 部分，前提是它是第一個引數。

[ **FontImage 示範**] 頁面會顯示如何使用 `FontImage` 標記延伸：

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

在此範例中，會使用 `FontImageExtension` 類別名稱的縮寫版本，在[`Image`](xref:Xamarin.Forms.Image)中顯示 XBox 圖示（來自 Ionicons 字型系列）。 運算式也會使用 `OnPlatform` 標記延伸，在 iOS 和 Android 上指定不同的 `FontFamily` 屬性值。 此外，運算式的 `Glyph=` 部分會被排除，而所設定的標記延伸屬性則會以逗號分隔。 請注意，雖然圖示的 unicode 字元是 `\uf30c`，但它必須在 XAML 中以轉義，因此會變成 `&#xf30c;`。

以下是程式執行情況：

[![FontImage 標記延伸的螢幕擷取畫面](consuming-images/fontimagedemo.png "FontImage 示範")](consuming-images/fontimagedemo-large.png#lightbox "FontImage 示範")

如需在 `FontImageSource` 物件中指定字型圖示資料來顯示字型圖示的詳細資訊，請參閱[顯示字型圖示](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="define-your-own-markup-extensions"></a>定義您自己的標記延伸

如果您遇到了無法在 Xamarin 中使用的 XAML 標記延伸模組，則可以[自行建立](creating.md)。

## <a name="related-links"></a>相關連結

- [標記延伸（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Xamarin 的 XAML 標記延伸章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [動態樣式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin. Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。
