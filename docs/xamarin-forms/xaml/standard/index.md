---
title: XAML 標準 （預覽）
description: 這篇文章說明如何開始探索在 Xamarin.Forms 中 XAML Standard 預覽版。
ms.prod: xamarin
ms.assetid: 24382DF1-BE70-4608-B86F-B79FB23E4A78
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/15/2017
ms.openlocfilehash: d9fb19fb233c25e5dd525c7c157013ef66f4a4f3
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51562753"
---
# <a name="xaml-standard-preview"></a>XAML 標準 （預覽）

![預覽](~/media/shared/preview.png)

遵循下列步驟來試驗標準 Xamarin.Forms XAML:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 下載[預覽以下 NuGet 套件](https://aka.ms/xf-xamlstandard-nuget)。
2. 新增**Xamarin.Forms.Alias**至 Xamarin.Forms.NET Standard 與平台專案的 NuGet 套件。
3. 初始化與套件 `Alias.Init()`
4. 新增`xmlns:a`參考 `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. 使用 XAML 中的類型-請參閱[控制項參考](controls.md)如需詳細資訊。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 下載[預覽以下 NuGet 套件](https://aka.ms/xf-xamlstandard-nuget)。
2. 新增**Xamarin.Forms.Alias**至 Xamarin.Forms.NET Standard 與平台專案的 NuGet 套件。
3. 初始化與套件 `Alias.Init()`
4. 新增`xmlns:a`參考 `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. 使用 XAML 中的類型-請參閱[控制項參考](controls.md)如需詳細資訊。

-----

下列 XAML 示範如何在 Xamarin.Forms 中使用某些標準 XAML 控制項`ContentPage`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ContentPage 
    xmlns="http://xamarin.com/schemas/2014/forms" 
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" 
    xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"
    x:Class="XAMLStandardSample.ItemsPage" 
    Title="{Binding Title}" x:Name="BrowseItemsPage">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Add" Clicked="AddItem_Clicked" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <a:StackPanel>
            <ListView x:Name="ItemsListView" ItemsSource="{Binding Items}" VerticalOptions="FillAndExpand" HasUnevenRows="true" RefreshCommand="{Binding LoadItemsCommand}" IsPullToRefreshEnabled="true" IsRefreshing="{Binding IsBusy, Mode=OneWay}" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <ViewCell>
                            <StackLayout Padding="10">
                                <a:TextBlock Text="{Binding Text}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemTextStyle}" FontSize="16" />
                                <a:TextBlock Text="{Binding Description}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemDetailTextStyle}" FontSize="13" />
                            </StackLayout>
                        </ViewCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </a:StackPanel>
    </ContentPage.Content>
</ContentPage>
```

> [!NOTE]
> 需要 xmlns `a:` XAML 標準控制項上的前置詞是目前的預覽版本的限制。


## <a name="related-links"></a>相關連結

- [NuGet 預覽](https://aka.ms/xf-xamlstandard-nuget)
- [控制項參考](controls.md)
