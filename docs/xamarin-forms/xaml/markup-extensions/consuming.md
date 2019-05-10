---
title: 使用 XAML 標記延伸
description: 這篇文章說明如何使用 Xamarin.Forms XAML 標記延伸來增強的威力與彈性的 XAML，藉由將從各種來源的項目屬性。
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2019
ms.openlocfilehash: fd67072953f0fc4e448fee7edeec84760ebbda9a
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65048329"
---
# <a name="consuming-xaml-markup-extensions"></a>使用 XAML 標記延伸

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)

XAML 標記延伸模組可協助增強的威力與彈性的 XAML，藉由將從各種來源的項目屬性。 數個 XAML 標記延伸是 XAML 2009 規格的一部分。 這些會出現在具有自訂的 XAML 檔案`x`命名空間前置詞，而且通常參照此前置詞。 這篇文章討論下列標記延伸模組：

- [`x:Static`](#static) – 參考靜態屬性、 欄位或列舉型別成員。
- [`x:Reference`](#reference) – 名為頁面元素的參考。
- [`x:Type`](#type) -將屬性設定為`System.Type`物件。
- [`x:Array`](#array) – 建構特定類型之物件的陣列。
- [`x:Null`](#null) -將屬性設定為`null`值。
- [`OnPlatform`](#onplatform) – 自訂每個平台為基礎的 UI 外觀。
- [`OnIdiom`](#onidiom) – 自訂的裝置執行應用程式的慣用句為基礎的 UI 外觀。
- [`DataTemplate`](#datatemplate-markup-extension) -將轉換成的型別[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。

其他的 XAML 標記延伸在過去已經支援其他 XAML 實作中，和 Xamarin.Forms 也支援。 這些是其他文章中更完整說明：

- `StaticResource` &ndash; 從資源字典，參考物件，如本文所述[**的資源字典**](~/xamarin-forms/xaml/resource-dictionaries.md)。
- `DynamicResource` &ndash; 回應物件中的資源字典中，變更文件中所述[**動態樣式**](~/xamarin-forms/user-interface/styles/dynamic.md)。
- `Binding` &ndash; 之間建立連結屬性的兩個物件，如本文所述[**資料繫結**](~/xamarin-forms/app-fundamentals/data-binding/index.md)。
- `TemplateBinding` &ndash; 從 [控制項] 範本中，執行資料繫結，如本文所述[**控制項範本中的繫結**](~/xamarin-forms/app-fundamentals/templates/control-templates/template-binding.md)。

[ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout)版面配置會使用自訂標記延伸[ `ConstraintExpression` ](xref:Xamarin.Forms.ConstraintExpression)。 這個標記延伸文件中所述[ **RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md)。

<a name="static" />

## <a name="xstatic-markup-extension"></a>x:Static 標記延伸

`x:Static`支援標記延伸[ `StaticExtension` ](xref:Xamarin.Forms.Xaml.StaticExtension)類別。 類別具有一個名為的單一屬性[ `Member` ](xref:Xamarin.Forms.Xaml.StaticExtension.Member)型別的`string`您將設定為公用的常數、 靜態屬性、 靜態欄位或列舉成員的名稱。

若要使用的一個常見方式`x:Static`是第一次定義類別有一些常數或靜態變數，例如此小`AppConstants`類別中[ **Markupextension** ](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)程式：

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

**X:static Demo**頁面會示範數種使用方式`x:Static`標記延伸。 最詳細的方法具現化`StaticExtension`類別之間`Label.FontSize`屬性項目標記：

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

XAML 剖析器也可讓`StaticExtension`類別，以縮寫成`x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

這可以進一步延伸，簡化，但變更導入了一些新的語法：它包含將放`StaticExtension`類別和成員在大括號中設定。 產生的運算式設定為直接`FontSize`屬性：

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

請注意，有*沒有*括號內的引號。 `Member`屬性`StaticExtension`不再 XML 屬性。 而是標記延伸之運算式的一部分。

就像您可以將縮寫`x:StaticExtension`至`x:Static`時您可以使用它做為物件元素，您可以也將它縮簡大括號內運算式中：

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

`StaticExtension`類別具有`ContentProperty`參考屬性的屬性`Member`，將會標示為類別的預設內容屬性的這個屬性。 XAML 標記延伸模組以大括號表示，您就可以排除`Member=`運算式的一部分：

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

這是最常見的形式`x:Static`標記延伸。

**靜態示範**頁面包含兩個其他範例。 XAML 檔案根標記包含適用於.NET 的 XML 命名空間宣告`System`命名空間：

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

這可讓`Label`設為靜態欄位的字型大小`Math.PI`。 這會導致較小的文字，因此`Scale`屬性設定為`Math.E`:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

最後一個範例顯示`Device.RuntimePlatform`值。 `Environment.NewLine`靜態屬性用來插入新行字元，兩者之間`Span`物件：

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

以下是執行範例：

[![X:static 示範](consuming-images/staticdemo-small.png "X:static 示範")](consuming-images/staticdemo-large.png#lightbox "X:static 示範")

<a name="reference" />

## <a name="xreference-markup-extension"></a>x:Reference 標記延伸

`x:Reference`支援標記延伸[ `ReferenceExtension` ](xref:Xamarin.Forms.Xaml.ReferenceExtension)類別。 類別具有一個名為的單一屬性[ `Name` ](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name)型別的`string`您設定的頁面上所提供的名稱的項目名稱`x:Name`。 這`Name`屬性是內容的屬性`ReferenceExtension`，因此`Name=`時，不需要`x:Reference`會出現在大括號。

`x:Reference`標記延伸模組只搭配資料繫結，會更詳細文件中所述[**的資料繫結**](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

**X:reference Demo**頁面會顯示兩種用法`x:Reference`以資料繫結，它用來設定第一個`Source`屬性`Binding`物件，而它用來設定第二個`BindingContext`兩個資料繫結的屬性：

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

兩者`x:Reference`運算式使用的縮寫的版本`ReferenceExtension`類別名稱，並消除`Name=`運算式的一部分。 在第一個範例中，`x:Reference`標記延伸模組會內嵌在`Binding`標記延伸。 請注意，`Source`和`StringFormat`設定會以逗號分隔。 以下是執行的程式：

[![X:reference 示範](consuming-images/referencedemo-small.png "X:reference 示範")](consuming-images/referencedemo-large.png#lightbox "X:reference 示範")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type 標記延伸

`x:Type`標記延伸是 XAML 相當於 C# [ `typeof` ](/dotnet/csharp/language-reference/keywords/typeof/)關鍵字。 它受到[ `TypeExtension` ](xref:Xamarin.Forms.Xaml.TypeExtension)類別，定義一個名為的屬性[ `TypeName` ](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName)型別的`string`設定類別或結構的名稱。 `x:Type`標記延伸模組會傳回[ `System.Type` ](xref:System.Type)該類別或結構的物件。 `TypeName` 內容屬性的`TypeExtension`，因此`TypeName=`時，不需要`x:Type`使用大括號會出現。

在 Xamarin.Forms 中，有有類型引數的數個屬性`Type`。 範例包括[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)屬性`Style`，而[X:typearguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments)屬性會用來在泛型類別中指定的引數。 不過，XAML 剖析器會執行`typeof`作業自動執行，而`x:Type`標記延伸模組不會在這些情況下。

同一個地方所在`x:Type`*是*必須是`x:Array`中所述的標記延伸[下一節](#array)。

`x:Type`標記延伸模組時也很有用建構的功能表，其中每個功能表項目對應至特定類型的物件。 您可以建立關聯`Type`物件與每個功能表項目，並選取功能表項目時，再執行個體化物件。

這是如何在導覽功能表`MainPage`中**標記延伸**程式運作。 **MainPage.xaml**檔案包含`TableView`與每個`TextCell`對應程式中的特定頁面：

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

以下是中的 開啟主頁面**標記延伸**:

[![主頁面](consuming-images/mainpage-small.png "主要頁面")](consuming-images/mainpage-large.png#lightbox "主頁面")

每個`CommandParameter`屬性設定為`x:Type`參考其中一個其他頁面的標記延伸。 `Command`屬性繫結至屬性，名為`NavigateCommand`。 這個屬性定義在`MainPage`程式碼後置檔案：

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

`NavigateCommand`屬性是`Command`可實作 execute 命令的引數類型的物件`Type`&mdash;的值`CommandParameter`。 此方法會使用`Activator.CreateInstance`來具現化的頁面，然後巡覽至它。 建構函式結束時，會設定`BindingContext`本身的頁面，讓`Binding`上`Command`運作。 請參閱[**資料繫結**](~/xamarin-forms/app-fundamentals/data-binding/index.md)文章和特別[ **Commanding** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)這種類型的程式碼的更多詳細的文件。

**X:type Demo**頁面會使用類似的技巧，來具現化 Xamarin.Forms 項目，並將其新增至`StackLayout`。 XAML 檔案一開始包含三個`Button`項目及其`Command`屬性設為`Binding`和`CommandParameter`設為三個 Xamarin.Forms 檢視類型的屬性：

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

定義程式碼後置檔案，並初始化`CreateCommand`屬性：

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

方法時執行`Button`按下之引數的新執行個體、 設定其`VerticalOptions`屬性，並將它加入`StackLayout`。 三個`Button`然後動態建立的檢視與共用 頁面的項目：

[![X:type 示範](consuming-images/typedemo-small.png "X:type 示範")](consuming-images/typedemo-large.png#lightbox "X:type 示範")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array 標記延伸

`x:Array`標記延伸可讓您在標記中定義陣列。 它受到[ `ArrayExtension` ](xref:Xamarin.Forms.Xaml.ArrayExtension)類別，定義兩個屬性：

- `Type` 型別的`Type`，表示陣列中元素的型別。
- `Items` 型別的`IList`，這是集合的項目本身。 這是內容屬性`ArrayExtension`。

`x:Array`標記延伸模組本身不會出現在大括號。 相反地，`x:Array`開始與結束標記分隔的項目清單。 設定`Type`屬性設`x:Type`標記延伸。

**X:array Demo**頁面會顯示如何使用`x:Array`將項目加入`ListView`藉由設定`ItemsSource`陣列的屬性：

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

`ViewCell`建立一個簡單`BoxView`每個色彩項目：

[![X:array 示範](consuming-images/arraydemo-small.png "X:array 示範")](consuming-images/arraydemo-large.png#lightbox "X:array 示範")

有數種方式來指定個別`Color`此陣列中的項目。 您可以使用`x:Static`標記延伸模組：

```xaml
<x:Static Member="Color.Blue" />
```

或者，您可以使用`StaticResource`來擷取資源字典中的一種色彩：

```xaml
<StaticResource Key="myColor" />
```

這篇文章的結尾，您會看到自訂的 XAML 標記延伸模組，也會建立新的色彩值：

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

定義時常見的類型，例如字串或數字的陣列，使用中列出的標記[**傳遞建構函式引數**](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)文章，以分隔的值。

<a name="null" />

## <a name="xnull-markup-extension"></a>x:Null 標記延伸

`x:Null`支援標記延伸[ `NullExtension` ](xref:Xamarin.Forms.Xaml.NullExtension)類別。 它有沒有屬性，而且相當於只是 XAML 的 C# [ `null` ](/dotnet/csharp/language-reference/keywords/null/)關鍵字。

`x:Null`標記延伸是很少會需要且很少使用，但如果您發現需要它，您會很高興它存在。

**X:null Demo**頁面上說明一種情況時`x:Null`可能都很方便。 假設您定義隱含`Style`for`Label`包含`Setter`，設定`FontFamily`屬性設為平台相依的系列名稱：

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

然後您會發現其中一個`Label`項目，您想要在隱含的所有屬性設定`Style`除了`FontFamily`，您想要的預設值。 您可以定義另一個`Style`針對該目的，但更簡單的方法是直接設定`FontFamily`屬性的特定`Label`要`x:Null`中心 」 所示， `Label`。

以下是執行的程式：

[![X:null 示範](consuming-images/nulldemo-small.png "X:null 示範")](consuming-images/nulldemo-large.png#lightbox "X:null 示範")

通知該四`Label`項目有襯線字型，但中心`Label`具有預設 sans serif 字型。

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>OnPlatform 標記延伸

`OnPlatform`標記延伸可讓您自訂每個平台為基礎的 UI 外觀。 它提供與相同的功能[ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1)並[ `On` ](xref:Xamarin.Forms.On)類別，但更簡潔的表示法。

`OnPlatform`支援標記延伸[ `OnPlatformExtension` ](xref:Xamarin.Forms.Xaml.OnPlatformExtension)類別，定義下列屬性：

- `Default` 型別的`object`，您要套用至代表平台的屬性設定為預設值。
- `Android` 型別的`object`，您在 Android 上套用設定的值。
- `GTK` 型別的`object`，要套用 GTK 平台上設定的值。
- `iOS` 型別的`object`，您要在 iOS 上套用設定值。
- `macOS` 型別的`object`，您要套用在 macOS 上設定的值。
- `Tizen` 型別的`object`，要套用 Tizen 平台上設定的值。
- `UWP` 型別的`object`，通用 Windows 平台上套用設定的值。
- `WPF` 型別的`object`，您要在 Windows Presentation Foundation 平台上套用設定值。
- `Converter` 型別的`IValueConverter`，，您將設定為`IValueConverter`實作。
- `ConverterParameter` 型別的`object`，您設定的值傳遞至`IValueConverter`實作。

> [!NOTE]
> XAML 剖析器允許[ `OnPlatformExtension` ](xref:Xamarin.Forms.Xaml.OnPlatformExtension)類別，以縮寫成`OnPlatform`。

`Default`屬性是內容屬性`OnPlatformExtension`。 因此，對於 XAML 標記運算式以大括號表示，您可以排除`Default=`一部分的運算式，只要是第一個引數。

> [!IMPORTANT]
> XAML 剖析器會預期正確類型的值，將提供給內容取用`OnPlatform`標記延伸。 如果型別轉換為有需要，`OnPlatform`標記延伸模組會嘗試執行使用 Xamarin.Forms 所提供的預設轉換子。 不過，有預設轉換子，在這些情況下，無法執行一些類型轉換`Converter`屬性應設為`IValueConverter`實作。

**OnPlatform Demo**頁面會顯示如何使用`OnPlatform`標記延伸模組：

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

在此範例中，這三`OnPlatform`運算式使用的縮寫的版本`OnPlatformExtension`類別名稱。 三個`OnPlatform`標記延伸模組組[ `Color` ](xref:Xamarin.Forms.BoxView.Color)， [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)，並[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)屬性[`BoxView` ](xref:Xamarin.Forms.BoxView) iOS、 Android 及 UWP 上的不同值。 標記延伸也提供預設值為未指定，同時不在平台上的這些屬性`Default=`運算式的一部分。 請注意，在以逗號分隔的標記延伸模組屬性所設定。

以下是執行的程式：

[![OnPlatform 示範](consuming-images/onplatformdemo-small.png "OnPlatform 示範")](consuming-images/onplatformdemo-large.png#lightbox "OnPlatform 示範")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>OnIdiom 標記延伸

`OnIdiom`標記延伸可讓您自訂的裝置執行應用程式的慣用句為基礎的 UI 外觀。 它受到[ `OnIdiomExtension` ](xref:Xamarin.Forms.Xaml.OnIdiomExtension)類別，定義下列屬性：

- `Default` 型別的`object`，您要套用至代表裝置慣用語的屬性設定為預設值。
- `Phone` 型別的`object`，您在手機上套用設定的值。
- `Tablet` 型別的`object`，您要在平板電腦上套用設定值。
- `Desktop` 型別的`object`，您在桌面平台上套用設定的值。
- `TV` 型別的`object`，要套用在電視平台上設定的值。
- `Watch` 型別的`object`，您要監看式平台上套用設定的值。
- `Converter` 型別的`IValueConverter`，，您將設定為`IValueConverter`實作。
- `ConverterParameter` 型別的`object`，您設定的值傳遞至`IValueConverter`實作。

> [!NOTE]
> XAML 剖析器允許[ `OnIdiomExtension` ](xref:Xamarin.Forms.Xaml.OnIdiomExtension)類別，以縮寫成`OnIdiom`。

`Default`屬性是內容屬性`OnIdiomExtension`。 因此，對於 XAML 標記運算式以大括號表示，您可以排除`Default=`一部分的運算式，只要是第一個引數。

> [!IMPORTANT]
> XAML 剖析器會預期正確類型的值，將提供給內容取用`OnIdiom`標記延伸。 如果型別轉換為有需要，`OnIdiom`標記延伸模組會嘗試執行使用 Xamarin.Forms 所提供的預設轉換子。 不過，有預設轉換子，在這些情況下，無法執行一些類型轉換`Converter`屬性應設為`IValueConverter`實作。

**OnIdiom Demo**頁面會顯示如何使用`OnIdiom`標記延伸模組：

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

在此範例中，這三`OnIdiom`運算式使用的縮寫的版本`OnIdiomExtension`類別名稱。 三個`OnIdiom`標記延伸模組組[ `Color` ](xref:Xamarin.Forms.BoxView.Color)， [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)，並[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)屬性[`BoxView` ](xref:Xamarin.Forms.BoxView)電話、 平板電腦和桌上型電腦的習慣用語上的不同值。 標記延伸也提供預設值，這些屬性中未指定，同時避免慣用語`Default=`運算式的一部分。 請注意，在以逗號分隔的標記延伸模組屬性所設定。

以下是執行的程式：

[![OnIdiom 示範](consuming-images/onidiomdemo-small.png "OnIdiom 示範")](consuming-images/onidiomdemo-large.png#lightbox "OnIdiom 示範")

## <a name="datatemplate-markup-extension"></a>DataTemplate 標記延伸

`DataTemplate`標記延伸可讓您轉換成類型[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。 它受到`DataTemplateExtension`類別，定義`TypeName`型別的屬性`string`，也就是設定為型別名稱，以轉換成`DataTemplate`。 `TypeName`屬性是內容屬性`DataTemplateExtension`。 因此，對於 XAML 標記運算式以大括號表示，您可以排除`TypeName=`運算式的一部分。

> [!NOTE]
> XAML 剖析器允許`DataTemplateExtension`類別，以縮寫成`DataTemplate`。

這個標記延伸的一般用法是在 Shell 應用程式中，如下列範例所示：

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

在此範例中，`MonkeysPage`會從轉換[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)來[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)，此值設定的值為`ShellContent.ContentTemplate`屬性。 這可確保`MonkeysPage`是只建立巡覽至頁面時，而不是在應用程式啟動。

如需有關 Shell 應用程式的詳細資訊，請參閱[Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="define-your-own-markup-extensions"></a>定義您自己的標記延伸

如果您遇到無法使用 Xamarin.Forms 中的 XAML 標記延伸模組的需求，您可以[建立您自己](creating.md)。

## <a name="related-links"></a>相關連結

- [標記延伸模組 （範例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Xamarin.Forms 通訊錄中的 XAML 標記延伸模組的章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [動態樣式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。
