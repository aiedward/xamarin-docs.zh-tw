---
title: Xamarin.Essentials： 剪貼簿
description: 本文件說明剪貼簿中的類別 Xamarin.Essentials，讓您複製和貼上到系統剪貼簿在應用程式之間的文字。
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 41b15b480fa23bd49667b68e904043e4f1a95732
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38842611"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials： 剪貼簿

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**剪貼簿**類別可讓您複製並貼到系統剪貼簿在應用程式之間的文字。

## <a name="using-clipboard"></a>使用剪貼簿

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

若要檢查是否**剪貼簿**具有目前準備好要貼上的文字：

```csharp
var hasText = Clipboard.HasText;
```

若要將文字設定為**剪貼簿**:

```csharp
Clipboard.SetText("Hello World");
```

若要讀取文字**剪貼簿**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [剪貼簿的原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [剪貼簿 API 文件](xref:Xamarin.Essentials.Clipboard)
