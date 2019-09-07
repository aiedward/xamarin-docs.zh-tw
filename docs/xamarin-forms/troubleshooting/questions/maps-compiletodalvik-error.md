---
title: 為什麼我的 Xamarin. Maps Android 專案會因為因為 COMPILETODALVIK 未預期的最上層錯誤而失敗？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 2872cc7b54e26d07b388f08d650048e8d3861930
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759972"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>為什麼我的 Xamarin. Maps Android 專案會因為因為 COMPILETODALVIK 未預期的最上層錯誤而失敗？

此錯誤可能會出現在 Visual Studio for Mac 的錯誤 pad 中，或在 Visual Studio 的 [組建輸出] 視窗中。在使用 Xamarin 的 Android 專案中。

藉由增加您的 Xamarin Android 專案的 JAVA 堆積大小，最常解決此問題。 請遵循下列步驟來增加堆積大小：

## <a name="visual-studio"></a>Visual Studio

1. 以滑鼠右鍵按一下 Android 專案，& 開啟 [專案選項]。
2. 前往**Android 選項-> Advanced**
3. 在 [JAVA 堆積大小] 文字方塊中，輸入1G。
4. 重建專案。

![Visual Studio 專案選項的螢幕擷取畫面](maps-compiletodalvik-error-images/vsjavaheap.png "Visual Studio 中的 Android 組建選項")

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 以滑鼠右鍵按一下 Android 專案，& 開啟 [專案選項]。
2. 移至**組建-> Android 組建-> Advanced**
3. 在 [JAVA 堆積大小] 文字方塊中，輸入1G。
4. 重建專案。  

![Visual Studio for Mac 專案選項的螢幕擷取畫面](maps-compiletodalvik-error-images/xsjavaheap.png "Visual Studio for Mac 中的 Android 組建選項")
