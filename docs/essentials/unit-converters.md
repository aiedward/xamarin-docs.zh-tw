---
title: Xamarin.Essentials Unit Converters (Xamarin.Essentials 單位轉換器)
description: Xamarin.Essentials 中的 UnitConverters 類別可提供數種單位轉換器，協助開發人員使用 Xamarin.Essentials。
ms.assetid: 35DE2704-E730-4337-9476-66CD53376943
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 866842cbed9f97dc957e3631c037fa8d27d20076
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83149776"
---
# <a name="xamarinessentials-unit-converters"></a>Xamarin. Essentials：單位轉換器

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
var celsius = UnitConverters.FahrenheitToCelsius(32.0);
```

以下為可用的轉換清單：

- FahrenheitToCelsius
- CelsiusToFahrenheit
- CelsiusToKelvin
- KelvinToCelsius
- MilesToMeters
- MilesToKilometers
- KilometersToMiles
- MetersToInternationalFeet
- InternationalFeetToMeters
- DegreesToRadians
- RadiansToDegrees
- DegreesPerSecondToRadiansPerSecond
- RadiansPerSecondToDegreesPerSecond
- DegreesPerSecondToHertz
- RadiansPerSecondToHertz
- HertzToDegreesPerSecond
- HertzToRadiansPerSecond
- KilopascalsToHectopascals
- HectopascalsToKilopascals
- KilopascalsToPascals
- HectopascalsToPascals
- AtmospheresToPascals
- PascalsToAtmospheres
- CoordinatesToMiles
- CoordinatesToKilometers
- KilogramsToPounds
- PoundsToKilograms
- StonesToPounds
- PoundsToStones

## <a name="api"></a>API

- [單位轉換器原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/UnitConverters.shared.cs)
- [單位轉換器 API 文件](xref:Xamarin.Essentials.UnitConverters)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Unit-Conversion-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
