---
title: Xamarin.Essentials單位轉換器
description: 中的 UnitConverters 類別 Xamarin.Essentials 提供數個單位轉換器，可協助開發人員使用 Xamarin.Essentials 。
ms.assetid: 35DE2704-E730-4337-9476-66CD53376943
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 01/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 332762a83796fef24278c6685a29f7d31a10dadc
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84801851"
---
# <a name="xamarinessentials-unit-converters"></a>Xamarin.Essentials：單位轉換器

**UnitConverters**類別提供數個單位轉換器，可協助開發人員使用 Xamarin.Essentials 。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-unit-converters"></a>使用單位轉換器

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

您可以使用中的靜態類別，取得所有單位轉換器 `UnitConverters` Xamarin.Essentials 。 比方說，您可輕易地將華氏轉換為攝氏。

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

- [單位轉換器原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Types/UnitConverters.shared.cs)
- [單位轉換器 API 文件](xref:Xamarin.Essentials.UnitConverters)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Unit-Conversion-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
