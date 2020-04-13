---
title: Xamarin.Essentials：裝置資訊
description: 本文件描述 Xamarin.Essentials 中的 DeviceInfo 類別，可提供執行應用程式的裝置相關資訊。
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 1cab4ea8ea3f98def4830e101783db1554efa69c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78295413"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials：裝置資訊

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

## <a name="platforms"></a>平台

[`DeviceInfo.Platform`](xref:Xamarin.Essentials.DeviceInfo.Platform)與映射到作業系統的常量字串相關。 可以使用 `DevicePlatform` 結構來檢查值：

- **DevicePlatform.iOS**：iOS
- **DevicePlatform.Android**：Android
- **DevicePlatform.UWP**：UWP
- **DevicePlatform.Unknown**：未知

## <a name="idioms"></a>慣用語

[`DeviceInfo.Idiom`](xref:Xamarin.Essentials.DeviceInfo.Idiom)關聯一個常量字串,該字串映射到應用程式正在運行的設備類型。 可以使用 `DeviceIdiom` 結構來檢查值：

- **DeviceIdiom.Phone**：手機
- **DeviceIdiom.Tablet**：平板電腦
- **DeviceIdiom.Desktop**：電腦
- **DeviceIdiom.TV**：TV
- **DeviceIdiom.Watch**：手錶
- **DeviceIdiom.Unknown**：未知

## <a name="device-type"></a>裝置類型

`DeviceInfo.DeviceType` 與列舉相互關聯，以判斷應用程式是在實體裝置或虛擬裝置上執行。 虛擬裝置是模擬器 (Simulator) 還是模擬器 (Emulator)。

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="ios"></a>[iOS](#tab/ios)

iOS 不會公開開發人員獲取特定 iOS 設備的模型的 API。 相反,硬體標識符返回,如_iPhone10,6,_ 它指的是 iPhone X。這些標識符的映射不是由蘋果提供的,但可以在這些(非官方來源[)iPhone Wiki](https://www.theiphonewiki.com/wiki/Models)和[獲取iOS模型](https://github.com/dannycabrera/Get-iOS-Model)上找到。

--------------

## <a name="api"></a>API

- [DeviceInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [DeviceInfo API 文件](xref:Xamarin.Essentials.DeviceInfo)
