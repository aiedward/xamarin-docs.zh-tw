---
title: Xamarin.Essentials： 剪貼簿
description: 本文件說明剪貼簿中的類別 Xamarin.Essentials，可讓您複製並貼到系統剪貼簿應用程式之間的文字。
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 41b15b480fa23bd49667b68e904043e4f1a95732
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782355"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials： 剪貼簿

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
Clipboard.SetText("Hello World");
```

若要讀取文字檔**剪貼簿**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [剪貼簿原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [剪貼簿應用程式開發介面文件](xref:Xamarin.Essentials.Clipboard)
