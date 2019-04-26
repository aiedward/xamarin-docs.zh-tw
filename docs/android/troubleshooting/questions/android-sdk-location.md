---
title: 在哪裡可以設定 Android SDK 的位置？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 11/16/2017
ms.openlocfilehash: c004fb7717f78750e7ac1e8dc1856a32ba808638
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227670"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>在哪裡可以設定 Android SDK 的位置？

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中，瀏覽至**工具 > 選項 > Xamarin > Android 設定**檢視和設定的 Android SDK 位置：

[![在 [喜好設定的範例位置] 索引標籤](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

每個路徑的預設位置如下所示：

- Java 開發套件位置： 

    **C:\\程式檔案\\Java\\jdk1.8.0_131**

- Android SDK 位置： 

    **C:\\Program Files (x86)\\Android\\android-sdk**

- Android NDK 的位置： 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\android-ndk-r13b**

請注意，NDK 的版本號碼可能會有所不同。 比方說，而不是**android 的 ndk-r13b**，它可能是較早版本，例如**android 的 ndk-r10e**。

若要設定 Android SDK 位置，輸入將 Android SDK 目錄的完整路徑**Android SDK 位置** 方塊中。 您可以瀏覽至 Android SDK 位置在檔案總管中，從 網址 列中，複製路徑並貼到這個路徑**Android SDK 位置** 方塊中。
例如，如果您的 Android SDK 位置處**c:\\使用者\\username\\AppData\\本機\\Android\\Sdk**，清除舊的路徑中**Android SDK 位置**方塊中貼上此路徑，然後按一下 **[確定]**。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，瀏覽至**喜好設定 > 專案 > SDK 的位置 > Android**。 在 [ **Android**頁面上，按一下**位置**] 索引標籤來檢視和設定 SDK 位置：

[![在 [喜好設定的範例位置] 索引標籤](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

每個路徑的預設位置如下所示：

- Android SDK 位置： 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Android NDK 的位置： 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Java SDK (JDK) 的位置： 

    **/usr**

請注意，NDK 的版本號碼可能會有所不同。 比方說，而不是**android 的 ndk-r14b**，它可能是較早版本，例如**android 的 ndk-r10e**。

若要設定 Android SDK 位置，輸入將 Android SDK 目錄的完整路徑**Android SDK 位置** 方塊中。 您可以選取 Android SDK 資料夾中搜尋工具中，按下**CTRL +&#8984;+ I**若要檢視資料夾資訊，請按一下並拖曳路徑右邊**位置：**、 複製，然後將它貼到**Android SDK位置**方塊中**位置**] 索引標籤。例如，如果您的 Android SDK 位置處 **~/Library/Developer/Android/Sdk**，清除舊的路徑中**Android SDK 位置**，在此路徑中，貼上，按一下 [ **[確定]**.

-----
