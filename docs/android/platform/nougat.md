---
title: Nougat 功能
description: 如何開始使用 Xamarin 來開發 Android Nougat 應用程式。
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: 128982abdee7a0fea8df79f7b7b9ecd6a290775a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761250"
---
# <a name="nougat-features"></a>Nougat 功能

_如何開始使用 Xamarin 來開發 Android Nougat 應用程式。_

本文概述 Android Nougat 中引進的功能，說明如何準備 Xamarin 以進行 Android Nougat 開發，並提供範例應用程式的連結，說明如何在中使用 Android Nougat 功能Xamarin Android 應用程式。

## <a name="overview"></a>總覽

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html)是 Google 對 Android 6.0 Marshmallow 的後續追蹤。 Xamarin 支援 Xamarin Android 7.0 和更新版本中的**android 7. x**系結。 Android Nougat 新增了許多 Nougat 功能的新 Api，如下所述：當您使用 Xamarin. Android 7.0 時，這些 Api 可供 Xamarin 應用程式使用。

[![執行 Android Nougat 之 Android 平板電腦和手機的主圖影像](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

如需 Android 7.x Api 的詳細資訊，請參閱[適用于開發人員的 android 7.1](https://developer.android.com/preview/api-overview.html)。
如需已知的 Xamarin. Android 7.0 問題清單，請參閱[版本](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)資訊。

Android Nougat 為 Xamarin. Android 開發人員提供許多相關的新功能。 這些功能包括：

- **多視窗支援**&ndash;這項增強功能讓使用者可以在畫面上一次開啟兩個應用程式。

- **通知增強功能**Android Nougat 中重新設計的通知系統包含*直接回復*功能，可讓使用者直接從通知 UI 快速回應文字訊息。 &ndash; 此外，如果您的應用程式為已接收的訊息建立通知，新的*配套通知*功能可以在收到一個以上的訊息時，將通知組合成單一群組。

- **資料保護**&ndash;這項功能是新的系統服務，可協助減少應用程式使用的行動資料，讓使用者可以控制應用程式使用行動資料的方式。

此外，Android Nougat 為應用程式開發人員提供了許多其他的增強功能，例如新的網路安全性設定功能、Doze 的 Go、金鑰證明、新的快速設定 Api、多地區設定支援、ICU4J Api、Web 工作增強功能、存取 JAVA 8 語言功能、限定範圍的目錄存取、自訂指標 API、平臺 VR 支援、虛擬檔案和背景處理優化。

本文說明如何使用 Android Nougat 來開始建立應用程式，以試用新功能並規劃遷移或功能工作，以新的 Android Nougat 平臺為目標。

## <a name="requirements"></a>需求

若要使用以 Xamarin 為基礎的應用程式中新的 Android Nougat 功能，需要下列各項：

- **Visual Studio 或 Visual Studio for Mac**&ndash;如果您使用 Visual Studio，則需要適用于 Xamarin 的 Visual Studio Tools 版4.2.0.628 或更新版本。 如果您使用 Visual Studio for Mac，則需要版本6.1.0 或更新版本的 Visual Studio for Mac。

- 您必須安裝並設定 Visual Studio 或 Visual Studio for Mac 的**xamarin android** &ndash; xamarin. android 7.0 或更新版本。

- **Android SDK** Android SDK 7.0 （API 24）或更新版本必須透過 [Android SDK 管理員] 安裝。

- **JAVA 開發人員套件**如果您要針對 API 層級24或更高版本進行開發，則 Xamarin Android 7.0 開發需要[JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本（jdk 8 也支援早于24的 API 層級）。 &ndash; 如果您使用自訂控制項或表單預覽器，則需要 JDK 8 的64位版本。

> [!IMPORTANT]
> Xamarin.Android 不支援 JDK 9。

請注意，您必須使用 Xamarin C6SR4 或更新版本來重建應用程式，才能與 Android Nougat 可靠地搭配使用。 因為 Android Nougat 只能連結至[NDK 提供的原生程式庫](https://developer.android.com/about/versions/nougat/android-7.0-changes.html)，所以使用程式庫（例如**Mono** ）的現有應用程式可能會在 Android Nougat 上執行時損毀（如果未正確重建）。

## <a name="getting-started"></a>快速入門

若要開始使用 Android Nougat 搭配 Xamarin，您必須先下載並安裝最新的工具和 SDK 套件，才能建立 Android Nougat 專案：

1. 從 Xamarin 安裝最新的 Xamarin. Android 更新。

2. 安裝**Android 7.0 （API 24）** 套件和工具或更新版本。

3. 建立以 Android Nougat 為目標的新 Xamarin Android 專案。

4. 設定適用于 Android Nougat 的模擬器或裝置。

下列各節將說明每個步驟：

### <a name="install-xamarin-updates"></a>安裝 Xamarin 更新

若要新增 Android Nougat 的 Xamarin 支援，請將 Visual Studio 或 Visual Studio for Mac 中的更新通道變更為穩定的通道，並套用最新的更新。 如果您也需要目前僅適用于 Alpha 或搶鮮版（Beta）通道的功能，您可以切換到 Alpha 或 Beta 通道（Alpha 和 Beta 通道也會提供 Android 7.x 的支援）。 如需有關如何變更更新（發行）通道的詳細資訊，請參閱[變更更新通道](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel)。

### <a name="install-the-android-sdk"></a>安裝 Android SDK

若要使用 Xamarin Android 7.0 建立專案，您必須先使用 Android SDK 管理員來安裝**SDK Platform Android N （API 24）** 或更新版本。 您也必須安裝最新的**Android SDK Tools**：

1. 啟動 Android SDK 管理員（在 Visual Studio for Mac 中，使用 [**工具] > 開啟&hellip;Android SDK 管理員**; 在 Visual Studio 中，使用 [**工具] > [Android > Android SDK 管理員**]）。

2. 安裝**Android 7.0 （API 24）** 或更新版本：

    [![在 Android SDK 管理員中選取 Android 7.0 套件](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3. 安裝最新的 Android SDK 工具：

    [![在 Android SDK 管理員中選取最新的 Android SDK 工具](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    您必須安裝 Android SDK Tools 修訂25.2.2 或更新版本、Android SDK 平臺工具24.0.3 或更新版本，以及 Android SDK Build tools 24.0.2 或更新版本。

4. 確認已針對 JDK 1.8 設定**JAVA 開發工具組位置**：

    [![在 [工具] [選項] 底下設定 JDK 8 路徑](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    若要在 Visual Studio 中查看此設定，請按一下 [**工具] > [選項] > [Xamarin > Android 設定**]。 在 Visual Studio for Mac 中，按一下 [喜好設定] [ **> 專案] > [SDK 位置 > Android**]。

### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin Android 專案

建立新的 Xamarin Android 專案。 如果您不熟悉使用 Xamarin 進行 Android 開發的新手，請參閱[Hello，Android](~/android/get-started/hello-android/index.md)以瞭解如何建立 Xamarin. android 專案。

當您建立 Android 專案時，您必須將版本設定設定為以 Android 7.0 或更新版本為目標。 例如，若要以 Android 7.0 的專案為目標，您必須將專案的目標 Android API 層級設定為**android 7.0 （API 24-Nougat）** 。 建議您將目標 framework 層級設定為 API 24 或更新版本。 如需設定 Android API 層級層級的詳細資訊，請參閱[瞭解 ANDROID Api 層](~/android/app-fundamentals/android-api-levels.md)級。

> [!NOTE]
> 目前，您必須將**最低 android 版本**設定為**ANDROID 7.0 （API 24-Nougat）** ，以將您的應用程式部署至 android Nougat 裝置或模擬器。

### <a name="configure-an-emulator-or-device"></a>設定模擬器或裝置

如果您使用模擬器，請啟動 Android AVD 管理員，並使用下列設定建立新的裝置：

- 裝置:[結點 5]、[結點 6]、[6P]、[結點 Player]、[結點 9] 或 [圖元 C]
- 目標：Android 7.0-API 層級24
- ABI： x86 或 x86\_64

例如，此虛擬裝置已設定為模擬結點6：

[![使用結點6裝置、Android 7.0 目標和 Intel Atom x86 CPU/ABI 設定 AVD](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

如果您使用的是實體裝置，例如結點5、6或9，您可以透過無線（OTA）更新來更新您的裝置，或下載系統映射並直接將裝置閃爍。 如需有關手動將裝置更新為 Android Nougat 的詳細資訊，請參閱[適用于結點裝置的 OTA 影像](https://developers.google.com/android/nexus/ota)。

請注意，Android Nougat 不支援 [結點 5] 裝置。

## <a name="new-features"></a>新功能

Android Nougat 引進了各種新特性和功能，例如多視窗支援、通知增強功能，以及資料保護。 下列各節將重點放在您的應用程式中，並提供可協助您開始使用這些功能的連結。

### <a name="multi-window-mode"></a>多視窗模式

多視窗模式讓使用者能夠一次開啟兩個應用程式，並提供完整的多工支援。 這些應用程式可以在分割畫面模式中並排執行（橫向）或一對一（直向）。
使用者可以在應用程式之間拖曳分隔線以調整其大小，而且可以在應用程式之間剪下和貼上內容。 當兩個應用程式以多視窗模式呈現時，選取的活動會繼續執行，而未選取的活動會暫停但仍會顯示。 多視窗模式不會修改 Android 活動生命週期。

[![以多視窗模式在直向和橫向執行的範例應用程式](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

您可以設定您的 Xamarin Android 應用程式的活動支援多視窗模式的方式。 例如，您可以設定屬性，以在多視窗模式中設定應用程式的最小大小和預設高度和寬度。 您可以使用新`Activity.IsInMultiWindowMode`的屬性來判斷您的活動是否處於多視窗模式。 例如：

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

[MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground)範例應用程式包含C#的程式碼會示範如何搭配使用多個視窗使用者介面與您的應用程式。

如需多視窗模式的詳細資訊，請參閱[多視窗支援](https://developer.android.com/guide/topics/ui/multi-window.html)。

### <a name="enhanced-notifications"></a>增強的通知

Android Nougat 引進了重新設計的通知系統。 它的特色是新的直接回復功能，可讓使用者在通知 UI 中直接針對傳入文字訊息的通知進行快速回復。 從 Android 7.0 開始，當收到一個以上的訊息時，通知訊息可以組合成單一群組。 此外，開發人員也可以自訂通知流覽、在通知中運用系統裝飾，並在產生通知時利用新的通知範本。

#### <a name="direct-reply"></a>直接回復

當使用者收到傳入訊息的通知時，Android Nougat 可以回復通知內的訊息（而不是開啟訊息應用程式來傳送回復）。
此內嵌回復功能讓使用者能夠快速地直接在通知介面內回應 SMS 或 text 訊息：

[![具有內嵌直接回復欄位之通知的螢幕擷取畫面](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

若要在您的應用程式中支援這項功能，您必須透過[RemoteInput](xref:Android.App.RemoteInput)物件將*內嵌回復動作*新增至您的應用程式，讓使用者可以直接從通知 UI 回復文字。
例如，下列`RemoteInput`程式碼會建立以接收文字輸入、建立回復動作的暫止意圖，以及建立遠端輸入啟用動作：

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

[訊息服務](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice)範例應用程式會C#包含程式碼，示範如何使用`RemoteInput`物件延伸通知。 如需將內嵌回復動作新增至 Android 7.0 或更新版本之應用程式的詳細資訊，請參閱 Android[回復通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct)主題。

#### <a name="bundled-notifications"></a>配套通知

Android Nougat 可以將通知訊息群組在一起（例如，透過訊息主題），並顯示群組，而不是每個個別的訊息。
此*配套通知*功能可讓使用者在一個動作中關閉或封存一組通知。 使用者可以向下滑動以展開通知的組合，以詳細查看每個通知：

[![配套通知的螢幕擷取畫面範例](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

若要支援配套的通知，您的應用程式可以使用[SetGroup](xref:Android.App.Notification.Builder.SetGroup*)方法來組合類似的通知。 如需 Android N 配套通知群組的詳細資訊，請參閱 Android 包裝[通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle)主題。

#### <a name="custom-views"></a>自訂檢視

Android Nougat 可讓您使用系統通知標頭、動作和可擴充的版面配置來建立自訂通知流覽。 如需 Android Nougat 中自訂通知視圖的詳細資訊，請參閱 Android[通知增強功能](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements)主題。

### <a name="data-saver"></a>資料保護

從 Android Nougat 開始，使用者可以啟用新的*資料保護*設定，以封鎖背景資料的使用。 這項設定也會指示您的應用程式盡可能在前景使用較少的資料。 [ConnectivityManager](xref:Android.Net.ConnectivityManager)已在 Android Nougat 中擴充，讓您的應用程式可以檢查使用者是否已啟用資料保護，讓您的應用程式可以在啟用資料保護時，設法限制其資料使用量。

如需 Android Nougat 中新的資料保護功能的詳細資訊，請參閱 Android[優化網路資料使用](https://developer.android.com/training/basics/network-ops/data-saver.html)方式主題。

### <a name="app-shortcuts"></a>應用程式快捷方式

Android 7.1 引進*應用程式快捷方式*功能，讓使用者可以使用您的應用程式快速啟動一般或建議的工作。
若要啟動快捷方式的功能表，使用者長按一或多&ndash;個功能表的應用程式圖示時，會出現一個快速震動。
放開 [按下] 會導致功能表保持不變：

[![訊息應用程式之應用程式快捷方式功能表的範例畫面](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

這項功能僅適用于 API 層級25或更高版本。
如需 Android 7.1 中新應用程式快捷方式功能的詳細資訊，請參閱 Android[應用程式快捷方式](https://developer.android.com/guide/topics/ui/shortcuts.html)主題。

### <a name="sample-code"></a>程式碼範例

有數個 Xamarin. Android 範例可向您示範如何利用 Android Nougat 功能：

- [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground)示範如何使用 Android Nougat 中提供的多視窗 API。 您可以將範例應用程式切換成多視窗模式，以瞭解它如何影響應用程式的生命週期和行為。

- [訊息服務](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice)是使用`NotificationCompatManager`傳送通知的簡單服務。 它也會使用`RemoteInput`物件延伸通知，以允許 Android Nougat 裝置直接從通知回復文字，而不需要開啟應用程式。

- [作用中通知](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-activenotifications)`NotificationManager`會示範如何使用 API 來告訴您應用程式目前顯示的通知數目。

- [限定範圍的目錄存取](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-scopeddirectoryaccess)示範如何使用限定範圍的目錄存取 API，輕鬆地存取特定目錄。 這可做為必須在資訊清單中`READ_EXTERNAL_STORAGE`定義`WRITE_EXTERNAL_STORAGE`或許可權的替代方法。

- [直接開機](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-directboot)說明如何將資料儲存在裝置加密的儲存體中，這在裝置於輸入任何使用者認證（PIN/模式/密碼）前後都開機時一律可使用。

## <a name="summary"></a>總結

本文介紹了 Android Nougat，並說明如何在 Android Nougat 上安裝及設定最新的 Xamarin Android 開發工具和套件。 它也提供了 Android Nougat 中可用的主要功能總覽，其中包含範例原始程式碼的連結，可協助您開始建立 Android Nougat 應用程式。

## <a name="related-links"></a>相關連結

- [適用于開發人員的 Android 7。1](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Xamarin Android 7.0 版本資訊](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)
