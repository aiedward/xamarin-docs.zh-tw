---
title: 浮點運算中 Xamarin.iOS
description: 本文件描述 Xamarin.iOS 處理 32 位元和 64 位元精確度浮點運算的方式，並討論相關聯的效能影響。
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ea5d69b52cbd4c76abb236bd1a272633dde440b7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786157"
---
# <a name="floating-point-operations-in-xamarinios"></a>浮點運算中 Xamarin.iOS

Xamarin.iOS 依預設會執行 32 位元和 64 位元浮點運算 on ARM 使用 64 位元有效位數。  

從浮點運算在 C# 中，在桌面上，行動裝置上, 預期的開發人員更接近此較高的精確度時可能十分顯著的效能影響。

很可能要使用 32 位元浮點運算將 32 位元浮點點程式碼編譯。  若要這樣做，您需要使用至少 Xamarin.iOS 8.10 和設定您的 iOS 上建置選項的面板"mtouch 多餘的引數 」 項目列的下列值：

     --aot-options=-O=float32

這會通知靜態編譯器 （單聲道的內建靜態編譯器或 LLVM 供電一個） 來執行浮點運算使用 32 位元浮點數。
