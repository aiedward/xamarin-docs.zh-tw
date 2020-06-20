---
title: 使用 XAML 標記延伸
description: 本文說明如何使用 Xamarin.Forms xaml 標記延伸，藉由允許從各種來源設定元素屬性，來增強 xaml 的能力和彈性。
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/17/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e1429c3f39e37dc552d7f6ca8767058e5aec853b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84903101"
---
# <a name="consuming-xaml-markup-extensions"></a>使用 XAML 標記延伸

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

XAML 標記延伸可讓您從各種來源設定元素屬性，以增強 XAML 的強大功能和彈性。 數個 XAML 標記延伸是 XAML 2009 規格的一部分。 這些會以慣用的命名空間前置詞出現在 XAML 檔案中 `x` ，而且通常會使用此前置詞來參考。 本文討論下列標記延伸：

- [`x:Static`](#xstatic-markup-extension)–參考靜態屬性、欄位或列舉成員。
- [`x:Reference`](#xreference-markup-extension)–參考頁面上的命名元素。
- [`x:Type`](#xtype-markup-extension)–將屬性設定為 `System.Type` 物件。
- [`x:Array`](#xarray-markup-extension)–建立特定類型之物件的陣列。
- [`x:Null`](#xnull-markup-extension)–將屬性設定為 `null` 值。
- [`OnPlatform`](#onplatform-markup-extension)–自訂以每個平臺為基礎的 UI 外觀。
- [`OnIdiom`](#onidiom-markup-extension)–根據應用程式執行所在裝置的用法來自訂 UI 外觀。
- [`DataTemplate`](#datatemplate-markup-extension)–將類型轉換成 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。
- [`FontImage`](#fontimage-markup-extension)–在可顯示的任何視圖中顯示字型圖示 `ImageSource` 。
- [`AppThemeBinding`](#appthemebinding-markup-extension)–根據目前的系統主題來使用資源。

其他 xaml 程式在過去已支援其他 XAML 標記延伸，而且也支援 Xamarin.Forms 。 其他文章中有更完整的說明：

- `StaticResource`-從資源字典參考物件，如[**資源字典**](~/xamarin-forms/xaml/resource-dictionaries.md)一文所述。
- `DynamicResource`-回應資源字典中物件的變更，如[**動態樣式**](~/xamarin-forms/user-interface/styles/dynamic.md)一文所述。
- `Binding`-在兩個物件的屬性之間建立連結，如[**資料**](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結一文所述。
- `TemplateBinding`-從控制項範本執行資料系結，如[** Xamarin.Forms 控制項範本**](~/xamarin-forms/app-fundamentals/templates/control-template.md)文章中所述。
- `RelativeSource`-設定相對於系結目標之位置的系結來源，如[相關](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)系結一文所述。

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)版面配置會使用自訂標記延伸模組 [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) 。 此標記延伸會在[**RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md)一文中說明。

## <a name="xstatic-markup-extension"></a>x:Static 標記延伸

`x:Static`類別支援標記延伸 [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension) 。 類別具有名為的單一屬性，其類型為， [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) `string` 您將其設定為公用常數、靜態屬性、靜態欄位或列舉成員的名稱。

其中一個常見的使用方式 `x:Static` 是先定義具有一些常數或靜態變數的類別，例如 `AppConstants` [**MarkupExtensions**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)程式中的這個小型類別：

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

[ **X:Static 示範**] 頁面會示範使用標記延伸的數種方式 `x:Static` 。 最詳細的方法會具現化 `StaticExtension` `Label.FontSize` 屬性元素標記之間的類別：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
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

XAML 剖析器也可讓 `StaticExtension` 類別縮寫為 `x:Static` ：

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

這可以更進一步簡化，但變更會引進一些新的語法：其包含將 `StaticExtension` 類別和成員設定放在大括弧中。 產生的運算式會直接設定為 `FontSize` 屬性：

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

請注意，大括弧內*沒有*引號。 的 `Member` 屬性不再 `StaticExtension` 是 XML 屬性。 它會改為標記延伸之運算式的一部分。

就像您在 `x:StaticExtension` `x:Static` 使用它做為物件元素時的縮寫一樣，您也可以在大括弧內的運算式中將其縮寫：

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

`StaticExtension`類別具有 `ContentProperty` 參考屬性的屬性 `Member` ，這會將此屬性標記為類別的預設內容屬性。 對於以大括弧表示的 XAML 標記延伸，您可以消除 `Member=` 運算式的部分：

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

這是最常見的 `x:Static` 標記延伸形式。

**靜態示範**頁面包含其他兩個範例。 XAML 檔案的根標記包含 .NET 命名空間的 XML 命名空間宣告 `System` ：

```xaml
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

這可讓 `Label` 字型大小設定為靜態欄位 `Math.PI` 。 這會產生相當小的文字，因此 `Scale` 屬性會設定為 `Math.E` ：

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

最後一個範例會顯示 `Device.RuntimePlatform` 值。 `Environment.NewLine`靜態屬性是用來在兩個物件之間插入新的行字元 `Span` ：

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

## <a name="xreference-markup-extension"></a>x:Reference 標記延伸

`x:Reference`類別支援標記延伸 [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) 。 類別具有名為的單一屬性 [`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) ，其類型為 `string` ，您將其設定為頁面上已指定名稱的專案名稱 `x:Name` 。 這個 `Name` 屬性是的 content 屬性 `ReferenceExtension` ，因此 `Name=` 當 `x:Reference` 出現在大括弧時，不需要這樣做。

`x:Reference`標記延伸會以獨佔方式用於資料系結，這會在[**資料**](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結一文中更詳細地說明。

[ **X:Reference 示範**] 頁面會顯示兩個使用資料系結的用法 `x:Reference` ，第一個是用來設定物件的屬性，第二個則是 `Source` `Binding` 用來設定 `BindingContext` 兩個數據系結的屬性：

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

這兩個 `x:Reference` 運算式都會使用類別名稱的縮寫版本 `ReferenceExtension` ，並消除 `Name=` 運算式的部分。 在第一個範例中， `x:Reference` 標記延伸模組內嵌在 `Binding` 標記延伸中。 請注意， `Source` 和 `StringFormat` 設定會以逗號分隔。 以下是程式執行情況：

[![x:Reference 示範](consuming-images/referencedemo-small.png "x:Reference 示範")](consuming-images/referencedemo-large.png#lightbox "x:Reference 示範")

## <a name="xtype-markup-extension"></a>x:Type 標記延伸

`x:Type`標記延伸是 c # 關鍵字的 XAML 對等用法 [`typeof`](/dotnet/csharp/language-reference/keywords/typeof/) 。 類別支援它，它會 [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension) 定義一個名為且類型為的屬性，而 [`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) 此型別 `string` 會設定為類別或結構名稱。 `x:Type`標記延伸 [`System.Type`](xref:System.Type) 會傳回該類別或結構的物件。 `TypeName`是的 content 屬性 `TypeExtension` ，因此 `TypeName=` 當 `x:Type` 以大括弧顯示時，不需要這麼做。

在中 Xamarin.Forms ，有數個屬性具有類型的引數 `Type` 。 範例包括的 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 屬性 `Style` ，以及用來在泛型類別中指定引數的[x:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#specifying-a-generic-type-argument)屬性。 不過，XAML 剖析器 `typeof` 會自動執行作業，而且在 `x:Type` 這些情況下不會使用標記延伸。

需要一個位置 `x:Type` *，其中*包含 `x:Array` 標記延伸，如下一[節](#xarray-markup-extension)所述。

當您在 `x:Type` 建立功能表，其中每個功能表項目都對應到特定類型的物件時，標記延伸也很有用。 您可以將 `Type` 物件與每個功能表項目建立關聯，然後在選取功能表項目時，具現化物件。

這是 `MainPage` **標記延伸**程式中的導覽功能表運作方式。 **MainPage**包含 `TableView` ，其中每個 `TextCell` 對應至程式中的特定頁面：

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

每個 `CommandParameter` 屬性都設定為 `x:Type` 參考其中一個其他頁面的標記延伸。 `Command`屬性會系結至名為的 `NavigateCommand` 屬性。 此屬性定義于 `MainPage` 程式碼後置檔案中：

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

`NavigateCommand`屬性是一種 `Command` 物件，它會使用值為的引數，來實作為執行命令 `Type` &mdash; `CommandParameter` 。 方法會使用 `Activator.CreateInstance` 來具現化網頁，然後流覽至該頁面。 此函式的結束處是將頁面的設定 `BindingContext` 為本身，讓 `Binding` on `Command` 能夠正常執行。 如需此類型程式碼的詳細資訊，請參閱[**資料**](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結一文，特別是[**命令**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)文章。

**X:Type 示範**頁面會使用類似的技術來具現化專案 Xamarin.Forms ，並將其加入至 `StackLayout` 。 XAML 檔案一開始是由三個元素所組成 `Button` `Command` ，其屬性會設定為 `Binding` ，而 `CommandParameter` 屬性會設定為三個視圖的類型 Xamarin.Forms ：

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

當按下時所執行的方法會 `Button` 建立引數的新實例、設定其 `VerticalOptions` 屬性，並將其新增至 `StackLayout` 。 然後，這三個 `Button` 元素會與動態建立的視圖共用頁面：

[![x:Type 示範](consuming-images/typedemo-small.png "x:Type 示範")](consuming-images/typedemo-large.png#lightbox "x:Type 示範")

## <a name="xarray-markup-extension"></a>x:Array 標記延伸

`x:Array`標記延伸可讓您在標記中定義陣列。 類別支援它，它會 [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension) 定義兩個屬性：

- `Type`類型的 `Type` ，表示陣列中元素的類型。
- `Items`屬於類型的 `IList` ，也就是專案本身的集合。 這是的 content 屬性 `ArrayExtension` 。

`x:Array`標記延伸本身永遠不會以大括弧括住。 相反 `x:Array` 地，開始和結束標記會分隔專案清單。 將 `Type` 屬性設定為 `x:Type` 標記延伸。

**X:Array 示範**頁面會顯示如何藉 `x:Array` `ListView` 由將屬性設定為數組，來使用將專案新增至 `ItemsSource` 。

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

會 `ViewCell` `BoxView` 為每個色彩專案建立簡單的：

[![x:Array 示範](consuming-images/arraydemo-small.png "x:Array 示範")](consuming-images/arraydemo-large.png#lightbox "x:Array 示範")

有數種方式可指定 `Color` 此陣列中的個別專案。 您可以使用 `x:Static` 標記延伸：

```xaml
<x:Static Member="Color.Blue" />
```

或者，您可以使用 `StaticResource` 從資源字典取出色彩：

```xaml
<StaticResource Key="myColor" />
```

在本文結尾處，您會看到一個也會建立新色彩值的自訂 XAML 標記延伸：

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

定義一般類型的陣列（例如字串或數位）時，請使用傳遞的函式[**引數**](~/xamarin-forms/xaml/passing-arguments.md#passing-constructor-arguments)一文中所列的標記來分隔值。

## <a name="xnull-markup-extension"></a>x:Null 標記延伸

`x:Null`類別支援標記延伸 [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension) 。 它沒有屬性，而只是 c # 關鍵字的 XAML 對等用法 [`null`](/dotnet/csharp/language-reference/keywords/null/) 。

`x:Null`標記延伸非常少用，而且很少使用，但是如果您覺得有需要，就很高興它存在。

**X:Null 示範**頁面 `x:Null` 會說明可能很方便的一個案例。 假設您定義的隱含 `Style` ，其中 `Label` 包含 `Setter` 將 `FontFamily` 屬性設定為平臺相依系列名稱的：

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

接著，您會發現其中一個 `Label` 元素的所有屬性設定都是隱含的，但您想要做為 `Style` `FontFamily` 預設值的除外。 您可以針對該目的定義另一個， `Style` 但是較簡單的方法只是將 `FontFamily` 特定的屬性設定 `Label` 為 `x:Null` ，如中央所示 `Label` 。

以下是程式執行情況：

[![x:Null 示範](consuming-images/nulldemo-small.png "x:Null 示範")](consuming-images/nulldemo-large.png#lightbox "x:Null 示範")

請注意，有四個 `Label` 元素具有 serif 字型，但中心 `Label` 具有預設的 sans-serif 字型。

## <a name="onplatform-markup-extension"></a>OnPlatform 標記延伸

`OnPlatform` 標記延伸可讓您自訂每個平台的 UI 外觀。 它提供與和類別相同的 [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) 功能 [`On`](xref:Xamarin.Forms.On) ，但具有更精確的標記法。

`OnPlatform`類別支援標記延伸 [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) ，其定義下列屬性：

- `Default`屬於類型的 `object` ，您會將設定為要套用至代表平臺之屬性的預設值。
- `Android`屬於類型的 `object` ，您會將設定為要在 Android 上套用的值。
- `GTK`屬於類型的 `object` ，您會將設定為要在 GTK 平臺上套用的值。
- `iOS`屬於類型的 `object` ，您會將設定為要在 iOS 上套用的值。
- `macOS`屬於類型的 `object` ，您會將設定為要在 macOS 上套用的值。
- `Tizen`屬於類型的 `object` ，您會將設定為要在 Tizen 平臺上套用的值。
- `UWP`屬於類型的 `object` ，您會將設定為要在通用 Windows 平臺上套用的值。
- `WPF`屬於類型的 `object` ，您會將設定為要在 Windows Presentation Foundation 平臺上套用的值。
- `Converter`屬於的類型 `IValueConverter` ，可以設定為 `IValueConverter` 執行。
- `ConverterParameter`型別為的 `object` ，可以設定為要傳遞至實作為的值 `IValueConverter` 。

> [!NOTE]
> XAML 剖析器允許將 [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) 類別縮寫為 `OnPlatform` 。

`Default`屬性是的 content 屬性 `OnPlatformExtension` 。 因此，對於以大括弧表示的 XAML 標記運算式，您可以去除 `Default=` 運算式的部分，前提是它是第一個引數。 如果 `Default` 未設定屬性，則會預設為 [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) 屬性值，前提是標記延伸是以為目標 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 。

> [!IMPORTANT]
> XAML 剖析器預期會將正確類型的值提供給使用標記延伸的屬性 `OnPlatform` 。 如果需要型別轉換， `OnPlatform` 標記延伸會嘗試使用所提供的預設轉換器來執行 Xamarin.Forms 。 不過，預設的轉換器無法執行某些類型轉換，而在這些情況下， `Converter` 應該將屬性設定為「 `IValueConverter` 執行」。

[ **OnPlatform 示範**] 頁面會顯示如何使用 `OnPlatform` 標記延伸：

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

在此範例中，所有三個 `OnPlatform` 運算式都會使用類別名稱的縮寫版本 `OnPlatformExtension` 。 這三個 `OnPlatform` 標記延伸會將的 [`Color`](xref:Xamarin.Forms.BoxView.Color) 、 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 屬性設定 [`BoxView`](xref:Xamarin.Forms.BoxView) 為 iOS、Android 和 UWP 上的不同值。 標記延伸也會針對未指定的平臺上的這些屬性提供預設值，同時排除 `Default=` 運算式的部分。 請注意，設定的標記延伸模組屬性是以逗號分隔。

以下是程式執行情況：

[![OnPlatform 示範](consuming-images/onplatformdemo-small.png "OnPlatform 示範")](consuming-images/onplatformdemo-large.png#lightbox "OnPlatform 示範")

## <a name="onidiom-markup-extension"></a>OnIdiom 標記延伸

`OnIdiom`標記延伸可讓您根據應用程式執行所在裝置的用法來自訂 UI 外觀。 類別支援它，它會 [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) 定義下列屬性：

- `Default`屬於類型的 `object` ，您會將設定為要套用至代表裝置慣用語之屬性的預設值。
- `Phone`屬於類型的 `object` ，您將設定為要在手機上套用的值。
- `Tablet`屬於類型的 `object` ，您會將設定為要在平板電腦上套用的值。
- `Desktop`屬於類型的 `object` ，您將其設定為要套用至桌面平臺的值。
- `TV`屬於類型的 `object` ，您會將設定為要在電視平臺上套用的值。
- `Watch`屬於類型的 `object` ，您會將設定為要在監看式平臺上套用的值。
- `Converter`屬於的類型 `IValueConverter` ，可以設定為 `IValueConverter` 執行。
- `ConverterParameter`型別為的 `object` ，可以設定為要傳遞至實作為的值 `IValueConverter` 。

> [!NOTE]
> XAML 剖析器允許將 [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) 類別縮寫為 `OnIdiom` 。

`Default`屬性是的 content 屬性 `OnIdiomExtension` 。 因此，對於以大括弧表示的 XAML 標記運算式，您可以去除 `Default=` 運算式的部分，前提是它是第一個引數。

> [!IMPORTANT]
> XAML 剖析器預期會將正確類型的值提供給使用標記延伸的屬性 `OnIdiom` 。 如果需要型別轉換， `OnIdiom` 標記延伸會嘗試使用所提供的預設轉換器來執行 Xamarin.Forms 。 不過，預設的轉換器無法執行某些類型轉換，而在這些情況下， `Converter` 應該將屬性設定為「 `IValueConverter` 執行」。

[ **OnIdiom 示範**] 頁面會顯示如何使用 `OnIdiom` 標記延伸：

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

在此範例中，所有三個 `OnIdiom` 運算式都會使用類別名稱的縮寫版本 `OnIdiomExtension` 。 這三個 `OnIdiom` 標記延伸會將的 [`Color`](xref:Xamarin.Forms.BoxView.Color) 、 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 屬性設定 [`BoxView`](xref:Xamarin.Forms.BoxView) 為電話、平板電腦和桌面慣用語上的不同值。 標記延伸也會針對未指定的慣用語上的這些屬性提供預設值，同時排除 `Default=` 運算式的部分。 請注意，設定的標記延伸模組屬性是以逗號分隔。

以下是程式執行情況：

[![OnIdiom 示範](consuming-images/onidiomdemo-small.png "OnIdiom 示範")](consuming-images/onidiomdemo-large.png#lightbox "OnIdiom 示範")

## <a name="datatemplate-markup-extension"></a>DataTemplate 標記延伸

`DataTemplate`標記延伸可讓您將類型轉換成 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 類別會支援，其 `DataTemplateExtension` 定義 `TypeName` 類型為的屬性，其 `string` 會設定為要轉換成的類型名稱 `DataTemplate` 。 `TypeName`屬性是的 content 屬性 `DataTemplateExtension` 。 因此，對於以大括弧表示的 XAML 標記運算式，您可以消除 `TypeName=` 運算式的部分。

> [!NOTE]
> XAML 剖析器允許將 `DataTemplateExtension` 類別縮寫為 `DataTemplate` 。

此標記延伸的一般用法是在 Shell 應用程式中，如下列範例所示：

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

在此範例中， `MonkeysPage` 會從轉換成 [`ContentPage`](xref:Xamarin.Forms.ContentPage) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，這會設定為屬性的值 `ShellContent.ContentTemplate` 。 這可確保 `MonkeysPage` 只會在流覽至頁面時發生，而不是在應用程式啟動時建立。

如需 Shell 應用程式的詳細資訊，請參閱[ Xamarin.Forms shell](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="fontimage-markup-extension"></a>FontImage 標記延伸

`FontImage`標記延伸可讓您在任何可顯示的視圖中顯示字型圖示 `ImageSource` 。 它會提供與類別相同的功能 `FontImageSource` ，但具有更精確的標記法。

`FontImage`類別支援標記延伸 `FontImageExtension` ，其定義下列屬性：

- `FontFamily`類型的 `string` ，字型圖示所屬的字型系列。
- `Glyph`屬於類型的 `string` unicode 字元值（字型圖示）。
- `Color`類型的 [`Color`](xref:Xamarin.Forms.Color) ，顯示字型圖示時要使用的色彩。
- `Size`類型的 `double` ，呈現的字型圖示的大小（以與裝置無關的單位）。 預設值是 30。 此外，這個屬性也可以設定為已命名的字型大小。

> [!NOTE]
> XAML 剖析器允許將 `FontImageExtension` 類別縮寫為 `FontImage` 。

`Glyph`屬性是的 content 屬性 `FontImageExtension` 。 因此，對於以大括弧表示的 XAML 標記運算式，您可以去除 `Glyph=` 運算式的部分，前提是它是第一個引數。

[ **FontImage 示範**] 頁面會顯示如何使用 `FontImage` 標記延伸：

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

在此範例中，類別名稱的縮寫版本 `FontImageExtension` 是用來從中的 Ionicons 字型系列顯示 XBox 圖示 [`Image`](xref:Xamarin.Forms.Image) 。 運算式也會使用 `OnPlatform` 標記延伸， `FontFamily` 在 IOS 和 Android 上指定不同的屬性值。 此外，運算式的 `Glyph=` 部分會被排除，而設定的標記延伸屬性會以逗號分隔。 請注意，當圖示的 unicode 字元是時 `\uf30c` ，它必須在 XAML 中以轉義，因此會變成 `&#xf30c;` 。

以下是程式執行情況：

[![FontImage 標記延伸的螢幕擷取畫面](consuming-images/fontimagedemo.png "FontImage 示範")](consuming-images/fontimagedemo-large.png#lightbox "FontImage 示範")

如需在物件中指定字型圖示資料來顯示字型圖示的詳細資訊 `FontImageSource` ，請參閱[顯示字型圖示](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="appthemebinding-markup-extension"></a>AppThemeBinding 標記延伸

`AppThemeBinding`標記延伸可讓您根據目前的系統主題，指定要使用的資源，例如影像或色彩。

> [!IMPORTANT]
> `AppThemeBinding`標記延伸具有最低的作業系統需求。 如需詳細資訊，請參閱[回應 Xamarin.Forms 應用程式中的系統主題變更](~/xamarin-forms/user-interface/theming/system-theme-changes.md)。

`AppThemeBinding`類別支援標記延伸 `AppThemeBindingExtension` ，其定義下列屬性：

- `Default`，屬於類型 `object` ，您將其設定為預設使用的資源。
- `Light`，屬於類型 `object` ，當裝置使用其淺色主題時，您會將其設定為要使用的資源。
- `Dark`，屬於類型 `object` ，當裝置使用其暗色調主題時，您會將其設定為要使用的資源。
- `Value`，屬於類型 `object` ，它會傳回目前正由標記延伸所使用的資源。

> [!NOTE]
> XAML 剖析器允許將 `AppThemeBindingExtension` 類別縮寫為 `AppBindingTheme` 。

`Default`屬性是的 content 屬性 `AppThemeBindingExtension` 。 因此，對於以大括弧表示的 XAML 標記運算式，您可以去除 `Default=` 運算式的部分，前提是它是第一個引數。

[ **AppThemeBinding 示範**] 頁面會顯示如何使用 `AppThemeBinding` 標記延伸：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.AppThemeBindingDemoPage"
             Title="AppThemeBinding Demo">
    <ContentPage.Resources>

        <Style x:Key="labelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{AppThemeBinding Black, Light=Blue, Dark=Teal}" />
        </Style>

    </ContentPage.Resources>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{AppThemeBinding Light=Green, Dark=Red}" />
        <Label Text="This text is black by default, blue in light mode, and teal in dark mode."
               Style="{StaticResource labelStyle}" />
    </StackLayout>
</ContentPage>
```

在此範例中，當裝置使用其淺色主題時，第一個的文字色彩 [`Label`](xref:Xamarin.Forms.Label) 會設定為綠色，而當裝置使用其暗色調主題時，會設為紅色。 第二個會 `Label` [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 透過設定其屬性 [`Style`](xref:Xamarin.Forms.Style) 。 這會 `Style` 根據預設，將的文字色彩設定為 `Label` 黑色，當裝置使用其淺色主題時，會將設為藍色，而當裝置使用其暗色調主題時，則設為青色。

以下是程式執行情況：

![AppThemeBinding 示範](consuming-images/appthemebindingdemo.png "AppThemeBinding 示範")

## <a name="define-markup-extensions"></a>定義標記延伸

如果您遇到無法在中使用的 XAML 標記延伸模組 Xamarin.Forms ，您可以[自行建立](creating.md)。

## <a name="related-links"></a>相關連結

- [標記延伸（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [書籍中的 XAML 標記延伸章節 Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [動態樣式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms外層](~/xamarin-forms/app-fundamentals/shell/index.md)
- [回應應用程式中的系統主題變更 Xamarin.Forms](~/xamarin-forms/user-interface/theming/system-theme-changes.md)
