---
title: Oreo 功能
description: 如何開始使用 Xamarin 來開發最新 Android 版本的應用程式。
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 07/06/2018
ms.openlocfilehash: 29f7725e41e5163b8f990c827983fbd79bdd1b1e
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510485"
---
# <a name="oreo-features"></a>Oreo 功能

_如何開始使用 Xamarin 來開發最新 Android 版本的應用程式。_

[Android 8.0 Oreo](https://developer.android.com/index.html)是 Google 提供的最新 android 版本。 Android Oreo 為 Xamarin. Android 開發人員提供許多相關的新功能。 這些功能包括通知通道、通知徽章、XML 中的自訂字型、可下載的字型、自動填滿和圖片 (PIP)。 Android Oreo 包含這些新功能的新 Api, 而當您使用 Xamarin. Android 8.0 和更新版本時, 這些 Api 可用於 Xamarin 應用程式。

[![Android Oreo 主圖影像](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

本文的結構是協助您開始開發適用于 Android 8.0 Oreo 的 Xamarin Android 應用程式。 其中說明如何安裝必要的更新、設定 SDK, 以及建立模擬器 (或裝置) 以進行測試。 它也概述 Android 8.0 Oreo 的新功能, 並提供範例應用程式的連結, 說明如何在 Xamarin Android 應用程式中使用 Android Oreo 功能。


## <a name="requirements"></a>需求

下列為在 Xamarin 應用程式中使用 Android Oreo 功能的必要條件:

-   **Visual Studio**&ndash;如果您使用 Windows, 則需要15.5 或更新版本的 Visual Studio。  如果您使用 Mac, 則需要 Visual Studio for Mac 版本7.2.0。

-   您必須安裝並設定 Visual Studio 的**xamarin android** &ndash; xamarin. android 8.0 或更新版本。

-   **Android SDK**&ndash; Android SDK 8.0 (API 26) 或更新版本必須透過 Android SDK 管理員安裝。



## <a name="getting-started"></a>快速入門

若要開始使用 Android Oreo 搭配 Xamarin, 您必須先下載並安裝最新的工具和 SDK 套件, 才能建立 Android Oreo 專案:

1. 更新至最新版本的 Visual Studio。

2. 透過 SDK 管理員安裝**Android 8.0.0 (API 26)** 或更新版本的套件和工具。

3. 建立以 Android Oreo (API 26) 為目標的新 Xamarin. Android 專案。

4. 設定用於測試 Android Oreo 應用程式的模擬器或裝置。

下列各節將說明每個步驟:

### <a name="update-visual-studio-and-xamarinandroid"></a>更新 Visual Studio 和 Xamarin. Android

若要將 Android Oreo 支援新增至 Visual Studio, 請執行下列動作:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

- 針對 Visual Studio 2019, 請使用[SDK 管理員](~/android/get-started/installation/android-sdk.md)來安裝 API 層級26.0 或更新版本。

- 如果您使用 Visual Studio 2017:

    1. 更新為 Visual Studio 2017 15.7 版或更新版本 (請參閱[update Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio))。

    2. 使用[SDK 管理員](~/android/get-started/installation/android-sdk.md)來安裝 API 層級26.0 或更新版本。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

- 更新至最新穩定版本的 Visual Studio for Mac, 如[更新 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/update)中所述。

-----

如需有關適用于 Android Oreo 之 Xamarin 支援的詳細資訊, 請參閱《 [android 8.0 版本資訊》](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/)。



### <a name="install-the-android-sdk"></a>安裝 Android SDK

若要使用 Xamarin 8.0 建立專案, 您必須先使用 Xamarin Android SDK 管理員來安裝適用于**Android 8.0-Oreo**或更新版本的 SDK 平臺。 您也必須安裝 Android SDK Tools 26.0 或更新版本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 啟動 SDK 管理員 (在 Visual Studio 中, 按一下 [**工具] > [Android > Android SDK 管理員**])。

2. 安裝**Android 8.0-Oreo**套件。 如果您使用 Android SDK 模擬器, 請務必包含您將需要的**x86**系統映射:

    [![在 Android SDK 管理員中選取 Android 8.0 套件](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. 安裝**Android SDK Tools 26.0.2**或更新版本、 **Android SDK 平臺工具 26.0.0**或更新版本, 以及**Android SDK 的 26.0.0** (或更新版本) 工具:

    [![在 Android SDK 管理員中選取 Android SDK Tools 26](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 啟動 SDK 管理員 (在 Visual Studio for Mac 中, 按一下 [**工具] > [SDK 管理員**])。

2. 安裝**Android 8.0-Oreo** SDK 套件。 如果您使用 Android SDK 模擬器, 請務必包含您將需要的**x86**系統映射:

    [![在 SDK 管理員中選取 Android 8.0 套件](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. 安裝**Android SDK Tools 26.0.2**或更新版本、 **Android SDK 平臺工具 26.0.0**或更新版本, 以及**Android SDK 的 26.0.0** (或更新版本) 工具:

    [![在 SDK 管理員中選取 Android SDK Tools 26](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin Android 專案

建立新的 Xamarin Android 專案。 如果您不熟悉使用 Xamarin 進行 Android 開發的新手, 請參閱[Hello, Android](~/android/get-started/hello-android/index.md)以瞭解如何建立 Xamarin. android 專案。

當您建立 Android 專案時, 您必須將版本設定設定為以 Android 8.0 或更新版本為目標。 例如, 若要以 Android 8.0 的專案為目標, 您必須將專案的目標 Android API 層級設定為**android 8.0 (API 26)** 。 建議您也將目標 framework 層級設定為 API 26 或更新版本。 如需設定 Android API 層級層級的詳細資訊, 請參閱[瞭解 ANDROID Api 層](~/android/app-fundamentals/android-api-levels.md)級。


### <a name="configure-an-emulator-or-device"></a>設定模擬器或裝置

如果您嘗試在安裝 Android SDK Tools 26.0 或更新版本之後啟動預設的 Google GUI 型 AVD 管理員, 您可能會收到下列錯誤對話方塊, 指示您改用命令列 AVD 管理員工具**avdmanager** :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Android Emulator 管理員警告對話方塊](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![Android Emulator 管理員警告對話方塊](oreo-images/mac/03-avd-warning.png)

-----

因為 Google 不再提供支援 API 26.0 和更新版本的獨立 GUI AVD 管理員, 所以會顯示此訊息。 針對 android 8.0 Oreo, 您必須使用 Xamarin Android Emulator 管理員或命令列`avdmanager`工具來建立適用于 android Oreo 的虛擬裝置。

若要使用 Android Device Manager 來建立和管理虛擬裝置, 請參閱[使用 Android Device Manager 管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md)。
若要建立沒有 Android Device Manager 的虛擬裝置, 請遵循下一節中的步驟。


#### <a name="creating-virtual-devices-using-avdmanager"></a>使用 avdmanager 建立虛擬裝置

若要使用**avdmanager**來建立新的虛擬裝置, 請遵循下列步驟:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  開啟 [命令提示字元] 視窗`JAVA_HOME` , 並將設為 JAVA SDK 在您電腦上的位置。 針對一般的 Xamarin 安裝, 您可以使用下列命令:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  將 Android SDK `bin`資料夾的位置新增至您`PATH`的。
    針對一般的 Xamarin 安裝, 您可以使用下列命令:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  關閉 [命令提示字元] 視窗, 然後開啟新的 [命令提示字元] 視窗。 使用[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令建立新的虛擬裝置。 例如, 若要使用適用于 API 層級26的 x86 系統映射來建立名為**AVD-Oreo-8.0**的 AVD, 請使用下列命令:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  當系統提示您**是否要建立自訂硬體設定檔 [否]** 時, 您可以輸入**no**並接受預設的硬體設定檔。 如果您說過, **avdmanager**會提示您自訂硬體設定檔的問題清單。

在您**avdmanager**以建立虛擬裝置之後, 它會包含在 [裝置] 下拉式功能表中:

[![已將新的 AVD 新增至裝置下拉式功能表](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  開啟**終端**機視窗, 並切換至 Mac 上 Android SDK tools 目錄的位置。 針對一般的 Xamarin 安裝, 您可以使用下列命令:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  使用[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令建立新的虛擬裝置。 例如, 若要使用適用于 API 層級26的 x86 系統映射來建立名為**AVD-Oreo-8.0**的 AVD, 請使用下列命令:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  當系統提示您**是否要建立自訂硬體設定檔 [否]** 時, 您可以輸入**no**並接受預設的硬體設定檔。 如果您說過, **avdmanager**會提示您自訂硬體設定檔的問題清單。

使用**avdmanager**建立虛擬裝置之後, 它會包含在 [裝置] 下拉式功能表中:

[![已將新的 AVD 新增至裝置下拉式功能表](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

如需設定 Android 模擬器以進行測試和偵測的詳細資訊, 請參閱[Android Emulator 上的調試](~/android/deploy-test/debugging/debug-on-emulator.md)程式。

如果您使用的是結點或圖元之類的實體裝置, 您可以透過無線 (OTA) 更新來更新您的裝置, 或下載系統映射並直接將裝置閃爍。 如需有關手動將裝置更新為 Android Oreo 的詳細資訊, 請參閱[適用于結點和圖元裝置的原廠映射](https://developers.google.com/android/images)。



## <a name="new-features"></a>新功能

Android Oreo 引進各種新特性和功能, 例如通知通道、通知徽章、XML 中的自訂字型、可下載的字型、自動填入, 以及圖片。 下列各節將重點放在您的應用程式中, 並提供可協助您開始使用這些功能的連結。



### <a name="notification-channels"></a>通知通道

*通知通道*是應用程式定義的通知類別。
您可以針對需要傳送的每一種通知類型建立通知通道, 也可以建立通知通道來反映應用程式使用者所做的選擇。 新的「通知通道」功能讓您可以讓使用者對不同類型的通知進行細微控制。 例如, 如果您要執行訊息應用程式, 您可以為使用者建立的每個交談群組建立個別的通知通道。

[通知通道](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan)會說明如何建立通知通道, 並使用它來張貼本機通知。 如需真實世界的程式碼範例, 請參閱[NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels)範例。這個範例應用程式會管理兩個通道, 並設定其他通知選項。



### <a name="notification-badges"></a>通知徽章

通知徽章是出現在應用程式圖示上的小點, 如下列螢幕擷取畫面所示:

[![應用程式圖示上的範例通知徽章](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

這些點表示應用程式&ndash;中的一或多個通知通道有新的通知, 這是使用者尚未關閉或採取行動的通知。 使用者可以長按圖示來概覽通知徽章的相關通知, 從 appeaars 的長按下功能表中關閉通知或採取行動。

如需有關通知徽章的詳細資訊, 請參閱 Android 開發人員[通知徽章](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges)主題。



### <a name="custom-fonts-in-xml"></a>XML 中的自訂字型

Android Oreo 引進了*XML*的字型, 讓您可以將自訂字型納入做為資源。 支援 OpenType ( **. otf**) 和 TrueType ( **. .ttf**) 字型格式。 若要將字型新增為資源, 請執行下列動作:

1. 建立**資源/字型**資料夾。

2. 將您的字型檔案 (例如, **.ttf**和**otf**檔案) 複製到**資源/字型**。 

3. 如有需要, 請將每個字型檔案重新命名, 使其符合 Android 檔案命名慣例 (也就是, 檔案名中只使用小寫 a-z、 *0-9*和底線)。 例如, 字型`Pacifico-Regular.ttf`檔案可能會重新命名為類似`pacifico.ttf`的內容。

4. 使用版面配置 XML 中的新`android:fontFamily`屬性來套用自訂字型。 例如, 下列`TextView`宣告會使用已加入的**pacifico. .ttf**字型資源:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

您也可以建立描述多個字型的字型家族 XML 檔案, 以及樣式和權數詳細資料。 如需詳細資訊, 請參閱 Android Developer [Fonts IN XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html)主題。


### <a name="downloadable-fonts"></a>可下載的字型

從 Android Oreo 開始, 應用程式可以向提供者要求字型, 而不是將其組合到 APK 中。 只有在需要時, 才會從網路下載字型。 這項功能可減少 APK 大小、節省電話的記憶體和行動資料的使用。 您也可以藉由安裝 Android 支援程式庫26套件, 在 Android API 14 版和更高版本上使用這項功能。

當您的應用程式需要字型時, 您`FontsRequest`可以建立物件 (指定要下載的字型), 然後將它`FontsContract`傳遞至方法以下載字型。 下列步驟將更詳細地說明字型下載程式:

1.  具現化[FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html)物件。 

2.  子類別和具現化[FontsContract. FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html)。

3.  執行用來處理完成字型要求的[FontRequestCallback OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29)方法。

4.  執行[FontRequestCallback. OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29)方法, 用來通知應用程式在字型要求過程中發生的任何錯誤。

5.  呼叫[FontsContract. RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback))方法, 以從字型提供者取得字型。 

當您呼叫`RequestFonts`方法時, 它會先檢查字型是否在本機快取 (從先前的`RequestFont`呼叫)。 如果未快取, 它會呼叫字型提供者, 以非同步方式抓取字型, 然後藉由叫用您`OnTypeFaceRetrieved`的方法, 將結果傳回到您的應用程式。

[可下載](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts)的字型範例示範如何使用 Android Oreo 中引進的可下載字型功能。 

如需下載字型的詳細資訊, 請參閱 Android 開發人員[可下載字型](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html)主題。



### <a name="autofill"></a>自動填滿

Android Oreo 中新的自動填滿架構可讓使用者更輕鬆地處理重複的工作, 例如登入、帳戶建立和信用卡交易。 使用者會花費較少的時間重新輸入資訊 (這可能會導致輸入錯誤)。 在您的應用程式可以使用自動填滿架構之前, 必須先在系統設定 (使用者可以啟用或停用自動填入) 中啟用自動填滿服務。

[AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/)範例示範如何使用自動填滿架構。 其中包括用戶端活動的執行, 以及應自動填入的視圖, 以及可提供自動填入資料給用戶端活動的服務。

如需有關新自動填入功能以及如何優化應用程式自動填入的詳細資訊, 請參閱 Android 開發人員自動填入[架構](https://developer.android.com/guide/topics/text/autofill.html)主題。



### <a name="picture-in-picture-pip"></a>圖片 (PIP)

Android Oreo 讓活動可以在圖片中啟動 (PIP) 模式, 以覆迭另一個活動的螢幕。 這項功能適用于影片播放。

若要指定應用程式的活動可以使用 PIP 模式, 請在 Android 資訊清單中將下列旗標設定為 true:

```xml
android:supportsPictureInPicture
```

若要指定活動在 PIP 模式時的行為方式, 請使用新的[PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html)物件。 `PictureInPictureParams`代表一組參數, 您可以用來初始化和更新 PIP 模式中的活動 (例如, 活動的慣用外觀比例)。 在 Android Oreo 中, 已將下列`Activity`新的 PIP 方法新增至:

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29)&ndash;將活動置於 PIP 模式。 活動會放在畫面的角落, 而畫面的其餘部分則會填入畫面上先前的活動。

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29)&ndash;更新活動的 PIP 設定 (例如, 外觀比例的變更)。

[PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture)範例示範在 Oreo 中引進的掌上型裝置 (PiP) 模式的基本使用方式。 此範例會播放影片, 而在顯示模式或其他活動之間來回切換時, 會繼續不中斷。



### <a name="other-features"></a>其他功能

Android Oreo 包含許多其他的新功能, 例如表情支援程式庫、位置 API、背景限制、適用于應用程式的寬範圍色彩、新的音訊編解碼器、Web 工作增強功能、改良的鍵盤流覽支援, 以及適用于的新 AAudio (pro 音訊) API高效能低延遲音訊: 如需這些功能的詳細資訊, 請參閱 Android 開發人員[Android Oreo 功能與 api](https://developer.android.com/about/versions/oreo/android-8.0.html)主題。



## <a name="behavior-changes"></a>行為變更

Android Oreo 包含各種系統和 API 行為變更, 可能會影響現有應用程式的功能。 這些變更如下所述。


### <a name="background-execution-limits"></a>背景執行限制

為了改善使用者體驗, Android Oreo 會對應用程式在背景中執行時的功能施加限制。 例如, 如果使用者觀看影片或玩遊戲, 則在背景中執行的應用程式可能會影響在前景中執行之影片密集型應用程式的效能。 因此, Android Oreo 會針對未直接與使用者互動的應用程式, 施加下列限制:

1.  **背景服務限制**&ndash;當應用程式在背景中執行時, 它會有數分鐘的時間範圍, 仍然允許它建立及使用服務。 在該視窗結束時, Android 會停止應用程式的背景服務, 並將它視為_閒置_。

2.  **廣播限制**&ndash; Android 7.0 (API 25) 在應用程式註冊要接收的廣播上放置限制。 Android Oreo 讓這些限制更嚴格。 例如, Android Oreo 應用程式無法再在其資訊清單中註冊隱含廣播的廣播接收器。

如需新背景執行限制的詳細資訊, 請參閱 Android 開發人員[背景執行限制](https://developer.android.com/about/versions/oreo/background.html)主題。


### <a name="breaking-changes"></a>重大變更

以 Android Oreo 或更新版本為目標的應用程式必須修改其應用程式, 以支援下列變更 (如果適用):

- Android Oreo 淘汰能夠設定個別通知的優先順序。 相反地, 您會在建立通知通道時設定建議的重要性層級。 您指派給通知通道的重要性層級, 會套用至您張貼至其上的所有通知訊息。

- 針對以 Android Oreo 為目標`PendingIntent.GetService()`的應用程式, 由於在背景中啟動的服務上所放置的新限制, 因此無法運作。 如果您的目標是 Android Oreo, 您應該改用[PendingIntent. GetBroadcast](xref:Android.App.PendingIntent.GetBroadcast*) 。  


## <a name="sample-code"></a>程式碼範例

有數個 Xamarin. Android 範例可向您示範如何利用 Android Oreo 功能:

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels)示範如何使用 Android Oreo 引進的新通知通道系統。 這個範例會管理兩個通知通道: 一個具有預設重要性, 另一個具有高重要性。

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture)示範 Oreo 中引進的掌上型裝置 (PiP) 模式的基本使用方式。 此範例會播放影片, 而在顯示模式或其他活動之間來回切換時, 會繼續不中斷。

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework)示範自動填滿架構的使用。 其中包括用戶端活動的執行, 以及應自動填入的視圖, 以及可提供自動填入資料給用戶端活動的服務。

-   [可下載](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts)的字型提供範例, 說明如何使用稍早所述的可下載字型功能。

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat)示範 EmojiCompat 支援程式庫的使用方式。 您可以使用此程式庫, 以防止您的應用程式將遺漏的表情字元顯示為 "tofu" 字元。

-   [位置更新暫止意圖](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent)說明使用`PendingIntent`位置 API 來取得裝置位置的相關更新。

-   [位置更新前景服務](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService)示範如何使用位置 API, 使用系結和啟動的前景服務來取得裝置位置的相關更新。


## <a name="video"></a>視訊

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Android 8.0 Oreo 開發C#**


## <a name="summary"></a>總結

本文介紹了 Android Oreo, 並說明如何在 Android Oreo 上安裝及設定最新的 Xamarin Android 開發工具和套件。 其中提供 Android Oreo 中可用的主要功能總覽, 其中包含數個新功能的範例原始程式碼連結。 其中包含 API 檔和 Android 開發人員主題的連結, 可協助您開始建立 Android Oreo 應用程式。 它也會反白顯示可能影響現有應用程式的最重要 Android Oreo 行為變更。


## <a name="related-links"></a>相關連結

- [Android 8.0 Oreo](https://developer.android.com/index.html)
