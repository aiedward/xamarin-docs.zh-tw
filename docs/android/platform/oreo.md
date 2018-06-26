---
title: Oreo 功能
description: 如何開始使用 Xamarin.Android 開發最新版的 Android 應用程式。
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: a23072427a74119bfa339fea8a695cd13b775685
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935099"
---
# <a name="oreo-features"></a>Oreo 功能

_如何開始使用 Xamarin.Android 開發最新版的 Android 應用程式。_

[Android 8.0 Oreo](https://developer.android.com/index.html)位於 Google Android 的最新版本。 Android Oreo Xamarin.Android 開發人員提供感興趣的許多新功能。 這些功能包括 XML、 可下載的字型、 自動填滿及圖片 (PIP) 中的圖片中的通知通道、 通知徽章、 自訂字型。 Android Oreo 包含新的 Api，針對這些新功能，而這些應用程式開發介面供 Xamarin.Android 應用程式，當您使用 Xamarin.Android 8.0 和更新版本。

[![Android Oreo 英雄映像](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

這份文件結構可協助您開始開發 Android 8.0 Oreo Xamarin.Android 應用程式。 它說明如何安裝必要的更新、 設定 SDK，以及為測試建立模擬器 （或裝置）。 它也說明如何使用 Android Oreo 功能 Xamarin.Android 應用程式中的範例應用程式的連結提供 Android 8.0 Oreo 中的新功能的外框。


## <a name="requirements"></a>需求

需要下列項目在 Xamarin 應用程式中使用 Android Oreo 功能：

-   **Visual Studio** &ndash;如果您使用 Windows 時，就需要 15.5 或更新版本的 Visual Studio 版本。  如果您使用的 Mac，Visual Studio for Mac 版本 7.2.0 需要。

-   **Xamarin.Android** &ndash; Xamarin.Android 8.0 或更新版本必須安裝並設定 Visual Studio。

-   **Android SDK** &ndash; Android SDK 8.0 (應用程式開發介面 26) 或更新版本必須安裝 Android SDK Manager 透過。



## <a name="getting-started"></a>快速入門

若要開始使用 Xamarin.Android Android Oreo，您必須下載並安裝最新工具與 SDK 封裝，才能建立 Android Oreo 專案：

1. 更新至最新版的 Visual Studio。

2. 安裝**Android 8.0.0 (應用程式開發介面 26)** 或更新版本的套件和工具透過 SDK Manager。

3. 建立新的 Xamarin.Android 專案的目標 Android Oreo (應用程式開發介面 26)。

4. 設定模擬器或裝置上的進行測試 Android Oreo 應用程式。

下列各節說明每個步驟：



### <a name="update-visual-studio-and-xamarinandroid"></a>更新 Visual Studio 和 Xamarin.Android

要加入 Visual Studio 中的 Android Oreo 支援，請執行下列作業：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   如果您使用 Visual Studio 2017: 

    1. Visual Studio 2017 15.5 或更新版本的更新 (請參閱[更新 Visual Studio 2017](https://docs.microsoft.com/en-us/visualstudio/install/update-visual-studio))。

    2. 使用 SDK Manager[安裝指示](~/android/get-started/installation/android-sdk.md#installation)安裝 Xamarin SDK Manager。
       因為 Google 不提供 standlone 支援 API 26.0 和更新版本的 GUI SDK manager 必須安裝 Xamarin SDK Manager。

-   如果您使用 Visual Studio 2015 中，我們建議為 25 降級 SDK 工具，並使用舊的 Google 模擬器管理員 GUI。 SDK 工具 25 仍然可以使用附 API 26 27 日和更新版本中，並不會影響新的平台開發。 這會提供您一個介面來管理您的 VS 的舊版本的 Android SDK。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

-   中所述，更新為最新穩定版本的 Visual Studio 2017 for Mac[更新 Visual Studio for Mac](https://docs.microsoft.com/en-us/visualstudio/mac/update)。

-----

如需 Xamarin Android Oreo 支援的詳細資訊，請參閱[Xamarin.Android 8.0 版本資訊](https://developer.xamarin.com/releases/android/xamarin.android_8/xamarin.android_8.0/)。



### <a name="install-the-android-sdk"></a>安裝 Android SDK

若要與 Xamarin.Android 8.0 建立專案時，您必須先使用 Xamarin Android SDK Manager 安裝的 SDK 平台**Android 8.0-Oreo**或更新版本。 您也必須安裝 Android SDK 工具 26.0 或更新版本。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 開始 SDK Manager (在 Visual Studio 中，按一下 **工具 > Android > Android SDK Manager**)。

2. 安裝**Android 8.0-Oreo**封裝。 如果您使用 Android SDK 模擬器，請務必包含**x86**系統映像，您將需要：

    [![選取 Android 8.0 封裝 Android SDK Manager 中](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. 安裝**Android SDK 工具 26.0.2**或更新版本， **Android 的 SDK 平台工具 26.0.0**或更新版本，和**Android 的 SDK 建置工具 26.0.0** （或更新版本）：

    [![在 Android SDK Manager 中選取 Android SDK 工具 26](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 開始 SDK Manager (在 Visual Studio for Mac 按一下**工具 > SDK Manager**)。

2. 安裝**Android 8.0-Oreo** SDK 封裝。 如果您使用 Android SDK 模擬器，請務必包含**x86**系統映像，您將需要：

    [![選取封裝 Android 8.0 SDK Manager 中](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. 安裝**Android SDK 工具 26.0.2**或更新版本， **Android 的 SDK 平台工具 26.0.0**或更新版本，和**Android 的 SDK 建置工具 26.0.0** （或更新版本）：

    [![在 SDK Manager 中選取 Android SDK 工具 26](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin.Android 專案

建立新的 Xamarin.Android 專案。 如果您是使用 Xamarin Android 開發的新手，請參閱[Hello，Android](~/android/get-started/hello-android/index.md)來了解如何建立 Xamarin.Android 專案。

當您建立 Android 專案時，您必須設定目標 Android 8.0 或更新版本的版本設定。 例如，適用於 Android 8.0 針對您的專案，您必須設定您的專案的目標 Android API 層級**Android 8.0 (應用程式開發介面 26)**。 建議您也設定您的目標架構層級 API 26 或更新版本。 如需設定 Android API 層級的層級相關資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。


### <a name="configure-an-emulator-or-device"></a>設定模擬器或裝置

如果您嘗試安裝 Android SDK 工具 26.0 之後啟動預設 Google gui AVD Manager 或更新版本中，您可能會收到下列錯誤對話方塊，指示您使用命令列 AVD manager 工具**avdmanager**改為:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Android 模擬器管理員警告對話方塊](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![Android 模擬器管理員警告對話方塊](oreo-images/mac/03-avd-warning.png)

-----

因為 Google 不再提供 standlone 支援 API 26.0 和更新版本的 GUI AVD manager，會顯示此訊息。 針對 Android 8.0 Oreo，您必須使用 Xamarin Android 模擬器管理員或命令列`avdmanager`工具來建立虛擬裝置的 Android Oreo。

若要使用 Android 裝置管理員來建立及管理虛擬裝置，請參閱[管理虛擬裝置的 Android 裝置管理員](~/android/get-started/installation/android-emulator/device-manager.md)。
若要建立虛擬裝置沒有 Android 裝置管理員中，請遵循下一節中的步驟。


#### <a name="creating-virtual-devices-using-avdmanager"></a>建立虛擬裝置使用 avdmanager

若要使用**avdmanager**若要建立新的虛擬裝置，請遵循下列步驟：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  開啟命令提示字元視窗，並設定`JAVA_HOME`至您的電腦上的 Java sdk 的位置。 典型的 Xamarin 安裝，您可以使用下列命令：

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  Android SDK 的位置加入`bin`資料夾，以您`PATH`。
    典型的 Xamarin 安裝，您可以使用下列命令：

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  關閉 [命令提示字元] 視窗，並開啟新的命令提示字元視窗。 建立新的虛擬裝置使用[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令。 例如，若要建立 AVD 名**AVD-Oreo-8.0**使用 x86 系統映像 API 層級 26，使用下列命令：

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  出現提示時**您想要建立自訂硬體設定檔 [no]** 您可以輸入**沒有**並接受預設硬體設定檔。 如果您說出**是**， **avdmanager**會提示您的自訂硬體設定檔的問題清單。

之後您**avdmanager**若要建立虛擬裝置，它將會包含在裝置下拉功能表：

[![新 AVD 加入裝置下拉式功能表](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  開啟**終端機**視窗，並切換到您的 mac 上的 Android SDK 工具目錄位置 典型的 Xamarin 安裝，您可以使用下列命令：

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  建立新的虛擬裝置使用[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令。 例如，若要建立 AVD 名**AVD-Oreo-8.0**使用 x86 系統映像 API 層級 26，使用下列命令：

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  出現提示時**您想要建立自訂硬體設定檔 [no]** 您可以輸入**沒有**並接受預設硬體設定檔。 如果您說出**是**， **avdmanager**會提示您使用自訂的問題清單，硬體設定檔。

使用之後**avdmanager**若要建立虛擬裝置，它將會包含在裝置下拉功能表：

[![新 AVD 加入裝置下拉式功能表](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

如需設定的 Android 模擬器進行測試和偵錯的詳細資訊，請參閱[Android 模擬器上偵錯](~/android/deploy-test/debugging/debug-on-emulator.md)。

如果您使用的實體裝置，例如管道或像素，您可以透過自動更新您的裝置，透過無線方式 (OTA) 更新或下載系統映像並直接快閃裝置。 如需手動更新 Android Oreo 您裝置的詳細資訊，請參閱[Nexus 和像素裝置的原廠映像](https://developers.google.com/android/images)。



## <a name="new-features"></a>新功能

Android Oreo 介紹各種不同的新特色與功能，例如通知通道、 通知徽章、 在 XML 中的自訂字型、 可下載的字型、 自動填滿和圖片的圖片。 下列各節會反白顯示這些功能，並提供連結，協助您開始在您的應用程式中使用它們。



### <a name="notification-channels"></a>通知通道

*通知通道*會通知應用程式定義的分類。
您可以建立通知通道的通知，您需要傳送，每一種，而且您可以建立通知通道，以反映您的應用程式的使用者所做的選擇。 新的通知通道功能，讓您提供使用者更細微的控制不同種類的通知。 例如，如果您實作傳訊應用程式，您可以建立每個使用者所建立的交談群組的個別通知通道。

[通知通道](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan)說明如何建立通知通道，並將它用於公佈本機的通知。 實際程式碼範例，請參閱[NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels)範例; 此範例應用程式管理兩個通道，並設定其他通知選項。



### <a name="notification-badges"></a>通知徽章

通知徽章是這個螢幕擷取畫面所示，在應用程式的圖示上顯示的小點：

[![範例應用程式圖示上的通知徽章](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

這些點表示有一或多個通知通道，該應用程式圖示相關聯的應用程式中的新通知&ndash;這些是使用者尚未解除或作用時的通知。 使用者可以長時間的按下的圖示解除或代表該 appeaars 通知從長時間按下功能表一下通知徽章，相關聯的通知。

如需通知徽章的詳細資訊，請參閱 Android 開發人員[通知徽章](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges)主題。



### <a name="custom-fonts-in-xml"></a>自訂 XML 中有多種字型

Android Oreo 導入了*XML 中的有多種字型*，這可讓您將自訂字型，做為資源。 OpenType (**opentype**) 和 TrueType (**.ttf**) 字型格式都受到支援。 若要新增為資源的字型，請執行下列作業：

1. 建立**資源/字型**資料夾。

2. 將字型檔案複製 (範例中， **.ttf**和**opentype**檔案) 至**資源/字型**。 

3. 如有必要，重新命名每個字型檔案，使它符合的 Android 檔案命名慣例 (亦即，使用只有小寫*a 到 z*， *0-9*，和檔案名稱中的底線)。 例如，字型檔`Pacifico-Regular.ttf`無法重新命名為類似`pacifico.ttf`。

4. 使用新的套用自訂字型`android:fontFamily`您配置的 XML 中的屬性。 例如，下列`TextView`宣告會使用加入**pacifico.ttf**字型資源：

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

您也可以建立一個字型家族 XML 檔案，描述多個字型以及樣式和加權詳細資料。 如需詳細資訊，請參閱 Android 開發人員[XML 中的有多種字型](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html)主題。


### <a name="downloadable-fonts"></a>可下載的字型

從 Android Oreo 開始，應用程式可以從提供者，而非它們組合成 APK 中要求的字型。 只有在必要時，會從網路下載字型。 這項功能可減少 APK 大小，以節省電話記憶體和行動資料使用量。 您也可以使用此功能在 Android API 版本 14 和更新版本上安裝的 Android 支援 Libary 26 套件。

當您的應用程式需要的字型時，您建立`FontsRequest`物件 （指定下載字型），然後將它傳遞給`FontsContract`方法來下載字型。 下列步驟說明字型下載程序，在更多詳細資料：

1.  具現化[FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html)物件。 

2.  子類別並具現化[FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html)。

3.  實作[FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29)方法，用來處理完成字型要求。

4.  實作[FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29)方法，用來通知您的應用程式的字型要求程序期間發生任何錯誤。

5.  呼叫[FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback))方法來擷取字型的字型提供者。 

當您呼叫`RequestFonts`方法，它會先檢查以查看是否在本機快取字型 (從先前呼叫`RequestFont`)。 如果沒有快取，它呼叫字型提供者，會擷取字型以非同步的方式，並藉由叫用，然後將結果傳遞至您的應用程式程式`OnTypeFaceRetrieved`方法。

[可下載的字型](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts)範例示範如何使用 Android Oreo 中引進的可下載的字型功能。 

如需下載字型的詳細資訊，請參閱 Android 開發人員[可下載的字型](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html)主題。



### <a name="autofill"></a>自動填滿

新_自動填滿_中 Android Oreo 架構可讓使用者更輕鬆地處理重複的工作，例如登入、 帳戶建立和信用卡交易。 使用者會耗費較少的時間重新鍵入資訊 （這可能會導致錯誤的輸入）。 您的應用程式可以使用自動填滿 Framework 之前，必須啟用自動填滿服務系統設定 （使用者可以啟用或停用自動填入） 中。

[AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/)範例將示範如何使用自動填滿架構。 它包含檢視應 autofilled，與服務可以提供給用戶端活動的自動填滿資料的用戶端活動的實作。

如需新的自動填寫功能以及如何最佳化您的應用程式的自動填滿的詳細資訊，請參閱 Android 開發人員[自動填滿 Framework](https://developer.android.com/guide/topics/text/autofill.html)主題。



### <a name="picture-in-picture-pip"></a>圖片 (PIP) 中的圖片

Android Oreo 可讓活動在圖片中圖片 (PIP) 模式下，啟動重疊的另一個活動畫面。 這項功能僅供播放視訊。

若要指定應用程式的活動可以使用 PIP 模式，請設定下列旗標設為 true，Android 資訊清單中：

```xml
android:supportsPictureInPicture
```

若要指定在 PIP 模式時，您的活動應如何運作，您使用新[PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html)物件。 `PictureInPictureParams` 代表一組您用來初始化和更新 PIP 模式 （例如，在活動的慣用外觀比例） 中的活動的參數。 若要新增下列新的 PIP 方法`Activity`Android Oreo 中：

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash;置於 PIP 模式中的活動。 活動放置在畫面上，角和螢幕的其餘部分會填入先前已在螢幕的活動。

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash;更新活動的 PIP 組態設定 （例如外觀比例變更）。

[PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture)範例示範基本的使用方式的掌上型裝置 Oreo 中導入的圖片中圖片 (PiP) 模式。 此範例會播放視訊就會繼續不會中斷時顯示模式或其他活動之間來回切換。



### <a name="other-features"></a>其他功能

Android Oreo 包含許多其他新功能，例如 Emoji 支援文件庫位置 API 背景限制、 應用程式、 新的音訊轉碼器、 WebView 增強功能、 改善的鍵盤巡覽支援，與新的 AAudio （pro 音訊） API 的整個範圍色彩高效能低度延遲的音訊，如需有關這些功能，請參閱 Android 開發人員[Android Oreo 功能和 Api](https://developer.android.com/about/versions/oreo/android-8.0.html)主題。



## <a name="behavior-changes"></a>行為變更

Android Oreo 包含各種系統和應用程式開發介面可能會影響現有的應用程式的功能的行為變更。 將說明這些變更，如下所示。


### <a name="background-execution-limits"></a>背景執行限制

若要改善使用者體驗，Android Oreo 會加諸限制可以執行的應用程式時在背景執行。 例如，如果使用者觀賞視訊或玩遊戲，在背景中執行的應用程式可能影響到視訊密集應用程式在前景執行的效能。 如此一來，Android Oreo 放置於下列限制不會直接與使用者互動的應用程式：

1.  **背景服務限制**&ndash;在背景中執行應用程式時，它會有幾分鐘的時間，它仍然允許建立和使用服務視窗。 該視窗結尾 Android 停止應用程式的背景服務，並將它視為正在_閒置_。

2.  **廣播限制** &ndash; Android 7.0 (應用程式開發介面 25) 放在應用程式登錄至收到的廣播的限制。 Android Oreo 讓這些限制更嚴格。 比方說，Android Oreo 應用程式不再可以註冊用於隱含廣播廣播的接收者在其資訊清單中。

如需有關新的背景執行限制的詳細資訊，請參閱 Android 開發人員[背景執行限制](https://developer.android.com/about/versions/oreo/background.html)主題。


### <a name="breaking-changes"></a>重大變更

應用程式的目標 Android Oreo 或更高版本必須修改自己的應用程式支援下列變更，在適用情況：

- Android Oreo 取代設定優先權的個別通知的能力。 相反地，建立通知通道時設定建議的重要性層級。 您指派給通知通道的重要性層級適用於所有通知訊息張貼到它。

- 應用程式的目標 Android Oreo，`PendingIntent.GetService()`無法運作因為放在服務在背景中啟動新的限制。 如果您的目標 Android Oreo，您應該使用[PendingIntent.GetBroadcast](https://developer.xamarin.com/api/member/Android.App.PendingIntent.GetBroadcast/p/Android.Content.Context/System.Int32/Android.Content.Intent/Android.App.PendingIntentFlags/)改為。  


## <a name="sample-code"></a>程式碼範例

數個 Xamarin.Android 範例可用來說明如何利用 Android Oreo 功能：

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels)示範如何使用 Android Oreo 中導入新的通知通道系統。 這個範例會管理兩個通知通道： 一個使用預設的重要性，而另一個具有高重要性。

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture)示範基本用法的掌上型裝置 Oreo 中導入的圖片中圖片 (PiP) 模式。 此範例會播放視訊就會繼續不會中斷時顯示模式或其他活動之間來回切換。

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework)示範如何使用自動填滿架構。 它包含檢視應 autofilled，與服務可以提供給用戶端活動的自動填滿資料的用戶端活動的實作。

-   [可下載的字型](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts)提供如何使用稍早所述的可下載的字型功能的範例。

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat)示範如何使用 EmojiCompat 支援程式庫。 您可以使用此文件庫以防止您的應用程式，從顯示遺漏 emoji 字元為"tofu"字元。

-   [位置更新暫止意圖](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent)說明關於裝置的位置取得更新的位置 api 的使用方式使用`PendingIntent`。

-   [位置更新前景服務](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService)示範如何使用位置 API 來取得裝置的位置使用的繫結和啟動前景服務相關的更新。


## <a name="video"></a>視訊

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Android 8.0 Oreo 開發使用 C#**


## <a name="summary"></a>總結

本文介紹 Android Oreo，並說明如何安裝及設定 Android Oreo 上的最新工具和 Xamarin.Android 開發的封裝。 它提供數個新功能的範例原始程式碼的連結中 Android Oreo，可用的重要功能的概觀。 它包含應用程式開發介面文件的連結，而且 Android 開發人員的主題，以協助您開始建立應用程式的 Android Oreo。 它也以反白顯示的最重要的 Android Oreo 行為變更可能會影響現有應用程式。


## <a name="related-links"></a>相關連結

- [Android 8.0 Oreo](https://developer.android.com/index.html)
