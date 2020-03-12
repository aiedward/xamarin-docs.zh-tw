---
title: 在哪裡可以設定 Android SDK 的位置？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 11/16/2017
ms.openlocfilehash: 8685be4bb1cc45ff04dc8d9f7d8e64e7b1483b60
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027031"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>在哪裡可以設定 Android SDK 的位置？

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中，流覽至 [工具] [ **> 選項] > [Xamarin > Android 設定**]，以查看並設定 Android SDK 位置：

[喜好設定中的 ![範例位置 索引標籤](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

每個路徑的預設位置如下所示：

- JAVA 開發工具組位置： 

    **C：\\Program Files\\JAVA\\jdk 1.8.0 _131**

- Android SDK 位置： 

    **C:\\Program Files (x86)\\Android\\android-sdk**

- Android NDK 位置： 

    **C：\\ProgramData\\Microsoft\\AndroidNDK64\\android-ndk-r13b**

請注意，NDK 的版本號碼可能會有所不同。 例如，而不是**android-ndk-r13b**，它可能是較舊的版本，例如**android-ndk-r10e**。

若要設定 Android SDK 位置，請在 [ **Android SDK 位置**] 方塊中輸入 Android SDK 目錄的完整路徑。 您可以流覽至 [檔案瀏覽器] 中的 Android SDK 位置，從網址列複製路徑，並將此路徑貼入 [ **Android SDK 位置**] 方塊中。
例如，如果您的 Android SDK 位置是在**C：\\使用者\\username\\AppData\\Local\\Android\\SDK**，請清除 [ **Android SDK 位置**] 方塊中的舊路徑，貼上此路徑，然後按一下 **[確定]** 。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，流覽至 [喜好設定] [ **> 專案] > [SDK 位置] > Android**。 在 [ **Android** ] 頁面中，按一下 [**位置**] 索引標籤，以查看並設定 SDK 位置：

[喜好設定中的 ![範例位置 索引標籤](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

每個路徑的預設位置如下所示：

- Android SDK 位置： 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Android NDK 位置： 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- JAVA SDK （JDK）位置： 

    **/usr**

請注意，NDK 的版本號碼可能會有所不同。 例如，而不是**android-ndk-r14b**，它可能是較舊的版本，例如**android-ndk-r10e**。

若要設定 Android SDK 位置，請在 [ **Android SDK 位置**] 方塊中輸入 Android SDK 目錄的完整路徑。 您可以選取搜尋工具中的 [Android SDK] 資料夾 **，按&#8984;CTRL + + I**以查看資料夾資訊，按一下並拖曳**Where：** ，Copy，然後貼到 [**位置**] 索引標籤中的 [ **Android SDK 位置**] 方塊中的路徑。例如，如果您的 Android SDK 位置位於 **~/Library/Developer/Android/Sdk**，請清除 [ **Android SDK 位置**] 方塊中的舊路徑，貼上此路徑，然後按一下 **[確定]** 。

-----
