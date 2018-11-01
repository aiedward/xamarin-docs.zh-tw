---
title: 'Xamarin.Essentials: Power Energy Saver Status'
description: Power 類別可讓程式取得省電狀態以判斷裝置是否在低電源狀態運作。
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: jamesmontemagno
ms.author: jamont
ms.date: 06/27/2018
ms.openlocfilehash: 5a89dba16a93b007c5d7312221d8d33e00c7404a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110000"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials: Power Energy Saver Status

![發行前的 NuGet](~/media/shared/pre-release.png)

**Power** 類別提供有關裝置省電狀態的資訊， 此狀態指出裝置是否在低電源狀態運作。 若裝置已開啟低電源狀態，應用程式應該避免背景處理。

## <a name="background"></a>背景

以電池電力運作的裝置可能會進入低電源省電狀態。 有時候裝置會自動切換到此狀態，例如當電池電力低於總容量的 20% 時。 針對省電模式，作業系統會減少可能會耗電的活動。 當進入省電模式時，應用程式也可以透過避免進行背景處理或執行其他耗電量大的活動，以降低耗電量。

針對 Android 裝置，**Power** 類別只會針對 Android 5.0 版 (Lollipop) 與更高版本傳回有意義的資訊。

## <a name="using-the-power-class"></a>使用 Power 類別

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

使用靜態 `Power.EnergySaverStatus` 屬性取得裝置的目前省電狀態：

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

此屬性會傳回 `EnergySaverStatus` 列舉的成員，亦即 `On`, `Off` 或 `Unknown`。 若屬性傳回 `On`，應用程式應該避免進行背景處理或執行其他耗電量大的活動。

應用程式也應該安裝事件處理常式。 **Power** 類別公開一個事件，當省電狀態變更時會觸發此事件：

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Power.EnergySaverStatusChanged += OnEnergySaverStatusChanged;
    }

    private void OnEnergySaverStatusChanged(EnergySaverStatusChangedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

若省電狀態變更為 `On`，應用程式應該停止執行背景處理。 若狀態變更為 `Unknown` 或 `Off`，應用程式可以繼續進行背景處理。

## <a name="api"></a>API

- [Power 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Power API 文件](xref:Xamarin.Essentials.Power)
