---
title: '安裝和設定磨損作業系統 onXamarin '
description: 本文會逐步解說準備您的電腦和裝置以進行 Android 磨損開發所需的安裝步驟和設定詳細資料。 在本文結尾，您將會有一個整合到 Visual Studio for Mac 和/或 Microsoft Visual Studio 的已運作的 Xamarin Android 磨損安裝，而且您將可以開始建立您的第一個 Xamarin. Android 磨損應用程式。
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 83ec214ae1838959355e99322ce5a809ead004fa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028735"
---
# <a name="setup-and-installation"></a>設定與安裝

_本文會逐步解說準備您的電腦和裝置以進行 Android 磨損開發所需的安裝步驟和設定詳細資料。在本文結尾，您將會有一個整合到 Visual Studio for Mac 和/或 Microsoft Visual Studio 的已運作的 Xamarin Android 磨損安裝，而且您將可以開始建立您的第一個 Xamarin. Android 磨損應用程式。_

## <a name="requirements"></a>需求

若要建立以 Xamarin 為基礎的 Android 磨損應用程式，您必須具備下列各項：

- 需要**Visual Studio 或 Visual Studio for Mac** &ndash; Visual Studio 2017 的社區或更新版本。

- **&ndash; xamarin. android 4.17**或更新版本必須安裝，並以 Visual Studio 或 Visual Studio for Mac 設定。

- **Android SDK** Android SDK 5.0.1 （API 21）或更新版本必須透過 Android SDK 管理員進行安裝。

- 如果您要針對 API 層級24或更高版本進行開發，則**JAVA 開發人員套件**&ndash; Xamarin Android 開發需要[JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) （jdk 1.8 也支援早于24的 api 層級）。

如果您是特別針對 API 層級23或更早的版本進行開發，您可以繼續使用[JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 。

> [!IMPORTANT]
> Xamarin.Android 不支援 JDK 9。

## <a name="installation"></a>安裝

安裝 Xamarin 之後，請執行下列步驟，讓您準備好建立和測試 Android 磨損應用程式： 

1. 安裝必要的 Android SDK 和工具。
2. 設定測試裝置。
3. 建立您的第一個 Android 磨損應用程式。

下列各節將說明這些步驟。

### <a name="install-android-sdk-and-tools"></a>安裝 Android SDK 和工具 

啟動**Android SDK 管理員**： 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![如何在 Visual Studio 中啟動 Android SDK 管理員](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![如何在 Visual Studio for Mac 中啟動 Android SDK 管理員](installation-images/xs/sdk-menu.png)

-----

請確定您已安裝下列 Android SDK 和工具：

- Android SDK Tools v 24.0.0 或更高版本，以及
- Android 4.4 W （API20），或
- Android 5.0.1 （API21）或更高版本。

如果您未安裝最新的 SDK 和工具，請下載所需的 SDK 工具*和*API 位（您可能需要稍微滾動一點來尋找它們，&ndash; API 選擇如下所示）： 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![啟用 Android 5.0.1 元件的範例 SDK 管理員螢幕擷取畫面](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![啟用 Android 4.4 和5.0.1 元件的範例 SDK 管理員螢幕擷取畫面](installation-images/xs/sdk-select.png)

-----

## <a name="configuration"></a>Configuration

在您可以使用測試應用程式之前，您必須先設定 Android 磨損模擬器或實際的 Android 磨損裝置。 

### <a name="android-wear-emulator"></a>Android 磨損模擬器

您必須先使用**Google 模擬器管理員**來設定 Android 磨損 Android 虛擬裝置（AVD），才能使用 android 磨損模擬器：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![如何從 Visual Studio 啟動 Android Emulator 管理員](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![如何從 Visual Studio for Mac 啟動 Android Emulator 管理員](installation-images/xs/emulator-menu.png)

-----

如需設定 Android 磨損模擬器的詳細資訊，請參閱[在模擬器上進行 Android 磨損的偵錯工具](~/android/wear/deploy-test/debug-on-emulator.md)。

### <a name="android-wear-device"></a>Android 磨損裝置

如果您有 Android 磨損裝置（例如 Android 磨損 Smartwatch），您可以在此裝置上進行應用程式的偵錯工具，而不是使用模擬器。 如需使用磨損裝置進行開發的相關資訊，請參閱[在磨損裝置上進行 Debug](~/android/wear/deploy-test/debug-on-device.md)。

## <a name="create-your-first-android-wear-app"></a>建立您的第一個 Android 磨損應用程式

遵循[Hello，磨損](~/android/wear/get-started/hello-wear.md)的指示來建立您的第一個監看式應用程式。

## <a name="packaging-your-app"></a>封裝您的應用程式

Android 磨損應用程式一律會與隨附的 Android 手機應用程式一起散發。 

當您新增 Android 磨損應用程式作為主要 Android 應用程式的參考時，會自動假設為 Android 磨損專案，並會為您產生所有必要的 XML 和中繼資料。 此外，它也會驗證套件和版本號碼是否相符，讓您可以輕鬆地將應用程式寄送到 Google Play。 

若要深入瞭解封裝磨損應用程式，請參閱[使用封裝](~/android/wear/deploy-test/packaging.md)。

## <a name="related-links"></a>相關連結

- [SkeletonWear （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-skeletonwear)
