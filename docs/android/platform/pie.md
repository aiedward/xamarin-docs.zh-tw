---
title: Android 9 Pie
description: 如何使用 Xamarin 開始開發適用于 Android 9 的應用程式圓形圖。
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 6f192e9171a9da2e2ef7e0478b5bb6838676faee
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453437"
---
# <a name="android-pie-features"></a>Android 圓形圖功能

_如何使用 Xamarin 開始開發適用于 Android 9 的應用程式圓形圖。_

Google 現在提供[Android 9 圓形圖](https://developer.android.com/about/versions/pie/)。 這一版提供許多新功能和 Api，其中有許多都是在最新的 Android 裝置中利用新的硬體功能的必要專案。

![Android 圓形圖影像](pie-images/01-android-p-logo.png)

本文的結構是協助您開始開發適用于 Android 的 Xamarin Android 應用程式。 它說明如何安裝必要的更新、設定 SDK，以及準備模擬器或裝置進行測試。 它也提供 Android 圓形圖中新功能的概要說明，並提供範例原始程式碼，說明如何使用一些重要的 Android 圓形圖功能。

Xamarin. Android 9.0 提供 Android 圓形圖的支援。 如需適用于 Android 圓形圖的 Xamarin Android 支援詳細資訊，請參閱 [Android P Developer Preview 3](/xamarin/android/release-notes/9/9.0/#android-p-dp1) 版本資訊。

## <a name="requirements"></a>需求

在 Xamarin 應用程式中使用 Android 圓形圖功能需要下列清單：

- **Visual Studio** &ndash; 建議使用 Visual Studio 2019。
    如果您使用 Visual Studio 2017，請在 Windows update 上 Visual Studio 2017 15.8 版或更新版本。 在 macOS 上，更新至適用于 Mac 7.6 版或更新版本的 Visual Studio 2017。

- **Xamarin** . android &ndash; xamarin. android 9.0.0.17 或更新版本必須隨 Visual Studio 安裝 (Xamarin。 Android 會在 **使用 .net** 工作負載) 的行動裝置開發過程中自動安裝。

- **JAVA 開發人員套件** &ndash; Xamarin Android 9.0 開發需要 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (，您也可以嘗試 Microsoft [OpenJDK](~/android/get-started/installation/openjdk.md)) 發佈的預覽。 JDK8 會在 **使用 .net** 工作負載的行動裝置開發過程中自動安裝。

- **Android SDK** &ndash; Android SDK API 28 或更新版本必須透過 Android SDK 管理員安裝。

## <a name="getting-started"></a>開始使用

若要開始使用 Xamarin 開發 Android 圓形圖應用程式，您必須先下載並安裝最新的工具和 SDK 套件，才能建立第一個 Android 圓形圖專案：

1. 建議使用 Visual Studio 2019。 如果您使用 Visual Studio 2017，請更新至 [Visual Studio 2017 15.8 版](/visualstudio/releasenotes/vs2017-relnotes) 或更新版本。 如果您使用 Visual Studio for Mac，請更新為 [Visual Studio 2017 （適用于 Mac 7.6 版](/visualstudio/releasenotes/vs2017-relnotes) 或更新版本）。

2. 透過 SDK 管理員安裝 **Android 圓形圖 (API 28) ** 套件和工具。

3. 建立以 **android 9.0**為目標的新 Xamarin android 專案。

4. 設定用來測試 Android 圓形圖應用程式的模擬器或裝置。

下列各節將說明每個步驟：

### <a name="update-visual-studio"></a>更新 Visual Studio 2017

建議使用 Visual Studio 2019 來建立使用 Xamarin 的 Android 圓形圖應用程式。

如果您使用 Visual Studio 2017，請更新至 Visual Studio 2017 15.8 版或更新版本 (如需指示，請參閱 [更新 Visual Studio 2017 至最新版本](/visualstudio/install/update-visual-studio)) 。 在 macOS 上，更新為 Visual Studio 2017 for Mac 7.6 或更新版本 (如需指示，請參閱 [安裝和安裝 Visual Studio for Mac](/visualstudio/mac/installation)) 。

### <a name="install-the-android-sdk"></a>安裝 Android SDK

若要建立具有 Xamarin. Android 9.0 的專案，您必須先使用 Android SDK 管理員安裝適用于 Android 的 SDK 平臺 ** (API 層級 28) ** 或更新版本。

1. 啟動 SDK 管理員。 在 Visual Studio 中，按一下 [ **工具] > Android > Android SDK 管理員**。 在 Visual Studio for Mac 中，按一下 [ **工具] > SDK 管理員**]。

2. 在右下角，按一下齒輪圖示，然後選取 [ **儲存機制 > Google (不支援的) **：

    [![將存放庫設定為 Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. 安裝**Android** SDK 封裝（在 [**平臺**] 索引標籤中列為**Android SDK Platform 28** ） (如需使用 SDK 管理員的詳細資訊，請參閱[Android SDK 安裝程式](~/android/get-started/installation/android-sdk.md)) ：

    [![安裝 Android 圓形圖套件](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. 如果您使用模擬器，請建立支援 **API 層級 28**的虛擬裝置。 如需建立虛擬裝置的詳細資訊，請參閱 [使用 Android 裝置管理員管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md)。

### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin. Android 專案

建立新的 Xamarin. Android 專案。 如果您是使用 Xamarin 進行 Android 開發的新手，請參閱 [Hello，Android](~/android/get-started/hello-android/index.md) 以瞭解如何建立 Xamarin. android 專案。

當您建立 Android 專案時，您必須將版本設定設定為以 Android 9.0 或更新版本為目標。 例如，若要以 Android 圓形圖的專案為目標，您必須將專案的目標 Android API 層級設定為 **android 9.0** (API 28) 。 建議您也將目標 Framework 層級設定為 API 28 或更新版本。 如需設定 Android API 層級的詳細資訊，請參閱 [瞭解 ANDROID Api 層級](~/android/app-fundamentals/android-api-levels.md)。

### <a name="configure-a-device-or-emulator"></a>設定裝置或模擬器

如果您使用的是實體裝置，例如結點或圖元，您可以遵循 [適用于結點和圖元裝置的原廠映射](https://developers.google.com/android/images)中的指示，將裝置更新為 Android 圓形圖。

如果您使用模擬器，請建立 API 層級28的虛擬裝置，然後選取以 x86 為基礎的映射。 如需使用 Android 裝置管理員來建立和管理虛擬裝置的詳細資訊，請參閱 [使用 Android 裝置管理員管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md)。
如需使用 Android 模擬器進行測試和偵錯工具的詳細資訊，請參閱 [Android Emulator 上的調試](~/android/deploy-test/debugging/debug-on-emulator.md)程式。

## <a name="new-features"></a>新功能

Android 圓形圖引進了各種新功能。 其中一些新功能的目的是要利用最新 Android 裝置所提供的新硬體功能，而有些則是設計來進一步增強 Android 使用者體驗：

- **顯示切除支援** &ndash; 提供 Api，可在較新的 Android 裝置上尋找位於畫面頂端的 _剪切_ 位置和圖形。

- **通知增強功能** &ndash; 通知訊息現在可以顯示影像，而新的 `Person` 類別會用來簡化對話參與者。

- **室內定位** &ndash; WiFi 往返時間通訊協定的平臺支援，讓應用程式可以使用 WiFi 裝置在室內設定中進行導覽。

- **多攝影機支援** &ndash; 提供從多個實體攝影機同時存取資料流程的功能， (例如雙重前端和雙回攝影機) 。

下列各節會強調這些功能，並提供簡短的程式碼範例，協助您在應用程式中開始使用這些功能。

### <a name="display-cutout-support"></a>顯示切除支援

有許多較新的 Android 裝置（具有邊緣對邊緣畫面）會在攝影機和喇叭的顯示器上方顯示 (或「凹槽」 ) 的 *顯示切除* 。
下列螢幕擷取畫面提供切除的模擬器範例：

[![模擬切除的 Android 模擬器](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

為了管理您的應用程式視窗如何在具有顯示切除的裝置上顯示其內容，Android 圓形圖已新增新的 [LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) 視窗版面配置屬性。 這個屬性可以設定為下列其中一個值：

- [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; 視窗絕對不能與裁剪區域重迭。

- [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; 視窗可延伸至裁剪區域，但只會在螢幕的短邊。 

- [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; 如果分割區包含在系統列內，則可以將視窗延伸至裁剪區域。

例如，若要防止應用程式視窗與切除區域重迭，請將版面配置裁剪模式設定為 [ *永不*]： 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

下列範例提供這些切除模式的範例。 左邊的第一個螢幕擷取畫面是處於非全螢幕模式的應用程式。 在中心螢幕擷取畫面中，應用程式會進入全螢幕，並 `LayoutInDisplayCutoutMode` 設定為 `LayoutInDisplayCutoutModeShortEdges` 。 請注意，應用程式的白色背景會延伸至顯示裁剪區域：

[![在模擬器中顯示切除模式的範例](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

在右上方 (上方的最後一個螢幕擷取畫面中) ， `LayoutInDisplayCutoutMode` 會在 `LayoutInDisplayCutoutModeShortNever` 進入全螢幕之前設定為。
請注意，應用程式的白色背景不允許延伸至顯示的裁剪區域。

如果您需要有關裝置上的切除區域的詳細資訊，您可以使用新的 [DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html) 類別。 `DisplayCutout` 表示無法用來顯示內容的顯示區域。 您可以使用這項資訊來取出剪切的位置和圖形，讓您的應用程式不會嘗試在這個非功能性區域中顯示內容。

如需 Android P 中新的切除功能的詳細資訊，請參閱 [顯示切除支援](https://developer.android.com/about/versions/pie/android-9.0#cutout)。

### <a name="notifications-enhancements"></a>通知增強功能

Android 圓形圖引進下列增強功能來改善訊息體驗：

-  (在   [Android Oreo](~/android/platform/oreo.md) 中引進的通知通道) 現在支援封鎖通道群組。

- 通知系統有三個新的不打擾類別 (將警示、系統音效和媒體來源的優先順序) 。 此外，還有七個新的不打擾模式可用來抑制視覺中斷 (例如徽章、通知燈、狀態列外觀，以及啟動全螢幕活動) 。

- 已加入新的 [Person](https://developer.android.com/reference/android/app/Person.html) 類別來代表訊息的寄件者。 使用這個類別有助於藉由識別參與交談 (（包括其虛擬人偶和 Uri) ）來優化每個通知的呈現。

- 通知現在可顯示影像。 

下列範例說明如何使用新的 Api 來產生包含影像的通知。 在以下螢幕擷取畫面中，會張貼文字通知，並在後面加上內嵌影像的通知。 展開通知時 (如右側) 所示，會顯示第一個通知的文字，並放大第二個通知中內嵌的影像：

[![具有影像的範例通知](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

下列範例說明如何在 Android 圓形圖通知中包含影像，並示範如何使用新的 `Person` 類別：

1. 建立 `Person` 代表寄件者的物件。 例如，寄件者的名稱和圖示會包含在 `fromPerson` ：

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. 建立 `Notification.MessagingStyle.Message` ，其中包含要傳送的影像，並將影像傳遞給新的 [MessagingStyle](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri) 方法。
   例如：

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. 將訊息新增至 `Notification.MessagingStyle` 物件。 例如：

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

如需建立通知的詳細資訊，請參閱 [本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。

### <a name="indoor-positioning"></a>室內定位

Android 圓形圖提供 IEEE 802.11 mc 的支援 (也稱為 _Wifi 往返時間_ 或 _wifi RTT_) ，可讓應用程式偵測到一或多個 wi-fi 存取點的距離。 使用這項資訊，您的應用程式可能會利用 *室內定位* ，精確度為一到兩個計量。 在提供 IEEE 801.11 mc 硬體支援的 Android 裝置上，您的應用程式可以提供流覽功能，例如智慧型設備的位置控制，或透過商店的輪流指示：

[![使用 WiFi RTT 的室內流覽範例](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

新的 [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) 類別和數個協助程式類別提供測量與 wi-fi 裝置距離的方法。 如需 Android P 中所引進之室內定位 Api 的詳細資訊，請參閱[android .net。](https://developer.android.com/reference/android/net/wifi/rtt/package-summary)

### <a name="multi-camera-support"></a>多攝影機支援

許多較新的 Android 裝置都有雙重前端和/或雙回相機，適用于這類功能，例如身歷聲視覺、增強的視覺效果，以及改良的縮放功能。 Android P 引進了新的 [多攝影機](https://developer.android.com/about/versions/pie/android-9.0#camera) API，讓您的應用程式可以使用 *邏輯攝影機* (或由兩個或多個實體攝影機所支援的 *邏輯多攝影機*) 。
若要判斷裝置是否支援邏輯多攝影機，您可以查看裝置上每個相機的功能，看看它是否支援 [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA)。

Android 圓形圖也包含新的 [>sessionconfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) 類別，可用來協助減少初始捕獲期間的延遲，並免除啟動和啟動相機串流的需求。

如需 Android P 中多攝影機支援的詳細資訊，請參閱 [多攝影機支援和攝影機更新](https://developer.android.com/about/versions/pie/android-9.0#camera)。

### <a name="other-features"></a>其他功能

此外，Android 圓形圖還支援數個其他新功能：

- 新的 [AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) 類別，可以用來繪製和顯示動畫影像。

- 取代的新 [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) 類別 `BitmapFactory` 。 `ImageDecoder` 可以用來解碼 `AnimatedImageDrawable` 。

- 支援 HDR (高動態範圍) 影片和 HEIF (高效率的影像檔案格式) 映射。

- [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)已增強，可更智慧地處理網路相關的作業。 [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters)類別的新[GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29)方法會傳回最適合用來執行指定工作之任何網路要求的網路。

如需最新 Android 圓形圖功能的詳細資訊，請參閱 [android 9 功能和 api](https://developer.android.com/about/versions/pie/android-9.0)。

## <a name="behavior-changes"></a>行為變更

當目標 Android 版本設為 API 層級28時，即使您未實行上述的新功能，還是會有數個平臺變更會影響您的應用程式行為。 下列清單是這些變更的簡短摘要：

- 應用程式現在必須先要求前景許可權，才能使用前景服務。

- 如果您的應用程式有一個以上的進程，它無法跨進程共用單一的  [web](xref:Android.Webkit.WebView) 程式資料目錄。

- 不再允許依路徑直接存取另一個應用程式的資料目錄。

如需以 Android P 為目標之應用程式的行為變更的詳細資訊，請參閱 [行為變更](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps)。

## <a name="sample-code"></a>範例程式碼

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) 是適用于 Android 的 Xamarin android 範例應用程式，示範如何設定顯示的圖樣模式、如何使用新的 `Person` 類別，以及如何傳送包含影像的通知。

## <a name="summary"></a>摘要

本文介紹 Android 圓形圖，並說明如何安裝和設定適用于 Xamarin 的 Android 開發的最新工具和套件。 它概述了 Android 圓形圖中可用的主要功能，其中有數個功能的範例原始程式碼。
其中包含 API 檔和 Android 開發人員主題的連結，可協助您開始建立適用于 Android 的應用程式圓形圖。 它也會反白顯示可能會影響現有應用程式的最重要 Android 圓形圖行為變更。

## <a name="related-links"></a>相關連結

- [Android 9 Pie](https://developer.android.com/about/versions/pie/)