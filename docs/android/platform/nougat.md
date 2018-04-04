---
title: Nougat 功能
description: 如何開始使用 Xamarin.Android 來開發 Android Nougat 應用程式。
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: fe544f8ac677987f8921ccb1c11b8930811b9553
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="nougat-features"></a>Nougat 功能

_如何開始使用 Xamarin.Android 來開發 Android Nougat 應用程式。_

本文提供的 Android Nougat 中, 引進的功能外框說明如何準備 Xamarin.Android 開發 Android Nougat 項目，以及提供的連結，說明如何使用中的 Android Nougat 功能的範例應用程式Xamarin.Android 應用程式。


## <a name="overview"></a>總覽

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html)是以 Android 6.0 Marshmallow Google 的補充資料。 Xamarin.Android 提供支援**Android 7.x 繫結**在 Xamarin Android 7.0 和更新版本。 Android Nougat 新增許多 Api，如下所述之 Nougat 功能當您使用 Xamarin.Android 7.0 Xamarin.Android 應用程式使用這些 Api。

[![Android 平板電腦和手機執行 Android Nougat 英雄映像](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

如需 Android 7.x 應用程式開發介面的詳細資訊，請參閱[Android 開發人員 7.1](http://developer.android.com/preview/api-overview.html)。
如需 Xamarin.Android 7.0 的已知問題的清單，請參閱[版本資訊](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/)。

Android Nougat Xamarin.Android 開發人員提供感興趣的許多新功能。 這些功能包括：

-   **支援多重視窗**&ndash;這項增強功能可讓使用者可以同時開啟兩個螢幕上的應用程式。

-   **通知的增強功能**&ndash;包含重新設計的通知中的系統 Android Nougat*直接回覆*功能，讓使用者快速回應直接從通知文字訊息UI。 此外，如果您的應用程式建立的通知收到訊息，則新*配套通知*功能可以組合通知一起當做單一群組收到多個訊息時。

-   **資料保護裝置**&ndash;這個功能可協助減少應用程式的行動數據使用的新系統服務，它可讓使用者控制應用程式如何使用行動電話通訊的資料。

此外，Android Nougat 帶來許多其他增強功能，例如新的網路安全性設定功能的應用程式開發人員感興趣的 Doze 在行進間、 金鑰證明，新的快速設定應用程式開發介面，支援多重地區設定 ICU4J Api WebView 增強功能Java 8 語言功能的存取權、 已設定領域的目錄存取、 自訂指標 API、 VR 支援平台、 虛擬檔案，以及背景處理最佳化。

本文說明如何開始使用試用新的功能，並規劃移轉或功能的工作，新的 Android Nougat 平台為目標的 Android Nougat 建立應用程式。


## <a name="requirements"></a>需求

需要下列項目在 Xamarin 應用程式中使用新的 Android Nougat 功能：

-   **Visual Studio 或 Visual Studio for Mac** &ndash;如果您使用 Visual Studio 中，版本 4.2.0.628 或更新版本的 Visual Studio 的 Xamarin 是必要項。 如果您使用 Visual Studio for Mac、 6.1.0 版本或更新版本的 Visual Studio。 需要 Mac。

-   **Xamarin.Android** &ndash; Xamarin.Android 7.0 或更新版本必須安裝並設定 Visual Studio 或 Visual Studio for mac。

-   **Android SDK** -Android SDK 7.0 (應用程式開發介面 24) 或更新版本必須安裝 Android SDK Manager 透過。

-   **Java Developer Kit** &ndash; Xamarin Android 7.0 開發需要[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本，如果您正在開發的應用程式開發介面層級 24 或更大 （JDK 8 也支援應用程式開發介面層級早於 24）。 如果您使用自訂控制項或表單預覽程式需要 64 位元版本的 JDK 8。

> [!IMPORTANT]
> Xamarin.Android 不支援 JDK 9。

請注意，應用程式必須重建 Xamarin C6SR4 或更新版本中使用 Android Nougat 可靠地運作。 因為可以只能連結 Android Nougat [NDK 提供原生程式庫](https://developer.android.com/about/versions/nougat/android-7.0-changes.html)、 現有的應用程式使用像是 文件庫**Mono.Data.Sqlite.dll**執行 Android Nougat 上，如果不正確時，可能會損毀重建。



## <a name="getting-started"></a>快速入門

若要開始使用 Xamarin.Android Android Nougat，您必須下載並安裝最新工具與 SDK 封裝，才能建立 Android Nougat 專案：

1.  從在 Xamarin 安裝最新的 Xamarin.Android 更新。

2.  安裝**Android 7.0 (應用程式開發介面 24)**套件和工具或更新版本。

3.  建立新的 Xamarin.Android 專案的目標 Android Nougat。

4.  設定 Android Nougat 模擬器或裝置。

下列各節說明每個步驟：


### <a name="install-xamarin-updates"></a>安裝 Xamarin 更新

若要新增 Android Nougat Xamarin 支援，變更更新通道，在 Visual Studio 或 Visual Studio for Mac 穩定的通道，並套用最新的更新。 如果您也需要目前只適用於 Alpha 或 Beta 通道的功能，您可以切換至 Alpha 或 Beta 通道 （Alpha 和 Beta 頻道也提供支援 Android 7.x）。 如需如何變更更新 （版本） 通道，請參閱[變更更新通道](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/)。



### <a name="install-the-android-sdk"></a>安裝 Android SDK

若要建立專案時使用 Xamarin Android 7.0，您必須先使用 Android SDK Manager 安裝**SDK 平台 Android N (應用程式開發介面 24)**或更新版本。 您也必須安裝最新**Android SDK 工具**:

1.  啟動 Android SDK Manager (在 Visual Studio for Mac 使用**工具 > 開啟 Android SDK Manager&hellip;**; 在 Visual Studio 中，使用**工具 > Android > Android SDK Manager**)。

2.  安裝**Android 7.0 (應用程式開發介面 24)**或更新版本：

    [![選取 Android 7.0 封裝 Android SDK Manager 中](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3.  安裝最新 Android SDK 工具：

    [![Android SDK Manager 中選取最新 Android SDK 工具](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    您必須安裝 Android SDK 工具修訂 25.2.2 或更新版本、 Android SDK 平台工具 24.0.3 或更新版本，以及 Android SDK 建置工具 24.0.2 或更新版本。

4.  確認**Java Development Kit 位置**JDK 1.8 設定：

    [![設定在工具選項 下的 JDK 8 路徑](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    若要在 Visual Studio 中檢視這項設定，請按一下**工具 > 選項 > Xamarin > Android 設定**。 在 Visual Studio for Mac 按一下**喜好設定 > 專案 > SDK 位置 > Android**。



### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin.Android 專案

建立新的 Xamarin.Android 專案。 如果您是使用 Xamarin Android 開發的新手，請參閱[Hello，Android](~/android/get-started/hello-android/index.md)來了解如何建立 Xamarin.Android 專案。

當您建立 Android 專案時，您必須設定目標 Android 7.0 或更新版本的版本設定。 比方說，若要將專案目標適用於 Android 7.0，您必須設定您的專案的目標 Android API 層級**Android 7.0 (應用程式開發介面 24-Nougat)**。 我們建議您設定您的目標架構層級設為 24 應用程式開發介面或更新版本。 如需設定 Android API 層級的層級相關資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。


> [!NOTE]
> 目前，您必須設定**Android 最小版本**至**Android 7.0 (應用程式開發介面 24-Nougat)**將您的應用程式部署至 Android Nougat 裝置或模擬器。



### <a name="configure-an-emulator-or-device"></a>設定模擬器或裝置

如果您使用模擬器，Android AVD Manager 啟動，並建立新的裝置，使用下列設定：

-   裝置： Nexus 5 X Nexus 6 Nexus 6 P、 Nexus Player Nexus 9、 或像素 c。
-   目標： Android 7.0 應用程式開發介面層級 24
-   ABI: x86 或 x86\_64

例如，此虛擬裝置設定為模擬 Nexus 6:

[![設定使用 Nexus 6 裝置、 Android 7.0 目標和 Intel Atom x86 CPU/ABI AVD](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

如果您使用的實體裝置，例如 Nexus 5 X、 6 或 9，您可以透過自動更新您的裝置，透過無線方式 (OTA) 更新或下載系統映像並直接快閃裝置。 如需手動更新 Android Nougat 您裝置的詳細資訊，請參閱[OTA 映像 Nexus 裝置](https://developers.google.com/android/nexus/ota)。

請注意 Nexus 5 裝置都不會受到 Android Nougat。



## <a name="new-features"></a>新功能

Android Nougat 介紹各種不同的新特色與功能，例如支援多重視窗、 通知的增強功能和資料保護裝置。 下列各節會反白顯示這些功能，並提供連結，協助您開始在您的應用程式中使用它們。



### <a name="multi-window-mode"></a>多重視窗模式

多重視窗模式，讓使用者具有完整的多工支援同時開啟兩個應用程式。 這些應用程式可以在分割全螢幕模式中執行的並存 （橫向） 或一位以上--其他 （直印）。
使用者可以調整大小，在應用程式之間拖曳分割線，而且也可以剪下和貼上內容的應用程式之間。 當兩個應用程式會出現在多重視窗模式中時，選取的活動會繼續執行時未選取的活動已暫停，但仍然可見。 多重視窗模式不會修改的 Android 活動生命週期。

[![在縱向或橫向的多重視窗模式中執行的範例應用程式](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

您可以設定活動的 Xamarin.Android 應用程式如何支援多重視窗模式。 例如，您可以設定在多重視窗模式中設定的最小大小的預設高度和寬度的應用程式的屬性。 您可以使用新`Activity.IsInMultiWindowMode`屬性來判斷您的活動是否為多重視窗模式。 例如: 

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

[MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/)範例應用程式包含 C# 程式碼示範如何利用多個視窗的使用者介面時，您的應用程式。

如需多重視窗模式的詳細資訊，請參閱[支援多重視窗](https://developer.android.com/guide/topics/ui/multi-window.html)。



### <a name="enhanced-notifications"></a>增強的通知

Android Nougat 導入了重新設計的通知系統。 它的特色新的直接回覆功能，可讓使用者快速回覆的連入直接在通知 UI 中的文字訊息通知。 從 Android 7.0，訊息可以一同組合為單一群組收到多個訊息時的通知。 此外，開發人員可以自訂檢視，運用系統裝飾在通知中，並利用新的通知範本時產生通知的通知。


#### <a name="direct-reply"></a>直接回覆

當使用者收到內送訊息的通知時，Android Nougat 能夠讓回覆內通知的訊息 （而非傳送回覆訊息的應用程式開啟）。
此內嵌回覆功能可讓使用者快速回應的 SMS 或文字訊息通知介面中直接：

[![具有內嵌直接回覆欄位通知的螢幕擷取畫面](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

若要支援此功能在您的應用程式中，您必須加入*內嵌回覆動作*應用程式透過[RemoteInput](https://developer.xamarin.com/api/type/Android.App.RemoteInput/)物件，讓使用者可以透過簡訊回覆直接從 UI 的通知。
例如，下列程式碼的組建`RemoteInput`接收文字輸入，建立暫止的意圖回覆動作，並建立遠端輸入啟用的動作：

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

這個動作會加入至通知：

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

[訊息處理服務](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/)範例應用程式包含 C# 程式碼示範如何擴充與通知`RemoteInput`物件。 如需有關加入內嵌回覆您的應用程式的動作適用於 Android 7.0 或更新版本，請參閱 Android[通知回覆](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct)主題。


#### <a name="bundled-notifications"></a>配套的通知

Android Nougat 可以群組在一起 （例如，藉由訊息主題） 的通知訊息，並顯示群組，而不是每個個別的訊息。
這*配套通知*功能可讓使用者可以關閉或封存的群組中一個動作的通知。 使用者可以向下滑動，展開 通知，以檢視詳細資料中的每個通知的組合：

[![螢幕擷取畫面的配套通知的範例](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

若要支援搭售的通知，您的應用程式可以使用[Builder.SetGroup](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetGroup/p/System.String/)組合類似的通知方法。 如需在 Android N 搭售的通知群組的詳細資訊，請參閱 Android[結合在一起的通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle)主題。


#### <a name="custom-views"></a>自訂檢視

Android Nougat 可以讓您與系統通知的標頭、 動作和可擴充的版面配置建立自訂通知檢視。 如需 Android Nougat 中的自訂通知檢視的詳細資訊，請參閱 Android[通知增強功能](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements)主題。



### <a name="data-saver"></a>資料保護裝置

從 Android Nougat 開始，使用者可以啟用新*資料保護裝置*設定區塊背景資料使用量。 這項設定也會通知您的應用程式儘可能使用較少的資料在前景。 [ConnectivityManager](https://developer.xamarin.com/api/type/Android.Net.ConnectivityManager/)已經擴充 Android Nougat 中，您的應用程式可以檢查使用者是否已啟用資料保護裝置，讓您的應用程式可以為了啟用資料保護裝置時，限制其資料使用量。

如需 Android Nougat 中新的資料保護裝置功能的詳細資訊，請參閱 Android[最佳化網路使用量資料](https://developer.android.com/training/basics/network-ops/data-saver.html)主題。



### <a name="app-shortcuts"></a>應用程式快速鍵

導入的 android 7.1*應用程式捷徑*功能可讓您快速開始常見或建議使用工作應用程式的使用者。
若要啟動的捷徑功能表上，使用者長時間-按下的應用程式圖示的第二個或多個&ndash;功能表隨即出現，並快速震動。
放開按下，會導致保留的功能表：

[![訊息的應用程式的應用程式快顯功能表的畫面範例](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

這項功能是提供只有應用程式開發介面層級 25 或更高。
如需 Android 7.1 的新應用程式捷徑功能的詳細資訊，請參閱 Android[應用程式捷徑](https://developer.android.com/guide/topics/ui/shortcuts.html)主題。


### <a name="sample-code"></a>程式碼範例

數個 Xamarin.Android 範例可用來說明如何利用 Android Nougat 功能：

-   [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/)示範如何使用多重視窗提供的 API 中 Android Nougat。 您可以切換的範例應用程式至多 windows 模式，以查看它如何影響應用程式的生命週期和行為。

-   [訊息服務](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/)一個簡單服務，就會傳送通知使用`NotificationCompatManager`。 它也會擴充以通知`RemoteInput`允許 Android Nougat 裝置透過簡訊回覆直接從通知而不必開啟應用程式的物件。

-   [使用中通知](https://developer.xamarin.com/samples/monodroid/android-n/ActiveNotifications/)示範如何使用`NotificationManager`告訴您有多少個通知您的應用程式目前顯示的應用程式開發介面。

-   [範圍 目錄存取](https://developer.xamarin.com/samples/monodroid/android-n/ScopedDirectoryAccess/)示範如何使用已設定領域的目錄存取應用程式開發介面來輕鬆地存取特定目錄。 這可做為替代不必定義`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`程式資訊清單中的權限。

-   [直接開機](https://developer.xamarin.com/samples/monodroid/android-n/DirectBoot/)說明如何將資料儲存在裝置加密的存放裝置開機兩者之前和之後輸入任何使用者 credentials(PIN/Pattern/Password) 時，則一律可用。


## <a name="summary"></a>總結

本文介紹 Android Nougat，並說明如何安裝及設定 Android Nougat 上的最新工具和 Xamarin.Android 開發的封裝。 它也可協助您開始建立應用程式的 Android Nougat 範例原始程式碼的連結提供 Android Nougat 中, 可用的重要功能的概觀。


## <a name="related-links"></a>相關連結

- [Android 開發人員 7.1](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Xamarin Android 7.0 版本資訊](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/)
