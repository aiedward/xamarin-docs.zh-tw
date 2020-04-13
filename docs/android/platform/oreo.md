---
title: 奧利奧功能
description: 如何開始使用Xamarin.Android開發適用於最新版本的Android應用程式。
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: davidortinau
ms.author: daortin
ms.date: 07/06/2018
ms.openlocfilehash: 56430f8c4988c16a31f9806b0ffb8b6355d6340b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020005"
---
# <a name="oreo-features"></a>奧利奧功能

_如何開始使用Xamarin.Android開發適用於最新版本的Android應用程式。_

[Android 8.0 奧利奧](https://developer.android.com/index.html)是最新的Android版本可從谷歌。 安卓奧利奧提供了許多感興趣的新功能,Xamarin.安卓開發者。 這些功能包括通知通道、通知徽章、XML 中的自定義字體、可下載字體、自動填充和圖片圖片 (PIP)。 Android Oreo 包含用於這些新功能的新 API,當您使用 Xamarin.Android 8.0 及更高版本時,這些 API 可供 Xamarin.Android 應用使用。

[![安卓奧利奧英雄形象](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

本文的結構是説明您開始開發 Xamarin.Android 應用程式,適用於 Android 8.0 Oreo。 它說明瞭如何安裝必要的更新、配置 SDK 和創建模擬器(或設備)以進行測試。 它還概述了 Android 8.0 Oreo 中的新功能,並提供了指向示例應用的連結,這些示例應用說明了如何在 Xamarin.Android 應用程式中使用 Android Oreo 功能。

## <a name="requirements"></a>需求

在基於 Xamarin 的應用程式中使用 Android Oreo 功能需要以下操作:

- **可視化工作室**&ndash;如果您正在使用 Windows,則需要 Visual Studio 的版本 15.5 或更高版本。  如果您使用的是 Mac,則需要適用於 Mac 版本 7.2.0 的可視化工作室。

- **Xamarin.安卓**&ndash;Xamarin.Android 8.0 或更高版本必須安裝並配置視覺工作室。

- **Android SDK** &ndash; Android SDK 8.0 (API 26) 或更高版本必須通過 Android SDK 管理器安裝。

## <a name="getting-started"></a>開始使用

要開始使用 Xamarin.Android 的 Android,您必須下載並安裝最新的工具和 SDK 套件,然後才能建立 Android Oreo 專案:

1. 更新至最新版本的可視化工作室。

2. 通過 SDK 管理器安裝**Android 8.0.0 (API 26)** 或更高版本的套件和工具。

3. 創建一個新的Xamarin.Android專案,目標是安卓奧利奧(API 26)。

4. 配置用於測試 Android Oreo 應用的模擬器或設備。

以下各節將介紹這些步驟:

### <a name="update-visual-studio-and-xamarinandroid"></a>更新視覺工作室和 Xamarin.安卓

要向視覺工作室添加 Android Oreo 支援,可以執行以下操作:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

- 對於 Visual Studio 2019,使用[SDK 管理器](~/android/get-started/installation/android-sdk.md)安裝 API 級別 26.0 或更高版本。

- 如果您使用的是視覺工作室 2017:

    1. 更新到視覺工作室 2017 版本 15.7 或更高版本 (請參閱[更新視覺工作室 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

    2. 使用[SDK 管理器](~/android/get-started/installation/android-sdk.md)安裝 API 級別 26.0 或更高版本。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

- 更新到最新的穩定版本的Visual工作室的Mac,如[更新視覺工作室的Mac](https://docs.microsoft.com/visualstudio/mac/update)解釋。

-----

有關 Xamarin 支援安卓奧利奧的更多資訊,請參閱[Xamarin.Android 8.0 發行說明](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/)。

### <a name="install-the-android-sdk"></a>安裝 Android SDK

要使用 Xamarin.Android 8.0 創建專案,您必須首先使用 Xamarin Android SDK 管理器來安裝**Android 8.0 - 奧利奧**或更高版本的 SDK 平臺。 您還必須安裝 Android SDK 工具 26.0 或更高版本。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 啟動 SDK 管理員(在可視化工作室中,按一下 **"工具> Android > Android SDK 管理器**)。

2. 安裝**Android 8.0 - 奧利奧**軟體包。 如果您使用的是 Android SDK 模擬器,請確保包含您需要的**x86**系統映像:

    [![在 Android SDK 管理器中選擇 Android 8.0 包](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. 安裝**Android SDK 工具 26.0.2**或更高版本 **、Android SDK 平臺工具 26.0.0**或更高版本,以及**Android SDK 構建工具 26.0.0(** 或更高版本):

    [![在 Android SDK 管理員中選擇 Android SDK 工具 26](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 啟動 SDK 管理員(在 Mac 的視覺化工作室中,按下 **'工具> SDK 管理員**)。

2. 安裝**Android 8.0 - 奧利奧**SDK 軟體包。 如果您使用的是 Android SDK 模擬器,請確保包含您需要的**x86**系統映像:

    [![在 SDK 管理員中選擇 Android 8.0 套件](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. 安裝**Android SDK 工具 26.0.2**或更高版本 **、Android SDK 平臺工具 26.0.0**或更高版本,以及**Android SDK 構建工具 26.0.0(** 或更高版本):

    [![在 SDK 管理員選擇 Android SDK 工具 26](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----

### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin.安卓專案

創建新的 Xamarin.安卓專案。 如果您是使用 Xamarin 的 Android 開發的新使用者,請參閱[Hello,Android,](~/android/get-started/hello-android/index.md)瞭解如何創建 Xamarin.Android 專案。

創建 Android 專案時,必須將版本設置配置為以 Android 8.0 或更高版本為目標。 例如,要針對 Android 8.0 的項目,必須將專案的目標 Android API 級別配置為**Android 8.0 (API 26)。** 建議您還將目標框架級別設置為 API 26 或更高版本。 有關設定 Android API 等級的更多資訊,請參閱[瞭解 Android API 級別](~/android/app-fundamentals/android-api-levels.md)。

### <a name="configure-an-emulator-or-device"></a>設定模擬器或裝置

如果您在安裝 Android SDK 工具 26.0 或更高版本後試著啟動預設的基於 Google GUI 的 AVD 管理員,您可能會收到以下錯誤對話框,該對話方塊指示您使用命令列 AVD 管理員工具**avdManager:**

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Android 模擬器管理員警告對話框](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![Android 模擬器管理員警告對話框](oreo-images/mac/03-avd-warning.png)

-----

顯示此消息是因為 Google 不再提供支援 API 26.0 及更高版本的獨立 GUI AVD 管理器。 對於 Android 8.0 奧利奧,您必須使用 Xamarin Android 模擬器`avdmanager`管理器或命令列 工具為 Android Oreo 創建虛擬設備。

要使用 Android 裝置管理員建立及管理虛擬裝置,請參閱[使用 Android 裝置管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md)。
要創建沒有 Android 裝置管理器的虛擬設備,請按照下一節中的步驟操作。

#### <a name="creating-virtual-devices-using-avdmanager"></a>使用虛擬管理員建立虛擬裝置

要使用**avdManager**建立新的虛擬裝置,請按照以下步驟操作:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 開啟命令提示器視窗,並`JAVA_HOME`設置為電腦上的 Java SDK 位置。 對於典型的 Xamarin 安裝,可以使用以下命令:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2. 將 Android SDK`bin`資料夾`PATH`的位置加入您的 。
    對於典型的 Xamarin 安裝,可以使用以下命令:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3. 關閉命令提示視窗並打開新的命令提示符視窗。 使用[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令建立新的虛擬裝置。 例如,要使用 API 級別 26 的 x86 系統映像建立名為**AVD-Oreo-8.0 的 AVD,** 請使用以下指令:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4. 當您被提示使用**是否希望創建自訂硬體設定檔 [否]** 時,可以輸入 **"否"** 並接受預設硬體設定檔。 如果你說**是****,avdManager**會提示你一個自定義硬體配置檔的問題清單。

**在建立**虛擬裝置後,它將包含在設備下拉選單中:

[![新 AVD 新增到裝置下拉選單](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 打開**終端**視窗並更改為 Mac 上的 Android SDK 工具目錄的位置。 對於典型的 Xamarin 安裝,可以使用以下命令:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2. 使用[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令建立新的虛擬裝置。 例如,要使用 API 級別 26 的 x86 系統映像建立名為**AVD-Oreo-8.0 的 AVD,** 請使用以下指令:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3. 當您被提示使用**是否希望創建自訂硬體設定檔 [否]** 時,可以輸入 **"否"** 並接受預設硬體設定檔。 如果你說**是****,avdManager**會提示你一個自定義硬體配置檔的問題清單。

使用**avdManager**建立虛擬裝置後,它將包含在設備下拉選單中:

[![新 AVD 新增到裝置下拉選單](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

有關設定 Android 模擬器以進行測試和除錯的詳細資訊,請參閱[Android 模擬器上的除錯](~/android/deploy-test/debugging/debug-on-emulator.md)。

如果您正在使用物理設備(如Nexus或 Pixel),則可以通過自動通過空氣 (OTA) 更新更新設備,或者下載系統映射並直接刷新設備。 有關手動將設備更新到 Android Oreo 的詳細資訊,請參閱[Nexus 和像素設備的工廠影像](https://developers.google.com/android/images)。

## <a name="new-features"></a>新功能

Android Oreo 引入了多種新功能和功能,如通知通道、通知徽章、XML 中的自定義字體、可下載字體、自動填充和圖片中畫。 以下各節重點介紹這些功能,並提供連結,以説明您在應用中開始使用它們。

### <a name="notification-channels"></a>通知通道

*通知通道*是應用定義的通知類別。
您可以為需要發送的每種類型的通知創建通知通道,還可以創建通知通道以反映應用使用者所做的選擇。 新的通知通道功能使您能夠為使用者提供對不同類型的通知的細粒度控制。 例如,如果要實現消息應用,則可以為使用者創建的每個對話組創建單獨的通知通道。

[通知渠道](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan)介紹了如何創建通知通道並使用它來發佈本地通知。 有關實際代碼示例,請參閱通知通道範例;請參閱"[通知通道](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels)"範例。此示例應用管理兩個通道並設置其他通知選項。

### <a name="notification-badges"></a>通知徽章

通知徽章是出現在應用圖示上的小點,如此屏幕截圖所示:

[![應用圖示上的範例通知徽章](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

這些點表示應用中與該應用圖示&ndash;關聯的一個或多個通知通道有新的通知,這些是使用者尚未拒絕或執行的通知。 用戶可以長按圖示查看與通知徽章關聯的通知,關閉或對來自應用者的長按功能表中的通知進行操作。

有關通知徽章的詳細資訊,請參閱 Android 開發人員[通知徽章](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges)主題。

### <a name="custom-fonts-in-xml"></a>XML 中的自訂字型

Android Oreo*引入了 XML 中的字體*,這使得您可以將自定義字體合併為資源成為可能。 支援 OpenType (**.otf**) 和 TrueType (**.ttf**) 字型格式。 要將字型新增為資源,可以執行以下操作:

1. 建立**資源/字型**資料夾。

2. 將字型檔案(例如 **.ttf**與 **.otf**檔案)複製到**資源/字型**。 

3. 如有必要,重新命名每個字體檔,使其符合 Android 檔案命名約定(即,僅使用小寫*a-z、0-9*和檔*0-9*名中的下劃線)。 例如,字型檔案`Pacifico-Regular.ttf`可以重新命名為`pacifico.ttf`類似 。

4. 使用佈局 XML`android:fontFamily`中的新 屬性應用自定義字型。 例如,以下`TextView`聲明使用添加的**pacifico.ttf**字型資源:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

您還可以創建一個字體系列 XML 檔,該檔描述多種字體以及樣式和權重詳細資訊。 有關詳細資訊,請參閱[XML 中的](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html)Android 開發人員字體主題。

### <a name="downloadable-fonts"></a>可下載字型

從 Android Oreo 開始,應用可以從供應商處請求字體,而不是將它們捆綁到 APK 中。 字體僅根據需要從網路下載。 此功能可減少 APK 大小,節省手機記憶體和蜂窩移動數據使用量。 您還可以透過安裝 Android 支援庫 26 包在 Android API 版本 14 及更高版本上使用此功能。

當應用需要字體時,您將創建一個`FontsRequest`物件(指定要下載的字體),然後將它傳遞給一`FontsContract`個 方法來下載字體。 以下步驟更詳細地描述了字體下載過程:

1. 實例化[字體請求](https://developer.android.com/reference/android/provider/FontRequest.html)物件。 

2. 子類與實體化[字型合約.Fontrequest回調](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html)。

3. 實現[Fontrequest 回檔.OnTypeFace 檢索](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29)方法,該方法用於處理字體請求的完成。

4. 實現[FontrequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29)方法,該方法用於通知應用在字體請求過程中發生的任何錯誤。

5. 調用[字體合同.requestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback))方法從字體提供程式檢索字體。 

呼叫`RequestFonts`方法時,它首先檢查字型是否本地緩存(從上一個調用`RequestFont`到 )。 如果未緩存,它將調用字體提供程式,非同步檢索字體,然後透過調用方法`OnTypeFaceRetrieved`將結果傳回應用。

[可下載字體](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts)範例展示如何使用 Android Oreo 中介紹的可下載字體功能。 

有關下載字體的詳細資訊,請參閱 Android 開發人員[可下載字體](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html)主題。

### <a name="autofill"></a>自動填寫

Android Oreo 中新的_自動填充_框架使用戶更容易處理重複性任務,如登錄、帳戶創建和信用卡交易。 用戶花費更少的時間重新鍵入資訊(這可能導致輸入錯誤)。 在應用可以使用自動填充框架之前,必須在系統設置中啟用自動填充服務(用戶可以啟用或禁用自動填充)。

[自動填充框架](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework)範例演示了自動填滿框架的使用。 它包括具有應自動填充的檢視的用戶端活動實現,以及可以為用戶端活動提供自動填充數據的服務。

有關新的自動填充功能以及如何優化應用進行自動填充的詳細資訊,請參閱 Android 開發人員[自動填充框架](https://developer.android.com/guide/topics/text/autofill.html)主題。

### <a name="picture-in-picture-pip"></a>圖片中的圖片 (PIP)

Android Oreo 使活動可以在圖片 (PIP) 模式下啟動,覆蓋另一個活動的螢幕。 此功能用於視頻播放。

要指定應用程式的活動可以使用 PIP 模式,請在 Android 清單中將以下標誌設定為 true:

```xml
android:supportsPictureInPicture
```

要指定活動在 PIP 模式下的行為方式,請使用新的[PictureInPicture Params](https://developer.android.com/reference/android/app/PictureInPictureParams.html)物件。 `PictureInPictureParams`表示一組參數,用於在 PIP 模式下初始化和更新活動(例如,活動的首選縱橫比)。 以下新的 PIP 方法已`Activity`新增到 Android Oreo 中:

- [進入圖片模式](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29)&ndash;將活動置於 PIP 模式。 活動放置在螢幕的一角,螢幕的其餘部分將填充螢幕上的上一個活動。

- [SetPicturePictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29)&ndash;更新活動的 PIP 配置設定(例如,縱橫比的變化)。

[PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture)示例演示了奧利奧引入的手持設備"畫中畫(PiP)"模式的基本使用方式。 該示例播放視頻,該視頻在顯示模式或其他活動之間來回切換時,會不間斷地播放。

### <a name="other-features"></a>其他功能

Android Oreo 包含許多其他新功能,如表情符號支援庫、位置 API、背景限制、應用寬域色、新音訊編解碼器、WebView 增強功能、改進的鍵盤導航支援以及用於高性能低延遲音訊的新 AAudio(專業音訊)API,有關這些功能的詳細資訊,請參閱 Android 開發人員[Android Oreo 功能和 API](https://developer.android.com/about/versions/oreo/android-8.0.html)主題。

## <a name="behavior-changes"></a>行為變更

Android Oreo 包括各種系統和 API 行為更改,這些更改可能會影響現有應用的功能。 這些更改描述如下。

### <a name="background-execution-limits"></a>背景執行限制

為了改善用戶體驗,Android Oreo 對應用在後台運行時可以執行哪些操作施加了限制。 例如,如果使用者正在觀看視頻或玩遊戲,則在後台運行的應用可能會損害在前台運行的視頻密集型應用的性能。 因此,Android Oreo 對未與使用者直接交互的應用實施以下限制:

1. **後台服務限制**&ndash;當應用在後台運行時,它有一個幾分鐘的視窗,在其中它仍然允許它創建和使用服務。 在視窗的末尾,Android 停止應用的背景服務,並將其視為_空閒_。

2. **廣播限制**&ndash;Android 7.0 (API 25) 對應用註冊接收的廣播施加了限制。 安卓奧利奧使這些限制更加嚴格。 例如,Android Oreo 應用無法再為其清單中隱式廣播註冊廣播接收器。

有關新的後台執行限制的詳細資訊,請參閱 Android 開發人員[後台執行限制](https://developer.android.com/about/versions/oreo/background.html)主題。

### <a name="breaking-changes"></a>重大變更

針對 Android Oreo 或更高版本的應用必須修改其應用以支援以下變更(如果適用):

- Android Oreo 棄用設置單個通知優先順序的功能。 相反,在創建通知通道時,您可以設置建議的重要性級別。 分配給通知通道的重要性級別適用於您發佈到它的所有通知消息。

- 對於面向 Android Oreo`PendingIntent.GetService()`的應用,由於對在後台啟動的服務設置了新的限制,因此不起作用。 如果您的目標是安卓奧利奧,您應該使用["待定意向.獲取廣播](xref:Android.App.PendingIntent.GetBroadcast*)"。  

## <a name="sample-code"></a>範例程式碼

有幾個 Xamarin.Android 範例可供向您展示如何利用 Android Oreo 功能:

- [通知管道](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels)演示如何使用 Android Oreo 中引入的新通知通道系統。 此示例管理兩個通知通道:一個具有預設重要性,另一個具有高度重要性。

- [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture)演示了奧利奧引入的手持設備在畫中畫 (PiP) 模式的基本用途。 該示例播放視頻,該視頻在顯示模式或其他活動之間來回切換時,會不間斷地播放。

- [自動填滿框架](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework)演示了自動填充框架的使用。 它包括具有應自動填充的檢視的用戶端活動實現,以及可以為用戶端活動提供自動填充數據的服務。

- [可下載字型](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts)提供了如何使用前面描述的可下載字體功能的範例。

- [表情符號Compat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-emojicompat)演示了表情符號康柏支援庫的使用方式。 您可以使用此庫來防止應用將缺少的表情符號字元顯示為「豆腐」字元。

- [位置更新掛起意圖](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdpendintent)說明了使用 的位置 API 取得有關裝置位置的`PendingIntent`更新的使用方式 。

- [位置更新前臺服務](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdfgservice)演示如何使用位置 API 使用綁定和啟動的前景服務獲取有關設備位置的更新。

## <a name="video"></a>影片

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**安卓 8.0 奧利奧開發與 C#**

## <a name="summary"></a>總結

本文介紹了Android Oreo,並解釋了如何安裝和配置最新的工具和軟體包Xamarin.Android開發在Android奧利奧。 它提供了 Android Oreo 中提供的主要功能的概述,並提供了幾個新功能的示例原始程式碼的連結。 它包括指向 API 文件和 Android 開發人員主題的連結,可説明您開始為 Android Oreo 創建應用。 它還強調了可能影響現有應用的最重要 Android Oreo 行為更改。

## <a name="related-links"></a>相關連結

- [安卓 8.0 奧利奧](https://developer.android.com/index.html)
