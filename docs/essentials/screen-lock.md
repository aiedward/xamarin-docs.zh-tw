---
title: Xamarin.Essentials 螢幕鎖定
description: 為了保持畫面從下降進入睡眠狀態，應用程式執行時，可以要求 ScreenLock 類別。
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0bdf75825d9c6dc594749fe7aa1e133207cfa0fa
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2018
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
        if (ScreenLock.IsActive)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease();
    }
}
```

## <a name="api"></a>API

- [畫面上鎖定原始碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [螢幕鎖定 API 文件](xref:Xamarin.Essentials.ScreenLock)
