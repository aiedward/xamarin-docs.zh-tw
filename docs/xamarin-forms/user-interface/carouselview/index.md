---
title: Xamarin. Forms CarouselView
description: CarouselView 是以類似浮動的版面配置來呈現資料清單的視圖。
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 06d20341053c4f77cb72aac9e1abdc85d9f95fdb
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908265"
---
# <a name="xamarinforms-carouselview"></a>Xamarin. Forms CarouselView

![](~/media/shared/preview.png "此 API 目前是發行前版本")

> [!IMPORTANT]
> CarouselView 檔正在開發中，而某些連結可能會參考 CollectionView 檔。 在大部分情況下，資訊應適用，因為 CarouselView 的本質是以 CollectionView 為基礎。

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)是用來在類似浮動的版面配置中呈現資料的視圖。 其實作為以[`CollectionView`](xref:Xamarin.Forms.CollectionView)為基礎。

## <a name="datacollectionviewpopulate-datamd"></a>[Data](../collectionview/populate-data.md)

會[`CarouselView`](xref:Xamarin.Forms.CarouselView)藉由[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)將資料的屬性設定`IEnumerable`為任何可執行檔集合，來填入資料。 您可以藉由將[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)屬性設定[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)為，來定義清單中每個專案的外觀。

## <a name="layoutlayoutmd"></a>[版面配置](layout.md)

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView)會在水準清單中顯示其專案。 不過，它也可以存取與 CollectionView 相同的版面配置，包括垂直方向。

## <a name="empty-viewscollectionviewemptyviewmd"></a>[空白的視圖](../collectionview/emptyview.md)

在[`CarouselView`](xref:Xamarin.Forms.CarouselView)中，您可以指定空的視圖，以在沒有資料可供顯示時提供意見反應給使用者。 空白的視圖可以是字串、視圖或多個視圖。

## <a name="scrollingcollectionviewscrollingmd"></a>[捲動](../collectionview/scrolling.md)

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，以便完全顯示專案。 此外， [`CarouselView`](xref:Xamarin.Forms.CarouselView)也會定義[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)兩個方法，以程式設計方式將專案滾動到 view。 其中一個多載會將指定索引處的專案滾動到 view，而另一個則會將指定的專案滾動到 view。
