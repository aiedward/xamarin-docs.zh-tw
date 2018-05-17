---
title: Xamarin.Essentials 裝置資訊
description: DeviceInfo 類別會提供執行應用程式之裝置的相關資訊。
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a398f2697b03cec26f2c786b11e7a3c332cb5f63
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials 裝置資訊

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**DeviceInfo**類別會提供執行應用程式之裝置的相關資訊。

## <a name="using-deviceinfo"></a>使用 DeviceInfo

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

透過應用程式開發介面公開下列資訊：

```csharp
// Device Model (SMG-950U)
var device = DeviceInfo.Model;

// Manufacturer (Samsung)
var manufacturer = DeviceInfo.Manufacturer;

// Device Name (Motz's iPhone)
var deviceName = DeviceInfo.Name;

// Operating System Version Number (7.0)
var version = DeviceInfo.VersionString;

// Platform (Android)
var platform = DeviceInfo.Platform;

// Idiom (Phone)
var idiom = DeviceInfo.Idiom;

// Device Type (Physical)
var deviceType = DeviceInfo.DeviceType;
```

## <a name="platformsxrefxamarinessentialsdeviceinfoplatforms"></a>[平台](xref:Xamarin.Essentials.DeviceInfo.Platforms)

`DeviceInfo.Platform` 相互關聯會對應至作業系統與常數字串。 值可以與簽入`Platforms`類別：

- **DeviceInfo.Platforms.iOS** – iOS
- **DeviceInfo.Platforms.Android** – Android
- **DeviceInfo.Platforms.UWP** – UWP
- **DeviceInfo.Platforms.Unsupported** – 不支援

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[慣用語](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` 相互關聯的常數字串，將應用程式對應至類型的裝置上正在執行。 值可以與簽入`Idioms`類別：

- **DeviceInfo.Idioms.Phone** – 電話
- **DeviceInfo.Idioms.Tablet** – 平板電腦
- **DeviceInfo.Idioms.Desktop** – 桌面
- **DeviceInfo.Idioms.TV** – 電視
- **DeviceInfo.Idioms.Unsupported** – 不支援

## <a name="device-type"></a>裝置類型

`DeviceInfo.DeviceType` 列舉型別來判斷應用程式是否執行下列項目的上實體或虛擬裝置將相互關聯。 模擬器虛擬裝置。

## <a name="api"></a>API

- [DeviceInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [DeviceInfo API 文件](xref:Xamarin.Essentials.DeviceInfo)
