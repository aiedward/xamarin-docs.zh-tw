---
ms.topic: include
ms.openlocfilehash: 5c11c94956f8d56c66c50a9a480177c5b77c2643
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947429"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[感應器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 以最快速度 （不保證會傳回在 UI 執行緒） 取得的感應器資料。
- **遊戲**– 評等適用於遊戲 （不保證會傳回在 UI 執行緒上）。
- **一般**– 預設速率適合螢幕方向的變更。
- **Ui** – 評等適用於一般使用者介面。

如果您的事件處理常式不保證會在 UI 執行緒上執行，以及事件處理常式需要存取使用者介面項目，如果使用[ `MainThread.BeginInvokeOnMainThread` ](~/essentials/main-thread.md) UI 執行緒上執行該程式碼的方法。