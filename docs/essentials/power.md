---
title: Xamarin.Essentials： 電源能源省電狀態
description: 電源類別可讓程式取得能源省電狀態來判斷裝置是否在低電源模式下運作。
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: charlespetzold
ms.author: chape
ms.date: 06/27/2018
ms.openlocfilehash: 6d8ccb5be69eb1ea7ea63d3f5c373d9284089679
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831516"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials： 電源能源省電狀態

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**電源**類別會提供裝置的能源省電狀態，這表示是否裝置正在執行中的低電源模式的相關資訊。 如果裝置的能源省電狀態是 on，應用程式應該避免背景處理。

## <a name="background"></a>背景

電池執行的裝置可置於低耗電能源省電模式。 有時候裝置會進入此模式時自動切換，比方說，電池低於 20%的容量。 作業系統會藉由減少傾向於耗盡電池的活動回應能源省電模式。 應用程式可以協助避免能源省電模式下開啟時的背景處理或其他高功率的活動。

適用於 Android 的裝置**電源**類別會傳回有意義的資訊只用於 Android 5.0 (Lollipop) 或更新版本。

## <a name="using-the-power-class"></a>使用 Power 類別

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

取得目前的裝置使用靜態的能源省電狀態`Power.EnergySaverStatus`屬性：

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

這個屬性會傳回的成員`EnergySaverStatus`列舉型別，也就是其中一個`On`， `Off`，或`Unknown`。 如果屬性傳回`On`，應用程式應該避免背景處理或其他可能會耗用大量電源的活動。

應用程式也應該安裝的事件處理常式。 **電源**類別會公開能源省電狀態變更時觸發的事件：

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

如果能源省電狀態會變更為`On`，應用程式應該停止執行背景處理。 如果狀態會變成`Unknown`或`Off`，應用程式可以繼續背景處理。

## <a name="api"></a>API

- [電源來源的程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Power API 文件](xref:Xamarin.Essentials.Power)
