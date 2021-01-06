---
title: Xamarin.Forms CarouselView
description: CarouselView 是以可滾動的版面配置呈現資料的視圖，使用者可以在其中滑動以移動專案集合。
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 958a7c292ba636368a016894e98fe8aaff0d0f60
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940560"
---
# <a name="no-locxamarinforms-carouselview"></a>Xamarin.Forms CarouselView

## <a name="introduction"></a>[簡介](introduction.md)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)是以可滾動的版面配置呈現資料的視圖，使用者可以在其中滑動以在專案集合中移動。

## <a name="data"></a>[Data](populate-data.md)

藉 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 由將資料的屬性設定為任何執行的集合，來填入資料 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) `IEnumerable` 。 您可以藉由將屬性設定為，來定義每個專案的外觀 [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。

## <a name="layout"></a>[版面配置](layout.md)

依預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會在水準清單中顯示其專案。 不過，它也可以存取與 CollectionView 相同的版面配置，包括垂直方向。

## <a name="interaction"></a>[互動](interaction.md)

中目前顯示的專案 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 可透過和屬性來存取 `CurrentItem` `Position` 。

## <a name="empty-views"></a>[空白視圖](emptyview.md)

在中 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，您可以指定空的視圖，在沒有資料可供顯示時，提供意見反應給使用者。 空白視圖可以是字串、視圖或多個視圖。

## <a name="scrolling"></a>[捲動](scrolling.md)

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，使專案完全顯示。 此外，也 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會定義兩個 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 方法，以程式設計方式將專案滾動到視圖中。 其中一個多載會將指定索引處的專案滾動至 view，而另一個多載會將指定的專案滾動至 view。
