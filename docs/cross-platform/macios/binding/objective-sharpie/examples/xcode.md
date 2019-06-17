---
title: 使用 Xcode 專案的真實世界範例
description: 本文件說明如何使用 Xcode 專案做為目標 Sharpie，簡化的建立程序直接輸入C#繫結 OBJECTIVE-C 程式碼。
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: ccfc2f1760d8971e2d824cf65344fa2a5e158c12
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978360"
---
# <a name="real-world-example-using-an-xcode-project"></a>使用 Xcode 專案的真實世界範例

**這個範例會使用[POP 程式庫，從 Facebook](https://github.com/facebook/pop)。**

新的 3.0 版開始，目標 Sharpie 支援 Xcode 專案做為輸入。 這些專案指定正確的標頭檔和編譯器旗標需要編譯原生程式庫，並因此不必太將它繫結。 目標 Sharpie 會選取第一個_目標_並不指示，否則如果專案的預設設定。

目標 Sharpie 嘗試剖析專案檔和標頭檔之前，您必須建置它。 專案通常會有正確地將結構外部的耗用量和整合，標頭檔，讓最好是一律建置完整的專案，然後再嘗試將它繫結的建置階段。

<pre>$ <b>git clone https://github.com/facebook/pop.git</b>
Cloning into 'pop'...
   <em>(more git clone output)</em>

$ <b>cd pop</b>
$ <b>sharpie bind pop.xcodeproj -sdk iphoneos9.0</b></pre>

