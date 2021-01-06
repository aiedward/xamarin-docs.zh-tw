---
title: Xamarin.Forms CarouselView 簡介
description: CarouselView 是以可滾動的版面配置呈現資料的視圖，使用者可以在其中滑動以移動專案集合。
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2aa33b0bd2e11d854f4f4dcfe03258a621301395
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939021"
---
# <a name="no-locxamarinforms-carouselview-introduction"></a>Xamarin.Forms CarouselView 簡介

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 是以可滾動的版面配置呈現資料的視圖，使用者可以在其中滑動以移動專案集合。 依預設， `CarouselView` 會以水準方向顯示其專案。 畫面上將會顯示單一專案，其中的滑動手勢會導致向前和向後流覽專案集合。 此外，也可以顯示表示中每個專案的指標 `CarouselView` ：

[![IOS 和 Android 上 CarouselView 和 IndicatorView 的螢幕擷取畫面](populate-data-images/indicators.png "IndicatorView 圓形")](populate-data-images/indicators-large.png#lightbox "IndicatorView 圓形")

依預設，會 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 提供對其專案集合的迴圈存取。 因此，從集合中的第一個專案向前輕輕的方式將會顯示集合中的最後一個專案。 同樣地，從集合中的最後一個專案開始，將會回到集合中的第一個專案。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 與一起共用許多的實作為 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 不過，這兩個控制項有不同的使用案例。 `CollectionView` 通常用來顯示任何長度的資料清單，而 `CarouselView` 通常用來反白顯示有限長度清單中的資訊。
