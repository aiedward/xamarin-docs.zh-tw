---
title: 繫結原生架構
description: 本檔說明如何使用目標 Sharpie 的架構選項，建立以架構形式散發之程式庫的系結。
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: conceptdev
ms.author: crdun
ms.date: 01/15/2016
ms.openlocfilehash: 560db570e915cc9bf261f482f03d972973968585
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279033"
---
# <a name="binding-native-frameworks"></a>繫結原生架構

有時，原生程式庫會以[架構](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html)的形式散發。 目標 Sharpie 提供便利的功能，可透過`-framework`選項系結已適當定義的架構。

例如，系結適用于 iOS 的[Adobe 創意 SDK 架構](https://creativesdk.adobe.com/downloads.html)很簡單：

```
$ sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1
```

在某些情況下，架構會指定**plist** ，以指出應該編譯架構的 SDK。 如果這項資訊存在，而且`-sdk`未傳遞任何明確的選項，則目標 Sharpie 會從架構的**Info. plist** （ `DTSDKName`索引鍵或`DTPlatformName`和`DTPlatformVersion`索引鍵的組合）推斷它。

`-framework`選項不允許傳遞明確的標頭檔。 根據架構名稱，慣例會選擇傘標頭檔案。 如果找不到傘標頭，目標 Sharpie 就不會嘗試系結架構，而且您必須藉由提供要剖析的正確傘標頭檔，以及 clang 的任何架構引數，來手動執行系結（例如`-F`架構搜尋路徑選項）。

實際上，指定`-framework`只是一個快捷方式。 下列系結引數與上述的`-framework`縮寫相同。
特別重要的是`-F .`提供給 clang 的架構搜尋路徑（請注意需要做為命令一部分的空格和句點）。

```
$ sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .
```
