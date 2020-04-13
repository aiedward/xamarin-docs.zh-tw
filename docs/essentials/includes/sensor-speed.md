---
ms.topic: include
ms.openlocfilehash: b82ebeaeb28195e3ec0f5a0200c0448b6b76196a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "61259471"
---
## <a name="sensor-speed"></a>[感應器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快** – 以最快的方式取得感應器資料 (不保證在 UI 執行緒上傳回)。
- **遊戲** – 適合遊戲的費率 (不保證在 UI 執行緒上傳回)。
- **預設值**= 預設速率,適用於螢幕方向更改。
- **UI** – 適合一般使用者介面的費率。

如果不能保證事件處理程式在 UI 線程上運行,並且事件處理程式需要存取使用者介面元素[`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md),請使用方法在 UI 線程上運行該代碼。
