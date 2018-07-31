---
ms.topic: include
ms.openlocfilehash: e4dfd1ac12f3010939d483381a785091d71599ed
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353265"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[感應器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 以最快速度 （不保證會傳回在 UI 執行緒） 取得的感應器資料。
- **遊戲**– 評等適用於遊戲 （不保證會傳回在 UI 執行緒上）。
- **一般**– 預設速率適合螢幕方向的變更。
- **UI** – 評等適用於一般使用者介面。

如果您的事件處理常式不保證會在 UI 執行緒上執行，以及事件處理常式需要存取使用者介面項目，如果使用[ `MainThread.BeginInvokeOnMainThread` ](~/essentials/main-thread.md) UI 執行緒上執行該程式碼的方法。