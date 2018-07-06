---
title: Xamarin.Essentials： 電池
description: 本文件說明 Xamarin.Essentials，可讓您檢查裝置的電池資訊和監視的變更中的電池類別。
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 1ed0ef5e013967545e739733c887702325f60c3f
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855051"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials： 電池

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**電池**類別可讓您檢查裝置的電池資訊和監視的變更。

## <a name="getting-started"></a>快速入門

若要存取**電池**須有下列的平台特定設定的功能。

# <a name="androidtabandroid"></a>[Android](#tab/android)

`Battery`權限是必要的而且必須設定 Android 專案中。 這可以透過下列方式新增：

開啟**AssemblyInfo.cs**下方的檔案**屬性**資料夾，並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Battery)]
```

或更新 Android 資訊清單：

開啟**AndroidManifest.xml**下方檔案**屬性**資料夾，並新增下列內**資訊清單**節點。

```xml
<uses-permission android:name="android.permission.BATTERY" />
```

或 Anroid 專案上按一下滑鼠右鍵，然後開啟專案的內容。 底下**Android 資訊清單**尋找**必要權限：** 區域，並檢查**電池**權限。 這樣會自動更新**AndroidManifest.xml**檔案。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要其他設定。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

不需要其他設定。

-----

## <a name="using-battery"></a>使用電池

在您的類別加入 Xamarin.Essentials 的參考：

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

每當任一電池的屬性變更時，會觸發事件：

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

# <a name="androidtabandroid"></a>[Android](#tab/android)

任何平台差異。

# <a name="iostabios"></a>[iOS](#tab/ios)

* 裝置必須用來測試 Api。 
* 只會傳回`Ac`或是`Battery`如`PowerSource`。 
* 無法取消震動。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* 只會傳回`Ac`或是`Battery`如`PowerSource`。 

-----

## <a name="api"></a>API

- [電池原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [電池 API 文件](xref:Xamarin.Essentials.Battery)
