---
title: Android 9 Pie
description: 如何開始使用 Xamarin 開發 Android 9 的應用程式。
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: 6475cd0f27e41321902b57dd28f59bfb250e0c8f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757454"
---
# <a name="android-pie-features"></a>Android 圓形圖功能

_如何開始使用 Xamarin 開發 Android 9 的應用程式。_

現在已可從 Google 取得[Android 9 圓形圖](https://developer.android.com/about/versions/pie/)。 此版本提供一些新功能和 Api，而且其中有許多都需要在最新的 Android 裝置上利用新的硬體功能。

![Android 圓形圖影像](pie-images/01-android-p-logo.png)

本文的結構是協助您開始開發適用于 Android 的 Xamarin Android 應用程式。 其中說明如何安裝必要的更新、設定 SDK，以及準備模擬器或裝置進行測試。 它也提供 Android 圓形圖中新功能的大綱，並提供範例原始程式碼，說明如何使用一些主要的 Android 圓形圖功能。

Xamarin. Android 9.0 提供 Android 圓形圖的支援。 如需適用于 Android 的 android 支援的詳細資訊，請參閱[Android P Developer Preview 3](https://docs.microsoft.com/xamarin/android/release-notes/9/9.0/#android-p-dp1)版本資訊。

## <a name="requirements"></a>需求

下列清單是在以 Xamarin 為基礎的應用程式中使用 Android 圓形圖功能的必要條件：

- **Visual Studio**&ndash;建議使用 Visual Studio 2019。
    如果您使用 Visual Studio 2017，請在 Windows update 上 Visual Studio 2017 15.8 或更新版本。 在 macOS 上，更新為 Visual Studio 2017 （適用于 Mac 7.6 版或更新版本）。

- 9\.0.0.17 或更新版本必須與 Visual Studio 一起安裝 **（xamarin.** android 會在使用 .net 工作負載進行行動裝置開發的過程中自動安裝）。 &ndash;

- **JAVA 開發人員套件**Xamarin Android 9.0 開發需要[JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (您也可以嘗試預覽 Microsoft 的 O [penJDK](~/android/get-started/installation/openjdk.md) 散發)。 &ndash; JDK8 會在**使用 .net**工作負載進行行動裝置開發的過程中自動安裝。

- **Android SDK**&ndash; Android SDK API 28 或更新版本必須透過 Android SDK 管理員安裝。

## <a name="getting-started"></a>使用者入門

若要開始使用 Xamarin 開發 Android 圓形圖應用程式，您必須先下載並安裝最新的工具和 SDK 套件，才能建立您的第一個 Android 圓形圖專案：

1. 建議使用 Visual Studio 2019。 如果您使用 Visual Studio 2017，請將更新為[Visual Studio 2017 15.8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) （含）以後版本。 如果您使用 Visual Studio for Mac，請更新至[Visual Studio 2017，以取得 Mac 7.6 版](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes)或更新版本。

2. 透過 SDK 管理員安裝**Android 圓形圖（API 28）** 套件和工具。

3. 建立以**android 9.0**為目標的新 Xamarin android 專案。

4. 設定用於測試 Android 圓形圖應用程式的模擬器或裝置。

下列各節將說明每個步驟：

### <a name="update-visual-studio"></a>更新 Visual Studio 2017

建議您使用 Visual Studio 2019 來建立使用 Xamarin 的 Android 圓形圖應用程式。

如果您使用 Visual Studio 2017，請更新為 Visual Studio 2017 15.8 或更新版本（如需指示，請參閱[將 Visual Studio 2017 更新為最新版本](https://docs.microsoft.com/visualstudio/install/update-visual-studio)）。 在 macOS 上，更新為 Mac 7.6 或更新版本 Visual Studio 2017 （如需指示，請參閱[設定和安裝 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation)）。

### <a name="install-the-android-sdk"></a>安裝 Android SDK

若要使用 Xamarin 9.0 建立專案，您必須先使用 Android SDK 管理員來安裝適用于 Android 的 SDK 平臺**圓形圖（API 層級28）** 或更新版本。

1. 啟動 SDK 管理員。 在 Visual Studio 中，按一下 **工具 > Android > Android SDK 管理員**。 在 Visual Studio for Mac 中，按一下 [**工具] > [SDK 管理員**]。

2. 在右下角，按一下齒輪圖示，然後選取 [存放庫] **> Google （不支援）** ：

    [![將存放庫設定為 Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. 安裝**Android 圓形圖**SDK 套件（在 [**平臺**] 索引標籤中列為**Android SDK Platform 28** ）（如需有關使用 SDK 管理員的詳細資訊，請參閱[Android SDK 安裝程式](~/android/get-started/installation/android-sdk.md)）：

    [![安裝 Android 圓形圖套件](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. 如果您使用模擬器，請建立支援**API 層級 28**的虛擬裝置。 如需建立虛擬裝置的詳細資訊，請參閱[使用 Android Device Manager 管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md)。

### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin Android 專案

建立新的 Xamarin Android 專案。 如果您不熟悉使用 Xamarin 進行 Android 開發的新手，請參閱[Hello，Android](~/android/get-started/hello-android/index.md)以瞭解如何建立 Xamarin. android 專案。

當您建立 Android 專案時，您必須將版本設定設定為以 Android 9.0 或更新版本為目標。 例如，若要以 Android 圓形圖的專案為目標，您必須將專案的目標 Android API 層級設定為**android 9.0** （API 28）。 建議您也將目標 Framework 層級設定為 API 28 或更新版本。 如需設定 Android API 層級的詳細資訊，請參閱[瞭解 ANDROID Api 層級](~/android/app-fundamentals/android-api-levels.md)。

### <a name="configure-a-device-or-emulator"></a>設定裝置或模擬器

如果您使用的是結點或圖元之類的實體裝置，您可以遵循[適用于結點和圖元裝置的原廠映射](https://developers.google.com/android/images)中的指示，將裝置更新為 Android 圓形圖。

如果您使用模擬器，請建立 API 層級28的虛擬裝置，並選取 x86 型映射。 如需使用 Android Device Manager 來建立和管理虛擬裝置的詳細資訊，請參閱[使用 Android Device Manager 管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md)。
如需有關使用 Android 模擬器進行測試和偵測的詳細資訊，請參閱[Android Emulator 上的調試](~/android/deploy-test/debugging/debug-on-emulator.md)程式。

## <a name="new-features"></a>新功能

Android 圓形圖引進了各種新功能。 其中一些新功能的目的是要利用最新的 Android 裝置所提供的新硬體功能，而有些則是設計來進一步增強 Android 使用者體驗：

- **顯示切除支援**提供 api，以在較新的 Android 裝置上尋找位於畫面頂端的切除位置和形狀。 &ndash;

- **通知增強功能**通知訊息現在可以顯示影像，而新`Person`的類別則用來簡化交談參與者。 &ndash;

- **室內定位**&ndash; Wifi 來回時間通訊協定的平臺支援，讓應用程式可以使用 wifi 裝置在室內設定中進行導覽。

- **多攝影機支援**&ndash;提供從多個實體相機（例如，雙前方和雙背相機）同時存取串流的功能。

下列各節將強調這些功能，並提供簡短的程式碼範例，以協助您開始在應用程式中使用它們。

### <a name="display-cutout-support"></a>顯示切除支援

許多具有邊緣到邊緣畫面的較新 Android 裝置在相機和喇叭顯示器的頂端會有*顯示切除*（或「槽口」）。
下列螢幕擷取畫面提供了切除的模擬器範例：

[![模擬切除的 Android 模擬器](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

若要管理應用程式視窗在顯示切除的裝置上顯示其內容的方式，Android 圓形圖已加入新的[LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode)視窗版面配置屬性。 這個屬性可以設為下列其中一個值：

- [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER)&ndash;視窗永遠不允許與切除區域重迭。

- [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES)&ndash;視窗可以擴充到裁剪區域，但只能延伸到螢幕的短邊緣。 

- [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT)&ndash;如果裁剪包含在系統列內，則可以將視窗延伸至裁剪區域。

例如，若要防止應用程式視窗與切除區域重迭，請將版面配置的圖樣模式設定為*永不*： 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

下列範例提供這些圖樣模式的範例。 左側的第一個螢幕擷取畫面是處於非全螢幕模式的應用程式。 在中央螢幕擷取畫面中，應用程式會變成 [全`LayoutInDisplayCutoutMode`螢幕] `LayoutInDisplayCutoutModeShortEdges`，並將設定為。 請注意，應用程式的白色背景會延伸到 [顯示] 裁剪區域：

[![在模擬器中顯示切除模式的範例](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

在最後一個螢幕擷取畫面（位於右邊）中， `LayoutInDisplayCutoutMode`會在進入`LayoutInDisplayCutoutModeShortNever`全螢幕之前設定為。
請注意，應用程式的白色背景不允許延伸至顯示裁剪區域。

如果您需要有關裝置上之切除區域的詳細資訊，您可以使用新的[DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html)類別。 `DisplayCutout`表示無法用來顯示內容的顯示區域。 您可以使用這項資訊來抓取切除的位置和形狀，讓您的應用程式不會嘗試在這個非功能性區域中顯示內容。

如需 Android P 中新的切除功能的詳細資訊，請參閱[顯示切除支援](https://developer.android.com/about/versions/pie/android-9.0#cutout)。

### <a name="notifications-enhancements"></a>通知增強功能

Android 圓形圖引進下列增強功能，以改善訊息體驗：

- 通知通道（在[Android Oreo](~/android/platform/oreo.md)中引進）現在支援封鎖通道群組。

- 通知系統有三個新的「不打擾」類別（排列警示、系統音效和媒體來源的優先順序）。 此外，還有七個新的「不打擾」模式可用來隱藏視覺中斷（例如徽章、通知燈、狀態列外觀，以及啟動全螢幕活動）。

- 已加入新的[Person](https://developer.android.com/reference/android/app/Person.html)類別來表示訊息的寄件者。 使用此類別有助於藉由識別與交談相關的人員（包括其虛擬人偶和 Uri）來優化每個通知的呈現。

- 通知現在可以顯示影像。 

下列範例說明如何使用新的 Api 來產生包含影像的通知。 在下列螢幕擷取畫面中，文字通知已張貼，後面接著具有內嵌影像的通知。 當通知展開（如右側所示）時，會顯示第一個通知的文字，並放大第二個通知中內嵌的影像：

[![具有影像的範例通知](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

下列範例說明如何在 Android 圓形圖通知中包含影像，並示範如何使用新`Person`的類別：

1. 建立代表寄件者的物件。`Person` 例如，寄件者的名稱和圖示包含在中`fromPerson`：

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. 建立, 其中包含要傳送的影像, 並將影像傳遞至新的  [MessagingStyle 方法](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri)。`Notification.MessagingStyle.Message`
   例如：

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. 將訊息新增至`Notification.MessagingStyle`物件。 例如：

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. 將此樣式插入通知產生器。 例如：

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. 發佈通知。 例如：

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

如需建立通知的詳細資訊，請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。

### <a name="indoor-positioning"></a>室內定位

Android 圓形圖提供 IEEE 802.11 mc （也稱為_Wifi 來回時間_或_wifi RTT_）的支援，讓應用程式能夠偵測到一個或多個 wi-fi 存取點的距離。 使用這項資訊，您的應用程式可能會利用一到兩個計量的精確度，使用*室內定位*。 在提供 IEEE 801.11 mc 硬體支援的 Android 裝置上，您的應用程式可以提供流覽功能，例如以位置為基礎的方式控制智慧型設備，或透過存放區的輪流指示：

[![使用 WiFi RTT 進行室內導覽的範例](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

新的[WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager)類別和數個 helper 類別提供測量 wi-fi 裝置距離的方法。 如需 Android P 中所引進之室內定位 Api 的詳細資訊，請參閱[android .net. Wifi](https://developer.android.com/reference/android/net/wifi/rtt/package-summary)。

### <a name="multi-camera-support"></a>多攝影機支援

許多較新的 Android 裝置都有兩個前端和/或雙背面的相機，適用于這類功能，包括身歷聲願景、增強的視覺效果，以及改良的縮放功能。 Android P 引進新的[多攝影機](https://developer.android.com/about/versions/pie/android-9.0#camera)API，讓您的應用程式可以使用由兩個或更多實體攝影機支援的*邏輯攝影機*（或*邏輯多攝影機*）。
若要判斷裝置是否支援邏輯多相機，您可以查看裝置上每個相機的功能，以查看是否支援[RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA)。

Android 圓形圖也包含新的[SessionConfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html)類別，可以用來協助減少初始捕獲期間的延遲，並消除啟動和啟動相機串流的需求。

如需 Android P 中多攝影機支援的詳細資訊，請參閱[多攝影機支援和相機更新](https://developer.android.com/about/versions/pie/android-9.0#camera)。

### <a name="other-features"></a>其他功能

此外，Android 圓形圖也支援其他幾項新功能：

- 新的[AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html)類別，可以用來繪製和顯示動畫影像。

- 取代`BitmapFactory`的新[ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html)類別。 `ImageDecoder`可以用來`AnimatedImageDrawable`解碼。

- 支援 HDR （高動態範圍）影片和 HEIF （高效率的影像檔案格式）影像。

- [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)已經過增強，可更聰明地處理網路相關的工作。 [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters)類別的新[GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29)方法會傳回最佳網路，以執行給定作業的任何網路要求。

如需最新 Android 圓形圖功能的詳細資訊，請參閱[android 9 功能和 api](https://developer.android.com/about/versions/pie/android-9.0)。

## <a name="behavior-changes"></a>行為變更

當目標 Android 版本設定為 API 層級28時，有數個平臺變更可能會影響應用程式的行為，即使您未執行上述的新功能。 下列清單是這些變更的簡短摘要：

- 應用程式現在必須先要求前景許可權，才能使用前景服務。

- 如果您的應用程式有一個以上的進程，它就無法跨進程共用單一的[web](xref:Android.Webkit.WebView)程式資料目錄。

- 不再允許依路徑直接存取另一個應用程式的資料目錄。

如需以 Android P 為目標之應用程式行為變更的詳細資訊，請參閱[行為變更](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps)。

## <a name="sample-code"></a>範例程式碼

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo)是適用于 android 的 Xamarin android 範例應用程式，可示範如何設定顯示裁剪模式、如何使用新`Person`的類別，以及如何傳送包含影像的通知。

## <a name="summary"></a>總結

本文介紹了 Android 圓形圖，並說明如何安裝和設定最新的 Xamarin Android 開發工具和套件。 其中提供 Android 圓形圖提供的主要功能總覽，以及其中幾項功能的範例原始程式碼。
其中包含 API 檔和 Android 開發人員主題的連結，可協助您開始建立 Android 的應用程式。 它也會反白顯示可能影響現有應用程式的最重要 Android 圓形圖行為變更。

## <a name="related-links"></a>相關連結

- [Android 9 圓形圖](https://developer.android.com/about/versions/pie/)
