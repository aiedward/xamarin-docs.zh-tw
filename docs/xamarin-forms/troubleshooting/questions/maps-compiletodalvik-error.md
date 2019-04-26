---
title: 為什麼我的 Xamarin.Forms.Maps Android 專案使用 COMPILETODALVIK UNEXPECTED TOP-LEVEL ERROR 失敗？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 9df9e348440b9dd4b18b3859d64cbe47bd05b24c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250450"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>為什麼我的 Xamarin.Forms.Maps Android 專案使用 COMPILETODALVIK UNEXPECTED TOP-LEVEL ERROR 失敗？

此錯誤可能會出現在 Visual studio 的 [錯誤] 面板中，for Mac 或 Visual Studio; 的 [建置輸出] 視窗中在使用 Xamarin.Forms.Maps Android 專案。

這是最常解析藉由增加您的 Xamarin.Android 專案的 Java 堆積大小。 請遵循下列步驟來增加堆積大小：

## <a name="visual-studio"></a>Visual Studio

1. 以滑鼠右鍵按一下 Android 專案，並開啟 [專案選項]。
2. 移至**Android 選項]-> [進階**
3. Java 堆積大小 文字方塊中輸入 1g。
4. 重建專案。

![Visual Studio 專案選項的螢幕擷取畫面](maps-compiletodalvik-error-images/vsjavaheap.png "Android 建置 Visual Studio 中的選項")

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1.  以滑鼠右鍵按一下 Android 專案，並開啟 [專案選項]。
2.  移至**組建]-> [Android 組建]-> [進階**
3.  Java 堆積大小 文字方塊中輸入 1g。
4.  重建專案。  

![Visual Studio for Mac 專案選項的螢幕擷取畫面](maps-compiletodalvik-error-images/xsjavaheap.png "Android 建置選項在 Visual Studio for Mac")

