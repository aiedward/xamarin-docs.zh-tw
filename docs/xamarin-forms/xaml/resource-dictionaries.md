---
title: "資源字典"
description: "XAML 資源都可以使用一次以上的物件定義。 ResourceDictionary 讓定義在單一位置，並在整個 Xamarin.Forms 應用程式重複使用的資源。 本文說明如何建立和使用 ResourceDictionary，以及如何合併資源字典。"
ms.topic: article
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/17/2017
ms.openlocfilehash: 0c2765551c16be605bc78d9ef32a91fd2c4ead8c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="resource-dictionaries"></a>資源字典

_XAML 資源都可以使用一次以上的物件定義。ResourceDictionary 讓定義在單一位置，並在整個 Xamarin.Forms 應用程式重複使用的資源。本文說明如何建立和使用 ResourceDictionary，以及如何合併資源字典。_

## <a name="overview"></a>總覽

A [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)是 Xamarin.Forms 應用程式所使用的資源的儲存機制。 儲存中的一般資源`ResourceDictionary`包含[樣式](~/xamarin-forms/user-interface/styles/index.md)，[控制項範本](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)，[資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)、 色彩和轉換器。

在 XAML 中，資源會定義在[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)然後擷取並套用至項目使用`StaticResource`標記延伸。 在 C# 中，資源會定義在`ResourceDictionary`然後擷取並套用至項目使用字串為基礎的索引子。 不過，沒有使用的一些好處`ResourceDictionary`在 C# 中，為資源可以輕鬆地直接指派給視覺元素的屬性而不需要先擷取從`ResourceDictionary`。

## <a name="creating-and-consuming-a-resourcedictionary"></a>建立和使用的資源字典

資源可以定義在[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)附加至[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/)集合的頁面或控制項，或以[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/)集合應用程式。 選擇要定義在何處[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)可以用它的影響：

