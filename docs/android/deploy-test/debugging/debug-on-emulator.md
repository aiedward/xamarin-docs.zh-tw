---
title: 在 Android Emulator 上偵錯
description: 本指南說明如何在 Visual Studio 中使用 Android Emulator 啟動並偵錯應用程式。
ms.prod: xamarin
ms.assetid: AEA165A4-D81A-411B-91DF-2DED2EED27B5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 2bc8f82db29ed3c07c67293a83e6874f0cc6acb2
ms.sourcegitcommit: 5821c9709bf5e06e6126233932f94f9cf3524577
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2019
ms.locfileid: "75556518"
---
# <a name="debug-on-the-android-emulator"></a>Android Emulator 上的 Debug

_在本指南中，您將了解如何在 Android Emulator 中啟動虛擬裝置來偵錯和測試您的應用程式。_

Android Emulator (安裝作為**使用 .NET 開發行動裝置應用程式**工作負載的一部分) 可在各種不同的組態中執行，以模擬不同的 Android 裝置。 這其中每一種組態都會建立為「虛擬裝置」。 在本指南中，您將了解如何從 Visual Studio 啟動模擬器，以及在虛擬裝置中執行應用程式。 如需有關設定 Android Emulator 及建立新虛擬裝置的資訊，請參閱 [Android Emulator Setup](~/android/get-started/installation/android-emulator/index.md) (Android 模擬器設定)。

## <a name="using-a-pre-configured-virtual-device"></a>使用預先設定的虛擬裝置

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio 包含預先設定的虛擬裝置，這些裝置會顯示在裝置下拉式功能表中。 例如，在以下 Visual Studio 2017 螢幕擷取畫面中，有數個可用的預先設定虛擬裝置：

- **VisualStudio\_android-23\_arm\_phone**

- **VisualStudio\_android-23\_arm\_tablet**

- **VisualStudio\_android-23\_x86\_phone** 

- **VisualStudio\_android-23\_x86\_tablet** 

