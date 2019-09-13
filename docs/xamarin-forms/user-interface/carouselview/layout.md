---
title: Xamarin. Forms CarouselView
description: 根據預設，CarouselView 會在水準清單中顯示其專案。 不過，它也可以存取與 CollectionView 相同的版面配置，包括垂直方向。
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 5bbaeead524089ea604cfd9a9fd7f3a85d04e724
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908279"
---
# <a name="xamarinforms-carouselview-layouts"></a>Xamarin. Forms CarouselView 版面配置

![](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)

## <a name="introduction"></a>簡介

CarouselView 提供的大部分版面配置功能都是源自 CollectionView。 您可以參考 CollectionView 的配置[檔](../collectionview/layout.md)，以查看各種版面配置的使用。

## <a name="differences-from-collectionview"></a>與 CollectionView 的差異

根據預設，CarouselView 中的專案會水準導向，如同應用程式中浮動切換的一般功能。

CarouselView 也提供一些額外的屬性：

> [!IMPORTANT]
> CarouselView 的其他屬性仍在開發中，而且此清單尚未完成。

| API | 函數 |
|---|---|---|
| NumberOfSideItems | 設定顯示在目前專案的每一端的專案數。 預設為0。
| PeekAreaInsets | 提供一種方式，以明顯向使用者指出，CarouselView 有其他專案可供滾動，方法是調整其與目前專案連續的可見度層級。

## <a name="setting-the-number-of-fully-visible-items"></a>設定完全可見專案的數目

根據預設，CarouselView 會在畫面上完整顯示一個專案。 使用者可以設定`NumberOfSideItems`屬性，以允許更多專案顯示在目前專案的相鄰。 請注意，任何設定為`PeekAreaInsets`的值仍將套用。

```xaml
<StackLayout Margin="20">
    <CarouselView ItemsSource="{Binding Monkeys}" HeightRequest="125" NumberOfSideItems="1">
        <CarouselView.ItemTemplate>
            <DataTemplate>
                <Frame BorderColor="Black">
                    <Grid>
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="Auto" />
                        </Grid.ColumnDefinitions>
                        <Label Grid.Column="1"
                            Text="{Binding Name}"
                            FontAttributes="Bold"
                            FontSize="14"/>
                        <Label Grid.Row="1"
                            Grid.Column="1"
                            Text="{Binding Location}"
                            FontAttributes="Italic"
                            FontSize="12"
                            VerticalOptions="End" />
                    </Grid>
                </Frame>
            </DataTemplate>
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

[Android(carouselview-images/side-items.png "CarouselView 端專案")![上具有側邊專案之 CarouselView 的螢幕擷取畫面]](carouselview-images/side-items-large.png#lightbox "CarouselView 端專案")

## <a name="making-adjacent-items-partially-visible"></a>讓連續的專案部分可見

在您的應用程式中使用 CarouselView 時，向使用者指出 CarouselView 的運作方式，是將`PeekAreaInsets`屬性設定為非零值（預設值），這會在螢幕上部分公開它們。

```xaml
<StackLayout Margin="20">
  <CarouselView ItemsSource="{Binding Monkeys}" HeightRequest="125" PeekAreaInsets="100">
      <CarouselView.ItemTemplate>
          <DataTemplate>
              <Frame BorderColor="Black">
                  <Grid>
                      <Grid.RowDefinitions>
                          <RowDefinition Height="Auto" />
                          <RowDefinition Height="Auto" />
                      </Grid.RowDefinitions>
                      <Grid.ColumnDefinitions>
                          <ColumnDefinition Width="Auto" />
                          <ColumnDefinition Width="Auto" />
                      </Grid.ColumnDefinitions>
                      <Label Grid.Column="1"
                          Text="{Binding Name}"
                          FontAttributes="Bold"
                          FontSize="14"/>
                      <Label Grid.Row="1"
                          Grid.Column="1"
                          Text="{Binding Location}"
                          FontAttributes="Italic"
                          FontSize="12"
                          VerticalOptions="End" />
                  </Grid>
              </Frame>
          </DataTemplate>
      </CarouselView.ItemTemplate>
  </CarouselView>
</StackLayout>
```

[Android(carouselview-images/peek-area-insets.png "CarouselView 端專案")![上具有側邊專案之 CarouselView 的螢幕擷取畫面]](carouselview-images/peek-area-insets-large.png#lightbox "CarouselView 端專案")

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)
- [CollectionView 版面設定檔](../collectionview/layout.md)
