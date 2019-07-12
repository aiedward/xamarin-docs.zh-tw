---
title: UrhoSharp Mac 的支援
description: 本文件討論 UrhoSharp macOS 支援。 它說明如何建立專案，並提供一些範例程式碼的連結。
ms.prod: xamarin
ms.assetid: 95FFBD36-14E9-4C17-B1E8-9A04E81E824D
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: ee0a03d168b6e628893b18a27d73b46d3fa2fbc2
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832665"
---
# <a name="urhosharp-mac-support"></a>UrhoSharp Mac 的支援

_Mac 特定設定和功能_

雖然 Urho 是可攜式類別庫，並可讓相同的 API，來使用於各種平台的遊戲邏輯，您仍然需要初始化 Urho，在您的平台特定的驅動程式，以及在某些情況下，您會想要充分利用平台特定功能.

在下列頁面中，假設`MyGame`是子類別的`Application`類別。

## <a name="macos"></a>macOS

**支援的架構：** x86/x86-64 32 位元和 64 位元。

## <a name="creating-a-project"></a>建立專案

建立主控台專案參考 Urho NuGet，請確定您可以找出資產 （包含的資料目錄的目錄）。

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

## <a name="example"></a>範例

[完整的範例](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Cocoa)
