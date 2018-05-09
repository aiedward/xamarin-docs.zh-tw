---
title: 資源字典
description: XAML 資源是可共用及重新用在整個 Xamarin.Forms 應用程式的物件。
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/02/2018
ms.openlocfilehash: ee3e4c984072fc019fe3719aab650a44d3899911
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="resource-dictionaries"></a>資源字典

_XAML 資源都可以共用及重複使用在整個 Xamarin.Forms 應用程式物件的定義。_

這些資源的物件會儲存在資源字典中。 本文說明如何建立和使用的資源字典，以及如何合併資源字典。

## <a name="overview"></a>總覽

A [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)是 Xamarin.Forms 應用程式所使用的資源的儲存機制。 儲存中的一般資源`ResourceDictionary`包含[樣式](~/xamarin-forms/user-interface/styles/index.md)，[控制項範本](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)，[資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)、 色彩和轉換器。

在 XAML 中，儲存在資源`ResourceDictionary`然後可以擷取與套用至項目使用`StaticResource`標記延伸。 在 C# 中，資源也可以定義在`ResourceDictionary`然後擷取並套用至項目使用字串為基礎的索引子。 不過，沒有使用的一些好處`ResourceDictionary`在 C# 中，共用的物件可以直接儲存為欄位或屬性，並不直接存取需要第一個從擷取的字典。

## <a name="creating-and-consuming-a-resourcedictionary"></a>建立和使用的資源字典

資源定義於[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)也就是將其設定為下列其中一種`Resources`屬性：

- [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/)任何衍生自的類別屬性 [`Application`](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)
- [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/)任何衍生自的類別屬性['VisualElement'](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)

Xamarin.Forms 程式包含只有一個類別衍生自`Application`但通常會使用許多類別衍生自`VisualElement`，包括頁面、 版面配置和控制項。 任何這些物件可以有其`Resources`屬性設定為`ResourceDictionary`。 選擇要放置在特定`ResourceDictionary`位置可以使用資源的影響：

- 中的資源`ResourceDictionary`，例如附加到檢視`Button`或`Label`只能套用至該特定物件，因此這不是非常有用。
- 中的資源`ResourceDictionary`例如附加至配置`StackLayout`或`Grid`可以套用至版面配置和版面配置的所有子系。 
- 中的資源`ResourceDictionary`定義在頁面層級可以套用至頁面和所有子系。
- 中的資源`ResourceDictionary`定義在應用程式層級可以套用在整個應用程式。

下列 XAML 顯示應用程式層級中定義的資源`ResourceDictionary`中**App.xaml**標準 Xamarin.Forms 程式過程中建立的檔案：

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

這`ResourceDictionary`定義三個[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)資源和[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)資源。 如需有關`App`類別，請參閱[應用程式類別](~/xamarin-forms/app-fundamentals/application-class.md)。

Xamarin.Forms 從 3.0 開始，明確`ResourceDictionary`標記不需要。 `ResourceDictionary`物件會自動建立，以及您可以直接之間插入資源`Resources`屬性項目標記：

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

每個資源都使用指定的索引鍵`x:Key`屬性，而成為它字典索引鍵中的`ResourceDictionary`。 索引鍵用來擷取資源中的`ResourceDictionary`由[ `StaticResource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticResourceExtension/)標記延伸，如內定義的其他資源會顯示下列 XAML 程式碼範例所示`StackLayout`:

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

第一個[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)擷取執行個體，並取用`LabelPageHeadingStyle`應用程式層級中定義的資源`ResourceDictionary`，第二個`Label`執行個體擷取和耗用`LabelNormalStyle`控制層級中定義的資源`ResourceDictionary`。 同樣地， [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)擷取執行個體，並取用`NormalTextColor`應用程式層級中定義的資源`ResourceDictionary`，而`MediumBoldText`控制層級中定義的資源`ResourceDictionary`。 這會導致下列螢幕擷取畫面所示的外觀：

[![](resource-dictionaries-images/screenshots-sml.png "耗用 ResourceDictionary 資源")](resource-dictionaries-images/screenshots.png#lightbox "耗用 ResourceDictionary 資源")

> [!NOTE]
> 應用程式層級的資源字典中，因此資源將會在應用程式啟動，而不是剖析，所需的頁面時，不應該併入專屬於單一頁面的資源。 如需詳細資訊，請參閱[減少應用程式資源字典的大小](~/xamarin-forms/deploy-test/performance.md)。

## <a name="overriding-resources"></a>覆寫資源

當`ResourceDictionary`資源共用`x:Key`屬性值，請檢視階層架構中較低位置定義的資源將會優先於定義更高版本上。 例如，設定`PageBackgroundColor`資源`Blue`在應用程式層級將會覆寫頁面層級`PageBackgroundColor`資源設為`Yellow`。 同樣地，頁面層級`PageBackgroundColor`控制層級將會覆寫資源`PageBackgroundColor`資源。 下列 XAML 程式碼範例示範此優先順序是：

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

[![](resource-dictionaries-images/overridding-screenshots-sml.png "覆寫 ResourceDictionary 資源")](resource-dictionaries-images/overridding-screenshots.png#lightbox "覆寫 ResourceDictionary 資源")

但請注意，背景列[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)仍黃色，因為[ `BarBackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarBackgroundColor/)屬性設定的值為`PageBackgroundColor`應用程式中定義的資源層級`ResourceDictionary`。

