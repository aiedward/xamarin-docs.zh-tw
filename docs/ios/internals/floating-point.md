---
title: Xamarin 中的浮點作業
description: 本檔說明 Xamarin iOS 如何處理32位和64位精確度的浮點運算，並討論與效能相關聯的影響。
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 1ecb00fecaf14afb8c6d5c59297eb26821ed791a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291933"
---
# <a name="floating-point-operations-in-xamarinios"></a>Xamarin 中的浮點作業

根據預設，Xamarin 會使用 ARM 上的64位精確度來執行32位和64位的浮點運算。  

雖然這個較高的精確度更接近桌上型電腦C#上的浮點作業所預期的，但在行動裝置上，效能影響可能會很大。

您可以編譯您的32位浮點程式碼，以使用32位的浮點運算。  若要這樣做，您必須使用至少為 Xamarin. iOS 8.10，並在 iOS 組建選項的面板中，于 [mtouch 額外的引數] 專案行中設定下列值：

```
--aot-options=-O=float32
```

這會通知靜態編譯器（Mono 的內建靜態編譯器，或 LLVM 驅動程式），使用32位的浮點數執行浮點運算。
