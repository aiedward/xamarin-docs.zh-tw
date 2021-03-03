---
title: Xamarin.Forms 地圖位置和距離
description: Xamarin.Forms。Maps 命名空間包含位置結構，通常會在定位地圖和其圖釘時使用，以及在定位地圖時選擇性地使用的距離結構。
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ccdcfc28e1d439b390459be242b959f53d0bd915
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367708"
---
# <a name="xamarinforms-map-position-and-distance"></a>Xamarin.Forms 地圖位置和距離

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)命名空間包含的 [`Position`](xref:Xamarin.Forms.Maps.Position) 結構，通常是在定位地圖和其圖釘時使用，而在 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 定位對應時可以選擇性地使用結構。

## <a name="position"></a>位置

[`Position`](xref:Xamarin.Forms.Maps.Position)結構會封裝儲存為緯度和經度值的位置。 此結構會定義兩個唯讀屬性：

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)， `double` 代表以十進位為單位的位置之緯度的型別。
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)，屬於類型 `double` ，代表以小數點為單位的位置的經度。

[`Position`](xref:Xamarin.Forms.Maps.Position) 物件是使用函式所建立，而此函式 `Position` 需要將緯度和經度引數指定為 `double` 值：

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

建立物件時 `Position` ，緯度值會在-90.0 和90.0 之間壓制，而經度值將會在-180.0 和180.0 之間壓制。

> [!NOTE]
> `GeographyUtils`類別具有 `ToRadians` 將值從度數轉換成弧度的擴充方法 `double` ，以及 `ToDegrees` 將 `double` 值從弧度轉換成度數的擴充方法。

## <a name="distance"></a>距離

[`Distance`](xref:Xamarin.Forms.Maps.Distance)結構會封裝儲存為值的距離 `double` ，表示以量值為單位的距離。 此結構會定義三個唯讀屬性：

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)，代表所 `double` 跨越之距離（以公里為限）的型別 `Distance` 。
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)，屬於類型 `double` ，代表所跨越之量表的距離 `Distance` 。
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)，屬於類型 `double` ，代表所跨越的距離（以英里為單位） `Distance` 。

[`Distance`](xref:Xamarin.Forms.Maps.Distance) 您可以使用此函式來建立物件，而此函式 `Distance` 需要將計量引數指定為 `double` ：

```csharp
Distance distance = new Distance(1450.5);
```

或者，您 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 可以使用 [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*) 、 [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*) 、 [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*) 和 `BetweenPositions` factory 方法來建立物件：

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
Distance distance4 = Distance.BetweenPositions(position1, position2);
```

## <a name="related-links"></a>相關連結

- [Maps 範例](/samples/xamarin/xamarin-forms-samples/workingwithmaps)