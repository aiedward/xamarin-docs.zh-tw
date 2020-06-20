---
title: Xamarin.Forms地圖
description: 地圖控制項會顯示對應，而且需要 Xamarin.Forms 。對應 NuGet 套件。
ms.prod: xamarin
ms.assetid: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2461ffa8168207e6a57fae005f752be48772a34a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139824"
---
# <a name="xamarinforms-map"></a>Xamarin.Forms地圖

## <a name="initialization-and-configuration"></a>[初始化和設定](setup.md)

[ Xamarin.Forms 。需要 maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) NuGet 封裝，才能在應用程式中使用對應功能。 此外，存取使用者的位置需要已授與應用程式的「位置」許可權。

## <a name="map-control"></a>[地圖控制項](map.md)

[`Map`](xref:Xamarin.Forms.Maps.Map)控制項是用來顯示和標注地圖的跨平臺視圖。 它會使用每個平臺的原生地圖控制項，為使用者提供快速且熟悉的地圖體驗。

## <a name="position-and-distance"></a>[位置和距離](position-distance.md)

[`Position`](xref:Xamarin.Forms.Maps.Position)結構通常會在定位地圖和其釘選時使用，以及 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 可選擇性地在定位對應時使用的結構。

## <a name="pins"></a>[圖釘](pins.md)

[`Map`](xref:Xamarin.Forms.Maps.Map)控制項允許以物件標記位置 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 。 `Pin`是一種地圖示記，會在攻絲時開啟資訊視窗。

## <a name="polygons-polylines-and-circles"></a>[多邊形, 聚合線條及圓形](polygons.md)

`Polygon`、 `Polyline` 和 `Circle` 元素可讓您反白顯示地圖上的特定區域。 `Polygon`是一個完全封閉的圖形，可以有筆觸和填滿色彩。 `Polyline`是不會完全括住區域的線條。 會反白 `Circle` 顯示地圖的迴圈區域。

## <a name="geocoding"></a>[地理編碼](geocoder.md)

[`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder)類別會在儲存于物件中的字串位址與緯度和經度座標之間進行轉換 [`Position`](xref:Xamarin.Forms.Maps.Position) 。

## <a name="launch-the-native-map-app"></a>[啟動原生地圖應用程式](native-map-app.md)

每個平臺上的原生對應應用程式都可以 Xamarin.Forms 由類別從應用程式啟動 Xamarin.Essentials `Launcher` 。
