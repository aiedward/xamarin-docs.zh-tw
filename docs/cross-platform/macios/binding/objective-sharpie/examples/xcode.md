---
title: 使用 Xcode 專案的真實世界範例
description: 本檔說明如何使用 Xcode 專案作為目標 Sharpie 的直接輸入，簡化建立C#目標 C 程式碼系結的流程。
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: conceptdev
ms.author: crdun
ms.date: 01/15/2016
ms.openlocfilehash: e460994994c1383f29028be7b13cec216f2d12f7
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290655"
---
# <a name="real-world-example-using-an-xcode-project"></a>使用 Xcode 專案的真實世界範例

**這個範例使用[來自 Facebook 的 POP 程式庫](https://github.com/facebook/pop)。**

3\.0 版的新功能，目標 Sharpie 支援將 Xcode 專案當做輸入。 這些專案會指定正確的標頭檔和編譯原生程式庫所需的編譯器旗標，因此也需要進行系結。 目標 Sharpie 會選取專案的第一個目標和預設_設定_（如果未指示的話）。

在目標 Sharpie 嘗試剖析專案和標頭檔之前，必須先建立它。 專案的組建階段通常會正確地將標頭檔的結構設為外部耗用量和整合，因此最好一律先建立完整的專案，然後再嘗試系結。

```
$ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   (more git clone output)

$ cd pop
$ sharpie bind pop.xcodeproj -sdk iphoneos9.0
```
