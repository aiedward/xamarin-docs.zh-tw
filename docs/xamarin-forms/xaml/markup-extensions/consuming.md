---
title: 使用 XAML 標記延伸
description: 本文說明如何使用 Xamarin XAML 標記延伸，藉由允許從各種來源設定元素屬性，來增強 XAML 的能力和彈性。
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/21/2020
ms.openlocfilehash: 7f46bc84543a1838241923ab91809bd01c706f44
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517098"
---
# <a name="consuming-xaml-markup-extensions"></a>使用 XAML 標記延伸

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

XAML 標記延伸可讓您從各種來源設定元素屬性，以增強 XAML 的強大功能和彈性。 數個 XAML 標記延伸是 XAML 2009 規格的一部分。 這些會以慣用`x`的命名空間前置詞出現在 XAML 檔案中，而且通常會使用此前置詞來參考。 本文討論下列標記延伸：

- [`x:Static`](#static)–參考靜態屬性、欄位或列舉成員。
- [`x:Reference`](#reference)–參考頁面上的命名元素。
- [`x:Type`](#type)–將屬性設定為`System.Type`物件。
- [`x:Array`](#array)–建立特定類型之物件的陣列。
- [`x:Null`](#null)–將屬性設定為`null`值。
- [`OnPlatform`](#onplatform)–自訂以每個平臺為基礎的 UI 外觀。
- [`OnIdiom`](#onidiom)–根據應用程式執行所在裝置的用法來自訂 UI 外觀。
- [`DataTemplate`](#datatemplate-markup-extension)–將類型轉換成[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。
- [`FontImage`](#fontimage-markup-extension)–在可顯示的任何視圖中顯示字型圖示`ImageSource`。
- [`OnAppTheme`](#onapptheme-markup-extension)–根據目前的系統主題來使用資源。

其他的 XAML 標記延伸在過去已經受到其他 XAML 的支援，而且 Xamarin 也支援。 其他文章中有更完整的說明：

- `StaticResource`-從資源字典參考物件，如[**資源字典**](~/xamarin-forms/xaml/resource-dictionaries.md)一文所述。
- `DynamicResource`-回應資源字典中物件的變更，如[**動態樣式**](~/xamarin-forms/user-interface/styles/dynamic.md)一文所述。
- `Binding`-在兩個物件的屬性之間建立連結，如[**資料**](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結一文所述。
- `TemplateBinding`-從控制項範本執行資料系結，如[**Xamarin 控制項範本**](~/xamarin-forms/app-fundamentals/templates/control-template.md)一文所述。
- `RelativeSource`-設定相對於系結目標之位置的系結來源，如[相關](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)系結一文所述。

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)版面配置會使用自訂標記延伸[`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)模組。 此標記延伸會在[**RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md)一文中說明。

<a name="static" />

## <a name="xstatic-markup-extension"></a>x:Static 標記延伸

[`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)類別支援`x:Static`標記延伸。 類別具有名[`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member)為的單一屬性，其`string`類型為，您將其設定為公用常數、靜態屬性、靜態欄位或列舉成員的名稱。

其中一個常見的使用`x:Static`方式是先定義具有一些常數或靜態變數的類別，例如[**MarkupExtensions**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)程式中`AppConstants`的這個小型類別：

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

[ **X:Static 示範**] 頁面會示範使用標記延伸`x:Static`的數種方式。 最詳細的方法會具`StaticExtension`現化`Label.FontSize`屬性元素標記之間的類別：

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

XAML 剖析器也可讓`StaticExtension`類別縮寫為`x:Static`：

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

這可以更進一步簡化，但變更會引進一些新的語法：其包含將`StaticExtension`類別和成員設定放在大括弧中。 產生的運算式會直接設定為`FontSize`屬性：

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

請注意，大括弧內*沒有*引號。 的`Member`屬性不再`StaticExtension`是 XML 屬性。 它會改為標記延伸之運算式的一部分。

就像您在使用`x:StaticExtension`它`x:Static`做為物件元素時的縮寫一樣，您也可以在大括弧內的運算式中將其縮寫：

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

`StaticExtension`類別具有參考屬性`ContentProperty` `Member`的屬性，這會將此屬性標記為類別的預設內容屬性。 對於以大括弧表示的 XAML 標記延伸，您可以消除`Member=`運算式的部分：

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

這是最常見的`x:Static`標記延伸形式。

**靜態示範**頁面包含其他兩個範例。 XAML 檔案的根標記包含 .NET `System`命名空間的 XML 命名空間宣告：

```xaml
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

這可讓`Label`字型大小設定為靜態欄位`Math.PI`。 這會產生相當小的文字，因此`Scale`屬性會設定為`Math.E`：

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

最後一個範例會顯示`Device.RuntimePlatform`值。 `Environment.NewLine`靜態屬性是用來在兩個`Span`物件之間插入新的行字元：

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

[`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)類別支援`x:Reference`標記延伸。 類別具有名[`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name)為的單一屬性，其`string`類型為，您將其設定為頁面上已指定名稱的專案名稱`x:Name`。 這個`Name`屬性是的`ReferenceExtension`content 屬性，因此當`Name=`出現在大括弧`x:Reference`時，不需要這樣做。

`x:Reference`標記延伸會以獨佔方式用於資料系結，這會在[**資料**](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結一文中更詳細地說明。

[ **X:Reference 示範**] 頁面會顯示兩`x:Reference`個使用資料系結的用法，第一個是用來`Source`設定`Binding`物件的屬性，第二個則是用來設定兩個`BindingContext`資料系結的屬性：

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

這`x:Reference`兩個運算式都會使用`ReferenceExtension`類別名稱的縮寫版本，並`Name=`消除運算式的部分。 在第一個範例中， `x:Reference`標記延伸模組內嵌在`Binding`標記延伸中。 請注意， `Source`和`StringFormat`設定會以逗號分隔。 以下是程式執行情況：

[![x:Reference 示範](consuming-images/referencedemo-small.png "x:Reference 示範")](consuming-images/referencedemo-large.png#lightbox "x:Reference 示範")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type 標記延伸

`x:Type`標記延伸是 c # [`typeof`](/dotnet/csharp/language-reference/keywords/typeof/)關鍵字的 XAML 對等用法。 [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)類別支援它，它會定義一個名[`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName)為且類型為的屬性，而此型`string`別會設定為類別或結構名稱。 `x:Type`標記延伸會傳回該[`System.Type`](xref:System.Type)類別或結構的物件。 `TypeName`是的 content 屬性`TypeExtension`，因此當`TypeName=`以大括弧顯示`x:Type`時，不需要這麼做。

在 Xamarin 中，有數個屬性具有類型`Type`的引數。 範例包括的[`TargetType`](xref:Xamarin.Forms.Style.TargetType)屬性`Style`，以及用來在泛型類別中指定引數的[x:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments)屬性。 不過，XAML 剖析器會自動`typeof`執行作業，而且在`x:Type`這些情況下不會使用標記延伸。

`x:Type` *需要一個位置，* 其中包含`x:Array`標記延伸，如下一[節](#array)所述。

當`x:Type`您在建立功能表，其中每個功能表項目都對應到特定類型的物件時，標記延伸也很有用。 您可以將`Type`物件與每個功能表項目建立關聯，然後在選取功能表項目時，具現化物件。

這是**標記延伸**程式中的`MainPage`導覽功能表運作方式。 **MainPage**包含`TableView` ，其中每個`TextCell`對應至程式中的特定頁面：

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

每`CommandParameter`個屬性都設定為`x:Type`參考其中一個其他頁面的標記延伸。 `Command`屬性會系結至名為`NavigateCommand`的屬性。 此屬性定義于程式`MainPage`代碼後置檔案中：

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

`NavigateCommand`屬性`Command`是一種物件，它會使用值`Type` &mdash;為的引數，來實作為`CommandParameter`執行命令。 方法會使用`Activator.CreateInstance`來具現化網頁，然後流覽至該頁面。 此函式的結束`BindingContext`處是將頁面的設定為本身，讓`Binding` on `Command`能夠正常執行。 如需此類型程式碼的詳細資訊，請參閱[**資料**](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結一文，特別是[**命令**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)文章。

**X:Type 示範**頁面會使用類似的技術來具現化 Xamarin. Forms 專案，並將其`StackLayout`加入至。 XAML 檔案一開始是由三`Button`個元素所`Command`組成`Binding` ，其屬性會設定`CommandParameter`為，而屬性會設定為三個 Xamarin 的類型。表單檢視：

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

程式碼後置檔案會定義並初始化`CreateCommand`屬性：

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

當按下時`Button`所執行的方法會建立引數的新實例、設定其`VerticalOptions`屬性，並將其新增至。 `StackLayout` 然後， `Button`這三個元素會與動態建立的視圖共用頁面：

[![x:Type 示範](consuming-images/typedemo-small.png "x:Type 示範")](consuming-images/typedemo-large.png#lightbox "x:Type 示範")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array 標記延伸

`x:Array`標記延伸可讓您在標記中定義陣列。 [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)類別支援它，它會定義兩個屬性：

- `Type`類型`Type`的，表示陣列中元素的類型。
- `Items`屬於類型`IList`的，也就是專案本身的集合。 這是的 content 屬性`ArrayExtension`。

`x:Array`標記延伸本身永遠不會以大括弧括住。 相反地`x:Array` ，開始和結束標記會分隔專案清單。 將`Type`屬性設定為`x:Type`標記延伸。

**X:Array 示範**頁面會顯示如何藉`x:Array` `ListView`由將`ItemsSource`屬性設定為數組，來使用將專案新增至。

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

會`ViewCell`為每個`BoxView`色彩專案建立簡單的：

[![x:Array 示範](consuming-images/arraydemo-small.png "x:Array 示範")](consuming-images/arraydemo-large.png#lightbox "x:Array 示範")

有數種方式可指定此陣列`Color`中的個別專案。 您可以使用`x:Static`標記延伸：

```xaml
<x:Static Member="Color.Blue" />
```

或者，您可以使用`StaticResource`從資源字典取出色彩：

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

[`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)類別支援`x:Null`標記延伸。 它沒有屬性，而只是 c # [`null`](/dotnet/csharp/language-reference/keywords/null/)關鍵字的 XAML 對等用法。

`x:Null`標記延伸非常少用，而且很少使用，但是如果您覺得有需要，就很高興它存在。

**X:Null 示範**頁面`x:Null`會說明可能很方便的一個案例。 `Style`假設您定義的隱含`Label` ，其中包含`Setter`將`FontFamily`屬性設定為平臺相依系列名稱的：

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

接著，您會發現其中一個`Label`元素的所有屬性設定都是隱含`Style`的，但您想要做`FontFamily`為預設值的除外。 您可以針對該`Style`目的定義另一個，但是較簡單的方法只是`FontFamily`將特定`Label`的屬性設定`x:Null`為，如中央`Label`所示。

以下是程式執行情況：

[![x:Null 示範](consuming-images/nulldemo-small.png "x:Null 示範")](consuming-images/nulldemo-large.png#lightbox "x:Null 示範")

請注意，有四`Label`個元素具有 serif 字型，但中心`Label`具有預設的 sans-serif 字型。

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>OnPlatform 標記延伸

`OnPlatform` 標記延伸可讓您自訂每個平台的 UI 外觀。 它提供與[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)和[`On`](xref:Xamarin.Forms.On)類別相同的功能，但具有更精確的標記法。

[`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension)類別支援`OnPlatform`標記延伸，其定義下列屬性：

- `Default`屬於類型`object`的，您會將設定為要套用至代表平臺之屬性的預設值。
- `Android`屬於類型`object`的，您會將設定為要在 Android 上套用的值。
- `GTK`屬於類型`object`的，您會將設定為要在 GTK 平臺上套用的值。
- `iOS`屬於類型`object`的，您會將設定為要在 iOS 上套用的值。
- `macOS`屬於類型`object`的，您會將設定為要在 macOS 上套用的值。
- `Tizen`屬於類型`object`的，您會將設定為要在 Tizen 平臺上套用的值。
- `UWP`屬於類型`object`的，您會將設定為要在通用 Windows 平臺上套用的值。
- `WPF`屬於類型`object`的，您會將設定為要在 Windows Presentation Foundation 平臺上套用的值。
- `Converter`屬於的`IValueConverter`類型，可以設定為`IValueConverter`執行。
- `ConverterParameter`型`object`別為的，可以設定為要傳遞至實作為`IValueConverter`的值。

> [!NOTE]
> XAML 剖析器允許將[`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension)類別縮寫為`OnPlatform`。

`Default`屬性是的`OnPlatformExtension`content 屬性。 因此，對於以大括弧表示的 XAML 標記運算式，您可以去除`Default=`運算式的部分，前提是它是第一個引數。 如果未`Default`設定屬性，則會預設為[`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue)屬性值，前提是標記延伸是以為[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)目標。

> [!IMPORTANT]
> XAML 剖析器預期會將正確類型的值提供給使用`OnPlatform`標記延伸的屬性。 如果需要型別轉換， `OnPlatform`標記延伸會嘗試使用由 Xamarin 提供的預設轉換器來執行。 不過，預設的轉換器無法執行某些類型轉換，而在這些情況下，應該將`Converter`屬性設定為「 `IValueConverter`執行」。

[ **OnPlatform 示範**] 頁面會顯示如何使用`OnPlatform`標記延伸：

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

在此範例中，所有`OnPlatform`三個運算式都會使用`OnPlatformExtension`類別名稱的縮寫版本。 這三`OnPlatform`個標記延伸會[`Color`](xref:Xamarin.Forms.BoxView.Color)將[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)的、 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)和屬性設定[`BoxView`](xref:Xamarin.Forms.BoxView)為 iOS、Android 和 UWP 上的不同值。 標記延伸也會針對未指定的平臺上的這些屬性提供預設值，同時排除運算式`Default=`的部分。 請注意，設定的標記延伸模組屬性是以逗號分隔。

以下是程式執行情況：

[![OnPlatform 示範](consuming-images/onplatformdemo-small.png "OnPlatform 示範")](consuming-images/onplatformdemo-large.png#lightbox "OnPlatform 示範")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>OnIdiom 標記延伸

`OnIdiom`標記延伸可讓您根據應用程式執行所在裝置的用法來自訂 UI 外觀。 [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension)類別支援它，它會定義下列屬性：

- `Default`屬於類型`object`的，您會將設定為要套用至代表裝置慣用語之屬性的預設值。
- `Phone`屬於類型`object`的，您將設定為要在手機上套用的值。
- `Tablet`屬於類型`object`的，您會將設定為要在平板電腦上套用的值。
- `Desktop`屬於類型`object`的，您將其設定為要套用至桌面平臺的值。
- `TV`屬於類型`object`的，您會將設定為要在電視平臺上套用的值。
- `Watch`屬於類型`object`的，您會將設定為要在監看式平臺上套用的值。
- `Converter`屬於的`IValueConverter`類型，可以設定為`IValueConverter`執行。
- `ConverterParameter`型`object`別為的，可以設定為要傳遞至實作為`IValueConverter`的值。

> [!NOTE]
> XAML 剖析器允許將[`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension)類別縮寫為`OnIdiom`。

`Default`屬性是的`OnIdiomExtension`content 屬性。 因此，對於以大括弧表示的 XAML 標記運算式，您可以去除`Default=`運算式的部分，前提是它是第一個引數。

> [!IMPORTANT]
> XAML 剖析器預期會將正確類型的值提供給使用`OnIdiom`標記延伸的屬性。 如果需要型別轉換， `OnIdiom`標記延伸會嘗試使用由 Xamarin 提供的預設轉換器來執行。 不過，預設的轉換器無法執行某些類型轉換，而在這些情況下，應該將`Converter`屬性設定為「 `IValueConverter`執行」。

[ **OnIdiom 示範**] 頁面會顯示如何使用`OnIdiom`標記延伸：

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

在此範例中，所有`OnIdiom`三個運算式都會使用`OnIdiomExtension`類別名稱的縮寫版本。 這三`OnIdiom`個標記延伸會[`Color`](xref:Xamarin.Forms.BoxView.Color)將[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)的、 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)和屬性設定[`BoxView`](xref:Xamarin.Forms.BoxView)為電話、平板電腦和桌面慣用語上的不同值。 標記延伸也會針對未指定的慣用語上的這些屬性提供預設值，同時排除運算式`Default=`的部分。 請注意，設定的標記延伸模組屬性是以逗號分隔。

以下是程式執行情況：

[![OnIdiom 示範](consuming-images/onidiomdemo-small.png "OnIdiom 示範")](consuming-images/onidiomdemo-large.png#lightbox "OnIdiom 示範")

## <a name="datatemplate-markup-extension"></a>DataTemplate 標記延伸

`DataTemplate`標記延伸可讓您將類型轉換成[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 `DataTemplateExtension`類別會支援，其定義類型`TypeName` `string`為的屬性，其會設定為要轉換成的類型名稱`DataTemplate`。 `TypeName`屬性是的`DataTemplateExtension`content 屬性。 因此，對於以大括弧表示的 XAML 標記運算式，您可以消除`TypeName=`運算式的部分。

> [!NOTE]
> XAML 剖析器允許將`DataTemplateExtension`類別縮寫為`DataTemplate`。

此標記延伸的一般用法是在 Shell 應用程式中，如下列範例所示：

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

在此範例中`MonkeysPage` ，會從[`ContentPage`](xref:Xamarin.Forms.ContentPage)轉換成[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，這會設定為`ShellContent.ContentTemplate`屬性的值。 這可確保`MonkeysPage`只會在流覽至頁面時發生，而不是在應用程式啟動時建立。

如需 Shell 應用程式的詳細資訊，請參閱[Xamarin. Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="fontimage-markup-extension"></a>FontImage 標記延伸

`FontImage`標記延伸可讓您在任何可顯示的視圖中顯示字型圖示`ImageSource`。 它會提供與`FontImageSource`類別相同的功能，但具有更精確的標記法。

`FontImageExtension`類別支援`FontImage`標記延伸，其定義下列屬性：

- `FontFamily`類型`string`的，字型圖示所屬的字型系列。
- `Glyph`屬於類型`string`的 unicode 字元值（字型圖示）。
- `Color`類型[`Color`](xref:Xamarin.Forms.Color)的，顯示字型圖示時要使用的色彩。
- `Size`類型`double`的，呈現的字型圖示的大小（以與裝置無關的單位）。 預設值是 30。 此外，這個屬性也可以設定為已命名的字型大小。

> [!NOTE]
> XAML 剖析器允許將`FontImageExtension`類別縮寫為`FontImage`。

`Glyph`屬性是的`FontImageExtension`content 屬性。 因此，對於以大括弧表示的 XAML 標記運算式，您可以去除`Glyph=`運算式的部分，前提是它是第一個引數。

[ **FontImage 示範**] 頁面會顯示如何使用`FontImage`標記延伸：

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

在此範例中， `FontImageExtension`類別名稱的縮寫版本是用來從中的 Ionicons 字型系列顯示 XBox 圖示。 [`Image`](xref:Xamarin.Forms.Image) 運算式也會使用`OnPlatform`標記延伸，在 IOS 和`FontFamily` Android 上指定不同的屬性值。 此外，運算式的`Glyph=`部分會被排除，而設定的標記延伸屬性會以逗號分隔。 請注意，當圖示的 unicode 字元是`\uf30c`時，它必須在 XAML 中以轉義，因此會`&#xf30c;`變成。

以下是程式執行情況：

[![FontImage 標記延伸的螢幕擷取畫面](consuming-images/fontimagedemo.png "FontImage 示範")](consuming-images/fontimagedemo-large.png#lightbox "FontImage 示範")

如需在`FontImageSource`物件中指定字型圖示資料來顯示字型圖示的詳細資訊，請參閱[顯示字型圖示](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="onapptheme-markup-extension"></a>OnAppTheme 標記延伸

`OnAppTheme`標記延伸可讓您根據目前的系統主題，指定要使用的資源，例如影像或色彩。 它會提供與`OnAppTheme<T>`類別相同的功能，但具有更精確的標記法。

> [!IMPORTANT]
> 標記`OnAppTheme`延伸具有最低的作業系統需求。 如需詳細資訊，請參閱[回應 Xamarin 應用程式中的系統主題變更](~/xamarin-forms/user-interface/theming/system-theme-changes.md)。

`OnAppThemeExtension`類別支援`OnAppTheme`標記延伸，其定義下列屬性：

- `Default`，屬於類型`object`，您將其設定為預設使用的資源。
- `Light`，屬於類型`object`，當裝置使用其淺色主題時，您會將其設定為要使用的資源。
- `Dark`，屬於類型`object`，當裝置使用其暗色調主題時，您會將其設定為要使用的資源。
- `Value`，屬於類型`object`，它會傳回目前正由標記延伸所使用的資源。
- `Converter`屬於的`IValueConverter`類型，可以設定為`IValueConverter`執行。
- `ConverterParameter`型`object`別為的，可以設定為要傳遞至實作為`IValueConverter`的值。

> [!NOTE]
> XAML 剖析器允許將`OnAppThemeExtension`類別縮寫為`OnAppTheme`。

`Default`屬性是的`OnAppThemeExtension`content 屬性。 因此，對於以大括弧表示的 XAML 標記運算式，您可以去除`Default=`運算式的部分，前提是它是第一個引數。

[ **OnAppTheme 示範**] 頁面會顯示如何使用`OnAppTheme`標記延伸：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.OnAppThemeDemoPage"
             Title="OnAppTheme Demo">
    <ContentPage.Resources>

        <Style x:Key="labelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{OnAppTheme Black, Light=Blue, Dark=Teal}" />
        </Style>

    </ContentPage.Resources>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{OnAppTheme Light=Green, Dark=Red}" />
        <Label Text="This text is black by default, blue in light mode, and teal in dark mode."
               Style="{DynamicResource labelStyle}" />
    </StackLayout>
</ContentPage>
```

在此範例中，當裝置使用其淺色[`Label`](xref:Xamarin.Forms.Label)主題時，第一個的文字色彩會設定為綠色，而當裝置使用其暗色調主題時，會設為紅色。 第二`Label`個會[`TextColor`](xref:Xamarin.Forms.Label.TextColor)透過設定其屬性[`Style`](xref:Xamarin.Forms.Style)。 這`Style`會根據預設，將的`Label`文字色彩設定為黑色，當裝置使用其淺色主題時，會將設為藍色，而當裝置使用其暗色調主題時，則設為青色。

> [!NOTE]
> 使用標記延伸的會套用至具有`DynamicResource`標記延伸的控制項，如此一來，當系統主題變更時，應用程式的 UI 就會更新。 `OnAppTheme` [`Style`](xref:Xamarin.Forms.Style)

以下是程式執行情況：

![OnAppTheme 示範](consuming-images/onappthemedemo.png "OnAppTheme 示範")

## <a name="define-markup-extensions"></a>定義標記延伸

如果您遇到了無法在 Xamarin 中使用的 XAML 標記延伸模組，則可以[自行建立](creating.md)。

## <a name="related-links"></a>相關連結

- [標記延伸（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Xamarin 的 XAML 標記延伸章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [動態樣式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [回應 Xamarin 中的系統主題變更。 Forms 應用程式](~/xamarin-forms/user-interface/theming/system-theme-changes.md)
