---
title: Xamarin.Essentials 剪貼簿
description: 剪貼簿類別可讓您複製並貼到系統剪貼簿應用程式之間的文字。
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 218f90746f130f02c47040a9191b1001fa80c203
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
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

- [剪貼簿原始程式碼](https://github.com/xamarin/Essentials/tree/master/Essentials/Clipboard)
- [剪貼簿應用程式開發介面文件](xref:Xamarin.Essentials.Clipboard)
