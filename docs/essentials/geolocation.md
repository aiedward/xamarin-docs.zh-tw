---
title: Xamarin.Essentials 地理位置
description: 地理位置類別會提供 Api 來擷取的裝置目前的地理位置座標。
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 399dcd54d574875bcb5e491e87731b817e840e54
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials 地理編碼

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**地理位置**類別會提供 Api 來擷取的裝置目前的地理位置座標。

## <a name="getting-started"></a>快速入門

若要存取**地理位置**還需要下列平台的特定安裝程式的功能。

# <a name="androidtabandroid"></a>[Android](#tab/android)

粗糙和正常位置的權限所需，且必須加以設定的 Android 專案。 此外，如果您的應用程式的目標 Android 5.0 (API level 21) 或更高版本，您必須宣告，您的應用程式會使用的硬體功能資訊清單檔中。 這可以透過下列方式加入：

開啟**AssemblyInfo.cs**底下**屬性**資料夾並加入：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

或更新 Android 資訊清單：

開啟**AndroidManifest.xml**底下**屬性**資料夾，然後將下列內部**資訊清單**節點。

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

或 Anroid 專案上按一下滑鼠右鍵，然後開啟專案的屬性。 在下**Android 資訊清單**尋找**必要的權限：**區域，然後核取**ACCESS_COARSE_LOCATION**和**ACCESS_FINE_LOCATION**權限。 這會自動更新**AndroidManifest.xml**檔案。

# <a name="iostabios"></a>[iOS](#tab/ios)

您的應用程式必須要有索引鍵您**Info.plist**如 NSLocationWhenInUseUsageDescription 才能存取裝置的位置。

開啟 plist 編輯器，然後加入**隱私權-位置時在使用使用方式描述**屬性和值，以顯示使用者的填滿。

或以手動方式編輯檔案，並加入下列項目：

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access location when open.</string>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

您必須設定`Location`應用程式的權限。 這可藉由開啟**Package.appxmanifest**和 selecing**功能** 索引標籤，並檢查**位置**。

-----

## <a name="using-geolocation"></a>使用地理位置

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

Geoloation API 也會提示使用者輸入時所需的權限。

您可以取得上次已知[位置](xref:Xamarin.Essentials.Location)的裝置，藉由呼叫`GetLastKnownLocationAsync`方法。 這通常是較快執行完整的查詢，但較不精確。

```csharp
try
{
    var location = await Geolocation.GetLastKnownLocationAsync();

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

若要查詢目前的裝置[位置](xref:Xamarin.Essentials.Location)座標`GetLocationAsync`可用。 建議將完整`GeolocationRequest`和`CancellationToken`因為它可能需要一些時間才能取得裝置的位置。

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

## <a name="geolocation-accuracy"></a>地理位置精確度

下表摘要列出每個平台的精確度

### <a name="lowest"></a>最低

| 平台 | 距離 （以公尺為單位） |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1000-5000 |

### <a name="low"></a>低

| 平台 | 距離 （以公尺為單位） |
| --- | --- |
| Android | 500 |
| iOS | 1000 |
| UWP | 300-3000 |

### <a name="medium-default"></a>Medium （預設值）

| 平台 | 距離 （以公尺為單位） |
| --- | --- |
| Android | 100-500 |
| iOS | 100 |
| UWP | 30-500 |

### <a name="high"></a>High

| 平台 | 距離 （以公尺為單位） |
| --- | --- |
| Android | 0-100 |
| iOS | 10 |
| UWP | < = 10 |

### <a name="best"></a>最佳

| 平台 | 距離 （以公尺為單位） |
| --- | --- |
| Android | 0-100 |
| iOS | ~0 |
| UWP | < = 10 |

## <a name="api"></a>API

- [地理位置的原始程式碼](https://github.com/xamarin/Essentials/tree/master/Essentials/Geolocation)
- [地理位置應用程式開發介面文件](xref:Xamarin.Essentials.Geolocation)
