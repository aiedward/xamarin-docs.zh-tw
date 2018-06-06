---
title: Xamarin.Essentials： 地理編碼
description: Xamarin.Essentials 中的地理編碼類別提供了 Api 這兩個 geocode placemark 位置座標，反轉 placemark geocode 座標。
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0b6cbf9ee5621466285656a5efee68ccc2c85211
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783024"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials： 地理編碼

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**地理編碼**類別提供了 Api geocode placemark 位置座標，反轉 placemark geocode coordincates。

## <a name="getting-started"></a>快速入門

若要存取**地理編碼**還需要下列平台的特定安裝程式的功能。

# <a name="androidtabandroid"></a>[Android](#tab/android)

不需要其他設定。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要其他設定。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

若要使用地理編碼 funcationality 需要 Bing 地圖服務 API 金鑰。 註冊免費的[Bing 地圖服務](https://www.bingmapsportal.com/)帳戶。 在下**我的帳戶 > 金鑰**建立新的金鑰，並填寫您的應用程式類型為基礎的資訊。

在您的應用程式生命週期之前呼叫任何**地理編碼**方法設定的 API 金鑰：

```csharp
Geocoding.MapKey = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>使用地理編碼

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

取得[位置](xref:Xamarin.Essentials.Location)座標地址：

```csharp
try
{
    var address =  "Microsoft Building 25 Redmond WA USA";
    var locations = await Geocoding.GetLocationsAsync(address);

    var location = locations?.FirstOrDefault();
    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occured in geocoding
}
```

取得[placemarks](xref:Xamarin.Essentials.Placemark)現有的座標集：

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

## <a name="api"></a>API

- [地理編碼原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [地理編碼 API 文件](xref:Xamarin.Essentials.Geocoding)
