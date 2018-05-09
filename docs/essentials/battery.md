---
title: Xamarin.Essentials 電池
description: 電池類別可讓您檢查裝置的電池資訊和監視的變更。
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 1420e95067c060991851aff9ef99a89ed89a8358
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials 電池

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**電池**類別可讓您檢查裝置的電池資訊和監視的變更。

## <a name="getting-started"></a>快速入門

若要存取**電池**還需要下列平台的特定安裝程式的功能。

# <a name="androidtabandroid"></a>[Android](#tab/android)

`Battery`需要權限，而且必須設定的 Android 專案中。 這可以透過下列方式加入：

開啟**AssemblyInfo.cs**底下**屬性**資料夾並加入：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Battery)]
```

或更新 Android 資訊清單：

開啟**AndroidManifest.xml**底下**屬性**資料夾，然後將下列內部**資訊清單**節點。

```xml
<uses-permission android:name="android.permission.BATTERY" />
```

或 Anroid 專案上按一下滑鼠右鍵，然後開啟專案的屬性。 在下**Android 資訊清單**尋找**必要的權限：**區域，然後核取**電池**權限。 這會自動更新**AndroidManifest.xml**檔案。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要其他設定。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

不需要其他設定。

-----

## <a name="using-battery"></a>使用電池

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

請檢查目前的電池資訊：

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
    case BatteryPowerSource.Ac:
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

每當任何電池的屬性變更事件，會觸發：

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryChanged += Battery_BatteryChanged;
    }

    void Battery_BatteryChanged(BatteryChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

## <a name="platform-differences"></a>平台差異

| 平台 | 差異 |
| --- | --- |
| iOS | 裝置必須用來測試應用程式開發介面。 |
| iOS | 只會傳回 Ac 或電池的 PowerSource。 |
| UWP | 只會傳回 Ac 或電池的 PowerSource。 |

## <a name="api"></a>API

- [電池原始程式碼](https://github.com/xamarin/Essentials/tree/master/Essentials/Battery)
- [電池 API 文件](xref:Xamarin.Essentials.Battery)
