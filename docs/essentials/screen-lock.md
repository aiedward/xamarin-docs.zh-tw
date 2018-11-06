---
title: Xamarin.Essentials：畫面鎖定
description: 本文件描述 Xamarin.Essentials 中的 ScreenLock 類別，可以要求在應用程式執行時防止畫面進入睡眠狀態。
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3bf8c949650cf9f039a5a516366a90e717dc944b
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675311"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials：畫面鎖定

![發行前的 NuGet](~/media/shared/pre-release.png)

**ScreenLock** 類別可以要求在應用程式執行時防止畫面進入睡眠狀態。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-screenlock"></a>使用 ScreenLock

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

畫面鎖定功能的運作方式是透過呼叫 `RequestActive` 和 `RequestRelease` 方法來要求不關閉畫面。

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
