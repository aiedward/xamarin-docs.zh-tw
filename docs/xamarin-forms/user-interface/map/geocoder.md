---
title: Xamarin. 表單地圖地理編碼
description: 本文說明如何使用 Geocoder 類別來地理編碼和反轉地理編碼對應資料。
ms.prod: xamarin
ms.assetid: DE7DB31A-8921-4614-8B49-DAEF1E7B03B3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/22/2019
ms.openlocfilehash: 6df7a2e0056cd2d448206b56e1e9d2600b342b3b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490255"
---
# <a name="xamarinforms-map-geocoding"></a>Xamarin. 表單地圖地理編碼

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)命名空間提供[`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder)類別，它會在字串位址與緯度和經度座標之間進行轉換，這些會儲存在[`Position`](xref:Xamarin.Forms.Maps.Position)物件中。 如需[`Position`](xref:Xamarin.Forms.Maps.Position)結構的詳細資訊，請參閱[地圖位置和距離](position-distance.md)。

## <a name="geocode-an-address"></a>地理編碼位址

藉由建立[`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder)實例並在 `Geocoder` 實例上呼叫[`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*)方法，可以將街道位址地理編碼成緯度和經度座標：

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

IEnumerable<Position> approximateLocations = await geoCoder.GetPositionsForAddressAsync("Pacific Ave, San Francisco, California");
Position position = approximateLocations.FirstOrDefault();
string coordinates = $"{position.Latitude}, {position.Longitude}";
```

[`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*)方法會接受代表位址的 `string` 引數，並以非同步方式傳回可能代表位址之[`Position`](xref:Xamarin.Forms.Maps.Position)物件的集合。

## <a name="reverse-geocode-an-address"></a>反向地理編碼位址

您可以藉由建立[`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder)實例並在 `Geocoder` 實例上呼叫[`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*)方法，將緯度和經度座標反向地理編碼為街道位址：

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

Position position = new Position(37.8044866, -122.4324132);
IEnumerable<string> possibleAddresses = await geoCoder.GetAddressesForPositionAsync(position);
string address = possibleAddresses.FirstOrDefault();
```

[`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*)方法會接受由緯度和經度座標組成的[`Position`](xref:Xamarin.Forms.Maps.Position)引數，並以非同步方式傳回字串的集合，這些字串表示位置附近的位址。

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin. 表單地圖位置和距離](position-distance.md)
- [Geocoder API](xref:Xamarin.Forms.Maps.Geocoder)
