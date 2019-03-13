---
ms.topic: include
ms.openlocfilehash: e4dfd1ac12f3010939d483381a785091d71599ed
ms.sourcegitcommit: 676c5a6795ab4896ccd1b288424bf2040b1208aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2018
ms.locfileid: "52294807"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[感應器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快** – 以最快的方式取得感應器資料 (不保證在 UI 執行緒上傳回)。
- **遊戲** – 適合遊戲的費率 (不保證在 UI 執行緒上傳回)。
- **一般** – 適合螢幕方向變更的預設費率。
- **UI** – 適合一般使用者介面的費率。

若您的事件處理常式不保證在 UI 執行緒上執行，且若事件處理常式需要存取使用者介面元素，請使用 [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) 方法在 UI 執行緒上執行程式碼。