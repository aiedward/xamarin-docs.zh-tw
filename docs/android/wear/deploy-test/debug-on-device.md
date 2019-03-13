---
title: 在 Wear 裝置上偵錯
description: 這篇文章說明如何在 Wear 裝置上的 Xamarin.Android Wear 應用程式進行偵錯。
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 232fcd1d369eba1daad170986f2e2c4c913a3649
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112447"
---
# <a name="debug-on-a-wear-device"></a>在 Wear 裝置上偵錯

_這篇文章說明如何在 Wear 裝置上的 Xamarin.Android Wear 應用程式進行偵錯。_


## <a name="overview"></a>總覽

如果您有 Android Wear 的裝置，例如 Android Wear Smartwatch 時，您可以執行應用程式，而不是使用模擬器在裝置上。 (如果您尚不熟悉部署和執行的程序的 Android Wear 的應用程式，請參閱[大家好，Wear](~/android/wear/get-started/hello-wear.md)。)

## <a name="prepare-the-wear-device"></a>準備在 Wear 裝置：

若要啟用偵錯 Android Wear 裝置上使用下列步驟：

1.  開啟**設定**Android Wear 裝置上的功能表。

2.  捲動至底部的功能表，然後點選**關於**。

3.  點選 組建編號 7 倍。

4.  在 **設定**功能表上，點選**開發人員選項**。

5.  確認**ADB 偵錯**已啟用。


## <a name="debugging-over-usb"></a>透過 USB 偵錯

如果您在 Wear 裝置有 USB 連接埠，您可以在 Wear 裝置連接到您的電腦、 部署，以及執行/偵錯應用程式，您會使用 Android 手機 (如需詳細資訊，請參閱 <<c0> [ 裝置上偵錯](~/android/deploy-test/debugging/debug-on-device.md))。


## <a name="debugging-over-bluetooth"></a>透過藍牙偵錯

如果您在 Wear 裝置沒有 USB 連接埠，您可以部署應用程式以在 Wear 裝置透過藍牙路由傳送至 Android 手機連接到您電腦的應用程式的偵錯輸出。 

### <a name="prepare-your-phone"></a>準備您的手機

若要準備您的手機使在 Wear 裝置的藍牙連線使用下列步驟： 

1.  如果您尚未這樣做，設定您手機上進行 Xamarin.Android 開發中所述[設定裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)。

2.  下載並安裝免費[Android Wear](https://play.google.com/store/apps/details?id=com.google.android.wearable.app)從 Google Play 商店的應用程式。

### <a name="connect-the-device"></a>將裝置連線

在 Wear 裝置連線到您的手機，使用下列步驟：

1.  在電話上，將會做為藍芽中繼 （上面所設定），啟動 Android Wear 應用程式。 

2.  點選**設定**圖示。

3.  啟用**偵錯透過藍牙**。 您應該會看到顯示 Android Wear 應用程式的畫面上的下列狀態：

        Host: disconnected
        Target: connected

4.  透過 USB 將電話連接到您的電腦。 您在電腦上，輸入下列命令：

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    如果找不到連接埠 4444，您可以使用任何其他可用的通訊埠具有存取權。 

    **請注意**： 如果您重新啟動 Visual Studio 或 Visual Studio for Mac，您必須執行下列命令一次是用來設定在 Wear 裝置的連線。

5.  當在 Wear 裝置會提示您時，請確認您允許**ADB 偵錯**。 在 Android Wear 應用程式時，您應該會看到狀態變更為：

        Host: connected
        Target: connected

6.  完成上述步驟之後，執行`adb devices`phone 與 Android 穿戴式裝置的狀態會顯示：

        List of devices attached
        127.0.0.1:4444    device
        019ad61df0a69399  device

此時，您可以將應用程式部署到在 Wear 裝置。

<a name="screenshots" />

### <a name="taking-screenshots"></a>螢幕擷取畫面

您可以採取 Wear 裝置的螢幕擷取畫面，輸入下列命令： 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

複製到您的電腦的螢幕擷取畫面，輸入下列命令：

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

刪除裝置上的螢幕擷取畫面中輸入下列命令：

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```


### <a name="uninstalling-an-app"></a>解除安裝應用程式

您可以輸入下列命令解除安裝應用程式從 wear 裝置：

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

例如，若要移除應用程式的封裝名稱`com.xamarin.weartest`，輸入下列命令：

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

如需偵錯 Android Wear 的裝置透過藍牙的詳細資訊，請參閱[偵錯透過藍牙](https://developer.android.com/training/wearables/apps/bt-debugging.html)。


## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>偵錯隨附電話應用程式與穿戴式應用程式

Android Wear 應用程式會使用隨附的 Android 手機應用程式的 Google Play 上發佈封裝 (如需詳細資訊，請參閱 <<c0> [ 使用 封裝](~/android/wear/deploy-test/packaging.md))。 不過，您仍然開發穿戴式應用程式和其附屬應用程式分開。 當您釋放您的應用程式，透過 Google Play 商店時，就會使用小幫手應用程式封裝並自動安裝 如果可能的話穿戴式應用程式。

若要偵錯穿戴式應用程式，使用隨附應用程式： 

1.  建置並部署到行動電話的小幫手應用程式。

2.  Wear 專案上按一下滑鼠右鍵，並將它設定為預設啟動專案。

3.  Wear 將專案部署到穿戴裝置。

4.  執行和偵錯穿戴式應用程式，在裝置上。

 
## <a name="summary"></a>總結

這篇文章說明如何設定從 Visual Studio 中透過藍芽、 Wear 偵錯的 Android Wear 裝置以及如何偵錯隨附電話應用程式與穿戴式應用程式。 它也會提供偵錯透過藍牙穿戴式應用程式的常見偵錯的祕訣。
