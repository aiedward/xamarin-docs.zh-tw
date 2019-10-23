---
title: Xamarin. Forms CarouselView 簡介
description: CarouselView 是以可滾動的版面配置呈現資料的視圖，使用者可以在其中滑動以移動專案集合。
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 2fe4d984f36880493a9a04d99b63876551366477
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696979"
---
# <a name="xamarinforms-carouselview-introduction"></a>Xamarin. Forms CarouselView 簡介

![](~/media/shared/preview.png "This API is currently pre-release")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)是以可滾動的版面配置呈現資料的視圖，使用者可以在其中滑動以移動專案集合。 根據預設，`CarouselView` 會以水準方向顯示其專案。 螢幕上會顯示單一專案，而滑動手勢會導致向前和向後流覽專案的集合。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)適用于 Xamarin. 表單4.3。 不過，它目前是實驗性，而且只能透過將下列程式程式碼新增至 iOS 上的 `AppDelegate` 類別，或在 Android 上的 `MainActivity` 類別中使用，然後再呼叫 `Forms.Init`：

```csharp
Forms.SetFlags("CarouselView_Experimental");
```

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)適用于 IOS 和 Android，但某些功能只能在通用 Windows 平臺部分使用。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)與[`CollectionView`](xref:Xamarin.Forms.CollectionView)共用大部分的實作為。 不過，這兩個控制項有不同的使用案例。 `CollectionView` 通常用來呈現任何長度的資料清單，而 `CarouselView` 通常用來反白顯示有限長度清單中的資訊。
