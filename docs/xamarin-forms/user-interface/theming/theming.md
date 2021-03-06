---
title: 主題 Xamarin.Forms 應用程式
description: 您可以在應用程式中執行主題 Xamarin.Forms ，方法是為每個主題建立 ResourceDictionary，然後載入具有 DynamicResource 標記延伸的資源。
ms.prod: xamarin
ms.assetId: B7B17F66-4E37-4B50-9A57-351B62BE4FED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 123a0868ce098f8c54e6805a1b2b9a6efb04c238
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375391"
---
# <a name="theme-a-no-locxamarinforms-application"></a>主題 Xamarin.Forms 應用程式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-theming/)

Xamarin.Forms 應用程式可以在執行時間使用標記延伸，以動態方式回應樣式變更 `DynamicResource` 。 此標記延伸類似于 `StaticResource` 標記延伸，這兩者都會使用字典索引鍵從提取值 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 不過，當 `StaticResource` 標記延伸執行單一字典查閱時， `DynamicResource` 標記延伸會維護字典索引鍵的連結。 因此，如果已取代與索引鍵相關聯的值，則會將變更套用至 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 這可讓執行時間主題在 Xamarin.Forms 應用程式中執行。

在應用程式中執行執行時間主題的程式如下所示 Xamarin.Forms ：

1. 在中定義每個主題的資源 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。
1. 使用標記延伸，在應用程式中使用主題資源 `DynamicResource` 。
1. 在應用程式的 **app.config** 檔案中設定預設主題。
1. 加入程式碼以在執行時間載入主題。

> [!IMPORTANT]
> `StaticResource`如果您不需要在執行時間變更應用程式主題，請使用標記延伸。

下列螢幕擷取畫面顯示主題頁面、使用淺色主題的 iOS 應用程式，以及使用深色主題的 Android 應用程式：

[![IOS 和 Android 上主題應用程式主頁面的螢幕擷取畫面](theming-images/main-page-both-themes.png "主題應用程式的主頁面")](theming-images/main-page-both-themes-large.png#lightbox "主題應用程式的主頁面") 
[ ![IOS 和 Android 上主題應用程式之詳細資料頁面的螢幕擷取畫面](theming-images/detail-page-both-themes.png "主題應用程式的詳細資料頁面")](theming-images/detail-page-both-themes-large.png#lightbox "主題應用程式的詳細資料頁面")

> [!NOTE]
> 在執行時間變更主題需要使用 XAML 樣式，而且目前不能使用 CSS。

## <a name="define-themes"></a>定義主題

主題會定義為儲存在中的資源物件集合 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。

下列範例顯示 `LightTheme` 來自範例應用程式的：

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

下列範例顯示 `DarkTheme` 來自範例應用程式的：

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

每個都 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 包含 [`Color`](xref:Xamarin.Forms.Color) 定義個別主題的資源，每個都 `ResourceDictionary` 使用相同的索引鍵值。 如需資源字典的詳細資訊，請參閱 [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

> [!IMPORTANT]
> 每個都需要程式碼後置於檔案 `ResourceDictionary` ，其會呼叫 `InitializeComponent` 方法。 這是必要的，如此一來，就可以在執行時間建立代表所選主題的 CLR 物件。

## <a name="set-a-default-theme"></a>設定預設主題

應用程式需要預設主題，讓控制項具有其取用之資源的值。 您可以藉由將主題合併 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 到應用程式中所定義的應用層級，來設定預設主題 `ResourceDictionary` ： **App.xaml**

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ThemingDemo.App">
    <Application.Resources>
        <ResourceDictionary Source="Themes/LightTheme.xaml" />
    </Application.Resources>
</Application>
```

如需合併資源字典的詳細資訊，請參閱 [合併的資源字典](~/xamarin-forms/xaml/resource-dictionaries.md#merged-resource-dictionaries)。

## <a name="consume-theme-resources"></a>使用主題資源

當應用程式想要取用儲存在 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 代表主題之的資源時，應該使用 `DynamicResource` 標記延伸來進行。 這可確保在執行時間選取不同的主題時，將會套用新主題中的值。

下列範例顯示可套用至物件之範例應用程式的三種樣式 [`Label`](xref:Xamarin.Forms.Label) ：

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

這些樣式是在應用層級的資源字典中定義，因此可供多個頁面使用。 每個樣式都會使用具有標記延伸的主題資源 `DynamicResource` 。

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

當主題資源直接取用時，應使用 `DynamicResource` 標記延伸來取用。 不過，使用標記延伸的樣式使用時 `DynamicResource` ，它應該與 `StaticResource` 標記延伸一起使用。

如需樣式的詳細資訊，請參閱 [ Xamarin.Forms 使用 XAML 樣式設定應用程式](~/xamarin-forms/user-interface/styles/xaml/index.md)的樣式。 如需標記延伸的詳細資訊 `DynamicResource` ，請參閱[中 Xamarin.Forms 的動態樣式](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)。

## <a name="load-a-theme-at-runtime"></a>在執行時間載入主題

在執行時間選取主題時，應用程式應該：

1. 從應用程式移除目前的主題。 這是藉由清除 [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) 應用層級的屬性來達成 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。
2. 載入選取的主題。 將所選主題的實例加入至應用層級的屬性，即可達成此目的 `MergedDictionaries` `ResourceDictionary` 。

任何 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 設定具有標記延伸之屬性的物件， `DynamicResource` 都會套用新的主題值。 這是因為 `DynamicResource` 標記延伸會維護字典索引鍵的連結。 因此，當取代與索引鍵相關聯的值時，會將變更套用至 `VisualElement` 物件。

在範例應用程式中，會透過包含的模式頁面來選取主題 [`Picker`](xref:Xamarin.Forms.Picker) 。 下列程式碼顯示 `OnPickerSelectionChanged` 方法，這個方法會在選取的主題變更時執行：

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

- [主題 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [回應系統主題變更](system-theme-changes.md)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [中的動態樣式 Xamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Xamarin.Forms使用 XAML 樣式設定應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)