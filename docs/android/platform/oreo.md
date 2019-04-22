---
title: Oreo 功能
description: 如何開始使用 Xamarin.Android 開發 Android 的最新版本的應用程式。
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 07/06/2018
ms.openlocfilehash: 24a9fa0e954ddba1451ba8bf98216550d7d70b51
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58854778"
---
# <a name="oreo-features"></a>Oreo 功能

_如何開始使用 Xamarin.Android 開發 Android 的最新版本的應用程式。_

[Android 8.0 Oreo](https://developer.android.com/index.html)是 Google 提供的最新 Android 版本。 在 android Oreo 提供 Xamarin.Android 開發人員感興趣的許多新功能。 這些功能包括 XML、 可下載的字型、 自動填滿及 picture in picture (PIP) 中的通知通道、 通知徽章、 自訂字型。 Android oreo 版包括新的 Api，這些新功能，以及這些 Api 是供 Xamarin.Android 應用程式，當您使用 Xamarin.Android 8.0 及更新版本。

[![Android Oreo 英雄影像](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

這篇文章是結構化，以協助您開始使用適用於 Android 8.0 Oreo 開發 Xamarin.Android 應用程式。 它會說明如何安裝必要的更新、 設定 SDK，以及建立用於測試的模擬器 （或裝置）。 它也提供 Android 8.0 Oreo 中的新功能的大綱，說明如何在 Xamarin.Android 應用程式中使用 Android Oreo 功能的範例應用程式的連結。


## <a name="requirements"></a>需求

需要下列項目在以 Xamarin 為基礎的應用程式中使用 Android Oreo 功能：

-   **Visual Studio** &ndash; 15.5 版或更新版本的 Visual Studio 如果您使用的 Windows，則需要。  如果您使用 Mac，Visual Studio for Mac 版本 7.2.0 是必要的。

-   **Xamarin.Android** &ndash; Xamarin.Android 8.0 或更新版本必須安裝並設定 Visual Studio。

-   **Android SDK** &ndash; Android SDK 8.0 (API 26) 或更新版本必須安裝 Android SDK 管理員透過。



## <a name="getting-started"></a>快速入門

若要開始使用 Xamarin.Android 的 Android oreo 版，您必須下載並安裝最新工具和 SDK 封裝，才能建立 Android Oreo 專案：

1. 更新至最新版的 Visual Studio。

2. 安裝**Android 8.0.0 (API 26)** 或更新版本的套件和工具透過 SDK 管理員。

3. 建立新的 Xamarin.Android 專案目標 Android Oreo (API 26)。

4. 設定模擬器或裝置上進行測試 Android Oreo 的應用程式。

下列各節說明每個步驟：

### <a name="update-visual-studio-and-xamarinandroid"></a>更新 Visual Studio 和 Xamarin.Android

若要新增至 Visual Studio 的 Android Oreo 的支援，請執行下列作業：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

- 針對 Visual Studio 2019，使用[SDK 管理員](~/android/get-started/installation/android-sdk.md)安裝 26.0 或更新版本的 API 層級。

- 如果您使用 Visual Studio 2017:

    1. Visual Studio 2017 15.7 版或更新版本的更新 (請參閱[Visual Studio 2017 更新](https://docs.microsoft.com/visualstudio/install/update-visual-studio))。

    2. 使用[SDK 管理員](~/android/get-started/installation/android-sdk.md)安裝 26.0 或更新版本的 API 層級。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

- 中所述，更新至最新的穩定版本的 Visual Studio for Mac[更新 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/update)。

-----

如需有關適用於 Android Oreo 的 Xamarin 支援的詳細資訊，請參閱[Xamarin.Android 8.0 版本資訊](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/)。



### <a name="install-the-android-sdk"></a>Android SDK 安裝

若要建立專案時使用 Xamarin.Android 8.0，您必須先使用 Xamarin Android SDK 管理員安裝的 SDK 平台**Android 8.0-Oreo**或更新版本。 您也必須安裝 Android SDK Tools 26.0 或更新版本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 啟動 SDK 管理員 (在 Visual Studio 中，按一下**工具 > Android > Android SDK 管理員**)。

2. 安裝**Android 8.0-Oreo**封裝。 如果您使用 Android SDK 模擬器，請務必包含**x86**系統映像，您必須：

    [![Android SDK Manager 中的套件選取 Android 8.0](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. 安裝**Android SDK Tools 26.0.2**或更新版本**Android 的 SDK 平台工具 26.0.0**或更新版本，並**Android 的 SDK 建置工具 26.0.0** （或更新版本）：

    [![選取 Android SDK 管理員中的 Android SDK Tools 26](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 啟動 SDK 管理員 (在 Visual Studio for Mac 中，按一下**工具 > SDK 管理員**)。

2. 安裝**Android 8.0-Oreo** SDK 封裝。 如果您使用 Android SDK 模擬器，請務必包含**x86**系統映像，您必須：

    [![在 SDK Manager 中的套件選取 Android 8.0](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. 安裝**Android SDK Tools 26.0.2**或更新版本**Android 的 SDK 平台工具 26.0.0**或更新版本，並**Android 的 SDK 建置工具 26.0.0** （或更新版本）：

    [![在 SDK 管理員中選取 Android SDK Tools 26](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin.Android 專案

建立新的 Xamarin.Android 專案。 如果您是使用 Xamarin 進行 Android 開發的新手，請參閱[Hello，Android](~/android/get-started/hello-android/index.md)來了解如何建立 Xamarin.Android 專案。

當您建立 Android 專案時，您必須設定目標 Android 8.0 或更新版本的版本設定。 比方說，若要將專案目標適用於 Android 8.0，您必須設定您的專案的目標 Android API 層級**Android 8.0 (API 26)**。 建議您將您的目標架構層級 API 26 或更新版本。 如需有關設定 Android API 層級的層級的詳細資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。


### <a name="configure-an-emulator-or-device"></a>設定模擬器或裝置

如果您嘗試啟動預設 Google GUI 型 AVD Manager 之後安裝 Android SDK Tools 26.0 或更新版本中，您可能會收到下列錯誤對話方塊，指示您使用命令列 AVD 管理員工具**avdmanager**改為:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Android Emulator 管理員警告對話方塊](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![Android Emulator 管理員警告對話方塊](oreo-images/mac/03-avd-warning.png)

-----

因為 Google 不再提供獨立的 GUI AVD manager 支援 26.0 和更新版本的 API，會顯示此訊息。 針對 Android 8.0 Oreo，您必須使用 Xamarin Android Emulator 管理員或命令列`avdmanager`工具來建立適用於 Android Oreo 的虛擬裝置。

若要使用 Android 裝置管理員來建立和管理虛擬裝置，請參閱[管理的虛擬裝置的 Android 裝置管理員](~/android/get-started/installation/android-emulator/device-manager.md)。
若要建立虛擬裝置，而不需要 Android 裝置管理員中，請遵循下一節中的步驟。


#### <a name="creating-virtual-devices-using-avdmanager"></a>建立虛擬裝置使用 avdmanager

若要使用**avdmanager**若要建立新的虛擬裝置，請遵循下列步驟：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  開啟命令提示字元視窗，並設定`JAVA_HOME`至您的電腦上的 Java sdk 的位置。 針對一般的 Xamarin 安裝中，您可以使用下列命令：

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  新增 Android SDK 的位置`bin`資料夾，以您`PATH`。
    針對一般的 Xamarin 安裝中，您可以使用下列命令：

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  關閉 [命令提示字元] 視窗，並開啟新的 [命令提示字元] 視窗。 使用建立新的虛擬裝置[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令。 例如，若要建立 AVD 名為**AVD-Oreo-8.0**使用 x86 系統映像 API 層級 26，使用下列命令：

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  您使用的提示時**您想要建立自訂硬體設定檔 [否]** 您可以輸入**沒有**並接受預設的硬體設定檔。 如果您說出 **[是]**， **avdmanager**會提示您的自訂硬體設定檔的問題清單。

之後您**avdmanager**若要建立您的虛擬裝置，它將會包含在裝置下拉式功能表：

[![新的 AVD 新增至裝置下拉式功能表](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  開啟**終端機**視窗，並切換至您的 mac 上的 Android SDK tools 目錄的位置 針對一般的 Xamarin 安裝中，您可以使用下列命令：

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  使用建立新的虛擬裝置[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令。 例如，若要建立 AVD 名為**AVD-Oreo-8.0**使用 x86 系統映像 API 層級 26，使用下列命令：

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  您使用的提示時**您想要建立自訂硬體設定檔 [否]** 您可以輸入**沒有**並接受預設的硬體設定檔。 如果您說出 **[是]**， **avdmanager**會提示您的自訂硬體設定檔的問題清單。

使用之後**avdmanager**若要建立您的虛擬裝置，它將會包含在裝置下拉式功能表：

[![新的 AVD 新增至裝置下拉式功能表](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

如需有關如何設定 Android 模擬器進行測試和偵錯的詳細資訊，請參閱 <<c0> [ 在 Android 模擬器上偵錯](~/android/deploy-test/debugging/debug-on-emulator.md)。

如果您使用實體裝置，例如 Nexus 或像素，您可以透過自動更新您的裝置，透過無線 (OTA) 更新或下載的系統映像並直接快閃裝置。 如需有關如何以手動方式將裝置升級至 Android Oreo 的詳細資訊，請參閱[Nexus 和像素裝置的原廠映像](https://developers.google.com/android/images)。



## <a name="new-features"></a>新功能

Android oreo 版導入了各種新功能和功能，例如通知通道、 通知徽章、 在 XML 中的自訂字型、 可下載的字型、 自動填滿和圖片的圖片。 下列各節會反白顯示這些功能，並提供連結，幫助您開始在您的應用程式中使用它們。



### <a name="notification-channels"></a>通知通道

*通知通道*會通知應用程式定義的分類。
您可以建立每種類型的通知，您需要傳送，通知通道，而且您可以建立通知通道，以反映您的應用程式的使用者所做的選擇。 新的通知通道功能，可讓您授與使用者更細微的控制不同種類的通知。 比方說，如果您要實作傳訊應用程式，您可以建立個別的通知通道使用者所建立的每個交談群組。

[通知通道](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan)說明如何建立通知通道，並將它用於張貼本機通知。 真實世界程式碼範例，請參閱[NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels)範例; 此範例應用程式管理兩個通道，並設定其他通知選項。



### <a name="notification-badges"></a>通知徽章

通知徽章會透過應用程式圖示會出現，如以下螢幕擷取畫面所示的小點：

[![在 應用程式圖示上的範例通知徽章](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

這些點可讓您指出有新的通知，該應用程式圖示與相關聯的應用程式中的一或多個通知通道&ndash;這些是使用者尚未關閉，或採取的通知。 使用者可以長時間-按下圖示以關閉或長按下功能表通知該 appeaars 採取行動，看看下列通知徽章，相關聯的通知。

如需通知徽章的詳細資訊，請參閱 Android 開發人員[通知徽章](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges)主題。



### <a name="custom-fonts-in-xml"></a>在 XML 中的自訂字型

Android oreo 版導入了*在 XML 中的字型*，這可讓您將自訂字型做為資源。 OpenType (**opentype**) 和 TrueType (**.ttf**) 支援字型格式。 若要將字型新增為資源，執行下列作業：

1. 建立**字型資源/** 資料夾。

2. 將字型檔案複製 (範例中， **.ttf**並**opentype**檔案) 來**資源/字型**。 

3. 如有必要，每個字型檔案重新命名，以符合 Android 檔案命名慣例 (亦即，使用只有小寫*a-z、0-9*， *0-9*，和檔案名稱中的底線)。 例如，字型檔案`Pacifico-Regular.ttf`無法重新命名為類似`pacifico.ttf`。

4. 使用新套用自訂字型`android:fontFamily`您配置 XML 中的屬性。 例如，下列`TextView`宣告會使用已加入**pacifico.ttf**字型資源：

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

您也可以建立一個字型家族 XML 檔案，說明多個字型以及樣式和權數的詳細資料。 如需詳細資訊，請參閱 Android 開發人員[在 XML 中的字型](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html)主題。


### <a name="downloadable-fonts"></a>可下載的字型

從 Android oreo 版開始，應用程式可以要求字型，從提供者，而不是將它們繫結至 APK。 只有在必要時，會從網路下載字型。 這項功能可減少 APK 大小，以節省電話記憶體和行動數據資料的使用方式。 您也可以在 Android API 版本 14 和更新版本上使用這項功能，透過安裝 Android 支援程式庫 26 套件。

當應用程式所需字型時，您會建立`FontsRequest`物件 （指定要下載的字型），然後將它傳遞給`FontsContract`方法下載字型。 下列步驟描述字型下載程序，在更多詳細資料：

1.  具現化[FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html)物件。 

2.  子類別並具現化[FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html)。

3.  實作[FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29)方法，用來處理完成字型要求。

4.  實作[FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29)方法，用來通知您的應用程式在字型要求處理期間發生任何錯誤。

5.  呼叫[FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback))方法來擷取字型的字型提供者。 

當您呼叫`RequestFonts`方法，它會先檢查以查看是否字型在本機快取 (從先前呼叫`RequestFont`)。 如果未快取，呼叫字型提供者、 擷取字型以非同步的方式，並藉由叫用，然後將結果傳遞給您的應用程式您`OnTypeFaceRetrieved`方法。

[可下載的字型](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts)範例示範如何使用 Android Oreo 中引入的可下載的字型功能。 

如需有關如何下載字型的詳細資訊，請參閱 Android 開發人員[可下載的字型](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html)主題。



### <a name="autofill"></a>自動填滿

新_自動填滿_Android Oreo 中的架構可讓使用者更輕鬆地處理重複的工作，例如登入、 帳戶建立和信用卡交易。 使用者較少的時間重新輸入資訊 （這可能會導致輸入錯誤）。 Autofill Framework 可以處理您的應用程式之前，自動填入服務必須能夠在系統設定 （使用者可以啟用或停用自動填入）。

[AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/)範例示範如何使用 Autofill Framework。 其包含的檢視，應該 autofilled，而且可以自動填滿資料提供給用戶端活動的服務的用戶端活動的實作。

如需有關新的自動填寫功能以及如何最佳化您的應用程式的自動填滿的詳細資訊，請參閱 Android 開發人員[Autofill Framework](https://developer.android.com/guide/topics/text/autofill.html)主題。



### <a name="picture-in-picture-pip"></a>Picture in Picture (PIP)

Android oreo 版可讓活動在圖片中圖片 (PIP) 模式下，啟動重疊的另一個活動畫面。 這項功能適用於播放的視訊。

若要指定應用程式的活動可以使用 PIP 模式，請設定下列旗標設為 true，在 Android 資訊清單中：

```xml
android:supportsPictureInPicture
```

若要指定在 PIP 模式時，您的活動應如何運作，您使用新[PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html)物件。 `PictureInPictureParams` 表示一組您用來初始化和更新 PIP 模式 （例如，活動的慣用外觀比例） 中的活動的參數。 下列新的 PIP 方法新增到`Activity`Android Oreo 中：

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash;置於 PIP 模式中的活動。 活動放置角的畫面和螢幕的其餘部分會填入已在螢幕的前一個活動。

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash;更新活動的 PIP 組態設定 （例如，變更外觀比例）。

[PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture)範例會示範 Oreo 中導入的掌上型裝置的圖片中圖片 (PiP) 模式的基本使用方式。 此範例會播放影片而仍會繼續不中斷時顯示模式或其他活動之間來回切換。



### <a name="other-features"></a>其他功能

在 android Oreo 包含許多其他新功能，例如 Emoji 支援程式庫，位置 API 背景限制、 應用程式、 新的音訊轉碼器、 WebView 增強功能、 改善的鍵盤巡覽支援，和新的 AAudio （pro 音訊） API 的寬色域圖色彩如需有關這些功能，高效能低延遲音訊，請參閱 Android 開發人員[Android Oreo 功能和 Api](https://developer.android.com/about/versions/oreo/android-8.0.html)主題。



## <a name="behavior-changes"></a>行為變更

在 android Oreo 包含各種系統和 API 可能會影響現有的應用程式的功能的行為變更。 將說明這些變更，如下所示。


### <a name="background-execution-limits"></a>背景執行限制

若要改善使用者體驗，Android Oreo 強制施行限制可以執行的應用程式在背景中執行時。 比方說，如果使用者從觀看的影片或玩遊戲，在背景中執行的應用程式可能會損害影片密集應用程式在前景執行的效能。 如此一來，Android Oreo 會將下列的限制，不會直接與使用者互動的應用程式：

1.  **背景服務限制**&ndash;在背景中執行應用程式時，它有幾分鐘的時間中仍然可以建立及使用服務視窗。 在該視窗結束時，Android 停止應用程式的背景服務，並將它視為正在_閒置_。

2.  **限制的廣播** &ndash; Android 7.0 (API 25) 放在應用程式註冊以接收的廣播的限制。 Android oreo 版可讓這些限制更嚴格。 例如，Android Oreo 應用程式不再可註冊用於隱含廣播的廣播的接收器在其資訊清單中。

如需有關新的背景執行限制的詳細資訊，請參閱 Android 開發人員[背景執行限制](https://developer.android.com/about/versions/oreo/background.html)主題。


### <a name="breaking-changes"></a>重大變更

目標 Android oreo 版或更高版本必須修改自己的應用程式，以支援下列的變更，在適用的應用程式：

- 在 android Oreo 取代設定優先順序的個別通知的能力。 相反地，建立通知通道時設定建議的重要性層級。 您指派給通知通道的重要性層級適用於所有通知訊息張貼給它。

- 應用程式的目標 Android Oreo，`PendingIntent.GetService()`因為放在背景中啟動的服務上的新限制而無法運作。 如果您的目標 Android Oreo，則應該使用[PendingIntent.GetBroadcast](https://developer.xamarin.com/api/member/Android.App.PendingIntent.GetBroadcast/p/Android.Content.Context/System.Int32/Android.Content.Intent/Android.App.PendingIntentFlags/)改。  


## <a name="sample-code"></a>程式碼範例

Xamarin.Android 的數個範例可示範如何利用 Android Oreo 功能：

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels)示範如何使用 Android Oreo 中導入新的通知通道系統。 此範例會管理兩個通知通道： 一個使用預設的重要性，而另一個以高重要性。

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture)示範 Oreo 中導入的掌上型裝置的圖片中圖片 (PiP) 模式的基本使用方式。 此範例會播放影片而仍會繼續不中斷時顯示模式或其他活動之間來回切換。

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework)示範 Autofill Framework 的使用方式。 其包含的檢視，應該 autofilled，而且可以自動填滿資料提供給用戶端活動的服務的用戶端活動的實作。

-   [可下載的字型](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts)提供如何使用稍早所述的可下載的字型功能的範例。

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat)示範 EmojiCompat 支援程式庫的使用方式。 您可以使用此程式庫，以防止您的應用程式，從顯示遺漏 emoji 字元為"tofu"字元。

-   [位置更新擱置的意圖](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent)說明有關裝置的位置取得更新的位置 api 的使用方式使用`PendingIntent`。

-   [位置更新前景服務](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService)示範如何使用位置 API 來取得裝置的位置使用的繫結和啟動前景服務相關的更新。


## <a name="video"></a>視訊

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Android 8.0 Oreo 開發使用 C#**


## <a name="summary"></a>總結

這篇文章介紹 Android Oreo，並說明如何安裝和設定 Android Oreo 上的最新的工具和適用於 Xamarin.Android 開發的封裝。 它提供數個新功能的範例原始程式碼的連結提供 Android Oreo 中的重要功能的概觀。 它包含 API 文件的連結，Android 開發人員主題，以協助您開始使用適用於 Android Oreo 中建立應用程式。 它也以反白顯示的最重要的 Android Oreo 行為變更可能會影響現有的應用程式。


## <a name="related-links"></a>相關連結

- [Android 8.0 Oreo](https://developer.android.com/index.html)
