---
title: Xamarin.Essentials： 螢幕鎖定
description: 本文件說明在 Xamarin.Essentials，可以要求將進入睡眠狀態，應用程式執行時，防止螢幕 ScreenLock 類別。
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c8110b7abc86fe1d12485579f134997718540e6
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848566"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials： 螢幕鎖定

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**ScreenLock**類別可以要求將進入睡眠狀態，應用程式執行時，防止螢幕。

## <a name="using-screenlock"></a>使用 ScreenLock

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

畫面鎖定功能的運作方式是呼叫`RequestActive`和`RequestRelease`向關閉要求螢幕的方法。

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

- [畫面鎖定原始碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [畫面鎖定 API 文件](xref:Xamarin.Essentials.ScreenLock)
