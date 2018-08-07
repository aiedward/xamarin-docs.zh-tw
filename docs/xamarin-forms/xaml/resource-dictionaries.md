---
title: 資源字典
description: XAML 資源是可共用及重複使用在 Xamarin.Forms 應用程式的物件。
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: d305de651f6e770d02ca35f5f8f8ffcf08424e28
ms.sourcegitcommit: bf51592be39b2ae3d63d029be1d7745ee63b0ce1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2018
ms.locfileid: "39573642"
---
# <a name="resource-dictionaries"></a>資源字典

_XAML 資源是可共用及重複使用在 Xamarin.Forms 應用程式的物件定義。_

這些資源的物件會儲存在資源字典。 本文說明如何建立和使用資源字典，以及如何合併資源字典。

## <a name="overview"></a>總覽

A [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)是資源所使用的 Xamarin.Forms 應用程式的儲存機制。 一般資源儲存在`ResourceDictionary`包括[樣式](~/xamarin-forms/user-interface/styles/index.md)，[控制項範本](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)，[資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)、 色彩和轉換器。

在 XAML 中，會儲存在的資源`ResourceDictionary`然後可以擷取並套用至項目使用`StaticResource`標記延伸。 在 C# 中，資源也可以定義在`ResourceDictionary`然後擷取並套用至項目使用字串為基礎的索引子。 不過，還有使用的一些好處`ResourceDictionary`在 C# 中，因為共用的物件可以直接儲存為欄位或屬性，並不直接存取需要第一個擷取這些字典中。

## <a name="creating-and-consuming-a-resourcedictionary"></a>建立及使用 ResourceDictionary

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

第一個[ `Label` ](xref:Xamarin.Forms.Label)執行個體擷取並取用`LabelPageHeadingStyle`中的應用程式層級定義的資源`ResourceDictionary`，第二個`Label`執行個體擷取及使用`LabelNormalStyle`控制層級中定義的資源`ResourceDictionary`。 同樣地， [ `Button` ](xref:Xamarin.Forms.Button)擷取執行個體，並取用`NormalTextColor`中的應用程式層級定義的資源`ResourceDictionary`，而`MediumBoldText`控制層級中定義的資源`ResourceDictionary`。 這會導致下列的螢幕擷取畫面所示的外觀：

[![](resource-dictionaries-images/screenshots-sml.png "使用 ResourceDictionary 資源")](resource-dictionaries-images/screenshots.png#lightbox "耗用 ResourceDictionary 資源")

> [!NOTE]
> 在應用程式層級資源字典中，這類資源將然後在應用程式啟動，而不是剖析，需要一個頁面時，不應包含專屬於單一頁面的資源。 如需詳細資訊，請參閱 <<c0> [ 減少應用程式資源字典大小](~/xamarin-forms/deploy-test/performance.md)。

## <a name="overriding-resources"></a>覆寫資源

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

[![](resource-dictionaries-images/overridding-screenshots-sml.png "覆寫資源 ResourceDictionary")](resource-dictionaries-images/overridding-screenshots.png#lightbox "覆寫 ResourceDictionary 資源")

但請注意，背景列[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)仍然是黃色，因為[ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)屬性設定的值為`PageBackgroundColor`應用程式中定義的資源層級`ResourceDictionary`。

以下是另一種方式思考`ResourceDictionary`優先順序： 當 XAML 剖析器遇到`StaticResource`走過註冊視覺化樹狀結構透過搜尋相符的索引鍵，使用第一個相符項目找到。 如果此搜尋網頁上就會結束，且金鑰仍尚未發現，XAML 剖析器會搜尋`ResourceDictionary`附加至`App`物件。 如果仍然找不到索引鍵，則會引發例外狀況。

## <a name="stand-alone-resource-dictionaries"></a>獨立的資源字典

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

不過，此方法有一些限制：`Resources`的屬性`ContentPage`參考僅此一個`ResourceDictionary`。 在大部分情況下，您想要包括其他選項`ResourceDictionary`執行個體，可能是其他資源，以及。

這項工作需要合併的資源字典。

## <a name="merged-resource-dictionaries"></a>合併的資源字典

合併的資源字典合併一或多個`ResourceDictionary`到另一個執行個體`ResourceDictionary`。 您可以在 XAML 檔案中設定[ `MergedDictionaries` ](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)屬性，以將會合併至應用程式、 網頁或控制層級的一或多個資源字典`ResourceDictionary`。

> [!IMPORTANT]
> `ResourceDictionary` 也會定義[ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith)屬性。 請勿使用這個屬性影響;它已被取代，截至 Xamarin.Forms 3.0。

執行個體`MyResourceDictionary`可以合併到任何應用程式、 網頁或控制層級`ResourceDictionary`。 下列 XAML 程式碼範例顯示要合併到 頁面層級`ResourceDictionary`使用`MergedDictionaries`屬性：

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

該標記會顯示的執行個體`MyResourceDictionary`新增至`ResourceDictionary`但您也可以參考其他`ResourceDictionary`中執行個體`MergedDictionary`屬性項目標記，以及這些標記以外的其他資源：

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

可以是其中之一`MergedDictionaries`一節中`ResourceDictionary`，但您可以將多個`ResourceDictionary`您想要在該處執行個體。

當合併[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)資源共用相同`x:Key`屬性值，Xamarin.Forms 會使用下列資源優先順序：

1. 資源字典的本機資源中。
1. 合併的資源字典中包含的資源透過已被取代[ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith)屬性。
1. 透過合併的資源字典中包含的資源`MergedDictionaries`中所述的反向順序的集合，`MergedDictionaries`屬性。

> [!NOTE]
> 搜尋資源字典可能是密集運算的工作，如果應用程式包含多個大型的資源字典。 因此，若要避免不必要的搜尋，您應該確定應用程式中的每個頁面只會使用適用於頁面資源字典。

## <a name="merging-dictionaries-in-xamarinforms-30"></a>Xamarin.Forms 3.0 中的合併字典

從第 3.0 Xamarin.Forms 的合併程序開始[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)執行個體已成為稍微更容易且更有彈性。 `MergedDictionaries`屬性項目標記不再需要。 相反地，您將新增至資源字典另`ResourceDictionary`具有新標記[ `Source` ](xref:Xamarin.Forms.ResourceDictionary.Source)屬性設定為與資源的 XAML 檔案的檔名：

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

因為 Xamarin.Forms 3.0 自動具現化`ResourceDictionary`，這兩個外部`ResourceDictionary`標記已不再需要：

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

這種新語法並未_未_具現化`MyResourceDictionary`類別。 相反地，它會參考 XAML 檔案。 基於這個原因，程式碼後置檔案 (**MyResourceDictionary.xaml.cs**) 已不再需要。 您也可以移除`x:Class`屬性的根標記**MyResourceDictionary.xaml**檔案。

## <a name="summary"></a>總結

這篇文章說明如何建立及取用[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，以及如何合併資源字典。 A`ResourceDictionary`允許在單一位置中，定義及重複使用在 Xamarin.Forms 應用程式的資源。

## <a name="related-links"></a>相關連結

- [資源字典 （範例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/resourcedictionaries/)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
