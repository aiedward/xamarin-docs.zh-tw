---
title: Android 9 Pie
description: 如何開始開發使用Xamarin.安卓的Android 9餅的應用程式。
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 0105b43116df697bc6688becb77298c236dfa601
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019878"
---
# <a name="android-pie-features"></a>安卓餅功能

_如何開始開發使用Xamarin.安卓的Android 9餅的應用程式。_

[Android 9餅圖](https://developer.android.com/about/versions/pie/)現在可從谷歌獲得。 此版本中提供了許多新功能和 API,其中許多功能和 API 都是利用最新 Android 設備中的新硬體功能所必需的。

![安卓餅英雄形象](pie-images/01-android-p-logo.png)

本文的結構是説明您開始開發 Xamarin.Android 應用程式,用於安卓餅。 它說明瞭如何安裝必要的更新、配置 SDK 以及準備模擬器或設備進行測試。 它還提供了 Android 餅圖中新功能的概述,並提供了示例原始程式碼,說明如何使用一些關鍵的 Android 餅圖功能。

Xamarin.Android 9.0 為安卓餅提供支援。 有關 Xamarin.Android 支援 Android 餅圖的詳細資訊,請參閱[Android P 開發人員預覽 3](https://docs.microsoft.com/xamarin/android/release-notes/9/9.0/#android-p-dp1)發行說明。

## <a name="requirements"></a>需求

在基於 Xamarin 的應用中使用 Android 餅圖功能需要以下清單:

- **建議**&ndash;使用 Visual Studio 視覺工作室 2019。
    如果您使用的是 Visual Studio 2017,則 Windows 上會更新至 Visual Studio 2017 版本 15.8 或更高版本。 在 macOS 上,針對 Mac 版本 7.6 或更高版本更新至 Visual Studio 2017。

- **Xamarin.Android** &ndash; Xamarin.Android 9.0.0.17 或更高版本必須安裝 Visual Studio(Xamarin.Android 作為**具有 .NET 工作負載的移動開發**的一部分自動安裝)。

- **Java開發人員工具組**&ndash;XamarinAndroid 9.0開發需要[JDK 8(](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或者你可以嘗試微軟的[OpenJDK](~/android/get-started/installation/openjdk.md)分發預覽)。 JDK8 作為移動開發中具有 **.NET**工作負載的一部分自動安裝。

- **Android SDK** &ndash; Android SDK API 28 或更高版本必須透過 Android SDK 管理器安裝。

## <a name="getting-started"></a>開始使用

要開始使用 Xamarin.Android 開發 Android 餅圖應用,您必須下載並安裝最新的工具和 SDK 套件,然後才能建立第一個 Android 餅圖專案:

1. 建議使用 Visual Studio 2019。 如果您使用的是 Visual Studio 2017,請更新至[Visual Studio 2017 版本 15.8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes)或更高版本。 如果您使用的是 Mac 的 Visual Studio,請更新至[Visual Studio 2017 的 Mac 版本 7.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes)或更高版本。

2. 通過 SDK 管理器安裝**Android 餅圖 (API 28)** 套件和工具。

3. 創建一個新的Xamarin.Android專案,目標是**Android9.0。**

4. 配置用於測試 Android 餅圖應用的模擬器或設備。

以下各節將介紹這些步驟:

### <a name="update-visual-studio"></a>更新 Visual Studio 2017

Visual Studio 2019 建議使用 Xamarin 構建 Android 餅圖應用。

如果您使用的是 Visual Studio 2017,請更新至 Visual Studio 2017 版本 15.8 或更高版本(有關說明,請參閱[將 Visual Studio 2017 更新到最新版本](https://docs.microsoft.com/visualstudio/install/update-visual-studio))。 在 macOS 上,針對 Mac 7.6 或更高版本更新至 Visual Studio 2017(有關說明,請參閱[為 Mac 設置和安裝可視化工作室](https://docs.microsoft.com/visualstudio/mac/installation))。

### <a name="install-the-android-sdk"></a>安裝 Android SDK

要使用 Xamarin.Android 9.0 創建項目,必須首先使用 Android SDK 管理器安裝**Android 餅圖(API 28 級)** 或更高版本的 SDK 平臺。

1. 啟動 SDK 管理員。 在視覺工作室中,單擊 **「工具> Android > Android SDK 管理器**。 在 Mac 的視覺工作室中,按下 **「工具> SDK 管理器**。

2. 在右下角,按下齒輪圖示並選擇 **「儲存庫> Google(不支援)):**

    [![儲存函式庫設定為 Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. 安裝**Android 餅圖**SDK 套件(在**平台**選項卡中列為**Android SDK 平臺 28)(** 有關使用 SDK 管理員的詳細資訊,請參閱 Android SDK[設定](~/android/get-started/installation/android-sdk.md)):

    [![安裝 Android 圓形圖包](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. 如果使用模擬器,請建立支援**API 級別 28**的虛擬裝置。 有關創建虛擬裝置的詳細資訊,請參閱使用[Android 裝置管理程式管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md)。

### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin.安卓專案

創建新的 Xamarin.安卓專案。 如果您是使用 Xamarin 的 Android 開發的新使用者,請參閱[Hello,Android,](~/android/get-started/hello-android/index.md)瞭解如何創建 Xamarin.Android 專案。

創建 Android 專案時,必須將版本設置配置為以 Android 9.0 或更高版本為目標。 例如,要針對 Android 餅圖的項目,必須將專案的目標 Android API 級別配置為**Android 9.0** (API 28)。 建議您還將目標框架級別設置為 API 28 或更高版本。 有關設定 Android API 等級的更多資訊,請參閱[瞭解 Android API 級別](~/android/app-fundamentals/android-api-levels.md)。

### <a name="configure-a-device-or-emulator"></a>設定裝置或模擬器

如果您正在使用物理設備(如 Nexus 或圖元),則可以按照[Nexus 和像素設備出廠圖像](https://developers.google.com/android/images)中的說明將設備更新為 Android 餅圖。

如果使用模擬器,請為 API 級別 28 創建虛擬裝置,然後選擇基於 x86 的圖像。 有關使用 Android 裝置管理員建立與管理虛擬裝置的資訊,請參閱使用[Android 裝置管理程式管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md)。
有關使用 Android 模擬器進行測試和除錯的資訊,請參閱 Android[模擬器上的除錯](~/android/deploy-test/debugging/debug-on-emulator.md)。

## <a name="new-features"></a>新功能

安卓餅推出了各種新功能。 其中一些新功能旨在利用最新的 Android 裝置提供的新硬體功能,而其他功能旨在進一步增強 Android 用戶體驗:

- **顯示切口支援**&ndash;提供 API,用於在較新的 Android 設備上查找螢幕頂部的_切口_的位置和形狀。

- **通知增強通知**&ndash;消息現在可以顯示圖像,並且使用`Person`新 類來簡化對話參與者。

- **室內定位**&ndash;平台支援 WiFi 往返時間協定,使應用能夠使用 WiFi 設備在室內設置中導航。

- **多攝像機支援**&ndash;提供從多個物理攝像機(如雙前置和雙背攝像機)同時訪問流的功能。

以下各節重點介紹這些功能,並提供簡短的代碼示例,以説明您在應用中開始使用它們。

### <a name="display-cutout-support"></a>顯示切口支援

許多帶有邊緣到邊緣螢幕的較新的 Android 設備在相機和揚聲器的顯示屏頂部有*一個顯示切口*(或"缺口」。)。
以下螢幕截圖提供切口的模擬器範例:

[![Android 模擬器模擬切口](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

為了管理應用視窗在具有顯示切口的設備上顯示其內容的方式,Android Pie 添加了一個新的[LayoutInDisplayCutout 模式](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode)視窗佈局屬性。 這個屬性可以設定為以下值之一:

- [佈局在顯示CutoutMode永遠不會](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER)&ndash;視窗永遠不會與切口區域重疊。

- [佈局在顯示CutoutMode短邊緣](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES)&ndash;視窗允許擴展到切口區域,但只能在螢幕的短邊緣。 

- [佈局 InDisplayCutoutMode 預設](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT)&ndash;如果切口包含在系統列中,則允許視窗擴展到切口區域。

例如,為了防止應用視窗與切口區域重疊,將佈局截止模式設定為*從不*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

以下示例提供了這些切口模式的範例。 左側的第一個螢幕截圖是處於非全屏模式的應用。 在中心螢幕擷取中,應用程式會將`LayoutInDisplayCutoutMode`全螢幕設定`LayoutInDisplayCutoutModeShortEdges`為 。 請注意,應用的白色背景延伸到顯示剪切區域:

[![模擬器中顯示剪切模式範例](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

在最後的螢幕截圖(右側的上方)中,`LayoutInDisplayCutoutMode`設置為在`LayoutInDisplayCutoutModeShortNever`全 屏之前。
請注意,不允許將應用的白色背景擴展到顯示剪切區域。

如果您需要有關設備上的剪切區域的更多詳細資訊,則可以使用新的[DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html)類。 `DisplayCutout`表示不能用於顯示內容的顯示區域。 您可以使用此資訊檢索切口的位置和形狀,以便應用不會嘗試在此非功能區域中顯示內容。

有關 Android P 中新的切口功能的詳細資訊,請參閱[顯示切口支援](https://developer.android.com/about/versions/pie/android-9.0#cutout)。

### <a name="notifications-enhancements"></a>通知增強功能

Android Pie 引入了以下增強功能,以改善消息傳遞體驗:

- 通知通道(在[Android Oreo](~/android/platform/oreo.md)中引入)現在支援阻止通道組。

- 通知系統有三個新的"請勿打擾"類別(確定警報、系統聲音和媒體源的優先順序)。 此外,還有七種新的"請勿打擾"模式可用於抑制視覺中斷(如徽章、通知燈、狀態欄外觀和全屏活動的啟動)。

- 已添加新[的 Person](https://developer.android.com/reference/android/app/Person.html)類以表示郵件的寄件者。 使用此類有助於透過識別參與對話的人(包括其頭像和 URI)來優化每個通知的呈現。

- 通知現在可以顯示圖像。 

下面的範例展示如何使用新的 API 生成包含影像的通知。 在以下螢幕截圖中,將發佈文本通知,然後發佈包含嵌入圖像的通知。 延伸通知時(如右側所示),將顯示第一個通知的文本,並放大嵌入在第二個通知中的影像:

[![帶影像的範例通知](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

下面的範例展示如何在 Android 餅圖通知中包含影像,並演示`Person`了新 類的用法:

1. 創建`Person`表示寄件人的物件。 例如,寄件者的名稱和圖示包含在`fromPerson`中 :

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. 創建包含`Notification.MessagingStyle.Message`要發送的圖像,將圖像傳遞到新的[通知.MessageStyle.Message.SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri)方法。
   例如：

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. 將消息添加到`Notification.MessagingStyle`物件。 例如：

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. 將此樣式插入通知生成器。 例如：

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

有關建立通知的詳細資訊,請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。

### <a name="indoor-positioning"></a>室內定位

Android Pie 支援 IEEE 802.11mc(也稱為_WiFi 往返時間_或_WiFi RTT),_ 這使得應用程式能夠檢測到一個或多個 Wi-Fi 接入點的距離。 使用此資訊,你的應用可以利用*室內定位*,精度為 1 到 2 米。 在為 IEEE 801.11mc 提供硬體支援的 Android 設備上,你的應用可以提供導航功能,例如基於位置的智慧設備控制或透過商店逐個轉向說明:

[![使用 WiFi RTT 的室內導覽範例](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

新的[WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager)類和多個幫助器類提供了測量到 Wi-Fi 設備距離的方法。 有關 Android P 中引入的室內定位 API 的更多資訊,請參閱[Android.Net.Wifi.Rtt](https://developer.android.com/reference/android/net/wifi/rtt/package-summary)。

### <a name="multi-camera-support"></a>多攝像機支援

許多較新的 Android 設備都有雙前置和/或雙背攝像頭,可用於立體視覺、增強的視覺效果和增強的變焦功能等功能。 Android P 引入了一個新的[多攝像機](https://developer.android.com/about/versions/pie/android-9.0#camera)API,使你的應用可以使用由兩個或多個物理攝像機支援*的邏輯攝像機*(或*邏輯多攝像機*)。
要確定裝置是否支援邏輯多攝像機,可以查看設備上每個攝像機的功能,以查看它是否支援[「請求能力」多攝像機](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA)。

Android Pie 還包括一個新的[Session 配置](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html)類,可用於説明減少初始捕獲期間的延遲,並無需啟動和啟動攝像機流。

有關 Android P 中的多攝像機支援的詳細資訊,請參閱[多攝像機支援和相機更新](https://developer.android.com/about/versions/pie/android-9.0#camera)。

### <a name="other-features"></a>其他功能

此外,Android 餅圖還支援其他幾個新功能:

- 新的[動畫圖像可繪製](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html)類,可用於繪製和顯示動畫圖像。

- 取代的新[影像解碼器](https://developer.android.com/reference/android/graphics/ImageDecoder.html)`BitmapFactory`類別 。 `ImageDecoder`可用於解碼`AnimatedImageDrawable`。

- 支援 HDR(高動態範圍)視頻和 HEIF(高效圖像檔格式)圖像。

- [作業計劃已](https://developer.android.com/reference/android/app/job/JobScheduler.html)增強,可以更智慧地處理與網路相關的作業。 [作業參數](https://developer.android.com/reference/android/app/job/JobParameters)類的新[GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29)方法傳回用於執行給定作業的任何網路請求的最佳網路。

有關最新的 Android 餅圖功能的詳細資訊,請參閱[Android 9 功能和 API](https://developer.android.com/about/versions/pie/android-9.0)。

## <a name="behavior-changes"></a>行為變更

當目標 Android 版本設置為 API 級別 28 時,即使您未實現上述新功能,也會對應用的行為進行多次平臺更改。 以下清單是這些變更的簡要摘要:

- 應用現在必須在使用前台服務之前請求前臺許可權。

- 如果應用有多個進程,則無法跨進程共用單個[WebView](xref:Android.Webkit.WebView)數據目錄。

- 不再允許通過路徑直接訪問其他應用的數據目錄。

有關面向 Android P 的應用程式的行為更改的詳細資訊,請參閱[行為更改](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps)。

## <a name="sample-code"></a>範例程式碼

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo)是一款適用於 Android Pie 的 Xamarin.Android 範例應用,演示`Person`如何設定顯示截止模式、 如何使用新類以及如何發送包含圖像的通知。

## <a name="summary"></a>總結

本文介紹了 Android 餅圖,並解釋了如何使用 Android 餅圖安裝和配置 Xamarin.Android 開發的最新工具和套件。 它提供了 Android Pie 中提供的主要功能的概述,並提供了其中幾個功能的示例原始程式碼。
它包括指向 API 文件和 Android 開發人員主題的連結,可説明您開始為 Android 餅圖創建應用。 它還強調了可能影響現有應用的最重要 Android 餅餅行為更改。

## <a name="related-links"></a>相關連結

- [Android 9 Pie](https://developer.android.com/about/versions/pie/)
