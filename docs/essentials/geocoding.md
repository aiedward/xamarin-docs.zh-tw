---
title: Xamarin.Essentials： 地理編碼
description: Xamarin.Essentials 中的地理編碼類別提供了 Api 這兩個地理編碼為位置的座標 placemark 和 placemark 反轉地理編碼的座標。
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a4d6e4d9b32e665893d82693a3c858630b63d372
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353671"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials： 地理編碼

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**地理編碼**類別提供的 Api 以地理編碼位置的座標 placemark 和 placemark 反轉地理編碼的座標。

## <a name="getting-started"></a>快速入門

若要存取**地理編碼**須有下列的平台特定設定的功能。

# <a name="androidtabandroid"></a>[Android](#tab/android)

不需要其他設定。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要其他設定。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Bing 地圖服務 API 金鑰，才能使用地理編碼功能。 免費註冊[Bing 地圖服務](https://www.bingmapsportal.com/)帳戶。 底下**我的帳戶 > 我的金鑰**建立新的金鑰，並填妥 根據您的應用程式類型的資訊 (應該**公開的 Windows 應用程式 (UWP，8.x 和更早版本)** UWP 應用程式)。

早在您的應用程式生命週期，然後再呼叫任何**地理編碼**方法設定 API 金鑰：

```csharp
Geocoding.MapKey = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>使用地理編碼

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

取得[位置](xref:Xamarin.Essentials.Location)地址的座標：

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

高度不一定可用。 如果不是的話`Altitude`屬性可能是`null`值可能為零。 高度可用，如果值是以公尺為單位以上之上。

取得[placemarks](xref:Xamarin.Essentials.Placemark)的一組現有的座標：

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

[ `Location` ](xref:Xamarin.Essentials.Location)並[ `LocationExtensions` ](xref:Xamarin.Essentials.LocationExtensions)類別定義來計算兩個位置之間的距離的方法。 請參閱文章[ **Xamarin.Essentials： 地理位置**](geolocation.md#calculate-distance)的範例。

## <a name="api"></a>API

- [地理編碼的原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [地理編碼 API 文件](xref:Xamarin.Essentials.Geocoding)
