---
title: Xamarin.Forms 雙螢幕設計模式
description: 本指南說明 Xamarin.Forms 如何支援針對雙螢幕裝置最佳化的各種不同設計模式。
ms.prod: xamarin
ms.assetid: 3176d792-6dba-4e00-b463-497c58678ee9
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: c739b32cb45c27bbbc166f898dee964a7cbbbce6
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145522"
---
# <a name="xamarinforms-dual-screen-design-patterns"></a>Xamarin.Forms 雙螢幕設計模式

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)

本指南介紹我們針對雙螢幕裝置所建議的設計模式，其中包含程式碼和範例，可協助您建立介面來提供吸引人且實用的使用者體驗。

## <a name="extended-canvas-pattern"></a>延伸畫布模式

延伸畫布模式會將兩個螢幕視為一個大型畫布，以便顯示地圖、影像、試算表，或其他利用延伸來取用最大空間的類似內容。

![](design-patterns-images/extended-canvas-sample.png "Extended canvas sample")

```xaml
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:d="http://xamarin.com/schemas/2014/forms/design"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    x:Class="Xamarin.Duo.Forms.Samples.ExtendCanvas">
    <ContentPage.Content>
        <Grid>
            <WebView x:Name="webView" HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
            <SearchBar x:Name="searchBar" Placeholder="Find a place..." BackgroundColor="DarkGray" Opacity="0.8" HorizontalOptions="FillAndExpand" VerticalOptions="Start" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

在此範例中， `Grid` 和內部內容會展開，以取用螢幕的所有可用空間，不論是在單一螢幕上顯示，還是跨越兩個螢幕。

## <a name="master-detail-pattern"></a>主要檢視與詳細檢視模式

主要檢視與詳細檢視模式是指主要檢視 (通常是顯示在左側的清單) 提供內容，讓使用者從中選取項目，以在右側檢視其詳細資料。

![](design-patterns-images/master-detail-sample.png "Master detail sample")

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
    x:Class="Xamarin.Duo.Forms.Samples.MasterDetail">
    <dualScreen:TwoPaneView MinWideModeWidth="4000" MinTallModeHeight="4000">
        <dualScreen:TwoPaneView.Pane1>
            <local:Master x:Name="masterPage"></local:Master>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <local:Details x:Name="detailsPage"></local:Details>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

在此範例中，您可以利用 `TwoPaneView` 在一個窗格上設定清單，然後在另一個窗格上設定詳細檢視。

## <a name="two-page-pattern"></a>雙頁模式

雙頁模式適用於適合雙頁版面配置的內容，例如文件閱讀程式、筆記或畫板。

![](design-patterns-images/two-page-sample.png "Two page sample")

```xaml
<Grid x:Name="layout">
    <CollectionView x:Name="cv" BackgroundColor="LightGray">
        <CollectionView.ItemsLayout>
            <GridItemsLayout
                SnapPointsAlignment="Start"
                SnapPointsType="MandatorySingle"
                Orientation="Horizontal"
                HorizontalItemSpacing="{Binding Source={x:Reference mainPage}, Path=HingeWidth}" />
        </CollectionView.ItemsLayout>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Frame BackgroundColor="LightGray" Padding="0" Margin="0"
                        WidthRequest="{Binding Source={x:Reference mainPage}, Path=ContentWidth}"
                        HeightRequest="{Binding Source={x:Reference mainPage}, Path=ContentHeight}">
                    <Frame Margin="20" BackgroundColor="White">
                        <Label FontSize="Large" Text="{Binding .}" VerticalTextAlignment="Center" HorizontalTextAlignment="Center" HorizontalOptions="Center" VerticalOptions="Center"></Label>
                    </Frame>
                </Frame>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</Grid>
```

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 具有分割鉸鏈寬度的格線版面配置，因此適用於提供這種雙螢幕體驗。

## <a name="dual-view-pattern"></a>雙檢視模式

雙檢視看起來可能就像是「雙頁」檢視，但不同之處在於內容和使用者案例。 在此模式中，您會並排比較內容，可能是為了編輯文件或相片、比較不同的餐廳菜單，或區分程式碼檔案的合併衝突。

![](design-patterns-images/dual-view-sample.png "Dual view sample")

```xaml
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
    x:Class="Xamarin.Duo.Forms.Samples.DualViewListPage">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <CollectionView SelectionMode="Single" x:Name="mapList">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Grid Padding="10,5,10,5">
                            <Frame Visual="Material" BorderColor="LightGray">
                                <StackLayout Padding="5">
                                    <Label FontSize="Title" Text="{Binding Title}"></Label>
                                </StackLayout>
                            </Frame>
                        </Grid>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <local:DualViewMap x:Name="mapPage"></local:DualViewMap>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="companion-pattern"></a>附屬模式

