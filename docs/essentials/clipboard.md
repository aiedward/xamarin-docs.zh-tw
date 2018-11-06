---
title: Xamarin.Essentials：剪貼簿
description: 本文件描述 Xamarin.Essentials 中的剪貼簿類別，可讓您複製文字並將其貼至應用程式之間的系統剪貼簿。
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 8dd238da678dfb5773801137d313b286590aa463
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675532"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials：剪貼簿

![發行前的 NuGet](~/media/shared/pre-release.png)

**剪貼簿**類別可讓您在應用程式之間複製文字，並貼到系統剪貼簿。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-clipboard"></a>使用 [剪貼簿]

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

檢查 [剪貼簿] 是否已具有要貼上的文字：

```csharp
var hasText = Clipboard.HasText;
```

將文字設定至 [剪貼簿]：

```csharp
Clipboard.SetText("Hello World");
```

從 [剪貼簿] 讀取文字：

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [剪貼簿原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [剪貼簿 API 文件](xref:Xamarin.Essentials.Clipboard)
