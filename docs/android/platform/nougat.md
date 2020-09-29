---
title: Nougat 功能
description: 如何開始使用 Xamarin 來開發適用于 Android Nougat 的應用程式。
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 135303743cce1f23bb1d70731f6e760188df11e2
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457207"
---
# <a name="nougat-features"></a>Nougat 功能

_如何開始使用 Xamarin 來開發適用于 Android Nougat 的應用程式。_

本文提供 Android Nougat 中引進的功能大綱，說明如何準備適用于 Android Nougat 開發的 Xamarin，以及提供範例應用程式的連結，說明如何在 Xamarin Android 應用程式中使用 Android Nougat 功能。

## <a name="overview"></a>概觀

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) 是 Google 對 Android 6.0 Marshmallow 的後續追蹤。 Xamarin 提供 Xamarin Android 7.0 和更新版本中 **android** 7.x 系結的支援。 Android Nougat 新增許多 Nougat 功能的新 Api，如下所述;當您使用 Xamarin 7.0. android 應用程式時，可以使用這些 Api。

[![執行 Android Nougat 的 Android 平板電腦和手機影像](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

如需有關 Android 7.x Api 的詳細資訊，請參閱 [適用于開發人員的 android 7.1](https://developer.android.com/preview/api-overview.html)。
如需已知的 Xamarin. Android 7.0 問題清單，請參閱 [版本](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)資訊。

Android Nougat 為 Xamarin 開發人員提供許多相關的新功能。 這些功能包括：

- **多視窗支援** &ndash; 這項增強功能可讓使用者一次在螢幕上開啟兩個應用程式。

- **通知增強功能** &ndash; Android Nougat 中重新設計的通知系統包含 *直接回復* 功能，可讓使用者直接從通知 UI 快速回應文字訊息。 此外，如果您的應用程式會建立已接收訊息的通知，則新的 *配套通知* 功能可在收到一個以上的訊息時，將通知組合成單一群組。

- **資料保護** &ndash; 這項功能是一項新的系統服務，可協助減少應用程式所使用的行動電話資料。它可讓使用者控制應用程式如何使用行動電話資料。

此外，Android Nougat 還為應用程式開發人員帶來了許多其他感興趣的增強功能，例如新的網路安全性設定功能、執行 Doze、金鑰證明、新的快速設定 Api、多重地區設定支援、ICU4J Api、流覽功能增強、JAVA 8 語言功能的存取、範圍目錄存取、自訂指標 API、平臺 VR 支援、虛擬檔案，以及背景處理優化

本文說明如何開始使用 Android Nougat 來建立應用程式，以試用新的功能，並規劃以新的 Android Nougat 平臺為目標的遷移或功能工作。

## <a name="requirements"></a>需求

在 Xamarin 應用程式中使用新的 Android Nougat 功能時，需要下列各項：

- **Visual Studio 或 Visual Studio for Mac** &ndash; 如果您使用 Visual Studio，則需要適用于 Xamarin 的 Visual Studio Tools 版本4.2.0.628 或更新版本。 如果您使用 Visual Studio for Mac，則需要 Visual Studio for Mac 版本6.1.0 或更新版本。

- **Xamarin.Android**您 &ndash; 必須安裝 Visual Studio 或 Visual Studio for Mac，才能安裝並設定 Xamarin. Android xamarin. android 7.0 或更新版本。

- 您必須透過 Android SDK 管理員安裝**Android SDK** -Android SDK 7.0 (API 24) 或更新版本。

- **JAVA 開發人員套件** &ndash; 如果您正在針對 API 層級24或更高的版本進行開發，則需要 [jdk 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 或更新版本的 Xamarin Android 7.0 開發 (jdk 8 也支援早于 24) 的 api 層級。 如果您使用自訂控制項或表單預覽程式，則需要64位版本的 JDK 8。

> [!IMPORTANT]
> Xamarin.Android 不支援 JDK 9。

請注意，您必須使用 Xamarin C6SR4 或更新版本來重建應用程式，才能使用 Android Nougat 來可靠地運作。 因為 Android Nougat 只能連結至 [NDK 提供的原生程式庫](https://developer.android.com/about/versions/nougat/android-7.0-changes.html)，所以使用程式庫（例如 **Mono.Data.Sqlite.dll** ）的現有應用程式在 Android Nougat 上執行時可能會損毀（如果未適當重建）。

## <a name="getting-started"></a>快速入門

若要開始搭配使用 Android Nougat 與 Xamarin，您必須先下載並安裝最新的工具和 SDK 套件，才能建立 Android Nougat 專案：

1. 從 Xamarin 安裝最新的 Xamarin. Android 更新。

2. 安裝 **Android 7.0 (API 24) ** 套件和工具或更新版本。

3. 建立以 Android Nougat 為目標的新 Xamarin Android 專案。

4. 設定適用于 Android Nougat 的模擬器或裝置。

下列各節將說明每個步驟：

### <a name="install-xamarin-updates"></a>安裝 Xamarin 更新

若要新增適用于 Android Nougat 的 Xamarin 支援，請將 Visual Studio 或 Visual Studio for Mac 中的更新通道變更為穩定通道，並套用最新的更新。 如果您還需要的功能目前僅適用于 Alpha 或搶鮮版（Beta）通道，您可以切換到 Alpha 或搶鮮版（Beta）通道 (Alpha 和搶鮮版（Beta）通道也提供 Android 7.x) 的支援。 如需有關如何變更更新 (發行) 通道的詳細資訊，請參閱 [變更更新通道](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel)。

### <a name="install-the-android-sdk"></a>安裝 Android SDK

若要建立具有 Xamarin Android 7.0 的專案，您必須先使用 Android SDK 管理員安裝 **SDK Platform Android N (API 24) ** 或更新版本。 您也必須安裝最新的 **Android SDK Tools**：

1. 啟動 Visual Studio for Mac 中的 Android SDK 管理員 (、使用 [**工具] > &hellip; [開啟] Android SDK 管理員**; 在 Visual Studio 中，使用 > **Android > Android SDK 管理員**) 的工具。

2. 安裝 **Android 7.0 (API 24) ** 或更新版本：

    [![在 Android SDK 管理員中選取 Android 7.0 套件](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3. 安裝最新的 Android SDK 工具：

    [![選取 Android SDK 管理員中最新的 Android SDK 工具](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    您必須安裝 Android SDK Tools revision 25.2.2 或更新版本、Android SDK Platform tools 24.0.3 或更新版本，以及 Android SDK Build tools 24.0.2 或更新版本。

4. 確認已針對 JDK 1.8 設定 **JAVA 開發工具組位置** ：

    [![在 [工具選項] 下設定 JDK 8 路徑](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    若要在 Visual Studio 中查看這項設定，請按一下 [ **工具] > [選項] > Xamarin > Android 設定**]。 在 Visual Studio for Mac 中，按一下 [喜好設定] **> [專案] > > Android 的 SDK 位置**。

### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin. Android 專案

建立新的 Xamarin. Android 專案。 如果您是使用 Xamarin 進行 Android 開發的新手，請參閱 [Hello，Android](~/android/get-started/hello-android/index.md) 以瞭解如何建立 Xamarin. android 專案。

當您建立 Android 專案時，您必須將版本設定設定為以 Android 7.0 或更新版本為目標。 例如，若要以 Android 7.0 的專案為目標，您必須將專案的目標 Android API 層級設定為 **android 7.0 (API 24-Nougat) **。 建議您將目標 framework 層級設定為 API 24 或更新版本。 如需設定 Android API 層級層級的詳細資訊，請參閱 [瞭解 ANDROID Api 層](~/android/app-fundamentals/android-api-levels.md)級。

> [!NOTE]
> 目前，您必須將 **最低 android 版本** 設定為 **ANDROID 7.0 (API 24-Nougat) ** ，以將您的應用程式部署至 android Nougat 裝置或模擬器。

### <a name="configure-an-emulator-or-device"></a>設定模擬器或裝置

如果您使用模擬器，請啟動 Android AVD 管理員，並使用下列設定來建立新的裝置：

- 裝置：結點5X、結點6、結點6P、結點 Player、結點9或圖元 C。
- 目標： Android 7.0-API 層級24
- ABI： x86 或 x86 \_ 64

例如，此虛擬裝置設定為模擬一個結點6：

[![使用結點6裝置、Android 7.0 目標和 Intel Atom x86 CPU/ABI 設定 AVD](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

如果您使用的是實體裝置，例如結點5、6或9，您可以透過無線 (OTA) 更新或下載系統映射，並直接將裝置閃爍，以更新裝置。 如需有關手動將裝置更新為 Android Nougat 的詳細資訊，請參閱 [適用于結點裝置的 OTA 映射](https://developers.google.com/android/nexus/ota)。

請注意，Android Nougat 不支援 [結點 5] 裝置。

## <a name="new-features"></a>新功能

Android Nougat 引進各種新功能，例如多視窗支援、通知增強功能，以及資料保護。 下列各節會強調這些功能，並提供可協助您在應用程式中開始使用這些功能的連結。

### <a name="multi-window-mode"></a>多視窗模式

多視窗模式讓使用者可以一次開啟兩個應用程式，並提供完整的多工支援。 這些應用程式可以在分割畫面模式下，並存執行 (橫向) 或一對多 (直向) 。
使用者可以拖曳應用程式之間的分隔線來調整其大小，並可在應用程式之間剪下和貼上內容。 當兩個應用程式在多視窗模式中呈現時，選取的活動會在未選取的活動暫停但仍然可見時繼續執行。 多視窗模式不會修改 Android 活動生命週期。

[![在直向和橫向的多視窗模式中執行的範例應用程式](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

您可以設定您的 Xamarin Android 應用程式活動如何支援多視窗模式。 例如，您可以設定屬性，以在多視窗模式中設定應用程式的大小下限和預設高度和寬度。 您可以使用新的 `Activity.IsInMultiWindowMode` 屬性來判斷您的活動是否處於多視窗模式。 例如：

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

[MultiWindowPlayground](/samples/xamarin/monodroid-samples/android-n-multiwindowplayground)範例應用程式包含 c # 程式碼，示範如何利用應用程式的多個視窗使用者介面。

如需有關多視窗模式的詳細資訊，請參閱 [多視窗支援](https://developer.android.com/guide/topics/ui/multi-window.html)。

### <a name="enhanced-notifications"></a>增強的通知

Android Nougat 引進了重新設計的通知系統。 它具備新的直接回復功能，可讓使用者直接在通知 UI 中，快速回復內送文字訊息的通知。 從 Android 7.0 開始，當收到一個以上的訊息時，通知訊息可以組合成單一群組。 此外，開發人員也可以自訂通知查看、在通知中利用系統裝飾，並在產生通知時利用新的通知範本。

#### <a name="direct-reply"></a>直接回復

當使用者收到內送訊息的通知時，Android Nougat 可以回復通知中的訊息 (而不是開啟訊息應用程式來傳送回復) 。
這項內嵌回復功能讓使用者可以直接在通知介面內快速回應 SMS 或文字訊息：

[![具有內嵌直接回復欄位的通知螢幕擷取畫面](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

若要在您的應用程式中支援這項功能，您必須透過[RemoteInput](xref:Android.App.RemoteInput)物件將*內嵌回復動作*新增至您的應用程式，讓使用者可以直接從通知 UI 透過文字進行回復。
例如，下列程式碼會建立 `RemoteInput` 以接收文字輸入、建立回復動作的擱置意圖，以及建立遠端輸入啟用的動作：

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

此動作會新增至通知：

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

[訊息服務](/samples/xamarin/monodroid-samples/android-n-messagingservice)範例應用程式包含 c # 程式碼，該程式碼會示範如何使用物件來擴充通知 `RemoteInput` 。 如需將內嵌回復動作新增至 Android 7.0 或更新版本之應用程式的詳細資訊，請參閱 Android [回復通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) 主題。

#### <a name="bundled-notifications"></a>配套的通知

Android Nougat 可以將通知訊息群組在一起 (例如，訊息主題) 並顯示群組，而不是每個個別訊息。
這項 *配套的通知* 功能讓使用者可以在一個動作中關閉或封存一組通知。 使用者可以向下滑動以展開通知的組合，以詳細地查看每個通知：

[![配套通知的螢幕擷取畫面範例](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

若要支援配套的通知，您的應用程式可以使用 [SetGroup](xref:Android.App.Notification.Builder.SetGroup*) 方法來組合類似的通知。 如需 Android N 配套通知群組的詳細資訊，請參閱 Android 套件組合 [通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) 主題。

#### <a name="custom-views"></a>自訂檢視

Android Nougat 可讓您使用系統通知標頭、動作和可展開的配置來建立自訂通知流覽。 如需 Android Nougat 中自訂通知查看的詳細資訊，請參閱 Android [通知增強功能](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) 主題。

### <a name="data-saver"></a>資料保護

從 Android Nougat 開始，使用者可以啟用新的 *資料保護* 設定，以封鎖背景資料使用量。 這項設定也會通知您的應用程式，盡可能在前景使用較少的資料。 [ConnectivityManager](xref:Android.Net.ConnectivityManager)已在 Android Nougat 中擴充，讓您的應用程式可以檢查使用者是否已啟用資料保護，讓您的應用程式可以在啟用資料保護時，盡力限制其資料使用量。

如需有關 Android Nougat 中新的資料保護功能的詳細資訊，請參閱 Android [優化網路資料使用量](https://developer.android.com/training/basics/network-ops/data-saver.html) 主題。

### <a name="app-shortcuts"></a>應用程式快捷方式

Android 7.1 引進了 *應用程式快捷方式* 功能，讓使用者可以使用您的應用程式快速啟動一般或建議的工作。
若要啟用快速鍵的功能表，使用者長按一或多個功能表上的應用程式圖示 &ndash; 時，會出現快速振動。
釋出時，功能表會保持不變：

[![訊息應用程式之應用程式快捷方式功能表的範例畫面](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

這項功能僅適用于 API 層級25或更高版本。
如需 Android 7.1 中新應用程式快捷方式功能的詳細資訊，請參閱 Android [應用程式快捷方式](https://developer.android.com/guide/topics/ui/shortcuts.html) 主題。

### <a name="sample-code"></a>範例程式碼

有數個 Xamarin Android 範例可示範如何利用 Android Nougat 功能：

- [MultiWindowPlayground](/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) 示範如何使用 Android Nougat 中可用的多視窗 API。 您可以將範例應用程式切換為多視窗模式，以查看它如何影響應用程式的生命週期和行為。

- [訊息服務](/samples/xamarin/monodroid-samples/android-n-messagingservice) 是使用來傳送通知的簡單服務 `NotificationCompatManager` 。 它也會以物件延伸通知， `RemoteInput` 讓 Android Nougat 裝置可以直接從通知透過文字回復，而不需要開啟應用程式。

- 作用中[通知](/samples/xamarin/monodroid-samples/android-n-activenotifications)示範如何使用 `NotificationManager` API 來告訴您應用程式目前顯示的通知數目。

- 限[域目錄存取](/samples/xamarin/monodroid-samples/android-n-scopeddirectoryaccess)示範如何使用限定範圍的目錄存取 API 來輕鬆地存取特定目錄。 這是 `READ_EXTERNAL_STORAGE` `WRITE_EXTERNAL_STORAGE` 在您的資訊清單中必須定義或許可權的替代方案。

- [直接開機](/samples/xamarin/monodroid-samples/android-n-directboot) 說明如何將資料儲存在裝置加密的儲存體中，當裝置在輸入任何使用者認證 (釘選/模式/密碼) 之前和之後都能隨時進行開機。

## <a name="summary"></a>摘要

本文介紹 Android Nougat，並說明如何在 Android Nougat 上安裝及設定適用于 Xamarin 的最新工具和套件。 它也提供了 Android Nougat 中可用的主要功能，並提供範例原始程式碼的連結，協助您開始建立 Android Nougat 應用程式。

## <a name="related-links"></a>相關連結

- [適用于開發人員的 Android 7。1](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Xamarin Android 7.0 版本資訊](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)