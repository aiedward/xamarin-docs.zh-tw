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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027031"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>在哪裡可以設定 Android SDK 的位置？

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在可視化工作室中,導航到**工具>選項> Xamarin > Android 設置**以查看和設置 Android SDK 位置:

[![偏好設定的範例位置選項卡](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

每個路徑的預設位置如下:

- Java 開發工具套件位置: 

    **C:\\\\程式\\檔案 Java jdk1.8.0_131**

- Android SDK 位置: 

    **C:\\程式檔案 (x86)\\安卓\\機器人-sdk**

- 安卓 NDK 位置: 

    **C:\\\\程式\\數據 微軟AndroidNDK64\\安卓-ndk-r13b**

請注意,NDK 的版本號可能會有所不同。 例如,而不是**android-ndk-r13b,** 它可能是一個早期版本,如**android-ndk-r10e。**

要設置 Android SDK 位置,請在**Android SDK 位置**框中輸入 Android SDK 目錄的完整路徑。 您可以瀏覽到檔案資源管理器中的 Android SDK 位置,從位址列複製路徑,並將此路徑粘貼到**Android SDK 位置**框中。
例如,如果您的 Android SDK 位置位於**\\\\C: 使用者\\使用者名\\\\AppData 本地\\Android Sdk**,請清除 Android SDK**位置**框中的舊路徑,在此路徑中粘貼,然後按一下「**確定**」。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 Mac 的視覺化工作室中,導航到 **「首選項>> Android > SDK 位置**。 在**Android**頁面中,按下「**位置**」選項卡以查看並設定 SDK 位置:

[![偏好設定的範例位置選項卡](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

每個路徑的預設位置如下:

- Android SDK 位置: 

    ***/庫/開發人員/夏馬林/安卓-sdk-macosx**

- 安卓 NDK 位置: 

    ***/庫/開發人員/夏馬林/安卓-ndk/安卓-ndk-r14b**

- Java SDK (JDK) 位置: 

    **/usr**

請注意,NDK 的版本號可能會有所不同。 例如,而不是**android-ndk-r14b,** 它可能是一個早期版本,如**android-ndk-r10e。**

要設置 Android SDK 位置,請在**Android SDK 位置**框中輸入 Android SDK 目錄的完整路徑。 您可以選擇 Finder 中的 Android SDK 資料夾,按**CTRL®&#8984;[I**查看資料夾資訊》,按下並拖動路徑到 **「位置」** 複製「右側,然後將其貼上到 **」 位置「** 選項卡中的**Android SDK 位置**框。例如,如果您的 Android SDK 位置位於 **+/庫/開發人員/Android/Sdk,** 請清除 Android **SDK 位置**框中的舊路徑,在此路徑中粘貼,然後單擊「**確定**」。

-----
