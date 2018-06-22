---
title: 可以設定我的 Android SDK 的位置？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 11/16/2017
ms.openlocfilehash: e7af6635aeec72a9a0e0c01eeb6eb0a77d2a1d7b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764004"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>可以設定我的 Android SDK 的位置？

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在 Visual Studio 中，瀏覽至**工具 > 選項 > Xamarin > Android 設定**檢視和設定的 Android SDK 位置：

[![喜好設定中的範例位置 索引標籤](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

每個路徑的預設位置如下：

- Java Development Kit 位置： 

    **C:\\程式檔案\\Java\\jdk1.8.0_131**

- Android SDK 位置： 

    **C:\\Program Files (x86)\\Android\\android-sdk**

- Android NDK 位置： 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\android-ndk-r13b**

請注意，NDK 的版本號碼可能會不同。 例如，而不是**android-ndk-r13b**，它可能是較早版本，例如**android-ndk-r10e**。

若要設定 Android SDK 的位置，請輸入至 Android SDK 目錄的完整路徑**Android SDK 位置**方塊。 您可以瀏覽至 [檔案總管] 中的 Android SDK 位置，從 [網址] 列中，複製路徑並貼上到此路徑**Android SDK 位置**方塊。
例如，如果您的 Android SDK 位置位於**c:\\使用者\\username\\AppData\\本機\\Android\\Sdk**，清除舊的路徑中**Android SDK 位置**，在這個路徑中，貼上，按一下 **確定**。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在 Visual Studio for Mac，瀏覽至**喜好設定 > 專案 > SDK 位置 > Android**。 在**Android**頁面上，按一下**位置** 索引標籤來檢視和設定 SDK 位置：

[![喜好設定中的範例位置 索引標籤](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

每個路徑的預設位置如下：

- Android SDK 位置： 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Android NDK 位置： 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Java SDK (JDK) 的位置： 

    **/usr**

請注意，NDK 的版本號碼可能會不同。 例如，而不是**android-ndk-r14b**，它可能是較早版本，例如**android-ndk-r10e**。

若要設定 Android SDK 的位置，請輸入至 Android SDK 目錄的完整路徑**Android SDK 位置**方塊。 您可以選取 Android SDK 資料夾中尋找工具中，按**CTRL +&#8984;+ I**若要檢視資料夾資訊，請按一下並拖曳路徑右邊的**位置：**，複製，然後將它貼到**Android SDK位置**方塊中**位置**] 索引標籤。例如，如果您的 Android SDK 位置位於 **~/Library/Developer/Android/Sdk**，清除舊的路徑中**Android SDK 位置**，在這個路徑中，貼上，按一下 [**確定**.

-----
