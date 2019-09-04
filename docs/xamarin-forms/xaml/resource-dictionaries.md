---
title: 資源字典
description: XAML 資源是可共用及重複使用在 Xamarin.Forms 應用程式的物件。
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2019
ms.custom: video
ms.openlocfilehash: 7c0fffbe626a740c15d85b1277c5158a5e564a15
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228081"
---
# <a name="resource-dictionaries"></a>資源字典

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

_XAML 資源是物件的定義, 可在整個 Xamarin 應用程式中共用和重複使用。這些資源物件會儲存在資源字典中。_

A [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)是資源所使用的 Xamarin.Forms 應用程式的儲存機制。 一般資源儲存在`ResourceDictionary`包括[樣式](~/xamarin-forms/user-interface/styles/index.md)，[控制項範本](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)，[資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)、 色彩和轉換器。

在 XAML 中，會儲存在的資源`ResourceDictionary`然後可以擷取並套用至項目使用`StaticResource`標記延伸。 在 C# 中，資源也可以定義在`ResourceDictionary`然後擷取並套用至項目使用字串為基礎的索引子。 不過，還有使用的一些好處`ResourceDictionary`在 C# 中，因為共用的物件可以直接儲存為欄位或屬性，並不直接存取需要第一個擷取這些字典中。

## <a name="create-and-consume-a-resourcedictionary"></a>建立和使用 ResourceDictionary

資源定義於[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)亦即則設定為下列其中一種`Resources`屬性：

- [ `Resources` ](xref:Xamarin.Forms.Application.Resources)任何衍生自的類別屬性 [`Application`](xref:Xamarin.Forms.Application)
- [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources)任何衍生自的類別屬性 [`VisualElement`](xref:Xamarin.Forms.Application)

Xamarin.Forms 程式包含一個類別衍生自`Application`通常會使用衍生自的許多類別，但`VisualElement`，包括頁面、 版面配置和控制項。 任何這些物件可以有其`Resources`屬性設定為`ResourceDictionary`。 選擇要放置特定`ResourceDictionary`可以在其中使用的資源的影響：

- 中的資源`ResourceDictionary`，例如附加到檢視`Button`或`Label`只能套用至該特定的物件，因此這不是很有用。
- 中的資源`ResourceDictionary`這類附加到版面配置`StackLayout`或`Grid`可以套用至版面配置和版面配置的所有子系。
- 中的資源`ResourceDictionary`定義在頁面層級可以套用至頁面和其所有子系。
- 中的資源`ResourceDictionary`定義在應用程式層級可以套用整個應用程式。

下列 XAML 會顯示在應用程式層級中定義的資源`ResourceDictionary`中**App.xaml**建立標準的 Xamarin.Forms 方案的檔案：

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

這`ResourceDictionary`會定義三個[ `Color` ](xref:Xamarin.Forms.Color)資源並[ `Style` ](xref:Xamarin.Forms.Style)資源。 如需詳細資訊`App`類別，請參閱[應用程式類別](~/xamarin-forms/app-fundamentals/application-class.md)。

開始在 Xamarin.Forms 3.0 中，明確`ResourceDictionary`標記並不需要。 `ResourceDictionary`物件會自動建立，而且您可以直接之間插入資源`Resources`屬性項目標記：

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

每個資源都使用指定的索引鍵`x:Key`屬性，它會變成為字典索引鍵中的`ResourceDictionary`。 金鑰可用來擷取資源，以從`ResourceDictionary`所[ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension)標記延伸，如下列 XAML 程式碼範例顯示定義內的其他資源所示`StackLayout`:

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

