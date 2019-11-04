---
title: Xamarin. 表單地圖位置和距離
description: '[Xamarin] 命名空間包含一個位置結構，通常會在定位地圖和其釘選時使用，以及在定位地圖時選擇性使用的距離結構。'
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/30/2019
ms.openlocfilehash: a68eab7bb3e6da764f5a35af4461d6af2be50ebe
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426150"
---
# <a name="xamarinforms-map-position-and-distance"></a>Xamarin. 表單地圖位置和距離

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)命名空間包含的[`Position`](xref:Xamarin.Forms.Maps.Position)結構，通常會在定位地圖和其釘選時使用，以及可選擇性地在定位對應時使用的[`Distance`](xref:Xamarin.Forms.Maps.Distance)結構。

## <a name="position"></a>位置

[`Position`](xref:Xamarin.Forms.Maps.Position)結構會封裝儲存為緯度和經度值的位置。 這個結構會定義兩個唯讀屬性：

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)，屬於 `double`類型，代表以十進位為單位的位置緯度。
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)，屬於 `double`類型，代表以十進位為單位之位置的經度。

[`Position`](xref:Xamarin.Forms.Maps.Position)物件是使用 `Position` 的「函式」建立，這需要指定為 `double` 值的緯度和經度引數：

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

> [!NOTE]
> 建立 `Position` 物件時，會在-90.0 和90.0 之間壓制緯度值，而經度值將會壓制到-180.0 和180.0 之間。

## <a name="distance"></a>長途電話

[`Distance`](xref:Xamarin.Forms.Maps.Distance)結構會封裝儲存為 `double` 值的距離，代表以量為單位的距離。 此結構會定義三個唯讀屬性：

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)，屬於 `double`類型，代表 `Distance`所跨越的距離（公里）。
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)，屬於 `double`類型，代表 `Distance`所跨越的量（以量為單位）。
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)，屬於 `double`類型，代表 `Distance`所跨越的距離（以英里為單位）。

[`Distance`](xref:Xamarin.Forms.Maps.Distance)物件可以使用 `Distance` 的函式來建立，這需要指定為 `double`的計量引數：

```csharp
Distance distance = new Distance(1450.5);
```

或者，您可以使用[`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*)、 [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*)和[`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*) factory 方法來建立[`Distance`](xref:Xamarin.Forms.Maps.Distance)物件：

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
```

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
