---
title: "真實世界範例使用 Xcode 專案"
ms.topic: article
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 34efeb2b505f6076623f22f36c2a48a52d6f399f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="real-world-example-using-an-xcode-project"></a>真實世界範例使用 Xcode 專案


**這個範例會使用[來自 Facebook 的快顯程式庫](https://github.com/facebook/pop)。**

新的 3.0 版開始，在目標 Sharpie 會支援做為輸入的 Xcode 專案。 這些專案指定正確的標頭檔和編譯器旗標需要編譯原生程式庫，並因此需要太將它繫結。 目標 Sharpie 會選取第一個_目標_並不指示，否則如果專案的預設設定。

目標 Sharpie 嘗試剖析專案檔和標頭檔之前，它就必須進行建置。 專案通常會有組建階段，因此最好一律建置完整的專案，再嘗試將它繫結，則正確將結構外部耗用量和整合、 標頭檔。

<pre>$ <b>git clone https://github.com/facebook/pop.git</b>
Cloning into 'pop'...
   <em>(more git clone output)</em>

$ <b>cd pop</b>
$ <b>sharpie bind pop.xcodeproj -sdk iphoneos9.0</b></pre>

