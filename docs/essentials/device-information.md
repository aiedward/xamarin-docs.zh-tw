---
title: Xamarin.Essentials： 裝置資訊
description: 本文件說明 Xamarin.Essentials，可提供裝置的相關資訊上執行應用程式中的 DeviceInfo 類別。
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b7246afca19607ef2f70288d4643696f4ac35d52
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831483"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials： 裝置資訊

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**DeviceInfo**類別提供執行應用程式裝置的相關資訊。

## <a name="using-deviceinfo"></a>使用 DeviceInfo

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

透過 API 公開下列資訊：

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

`DeviceInfo.Platform` 相互關聯與對應至作業系統的常數字串。 值可以使用檢查`Platforms`類別：

- **DeviceInfo.Platforms.iOS** – iOS
- **DeviceInfo.Platforms.Android** -Android
- **DeviceInfo.Platforms.UWP** – UWP
- **DeviceInfo.Platforms.Unsupported** – 不支援

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[慣用語](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` 相互關聯的常數字串，對應至 裝置類型的應用程式上執行。 值可以使用檢查`Idioms`類別：

- **DeviceInfo.Idioms.Phone** – 電話
- **DeviceInfo.Idioms.Tablet** – 平板電腦
- **DeviceInfo.Idioms.Desktop** -桌面
- **DeviceInfo.Idioms.TV** – 電視
- **DeviceInfo.Idioms.Unsupported** – 不支援

## <a name="device-type"></a>裝置類型

`DeviceInfo.DeviceType` 列舉型別來判斷應用程式是否在實體上的執行或虛擬裝置會將相互關聯。 虛擬裝置是模擬器。

## <a name="api"></a>API

- [DeviceInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [DeviceInfo API 文件](xref:Xamarin.Essentials.DeviceInfo)
