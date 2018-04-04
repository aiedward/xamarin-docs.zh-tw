---
title: 損耗裝置上偵錯
description: 本文說明如何偵錯損耗裝置上的 Xamarin.Android 損耗應用程式。
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 3f3143dcda4017bbabfbd34a58a40665beea6f75
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="debug-on-a-wear-device"></a>損耗裝置上偵錯

_本文說明如何偵錯損耗裝置上的 Xamarin.Android 損耗應用程式。_


## <a name="overview"></a>總覽

如果您有例如 Android 穿 Smartwatch 戴上的 Android 裝置，您可以執行應用程式，而不是使用模擬器的裝置上。 (如果您尚不熟悉的程序部署和執行的 Android 戴上的應用程式，請參閱[Hello，穿](~/android/wear/get-started/hello-wear.md)。)

## <a name="prepare-the-wear-device"></a>準備損耗裝置：

啟用偵錯 Android 戴上的裝置上使用下列步驟：

1.  開啟**設定**戴上的 Android 裝置上的功能表。

2.  捲動至底部的功能表，然後點選**有關**。

3.  7 次點選的組建編號。

4.  在**設定**功能表上，點選**開發人員選項**。

5.  確認**ADB 偵錯**已啟用。


## <a name="debugging-over-usb"></a>透過 USB 偵錯

如果損耗裝置有 USB 連接埠，可以將損耗裝置連接到您的電腦、 部署，以及執行/偵錯應用程式以想要的 Android 手機 (如需詳細資訊，請參閱[在裝置上偵錯](~/android/deploy-test/debugging/debug-on-device.md))。


## <a name="debugging-over-bluetooth"></a>透過藍牙偵錯

如果您損耗的裝置不需要的 USB 連接埠，您可以部署應用程式損耗裝置透過藍牙路由連接到您電腦的 Android 手機應用程式的偵錯輸出。 

### <a name="prepare-your-phone"></a>準備您的電話

若要準備您的電話使損耗裝置的藍芽連線使用下列步驟： 

1.  如果您尚未這樣做，設定您的電話 Xamarin.Android 開發中所述[設定註冊裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)。

2.  下載並安裝免費[Android 穿](https://play.google.com/store/apps/details?id=com.google.android.wearable.app)從 Google Play 商店應用程式。

### <a name="connect-the-device"></a>連接到裝置

將損耗裝置連接到您的手機，使用下列步驟：

1.  在電話上，將做為藍芽媒介 （上列設定），請啟動 Android 戴上的應用程式。 

2.  點選**設定**圖示。

3.  啟用**透過藍牙偵錯**。 您應該會看到有 Android 應用程式的螢幕上顯示下列狀態：

        Host: disconnected
        Target: connected

4.  透過 USB 將電話連接到您的電腦。 您在電腦上，輸入下列命令：

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    如果找不到連接埠 4444，您可以使用任何其他可用的通訊埠您具有存取權。 

    **請注意**： 如果您重新啟動 Visual Studio 或 Visual Studio for Mac，您必須執行這些命令一次安裝損耗裝置的連線。

5.  當損耗裝置會提示您時，請確認您允許**ADB 偵錯**。 在 Android 戴上應用程式時，您應該會看到狀態變更為：

        Host: connected
        Target: connected

6.  完成上述步驟之後，執行`adb devices`顯示的電話和 Android 戴上裝置狀態：

        List of devices attached
        127.0.0.1:4444    device
        019ad61df0a69399  device

此時，您可以部署應用程式到損耗裝置。

<a name="screenshots" />

### <a name="taking-screenshots"></a>擷取螢幕擷取畫面

您可以採取損耗裝置的螢幕擷取畫面，輸入下列命令： 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

複製到電腦的螢幕擷取畫面，輸入下列命令：

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

刪除裝置上的螢幕擷取畫面，輸入下列命令：

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```


### <a name="uninstalling-an-app"></a>解除安裝應用程式

您可以輸入下列命令解除安裝應用程式從損耗裝置：

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

例如，若要移除應用程式與封裝名稱`com.xamarin.weartest`，輸入下列命令：

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

如需偵錯 Android 戴上的裝置透過藍牙的詳細資訊，請參閱[透過藍牙偵錯](https://developer.android.com/training/wearables/apps/bt-debugging.html)。


## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>偵錯損耗具有的應用程式之附屬 phone 應用程式

Android 損耗應用程式會使用 Google Play 上的發佈附屬 Android 手機應用程式封裝 (如需詳細資訊，請參閱[使用封裝](~/android/wear/deploy-test/packaging.md))。 不過，您仍然開發損耗應用程式和其附屬應用程式分開。 當您放開您的應用程式，透過 Google Play 商店時，將會與 附屬應用程式一起封裝損耗應用程式，並盡可能自動安裝。

若要偵錯損耗應用程式與附屬應用程式： 

1.  建置並部署到行動電話的附屬應用程式。

2.  損耗專案上按一下滑鼠右鍵，並將它設為預設啟動專案。

3.  將損耗專案部署到穿戴裝置。

4.  執行和偵錯損耗應用裝置上。

 
## <a name="summary"></a>總結

本文說明如何設定從 Visual Studio 藍芽，損耗偵錯的有 Android 裝置以及如何偵錯損耗具有的應用程式之附屬 phone 應用程式。 它也會提供以偵錯藍芽損耗應用程式的常見偵錯秘訣。