[![虛擬裝置](debug-on-emulator-images/win/01-virtual-devices-sml.png)](debug-on-emulator-images/win/01-virtual-devices.png#lightbox)

通常您會選取 [VisualStudio\_android-23\_x86\_phone] 虛擬裝置來針對手機應用程式進行測試和偵錯。 如果這其中一個預先設定的虛擬裝置符合您的需求 (亦即符合您應用程式的目標 API 層級)，請跳至[啟動模擬器](#launching)以開始在模擬器中執行您的應用程式。 (如果您不熟悉 Android API 層級，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md))。

如果您的 Xamarin.Android 專案使用與可用虛擬裝置不相容的「目標 Framework」層級，下拉式功能表會在 [不支援的裝置] 底下列出無法使用的虛擬裝置。 例如，以下專案的「目標 Framework」設定為 **Android 7.1 Nougat (API 25)** ，這與本範例列出的 **Android 6.0** 虛擬裝置不相容：

[![不相容的虛擬裝置](debug-on-emulator-images/win/02-incompatible-level-sml.png)](debug-on-emulator-images/win/02-incompatible-level.png#lightbox)

您可以按一下 [變更最低 Android 目標] 來變更專案的「最低 Android 版本」，以讓它符合可用虛擬裝置的 API 層級。 或者，您也可以使用 [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) 建立支援您目標 API 層級的新虛擬裝置。
若要設定新 API 層級的虛擬裝置，您必須先安裝該 API 層級的對應系統映像 (請參閱[設定 Xamarin.Android 的 Android SDK](~/android/get-started/installation/android-sdk.md))。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Visual Studio for Mac 包含預先設定的虛擬裝置，這些裝置會顯示在裝置下拉式功能表中。 例如，在以下螢幕擷取畫面中，有兩個可用的預先設定虛擬裝置：

- **Android\_Accelerated\_x86**

- **Android\_ARMv7a**

[![虛擬裝置](debug-on-emulator-images/mac/01-virtual-devices-sml.png)](debug-on-emulator-images/mac/01-virtual-devices.png#lightbox)

通常您會選取 [Android\_Accelerated\_x86] 虛擬裝置來針對手機應用程式進行測試和偵錯。 如果這個預先設定的虛擬裝置符合您的需求 (亦即符合您應用程式的目標 API 層級)，請跳至[啟動模擬器](#launching)以開始在模擬器中執行您的應用程式。 (如果您不熟悉 Android API 層級，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md))。

-----

## <a name="editing-virtual-devices"></a>編輯虛擬裝置

若要修改虛擬裝置 (或建立新的)，您必須使用 [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)。

<a name="launching" />

## <a name="launching-the-emulator"></a>啟動模擬器

在 Visual Studio 頂端附近有一個下拉式功能表，可用來選取 [偵錯] 或 [發行] 模式。 選擇 [偵錯] 會讓偵錯工具在應用程式啓動後連接至在模擬器內執行的應用程式處理序。 選擇 [發行] 模式會停用偵錯工具 (但您仍然可以執行應用程式並使用記錄陳述式偵錯)。 從裝置下拉式功能表中選擇虛擬裝置之後，請選取 [偵錯] 或 [發行] 模式，然後按一下 [播放] 按鈕來執行應用程式：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![[偵錯] 和 [發行] 模式、[播放] 按鈕](debug-on-emulator-images/win/17-debug-release-sml.png)](debug-on-emulator-images/win/17-debug-release.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![[偵錯] 和 [發行] 模式、[播放] 按鈕](debug-on-emulator-images/mac/16-debug-release-sml.png)](debug-on-emulator-images/mac/16-debug-release.png#lightbox)

-----

在模擬器啟動之後，Xamarin.Android 就會將應用程式部署至模擬器。 模擬器會以所設定的虛擬裝置映像來執行應用程式。 Android Emulator 的螢幕擷取畫面範例如下所示。 在此範例中，模擬器執行的是稱為 **MyApp** 的空白應用程式：

![執行空白應用程式的模擬器](debug-on-emulator-images/emulator-running.png)

您可以讓模擬器保持執行：無須在每次啓動執行應用程式時關閉它再重新啟動。 第一次在模擬器中執行 Xamarin.Android 應用程式時，會安裝目標 API 層級的 Xamarin.Android 共用執行階段，接著再安裝應用程式。 安裝執行階段可能需要一些時間，因此請耐心等候。 只有在將第一個 Xamarin.Android 應用程式部署至模擬器時，才會安裝執行階段 &ndash; 後續部署速度會較快，因為要複製到模擬器的只有應用程式。

<a name="quick-boot" />

## <a name="quick-boot"></a>快速開機

Android Emulator 的較新版本包含稱為_快速開機_的功能，只要幾秒鐘即可啟動模擬器。 當您關閉模擬器時，它會拍攝虛擬裝置狀態的快照，以便重新啟動時可以快速從該狀態還原。
您需要下列項目才能存取這項功能：

- Android 模擬器 27.0.2 版或更新版本
- Android SDK Tools 26.1.1 版或更新版本

安裝好上述模擬器和 SDK Tools 版本之後，預設會啟用快速開機功能。 

虛擬裝置第一次冷開機不會提升速度，因為尚未建立快照集：

![冷開機螢幕擷取畫面](debug-on-emulator-images/cold-boot.png)

當您結束模擬器時，快速開機會儲存模擬器狀態的快照集：

![於關機時儲存狀態](debug-on-emulator-images/saving-state.png)

後續的虛擬裝置啟動會比較快，因為模擬器只還原您關閉模擬器時的狀態。

![於重新啟動時載入狀態](debug-on-emulator-images/loading-state.png)

## <a name="troubleshooting"></a>疑難排解

如需常見模擬器問題的秘訣和因應措施，請參閱 [Android Emulator Troubleshooting](~/android/get-started/installation/android-emulator/troubleshooting.md) (Android Emulator 疑難排解)。

## <a name="summary"></a>總結

本指南說明設定 Android Emulator 以執行並測試 Xamarin.Android 應用程式的程序。 它描述使用預先設定之虛擬裝置啟動模擬器的步驟，並提供從 Visual Studio 將應用程式部署至模擬器的步驟。 

如需有關使用 Android Emulator 的詳細資訊，請參閱下列 Android Developer 主題：

- [在螢幕上瀏覽](https://developer.android.com/studio/run/emulator.html#navigate)

- [在模擬器中執行基本工作](https://developer.android.com/studio/run/emulator.html#tasks)

- [使用延伸控制項、設定及說明](https://developer.android.com/studio/run/emulator.html#extended)

- [使用快速開機執行模擬器](https://developer.android.com/studio/run/emulator#quickboot)
