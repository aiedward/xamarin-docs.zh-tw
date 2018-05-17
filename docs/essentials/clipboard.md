---
title: Xamarin.Essentials 剪貼簿
description: 剪貼簿類別可讓您複製並貼到系統剪貼簿應用程式之間的文字。
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 67a0218325918b57e5ed2618b57d52d3fe3ee820
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials 剪貼簿

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**剪貼簿**類別可讓您複製並貼到系統剪貼簿應用程式之間的文字。

## <a name="using-clipboard"></a>使用剪貼簿

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

若要檢查如果**剪貼簿**目前準備好要貼上的文字：

```csharp
var hasText = Clipboard.HasText;
```

若要設定文字**剪貼簿**:

```csharp
ClipBoard.SetText("Hello World");
```

若要讀取文字檔**剪貼簿**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [剪貼簿原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [剪貼簿應用程式開發介面文件](xref:Xamarin.Essentials.Clipboard)
