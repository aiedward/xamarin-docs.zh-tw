---
title: 動態樣式
description: 樣式請勿回應屬性的變更，並針對應用程式的持續時間維持不變。 例如，指派之後樣式來視覺化項目，如果其中一個 Setter 執行個體在修改、 移除或加入的新 Setter 執行個體，所做的變更將不會套用至視覺項目。 不過，應用程式可以藉由使用動態的資源回應在執行階段動態樣式變更。
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: e0cfcbaef70f58622a21315637279740f568ada8
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848066"
---
# <a name="dynamic-styles"></a>動態樣式

_樣式請勿回應屬性的變更，並針對應用程式的持續時間維持不變。例如，指派之後樣式來視覺化項目，如果其中一個 Setter 執行個體在修改、 移除或加入的新 Setter 執行個體，所做的變更將不會套用至視覺項目。不過，應用程式可以藉由使用動態的資源回應在執行階段動態樣式變更。_

`DynamicResource`標記延伸是類似於`StaticResource`標記延伸中的同時使用字典索引鍵擷取值，以從[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。 但是，當`StaticResource`執行單一字典查閱，`DynamicResource`維護字典索引鍵的連結。 因此，如果索引鍵相關聯的字典項目取代時，變更套用至視覺項目。 這可讓執行階段要在應用程式中進行的樣式變更。

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

[ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)執行個體會使用`DynamicResource`參考的標記延伸[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)名為`searchBarStyle`，不在 XAML 中定義。 不過，因為[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性`SearchBar`執行個體已設定使用`DynamicResource`，遺失的字典索引鍵並不會造成擲回例外狀況。

相反地，在程式碼後置檔案中，建構函式建立[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)具有索引鍵的項目`searchBarStyle`，如下列程式碼範例所示：

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

當`OnButtonClicked`執行事件處理常式時，`searchBarStyle`會之間切換`blueSearchBarStyle`和`greenSearchBarStyle`。 這會導致下列螢幕擷取畫面所示的外觀：

[![](dynamic-images/dynamic-style-blue.png "藍色動態樣式範例")](dynamic-images/dynamic-style-blue-large.png#lightbox "藍色動態樣式範例")
[![](dynamic-images/dynamic-style-green.png "綠色動態樣式範例")](dynamic-images/dynamic-style-green-large.png#lightbox "綠色動態樣式範例")

下列程式碼範例會示範 C# 中的對等頁：

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

在 C# 中， [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)執行個體會使用[ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/)方法來參考`searchBarStyle`。 `OnButtonClicked`事件處理常式程式碼是 XAML 範例中，並在執行時，相同`searchBarStyle`會之間切換`blueSearchBarStyle`和`greenSearchBarStyle`。

<a name="dynamic-style-inheritance">

## <a name="dynamic-style-inheritance"></a>動態樣式繼承

從動態樣式衍生樣式無法達到使用[ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/)屬性。 相反地， [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)類別包含[ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/)屬性，可以設定為字典索引鍵的值可能會動態變更。

下列程式碼範例示範*動態*XAML 頁面中設定樣式繼承：

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

[ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)執行個體會使用`StaticResource`參考的標記延伸[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)名為`tealSearchBarStyle`。 這`Style`設定一些額外的屬性，並使用[ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/)屬性來參考`searchBarStyle`。 `DynamicResource`標記延伸不需要，因為`tealSearchBarStyle`不會變更，除了`Style`從它衍生。 因此，`tealSearchBarStyle`會維護連結`searchBarStyle`和更改基底樣式變更時。

在程式碼後置檔案中，建構函式會建立[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)具有索引鍵的項目`searchBarStyle`、 每個之前的範例中示範動態的樣式。 當`OnButtonClicked`執行事件處理常式時，`searchBarStyle`會之間切換`blueSearchBarStyle`和`greenSearchBarStyle`。 這會導致下列螢幕擷取畫面所示的外觀：

[![](dynamic-images/dynamic-style-inheritance-blue.png "藍色動態樣式繼承範例")](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox "藍色動態樣式繼承範例")
[![](dynamic-images/dynamic-style-inheritance-green.png "綠色動態樣式繼承範例")](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox "綠色動態樣式繼承範例")

下列程式碼範例會示範 C# 中的對等頁：

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

`tealSearchBarStyle`直接指派給[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性[ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)執行個體。 這`Style`會設定一些額外的屬性，並使用[ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/)屬性來參考`searchBarStyle`。 [ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/)方法不需要以下因為`tealSearchBarStyle`不會變更，除了`Style`從它衍生。 因此，`tealSearchBarStyle`會維護連結`searchBarStyle`和更改基底樣式變更時。

## <a name="summary"></a>總結

樣式請勿回應屬性的變更，並針對應用程式的持續時間維持不變。 不過，應用程式可以藉由使用動態的資源回應在執行階段動態樣式變更。 此外，*動態*樣式可以衍生自具有[ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/)屬性。



## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [動態樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [使用樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [樣式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
