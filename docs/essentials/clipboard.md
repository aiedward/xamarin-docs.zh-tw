---
title: Xamarin.Essentials:剪貼簿
description: 本文件描述 Xamarin.Essentials 中的剪貼簿類別，可讓您複製文字並將其貼至應用程式之間的系統剪貼簿。
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 02/12/2019
ms.custom: video
ms.openlocfilehash: c186f5c61bd2fa3df305be92a03135e57e302d02
ms.sourcegitcommit: 6e04246207aa743820029e8c217a43cfdd24f991
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2019
ms.locfileid: "67352122"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials:剪貼簿

**剪貼簿**類別可讓您在應用程式之間複製文字，並貼到系統剪貼簿。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-clipboard"></a>使用 [剪貼簿]

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

檢查 [剪貼簿]  是否已具有要貼上的文字：

```csharp
var hasText = Clipboard.HasText;
```

將文字設定至 [剪貼簿]  ：

```csharp
await Clipboard.SetTextAsync("Hello World");
```

從 [剪貼簿]  讀取文字：

```csharp
var text = await Clipboard.GetTextAsync();
```

> [!TIP]
> 對剪貼簿的存取必須在主要使用者介面執行緒上完成。 請參閱 [MainThread](~/essentials/main-thread.md) API 以了解如何叫用主要使用者介面執行緒上的方法。

## <a name="api"></a>API

- [剪貼簿原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [剪貼簿 API 文件](xref:Xamarin.Essentials.Clipboard)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Clipboard-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
