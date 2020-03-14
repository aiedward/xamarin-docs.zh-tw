---
title: Xamarin 中的動態樣式
description: 本文說明 Xamarin Forms 應用程式如何使用動態資源，在執行時間動態回應樣式變更。
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 9a26532d13b843b812da94739be071c7accac212
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305546"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Xamarin 中的動態樣式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_樣式不會回應屬性變更，而且在應用程式的持續時間內會保持不變。例如，將樣式指派給視覺效果專案之後，如果其中一個 Setter 實例已修改、已移除，或加入新的 Setter 實例，這些變更將不會套用至視覺元素。不過，應用程式可以使用動態資源，在執行時間動態回應樣式變更。_

`DynamicResource` 標記延伸類似于 `StaticResource` 標記延伸，這兩者都使用字典索引鍵從[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)提取值。 不過，當 `StaticResource` 執行單一字典查閱時，`DynamicResource` 會維護字典索引鍵的連結。 因此，如果已取代與索引鍵相關聯的字典專案，則會將變更套用至視覺元素。 這可讓您在應用程式中進行執行時間樣式變更。

下列程式碼範例示範 XAML 頁面中的*動態*樣式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle"
                   TargetType="SearchBar"
                   BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle"
                   TargetType="SearchBar">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Placeholder="These SearchBar controls"
                       Style="{DynamicResource searchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`SearchBar`](xref:Xamarin.Forms.SearchBar)實例使用 `DynamicResource` 標記延伸來參考名為 `searchBarStyle`的[`Style`](xref:Xamarin.Forms.Style) ，但 XAML 中未定義此名稱。 不過，因為 `SearchBar` 實例的[`Style`](xref:Xamarin.Forms.NavigableElement.Style)屬性是使用 `DynamicResource`來設定，所以遺漏的字典索引鍵不會導致擲回例外狀況。

相反地，在程式碼後置檔案中，此函式會建立具有索引鍵 `searchBarStyle`的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)專案，如下列程式碼範例所示：

```csharp
public partial class DynamicStylesPage : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPage ()
    {
        InitializeComponent ();
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
    }

    void OnButtonClicked (object sender, EventArgs e)
    {
        if (originalStyle) {
            Resources ["searchBarStyle"] = Resources ["greenSearchBarStyle"];
            originalStyle = false;
        } else {
            Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
            originalStyle = true;
        }
    }
}
```

執行 `OnButtonClicked` 事件處理常式時，`searchBarStyle` 會在 `blueSearchBarStyle` 和 `greenSearchBarStyle`之間切換。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![藍色動態樣式範例](dynamic-images/dynamic-style-blue.png)](dynamic-images/dynamic-style-blue-large.png#lightbox)
[![綠色動態樣式範例](dynamic-images/dynamic-style-green.png)](dynamic-images/dynamic-style-green-large.png#lightbox)

下列程式碼範例示範中C#的對等頁面：

```csharp
public class DynamicStylesPageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        ...
        var searchBar1 = new SearchBar { Placeholder = "These SearchBar controls" };
        searchBar1.SetDynamicResource (VisualElement.StyleProperty, "searchBarStyle");
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button    }
        };
    }
    ...
}
```

在C#中， [`SearchBar`](xref:Xamarin.Forms.SearchBar)實例會使用[`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*)方法來參考 `searchBarStyle`。 `OnButtonClicked` 事件處理常式程式碼與 XAML 範例相同，而且在執行時，`searchBarStyle` 會在 `blueSearchBarStyle` 和 `greenSearchBarStyle`之間切換。

## <a name="dynamic-style-inheritance"></a>動態樣式繼承

無法使用[`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn)屬性來衍生動態樣式的樣式。 相反地， [`Style`](xref:Xamarin.Forms.Style)類別包含[`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey)屬性，它可以設定為字典索引鍵，其值可能會動態變更。

下列程式碼範例示範 XAML 頁面中的*動態*樣式繼承：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle" TargetType="SearchBar" BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle" TargetType="SearchBar">
              ...
            </Style>
            <Style x:Key="tealSearchBarStyle" TargetType="SearchBar" BaseResourceKey="searchBarStyle">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Text="These SearchBar controls" Style="{StaticResource tealSearchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`SearchBar`](xref:Xamarin.Forms.SearchBar)實例會使用 `StaticResource` 標記延伸來參考名為 `tealSearchBarStyle`的[`Style`](xref:Xamarin.Forms.Style) 。 這個 `Style` 會設定一些額外的屬性，並使用[`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey)屬性來參考 `searchBarStyle`。 不需要 `DynamicResource` 標記延伸，因為 `tealSearchBarStyle` 不會變更，除了衍生自的 `Style` 以外。 因此，`tealSearchBarStyle` 會維持 `searchBarStyle` 的連結，並在基底樣式變更時改變。

在程式碼後置檔案中，此函式會根據先前示範動態樣式的範例，建立具有索引鍵 `searchBarStyle`的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)專案。 執行 `OnButtonClicked` 事件處理常式時，`searchBarStyle` 會在 `blueSearchBarStyle` 和 `greenSearchBarStyle`之間切換。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![藍色動態樣式繼承範例](dynamic-images/dynamic-style-inheritance-blue.png)](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox)
[![綠色動態樣式繼承範例](dynamic-images/dynamic-style-inheritance-green.png)](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox)

下列程式碼範例示範中C#的對等頁面：

```csharp
public class DynamicStylesInheritancePageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesInheritancePageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var tealSearchBarStyle = new Style (typeof(SearchBar)) {
            BaseResourceKey = "searchBarStyle",
            ...
        };
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = {
                new SearchBar { Text = "These SearchBar controls", Style = tealSearchBarStyle },
                ...
            }
        };
    }
    ...
}
```

`tealSearchBarStyle` 會直接指派給[`SearchBar`](xref:Xamarin.Forms.SearchBar)實例的[`Style`](xref:Xamarin.Forms.NavigableElement.Style)屬性。 這個 `Style` 會設定一些額外的屬性，並使用[`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey)屬性來參考 `searchBarStyle`。 這裡不需要[`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*)方法，因為 `tealSearchBarStyle` 不會變更，除了衍生自的 `Style` 以外。 因此，`tealSearchBarStyle` 會維持 `searchBarStyle` 的連結，並在基底樣式變更時改變。

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [動態樣式（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [使用樣式（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [庫](xref:Xamarin.Forms.Setter)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
