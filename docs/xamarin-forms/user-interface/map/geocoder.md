---
title: Xamarin.Forms地圖地理編碼
description: 本文說明如何使用來地理編碼和反轉地理編碼對應資料 Xamarin.Forms 。對應 Geocoder 類別。
ms.prod: xamarin
ms.assetid: DE7DB31A-8921-4614-8B49-DAEF1E7B03B3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/22/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7e92385f3a82c2d12881be4ff3e54bc47533696d
ms.sourcegitcommit: ea2abdc789d0e292c3e1700a2b53b92097e0e542
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87517489"
---
# <a name="no-locxamarinforms-map-geocoding"></a>Xamarin.Forms地圖地理編碼

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)命名空間提供的 [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) 類別，會在字串位址與儲存于物件中的緯度和經度座標之間進行轉換 [`Position`](xref:Xamarin.Forms.Maps.Position) 。 如需結構的詳細資訊 [`Position`](xref:Xamarin.Forms.Maps.Position) ，請參閱[地圖位置和距離](position-distance.md)。

> [!NOTE]
> 替代的地理編碼 API 是在中 avalible Xamarin.Essentials 。 Xamarin.Essentials `Geocoding` 此 api 會在地理編碼位址時提供結構化位址資料，而不是此 api 所傳回的字串。 如需詳細資訊，請參閱[ Xamarin.Essentials ：地理編碼](~/essentials/geocoding.md)。

## <a name="geocode-an-address"></a>地理編碼位址

藉由建立 [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) 實例並 [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) 在實例上呼叫方法，可以將街道位址地理編碼成緯度和經度座標 `Geocoder` ：

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

IEnumerable<Position> approximateLocations = await geoCoder.GetPositionsForAddressAsync("Pacific Ave, San Francisco, California");
Position position = approximateLocations.FirstOrDefault();
string coordinates = $"{position.Latitude}, {position.Longitude}";
```

[`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*)方法接受 `string` 代表位址的引數，並以非同步方式傳回 [`Position`](xref:Xamarin.Forms.Maps.Position) 可能代表位址的物件集合。

## <a name="reverse-geocode-an-address"></a>反向地理編碼位址

您可以藉由建立 [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) 實例並 [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) 在實例上呼叫方法，將緯度和經度座標反向地理編碼為街道位址 `Geocoder` ：

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

Position position = new Position(37.8044866, -122.4324132);
IEnumerable<string> possibleAddresses = await geoCoder.GetAddressesForPositionAsync(position);
string address = possibleAddresses.FirstOrDefault();
```

[`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*)方法會採用 [`Position`](xref:Xamarin.Forms.Maps.Position) 由緯度和經度座標組成的引數，並以非同步方式傳回字串的集合，這些字串表示位置附近的位址。

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Forms地圖位置和距離](position-distance.md)
- [Geocoder API](xref:Xamarin.Forms.Maps.Geocoder)
