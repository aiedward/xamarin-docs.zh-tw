---
title: 繫結的原生架構
description: 本文件說明如何使用目標 Sharpie-建立的繫結至程式庫的架構選項分散式做的架構。
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 02ee21ce58ecf945893f7e4f94763731abe92018
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781441"
---
# <a name="binding-native-frameworks"></a>繫結的原生架構

原生程式庫做為發佈的有時[framework](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html)。 目標 Sharpie 提供方便的功能，為繫結適當地定義架構透過`-framework`選項。

例如，繫結[Adobe 創意 SDK Framework](https://creativesdk.adobe.com/downloads.html) iOS 是簡單：

<pre>$ <b>sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1</b></pre>

在某些情況下，將指定的一種架構**Info.plist**應該編譯表示針對哪個 SDK 架構。 這項資訊是否有任何明確和`-sdk`傳遞選項時，目標 Sharpie 推斷它從架構的**Info.plist** (任一`DTSDKName`按鍵或組合`DTPlatformName`和`DTPlatformVersion`索引鍵)。

`-framework`選項不允許傳遞明確的標頭檔。 選擇概括性標頭檔是依照慣例為基礎的架構名稱。 如果找不到概括性標頭，目標 Sharpie 不會嘗試繫結架構提供所要剖析，以及 clang 任何架構的引數的正確概括性標頭檔案，您必須以手動方式執行繫結 (例如`-F`架構搜尋路徑選項)。

背後原理，指定`-framework`是快顯。 下列繫結引數會與相同`-framework`上述的縮寫。
特別重要的是`-F .`framework 搜尋路徑提供給 clang （請注意空白和句號，也就是命令中的必要部分）。

<pre>$ <b>sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .</b></pre>

