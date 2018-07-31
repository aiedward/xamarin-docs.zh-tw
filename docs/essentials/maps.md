---
title: Xamarin.Essentials 對應
description: Xamarin.Essentials 中的對應類別可讓應用程式開啟至特定位置或 placemark 已安裝之地圖集應用程式。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 445e2da84e9a9aaf1ce4d836af11cfba963b8cbb
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353934"
---
# <a name="xamarinessentials-maps"></a>Xamarin.Essentials： 對應

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**對應**類別可讓應用程式開啟至特定位置或 placemark 已安裝之地圖集應用程式。

## <a name="using-maps"></a>使用對應

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

對應功能的運作方式是呼叫`OpenAsync`方法`Location`或是`Placemark`若要開啟包含選擇性`MapsLaunchOptions`。

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

當開啟`Placemark`提供下列資訊：

* `CountryName`
* `AdminArea`
* `Thoroughfare`
* `Locality`

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

如果您已經有的參考`Location`或是`Placemark`您可以使用內建的擴充方法`OpenMapsAsync`具選擇性`MapsLaunchOptions`:

```csharp
public class MapsTest
{
    public async Task OpenPlacemarkOnMaps(Placemark placemark)
    {
        await placemark.OpenMapsAsync();
    }
}
```

## <a name="platform-differences"></a>平台差異

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `MapDirectionsMode` 不支援，而且沒有任何作用。

# <a name="iostabios"></a>[iOS](#tab/ios)

* `MapDirectionsMode` 若要開啟對應的應用程式時設定的預設方向模式支援。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `MapDirectionsMode` 不支援，而且沒有任何作用。

--------------

## <a name="platform-implementation-specifics"></a>平台實作的特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android 使用`geo:`啟動對應應用程式在裝置上的 Uri 配置。 這可能會提示使用者選取現有的應用程式支援這種 Uri 配置。  Xamarin.Essentials 會測試與 Google Maps 支援這種配置。

# <a name="iostabios"></a>[iOS](#tab/ios)

任何平台特定實作詳細資料。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

任何平台特定實作詳細資料。

--------------

## <a name="api"></a>API

- [對應原始程式碼。](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Maps)
- [地圖服務 API 文件](xref:Xamarin.Essentials.Maps)
