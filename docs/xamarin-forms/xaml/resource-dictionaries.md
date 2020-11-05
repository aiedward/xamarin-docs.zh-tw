---
title: Xamarin.Forms 資源字典
description: Xamarin.Forms XAML 資源是可在整個應用程式中共用和重複使用的物件 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: video
ms.openlocfilehash: 90068096eced1fd1ddd2eb59b845eb4d5e41286f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368878"
---
# <a name="no-locxamarinforms-resource-dictionaries"></a>Xamarin.Forms 資源字典

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)是應用程式所使用之資源的儲存機制 Xamarin.Forms 。 儲存在中的一般資源 `ResourceDictionary` 包括 [樣式](~/xamarin-forms/user-interface/styles/index.md)、 [控制項範本](~/xamarin-forms/app-fundamentals/templates/control-template.md)、 [資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)、色彩和轉換器。

在 XAML 中，您 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 可以使用 `StaticResource` 或標記延伸，參考儲存在中的資源，並將其套用至元素 `DynamicResource` 。 在 c # 中，您也可以在中定義資源，然後 `ResourceDictionary` 使用以字串為基礎的索引子來參考並套用至元素。 不過，在 c # 中使用 a 沒有任何好處 `ResourceDictionary` ，因為共用物件可以儲存為欄位或屬性，而且可以直接存取，而不需要先從字典中取出它們。

## <a name="create-resources-in-xaml"></a>以 XAML 建立資源

