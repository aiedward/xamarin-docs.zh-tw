---
title: Xamarin. 表單地圖多邊形和折線
description: 本文說明如何在 Xamarin. Forms map 實例上建立多邊形和折線。
ms.prod: xamarin
ms.assetid: CDAF0B02-1AA8-4AD6-94A7-ABFC18006A2D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/20/2019
ms.openlocfilehash: 42c00a060e0477aff4ea02f6213fa751b2adf4dd
ms.sourcegitcommit: 5c22097bed2a8d51ecaf6ca197bf4d449dfe1377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72810490"
---
# <a name="xamarinforms-map-polygons-and-polylines"></a>Xamarin. 表單地圖多邊形和折線

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[![「iOS 和 Android 上的多邊形和聚合線條示範」](polygons-images/polygon-app-cropped.png)](polygons-images/polygon-app.png#lightbox)

`Polygon` 和 `Polyline` 元素可讓您反白顯示地圖上的特定區域。 `Polygon` 是一個完全封閉的圖形，可以有筆觸和填滿色彩。 `Polyline` 是不會完全括住區域的一行。

> [!NOTE]
> `Polygon` 和 `Polyline` 的範例可在範例專案的**PolygonsPage**上找到。

`Polygon` 和 `Polyline` 類別衍生自 `MapElement`，它會公開下列[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)屬性：

- `StrokeColor` 是決定線條色彩的 `Color` 屬性。
- `StrokeWidth` 是決定線條寬度的 `float` 屬性。
- `Geopath` 會同時在 `Polygon` 和 `Polyline`上定義，而且是指定圖形點之[`Position`](xref:Xamarin.Forms.Maps.Position)物件的清單。

`Polygon` 類別會定義一個額外的屬性：

- `FillColor` 是一個 `Color` 屬性，可決定多邊形的背景色彩。

> [!NOTE]
> 如果未指定 `StrokeColor` 屬性，筆劃會預設為黑色。 如果未指定 [`FillColor`] 屬性，則填滿會預設為 [透明]。 因此，如果未指定任何屬性，則圖形會有黑色外框，沒有填滿。

## <a name="create-a-polygon"></a>建立多邊形

藉由具現化 `Polygon` 物件，並將它新增至地圖的 `MapElements` 集合，可以將它加入至對應。 執行下列工作即可用 XAML 來達成這點：

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
         <maps:Map.MapElements>
             <maps:Polygon StrokeColor="#FF9900"
                           StrokeWidth="8"
                           FillColor="#88FF9900">
                 <maps:Polygon.Geopath>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>47.6368678</x:Double>
                             <x:Double>-122.137305</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     ...
                 </maps:Polygon.Geopath>
             </maps:Polygon>
         </maps:Map.MapElements>
     </maps:Map>
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};

// instantiate a polygon
Polygon polygon = new Polygon
{
    StrokeWidth = 8,
    StrokeColor = Color.FromHex("#1BA1E2"),
    FillColor = Color.FromHex("#881BA1E2"),
    Geopath =
    {
        new Position(47.6368678, -122.137305),
        new Position(47.6368894, -122.134655),
        new Position(47.6359424, -122.134655),
        new Position(47.6359496, -122.1325521),
        new Position(47.6424124, -122.1325199),
        new Position(47.642463,  -122.1338932),
        new Position(47.6406414, -122.1344833),
        new Position(47.6384943, -122.1361248),
        new Position(47.6372943, -122.1376912)
    }
};

// add the polygon to the map's MapElements collection
map.MapElements.Add(polygon);
```

`StrokeColor` 和 `StrokeWidth` 屬性會指定為自訂多邊形的外框。 `FillColor` 屬性值符合 `StrokeColor` 屬性值，但指定了 Alpha 值以使其成為透明，讓基礎地圖可以透過圖形顯示。 `GeoPath` 屬性包含定義多邊形點地理座標的 `Position` 物件清單。 `Polygon` 物件一旦加入至 `Map`的 `MapElements` 集合之後，就會在地圖上轉譯。

> [!NOTE]
> `Polygon` 是一個完全封閉的圖形。 如果第一個和最後一個點不相符，就會自動連接。

## <a name="create-a-polyline"></a>建立折線

藉由具現化 `Polyline` 物件，並將它新增至地圖的 `MapElements` 集合，可以將它加入至對應。 執行下列工作即可用 XAML 來達成這點：

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
         <maps:Map.MapElements>
             <maps:Polyline StrokeColor="Blue"
                            StrokeWidth="12">
                 <maps:Polyline.Geopath>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>47.6381401</x:Double>
                             <x:Double>-122.1317367</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     ...
                 </maps:Polyline.Geopath>
             </maps:Polyline>
         </maps:Map.MapElements>
     </maps:Map>
</ContentPage>
```

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};
// instantiate a polyline
Polyline polyline = new Polyline
{
    StrokeColor = Color.Blue,
    StrokeWidth = 12,
    Geopath =
    {
        new Position(47.6381401, -122.1317367),
        new Position(47.6381473, -122.1350841),
        new Position(47.6382847, -122.1353094),
        new Position(47.6384582, -122.1354703),
        new Position(47.6401136, -122.1360819),
        new Position(47.6403883, -122.1364681),
        new Position(47.6407426, -122.1377019),
        new Position(47.6412558, -122.1404056),
        new Position(47.6414148, -122.1418647),
        new Position(47.6414654, -122.1432702)
    }
};

// add the polyline to the map's MapElements collection
map.MapElements.Add(polyline);
```

`StrokeColor` 和 `StrokeWidth` 屬性會指定為自訂行。 `GeoPath` 屬性包含定義折線點地理座標的 `Position` 物件清單。 `Polyline` 物件一旦加入至 `Map`的 `MapElements` 集合之後，就會在地圖上轉譯。

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
