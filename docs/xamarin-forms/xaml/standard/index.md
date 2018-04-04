---
title: XAML 標準 （預覽）
description: 如何開始瀏覽 Xamarin.Forms 中的 XAML 標準預覽
ms.prod: xamarin
ms.assetid: 24382DF1-BE70-4608-B86F-B79FB23E4A78
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 7cb2a79fffda40f09ee75a12d6287b162566741d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="xaml-standard-preview"></a>XAML 標準 （預覽）

![預覽](~/media/shared/preview.png)

遵循下列步驟來實驗 XAML 標準 Xamarin.Forms 中：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 下載[預覽 NuGet 套件](https://aka.ms/xf-xamlstandard-nuget)。
2. 新增**Xamarin.Forms.Alias** NuGet 套件加入 PCL Xamarin.Forms、 標準.NET 與平台專案。
3. 初始化與套件 `Alias.Init()`
4. 新增`xmlns:a`參考 `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. 在 XAML 中使用的類型-請參閱[控制參考](controls.md)如需詳細資訊。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 下載[預覽 NuGet 套件](https://aka.ms/xf-xamlstandard-nuget)。
2. 新增**Xamarin.Forms.Alias** NuGet 套件加入 PCL Xamarin.Forms、 標準.NET 與平台專案。
3. 初始化與套件 `Alias.Init()`
4. 新增`xmlns:a`參考 `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. 在 XAML 中使用的類型-請參閱[控制參考](controls.md)如需詳細資訊。

-----

下列 XAML 會示範在 Xamarin.Forms 中使用某些標準 XAML 控制項`ContentPage`:

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

- [預覽 NuGet](https://aka.ms/xf-xamlstandard-nuget)
- [控制項參考](controls.md)
