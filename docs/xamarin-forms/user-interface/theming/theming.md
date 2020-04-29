---
title: 為 Xamarin. Forms 應用程式建立主題
description: 主題可以在 Xamarin 中實作為應用程式，方法是為每個主題建立 ResourceDictionary，然後載入具有 DynamicResource 標記延伸的資源。
ms.prod: xamarin
ms.assetId: B7B17F66-4E37-4B50-9A57-351B62BE4FED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2019
ms.openlocfilehash: 43626c1f1581966a5b1ef65f97c177b83f19d0c2
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517605"
---
# <a name="theme-a-xamarinforms-application"></a>為 Xamarin. Forms 應用程式建立主題

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)

Xamarin： Forms 應用程式可以使用`DynamicResource`標記延伸，在執行時間動態回應樣式變更。 此標記延伸類似于`StaticResource`標記延伸，這兩者都使用字典索引鍵從提取值。 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 不過，當`StaticResource`標記延伸執行單一字典查閱時， `DynamicResource`標記延伸會維護字典索引鍵的連結。 因此，如果已取代與索引鍵相關聯的值，變更就會套用至[`VisualElement`](xref:Xamarin.Forms.VisualElement)。 這可讓執行時間主題在 Xamarin. Forms 應用程式中執行。

在 Xamarin. Forms 應用程式中執行執行時間主題的流程如下所示：

1. 在中定義每個主題的資源[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)。
1. 使用`DynamicResource`標記延伸，在應用程式中取用主題資源。
1. 設定應用程式的**app.xaml**檔案中的預設主題。
1. 加入程式碼以在執行時間載入主題。

> [!NOTE]
> 在執行時間變更主題需要使用 XAML 樣式，而且目前無法使用 CSS。

下列螢幕擷取畫面顯示主題頁面，其中包含使用淺色主題的 iOS 應用程式，以及使用深色主題的 Android 應用程式：

