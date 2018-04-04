---
title: 使用 XAML 標記延伸
description: 使用 Xamarin.Forms 中可用的 XAML 標記延伸
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: 25eada483e8bd2ce95cb3101dfe873ea38b283ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="consuming-xaml-markup-extensions"></a>使用 XAML 標記延伸

XAML 標記延伸強化的功能和彈性的 XAML 允許從各種來源的設定項目屬性。 數個 XAML 標記延伸是 XAML 2009 規格的一部分。 這些會出現在 XAML 檔案具有可`x`命名空間前置詞，且一般是指使用此前置詞。 這些都是以下列各節所述：

- [`x:Static`](#static) &ndash; 參考靜態屬性、 欄位或列舉型別成員。
- [`x:Reference`](#reference) &ndash; 名為頁面元素的參考。
- [`x:Type`](#type) &ndash; 將屬性設為`System.Type`物件。
- [`x:Array`](#array) &ndash; 建構特定類型之物件的陣列。
- [`x:Null`](#null) &ndash; 將屬性設為`null`值。

其他三個 XAML 標記延伸在過去已由其他 XAML 實作中，支援和 Xamarin.Forms 也支援。 這些會更詳細說明其他文件：

- `StaticResource` &ndash; 發行項中所述，從資源字典中，參考物件[**資源字典**](~/xamarin-forms/xaml/resource-dictionaries.md)。
- `DynamicResource` &ndash; 回應中的資源字典中的物件變更發行項中所述[**動態樣式**](~/xamarin-forms/user-interface/styles/dynamic.md)。
- `Binding` &ndash; 發行項中所述，建立兩個物件的屬性之間的連結[**資料繫結**](~/xamarin-forms/app-fundamentals/data-binding/index.md)。
- `TemplateBinding` &ndash; 發行項中所述，執行資料繫結控制項範本，從 [**繫結的控制項範本**] 輔助線/xamarin-form/應用程式的基本概念/範本/控制項-範本/範本繫結 /)

[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)版面配置使用的自訂標記延伸會利用[ `ConstraintExpression` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ConstraintExpression/)。 這個標記延伸文件中所述[ **RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md)。

<a name="static" />

## <a name="xstatic-markup-extension"></a>x:Static 標記延伸

`x:Static`標記延伸支援[ `StaticExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/)類別。 此類別具有單一內容，名為[ `Member` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.StaticExtension.Member/)型別的`string`設公用常數、 靜態屬性、 靜態欄位或列舉型別成員的名稱。

若要使用的常用方式`x:Static`是先定義之類別的部分常數或靜態變數，例如此小`AppConstants`類別[ **MarkupExtensions** ](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)程式：

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

**X:static 示範**頁面示範數種方式使用`x:Static`標記延伸。 最詳細方法具現化`StaticExtension`類別之間`Label.FontSize`屬性項目標記：

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

XAML 剖析器也可讓`StaticExtension`類別來縮寫成`x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

這可以更進一步，簡化，但變更導入了一些新的語法： 組成，而將`StaticExtension`類別和成員在大括號中設定。 產生的運算式會直接設定`FontSize`屬性：

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

請注意，有*沒有*括號內的引號。 `Member`屬性`StaticExtension`不再 XML 屬性。 它是改為標記延伸運算式的一部分。

就像您可以將縮寫`x:StaticExtension`至`x:Static`時您可以使用它做為物件項目，您可以也將它縮簡大括號內運算式中：

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

`StaticExtension`類別具有`ContentProperty`屬性參考的屬性`Member`，將會標示為類別的預設內容屬性的這個屬性。 XAML 標記延伸使用大括號表示，您就可以排除`Member=`運算式的一部分：

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

這是最常見的形式`x:Static`標記延伸。

**靜態示範**頁面包含兩個其他範例。 根標記的 XAML 檔案包含 XML 命名空間宣告為.net`System`命名空間：

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

這可讓`Label`字型大小設為靜態欄位`Math.PI`。 這樣會造成較小的文字，所以`Scale`屬性設定為`Math.E`:

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

以下是所有三個平台上執行的範例：

[![X:static 示範](consuming-images/staticdemo-small.png "X:static 示範")](consuming-images/staticdemo-large.png#lightbox "X:static 示範")

<a name="reference" />

## <a name="xreference-markup-extension"></a>x:Reference 標記延伸

`x:Reference`標記延伸支援[ `ReferenceExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/)類別。 此類別具有單一內容，名為[ `Name` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.ReferenceExtension.Name/)型別的`string`設了指定的名稱與頁面上的項目名稱`x:Name`。 這`Name`屬性是內容屬性`ReferenceExtension`，因此`Name=`時，不需要`x:Reference`會出現在大括號。

`x:Reference`標記延伸專門用於資料繫結詳細文章中所述[**資料繫結**](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

**X:reference 示範**頁面會顯示的兩種用法`x:Reference`與資料繫結，用來設定第一個`Source`屬性`Binding`物件，而第二個用來設定`BindingContext`兩個資料繫結的屬性：

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

同時`x:Reference`運算式使用的縮寫的版本`ReferenceExtension`類別名稱，並消除`Name=`運算式的一部分。 在第一個範例中，`x:Reference`標記延伸會內嵌在`Binding`標記延伸。 請注意，`Source`和`StringFormat`設定會以逗號分隔。 以下是所有三個平台上執行的程式：

[![X:reference 示範](consuming-images/referencedemo-small.png "X:reference 示範")](consuming-images/referencedemo-large.png#lightbox "X:reference 示範")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type 標記延伸

`x:Type`標記延伸是 XAML 相當於 C# [ `typeof` ](/dotnet/csharp/language-reference/keywords/typeof/)關鍵字。 它支援[ `TypeExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/)類別，定義名為一個內容[ `TypeName` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.TypeExtension.TypeName/)型別的`string`設定為類別或結構的名稱。 `x:Type`標記延伸傳回[ `System.Type` ](https://developer.xamarin.com/api/type/System.Type/)該類別或結構的物件。 `TypeName` 表示內容屬性的`TypeExtension`，因此`TypeName=`時，不需要`x:Type`隨即出現，並大括號。

在透過 Xamarin.Forms，有數個屬性具有類型引數`Type`。 範例包括[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)屬性`Style`，而[X:typearguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments)屬性會用來指定泛型類別中的引數。 不過，XAML 剖析器會執行`typeof`作業自動執行，而`x:Type`標記延伸不是在這些情況下。

同一個地方其中`x:Type`*是*需要是使用`x:Array`標記延伸中所述[下一節](#array)。

`x:Type`標記延伸時也很有用建構的功能表，其中每個功能表項目都會對應到特定類型的物件。 您可以將關聯`Type`物件與每個功能表項目，並選取功能表項目時，再執行個體化物件。

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

以下是開啟主頁面**標記延伸**:

[![主頁面](consuming-images/mainpage-small.png "主要頁面")](consuming-images/mainpage-large.png#lightbox "主頁面")

每個`CommandParameter`屬性設定為`x:Type`參考其中一個其他頁面的標記延伸。 `Command`屬性繫結至具名屬性`NavigateCommand`。 這個屬性定義在`MainPage`程式碼後置檔案：

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

`NavigateCommand`屬性是`Command`實作 execute 命令的引數的型別物件`Type`&mdash;值`CommandParameter`。 此方法會使用`Activator.CreateInstance`來具現化 頁面，並接著移動它。 建構函式結束時，會設定`BindingContext`本身的頁面，可讓`Binding`上`Command`運作。 請參閱[**資料繫結**](~/xamarin-forms/app-fundamentals/data-binding/index.md)發行項，尤其是[ **Commanding** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)如需詳細資訊，此類型的程式碼的相關發行項。

**X:type 示範**頁面會使用類似的技術，來具現化 Xamarin.Forms 項目，並將其新增至`StackLayout`。 XAML 檔案中一開始包含三個`Button`的項目及其`Command`屬性設定為`Binding`和`CommandParameter`屬性設定為三個 Xamarin.Forms 檢視類型：

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

方法時執行`Button`按下的引數的新執行個體、 設定其`VerticalOptions`屬性，並將它加入`StackLayout`。 三個`Button`項目然後分享頁面具有動態建立的檢視：

[![x:Type Demo](consuming-images/typedemo-small.png "x:Type Demo")](consuming-images/typedemo-large.png#lightbox "x:Type Demo")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array 標記延伸

`x:Array`標記延伸可讓您在標記中定義陣列。 它會受到[ `ArrayExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/)類別，定義兩個屬性：

- `Type` 型別的`Type`，表示陣列中項目的類型。
- `Items` 型別的`IList`，這是集合的項目本身。 這是內容屬性`ArrayExtension`。

`x:Array`標記延伸本身不會出現在大括號。 相反地，`x:Array`開始和結束標記分隔的項目清單。 設定`Type`屬性`x:Type`標記延伸。

**X:array 示範**頁面會顯示如何使用`x:Array`加入項目到`ListView`藉由設定`ItemsSource`陣列的屬性：

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

`ViewCell`建立簡單`BoxView`每個色彩項目：

[![x:Array Demo](consuming-images/arraydemo-small.png "x:Array Demo")](consuming-images/arraydemo-large.png#lightbox "x:Array Demo")

有數種方法來指定個別`Color`此陣列中的項目。 您可以使用`x:Static`標記延伸：

```xaml
<x:Static Member="Color.Blue" />
```

或者，您可以使用`StaticResource`擷取的資源字典中的色彩：

```xaml
<StaticResource Key="myColor" />
```

這篇文章結束時，您會看到自訂的 XAML 標記延伸模組，也會建立新的色彩值：

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

定義時常見的類型，例如字串或數字的陣列，使用中列出的標記[**傳遞建構函式引數**](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)分隔值的發行項。

<a name="null" />

## <a name="xnull-markup-extension"></a>x:Null 標記延伸

`x:Null`標記延伸支援[ `NullExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/)類別。 它沒有任何屬性，而且只要 XAML 相當於 C# [ `null` ](/dotnet/csharp/language-reference/keywords/null/)關鍵字。

`x:Null`很少會需要的標記延伸模組，且不常使用，但如果您發現需要它，您將必須存在高興。

**X:null 示範**頁面說明一個案例時`x:Null`可能會很方便。 假設您定義隱含`Style`如`Label`包含`Setter`設定`FontFamily`屬性設為平台相依的系列名稱：

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

然後您會發現其中一個`Label`項目，您想要在隱含的所有屬性設定`Style`除了`FontFamily`，以您都想要的預設值。 您可以定義另一個`Style`針對該用途，但更簡單的方法是只將`FontFamily`於特定的屬性`Label`至`x:Null`中心所示， `Label`。

以下是三個平台上執行的程式：

[![X:null 示範](consuming-images/nulldemo-small.png "X:null 示範")](consuming-images/nulldemo-large.png#lightbox "X:null 示範")

通知的四個`Label`元素具有新細明體字型，但在中心`Label`預設 sans serif 字型。

## <a name="define-your-own-markup-extensions"></a>定義您自己的標記延伸

如果您遇到無法使用 Xamarin.Forms 中的 XAML 標記延伸的需求，您可以[建立您自己](creating.md)。


## <a name="related-links"></a>相關連結

- [標記延伸 （範例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Xamarin.Forms 通訊錄中的 XAML 標記延伸的章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [動態樣式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
