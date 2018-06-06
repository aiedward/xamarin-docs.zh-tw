---
title: Xamarin.Essentials： 螢幕鎖定
description: 本文件說明 ScreenLock 中的類別 Xamarin.Essentials，為了保持畫面從下降處於睡眠模式時執行的應用程式可以要求。
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c8110b7abc86fe1d12485579f134997718540e6
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782904"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials： 螢幕鎖定

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
        if (!ScreenLock.IsActive)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease();
    }
}
```

## <a name="api"></a>API

- [畫面上鎖定原始碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [螢幕鎖定 API 文件](xref:Xamarin.Essentials.ScreenLock)
