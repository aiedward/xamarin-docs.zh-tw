---
title: Android 9 Pie
description: 如何開始使用 Xamarin.Android 的 Android 9 圓形圖中開發應用程式。
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: e08324d5a905639e7ecd59bc4c7a5c0f02d0adfe
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217936"
---
# <a name="android-pie-features"></a>Android 的圓形圖功能

_如何開始使用 Xamarin.Android 的 Android 9 圓形圖中開發應用程式。_

[Android 的 9 圓形圖](https://developer.android.com/about/versions/pie/)現在可從 Google。 許多新功能和 Api 可在此版本中，並有許多都是必要的最新的 Android 裝置利用新的硬體功能。

![Android 的圓形圖英雄影像](pie-images/01-android-p-logo.png)

這篇文章是結構化，以協助您開始在 Android 圓形圖開發 Xamarin.Android 應用程式。 它會說明如何安裝必要的更新、 設定 SDK，以及準備的模擬器或裝置上的進行測試。 它也概述 Android 圓形圖中的新功能，並提供說明如何使用一些關鍵的 Android 圓形圖功能的範例原始程式碼。

Xamarin.Android 9.0 提供支援 Android 的圓形圖。 如需 Android 圓形圖的 Xamarin.Android 支援的詳細資訊，請參閱[Android P Developer Preview 3](https://docs.microsoft.com/xamarin/android/release-notes/9/9.0/#android-p-dp1)版本資訊。

## <a name="requirements"></a>需求

下列清單，才能在以 Xamarin 為基礎的應用程式中使用 Android 的圓形圖功能：

- **Visual Studio** &ndash;建議使用 Visual Studio 2019。
    如果您使用 Visual Studio 2017，在 Visual Studio 2017 15.8 或更新版本的 Windows 更新。 在 macOS 上，更新 Visual Studio 2017 for Mac 7.6 或更新版本。

- **Xamarin.Android** &ndash; Xamarin.Android 9.0.0.17 或更新版本必須安裝 Visual studio (Xamarin.Android 自動安裝的一部分**使用.NET 進行行動開發**工作負載)。

- **Java Developer Kit** &ndash; Xamarin Android 9.0 開發需要[JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (或者，您可以嘗試的 microsoft 的預覽[OpenJDK](~/android/get-started/installation/openjdk.md))。 JDK8 會自動安裝的一部分**使用.NET 進行行動開發**工作負載。

- **Android SDK** &ndash;透過 Android SDK 管理員必須安裝 Android SDK API 28 或更新版本。

## <a name="getting-started"></a>使用者入門

若要開始開發使用 Xamarin.Android 的 Android 圓形圖應用程式，您必須下載並安裝最新工具和 SDK 封裝，才能建立第一個圓形圖 Android 專案：

1. 建議使用 Visual Studio 2019。 如果您使用 Visual Studio 2017，更新[Visual Studio 2017 版本 15.8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes)或更新版本。 如果您使用 Visual Studio for Mac，更新[Visual Studio 2017 for Mac 版本 7.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes)或更新版本。

2. 安裝**Android 圓形圖 (API 28)** 套件和工具透過 SDK 管理員。

3. 建立新的 Xamarin.Android 專案目標**Android 9.0**。

4. 設定模擬器或裝置上的進行測試 Android Pie 應用程式。

下列各節說明每個步驟：

### <a name="update-visual-studio"></a>更新 Visual Studio 2017

Visual Studio 2019 建議用於建置使用 Xamarin Android 圓形圖應用程式。

如果您使用 Visual Studio 2017，更新至 Visual Studio 2017 15.8 或更新版本 (如需相關指示，請參閱 <<c0> [ 最新版本的更新 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio))。 在 macOS 上，更新至 Visual Studio 2017 for Mac 7.6 或更新版本 (如需相關指示，請參閱 <<c0> [ 安裝程式並安裝 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation))。

### <a name="install-the-android-sdk"></a>Android SDK 安裝

Xamarin.Android 9.0 建立專案時，您必須先使用 Android SDK 管理員安裝的 SDK 平台**Android 圓形圖 （API 層級 28）** 或更新版本。

1. 啟動 SDK 管理員。 在 Visual Studio 中，按一下**工具 > Android > Android SDK 管理員**。 在 Visual Studio for Mac 中，按一下**工具 > SDK 管理員**。

2. 在右下角，按一下齒輪圖示並選取**存放庫 > Google （不支援）**:

    [![將存放庫設定為 Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. 安裝**Android 圓形圖**SDK 封裝，它會列為**Android SDK 平台 28**中**平台** 索引標籤 （如需使用 SDK 管理員的詳細資訊，請參閱[Android SDK 安裝程式](~/android/get-started/installation/android-sdk.md)):

    [![封裝安裝 Android 的圓形圖](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. 如果您正使用模擬器，建立虛擬裝置支援**API 層級 28**。 如需建立虛擬裝置的詳細資訊，請參閱[管理的虛擬裝置的 Android 裝置管理員](~/android/get-started/installation/android-emulator/device-manager.md)。

### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin.Android 專案

建立新的 Xamarin.Android 專案。 如果您是使用 Xamarin 進行 Android 開發的新手，請參閱[Hello，Android](~/android/get-started/hello-android/index.md)來了解如何建立 Xamarin.Android 專案。

當您建立 Android 專案時，您必須設定目標 Android 9.0 或更新版本的版本設定。 比方說，若要將專案目標為 Android 的圓形圖，您必須設定您的專案的目標 Android API 層級**Android 9.0** (API 28)。 建議您將您的目標架構層級 API 28 或更新版本。 如需有關設定 Android API 層級的詳細資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。


### <a name="configure-a-device-or-emulator"></a>設定裝置或模擬器

如果您使用實體裝置，例如 Nexus 或像素，您可以將裝置更新到 Android 圓形圖中的指示[Nexus 和像素裝置的原廠映像](https://developers.google.com/android/images)。

如果您正使用模擬器，請建立 API 層級 28 的虛擬裝置，並選取 x86 型映像。 使用 Android 裝置管理員來建立和管理虛擬裝置的相關資訊，請參閱[管理的虛擬裝置的 Android 裝置管理員](~/android/get-started/installation/android-emulator/device-manager.md)。
如需使用 Android 模擬器來進行測試和偵錯資訊，請參閱[在 Android 模擬器上偵錯](~/android/deploy-test/debugging/debug-on-emulator.md)。



## <a name="new-features"></a>新功能

Android 的圓形圖導入了各種新功能。 部分新功能被要利用新的硬體功能，提供最新的 Android 裝置，而其他專為進一步強化的 Android 使用者體驗：

-   **顯示空心支援**&ndash;提供的 Api，以尋找其位置和形狀_剪裁_較新的 Android 裝置上螢幕的頂端。

-   **通知的增強功能**&ndash;通知訊息可以現在會顯示映像和新`Person`類別用來簡化交談的參與者。

-   **室內定位** &ndash; WiFi Round 來回時間通訊協定，可讓使用 WiFi 裝置的瀏覽在室內的設定中的應用程式的平台支援。

-   **支援多重攝影機**&ndash;提供了功能存取資料流同時從多個實體的相機，（例如雙重前端和雙重後攝影機）。


下列各節會反白顯示這些功能，並提供簡短的程式碼範例，以協助您開始在您的應用程式中使用它們。

### <a name="display-cutout-support"></a>顯示空心支援

有許多較新的 Android 裝置與邊緣到邊緣畫面*顯示空心*（或 「 波陷 」） 頂端的網路攝影機和喇叭的顯示。
下列螢幕擷取畫面提供空心模擬器的範例：

[![模擬空心的 android 模擬器](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

若要管理您的應用程式視窗顯示裁剪的裝置上顯示其內容的方式，Android 圓形圖已新增新[LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode)視窗版面配置屬性。 此屬性可以設為下列值之一：

-   [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash;視窗絕不允許重疊的裁剪區域。

-   [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash;視窗可以擴充到裁剪區域，但是只在短邊緣的螢幕。 

-   [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash;視窗可以擴充到裁剪區域，如果剪裁包含在系統列。

例如，若要防止應用程式視窗重疊的裁剪區域，將配置裁剪模式設定為*從未*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

下列範例會提供這些裁剪模式的範例。 在左側的第一個螢幕擷取畫面是非全螢幕模式中的應用程式。 在 center 螢幕擷取畫面中，應用程式會進入全螢幕與`LayoutInDisplayCutoutMode`設定為`LayoutInDisplayCutoutModeShortEdges`。 請注意，應用程式的白色背景延伸至顯示裁剪區域：

[![範例顯示在模擬器中的空心模式](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

在最終的螢幕擷取畫面 (上方右側)，`LayoutInDisplayCutoutMode`設為`LayoutInDisplayCutoutModeShortNever`再進入全螢幕。
請注意，不允許應用程式的白色背景延伸至顯示裁剪區域。

如果您需要在裝置上的裁剪區域的詳細的資訊，您可以使用新[DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html)類別。 `DisplayCutout` 表示不能用來顯示內容的顯示區域。 若要擷取的位置和形狀裁剪，讓您的應用程式不會嘗試在此非功能性的區域中顯示內容，您可以使用這項資訊。

如需 Android P 中新的裁剪功能的詳細資訊，請參閱[顯示空心支援](https://developer.android.com/about/versions/pie/android-9.0#cutout)。



### <a name="notifications-enhancements"></a>通知的增強功能

Android 的圓形圖導入了下列增強功能來改善訊息經驗：

-   通知通道 (在中引進[Android Oreo](~/android/platform/oreo.md)) 現在支援 封鎖的通道群組。

-   通知系統有三個 （排列優先順序的警示、 系統聲音和媒體來源） 新 Do Not 打擾類別。 此外，有七個新 Do Not 打擾的模式，可用來隱藏 visual 中斷 （例如徽章、 通知燈號、 狀態 列的外觀，和啟動的全螢幕活動）。

-   新[人員](https://developer.android.com/reference/android/app/Person.html)類別已新增至代表訊息的寄件者。 使用這個類別可協助識別交談 （包括其虛擬人偶和 Uri） 中的相關人員，以最佳化每個通知的呈現。

-   通知現在會顯示映像。 

下列範例說明如何使用新的 Api 來產生包含映像的通知。 在下列螢幕擷取畫面中，文字通知張貼，且後面跟著內嵌影像的通知。 當通知會展開 （如同在右邊）、 在顯示的第一個通知文字和影像內嵌在會放大第二個通知：

[![使用映像的範例通知](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

下列範例說明如何在 Android 圓形圖通知中，包含影像，而且它會示範新的使用方式`Person`類別：

1. 建立`Person`物件，表示寄件者。 比方說，寄件者的名稱和圖示會包含在`fromPerson`:

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. 建立`Notification.MessagingStyle.Message`其中包含要傳送的映像傳遞至新的映像[Notification.MessagingStyle.Message.SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri)方法。
   例如：

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. 加入訊息`Notification.MessagingStyle`物件。 例如: 

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. 通知產生器中插入這種樣式。 例如: 

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. 將發行通知。 例如: 

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

如需建立通知的詳細資訊，請參閱 <<c0> [ 本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。


### <a name="indoor-positioning"></a>室內定位

Android 的圓形圖提供支援 IEEE 802.11mc (也稱為_WiFi Round 來回時間_或是_WiFi RTT_)，這可讓應用程式偵測到其中的距離或更多的 Wi-fi 存取點。 使用這項資訊，就可以針對您的應用程式，以善用*室內定位*，精確度為一到兩個計量。 IEEE 801.11mc 硬體支援的 Android 裝置，您的應用程式可以提供導覽功能，例如智慧型設備或透過市集開啟藉由開啟指示的位置為基礎的控制項：

[![使用 WiFi RTT 的室內巡覽的範例](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

新[WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager)類別和數個協助程式類別提供方法來測量到 Wi-fi 裝置之間的距離。 如需 Android P 中導入室內定位 Api 的詳細資訊，請參閱[Android.Net.Wifi.Rtt](https://developer.android.com/reference/android/net/wifi/rtt/package-summary)。


### <a name="multi-camera-support"></a>多重攝影機支援

許多較新的 Android 裝置有雙重前端和/或雙重後可用於這類功能是立體聲的願景、 增強的視覺效果，以及改善的縮放功能的相機。 Android P 導入了新[多重攝影機](https://developer.android.com/about/versions/pie/android-9.0#camera)可讓您的應用程式使用的 API*邏輯相機*(或*邏輯多相機*) 由二或多個備份實體的相機。
若要判斷是否裝置支援邏輯的多個相機，您可以查看每個裝置上的相機的功能，以查看它是否支援[RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA)。

Android 的圓形圖也包含新[SessionConfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html)可用來協助減少延遲，在初始擷取期間並不需要啟動，並啟動相機資料流的類別。

如需有關多重攝影機支援 Android P，請參閱 <<c0> [ 多重攝影機支援和相機更新](https://developer.android.com/about/versions/pie/android-9.0#camera)。


### <a name="other-features"></a>其他功能

此外，Android 圓形圖支援數個其他新功能：

-   新[AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html)類別，可用來繪製，並且顯示動畫的影像。

-   新[ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html)類別，它會取代`BitmapFactory`。 `ImageDecoder` 可以用來解碼`AnimatedImageDrawable`。

-   支援映像 HDR （高動態範圍） 視訊和 HEIF （高效率的影像檔案格式）。

-   [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)已經過增強，以更有效地處理網路相關的作業。 新[GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29)方法[JobParameters](https://developer.android.com/reference/android/app/job/JobParameters)類別會傳回最佳的網路，讓您指定作業執行的任何網路要求。

如需最新的 Android 圓形圖功能的詳細資訊，請參閱[Android 9 功能和 Api](https://developer.android.com/about/versions/pie/android-9.0)。


## <a name="behavior-changes"></a>行為變更

當目標 Android 版本設定為 API 層級 28 時，有數個會影響您的應用程式行為，即使您不想要實作上面所述的新功能的平台變更。 下列清單是這些變更的簡短摘要：

-  現在，應用程式必須要求前景權限，才能使用前景服務。

-  如果您的應用程式有多個處理程序，它不能共用單一[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)跨處理序的資料目錄。

-  不再允許直接存取另一個應用程式的資料目錄的路徑。

如需目標 Android P 的應用程式的行為變更的詳細資訊，請參閱[行為變更](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps)。


## <a name="sample-code"></a>範例程式碼

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) Xamarin.Android 範例應用程式為 Android 的圓形圖，示範如何設定顯示空心模式，如何使用新`Person`類別，以及如何傳送通知，其中包含映像。


## <a name="summary"></a>總結

這篇文章介紹 Android 的圓形圖，並說明如何安裝及設定最新的工具和適用於 Xamarin.Android 開發的封裝與 Android 的圓形圖。 它提供的許多功能的範例原始程式碼中 Android 的圓形圖，提供重要功能的概觀。
它包含 API 文件的連結，Android 開發人員主題，以協助您開始建立應用程式的 Android 的圓形圖。 它也以反白顯示的最重要的 Android 圓形圖行為變更可能會影響現有的應用程式。


## <a name="related-links"></a>相關連結

- [Android 9 Pie](https://developer.android.com/about/versions/pie/)
