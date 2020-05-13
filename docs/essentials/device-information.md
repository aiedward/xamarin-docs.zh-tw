---
title: Xamarin.Essentials：裝置資訊
description: 本文件描述 Xamarin.Essentials 中的 DeviceInfo 類別，可提供執行應用程式的裝置相關資訊。
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 1790e950dfccddcca84adf97bcff64f905ee59f6
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83150081"
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

[`DeviceInfo.Platform`](xref:Xamarin.Essentials.DeviceInfo.Platform)與對應至作業系統的常數位串相互關聯。 可以使用 `DevicePlatform` 結構來檢查值：

- **DevicePlatform.iOS**：iOS
- **DevicePlatform.Android**：Android
- **DevicePlatform.UWP**：UWP
- **DevicePlatform.Unknown**：未知

## <a name="idioms"></a>慣用語

[`DeviceInfo.Idiom`](xref:Xamarin.Essentials.DeviceInfo.Idiom)將對應至執行應用程式之裝置類型的常數位串相互關聯。 可以使用 `DeviceIdiom` 結構來檢查值：

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

iOS 不會為開發人員公開 API 以取得特定 iOS 裝置的型號。 相反地，會傳回硬體識別碼（例如_代表 iphone10，6），_ 這是指 iPhone X。Apple 不會提供這些識別碼的對應，但可以在[IPhone Wiki 的](https://www.theiphonewiki.com/wiki/Models)這些（非官方來源）上找到，並[取得 iOS 模型](https://github.com/dannycabrera/Get-iOS-Model)。

--------------

## <a name="api"></a>API

- [DeviceInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [DeviceInfo API 文件](xref:Xamarin.Essentials.DeviceInfo)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Device-Information-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
