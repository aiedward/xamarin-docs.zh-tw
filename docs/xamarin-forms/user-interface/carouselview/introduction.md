---
title: Xamarin. Forms CarouselView 簡介
description: CarouselView 是以類似浮動的版面配置呈現資料的視圖。
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 7979f6ed362c580d9cf80f19b3bc0ea7550ca70c
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70985965"
---
# <a name="xamarinforms-carouselview-introduction"></a>Xamarin. Forms CarouselView 簡介

![](~/media/shared/preview.png "This API is currently pre-release")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)是以類似浮動的方式呈現資訊的視圖。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)適用于 Xamarin. 表單4.3。 不過，它目前是實驗性，而且只能透過將下列程式程式碼新增至 iOS 上的 `AppDelegate` 類別，或在 Android 上的 `MainActivity` 類別中使用，然後再呼叫 `Forms.Init`：

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

_注意：在撰寫本文時，CarouselView 仍會使用 CollectionView 旗標來啟用其功能。_

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)適用于 IOS 和 Android，但某些功能只能在通用 Windows 平臺部分使用。

## <a name="when-to-use-carouselview"></a>使用 CarouselView 的時機

雖然 CarouselView 是從 CollectionView 中的大部分實作為基礎，但其目的更著重于。 雖然您可以自行決定使用 CollectionView 和 CarouselView，但應用程式中的轉盤通常用來反白顯示資訊，並限制在使用的總專案數。
