---
title: Xamarin.Essentials 螢幕鎖定
description: 為了保持畫面從下降進入睡眠狀態，應用程式執行時，可以要求 ScreenLock 類別。
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7175362dcb7f85746ea85447936d7fe3e2fd026b
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials 螢幕鎖定

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**ScreenLock**類別可以為了保持畫面從下降處於睡眠模式時執行的應用程式要求。

## <a name="using-screenlock"></a>使用 ScreenLock

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

螢幕鎖定功能的運作方式是呼叫`RequestActive`和`RequestRelease`向關閉要求螢幕的方法。

```csharp
public class ScreenLockTest
{
    public void ToggleScreenLock()
    {
        if (ScreenLock.IsMonitoring)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease;
    }
}
```

## <a name="api"></a>API

- [畫面上鎖定原始碼](https://github.com/xamarin/Essentials/tree/master/Essentials/ScreenLock)
- [螢幕鎖定 API 文件](xref:Xamarin.Essentials.ScreenLock)
