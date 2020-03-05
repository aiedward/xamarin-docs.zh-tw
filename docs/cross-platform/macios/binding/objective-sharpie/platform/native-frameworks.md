---
title: 繫結原生架構
description: 本檔說明如何使用目標 Sharpie 的架構選項，建立以架構形式散發之程式庫的系結。
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: davidortinau
ms.author: daortin
ms.date: 01/15/2016
ms.openlocfilehash: 0733e2b406f5032a2e2717df66c96dcb28301f09
ms.sourcegitcommit: 24883be72e485e5311dd0eb91f9a22f78eeec11a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/17/2020
ms.locfileid: "78292705"
---
# <a name="binding-native-frameworks"></a>繫結原生架構

有時，原生程式庫會以[架構](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html)的形式散發。 目標 Sharpie 提供便利的功能，可透過 [`-framework`] 選項系結適當定義的架構。

例如，系結適用于 iOS 的[Adobe 創意 SDK 架構](https://creativesdk.adobe.com/downloads.html)很簡單：

```
$ sharpie bind \
    -framework ./AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1
```

在某些情況下，架構會指定**plist** ，以指出應該編譯架構的 SDK。 如果這項資訊存在，而且未傳遞明確的 `-sdk` 選項，則目標 Sharpie 會從架構的**Info. plist** （`DTSDKName` 鍵或 `DTPlatformName` 和 `DTPlatformVersion` 金鑰的組合）推斷它。

`-framework` 選項不允許傳遞明確的標頭檔。 根據架構名稱，慣例會選擇傘標頭檔案。 如果找不到傘標頭，目標 Sharpie 就不會嘗試系結架構，而且您必須藉由提供要剖析的正確傘標頭檔，以及 clang 的任何架構引數（例如 `-F` framework 搜尋路徑選項），手動執行系結。

實際上，指定 `-framework` 只是一個快捷方式。 下列系結引數與上述 `-framework` 速記相同。
特別重要的是提供給 clang 的 `-F .` framework 搜尋路徑（請注意，命令所需的空格和句點）。

```
$ sharpie bind \
    -sdk iphoneos8.1 \
    ./AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .
```
