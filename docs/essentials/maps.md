---
title: Xamarin.Essentials Maps
description: Xamarin.Essentials 中的 Maps 類別可讓應用程式開啟已安裝的地圖應用程式，並開啟至特定位置或地標。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: ed5d909ae43ddb9e531851f5571f71517274db21
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123618"
---
# <a name="xamarinessentials-maps"></a>Xamarin.Essentials: Maps

![發行前的 NuGet](~/media/shared/pre-release.png)

**Maps** 類別可讓應用程式開啟已安裝的地圖應用程式，並開啟至特定位置或地標。

## <a name="using-maps"></a>使用 Maps

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

Maps 的運作方式是透過呼叫 `OpenAsync` 方法搭配 `Location` 或 `Placemark` 以開啟選擇性的 `MapsLaunchOptions`。

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(location, options);
    }
}
```

當開啟 `Placemark` 時，需要下列資訊：

- `CountryName`
- `AdminArea`
- `Thoroughfare`
- `Locality`

```csharp
public class MapsTest
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
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(placemark, options);
    }
}
```

## <a name="extension-methods"></a>擴充方法

如果您已經有 `Location` 或 `Placemark` 的參考，您可以使用內建擴充方法 `OpenMapsAsync` 搭配選擇性的 `MapsLaunchOptions`：

```csharp
public class MapsTest
{
    public async Task OpenPlacemarkOnMaps(Placemark placemark)
    {
        await placemark.OpenMapsAsync();
    }
}
```

## <a name="directions-mode"></a>路線模式

如果您呼叫 `OpenMapsAsync` 而不搭配任何 `MapsLaunchOptions`，地圖將會開啟至指定的位置。 您也可以選擇從裝置的目前位置計算導航路線。 這是透過設定 `MapsLaunchOptions` 上的 `MapDirectionsMode` 來完成的：

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapsLaunchOptions { MapDirectionsMode = MapDirectionsMode.Driving };

        await Maps.OpenAsync(location, options);
    }
}
```

## <a name="platform-differences"></a>平台差異

# <a name="androidtabandroid"></a>[Android](#tab/android)

- `MapDirectionsMode` 支援腳踏車、開車與走路。

# <a name="iostabios"></a>[iOS](#tab/ios)

- `MapDirectionsMode` 支援開車、大眾運輸與走路。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- `MapDirectionsMode` 支援開車、大眾運輸與走路。

--------------

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android 使用 `geo:` Uri 配置以啟動裝置上的地圖應用程式。 這可能會提示使用者從支援此 Uri 配置的現有應用程式中選取。  Xamarin.Essentials 已使用 Google 地圖測試，可支援此配置。

# <a name="iostabios"></a>[iOS](#tab/ios)

沒有平台特定實作詳細資料。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

沒有平台特定實作詳細資料。

--------------

## <a name="api"></a>API

- [Maps 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Maps)
- [Maps API 文件](xref:Xamarin.Essentials.Maps)
