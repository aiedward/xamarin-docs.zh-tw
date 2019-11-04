---
title: Xamarin. 表單地圖
description: 地圖控制項會顯示地圖，而且需要有 [Xamarin] NuGet 套件。
ms.prod: xamarin
ms.assetid: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 013e126b76de08442327707cd0502f207826dad8
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2019
ms.locfileid: "73425590"
---
# <a name="xamarinforms-map"></a>Xamarin. 表單地圖

## <a name="initialization-and-configurationsetupmd"></a>[初始化和組態](setup.md)

在應用程式中使用對應功能時，需要有[Xamarin](https://www.nuget.org/packages/Xamarin.Forms.Maps/) NuGet 套件。 此外，存取使用者的位置需要已授與應用程式的「位置」許可權。

## <a name="map-controlmapmd"></a>[地圖控制項](map.md)

[`Map`](xref:Xamarin.Forms.Maps.Map)控制項是用來顯示和標注地圖的跨平臺視圖。 它會使用每個平臺的原生地圖控制項，為使用者提供快速且熟悉的地圖體驗。

## <a name="position-and-distanceposition-distancemd"></a>[位置和距離](position-distance.md)

在定位地圖和其釘選時，通常會使用[`Position`](xref:Xamarin.Forms.Maps.Position)結構，而[`Distance`](xref:Xamarin.Forms.Maps.Distance)結構則可選擇性地在定位對應時使用。

## <a name="pinspinsmd"></a>[釘選](pins.md)

[`Map`](xref:Xamarin.Forms.Maps.Map)控制項允許以[`Pin`](xref:Xamarin.Forms.Maps.Pin)物件標記位置。 `Pin` 是在攻絲時開啟資訊視窗的地圖示記。

## <a name="polygons-and-polylinespolygonsmd"></a>[多邊形和聚合線條](polygons.md)

`Polygon` 和 `Polyline` 元素可讓您反白顯示地圖上的特定區域。 `Polygon` 是一個完全封閉的圖形，可以有筆觸和填滿色彩。 `Polyline` 是不會完全括住區域的一行。

## <a name="geocodinggeocodermd"></a>[地理編碼](geocoder.md)

[`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder)類別會在儲存于[`Position`](xref:Xamarin.Forms.Maps.Position)物件中的字串位址與緯度和經度座標之間進行轉換。

## <a name="launch-the-native-map-appnative-map-appmd"></a>[啟動原生對應應用程式](native-map-app.md)

在每個平臺上的原生對應應用程式，都可以由 Xamarin. Essentials `Launcher` 類別，從 Xamarin. Forms 應用程式中啟動。
