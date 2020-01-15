---
title: 資源字典
description: XAML 資源是可以在整個 Xamarin 應用程式中共用和重複使用的物件。
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2019
ms.custom: video
ms.openlocfilehash: 49ea5c2a8332e625710af8a9947e1cb0e0338040
ms.sourcegitcommit: 211fed94fb96127a3e158ae1ff5d7eb831a203d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75955821"
---
# <a name="resource-dictionaries"></a>資源字典

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

_XAML 資源是物件的定義，可在整個 Xamarin 應用程式中共用和重複使用。這些資源物件會儲存在資源字典中。_

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)是 Xamarin. Forms 應用程式所使用之資源的存放庫。 儲存在 `ResourceDictionary` 中的一般資源包括[樣式](~/xamarin-forms/user-interface/styles/index.md)、[控制項範本](~/xamarin-forms/app-fundamentals/templates/control-template.md)、[資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)、色彩和轉換器。

在 XAML 中，儲存在 `ResourceDictionary` 中的資源可以使用 `StaticResource` 標記延伸來抓取和套用至元素。 在C#中，您也可以在 `ResourceDictionary` 中定義資源，然後使用以字串為基礎的索引子來抓取和套用至專案。 不過，在中C#使用 `ResourceDictionary` 並沒有任何好處，因為共用物件可以單純地儲存為欄位或屬性，並直接存取，而不需要先從字典中取出。

## <a name="create-and-consume-a-resourcedictionary"></a>建立和使用 ResourceDictionary

