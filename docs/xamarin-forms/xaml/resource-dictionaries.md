---
title: Xamarin.表單資源字典
description: Xamarin.Forms XAML 資源是可在 Xamarin.Forms 應用程式中共用和重用的物件。
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
ms.custom: video
ms.openlocfilehash: 8dd3c7f36ddd436a812927816a1326dbb7c48341
ms.sourcegitcommit: ee9e48e2ec643915f42a6c1641077970ae20cb17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80523235"
---
# <a name="xamarinforms-resource-dictionaries"></a>Xamarin.表單資源字典

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

A[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)是 Xamarin.Forms 應用程式使用的資源的儲存庫。 儲存在`ResourceDictionary`中的典型資源包括[樣式](~/xamarin-forms/user-interface/styles/index.md)、[控制項樣本](~/xamarin-forms/app-fundamentals/templates/control-template.md)、[資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)、顏色和轉換器。

在 XAML[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中,`StaticResource``DynamicResource`可以使用或標記擴展來參考 儲存在中的資源並將其應用於元素。 在 C# 中,資源`ResourceDictionary`也可以在 中定義,然後透過使用基於字串的索引器引用並應用於元素。 但是,在 C#`ResourceDictionary`中使用 沒有什麼好處,因為共用物件可以儲存為欄位或屬性,並且無需首先從字典中檢索它們即可直接存取。

## <a name="create-resources-in-xaml"></a>在 XAML 建立資源

每個[`VisualElement`](xref:Xamarin.Forms.VisualElement)派生物件都有[`Resources`](xref:Xamarin.Forms.VisualElement.Resources)一 個屬性,[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)該屬性 可以包含資源。 同樣,[`Application`](xref:Xamarin.Forms.Application)派生物件具有一[`Resources`](xref:Xamarin.Forms.VisualElement.Resources)個 屬性,該[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)屬性可以包含資源。

Xamarin.Forms 應用程式僅包含派生[`Application`](xref:Xamarin.Forms.Application)自 的類,但通常使用派[`VisualElement`](xref:Xamarin.Forms.VisualElement)生自的許多類,包括頁面、佈局和控件。 這些物件中的任何一個都可以將其`Resources`屬性設置[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)為 包含資源。 選擇在何處放置特定`ResourceDictionary`影響,以便使用資源:

- 附加到檢視`ResourceDictionary`(如[`Button`](xref:Xamarin.Forms.Button)或[`Label`](xref:Xamarin.Forms.Label)只能應用於該特定物件的)中的資源。
- `ResourceDictionary`附加到佈局的資源(如[`StackLayout`](xref:Xamarin.Forms.StackLayout)或[`Grid`](xref:Xamarin.Forms.Grid))可以應用於佈局以及該佈局的所有子級。
- 在頁面級別`ResourceDictionary`定義的資源可以應用於頁面及其所有子級。
- 在應用程式級別`ResourceDictionary`定義的資源可以應用於整個應用程式。

除了隱式樣式外,資源字典中的每個資源都必須具有使用`x:Key`屬性 定義的唯一字串鍵。

以下 XAML 顯示`ResourceDictionary`了在**App.xaml**檔中的應用程式等級中定義的資源:

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

在此範例中,資源字典定義資源、多個[`Thickness`](xref:Xamarin.Forms.Thickness)[`Color`](xref:Xamarin.Forms.Color)資源和兩個隱式[`Style`](xref:Xamarin.Forms.Style)資源。 有關該類別的詳細資訊,`App`請參考[Xamarin.Forms 應用類別](~/xamarin-forms/app-fundamentals/application-class.md)。

> [!NOTE]
> 將所有資源放在顯式`ResourceDictionary`標記之間也有效。 但是,由於 Xamarin.窗體 3.0,`ResourceDictionary`因此不需要標記。 相反,對`ResourceDictionary`象 是自動建立的,您可以`Resources`直接在 屬性元素標記之間插入資源。

## <a name="consume-resources-in-xaml"></a>在 XAML 中使用資源

每個資源都有一個使用`x:Key`屬性指定的鍵,該屬性將成為`ResourceDictionary`中的字典密鑰。 該鍵用於引用[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)[`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)具有[`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)或標記擴展的資源。

`StaticResource`標記擴展與`DynamicResource`標記擴展類似,因為兩者都使用字典鍵引用資源字典中的值。 但是,`StaticResource`當標記擴展執行單個字典查找時`DynamicResource`, 標記擴展保留指向字典鍵的連結。 因此,如果替換與鍵關聯的字典條目,則更改將應用於可視元素。 這允許在應用程式中進行運行時資源更改。 有關標記延伸的詳細資訊,請參閱[XAML 標籤延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。

下面的 XAML 範例展示如何使用資源,並在中定義[`StackLayout`](xref:Xamarin.Forms.StackLayout)其他資源 :

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

在此範例中,[`ContentPage`](xref:Xamarin.Forms.ContentPage)物件使用應用程式級資源字典中定義的隱式樣式。 物件[`StackLayout`](xref:Xamarin.Forms.StackLayout)使用應用程式級`PageMargin`資源 字典中定義的[`Button`](xref:Xamarin.Forms.Button)資源, 而[`StackLayout`](xref:Xamarin.Forms.StackLayout)該物件使用 資源字典中定義的隱式樣式。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![消耗資源字典資源](resource-dictionaries-images/consuming.png "使用資源字典資源")](resource-dictionaries-images/consuming-large.png#lightbox "使用資源字典資源")

> [!IMPORTANT]
> 特定於單個頁面的資源不應包含在應用程式級資源字典中,因為這些資源將在應用程式啟動時進行分析,而不是在頁面需要時分析。 有關詳細資訊,請參閱[減小應用程式資源字典大小](~/xamarin-forms/deploy-test/performance.md)。

## <a name="resource-lookup-behavior"></a>資源查找行為

當使用[`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)[`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)或 標記延伸參考資源時,將發生以下查找過程:

- 在資源字典中檢查請求的鍵(如果存在)中設置該屬性的元素。 如果找到請求的密鑰,則返回其值並終止查找過程。
- 如果未找到匹配項,則查找過程向上搜索可視化樹,檢查每個父元素的資源字典。 如果找到請求的密鑰,則返回其值並終止查找過程。 否則,該過程將繼續向上,直到到達根元素。
- 如果在根元素上找不到匹配項,則檢查應用程式級資源字典。
- 如果未找到符合項,則引發`XamlParseException`。

因此,當 XAML 解析`StaticResource``DynamicResource`器遇到 或標記擴展時,它會使用它找到的第一個匹配項,通過可視化樹向上移動來搜索匹配鍵。 如果此搜索在頁面結束,並且尚未找到密鑰,則 XAML 解析器`ResourceDictionary`將搜索 附加`App`到物件。 如果未找到該鍵,則引發異常。

## <a name="override-resources"></a>覆寫資源

當資源分享鍵時,在可視化樹中定義較低的資源將優先於向上定義的資源。 `AppBackgroundColor`例如,將`AliceBlue`資源設置為應用程式級別的資源將被設置為`AppBackgroundColor``Teal`的頁面級別資源覆蓋。 同樣,控件級別資源`AppBackgroundColor`將覆蓋頁面`AppBackgroundColor`級別 資源。

## <a name="stand-alone-resource-dictionaries"></a>獨立資源字典

派生自的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)類也可以位於單獨的獨立檔中。 然後,可以在應用程式之間共享結果的檔。

要建立此類檔,請向專案添加新**的內容檢視**或**內容頁**項(但不包括僅包含 C# 檔**的內容檢視**或**內容頁**)。 刪除代碼背後的檔案,並在 XAML 檔中改變基類別的[`ContentView`](xref:Xamarin.Forms.ContentView)名稱[`ContentPage`](xref:Xamarin.Forms.ContentPage)從或[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)變更為 。 此外,從檔的根`x:Class`標記中刪除該屬性。

下面的 XAML 範例[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)顯示一個名為**MyResource 字典.xaml**的 :

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

這個資源,[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)包含單資源,該資源是類型的物件[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 **MyResource字典.xaml**可以通過將其合併到另一個資源字典中來使用。

## <a name="merged-resource-dictionaries"></a>合併的資源字典

合併的資源字典將一個或多個[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)物件合併到另一`ResourceDictionary`個 物件中。

### <a name="merge-local-resource-dictionaries"></a>合併本地端字典

以建立[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)資源的[`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)XAML`ResourceDictionary`檔案`ResourceDictionary`的檔案名稱的物件,可以將本地檔案合併到另一個檔案:

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

此語法不會實例化類別`MyResourceDictionary`。 相反,它引用 XAML 檔。 因此,在設置[`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)屬性時,不需要代碼背後的檔,並且可以`x:Class`從**MyResource 字典.xaml**檔的根標記中刪除該屬性。

> [!IMPORTANT]
> 只能[`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)從 XAML 設置該屬性。

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>合併其他程式集的資源字典

還可以[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)通過將 A`ResourceDictionary`添加[`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)`ResourceDictionary`到 的屬性 中,將其合併為另一個。 此技術允許合併資源字典,而不管它們駐留的程式集是什麼。 從外部程式集合並資源字典[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)需要將生成操作設置為**EmbeddedResource,** 具有代碼後面的檔,並在檔的根標`x:Class`記中定義 屬性。

> [!WARNING]
> 類別[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)還定義[`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith)屬性 。 但是,此屬性已被棄用,不應再使用。

以下代碼範例顯示兩個資源字典被新增到頁面等級的[`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)集合中:

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

在此範例中,來自同一程式集的資源字典和外部程式集的資源字典將合併到頁面級別資源字典中。 此外,您還可以在`ResourceDictionary`[`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)屬性元素標記中添加其他物件,以及這些標記之外的其他資源。

> [!IMPORTANT]
> 中只能有一個`MergedDictionaries`[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)屬性元素標記,但可以根據需要將盡可能`ResourceDictionary`多的 物件放入其中。

當合併[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)的資源分享相同`x:Key`的 屬性值時,Xamarin.Forms 使用以下資源優先順序:

1. 資源字典的本地資源。
1. 使用`MergedDictionaries`集合的資源字典包含的資源,其順序相反,它們列在屬性中`MergedDictionaries`。

> [!NOTE]
> 如果應用程式包含多個大型資源字典,則搜索資源字典可能是計算密集型任務。 因此,為了避免不必要的搜索,應確保應用程式中的每個頁面只使用適合該頁的資源字典。

## <a name="related-links"></a>相關連結

- [資源字典(範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms 樣式](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
