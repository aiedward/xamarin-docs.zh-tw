---
title: Xamarin.Essentials：地理編碼
description: 中的地理編碼類別 Xamarin.Essentials 提供 api，可將地標地理編碼至位置座標，並將地理編碼座標轉換成地標。
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 66383e441435b5f4bdb48224c9ab602f28072b3d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802330"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials：地理編碼

**地理編碼**類別會提供 API，可以將地標進行地理編碼為位置座標，也可以將地理編碼座標反轉為地標。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取**地理編碼**功能，需要下列平台特定設定。

# <a name="android"></a>[Android](#tab/android)

不需要進行額外設定。

# <a name="ios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwp"></a>[UWP](#tab/uwp)

需要 Bing 地圖服務 API 金鑰，才能使用地理編碼功能。 註冊免費的 [Bing 地圖服務](https://www.bingmapsportal.com/)帳戶。 在 [我的帳戶 > 我的金鑰]**** 下方建立一個新金鑰，並根據您的應用程式類型填寫資訊 (針對 UWP 應用程式，應該是**公開 Windows 應用程式 (UWP、8.x 及更早版本)**)。

在呼叫任何 **Geocoding** 方法前，請在應用程式生命週期的早期設定 API 金鑰 (僅適用於 UWP)：

```csharp
Platform.MapServiceToken = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>使用地理編碼

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

取得地址的[位置](xref:Xamarin.Essentials.Location)座標：

```csharp
try
{
    var address =  "Microsoft Building 25 Redmond WA USA";
    var locations = await Geocoding.GetLocationsAsync(address);

    var location = locations?.FirstOrDefault();
    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

高度不一定可用。 如果不可用，則 `Altitude` 屬性可能為 `null`，或值可能為零。 如可使用高度，此值 (公尺) 會高於海平面。

## <a name="using-reverse-geocoding"></a>使用反向地理編碼

反向地理編碼是為一組現有座標集取得[地標](xref:Xamarin.Essentials.Placemark)的流程：

```csharp
try
{
    var lat = 47.673988;
    var lon = -122.121513;

    var placemarks = await Geocoding.GetPlacemarksAsync(lat, lon);

    var placemark = placemarks?.FirstOrDefault();
    if (placemark != null)
    {
        var geocodeAddress =
            $"AdminArea:       {placemark.AdminArea}\n" +
            $"CountryCode:     {placemark.CountryCode}\n" +
            $"CountryName:     {placemark.CountryName}\n" +
            $"FeatureName:     {placemark.FeatureName}\n" +
            $"Locality:        {placemark.Locality}\n" +
            $"PostalCode:      {placemark.PostalCode}\n" +
            $"SubAdminArea:    {placemark.SubAdminArea}\n" +
            $"SubLocality:     {placemark.SubLocality}\n" +
            $"SubThoroughfare: {placemark.SubThoroughfare}\n" +
            $"Thoroughfare:    {placemark.Thoroughfare}\n";

        Console.WriteLine(geocodeAddress);
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

## <a name="distance-between-two-locations"></a>兩個位置之間的距離

[`Location`](xref:Xamarin.Essentials.Location)和 [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) 類別會定義方法，以計算兩個位置之間的距離。 如需範例，請參閱文章[** Xamarin.Essentials ：地理位置**](geolocation.md#calculate-distance)。

## <a name="api"></a>API

- [地理編碼原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Geocoding)
- [地理編碼 API 文件](xref:Xamarin.Essentials.Geocoding)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Geocoding-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
