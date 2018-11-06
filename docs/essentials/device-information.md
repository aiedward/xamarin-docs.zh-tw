---
title: Xamarin.Essentials：裝置資訊
description: 本文件描述 Xamarin.Essentials 中的 DeviceInfo 類別，可提供執行應用程式的裝置相關資訊。
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 530b04446703d78452357b2c9f9089e59ebf6e6c
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674809"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials：裝置資訊

![發行前的 NuGet](~/media/shared/pre-release.png)

**DeviceInfo** 類別可提供執行應用程式的裝置相關資訊。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-deviceinfo"></a>使用 deviceInfo

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

下列資訊會透過 API 公開：

```csharp
// Device Model (SMG-950U, iPhone10,6)
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

`DeviceInfo.Platform` 與對應至作業系統的常數字串相互關聯。 可以使用 `Platforms` 類別來檢查值：

- **DeviceInfo.Platforms.iOS** - iOS
- **DeviceInfo.Platforms.Android** - Android
- **DeviceInfo.Platforms.UWP** - UWP
- **DeviceInfo.Platforms.Unsupported** – 不支援

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[慣用語](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` 與對應至執行應用程式之裝置類型的常數字串相互關聯。 可以使用 `Idioms` 類別來檢查值：

- **DeviceInfo.Idioms.Phone** – 手機
- **DeviceInfo.Idioms.Tablet** – 平板電腦
- **DeviceInfo.Idioms.Desktop** – 電腦
- **DeviceInfo.Idioms.TV** – 電視
- **DeviceInfo.Idioms.Unsupported** - 不支援

## <a name="device-type"></a>裝置類型

`DeviceInfo.DeviceType` 與列舉相互關聯，以判斷應用程式是在實體裝置或虛擬裝置上執行。 虛擬裝置是模擬器 (Simulator) 還是模擬器 (Emulator)。

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="iostabios"></a>[iOS](#tab/ios)

iOS 不會為開發人員公開 API 以取得特定 iOS 裝置的名稱。 而會傳回硬體識別碼，例如代表 iPhone X 的 _iPhone10,6_。這些識別碼的對應不由 Apple 提供，但可以在 [iPhone Wiki](https://www.theiphonewiki.com/wiki/Models) 上找到 (非官方來源)。

--------------

## <a name="api"></a>API

- [DeviceInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [DeviceInfo API 文件](xref:Xamarin.Essentials.DeviceInfo)
