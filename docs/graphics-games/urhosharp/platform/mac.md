---
title: UrhoSharp Mac 支援
description: Mac 特定設定和 UrhoSharp 的功能。
ms.prod: xamarin
ms.assetid: 95FFBD36-14E9-4C17-B1E8-9A04E81E824D
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 4f439d8f848e84b0e775a56204a1834b55c7ad10
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="urhosharp-mac-support"></a>UrhoSharp Mac 支援

_Mac 特定設定和功能_

雖然 Urho 是可攜式類別程式庫，並可讓相同的 API，用於在各種平台間之遊戲的邏輯，您仍然需要初始化 Urho 驅動程式中的平台特定，並在某些情況下，您會想要利用平台特定的功能.

在下列頁面中，假設`MyGame`是子類別的`Application`類別。

## <a name="macos"></a>macOS

**支援的架構：** x86/x86-64 32 位元和 64 位元。

## <a name="creating-a-project"></a>建立專案

建立主控台專案參考 Urho NuGet，請確定您可以找到資產 （包含的資料目錄的目錄）。

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

## <a name="example"></a>範例

[完整範例](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Cocoa)


