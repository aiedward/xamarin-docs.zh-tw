---
title: 繫結原生架構
description: 本文件說明如何使用目標 Sharpie-建立繫結至程式庫的架構選項分散式的架構。
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: ca103ee027597813be51e126aaa05f9aa969af35
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199124"
---
# <a name="binding-native-frameworks"></a>繫結原生架構

原生程式庫散發為有時[framework](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html)。 目標 Sharpie 提供方便的功能，正確地繫結定義架構透過`-framework`選項。

比方說，繫結[Adobe Creative SDK 架構](https://creativesdk.adobe.com/downloads.html)iOS 是簡單：

<pre>$ <b>sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1</b></pre>

在某些情況下，將指定的一種架構**Info.plist**應該編譯表示針對哪個 SDK 架構。 這項資訊有和任何明確`-sdk`傳遞選項，目標 Sharpie 會推斷它從架構的**Info.plist** (任一`DTSDKName`機碼或多種`DTPlatformName`和`DTPlatformVersion`索引鍵)。

`-framework`選項不允許明確的標頭檔，來傳遞。 依照慣例為基礎的架構名稱選擇傘標頭檔。 如果找不到的傘狀標頭，目標 Sharpie 不會嘗試繫結架構提供剖析，以及針對 clang 任何 framework 引數的正確保護傘標頭檔，您必須以手動方式執行繫結 (例如`-F`架構搜尋路徑選項)。

在幕後，指定`-framework`的捷徑。 下列繫結引數會與相同`-framework`上述的速記。
是特殊的重要性`-F .`提供給 clang 架構搜尋路徑 （請注意空格和期限，也就是必要的命令的一部分）。

<pre>$ <b>sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .</b></pre>

## <a name="related-links"></a>相關連結

- [Xamarin University 課程：建置 OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University 課程：建置目標 Sharpie OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)