以下是另一種方式思考`ResourceDictionary`優先順序： 當 XAML 剖析器遇到`StaticResource`、 它所經過的視覺化樹狀結構向上搜尋相符的索引鍵，它使用第一個相符項目尋找。 如果這項搜尋結束在的頁面，並找到索引鍵仍未被 XAML 剖析器會搜尋`ResourceDictionary`附加至`App`物件。 如果仍然找不到索引鍵，則會引發例外狀況。

## <a name="stand-alone-resource-dictionaries"></a>獨立的資源字典

類別衍生自`ResourceDictionary`也可以在不同的獨立檔案。 (更明確地說，衍生自類別`ResourceDictionary`通常需要_組_檔案的資源會定義在 XAML 檔案的程式碼後置檔案，但因為`InitializeComponent`也是必要的呼叫。)產生的檔案就可以在應用程式之間共用。

若要建立這類檔案，加入新**內容檢視**或**內容頁面**項目加入專案 (而非**內容檢視**或**內容頁面**與只有 C# 檔案中）。 XAML 檔案和 C# 檔案中，變更 基底類別的名稱`ContentView`或`ContentPage`至`ResourceDictionary`。 在 XAML 檔案中，基底類別的名稱會是最上層元素。

下列 XAML 範例所示[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)名為`MyResourceDictionary`:

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

這`ResourceDictionary`包含單一資源，也就是類型的物件`DataTemplate`。

您可以具現化`MyResourceDictionary`放之間`Resources`屬性項目，例如，在標記`ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>  
```

執行個體`MyResourceDictionary`設`Resources`屬性`ContentPage`物件。

不過，這個方法有一些限制：`Resources`屬性`ContentPage`參考此一個`ResourceDictionary`。 在大部分情況下，您想要包括其他選項`ResourceDictionary`執行個體，可能是其他資源，以及。

這項工作需要合併的資源字典。

## <a name="merged-resource-dictionaries"></a>合併的資源字典

合併的資源字典結合一或多個`ResourceDictionary`到另一個執行個體`ResourceDictionary`。 您可以在 XAML 檔案中設定[ `MergedDictionaries` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedDictionaries/)屬性，以將合併到應用程式、 分頁或控制層級的一或多個資源字典`ResourceDictionary`。

> [!IMPORTANT]
> `ResourceDictionary` 也會定義[ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/)屬性。 請勿使用這個屬性。它已被取代為準，Xamarin.Forms 3.0。

與執行個體`MyResourceDictionary`可以合併到任何應用程式、 分頁或控制層級`ResourceDictionary`。 下列 XAML 程式碼範例顯示要合併到頁面層級`ResourceDictionary`使用`MergedDictionaries`屬性：

```xaml
<ContentPage ...>
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

該標記會顯示的執行個體`MyResourceDictionary`加入`ResourceDictionary`，但您也可以參考其他`ResourceDictionary`內`MergedDictionary`屬性項目標記，以及這些標記以外的其他資源：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary.MergedDictionaries>

                <!-- Add more resource dictionaries here -->

                <local:MyResourceDictionary />

                <!-- Add more resource dictionaries here -->

            </ResourceDictionary.MergedDictionaries>

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

可以有只有一個`MergedDictionaries`一節中`ResourceDictionary`，但您可以將許多`ResourceDictionary`您想要在該處執行個體。

當合併[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)資源都共用相同`x:Key`屬性值，Xamarin.Forms 使用的下列資源優先順序：

1. 在資源字典的本機資源。
1. 合併資源字典中所包含的資源已被取代透過[ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/)屬性。
1. 透過合併資源字典中所包含的資源`MergedDictionaries`集合，它們會列在順序`MergedDictionaries`屬性。

> [!NOTE]
> 搜尋資源字典可能是密集的工作，如果應用程式包含多個大型的資源字典。 因此，若要避免不必要的搜尋，您應該確定應用程式中的每個頁面只會使用適用於頁面資源字典。

## <a name="merging-dictionaries-in-xamarinforms-30"></a>合併的字典，在 Xamarin.Forms 3.0

Xamarin.Forms 3.0，合併的程序開始`ResourceDictionaries`變得稍微更容易且更有彈性。 `MergedDictionaries`屬性項目標記不再需要。 相反地，您將加入至資源字典另`ResourceDictionary`標記與新[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.Source/)屬性設定為使用的資源之 XAML 檔案的檔名：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary Source="MyResourceDictionary.xaml" />

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

因為 Xamarin.Forms 3.0 自動具現化`ResourceDictionary`，這兩者外部`ResourceDictionary`標記不再需要：

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

這種新語法未_不_具現化`MyResourceDictionary`類別。 相反地，它會參考之 XAML 檔案。 基於這個原因，程式碼後置檔案 (**MyResourceDictionary.xaml.cs**) 已不再需要。 您也可以移除`x:Class`屬性從根標記**MyResourceDictionary.xaml**檔案。 

## <a name="summary"></a>總結

本文說明如何建立和使用[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，以及如何合併資源字典。 A`ResourceDictionary`讓定義在單一位置，並在整個 Xamarin.Forms 應用程式重複使用的資源。

## <a name="related-links"></a>相關連結

- [資源字典 （範例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/resourcedictionaries/)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
