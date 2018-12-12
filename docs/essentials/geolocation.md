---
title: Xamarin.Essentials：地理位置
description: 本文件描述 Xamarin.Essentials 中的地理位置類別，可提供用於擷取裝置目前地理位置座標的 API。
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 651da789dcd87810ef6650d87fc2df4f91ce58e1
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898923"
---
# <a name="xamarinessentials-geolocation"></a>Xamarin.Essentials：地理位置

**地理位置**類別會提供 API 來擷取裝置的目前地理位置座標。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取**地理位置**功能，需要下列平台特定設定：

# <a name="androidtabandroid"></a>[Android](#tab/android)

需要粗略和精確位置的權限，並且必須在 Android 專案中設定。 此外，如果您的應用程式針對 Android 5.0 (API 層級 21) 或更新版，則必須宣告您應用程式使用資訊清單	檔案中的硬體功能。 能以下列方式新增：

開啟 [Properties] 資料夾下的 **AssemblyInfo.cs** 檔案並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

或更新 Android 資訊清單：

開啟 [Properties] 資料夾下的 **AndroidManifest.xml** 檔案並在 [manifest] 節點內新增下列內容：

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

或以滑鼠右鍵按一下 Android 專案並開啟專案的屬性。 在 [Android 資訊清單] 下，尋找 [必要權限] 區域並選取 **ACCESS_COARSE_LOCATION** 和 **ACCESS_FINE_LOCATION** 權限。 這將會自動更新 **AndroidManifest.xml** 檔案。

# <a name="iostabios"></a>[iOS](#tab/ios)

您應用程式的 **Info.plist** 必須包含 `NSLocationWhenInUseUsageDescription` 鍵，才能存取裝置的位置。

開啟 plist 編輯器並新增 **Privacy - Location When In Use Usage Description** 屬性，並填寫一個值以顯示使用者。

或手動編輯檔案並新增下列內容：

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access location when open.</string>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

您必須為應用程式設定 `Location` 權限。 可以透過開啟 **Package.appxmanifest**、選取 [功能] 索引標籤，並選取 [位置] 來完成。

-----

## <a name="using-geolocation"></a>使用地理位置

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

地理位置 API 也會在必要時，提示使用者提供權限。

您可以透過呼叫 `GetLastKnownLocationAsync` 方法取得裝置的最後一個已知[位置](xref:Xamarin.Essentials.Location)。 這通常比執行完整查詢更快，但較不精確。

```csharp
try
{
    var location = await Geolocation.GetLastKnownLocationAsync();

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
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

高度不一定可用。 如果不可用，則 `Altitude` 屬性可能為 `null`，或值可能為零。 如可使用高度，此值 (公尺) 會高於海平面。 

若要查詢目前裝置的[位置](xref:Xamarin.Essentials.Location)座標，可以使用 `GetLocationAsync`。 建議您傳入完整的 `GeolocationRequest` 和 `CancellationToken`，因為可能需要一些時間才能取得裝置的位置。

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
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

下表概述每個平台的精確度：

### <a name="lowest"></a>最低

| Platform | 距離 (以公尺為單位) |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1000 - 5000 |

### <a name="low"></a>低

| Platform | 距離 (以公尺為單位) |
| --- | --- |
| Android | 500 |
| iOS | 1000 |
| UWP | 300 - 3000 |

### <a name="medium-default"></a>中 (預設)

| Platform | 距離 (以公尺為單位) |
| --- | --- |
| Android | 100 - 500 |
| iOS | 100 |
| UWP | 30-500 |

### <a name="high"></a>High

| Platform | 距離 (以公尺為單位) |
| --- | --- |
| Android | 0 - 100 |
| iOS | 10 |
| UWP | <= 10 |

### <a name="best"></a>最佳

| Platform | 距離 (以公尺為單位) |
| --- | --- |
| Android | 0 - 100 |
| iOS | ~0 |
| UWP | <= 10 |

<a name="calculate-distance" />

## <a name="distance-between-two-locations"></a>兩個位置之間的距離

[`Location`](xref:Xamarin.Essentials.Location) 和 [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) 類別會定義 `CalculateDistance` 方法，可讓您計算兩個地理位置之間的距離。 此計算出的距離不會考慮道路或其他路徑，而僅僅是沿著地球表面兩個點之間的最短距離，也稱為「大圓距離」；或口語化說法：「直線」的距離。

以下為範例：

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnit.Miles);
```

`Location` 建構函式在該順序中具有緯度與經度的引數。 正緯度值位於赤道以北，正經度值位於本初子午線以東。 使用 `CalculateDistance` 的最後引數來指定英里或公里。 `Location` 類別也會定義在兩個單位之間進行轉換的 `KilometersToMiles` 和 `MilesToKilometers` 方法。

## <a name="api"></a>API

- [地理位置原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geolocation)
- [地理位置 API 文件](xref:Xamarin.Essentials.Geolocation)