每個 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 衍生物件都有一個 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 屬性，也就是 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 可以包含資源的。 同樣地， [`Application`](xref:Xamarin.Forms.Application) 衍生的物件具有 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 可包含資源的屬性（property） [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。

Xamarin.Forms應用程式只包含衍生自的類別 [`Application`](xref:Xamarin.Forms.Application) ，但通常會使用衍生自的許多類別 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ，包括頁面、配置和控制項。 這些物件的任何一個都可以 `Resources` 將其屬性設定為 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 包含資源。 選擇要在何處放置 `ResourceDictionary` 可以使用資源的特定影響：

- 在中 `ResourceDictionary` ，附加至或等視圖的資源只能 [`Button`](xref:Xamarin.Forms.Button) 套用 [`Label`](xref:Xamarin.Forms.Label) 至該特定物件。
- `ResourceDictionary`附加至配置（例如或）的資源 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 可以套用至版面配置和該配置的 [`Grid`](xref:Xamarin.Forms.Grid) 所有子系。
- 在 `ResourceDictionary` 頁面層級定義的資源可以套用至頁面和其所有子系。
- 在 `ResourceDictionary` 應用層級定義的資源可以套用至整個應用程式。

除了隱含樣式之外，資源字典中的每個資源都必須有以屬性定義的唯一字串索引鍵 `x:Key` 。

下列 XAML 會顯示在應用程式的應用層級 `ResourceDictionary` 中 **App.xaml** 定義的資源：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ResourceDictionaryDemo.App">
    <Application.Resources>

        <Thickness x:Key="PageMargin">20</Thickness>

        <!-- Colors -->
        <Color x:Key="AppBackgroundColor">AliceBlue</Color>
        <Color x:Key="NavigationBarColor">#1976D2</Color>
        <Color x:Key="NavigationBarTextColor">White</Color>
        <Color x:Key="NormalTextColor">Black</Color>

        <!-- Implicit styles -->
        <Style TargetType="{x:Type NavigationPage}">
            <Setter Property="BarBackgroundColor"
                    Value="{StaticResource NavigationBarColor}" />
            <Setter Property="BarTextColor"
                    Value="{StaticResource NavigationBarTextColor}" />
        </Style>

        <Style TargetType="{x:Type ContentPage}"
               ApplyToDerivedTypes="True">
            <Setter Property="BackgroundColor"
                    Value="{StaticResource AppBackgroundColor}" />
        </Style>

    </Application.Resources>
</Application>
```

在此範例中，資源字典會定義 [`Thickness`](xref:Xamarin.Forms.Thickness) 資源、多個 [`Color`](xref:Xamarin.Forms.Color) 資源和兩個隱含 [`Style`](xref:Xamarin.Forms.Style) 資源。 如需類別的詳細資訊 `App` ，請參閱[ Xamarin.Forms 應用程式類別](~/xamarin-forms/app-fundamentals/application-class.md)。

> [!NOTE]
> 將所有資源放在明確標記之間也是有效的 `ResourceDictionary` 。 不過，由於 Xamarin.Forms 3.0，因此 `ResourceDictionary` 不需要標記。 相反地， `ResourceDictionary` 系統會自動建立物件，而您可以直接在 `Resources` 屬性元素標記之間插入資源。

## <a name="consume-resources-in-xaml"></a>使用 XAML 中的資源

每個資源都有一個使用屬性指定的索引鍵 `x:Key` ，它會成為中的字典索引鍵 `ResourceDictionary` 。 金鑰是用來參考 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 具有 [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) 或標記延伸之的資源 [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) 。

`StaticResource`標記延伸類似于 `DynamicResource` 標記延伸，這兩者都會使用字典索引鍵來參考資源字典中的值。 不過，當 `StaticResource` 標記延伸執行單一字典查閱時， `DynamicResource` 標記延伸會維護字典索引鍵的連結。 因此，如果已取代與索引鍵相關聯的字典專案，則會將變更套用至視覺效果元素。 這可讓您在應用程式中進行執行時間資源變更。 如需標記延伸的詳細資訊，請參閱 [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。

下列 XAML 範例顯示如何使用資源，並在中定義其他資源 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ResourceDictionaryDemo.HomePage"
             Title="Home Page">
    <StackLayout Margin="{StaticResource PageMargin}">
        <StackLayout.Resources>
            <!-- Implicit style -->
            <Style TargetType="Button">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
            </Style>
        </StackLayout.Resources>

        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries." />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked" />
    </StackLayout>
</ContentPage>
```

在此範例中， [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件會使用應用層級資源字典中所定義的隱含樣式。 [`StackLayout`](xref:Xamarin.Forms.StackLayout)物件會使用在 `PageMargin` 應用層級資源字典中定義的資源，而 [`Button`](xref:Xamarin.Forms.Button) 物件會使用資源字典中所定義的隱含樣式 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![使用 ResourceDictionary 資源](resource-dictionaries-images/consuming.png "使用 ResourceDictionary 資源")](resource-dictionaries-images/consuming-large.png#lightbox "使用 ResourceDictionary 資源")

> [!IMPORTANT]
> 單一頁面專屬的資源不應該包含在應用層級資源字典中，因為這類資源接著會在應用程式啟動時剖析，而不是在頁面需要時進行剖析。 如需詳細資訊，請參閱 [減少應用程式資源字典的大小](~/xamarin-forms/deploy-test/performance.md)。

## <a name="resource-lookup-behavior"></a>資源查閱行為

當使用 [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) 或標記延伸來參考資源時，會發生下列查閱進程 [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) ：

- 針對設定屬性的專案，會在資源字典中檢查所要求的金鑰（如果存在的話）。 如果找到要求的索引鍵，則會傳回其值，而且查閱進程會終止。
- 如果找不到相符專案，則查閱進程會向上搜尋視覺化樹狀結構，並檢查每個父元素的資源字典。 如果找到要求的索引鍵，則會傳回其值，而且查閱進程會終止。 否則，進程會繼續往上往上，直到到達根項目為止。
- 如果在根項目找不到相符專案，則會檢查應用層級資源字典。
- 如果仍然找不到相符項， `XamlParseException` 則會擲回。

因此，當 XAML 剖析器遇到 `StaticResource` 或 `DynamicResource` 標記延伸時，它會使用所找到的第一個相符範圍，透過視覺化樹狀結構來搜尋相符的索引鍵。 如果此搜尋結束于頁面，但仍找不到索引鍵，則 XAML 剖析器會搜尋 `ResourceDictionary` 附加至 `App` 物件的。 如果仍然找不到索引鍵，則會擲回例外狀況。

## <a name="override-resources"></a>覆寫資源

當資源分享金鑰時，視覺化樹狀結構中較低層級所定義的資源將會優先于定義較高的資源。 例如， `AppBackgroundColor` `AliceBlue` 在應用層級設定的資源將會由設定為的頁面層級 `AppBackgroundColor` 資源覆寫 `Teal` 。 同樣地， `AppBackgroundColor` 控制項層級資源將會覆寫頁面層級資源 `AppBackgroundColor` 。

## <a name="stand-alone-resource-dictionaries"></a>獨立資源字典

衍生自的類別 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 也可以位於個別的獨立檔案中。 然後，您可以在應用程式之間共用結果檔。

若要建立這類檔案，請將新的 **內容視圖** 或 **內容頁面** 專案加入至專案 (但不是只有 c # 檔案) 的 **內容****頁面** 。 刪除程式碼後端檔案，然後在 XAML 檔案中，將基類的名稱從或變更 [`ContentView`](xref:Xamarin.Forms.ContentView) [`ContentPage`](xref:Xamarin.Forms.ContentPage) 為 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 此外，請從檔案 `x:Class` 的根標記中移除該屬性。

下列 XAML 範例顯示 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 名為 **MyResourceDictionary 的 xaml** ：

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}"
                       TextColor="{StaticResource NormalTextColor}"
                       FontAttributes="Bold" />
                <Label Grid.Column="1"
                       Text="{Binding Age}"
                       TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2"
                       Text="{Binding Location}"
                       TextColor="{StaticResource NormalTextColor}"
                       HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

在此範例中， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 包含單一資源，也就是類型的物件 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 **MyResourceDictionary** 可以藉由將其合併至另一個資源字典來取用。

## <a name="merged-resource-dictionaries"></a>合併的資源字典

合併的資源字典會將一個或多個 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 物件結合到另一個物件 `ResourceDictionary` 。

### <a name="merge-local-resource-dictionaries"></a>合併本機資源字典

您 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 可以 `ResourceDictionary` 建立一個物件，將 `ResourceDictionary` 其 [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) 屬性設定為具有下列資源之 XAML 檔案的檔案名，藉以將本機檔案合併至另一個檔案：

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

這個語法不會具現化 `MyResourceDictionary` 類別。 相反地，它會參考 XAML 檔案。 基於這個理由，設定屬性時 [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) ，不需要程式碼後端檔案，而且 `x:Class` 可以從 **MyResourceDictionary** 的根標記中移除該屬性。

> [!IMPORTANT]
> [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)只能從 XAML 設定屬性。

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>合併其他元件的資源字典

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)也可以藉由將加入至的屬性，將它合併至另一個 `ResourceDictionary` [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) `ResourceDictionary` 。 這項技術可讓您合併資源字典，而不論其所在的元件為何。 合併外部元件的資源字典需要將 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 組建動作設為 **EmbeddedResource** 、具有程式碼後端檔案，以及在檔案的 `x:Class` 根標記中定義屬性。

> [!WARNING]
> [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)類別也會定義 [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) 屬性。 不過，這個屬性已被取代，不應該再使用。

下列程式碼範例顯示兩個資源字典新增至 [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) 頁面層級的集合 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ：

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

在此範例中，來自相同元件的資源字典，以及來自外部元件的資源字典，都會合並到頁面層級的資源字典中。 此外，您也可以在 `ResourceDictionary` [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) 屬性元素標記和這些標記以外的其他資源中加入其他物件。

> [!IMPORTANT]
> 中只能有一個 `MergedDictionaries` 屬性元素標記 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，但您可以 `ResourceDictionary` 視需要在其中放置任意數量的物件。

當合併 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 的資源分享相同 `x:Key` 的屬性值時，會 Xamarin.Forms 使用下列資源優先順序：

1. 資源字典的本機資源。
1. 資源字典中所包含的資源 `MergedDictionaries` ，會以相反的順序在屬性中列出它們 `MergedDictionaries` 。

> [!NOTE]
> 如果應用程式包含多個大型的資源字典，則搜尋資源字典可能是需要大量計算的工作。 因此，若要避免不必要的搜尋，您應該確定應用程式中的每個頁面都只使用適用于頁面的資源字典。

## <a name="related-links"></a>相關連結

- [資源字典 (範例) ](/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms 風格](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary) \(英文\)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]