- 中的資源[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定義於控制項層級只能套用至控制項和其子系。
- 中的資源[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定義在頁面層級只能套用至頁面和其子系。
- 中的資源[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定義在應用程式層級可以套用在整個應用程式。

下列 XAML 程式碼範例會顯示應用程式層級中定義的資源[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/):

```xaml
<Application ...>
    <Application.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Yellow</Color>
            <Color x:Key="HeadingTextColor">Black</Color>
            <Color x:Key="NormalTextColor">Blue</Color>
            <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
                <Setter Property="FontAttributes" Value="Bold" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

這[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定義三個[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)資源和[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)資源。 如需有關建立 XAML`App`類別，請參閱[應用程式類別](~/xamarin-forms/app-fundamentals/application-class.md)。

每個資源具有使用指定的索引鍵`x:Key`屬性，提供描述性的索引鍵中`ResourceDictionary`。 索引鍵用來擷取資源中的[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)由`StaticResource`標記延伸，如層級會顯示在控制項中所定義的其他資源的下列 XAML 程式碼範例所示`ResourceDictionary`:

```xaml
<StackLayout Margin="0,20,0,0">
  <StackLayout.Resources>
    <ResourceDictionary>
      <Style x:Key="LabelNormalStyle" TargetType="Label">
        <Setter Property="TextColor" Value="{StaticResource NormalTextColor}" />
      </Style>
      <Style x:Key="MediumBoldText" TargetType="Button">
        <Setter Property="FontSize" Value="Medium" />
        <Setter Property="FontAttributes" Value="Bold" />
      </Style>
    </ResourceDictionary>
  </StackLayout.Resources>
  <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
    <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
           Margin="10,20,10,0"
           Style="{StaticResource LabelNormalStyle}" />
    <Button Text="Navigate"
            Clicked="OnNavigateButtonClicked"
            TextColor="{StaticResource NormalTextColor}"
            Margin="0,20,0,0"
            HorizontalOptions="Center"
            Style="{StaticResource MediumBoldText}" />
</StackLayout>
```

第一個[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)擷取執行個體，並取用`LabelPageHeadingStyle`應用程式層級中定義的資源[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，第二個`Label`執行個體擷取和取用`LabelNormalStyle`控制層級中定義的資源`ResourceDictionary`。 同樣地， [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)擷取執行個體，並取用`NormalTextColor`應用程式層級中定義的資源`ResourceDictionary`，而`MediumBoldText`控制層級中定義的資源`ResourceDictionary`。 這會導致下列螢幕擷取畫面所示的外觀：

[![](resource-dictionaries-images/screenshots-sml.png "耗用 ResourceDictionary 資源")](resource-dictionaries-images/screenshots.png "耗用 ResourceDictionary 資源")

> [!NOTE]
> **請注意**： 專屬於單一頁面的資源不應該包含在應用程式層級的資源字典中，因此資源將然後時加以剖析而不是應用程式啟動時所需的頁面。 如需詳細資訊，請參閱[減少應用程式資源字典的大小](~/xamarin-forms/deploy-test/performance.md)。

## <a name="overriding-resources"></a>覆寫資源

當[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)資源共用`x:Key`屬性值，請檢視階層架構中較低位置定義的資源將會優先於定義更高版本上。 例如，設定`PageBackgroundColor`資源`Blue`在應用程式層級將會覆寫頁面層級`PageBackgroundColor`資源設為`Yellow`。 同樣地，頁面層級`PageBackgroundColor`控制層級將會覆寫資源`PageBackgroundColor`資源。 下列 XAML 程式碼範例示範此優先順序是：

```xaml
<ContentPage ... BackgroundColor="{StaticResource PageBackgroundColor}">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Blue</Color>
            <Color x:Key="NormalTextColor">Yellow</Color>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="0,20,0,0">
        ...
        <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
               Margin="10,20,10,0"
               Style="{StaticResource LabelNormalStyle}" />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked"
                TextColor="{StaticResource NormalTextColor}"
                Margin="0,20,0,0"
                HorizontalOptions="Center"
                Style="{StaticResource MediumBoldText}" />
    </StackLayout>
</ContentPage>
```

原始`PageBackgroundColor`和`NormalTextColor`定義的執行個體，應用程式層級，會覆寫`PageBackgroundColor`和`NormalTextColor`在頁面層級定義的執行個體。 因此，網頁的背景色彩會變成藍色，且頁面上的文字變成黃色，如下列螢幕擷取畫面所示：

[![](resource-dictionaries-images/overridding-screenshots-sml.png "覆寫 ResourceDictionary 資源")](resource-dictionaries-images/overridding-screenshots.png "覆寫 ResourceDictionary 資源")

但請注意，背景列[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)仍黃色，因為[ `BarBackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarBackgroundColor/)屬性設定的值為`PageBackgroundColor`應用程式中定義的資源層級[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。

## <a name="merged-resource-dictionaries"></a>合併的資源字典

合併的資源字典結合一或多個[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)到另一個執行個體。 這是藉由設定`ResourceDictionary.MergedDictionaries`屬性，以將合併到應用程式、 分頁或控制層級的一或多個資源字典`ResourceDictionary`。

> [!IMPORTANT]
> [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)型別也有[ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/)屬性可以用來合併單一`ResourceDictionary`到另一個，與`ResourceDictionary`指定為此值的`MergedWith`屬性要合併到目前`ResourceDictionary`執行個體。 透過合併時`MergedWith`屬性，在目前的任何資源`ResourceDictionary`共用`x:Key`屬性值中的資源`ResourceDictionary`合併，將會被取代。 不過，`MergedWith`屬性將會被取代，在 Xamarin.Forms 未來版本中。 因此，建議使用`MergedDictionaries`屬性合併`ResourceDictionary`執行個體。

下列 XAML 程式碼範例所示[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)名為`MyResourceDictionary`，其中包含單一資源：

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ResourceDictionaryDemo.MyResourceDictionary">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}" TextColor="{StaticResource NormalTextColor}" FontAttributes="Bold" />
                <Label Grid.Column="1" Text="{Binding Age}" TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2" Text="{Binding Location}" TextColor="{StaticResource NormalTextColor}" HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

`MyResourceDictionary` 可以合併到任何應用程式、 分頁或控制層級`ResourceDictionary`。 下列 XAML 程式碼範例顯示要合併到頁面層級`ResourceDictionary`使用`MergedDictionaries`屬性：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
                <!-- Add more resource dictionaries here -->
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

當合併[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)資源都共用相同`x:Key`屬性值，Xamarin.Forms 使用的下列資源優先順序：

1. 在資源字典的本機資源。
1. 已透過合併資源字典中所包含的資源[ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/)屬性。
1. 透過合併資源字典中所包含的資源`MergedDictionaries`集合，它們會列在順序`MergedDictionaries`屬性。

> [!NOTE]
> **請注意**： 搜尋資源字典可能是密集的工作，如果應用程式包含多個大型的資源字典。 因此，請確定應用程式中的每個頁面只會使用適用於頁面上，若要避免不必要的搜尋資源字典。

## <a name="summary"></a>總結

本文說明如何建立和使用[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，以及如何合併資源字典。 A`ResourceDictionary`讓定義在單一位置，並在整個 Xamarin.Forms 應用程式重複使用的資源。


## <a name="related-links"></a>相關連結

- [資源字典 （範例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/resourcedictionaries/)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
