---
title: Xamarin.FormsCarouselView
description: CarouselView 是以可滾動的版面配置呈現資料的視圖，使用者可以在其中滑動以移動專案集合。
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2ff88431869c4a14919fb13e5059cd11fcdbdc3c
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918638"
---
# <a name="no-locxamarinforms-carouselview"></a>Xamarin.FormsCarouselView

![發行前版本 API](~/media/shared/preview.png)

## <a name="introduction"></a>[簡介](introduction.md)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)是以可滾動版面配置呈現資料的視圖，使用者可以在其中滑動以在專案集合之間移動。

## <a name="data"></a>[資料](populate-data.md)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)會藉由將資料的屬性設定 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 為任何可執行檔集合，來填入資料 `IEnumerable` 。 將屬性設定為，即可定義每個專案的外觀 [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。

## <a name="layout"></a>[版面配置](layout.md)

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會在水準清單中顯示其專案。 不過，它也可以存取與 CollectionView 相同的版面配置，包括垂直方向。

## <a name="interaction"></a>[互動](interaction.md)

中目前顯示的專案 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 可以透過和屬性來存取 `CurrentItem` `Position` 。

## <a name="empty-views"></a>[空白的視圖](emptyview.md)

在中 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，您可以指定空的視圖，以在沒有資料可供顯示時提供意見反應給使用者。 空白的視圖可以是字串、視圖或多個視圖。

## <a name="scrolling"></a>[捲動](scrolling.md)

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，以便完全顯示專案。 此外，也 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會定義兩個 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 方法，以程式設計方式將專案滾動到 view。 其中一個多載會將指定索引處的專案滾動到 view，而另一個則會將指定的專案滾動到 view。