資源會在[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中定義，然後設定為下列其中一個 `Resources` 屬性：

- 衍生自之任何類別的[`Resources`](xref:Xamarin.Forms.Application.Resources)屬性[`Application`](xref:Xamarin.Forms.Application)
- 衍生自之任何類別的[`Resources`](xref:Xamarin.Forms.VisualElement.Resources)屬性[`VisualElement`](xref:Xamarin.Forms.Application)

Xamarin 程式僅包含一個衍生自 `Application` 的類別，但通常會使用衍生自 `VisualElement`的許多類別，包括頁面、版面配置和控制項。 這些物件中的任何一個都可以將其 `Resources` 屬性設為 `ResourceDictionary`。 選擇要在何處放置特定 `ResourceDictionary` 會影響可使用資源的位置：

- `ResourceDictionary` 中附加到 `Button` 或 `Label` 等視圖的資源，只能套用至該特定物件，因此這不是很好用。
- `ResourceDictionary` 中附加至配置（例如 `StackLayout` 或 `Grid`）的資源，可以套用至該配置的版面配置和所有子系。
- 在頁面層級定義之 `ResourceDictionary` 中的資源，可以套用至頁面和其所有子系。
- 在應用層級定義的 `ResourceDictionary` 中的資源，可以在整個應用程式中套用。

下列 XAML 會顯示在應用層級中定義的資源 `ResourceDictionary` 在建立為標準 Xamarin. Forms 程式一部分的**app.xaml**檔案中：

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

此 `ResourceDictionary` 會定義三個[`Color`](xref:Xamarin.Forms.Color)資源和一個[`Style`](xref:Xamarin.Forms.Style)資源。 如需 `App` 類別的詳細資訊，請參閱[應用程式類別](~/xamarin-forms/app-fundamentals/application-class.md)。

從 Xamarin. 表單3.0 開始，不需要明確的 `ResourceDictionary` 標記。 `ResourceDictionary` 物件會自動建立，而且您可以直接在 `Resources` 屬性元素標記之間插入資源：

```xaml
<Application ...>
    <Application.Resources>
        <Color x:Key="PageBackgroundColor">Yellow</Color>
        <Color x:Key="HeadingTextColor">Black</Color>
        <Color x:Key="NormalTextColor">Blue</Color>
        <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
            <Setter Property="FontAttributes" Value="Bold" />
            <Setter Property="HorizontalOptions" Value="Center" />
            <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
        </Style>
    </Application.Resources>
</Application>
```

每個資源都有一個使用 `x:Key` 屬性指定的索引鍵，它會成為 `ResourceDictionary`中的字典索引鍵。 金鑰是用來從 `ResourceDictionary` 的[`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)標記延伸抓取資源，如下列 XAML 程式碼範例所示，其中會顯示在 `StackLayout`內定義的其他資源：

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

第一個[`Label`](xref:Xamarin.Forms.Label)實例會抓取並取用在應用層級 `ResourceDictionary`中定義的 `LabelPageHeadingStyle` 資源，第二個 `Label` 實例會抓取並取用控制項層級 `LabelNormalStyle` 中定義的 `ResourceDictionary`資源。 同樣地， [`Button`](xref:Xamarin.Forms.Button)實例會抓取並取用在應用層級 `ResourceDictionary`中定義的 `NormalTextColor` 資源，以及在控制項層級 `ResourceDictionary`中定義的 `MediumBoldText` 資源。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![使用 ResourceDictionary 資源](resource-dictionaries-images/screenshots-sml.png)](resource-dictionaries-images/screenshots.png#lightbox)

> [!NOTE]
> 單一頁面特有的資源不應包含在應用層級資源字典中，因為這類資源會在應用程式啟動時進行剖析，而不是在頁面需要時進行剖析。 如需詳細資訊，請參閱[減少應用程式資源字典大小](~/xamarin-forms/deploy-test/performance.md)。

## <a name="override-resources"></a>覆寫資源

當 `ResourceDictionary` 資源分享 `x:Key` 屬性值時，在視圖階層架構中所定義的資源會優先于所定義的較高層級。 例如，將 `PageBackgroundColor` 資源設定為應用層級的 `Blue`，將會被設定為 `Yellow`的頁面層級 `PageBackgroundColor` 資源所覆寫。 同樣地，`PageBackgroundColor` 資源的控制項層級會覆寫 `PageBackgroundColor` 資源的頁面層級。 下列 XAML 程式碼範例會示範此優先順序：

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

在應用層級定義的原始 `PageBackgroundColor` 和 `NormalTextColor` 實例會由在頁面層級定義的 `PageBackgroundColor` 和 `NormalTextColor` 實例覆寫。 因此，頁面背景色彩會變成藍色，而頁面上的文字會變成黃色，如下列螢幕擷取畫面所示：

[![覆寫 ResourceDictionary 資源](resource-dictionaries-images/overridding-screenshots-sml.png)](resource-dictionaries-images/overridding-screenshots.png#lightbox)

不過，請注意， [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)的背景橫條仍然是黃色，因為[`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)屬性會設定為應用層級 `ResourceDictionary`中所定義 `PageBackgroundColor` 資源的值。

以下是另一種考慮 `ResourceDictionary` 優先順序的方法：當 XAML 剖析器遇到 `StaticResource`時，它會使用所找到的第一個相符項，透過視覺化樹狀結構來搜尋相符的索引鍵。 如果此搜尋結束于頁面，但仍找不到索引鍵，則 XAML 剖析器會搜尋附加至 `App` 物件的 `ResourceDictionary`。 如果仍然找不到索引鍵，則會引發例外狀況。

## <a name="stand-alone-resource-dictionaries"></a>獨立資源字典

衍生自 `ResourceDictionary` 的類別也可以位於個別的獨立檔案中。 （更精確地說，衍生自 `ResourceDictionary` 的類別通常_需要一組_檔案，因為資源是在 XAML 檔案中定義，但也需要具有 `InitializeComponent` 呼叫的程式碼後置檔案）。然後，就可以在應用程式之間共用結果檔案。

若要建立這類檔案，請將新的 [**內容**] 或 [**內容] 頁面**專案新增至專案（而不是只C#包含檔案的**內容視圖**或**內容頁面**）。 在 XAML 檔案和C#檔案中，將基類的名稱從 `ContentView` 或 `ContentPage` 變更為 `ResourceDictionary`。 在 XAML 檔案中，基類的名稱是最上層的元素。

下列 XAML 範例顯示名為 `MyResourceDictionary`的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ：

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

此 `ResourceDictionary` 包含單一資源，也就是 `DataTemplate`類型的物件。

您可以將 `MyResourceDictionary` 放在一對 `Resources` 屬性專案標記之間，例如，在 `ContentPage`中：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

`MyResourceDictionary` 的實例會設定為 `ContentPage` 物件的 `Resources` 屬性。

不過，這種方法有一些限制： `ContentPage` 的 `Resources` 屬性只會參考這個 `ResourceDictionary`。 在大部分的情況下，您會想要選擇包含其他 `ResourceDictionary` 實例，也可能是其他資源。

這項工作需要合併的資源字典。

## <a name="merged-resource-dictionaries"></a>合併的資源字典

合併的資源字典會將一或多個[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)物件結合成另一個 `ResourceDictionary`。

### <a name="merge-local-resource-dictionaries"></a>合併本機資源字典

本機[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)可以合併到另一個 `ResourceDictionary`，方法是將[`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)屬性設定為 XAML 檔案的檔案名，其中包含下列資源：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <!-- Add more resources here -->
        <ResourceDictionary Source="MyResourceDictionary.xaml" />
        <!-- Add more resources here -->
    </ContentPage.Resources>
    ...
</ContentPage>
```

這個語法不會具現化 `MyResourceDictionary` 類別。 相反地，它會參考 XAML 檔案。 基於這個理由，設定[`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)屬性時，不需要程式碼後置檔案（**MyResourceDictionary.xaml.cs**），而且 `x:Class` 屬性可以從**MyResourceDictionary**的根標記中移除。 此外，使用這種方法來合併資源字典時，Xamarin 會自動具現化[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，因此不需要外部 `ResourceDictionary` 標記。

> [!IMPORTANT]
> 只能從 XAML 設定[`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)屬性。

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>合併其他元件中的資源字典

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)也可以藉由將它加入至 `ResourceDictionary`的[`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)屬性，而合併至另一個 `ResourceDictionary`。 這項技術可讓資源字典合併，而不論其所在的元件為何。

> [!IMPORTANT]
> [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)類別也會定義[`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith)屬性。 不過，此屬性已被取代，且不應再使用。

下列程式碼範例會示範如何將 `MyResourceDictionary` 新增至頁面層級的[`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)集合[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:ResourceDictionaryDemo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

這個範例會顯示 `MyResourceDictionary`的實例（位於相同的元件中），並新增至[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)。 此外，您也可以從其他元件、 [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)屬性專案標記內的其他 `ResourceDictionary` 物件，以及這些標記以外的其他資源加入資源字典：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:ResourceDictionaryDemo"
             xmlns:theme="clr-namespace:MyThemes;assembly=MyThemes">
    <ContentPage.Resources>
        <ResourceDictionary>
            <!-- Add more resources here -->
            <ResourceDictionary.MergedDictionaries>
                <!-- Add more resource dictionaries here -->
                <local:MyResourceDictionary />
                <theme:LightTheme />
                <!-- Add more resource dictionaries here -->
            </ResourceDictionary.MergedDictionaries>
            <!-- Add more resources here -->
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

> [!IMPORTANT]
> [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中只能有一個 `MergedDictionaries` 的屬性元素標記，但您可以視需要在該處放入許多 `ResourceDictionary` 物件。

當合併[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)資源分享相同的 `x:Key` 屬性值時，Xamarin. Forms 會使用下列資源優先順序：

1. 資源字典的本機資源。
1. 資源字典中所包含的資源，已透過 `MergedDictionaries` 集合合併，並以相反順序列在 `MergedDictionaries` 屬性中。

> [!NOTE]
> 如果應用程式包含多個大型資源字典，搜尋資源字典可能是需要大量計算的工作。 因此，若要避免不必要的搜尋，您應該確定應用程式中的每個頁面只會使用適用于該頁面的資源字典。

## <a name="related-links"></a>相關連結

- [資源字典（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
