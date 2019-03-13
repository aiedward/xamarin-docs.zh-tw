---
title: Xamarin.Essentials：地理編碼
description: Xamarin.Essentials 中的地理編碼類別會提供 API，既可以將地標進行地理編碼為位置座標，也可以將地理編碼座標反轉為地標。
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 0f44f179588c564ac9fa46d42ec18de6d303e239
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898975"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials：地理編碼

**地理編碼**類別會提供 API，可以將地標進行地理編碼為位置座標，也可以將地理編碼座標反轉為地標。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取**地理編碼**功能，需要下列平台特定設定。

# <a name="androidtabandroid"></a>[Android](#tab/android)

不需要進行額外設定。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

需要 Bing 地圖服務 API 金鑰，才能使用地理編碼功能。 註冊免費的 [Bing 地圖服務](https://www.bingmapsportal.com/)帳戶。 在 [我的帳戶 > 我的金鑰] 下方建立一個新金鑰，並根據您的應用程式類型填寫資訊 (針對 UWP 應用程式，應該是**公開 Windows 應用程式 (UWP、8.x 及更早版本)**)。

在呼叫任何 **Geocoding** 方法前，請在應用程式生命週期的早期設定 API 金鑰 (僅適用於 UWP)：

```csharp
Platform.MapServiceToken = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>使用地理編碼

在類別中新增對 Xamarin.Essentials 的參考：

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

取得一組現有座標的[地標](xref:Xamarin.Essentials.Placemark)：

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

[`Location`](xref:Xamarin.Essentials.Location) 和 [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) 類別會定義方法，以計算兩個位置之間的距離。 如需範例，請參閱 [**Xamarin.Essentials：地理位置**](geolocation.md#calculate-distance)一文。

## <a name="api"></a>API

- [地理編碼原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [地理編碼 API 文件](xref:Xamarin.Essentials.Geocoding)
