---
title: 為什麼我要這麼做 Xamarin.Forms 。對應 Android 專案失敗，發生因為 COMPILETODALVIK 非預期的最上層錯誤？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e29535e71cb77b05da41c043c6fd932ae4f5ce95
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135846"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>為什麼我要這麼做 Xamarin.Forms 。對應 Android 專案失敗，發生因為 COMPILETODALVIK 非預期的最上層錯誤？

此錯誤可能會出現在 Visual Studio for Mac 的錯誤 pad 中，或在 Visual Studio 的 [組建輸出] 視窗中。在 Android 專案中使用 Xamarin.Forms 。繪.

藉由增加您的 Xamarin Android 專案的 JAVA 堆積大小，最常解決此問題。 請遵循下列步驟來增加堆積大小：

## <a name="visual-studio"></a>Visual Studio

1. 以滑鼠右鍵按一下 Android 專案，& 開啟 [專案選項]。
2. 前往**Android 選項-> Advanced**
3. 在 [JAVA 堆積大小] 文字方塊中，輸入1G。
4. 請重建專案。

![Visual Studio 專案選項的螢幕擷取畫面](maps-compiletodalvik-error-images/vsjavaheap.png "Visual Studio 中的 Android 組建選項")

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 以滑鼠右鍵按一下 Android 專案，& 開啟 [專案選項]。
2. 移至**組建-> Android 組建-> Advanced**
3. 在 [JAVA 堆積大小] 文字方塊中，輸入1G。
4. 請重建專案。  

![Visual Studio for Mac 專案選項的螢幕擷取畫面](maps-compiletodalvik-error-images/xsjavaheap.png "Visual Studio for Mac 中的 Android 組建選項")
