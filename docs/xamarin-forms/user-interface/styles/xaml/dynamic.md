---
title: 在 Xamarin.Forms 中的動態樣式
description: 這篇文章說明如何在 Xamarin.Forms 應用程式可以回應樣式變更在執行階段動態使用動態資源。
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 574f4cb5541a12525fb0d160599d4d2f13653cf0
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926943"
---
# <a name="dynamic-styles-in-xamarinforms"></a>在 Xamarin.Forms 中的動態樣式

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)

_樣式，請勿回應屬性的變更和應用程式的持續期間保持不變。例如，指派樣式來視覺化項目，如果其中一個 Setter 執行個體已經過修改，移除或加入新的 set 存取子執行個體之後, 所做的變更不會套用至的視覺化項目。不過，應用程式可以使用動態資源回應在執行階段動態的樣式變更。_

`DynamicResource`標記延伸是類似`StaticResource`兩者都使用字典索引鍵，來擷取的值中的標記延伸[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 不過，雖然`StaticResource`執行單一字典查閱，`DynamicResource`維護字典索引鍵的連結。 因此，如果索引鍵相關聯的字典項目取代時，變更套用至的視覺化項目。 這可讓執行階段要在應用程式中進行的樣式變更。

下列程式碼範例示範*動態*XAML 頁面中的樣式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" Icon="xaml.png">
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

[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)執行個體會使用`DynamicResource`標記延伸參考[ `Style` ](xref:Xamarin.Forms.Style)名為`searchBarStyle`，它不會在 XAML 中定義。 不過，因為[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)的屬性`SearchBar`執行個體已設定使用`DynamicResource`，遺漏的字典索引鍵不會導致擲回例外狀況。

相反地，在程式碼後置檔案中，建構函式建立[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)具有索引鍵的項目`searchBarStyle`，如下列程式碼範例所示：

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

當`OnButtonClicked`事件處理常式執行時，`searchBarStyle`將會切換`blueSearchBarStyle`和`greenSearchBarStyle`。 這會導致下列的螢幕擷取畫面所示的外觀：

[![](dynamic-images/dynamic-style-blue.png "藍色動態樣式範例")](dynamic-images/dynamic-style-blue-large.png#lightbox "藍色動態樣式範例")
[![](dynamic-images/dynamic-style-green.png "綠色動態樣式範例")](dynamic-images/dynamic-style-green-large.png#lightbox "綠色動態樣式範例")

下列程式碼範例示範如何在 C# 中的對等頁面：

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

在 C# 中， [ `SearchBar` ](xref:Xamarin.Forms.SearchBar)執行個體會使用[ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*)方法，以參考`searchBarStyle`。 `OnButtonClicked`事件處理常式程式碼等同於 XAML 的範例，並執行時，`searchBarStyle`將會切換`blueSearchBarStyle`和`greenSearchBarStyle`。

## <a name="dynamic-style-inheritance"></a>動態樣式繼承

動態樣式衍生樣式無法使用來達成[ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn)屬性。 相反地， [ `Style` ](xref:Xamarin.Forms.Style)類別包含[ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey)屬性，可以設定為字典索引鍵的值可能會以動態方式變更。

下列程式碼範例示範*動態*XAML 頁面中的樣式繼承：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" Icon="xaml.png">
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

[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)執行個體會使用`StaticResource`標記延伸參考[ `Style` ](xref:Xamarin.Forms.Style)名為`tealSearchBarStyle`。 這`Style`會設定一些額外的屬性，並使用[ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey)屬性來參考`searchBarStyle`。 `DynamicResource`標記延伸模組不需要，因為`tealSearchBarStyle`不會變更，除了`Style`它衍生自。 因此，`tealSearchBarStyle`會維護連結`searchBarStyle`和更改基底的樣式變更時。

在程式碼後置檔案中，建構函式會建立[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)具有索引鍵的項目`searchBarStyle`、 每個先前的範例，示範動態樣式。 當`OnButtonClicked`事件處理常式執行時，`searchBarStyle`將會切換`blueSearchBarStyle`和`greenSearchBarStyle`。 這會導致下列的螢幕擷取畫面所示的外觀：

[![](dynamic-images/dynamic-style-inheritance-blue.png "藍色動態樣式繼承範例")](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox "藍色動態樣式繼承範例")
[![](dynamic-images/dynamic-style-inheritance-green.png "綠色動態樣式繼承範例")](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox "綠色動態樣式繼承範例")

下列程式碼範例示範如何在 C# 中的對等頁面：

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

`tealSearchBarStyle`直接指派給[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)屬性[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)執行個體。 這`Style`會設定一些其他的屬性，並使用[ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey)屬性來參考`searchBarStyle`。 [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*)方法並不需要這裡因為`tealSearchBarStyle`不會變更，除了`Style`它衍生自。 因此，`tealSearchBarStyle`會維護連結`searchBarStyle`和更改基底的樣式變更時。

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [動態樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [使用樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [樣式](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
