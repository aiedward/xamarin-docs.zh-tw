---
title: "浮點數"
ms.topic: article
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ea29132ad4ac55f6fb151ac2125ab1add82c8518
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="floating-point"></a>浮點數

Xamarin.iOS 依預設會執行 32 位元和 64 位元浮點運算 on ARM 使用 64 位元有效位數。  

從浮點運算在 C# 中，在桌面上，行動裝置上, 預期的開發人員更接近此較高的精確度時可能十分顯著的效能影響。

很可能要使用 32 位元浮點運算將 32 位元浮點點程式碼編譯。  若要這樣做，您需要使用至少 Xamarin.iOS 8.10 和設定您的 iOS 上建置選項的面板"mtouch 多餘的引數 」 項目列的下列值：

     --aot-options=-O=float32

這會通知靜態編譯器 （單聲道的內建靜態編譯器或 LLVM 供電一個） 來執行浮點運算使用 32 位元浮點數。
