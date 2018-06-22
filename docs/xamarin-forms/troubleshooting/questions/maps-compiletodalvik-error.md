---
title: 為什麼我 Xamarin.Forms.Maps Android 專案與 COMPILETODALVIK 未預期的最高層級錯誤失敗？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 9df9e348440b9dd4b18b3859d64cbe47bd05b24c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30790661"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>為什麼我 Xamarin.Forms.Maps Android 專案與 COMPILETODALVIK 未預期的最高層級錯誤失敗？

可能會出現此錯誤在錯誤輸入板中的 Visual Studio for Mac，或在建置輸出視窗中的 Visual Studio;在使用 Xamarin.Forms.Maps Android 專案。

這最常見的解決方式增加 Xamarin.Android 專案 Java 堆積大小。 遵循下列步驟，以提高的堆積大小：

## <a name="visual-studio"></a>Visual Studio

1. 以滑鼠右鍵按一下 Android 專案 （& s) 開啟的專案選項。
2. 移至**Android 選項]-> [進階**
3. 在 Java 堆積大小 文字方塊中輸入 1 G。
4. 重建專案。

![Visual Studio 專案選項的螢幕擷取畫面](maps-compiletodalvik-error-images/vsjavaheap.png "Android 建置 Visual Studio 中的選項")

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1.  以滑鼠右鍵按一下 Android 專案 （& s) 開啟的專案選項。
2.  移至**組建]-> [Android 建置]-> [進階**
3.  在 Java 堆積大小 文字方塊中輸入 1 G。
4.  重建專案。  

![Visual Studio for Mac 專案選項的螢幕擷取畫面](maps-compiletodalvik-error-images/xsjavaheap.png "Android 建置選項在 Visual Studio for Mac")

