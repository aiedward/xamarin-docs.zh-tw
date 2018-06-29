---
title: Xamarin.Essentials： 電源能源保護裝置狀態
description: 電源類別可讓程式取得能源保護裝置狀態來判斷裝置是否在低電源模式下運作。
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: charlespetzold
ms.author: chape
ms.date: 06/27/2018
ms.openlocfilehash: 6d8ccb5be69eb1ea7ea63d3f5c373d9284089679
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080474"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials： 電源能源保護裝置狀態

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**電源**類別會提供裝置的能源保護裝置狀態，指出裝置是否正在執行中的低電源模式的相關資訊。 如果裝置的能源保護裝置狀態是 on，應用程式應該避免背景處理。

## <a name="background"></a>背景

以電池供電的裝置可置於低耗電能源電模式。 有時候裝置會切換成這種模式自動，比方說，電池低於 20%容量時。 作業系統會藉由減少通常會耗盡電池的活動回應能源電模式。 應用程式可以協助避免模式時，能源保護裝置上的背景處理或其他摦活動。

對於 Android 裝置，**電源**類別會傳回有意義的資訊僅適用於 Android 5.0 （棒棒糖符號） 和更新版本。

## <a name="using-the-power-class"></a>使用 Power 類別

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

取得目前能源保護裝置狀態的裝置使用靜態`Power.EnergySaverStatus`屬性：

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

這個屬性會傳回的成員`EnergySaverStatus`列舉型別，可能是`On`， `Off`，或`Unknown`。 如果屬性傳回`On`，應用程式應該避免背景處理或其他可能會耗用大量資源的活動。

應用程式也應該安裝的事件處理常式。 **電源**類別會公開能源保護裝置狀態變更時觸發的事件：

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Power.EnergySaverStatusChanaged += OnEnergySaverStatusChanaged;
    }

    private void OnEnergySaverStatusChanaged(EnergySaverStatusChanagedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

如果能源保護裝置狀態會變更為`On`，應用程式應該停止執行背景處理。 如果狀態變更為`Unknown`或`Off`，應用程式可以繼續背景處理。

## <a name="api"></a>API

- [電源原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [電源 API 文件](xref:Xamarin.Essentials.Power)
