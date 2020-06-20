---
title: Xamarin.FormsCarouselView 簡介
description: CarouselView 是以可滾動的版面配置呈現資料的視圖，使用者可以在其中滑動以移動專案集合。
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2e67acd0188e1147481005502ad9ccdaada645d9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140266"
---
# <a name="xamarinforms-carouselview-introduction"></a>Xamarin.FormsCarouselView 簡介

![](~/media/shared/preview.png "This API is currently pre-release")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)是以可滾動版面配置呈現資料的視圖，使用者可以在其中滑動以在專案集合之間移動。 根據預設， `CarouselView` 會以水準方向顯示其專案。 螢幕上會顯示單一專案，而滑動手勢會導致向前和向後流覽專案的集合。 此外，也可以顯示表示中每個專案的指標 `CarouselView` ：

[![CarouselView 和 IndicatorView （在 iOS 和 Android 上）的螢幕擷取畫面](populate-data-images/indicators.png "IndicatorView 圓形")](populate-data-images/indicators-large.png#lightbox "IndicatorView 圓形")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)可在4.3 中取得 Xamarin.Forms 。 不過，它目前是實驗性，而且只能透過將下列程式程式碼新增至您的 `AppDelegate` iOS 或 `MainActivity` Android 上的類別，然後再呼叫 `Forms.Init` ：

```csharp
Forms.SetFlags("CarouselView_Experimental");
```

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)在 iOS 和 Android 上都有提供，但某些功能只能在通用 Windows 平臺部分使用。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)與共享大部分的執行 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 不過，這兩個控制項有不同的使用案例。 `CollectionView`通常用來顯示任何長度的資料清單，而 `CarouselView` 通常用來反白顯示有限長度清單中的資訊。
