---
title: Xamarin. Forms CarouselView
description: CarouselView 是以可滾動的版面配置呈現資料的視圖，使用者可以在其中滑動以移動專案集合。
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 816c1b6e4ab497d0ada0f80fa3ad4800912587c3
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696992"
---
# <a name="xamarinforms-carouselview"></a>Xamarin. Forms CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

[@No__t_1](xref:Xamarin.Forms.CarouselView)是以可滾動的版面配置呈現資料的視圖，使用者可以在其中滑動以移動專案集合。

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

[@No__t_1](xref:Xamarin.Forms.CarouselView)會藉由將其[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)屬性設定為任何會執行 `IEnumerable` 的集合來填入資料。 將 [ [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) ] 屬性設定為 [ [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)]，即可定義每個專案的外觀。

## <a name="layoutlayoutmd"></a>[版面配置](layout.md)

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView)會在水準清單中顯示其專案。 不過，它也可以存取與 CollectionView 相同的版面配置，包括垂直方向。

## <a name="interactioninteractionmd"></a>[操作](interaction.md)

[@No__t_1](xref:Xamarin.Forms.CarouselView)中目前顯示的專案可以透過 `CurrentItem` 和 `Position` 屬性來存取。

## <a name="empty-viewsemptyviewmd"></a>[空白的視圖](emptyview.md)

在[`CarouselView`](xref:Xamarin.Forms.CarouselView)中，您可以指定空的視圖，以在沒有資料可供顯示時提供意見反應給使用者。 空白的視圖可以是字串、視圖或多個視圖。

## <a name="scrollingscrollingmd"></a>[捲動](scrolling.md)

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，以便完全顯示專案。 此外， [`CarouselView`](xref:Xamarin.Forms.CarouselView)會定義兩個[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法，以程式設計方式將專案滾動到 view。 其中一個多載會將指定索引處的專案滾動到 view，而另一個則會將指定的專案滾動到 view。
