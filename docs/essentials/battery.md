---
title: Xamarin.Essentials：電池
description: 本文件描述 Xamarin.Essentials 中的電池類別，可讓您檢查裝置的電池資訊並監視變更。
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6a14c939064538a405a1fe64061e0bb2e903fedd
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675428"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials：電池

![發行前的 NuGet](~/media/shared/pre-release.png)

**電池**類別可讓您檢查裝置的電池資訊並監視變更。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取**電池**功能，需要下列平台特定設定。

# <a name="androidtabandroid"></a>[Android](#tab/android)

需要 `Battery` 權限，而且必須在 Android 專案中設定。 能以下列方式新增：

開啟 [Properties] 資料夾下的 **AssemblyInfo.cs** 檔案並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

或更新 Android 資訊清單：

開啟 [Properties] 資料夾下的 **AndroidManifest.xml** 檔案並在 [manifest] 節點內新增下列內容。

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

禍以滑鼠右鍵按一 Android 專案並開啟專案的屬性。 在 [Android 資訊清單] 下，尋找 [必要權限] 區域並選取 [電池] 權限。 這將會自動更新 **AndroidManifest.xml** 檔案。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

不需要進行額外設定。

-----

## <a name="using-battery"></a>使用電池

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

檢查目前的電池資訊：

```csharp
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or -1.0 if unable to determine.

var state = Battery.State;

switch (state)
{
    case BatteryState.Charging:
        // Currently charging
        break;
    case BatteryState.Full:
        // Battery is full
        break;
    case BatteryState.Discharging:
    case BatteryState.NotCharging:
        // Currently discharging battery or not being charged
        break;
    case BatteryState.NotPresent:
        // Battery doesn't exist in device (desktop computer)
    case BatteryState.Unknown:
        // Unable to detect battery state
        break;
}

var source = Battery.PowerSource;

switch (source)
{
    case BatteryPowerSource.Battery:
        // Being powered by the battery
        break;
    case BatteryPowerSource.AC:
        // Being powered by A/C unit
        break;
    case BatteryPowerSource.Usb:
        // Being powered by USB cable
        break;
    case BatteryPowerSource.Wireless:
        // Powered via wireless charging
        break;
    case BatteryPowerSource.Unknown:
        // Unable to detect power source
        break;
}
```

每當任一電池的屬性變更時，會觸發事件：

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryChanged += Battery_BatteryChanged;
    }

    void Battery_BatteryChanged(object sender, BatteryChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

## <a name="platform-differences"></a>平台差異

# <a name="androidtabandroid"></a>[Android](#tab/android)

無平台差異。

# <a name="iostabios"></a>[iOS](#tab/ios)

* 裝置必須用來測試 API。 
* 針對 `PowerSource`，只會傳回 `AC` 或 `Battery`。
* 無法取消震動。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* 針對 `PowerSource`，只會傳回 `AC` 或 `Battery`。

-----

## <a name="api"></a>API

- [電池原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [電池 API 文件](xref:Xamarin.Essentials.Battery)
