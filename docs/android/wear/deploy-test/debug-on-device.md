---
title: 在 Wear 裝置上偵錯
description: 本文說明如何在磨損裝置上進行 Xamarin. Android 磨損應用程式的偵錯工具。
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 9596ff34352337d892fc22df5651b2f78f8c33f6
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762166"
---
# <a name="debug-on-a-wear-device"></a>在 Wear 裝置上偵錯

_本文說明如何在磨損裝置上進行 Xamarin. Android 磨損應用程式的偵錯工具。_

## <a name="overview"></a>總覽

如果您有 Android 磨損裝置（例如 Android 磨損 Smartwatch），您可以在裝置上執行應用程式，而不是使用模擬器。 （如果您還不熟悉部署和執行 Android 磨損應用程式的流程，請參閱[Hello，磨損](~/android/wear/get-started/hello-wear.md)）。

## <a name="prepare-the-wear-device"></a>準備磨損裝置：

使用下列步驟在 Android 磨損裝置上啟用調試：

1. 開啟 Android 磨損裝置上的 [**設定**] 功能表。

2. 流覽至功能表底部，然後按一下 [**關於**]。

3. 請按組建編號7次。

4. 在 [**設定**] 功能表上，按一下 [**開發人員選項**]。

5. 確認已啟用**ADB 的調試**程式。

## <a name="debugging-over-usb"></a>透過 USB 進行調試

如果您的磨損裝置具有 USB 埠，您可以將磨損裝置連接到電腦、部署到該裝置，並執行/debug 應用程式，就像使用 Android 手機一樣（如需詳細資訊，請參閱[在裝置上的 debug](~/android/deploy-test/debugging/debug-on-device.md)）。

## <a name="debugging-over-bluetooth"></a>透過藍牙進行的調試

如果您的磨損裝置沒有 USB 埠，您可以將應用程式的 debug 輸出路由至連線到您電腦的 Android 手機，藉此將應用程式部署到透過藍牙的磨損裝置。 

### <a name="prepare-your-phone"></a>準備您的手機

使用下列步驟來準備您的手機，以進行與磨損裝置的藍牙連線： 

1. 如果您尚未這麼做，請設定您的手機以進行 Xamarin. Android 開發，如[設定裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)中所述。

2. 從 Google Play 商店下載並安裝免費的[Android 磨損](https://play.google.com/store/apps/details?id=com.google.android.wearable.app)應用程式。

### <a name="connect-the-device"></a>連接裝置

使用下列步驟將您的磨損裝置連接到您的手機：

1. 在將做為藍牙媒介的電話上（如上所設定），啟動 Android 磨損應用程式。 

2. 請按 [**設定**] 圖示。

3. 啟用透過藍牙進行的**調試**。 您應該會看到下列狀態顯示在 Android 磨損應用程式的畫面上：

    ```
    Host: disconnected
    Target: connected
    ```

4. 透過 USB 將電話連接到您的電腦。 在您的電腦上，輸入下列命令：

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    如果無法使用通訊埠4444，您可以使用任何其他可存取的可用埠。 

    > [!NOTE]
    > 如果您重新開機 Visual Studio 或 Visual Studio for Mac，則必須再次執行這些命令，以設定與磨損裝置的連線。

5. 當磨損裝置提示您時，請確認您允許**ADB 的調試**程式。 在 Android 磨損應用程式中，您應該會看到狀態變更為：

    ```
    Host: connected
    Target: connected
    ```

6. 完成上述步驟之後， `adb devices`執行會顯示電話和 Android 磨損裝置的狀態：

    ```
    List of devices attached
    127.0.0.1:4444    device
    019ad61df0a69399  device
    ```

此時，您可以將應用程式部署至磨損裝置。

<a name="screenshots" />

### <a name="taking-screenshots"></a>拍攝螢幕擷取畫面

您可以藉由輸入下列命令，來建立磨損裝置的螢幕擷取畫面： 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

輸入下列命令，將螢幕擷取畫面複製到您的電腦：

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

輸入下列命令，以刪除裝置上的螢幕擷取畫面：

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```

### <a name="uninstalling-an-app"></a>卸載應用程式

您可以輸入下列命令，從磨損裝置卸載應用程式：

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

例如，若要使用套件名稱`com.xamarin.weartest`移除應用程式，請輸入下列命令：

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

如需有關透過藍牙來檢查 Android 磨損裝置的詳細資訊，請參閱透過藍牙進行的[調試](https://developer.android.com/training/wearables/apps/bt-debugging.html)程式。

## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>使用隨附的電話應用程式來進行磨損應用程式的偵錯工具

Android 磨損應用程式會以隨附的 Android 手機應用程式封裝，以便在 Google Play 上散發（如需詳細資訊，請參閱[使用封裝](~/android/wear/deploy-test/packaging.md)）。 不過，您仍然會分別開發磨損應用程式及其附屬應用程式。 當您透過 Google Play 商店發行應用程式時，會將磨損應用程式與隨附應用程式封裝在一起，並在可能的情況下自動安裝。

若要使用隨附的應用程式來進行磨損應用程式的調試： 

1. 建立隨附的應用程式，並將其部署到手機。

2. 以滑鼠右鍵按一下 [磨損] 專案，並將它設定為預設的 [起始專案]。

3. 將磨損專案部署到穿戴式裝置。

4. 在裝置上執行並對磨損應用程式進行偵錯工具。

## <a name="summary"></a>總結

本文說明如何設定 Android 磨損裝置，以透過藍牙從 Visual Studio 進行磨損的調試，以及如何使用隨附的電話應用程式來對磨損應用程式進行程式。 此外，它也提供了常見的偵錯工具，可透過藍牙來對磨損應用程式進行調試。
