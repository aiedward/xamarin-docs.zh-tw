---
title: "設定與安裝"
description: "本文逐步解說的安裝步驟及準備您的電腦和裝置的 Android 戴上開發所需的組態詳細資料。 這篇文章結束時，您必須可運作的 Xamarin.Android 損耗安裝整合至 Visual Studio for Mac 及/或 Microsoft Visual Studio 中，及您就可以開始建置 Xamarin.Android 損耗應用程式第一次。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 012f563dcdaa70e33d641a4d8fb52df1622c260a
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="setup-and-installation"></a>設定與安裝

_本文逐步解說的安裝步驟及準備您的電腦和裝置的 Android 戴上開發所需的組態詳細資料。這篇文章結束時，您必須可運作的 Xamarin.Android 損耗安裝整合至 Visual Studio for Mac 及/或 Microsoft Visual Studio 中，及您就可以開始建置 Xamarin.Android 損耗應用程式第一次。_

## <a name="requirements"></a>需求

需要下列項目建立 Xamarin 為基礎的 Android 戴上的應用程式：

-   **Visual Studio 或 Visual Studio for Mac** &ndash;您如果您使用 Visual Studio 中，Visual Studio 2015 Professional 或更新版本。

-   **Xamarin.Android** &ndash; Xamarin.Android 4.17 或更新版本必須安裝並設定 Visual Studio 或 Visual Studio for mac。

-   **Android SDK** -5.0.1 (API 21) 的 Android SDK 安裝或更新版本必須是透過 Android SDK Manager 中。

-   **Java Developer Kit** &ndash; Xamarin Android 開發需要[JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)如果您正在開發的應用程式開發介面層級 24 或更大 （JDK 1.8 也支援應用程式開發介面層級早於 24）。

您可以繼續使用[JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)如果您是開發應用程式開發介面層級 23 專為或更早版本。

> [!IMPORTANT]
> Xamarin.Android 不支援 JDK 9。

## <a name="installation"></a>安裝

您已安裝 Xamarin.Android 之後，執行下列步驟，讓您準備好來建置及測試 Android 戴上的應用程式： 

1.  安裝必要的 Android SDK 和工具。
2.  設定測試裝置。
3.  建立第一個 Android 戴上應用程式。

下列各節說明這些步驟。


### <a name="install-android-sdk-and-tools"></a>安裝 Android SDK 和工具 

啟動**Android SDK Manager**: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![如何啟動 Visual Studio 中 Android SDK Manager](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![如何啟動 Visual Studio 中 Android SDK Manager for Mac](installation-images/xs/sdk-menu.png)

-----


請確定您具有下列的 Android SDK 和工具安裝：

* Android SDK 工具 v 24.0.0 或更高版本，以及
* Android 4.4W (API20)，或
* Android 5.0.1 (API21) 或更高版本。

如果您沒有最新的 SDK 和工具的安裝，下載所需的 SDK 工具*和*API 位元 (您可能需要捲動以尋找這些位元&ndash;應用程式開發介面選取項目如下所示): 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![啟用 Android 5.0.1 範例 SDK Manager 螢幕擷取畫面的元件](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![啟用 Android 4.4 和 5.0.1 範例 SDK Manager 螢幕擷取畫面的元件](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>組態

您可以使用之前測試應用程式，有 Android 模擬器或實際有 Android 裝置，您必須設定。 


### <a name="android-wear-emulator"></a>損耗 android 模擬器

您可以使用 Android 穿模擬器之前，您必須將 Android 穿 Android 虛擬裝置 (AVD) 使用**Google 模擬器管理員**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![如何啟動 Android 模擬器管理員，從 Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![如何啟動 Android 模擬器管理員，從 Visual Studio for Mac](installation-images/xs/emulator-menu.png)

-----

多個設定有 Android 模擬器的詳細資訊，請參閱[偵錯的模擬器上的 Android 穿](~/android/wear/deploy-test/debug-on-emulator.md)。


### <a name="android-wear-device"></a>Android 損耗裝置

如果您有例如 Android 穿 Smartwatch 戴上的 Android 裝置，您可以偵錯此裝置，而不是使用模擬器上的應用程式。 如需與損耗裝置開發的詳細資訊，請參閱[戴上的裝置上偵錯](~/android/wear/deploy-test/debug-on-device.md)。


## <a name="create-your-first-android-wear-app"></a>建立您的第一個 Android 損耗應用程式

請遵循[Hello，穿](~/android/wear/get-started/hello-wear.md)指示，來建置您的第一個監看式應用程式。


## <a name="packaging-your-app"></a>封裝您應用程式

Android 損耗應用程式一律會隨附附屬 Android 手機應用程式。 

當您將 Android 戴上應用程式新增為您主要的 Android 應用程式的參考它會自動假設為 Android 戴上的專案，並會為您產生所有必要的 XML 和中繼資料。 此外，它會確認封裝和版本號碼的符合，因此您可以輕鬆地交給您的應用程式 Google Play。 

若要深入了解封裝損耗應用程式，請參閱[使用封裝](~/android/wear/deploy-test/packaging.md)。


## <a name="related-links"></a>相關連結

- [SkeletonWear （範例）](https://developer.xamarin.com/samples/SkeletonWear/)