附屬模式示範如何使用第二個螢幕來提供與主要檢視相關的第二層內容，例如繪圖應用程式、遊戲或媒體編輯。

![](design-patterns-images/companion-pane-sample.png "Companion pane sample")

```xaml
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples" xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:dualscreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
    x:Name="mainPage"
    x:Class="Xamarin.Duo.Forms.Samples.CompanionPane"
    BackgroundColor="LightGray"
    Visual="Material">
    <dualscreen:TwoPaneView x:Name="twoPaneView" MinWideModeWidth="4000" MinTallModeHeight="4000">
        <dualscreen:TwoPaneView.Pane1>
            <CarouselView x:Name="cv" BackgroundColor="LightGray" IsScrollAnimated="False" >
                <CarouselView.ItemTemplate>
                    <DataTemplate>
                        <Frame BackgroundColor="LightGray" Padding="0" Margin="0"
                           WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane1.Width}"
                           HeightRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane1.Height}">
                            <Frame Margin="20" BackgroundColor="White">
                                <Label FontSize="Large" Text="{Binding ., StringFormat='Slide Content {0}'}" VerticalTextAlignment="Center" HorizontalTextAlignment="Center" HorizontalOptions="Center" VerticalOptions="Center"></Label>
                            </Frame>
                        </Frame>
                    </DataTemplate>
                </CarouselView.ItemTemplate>
            </CarouselView>
        </dualscreen:TwoPaneView.Pane1>
        <dualscreen:TwoPaneView.Pane2>
            <CollectionView x:Name="indicators"
            SelectionMode="Single"
            Margin="20, 20, 20, 20"
            BackgroundColor="LightGray"
            WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane2.Width}"
            ItemsSource="{Binding Source={x:Reference cv}, Path=ItemsSource}">
                <CollectionView.Resources>
                    <ResourceDictionary>
                        <Style TargetType="Frame">
                            <Setter Property="VisualStateManager.VisualStateGroups">
                                <VisualStateGroupList>
                                    <VisualStateGroup x:Name="CommonStates">
                                        <VisualState x:Name="Normal">
                                            <VisualState.Setters>
                                                <Setter Property="Padding" Value="0"></Setter>
                                            </VisualState.Setters>
                                        </VisualState>
                                        <VisualState x:Name="Selected">
                                            <VisualState.Setters>
                                                <Setter Property="BorderColor" Value="Green" />
                                                <Setter Property="Padding" Value="1"></Setter>
                                            </VisualState.Setters>
                                        </VisualState>
                                    </VisualStateGroup>
                                </VisualStateGroupList>
                            </Setter>
                        </Style>
                    </ResourceDictionary>
                </CollectionView.Resources>
                <CollectionView.ItemsLayout>
                    <LinearItemsLayout Orientation="Vertical" ItemSpacing="10"></LinearItemsLayout>
                </CollectionView.ItemsLayout>
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Frame WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane2.Width}" CornerRadius="10" HeightRequest="60" BackgroundColor="White" Margin="0">
                            <StackLayout HorizontalOptions="Fill" VerticalOptions="Fill"  Orientation="Horizontal">
                                <Label FontSize="Micro" Padding="20,0,20,0" VerticalTextAlignment="Center" WidthRequest="140" Text="{Binding ., StringFormat='Slide Content {0}'}"></Label>
                                <Label FontSize="Small" Padding="20,0,20,0" VerticalTextAlignment="Center" HorizontalOptions="FillAndExpand" BackgroundColor="DarkGray"  Grid.Column="1" Text="{Binding ., StringFormat='Slide {0}'}"></Label>
                            </StackLayout>
                        </Frame>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </dualscreen:TwoPaneView.Pane2>
    </dualscreen:TwoPaneView>
</ContentPage>
```

## <a name="related-links"></a>相關連結

- [雙螢幕範例 (GitHub)](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)
- [建立適用於雙螢幕裝置的應用程式](index.md)