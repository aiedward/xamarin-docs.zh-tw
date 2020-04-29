---
title: Xamarin. 表單地圖位置和距離
description: '[Xamarin] 命名空間包含一個位置結構，通常會在定位地圖和其釘選時使用，以及在定位地圖時選擇性使用的距離結構。'
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
ms.openlocfilehash: 567e1b5620378f0c1b64d17c56c8fb451f18abc3
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517440"
---
# <a name="xamarinforms-map-position-and-distance"></a>Xamarin. 表單地圖位置和距離

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)命名空間包含的[`Position`](xref:Xamarin.Forms.Maps.Position)結構，通常會在定位地圖和其釘選時使用，以及[`Distance`](xref:Xamarin.Forms.Maps.Distance)可選擇性地在定位對應時使用的結構。

## <a name="position"></a>位置

[`Position`](xref:Xamarin.Forms.Maps.Position)結構會封裝儲存為緯度和經度值的位置。 這個結構會定義兩個唯讀屬性：

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)，屬於類型`double`，表示以十進位為單位的位置緯度。
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)，屬於類型`double`，表示以十進位為單位之位置的經度。

[`Position`](xref:Xamarin.Forms.Maps.Position)物件是使用`Position` 「函式」所建立，其需要指定為`double`值的緯度和經度引數：

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

建立`Position`物件時，緯度值會在-90.0 和90.0 之間壓制，而經度值將會在-180.0 和180.0 之間壓制。

> [!NOTE]
> `GeographyUtils`類別的`ToRadians`擴充方法會將`double`值從角度轉換成弧度，以及將`ToDegrees` `double`值從弧度轉換成度數的擴充方法。

## <a name="distance"></a>Distance

[`Distance`](xref:Xamarin.Forms.Maps.Distance)結構會封裝儲存為`double`值的距離，代表以量為單位的距離。 此結構會定義三個唯讀屬性：

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)，屬於類型`double`，表示所`Distance`跨越的距離（以公里為限）。
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)，屬於類型`double`，代表跨越的量（以量為單位） `Distance`。
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)，屬於類型`double`，表示所`Distance`跨越的距離（以英里為單位）。

[`Distance`](xref:Xamarin.Forms.Maps.Distance)物件可以使用此`Distance`函式來建立，而此函式需要指定為`double`的計量引數：

```csharp
Distance distance = new Distance(1450.5);
```

或者， [`Distance`](xref:Xamarin.Forms.Maps.Distance)您可以使用[`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*)、 [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*)、 [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*)和`BetweenPositions` factory 方法來建立物件：

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
Distance distance4 = Distance.BetweenPositions(position1, position2);
```

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
