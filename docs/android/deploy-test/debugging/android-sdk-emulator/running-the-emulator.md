---
title: "執行 Android SDK 模擬器"
description: "如何使用 Android SDK 模擬器來進行應用程式偵錯"
ms.topic: article
ms.prod: xamarin
ms.assetid: AEA165A4-D81A-411B-91DF-2DED2EED27B5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 89768d2562814091f0e5894c4af2edd67d68cb00
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="running-the-android-sdk-emulator"></a>執行 Android SDK 模擬器

在本指南中，您將了解如何在 Android SDK Emulator 中啟動虛擬裝置來進行偵錯和測試您的應用程式。

## <a name="using-a-pre-configured-virtual-device"></a>使用預先設定的虛擬裝置

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Visual Studio 包含預先設定的虛擬裝置，這些裝置會顯示在裝置下拉式功能表中。 例如，在以下 Visual Studio 2017 螢幕擷取畫面中，有數個可用的預先設定虛擬裝置：

-   **VisualStudio\_android-23\_arm\_phone**

-   **VisualStudio\_android-23\_arm\_tablet**

-   **VisualStudio\_android-23\_x86\_phone** 

-   **VisualStudio\_android-23\_x86\_tablet** 

[![虛擬裝置](running-the-emulator-images/win/01-virtual-devices-sml.png)](running-the-emulator-images/win/01-virtual-devices.png#lightbox)

通常您會選取 [VisualStudio\_android-23\_x86\_phone] 虛擬裝置來針對手機應用程式進行測試和偵錯。 如果這其中一個預先設定的虛擬裝置符合您的需求 (亦即符合您應用程式的目標 API 層級)，請跳至[啟動模擬器](#launching)以開始在模擬器中執行您的應用程式。 (如果您不熟悉 Android API 層級，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md))。

如果您的 Xamarin.Android 專案使用與可用虛擬裝置不相容的「目標 Framework」層級，下拉式功能表將會在 [不支援的裝置] 底下列出無法使用的虛擬裝置。 例如，以下專案的「目標 Framework」設定為 **Android 7.1 Nougat (API 25)**，這與預設提供的 **Android 6.0** 虛擬裝置不相容：

[![不相容的虛擬裝置](running-the-emulator-images/win/02-incompatible-level-sml.png)](running-the-emulator-images/win/02-incompatible-level.png#lightbox)

您可以按一下 [變更最低 Android 目標] 來變更專案的「最低 Android 版本」，以讓它符合可用虛擬裝置的 API 層級。 或者，您也可以使用 **Android 模擬器管理員**來建立支援您目標 API 層級的新虛擬裝置，如稍後的[設定虛擬裝置](#virtualdevice)所述。 若要設定新 API 層級的虛擬裝置，您必須先安裝該 API 層級的對應系統映像 &ndash; 下一節中將會說明。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

Visual Studio for Mac 包含預先設定的虛擬裝置，這些裝置會顯示在裝置下拉式功能表中。 例如，在以下螢幕擷取畫面中，有兩個可用的預先設定虛擬裝置：

-   **Android\_Accelerated\_x86**

-   **Android\_ARMv7a**

[![虛擬裝置](running-the-emulator-images/mac/01-virtual-devices-sml.png)](running-the-emulator-images/mac/01-virtual-devices.png#lightbox)

通常您會選取 [Android\_Accelerated\_x86] 虛擬裝置來針對手機應用程式進行測試和偵錯。 如果這個預先設定的虛擬裝置符合您的需求 (亦即符合您應用程式的目標 API 層級)，請跳至[啟動模擬器](#launching)以開始在模擬器中執行您的應用程式。 (如果您不熟悉 Android API 層級，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md))。

-----

## <a name="creating-custom-virtual-devices"></a>建立自訂虛擬裝置

若要建立自訂虛擬裝置，您必須使用「Xamarin Android 裝置管理員」或 Android SDK 隨附的舊版「Google 模擬器管理員」。 如需有關建立和自訂虛擬裝置的詳細資訊，請參閱 [Xamarin Android 裝置管理員](~/android/get-started/installation/android-emulator/xamarin-device-manager.md)。
如果您偏好使用舊版「Google 模擬器管理員」，請參閱 [Google 模擬器管理員](~/android/get-started/installation/android-emulator/google-emulator-manager.md)。

請注意，如果您要針對 Android 8.0 Oreo 進行開發，就必須使用「Xamarin Android 裝置管理員」。

<a name="launching" />

## <a name="launching-the-emulator"></a>啟動模擬器

在 IDE 頂端附近有一個下拉式功能表，可用來選取 [偵錯] 或 [發行] 模式。 選擇 [偵錯] 會將偵錯工具連接至在模擬器內執行的應用程式處理序。 

從裝置下拉式功能表中選擇虛擬裝置之後，請選取 [偵錯] 或 [發行] 模式，然後按一下 [播放] 按鈕來執行應用程式：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![[偵錯] 和 [發行] 模式、[播放] 按鈕](running-the-emulator-images/win/17-debug-release-sml.png)](running-the-emulator-images/win/17-debug-release.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![[偵錯] 和 [發行] 模式、[播放] 按鈕](running-the-emulator-images/mac/16-debug-release-sml.png)](running-the-emulator-images/mac/16-debug-release.png#lightbox)

-----

在 Android 模擬器啟動之後，Xamarin.Android 就會將應用程式部署至模擬器。 模擬器會以所設定的虛擬裝置映像來執行應用程式。 以下顯示「Android SDK 模擬器」的範例螢幕擷取畫面 (模擬器正在執行名為 **MyApp** 的空白應用程式)：

![執行空白應用程式的模擬器](running-the-emulator-images/emulator-running.png)

您可以讓模擬器保持執行；無須在每次執行應用程式時都將其關閉並重新啟動。 第一次在模擬器中執行 Xamarin.Android 應用程式時，會安裝目標 API 層級的 Xamarin.Android 共用執行階段，再接著安裝應用程式。 安裝執行階段可能需要一些時間，因此請耐心等候。 只有在將第一個 Xamarin.Android 應用程式部署至模擬器時，才會安裝執行階段 &ndash; 後續部署速度會較快，因為要複製到模擬器的只有應用程式。

如需有關使用「Android SDK 模擬器」的詳細資訊，請參閱下列 Android 開發人員主題：

-   [在螢幕上瀏覽](https://developer.android.com/studio/run/emulator.html#navigate)

-   [在模擬器中執行基本工作](https://developer.android.com/studio/run/emulator.html#tasks)

-   [使用延伸控制項、設定及說明](https://developer.android.com/studio/run/emulator.html#extended)

