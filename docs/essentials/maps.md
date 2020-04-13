---
title: Xamarin.Essentials Map
description: Xamarin.Essentials 中的 Maps 類別可讓應用程式將已安裝的地圖應用程式開啟至特定位置或地標。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
ms.openlocfilehash: c0875534d88ea5b66b3072c35b9d38894fe98934
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "61354567"
---
# <a name="xamarinessentials-map"></a>Xamarin.Essentials：Map

**Maps** 類別可讓應用程式將已安裝的地圖應用程式開啟至特定位置或地標。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-map"></a>使用 Map

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

Maps 的運作方式是使用 `Location` 或 `Placemark` 呼叫 `OpenAsync` 方法，來使用選擇性的 `MapLaunchOptions` 開啟。

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        await Map.OpenAsync(location, options);
    }
}
```

當開啟 `Placemark` 時，需要下列資訊：

- `CountryName`
- `AdminArea`
- `Thoroughfare`
- `Locality`

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var placemark = new Placemark
            {
                CountryName = "United States",
                AdminArea = "WA",
                Thoroughfare = "Microsoft Building 25",
                Locality = "Redmond"
            };
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        await Map.OpenAsync(placemark, options);
    }
}
```

## <a name="extension-methods"></a>擴充方法

如果您已經有 `Location` 或 `Placemark` 的參考，您可以使用內建擴充方法 `OpenMapAsync` 搭配選擇性的 `MapLaunchOptions`：

```csharp
public class MapTest
{
    public async Task OpenPlacemarkOnMap(Placemark placemark)
    {
        await placemark.OpenMapAsync();
    }
}
```

## <a name="directions-mode"></a>路線模式

如果您呼叫 `OpenMapAsync` 而不搭配任何 `MapLaunchOptions`，地圖將會開啟至指定的位置。 您也可以選擇從裝置的目前位置計算導航路線。 這是透過設定 `MapLaunchOptions` 上的 `NavigationMode` 來完成的：

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { NavigationMode = NavigationMode.Driving };

        await Map.OpenAsync(location, options);
    }
}
```

## <a name="platform-differences"></a>平台差異

# <a name="android"></a>[Android](#tab/android)

- `NavigationMode` 支援腳踏車、開車與走路。

# <a name="ios"></a>[iOS](#tab/ios)

- `NavigationMode` 支援開車、大眾運輸與走路。

# <a name="uwp"></a>[UWP](#tab/uwp)

- `NavigationMode` 支援開車、大眾運輸與走路。

--------------

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="android"></a>[Android](#tab/android)

Android 使用 `geo:` Uri 配置以啟動裝置上的地圖應用程式。 這可能會提示使用者從支援此 Uri 配置的現有應用程式中選取。  Xamarin.Essentials 已使用 Google 地圖測試，可支援此配置。

# <a name="ios"></a>[iOS](#tab/ios)

沒有平台特定實作詳細資料。

# <a name="uwp"></a>[UWP](#tab/uwp)

沒有平台特定實作詳細資料。

--------------

## <a name="api"></a>API

- [Map 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Map)
- [Map API 文件](xref:Xamarin.Essentials.Map)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Maps-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