第一個[ `Label` ](xref:Xamarin.Forms.Label)執行個體擷取並取用`LabelPageHeadingStyle`中的應用程式層級定義的資源`ResourceDictionary`，第二個`Label`執行個體擷取及使用`LabelNormalStyle`控制層級中定義的資源`ResourceDictionary`。 同樣地， [ `Button` ](xref:Xamarin.Forms.Button)擷取執行個體，並取用`NormalTextColor`中的應用程式層級定義的資源`ResourceDictionary`，而`MediumBoldText`控制層級中定義的資源`ResourceDictionary`。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![使用 ResourceDictionary 資源](resource-dictionaries-images/screenshots-sml.png)](resource-dictionaries-images/screenshots.png#lightbox)

> [!NOTE]
> 在應用程式層級資源字典中，這類資源將然後在應用程式啟動，而不是剖析，需要一個頁面時，不應包含專屬於單一頁面的資源。 如需詳細資訊，請參閱 <<c0> [ 減少應用程式資源字典大小](~/xamarin-forms/deploy-test/performance.md)。

## <a name="override-resources"></a>覆寫資源

當`ResourceDictionary`資源共用`x:Key`屬性值，定義檢視階層中的較低的資源將會優先於設定更高版本定義。 例如，設定`PageBackgroundColor`資源`Blue`在應用程式層級會覆寫頁面層級`PageBackgroundColor`資源集`Yellow`。 同樣地，頁面層級`PageBackgroundColor`控制層級會覆寫資源`PageBackgroundColor`資源。 下列 XAML 程式碼範例示範此優先順序是：

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

原始`PageBackgroundColor`並`NormalTextColor`執行個體，在應用程式層級中，定義會覆寫`PageBackgroundColor`和`NormalTextColor`頁面層級定義的執行個體。 因此，網頁的背景色彩會變成藍色，並如下列螢幕擷取畫面所示的頁面上的文字會變成黃色:

[![覆寫 ResourceDictionary 資源](resource-dictionaries-images/overridding-screenshots-sml.png)](resource-dictionaries-images/overridding-screenshots.png#lightbox)

但請注意，背景列[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)仍然是黃色，因為[ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)屬性設定的值為`PageBackgroundColor`應用程式中定義的資源層級`ResourceDictionary`。

以下是另一種考慮`ResourceDictionary`優先順序的方法:當 XAML 剖析器遇到`StaticResource`時, 它會使用所找到的第一個相符項, 透過視覺化樹狀結構來搜尋相符的索引鍵。 如果此搜尋網頁上就會結束，且金鑰仍尚未發現，XAML 剖析器會搜尋`ResourceDictionary`附加至`App`物件。 如果仍然找不到索引鍵，則會引發例外狀況。

## <a name="stand-alone-resource-dictionaries"></a>獨立資源字典

類別衍生自`ResourceDictionary`也可以在不同的獨立檔案。 (更精確地說，衍生自類別`ResourceDictionary`通常需要_配對_檔案的資源會定義在 XAML 檔案，但使用的程式碼後置檔案，因為`InitializeComponent`也是必要的呼叫。)產生的檔案就可以在應用程式之間共用。

若要建立這類檔案，加入新**內容檢視**或**內容頁**項目加入專案 (而非**內容檢視**或**內容頁面**與只有 C# 檔案）。 XAML 檔案和 C# 檔案中，變更 基底類別的名稱`ContentView`或是`ContentPage`至`ResourceDictionary`。 在 XAML 檔案中，基底類別的名稱會是最上層元素。

下列 XAML 範例所示[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)名為`MyResourceDictionary`:

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

這`ResourceDictionary`包含單一資源，也就是型別的物件`DataTemplate`。

您可以具現化`MyResourceDictionary`將它放一對之間`Resources`property 項目，例如，在標記`ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

執行個體`MyResourceDictionary`設定為`Resources`屬性`ContentPage`物件。

不過, 這種方法有一些限制:的屬性只會參考此項`ResourceDictionary`。 `ContentPage` `Resources` 在大部分情況下，您想要包括其他選項`ResourceDictionary`執行個體，可能是其他資源，以及。

這項工作需要合併的資源字典。

## <a name="merged-resource-dictionaries"></a>合併的資源字典

合併的資源字典會將一[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)或多個`ResourceDictionary`物件結合成另一個。

### <a name="merge-local-resource-dictionaries"></a>合併本機資源字典

將[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) [屬性`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)設定為 XAML 檔案的`ResourceDictionary`檔案名 (包含下列資源), 即可將本機合併至另一個:

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

這個語法不會具現`MyResourceDictionary`化類別。 相反地，它會參考 XAML 檔案。 基於這個[`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)理由, 設定屬性時, 不需要程式碼後置檔案 (**MyResourceDictionary.xaml.cs**), 而且可以從`x:Class` **MyResourceDictionary**的根標記中移除該屬性。 此外, 使用這種方法來合併資源字典時, Xamarin 會自動具現化[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), 因此不需要`ResourceDictionary`外部標記。

> [!IMPORTANT]
> [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)屬性只能從 XAML 設定。

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>合併其他元件中的資源字典

也[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)可以藉`ResourceDictionary` 由將`ResourceDictionary`它加入至的[屬性,以合併至另一個。`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) 這項技術可讓資源字典合併, 而不論其所在的元件為何。

> [!IMPORTANT]
> 類別也會[`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith)定義屬性。 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 不過, 此屬性已被取代, 且不應再使用。

下列程式碼範例`MyResourceDictionary`會示範如何新增至頁面層級[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)的[`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)集合:

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

這個範例會顯示實例`MyResourceDictionary`, 其位於相同的元件中, 並加入[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)至。 此外, 您也可以從其他元件、 `ResourceDictionary` [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)屬性專案標記內的其他物件, 以及這些標記以外的其他資源加入資源字典:

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
> 中只能有一個`MergedDictionaries`屬性專案標記`ResourceDictionary` [,但您可以視需要將多個物件`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)放在該處。

當合併[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)資源共用相同`x:Key`屬性值，Xamarin.Forms 會使用下列資源優先順序：

1. 資源字典的本機資源中。
1. 透過合併的資源字典中包含的資源`MergedDictionaries`中所述的反向順序的集合，`MergedDictionaries`屬性。

> [!NOTE]
> 搜尋資源字典可能是密集運算的工作，如果應用程式包含多個大型的資源字典。 因此，若要避免不必要的搜尋，您應該確定應用程式中的每個頁面只會使用適用於頁面資源字典。

## <a name="related-links"></a>相關連結

- [資源字典 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
