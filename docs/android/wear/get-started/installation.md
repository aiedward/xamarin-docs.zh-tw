---
title: '安裝和設定 Wear OS onXamarin.Android '
description: 這篇文章會逐步引導完成安裝步驟和準備您的電腦和裝置進行 Android Wear 開發所需的組態詳細資料。 根據這篇文章結束時，就會看到可運作的 Xamarin.Android Wear 安裝整合至 Visual Studio for Mac 和/或 Microsoft Visual Studio 中，而您就能開始建置第一個 Xamarin.Android Wear 應用程式。
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 96fd6d32f37dd90422f05caf33cfda9a65683fd2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61308007"
---
# <a name="setup-and-installation"></a>設定與安裝

_這篇文章會逐步引導完成安裝步驟和準備您的電腦和裝置進行 Android Wear 開發所需的組態詳細資料。根據這篇文章結束時，就會看到可運作的 Xamarin.Android Wear 安裝整合至 Visual Studio for Mac 和/或 Microsoft Visual Studio 中，而您就能開始建置第一個 Xamarin.Android Wear 應用程式。_

## <a name="requirements"></a>需求

需要下列項目來建立以 Xamarin 為基礎的 Android 穿戴式應用程式：

-   **Visual Studio 或 Visual Studio for Mac** &ndash; Visual Studio 2017 Community 或更新版本。

-   **Xamarin.Android** &ndash; Xamarin.Android 4.17 或更新版本必須安裝並設定與 Visual Studio 或 Visual Studio for mac。

-   **Android SDK** -Android SDK 5.0.1 (API 21) 或更新版本必須安裝 Android SDK 管理員透過。

-   **Java Developer Kit** &ndash; Xamarin Android 開發需要[JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)如果您是開發的 API 層級 24 或更高 （JDK 1.8 也支援 API 層級早於 24）。

您可以繼續使用[JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)如果您是開發特別針對 API 層級 23 或更早版本。

> [!IMPORTANT]
> Xamarin.Android 不支援 JDK 9。

## <a name="installation"></a>安裝

您已安裝 Xamarin.Android 之後，執行下列步驟，以便您準備好建置及測試 Android Wear 的應用程式： 

1.  安裝必要的 Android SDK 和工具。
2.  設定測試裝置。
3.  建立第一個 Android Wear 應用程式。

下列各節說明這些步驟。


### <a name="install-android-sdk-and-tools"></a>安裝 Android SDK 和工具 

啟動**Android SDK 管理員**: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![如何啟動 Android SDK 管理員，在 Visual Studio 中](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![如何啟動 Android SDK 管理員，在 Visual Studio for Mac](installation-images/xs/sdk-menu.png)

-----


請確定您具有下列 Android SDK 及工具安裝：

* Android SDK Tools v 24.0.0 或更高版本，以及
* Android 4.4W (API20)，或
* Android 5.0.1 (API21) 或更高版本。

如果您沒有最新的 SDK 和工具安裝、 下載必要的 SDK 工具*並*API 位元 (您可能需要捲動以找出它們有點&ndash;API 選取如下所示): 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![啟用 Android 5.0.1 的螢幕擷取畫面範例 SDK Manager 元件](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![啟用 Android 4.4 和 5.0.1 的螢幕擷取畫面範例 SDK Manager 元件](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>組態

您可以使用之前測試應用程式，您必須設定 Android Wear 模擬器或實際的 Android Wear 裝置。 


### <a name="android-wear-emulator"></a>Android Wear 模擬器

您可以使用 Android Wear 模擬器之前，您必須設定 Android Wear Android 虛擬裝置 (AVD) 使用**Google 模擬器管理員**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![如何啟動 Android Emulator 管理員，從 Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![如何啟動 Android Emulator 管理員，從 Visual Studio for Mac](installation-images/xs/emulator-menu.png)

-----

如需有關設定 Android Wear 模擬器的詳細資訊，請參閱[偵錯的模擬器上的 Android Wear](~/android/wear/deploy-test/debug-on-emulator.md)。


### <a name="android-wear-device"></a>Android Wear 裝置

如果您有 Android Wear 的裝置，例如 Android Wear Smartwatch 時，您可以偵錯此裝置，而不是使用模擬器上的應用程式。 如需使用在 Wear 裝置進行開發，請參閱[穿戴式裝置上偵錯](~/android/wear/deploy-test/debug-on-device.md)。


## <a name="create-your-first-android-wear-app"></a>建立第一個 Android Wear 應用程式

請遵循[大家好，Wear](~/android/wear/get-started/hello-wear.md)指示，來建置您的第一個監看式應用程式。


## <a name="packaging-your-app"></a>封裝您的應用程式

Android wear 應用程式一律會使用隨附的 Android 手機應用程式散發。 

當您新增 Android Wear 應用程式做為參考您主要的 Android 應用程式時它會自動假設為 Android Wear 的專案，並會為您產生所有必要的 XML 和中繼資料。 此外，它會驗證套件和版本號碼的符合，因此您可以至 Google Play 輕鬆寄送您的應用程式。 

若要深入了解封裝 Wear 應用程式，請參閱[使用 封裝](~/android/wear/deploy-test/packaging.md)。


## <a name="related-links"></a>相關連結

- [SkeletonWear （範例）](https://developer.xamarin.com/samples/SkeletonWear/)
