---
title: Xamarin.Essentials Unit Converters (Xamarin.Essentials 單位轉換器)
description: Xamarin.Essentials 中的 UnitConverters 類別可提供數種單位轉換器，協助開發人員使用 Xamarin.Essentials。
ms.assetid: 35DE2704-E730-4337-9476-66CD53376943
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 23126359c3b5e1c7e3562177b82f12596d2893a4
ms.sourcegitcommit: 64d6da88bb6ba222ab2decd2fdc8e95d377438a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58176029"
---
# <a name="xamarinessentials-unit-converters"></a>Xamarin.Essentials:單位轉換器

**UnitConverters** 類別可提供數種單位轉換器，協助開發人員使用 Xamarin.Essentials。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-unit-converters"></a>使用單位轉換器

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

可利用 Xamarin.Essentials 中的靜態 `UnitConverters` 類別，使用全部的單位轉換器。 比方說，您可輕易地將華氏轉換為攝氏。

```csharp
var celcius = UnitConverters.FahrenheitToCelsius(32.0);
```

以下為可用的轉換清單：

* FahrenheitToCelsius
* CelsiusToFahrenheit
* CelsiusToKelvin
* KelvinToCelsius
* MilesToMeters
* MilesToKilometers
* KilometersToMiles
* DegreesToRadians
* RadiansToDegrees
* DegreesPerSecondToRadiansPerSecond
* RadiansPerSecondToDegreesPerSecond
* DegreesPerSecondToHertz
* RadiansPerSecondToHertz
* HertzToDegreesPerSecond
* HertzToRadiansPerSecond
* KilopascalsToHectopascals
* HectopascalsToKilopascals
* KilopascalsToPascals
* HectopascalsToPascals
* AtmospheresToPascals
* PascalsToAtmospheres
* CoordinatesToMiles
* CoordinatesToKilometers

## <a name="api"></a>API

- [單位轉換器原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/UnitConverters.shared.cs)
- [單位轉換器 API 文件](xref:Xamarin.Essentials.UnitConverters)