[![Screenshot of the main page of a themed app, on iOS and Android](theming-images/main-page-both-themes.png "主題應用程式的主頁面") ](theming-images/main-page-both-themes-large.png#lightbox "主題應用程式的主頁面") 
Ios 和 android 上主題應用程式之 [詳細資料] 頁面的 ios 和 android[![螢幕擷取畫面](theming-images/detail-page-both-themes.png "主題應用程式的詳細資料頁面")](theming-images/detail-page-both-themes-large.png#lightbox "主題應用程式的詳細資料頁面")

## <a name="define-themes"></a>定義主題

主題會定義為儲存在中之資源物件的集合[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)。

下列範例會顯示範例`LightTheme`應用程式中的：

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ThemingDemo.LightTheme">
    <Color x:Key="PageBackgroundColor">White</Color>
    <Color x:Key="NavigationBarColor">WhiteSmoke</Color>
    <Color x:Key="PrimaryColor">WhiteSmoke</Color>
    <Color x:Key="SecondaryColor">Black</Color>
    <Color x:Key="PrimaryTextColor">Black</Color>
    <Color x:Key="SecondaryTextColor">White</Color>
    <Color x:Key="TertiaryTextColor">Gray</Color>
    <Color x:Key="TransparentColor">Transparent</Color>
</ResourceDictionary>
```

下列範例會顯示範例`DarkTheme`應用程式中的：

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ThemingDemo.DarkTheme">
    <Color x:Key="PageBackgroundColor">Black</Color>
    <Color x:Key="NavigationBarColor">Teal</Color>
    <Color x:Key="PrimaryColor">Teal</Color>
    <Color x:Key="SecondaryColor">White</Color>
    <Color x:Key="PrimaryTextColor">White</Color>
    <Color x:Key="SecondaryTextColor">White</Color>
    <Color x:Key="TertiaryTextColor">WhiteSmoke</Color>
    <Color x:Key="TransparentColor">Transparent</Color>
</ResourceDictionary>
```

每[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)個[`Color`](xref:Xamarin.Forms.Color)都包含定義各自主題的資源，每`ResourceDictionary`個都使用相同的索引鍵值。 如需資源字典的詳細資訊，請參閱[資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

> [!IMPORTANT]
> 每個`ResourceDictionary`都需要程式碼後置檔案，這會`InitializeComponent`呼叫方法。 這是必要的，因此，您可以在執行時間建立代表所選主題的 CLR 物件。

## <a name="set-a-default-theme"></a>設定預設主題

應用程式需要預設主題，讓控制項具有所取用資源的值。 藉由將主題的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)合併到應用程式層級`ResourceDictionary` （定義于**app.xaml**中），可以設定預設主題：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ThemingDemo.App">
    <Application.Resources>
        <ResourceDictionary Source="Themes/LightTheme.xaml" />
    </Application.Resources>
</Application>
```

如需合併資源字典的詳細資訊，請參閱[合併的資源字典](~/xamarin-forms/xaml/resource-dictionaries.md#merged-resource-dictionaries)。

## <a name="consume-theme-resources"></a>使用主題資源

當應用程式想要使用儲存在代表主題之中的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)資源時，應該使用`DynamicResource`標記延伸來執行此動作。 這可確保在執行時間選取不同的主題時，將會套用新主題中的值。

下列範例顯示可套用至[`Label`](xref:Xamarin.Forms.Label)物件的範例應用程式中的三個樣式：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ThemingDemo.App">
    <Application.Resources>

        <Style x:Key="LargeLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource SecondaryTextColor}" />
            <Setter Property="FontSize"
                    Value="30" />
        </Style>

        <Style x:Key="MediumLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource PrimaryTextColor}" />
            <Setter Property="FontSize"
                    Value="25" />
        </Style>

        <Style x:Key="SmallLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource TertiaryTextColor}" />
            <Setter Property="FontSize"
                    Value="15" />
        </Style>

    </Application.Resources>
</Application>
```

這些樣式是在應用層級的資源字典中定義，因此可供多個頁面使用。 每種樣式都會使用具有標記`DynamicResource`延伸的主題資源。

這些樣式接著會供頁面使用：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ThemingDemo"
             x:Class="ThemingDemo.UserSummaryPage"
             Title="User Summary"
             BackgroundColor="{DynamicResource PageBackgroundColor}">
    ...
    <ScrollView>
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="200" />
                <RowDefinition Height="120" />
                <RowDefinition Height="70" />
            </Grid.RowDefinitions>
            <Grid BackgroundColor="{DynamicResource PrimaryColor}">
                <Label Text="Face-Palm Monkey"
                       VerticalOptions="Center"
                       Margin="15"
                       Style="{StaticResource MediumLabelStyle}" />
                ...
            </Grid>
            <StackLayout Grid.Row="1"
                         Margin="10">
                <Label Text="This monkey reacts appropriately to ridiculous assertions and actions."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Cynical but not unfriendly."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Seven varieties of grimaces."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Doesn't laugh at your jokes."
                       Style="{StaticResource SmallLabelStyle}" />
            </StackLayout>
            ...
        </Grid>
    </ScrollView>
</ContentPage>
```

當主題資源直接取用時，應該搭配`DynamicResource`標記延伸來使用。 不過，使用`DynamicResource`標記延伸的樣式時，應該搭配`StaticResource`標記延伸來使用。

如需樣式的詳細資訊，請參閱[使用 XAML 樣式設定 Xamarin 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)。 如需`DynamicResource`標記延伸的詳細資訊，請參閱[Xamarin 中的動態樣式](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)。

## <a name="load-a-theme-at-runtime"></a>在執行時間載入主題

在執行時間選取主題時，應用程式應該：

1. 從應用程式中移除目前的主題。 這是藉由清除應用[`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)層級[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)的屬性來達成。
2. 載入選取的主題。 藉由將所選主題的實例新增至應用層級`MergedDictionaries` `ResourceDictionary`的屬性，即可達成此目的。

任何[`VisualElement`](xref:Xamarin.Forms.VisualElement)設定具有`DynamicResource`標記延伸之屬性的物件，都會套用新的主題值。 這是因為`DynamicResource`標記延伸會維護字典索引鍵的連結。 因此，當取代與索引鍵相關聯的值時，會將變更套用`VisualElement`至物件。

在範例應用程式中，會透過包含的[`Picker`](xref:Xamarin.Forms.Picker)模式頁面來選取主題。 下列程式碼顯示`OnPickerSelectionChanged`方法，當選取的主題變更時，就會執行此方法：

```csharp
void OnPickerSelectionChanged(object sender, EventArgs e)
{
    Picker picker = sender as Picker;
    Theme theme = (Theme)picker.SelectedItem;

    ICollection<ResourceDictionary> mergedDictionaries = Application.Current.Resources.MergedDictionaries;
    if (mergedDictionaries != null)
    {
        mergedDictionaries.Clear();

        switch (theme)
        {
            case Theme.Dark:
                mergedDictionaries.Add(new DarkTheme());
                break;
            case Theme.Light:
            default:
                mergedDictionaries.Add(new LightTheme());
                break;
        }
    }
}
```

## <a name="related-links"></a>相關連結

- [主題（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [回應系統主題變更](system-theme-changes.md)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin 中的動態樣式](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [使用 XAML 樣式設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
