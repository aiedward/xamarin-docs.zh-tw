---
title: 努加特功能
description: 如何開始使用Xamarin.Android開發AndroidNougat的應用程式。
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 6274c75abf229268070d495ced662724f5c16627
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027096"
---
# <a name="nougat-features"></a>努加特功能

_如何開始使用Xamarin.Android開發AndroidNougat的應用程式。_

本文概述了 Android Nougat 中介紹的功能,解釋了如何為 Android Nougat 開發準備 Xamarin.Android,並提供範例應用程式的連結,說明如何在 Xamarin.Android 應用程式中使用 Android Nougat 功能。

## <a name="overview"></a>概觀

[安卓努加特](https://developer.android.com/about/versions/nougat/android-7.0.html)是谷歌的後續行動Android 6.0棉花糖。 Xamarin.Android 在 Xamarin Android 7.0 及更高版本中為**Android 7.x 綁定**提供支援。 Android Nougat 為下面描述的努加特功能添加了許多新的 API;當您使用 Xamarin.Android 7.0 時,這些 API 可用於 Xamarin.Android 應用程式。

[![運行 Android Nougat 的 Android 平板電腦和手機的英雄圖像](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

有關 Android 7.x API 的詳細資訊,請參閱[適用於開發人員的 Android 7.1](https://developer.android.com/preview/api-overview.html)。
有關已知 Xamarin.Android 7.0 問題的清單,請參閱[發行說明](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)。

安卓努加特提供了許多感興趣的新功能,Xamarin.安卓開發者。 這些功能包括：

- **多視窗支援**&ndash;此增強功能使用戶能夠同時在螢幕上打開兩個應用。

- **通知增強**&ndash;Android Nougat 中重新設計的通知系統包含*一個直接回覆*功能,允許使用者直接從通知 UI 快速回應簡訊。 此外,如果應用為收到的消息創建通知,則新的*捆綁通知*功能可以在收到多條消息時將通知捆綁為單個組。

- **數據保存**&ndash;此功能是一種新的系統服務,可幫助減少應用使用蜂窩移動數據;它使用戶能夠控制應用如何使用蜂窩移動數據。

此外,Android Nougat 為應用開發人員帶來了許多其他感興趣的增強功能,例如新的網路安全配置功能、Goze、密鑰證明、新的快速設置 API、多區域設置支援、ICU4J API、WebView 增強功能、訪問 Java 8 語言功能、作用域目錄訪問、自定義指標 API、平臺 VR 支援、虛擬檔和後台處理優化。

本文介紹如何開始使用 Android Nougat 構建應用程式,以嘗試新功能並計劃遷移或功能工作,以定位新的 Android Nougat 平臺。

## <a name="requirements"></a>需求

在基於 Xamarin 的應用程式中使用新的 Android Nougat 功能需要以下操作:

- **Visual Studio 或 Mac 視覺工作室**&ndash;如果您正在使用 Visual Studio,則需要 Xamarin 的 Visual Studio 工具版本 4.2.0.628 或更高版本。 如果您使用的是 Mac 的 Visual Studio,則需要適用於 Mac 的 Visual Studio 版本 6.1.0 或更高版本。

- **Xamarin.Android** &ndash; Xamarin.Android 7.0 或更高版本必須安裝並配置用於 Mac 的 Visual Studio 或視覺工作室。

- **Android SDK** - Android SDK 7.0 (API 24) 或更高版本必須通過 Android SDK 管理器安裝。

- **Java 開發人員工具組**&ndash;Xamarin Android 7.0 開發需要[JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高版本(如果您正在開發 API 級別 24 或更高(JDK 8 還支援早於 24 的 API 級別)。 如果使用自定義控制件或表單預覽器,則需要 64 位元版本的 JDK 8。

> [!IMPORTANT]
> Xamarin.Android 不支援 JDK 9。

請注意,應用必須使用 Xamarin C6SR4 或更高版本重建,才能可靠地使用 Android Nougat。 由於 Android Nougat 只能連結到[NDK 提供的本機庫](https://developer.android.com/about/versions/nougat/android-7.0-changes.html),因此使用**Mono.Data.Sqlite.dll**等庫的現有應用在 Android Nougat 上運行時可能會崩潰,如果它們未正確重建。

## <a name="getting-started"></a>開始使用

要開始使用 Xamarin.Android 的 Android Android,您必須下載並安裝最新的工具和 SDK 套件,然後才能創建 Android Nougat 專案:

1. 安裝來自 Xamarin.Android 的最新更新。

2. 安裝**Android 7.0 (API 24)** 套件和工具或更高版本。

3. 創建一個新的Xamarin.Android專案,目標是安卓努加特。

4. 為安卓努加特配置模擬器或設備。

以下各節將介紹這些步驟:

### <a name="install-xamarin-updates"></a>安裝 Xamarin 更新

要添加 Xamarin 對 Android Nougat 的支援,請將 Mac 的 Visual Studio 或 Visual Studio 中的更新通道更改為"穩定"頻道並應用最新更新。 如果您需要目前僅在 Alpha 或 Beta 頻道中可用的功能,則可以切換到 Alpha 或 Beta 通道(Alpha 和 Beta 通道也提供對 Android 7.x 的支援)。 有關如何變更更新(發佈)通道的資訊,請參閱[變更更新通道](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel)。

### <a name="install-the-android-sdk"></a>安裝 Android SDK

要使用 Xamarin Android 7.0 建立項目,必須首先使用 Android SDK 管理器安裝**SDK 平臺 Android N (API 24)** 或更高版本。 您必須安裝最新的**Android SDK 工具**:

1. 啟動 Android SDK 管理器(在 Mac 可視化工作室中,使用**工具&hellip;> 打開 Android SDK 管理器**;在可視化工作室中,使用**工具> Android > Android SDK 管理器**)。

2. 安裝**Android 7.0 (API 24)** 或更高版本:

    [![在 Android SDK 管理器中選擇 Android 7.0 套件](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3. 安裝最新的 Android SDK 工具:

    [![在 Android SDK 管理員中選擇最新的 Android SDK 工具](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    您必須安裝 Android SDK 工具版本 25.2.2 或更高版本,Android SDK 平臺工具 24.0.3 或更高版本,以及 Android SDK 構建工具 24.0.2 或更高版本。

4. 認證**Java 開發工具套件位置**是否設定為 JDK 1.8:

    [![在「工具」選項下設定 JDK 8 路徑](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    要檢視此設定在視覺工作室, 按下**工具>選項> Xamarin > Android 設置**. 在 Mac 的可視化工作室中,按兩下 **「偏好>> Android 的 SDK 位置> SDK 位置**"。

### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin.安卓專案

創建新的 Xamarin.安卓專案。 如果您是使用 Xamarin 的 Android 開發的新使用者,請參閱[Hello,Android,](~/android/get-started/hello-android/index.md)瞭解如何創建 Xamarin.Android 專案。

創建 Android 專案時,必須將版本設置配置為以 Android 7.0 或更高版本為目標。 例如,要針對 Android 7.0 的項目,必須將專案的目標 Android API 級別配置為**Android 7.0(API 24 - Nougat)。** 建議您將目標框架級別設置為 API 24 或更高版本。 有關設定 Android API 等級的更多資訊,請參閱[瞭解 Android API 級別](~/android/app-fundamentals/android-api-levels.md)。

> [!NOTE]
> 目前,您必須將**最小 Android 版本**設定為 Android **7.0(API 24 - Nougat),** 才能將應用部署到 Android Nougat 設備或模擬器。

### <a name="configure-an-emulator-or-device"></a>設定模擬器或裝置

如果您使用的是模擬器,請啟動 Android AVD 管理員,並使用以下設定建立新裝置:

- 設備: Nexus 5X, Nexus 6, Nexus 6P, Nexus 播放器, Nexus 9, 或圖元 C.
- 目標: Android 7.0 - API 級別 24
- ABI: x86\_或 x86 64

例如,此虛擬設備配置為類比 Nexus 6:

[![使用 Nexus 6 設備、Android 7.0 目標和英特爾凌動 x86 CPU/ABI 配置 AVD](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

如果您正在使用物理設備(如Nexus 5X、6或9),則可以通過自動通過空氣 (OTA) 更新更新設備,或者下載系統映射並直接刷新設備。 有關手動將設備更新到 Android Nougat 的詳細資訊,請參閱[Nexus 裝置的 OTA 影像](https://developers.google.com/android/nexus/ota)。

請注意,Nexus 5 設備不受安卓 Nougat 的支援。

## <a name="new-features"></a>新功能

Android Nougat 引入了多種新功能和功能,如多窗口支援、通知增強功能和數據保存。 以下各節重點介紹這些功能,並提供連結,以説明您在應用中開始使用它們。

### <a name="multi-window-mode"></a>多視窗模式

多視窗模式使用戶能夠在完全支援的全多任務支援下同時打開兩個應用。 這些應用可以在分屏模式下並排運行(橫向)或一個高於其他(縱向)。
用戶可以在應用之間拖動分隔符以調整其大小,並且他們可以剪切和粘貼應用之間的內容。 當兩個應用以多視窗模式顯示時,所選活動將繼續運行,而未選擇的活動暫停但仍可見。 多視窗模式不會修改 Android 活動生命週期。

[![在縱向與橫向中以多視窗模式執行的應用程式範例](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

您可以設定 Xamarin.Android 應用的活動如何支援多視窗模式。 例如,您可以配置在多視窗模式下設置應用最小大小和預設高度和寬度的屬性。 可以使用新`Activity.IsInMultiWindowMode`屬性確定活動是否處於多視窗模式。 例如：

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

[多視窗遊樂場](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground)範例應用包括 C# 代碼,用於展示如何利用應用的多個視窗使用者介面。

關於多視窗模式的詳細資訊,請參閱[多視窗支援](https://developer.android.com/guide/topics/ui/multi-window.html)。

### <a name="enhanced-notifications"></a>增強通知

Android Nougat 引入了重新設計的通知系統。 它具有新的直接回覆功能,使用戶能夠直接在通知 UI 中快速回覆傳入文本訊息的通知。 從 Android 7.0 開始,當收到多條消息時,通知消息可以作為單個組捆綁在一起。 此外,開發人員還可以自定義通知檢視、利用通知中的系統修飾,並在生成通知時利用新的通知範本。

#### <a name="direct-reply"></a>直接回覆

當使用者收到傳入消息的通知時,Android Nougat 可以回復通知中的消息(而不是打開消息應用以發送回復)。
此內聯回覆功能使用戶能夠直接在通知介面中快速回應 SMS 或簡訊:

[![帶有內聯直接回覆欄位的通知螢幕截圖](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

要在應用中支援此功能,必須通過[RemoteInput](xref:Android.App.RemoteInput)物件向應用添加*內聯回覆操作*,以便使用者可以直接從通知 UI 通過文本進行回復。
例如,以下代碼產生 用於`RemoteInput`接收 文字輸入的 ,為答覆操作生成掛起的意圖,並建立啟用遠端輸入的操作:

```csharp
// Build a RemoteInput for receiving text input:
var remoteInput = new Android.Support.V4.App.RemoteInput.Builder (EXTRA_REMOTE_REPLY)
    .SetLabel (GetString (Resource.String.reply))
    .Build ();

// Build a Pending Intent for the reply action to trigger:
PendingIntent replyIntent = PendingIntent.GetBroadcast (ApplicationContext,
                                conversation.ConversationId,
                                GetMessageReplyIntent (conversation.ConversationId),
                                PendingIntentFlags.UpdateCurrent);

// Build an Android 7.0 compatible Remote Input enabled action:
NotificationCompat.Action actionReplyByRemoteInput = new NotificationCompat.Action.Builder (
    Resource.Drawable.notification_icon,
    GetString (Resource.String.reply),
    replyIntent).AddRemoteInput (remoteInput).Build ();
```

此動作將新增到通知中:

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

[消息服務](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice)範例應用包括 C# 代碼,用於`RemoteInput`展示如何使用 物件擴展通知。 有關為 Android 7.0 或更高版本向應用添加內聯回覆操作的詳細資訊,請參閱 Android[回復通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct)主題。

#### <a name="bundled-notifications"></a>捆綁通知

Android Nougat 可以將通知消息分組在一起(例如,按消息主題)並顯示組,而不是每個單獨的消息。
此*捆綁通知*功能使用戶能夠在一個操作中關閉或存檔一組通知。 使用者可以向下滑動以展開通知捆綁包以詳細查看每個通知:

[![捆綁通知的螢幕截圖範例](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

為了支援捆綁通知,你的應用可以使用[Builder.SetGroup](xref:Android.App.Notification.Builder.SetGroup*)方法捆綁類似的通知。 有關 Android N 中捆綁通知組的詳細資訊,請參閱 Android[捆綁通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle)主題。

#### <a name="custom-views"></a>自訂檢視

Android Nougat 使您能夠使用系統通知標頭、操作和可擴展布局創建自定義通知視圖。 有關 Android Nougat 中的自訂通知檢視的詳細資訊,請參閱 Android[通知增強功能](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements)主題。

### <a name="data-saver"></a>資料儲存程式

從 Android Nougat 開始,用戶可以啟用新的*數據保存設置*,阻止後台數據使用。 此設置還指示應用盡可能減少前臺使用的數據。 [連接管理器](xref:Android.Net.ConnectivityManager)已在 Android Nougat 中擴展,以便你的應用可以檢查使用者是否已啟用數據保存程式,以便應用可以在啟用數據保存時努力限制其數據使用量。

有關 Android Nougat 中新的數據保存功能的詳細資訊,請參閱 Android[優化網路數據使用](https://developer.android.com/training/basics/network-ops/data-saver.html)主題。

### <a name="app-shortcuts"></a>套用捷徑

Android 7.1 引入了*應用快捷方式*功能,使用戶能夠快速啟動應用的常見或建議任務。
要啟動快捷方式功能表,使用者會長時間按下應用圖示一秒鐘或更長時間&ndash;,功能表會快速振動。
釋放新聞會導致選單保持:

[![訊息應用的應用程式快捷方式選單的範例螢幕](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

此功能僅提供 API 級別 25 或更高版本。
有關 Android 7.1 中新的應用快捷方式功能的詳細資訊,請參閱 Android[應用快捷方式](https://developer.android.com/guide/topics/ui/shortcuts.html)主題。

### <a name="sample-code"></a>範例程式碼

有幾個 Xamarin.Android 示例可供您展示如何利用 Android 努加特功能:

- [多視窗遊樂場](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground)演示了 Android Nougat 中可用的多視窗 API 的使用。 您可以將示例應用切換到多視窗模式,以查看它如何影響應用的生命週期和行為。

- [消息服務](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice)是一種使用`NotificationCompatManager`發送 通知的簡單服務。 它還使用`RemoteInput`物件擴展通知,允許 Android Nougat 設備直接從通知中通過文本進行回復,而無需打開應用。

- [活動通知](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-activenotifications)簡報`NotificationManager`如何使用 API 告訴應用程式目前顯示的通知數。

- [範圍目錄存取](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-scopeddirectoryaccess)展示如何使用作用域目錄訪問 API 輕鬆存取特定目錄。 這替代了必須定義`READ_EXTERNAL_STORAGE``WRITE_EXTERNAL_STORAGE`或許可權的清單。

- [直接啟動](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-directboot)說明瞭如何在設備加密儲存中儲存數據,在輸入任何使用者憑據(PIN/模式/密碼)之前和之後啟動設備時始終可用。

## <a name="summary"></a>總結

本文介紹了Android Nougat,並解釋了如何安裝和配置最新的工具和軟體包Xamarin.Android開發在Android努加特。 它還概述了 Android Nougat 中提供的主要功能,並提供了指向範例原始程式碼的連結,以説明您開始為 Android Nougat 創建應用程式。

## <a name="related-links"></a>相關連結

- [面向開發人員的 Android 7.1](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Xamarin 安卓 7.0 發行說明](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)
