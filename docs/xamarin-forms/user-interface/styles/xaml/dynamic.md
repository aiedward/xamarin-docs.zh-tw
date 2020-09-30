---
title: 中的動態樣式 Xamarin.Forms
description: 本文說明 Xamarin.Forms 應用程式如何使用動態資源，在執行時間動態回應樣式變更。
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: video
ms.openlocfilehash: d6944da583679d58885c20d327b3e43e176eb6fa
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563350"
---
# <a name="dynamic-styles-in-no-locxamarinforms"></a>中的動態樣式 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_樣式不會回應屬性變更，而且會在應用程式的持續時間內保持不變。例如，在將樣式指派給視覺專案之後，如果其中一個 Setter 實例已修改、移除或加入新的 Setter 實例，變更將不會套用至視覺元素。不過，應用程式可以在執行時間使用動態資源動態地回應樣式變更。_

`DynamicResource`標記延伸類似于 `StaticResource` 標記延伸，這兩者都會使用字典索引鍵從提取值 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 不過，雖然會 `StaticResource` 執行單一字典查閱，但是會 `DynamicResource` 維護字典索引鍵的連結。 因此，如果已取代與索引鍵相關聯的字典專案，則會將變更套用至視覺效果元素。 這可讓您在應用程式中進行執行時間樣式變更。

下列程式碼範例示範 XAML 頁面中的 *動態* 樣式：

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

這些 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 實例會使用 `DynamicResource` 標記延伸來參考 [`Style`](xref:Xamarin.Forms.Style) `searchBarStyle` 未在 XAML 中定義的命名。 不過，因為 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 實例的屬性 `SearchBar` 是使用來設定的，所以遺漏的字典索引 `DynamicResource` 鍵不會導致擲回例外狀況。

相反地，在程式碼後端檔案中，此函式會 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 使用索引鍵來建立專案 `searchBarStyle` ，如下列程式碼範例所示：

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

當 `OnButtonClicked` 事件處理常式執行時， `searchBarStyle` 會在和之間切換 `blueSearchBarStyle` `greenSearchBarStyle` 。 這會導致下列螢幕擷取畫面中顯示的外觀：

[ ![ 藍色動態樣式範例](dynamic-images/dynamic-style-blue.png)](dynamic-images/dynamic-style-blue-large.png#lightbox) 
 [ ![ 綠色動態樣式範例](dynamic-images/dynamic-style-green.png)](dynamic-images/dynamic-style-green-large.png#lightbox)

下列程式碼範例示範 c # 中的對等頁面：

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

在 c # 中， [`SearchBar`](xref:Xamarin.Forms.SearchBar) 實例會使用 [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) 方法來參考 `searchBarStyle` 。 `OnButtonClicked`事件處理常式程式碼與 XAML 範例相同，而且在執行時， `searchBarStyle` 會在和之間切換 `blueSearchBarStyle` `greenSearchBarStyle` 。

## <a name="dynamic-style-inheritance"></a>動態樣式繼承

無法使用屬性來衍生動態樣式的樣式 [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) 。 相反地， [`Style`](xref:Xamarin.Forms.Style) 類別包含 [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) 屬性，此屬性可以設定為可能動態變更其值的字典索引鍵。

下列程式碼範例示範 XAML 頁面中的 *動態* 樣式繼承：

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

這些 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 實例會使用 `StaticResource` 標記延伸來參考 [`Style`](xref:Xamarin.Forms.Style) 名為的 `tealSearchBarStyle` 。 這會 `Style` 設定一些其他屬性，並使用 [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) 屬性來參考 `searchBarStyle` 。 `DynamicResource`標記延伸不是必要的 `tealSearchBarStyle` ，因為它是衍生自的，但不會變更 `Style` 。 因此， `tealSearchBarStyle` 會維護的連結， `searchBarStyle` 並在基底樣式變更時改變。

在程式碼後端檔案中，此函式 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) `searchBarStyle` 會根據先前示範的動態樣式範例，建立含有索引鍵的專案。 當 `OnButtonClicked` 事件處理常式執行時， `searchBarStyle` 會在和之間切換 `blueSearchBarStyle` `greenSearchBarStyle` 。 這會導致下列螢幕擷取畫面中顯示的外觀：

[ ![ 藍色動態樣式繼承範例](dynamic-images/dynamic-style-inheritance-blue.png)](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox) 
 [ ![ 綠色動態樣式繼承範例](dynamic-images/dynamic-style-inheritance-green.png)](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox)

下列程式碼範例示範 c # 中的對等頁面：

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

`tealSearchBarStyle`會直接指派給實例的 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 屬性 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 。 這會 `Style` 設定一些其他屬性，並使用 [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) 屬性來參考 `searchBarStyle` 。 [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*)此處不需要方法，因為 `tealSearchBarStyle` 它是衍生自的，但不會變更 `Style` 。 因此， `tealSearchBarStyle` 會維護的連結， `searchBarStyle` 並在基底樣式變更時改變。

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [動態樣式 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [使用樣式 (範例) ](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary) \(英文\)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]