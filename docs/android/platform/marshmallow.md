---
title: 棉花糖功能
description: 本文可説明您開始使用 Xamarin.Android 開發適用於 Android 6.0 棉花糖的應用程式。
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: fb1ba92be9527d490b3d34bd4c0e454b0a750837
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019980"
---
# <a name="marshmallow-features"></a>棉花糖功能

_本文可説明您開始使用 Xamarin.Android 開發適用於 Android 6.0 棉花糖的應用程式。_

本文概述了 Android 6.0 棉花糖中的新功能,解釋了如何準備 Xamarin.Android 用於 Android 棉花糖開發,並提供示例應用程式的連結,說明如何使用 Xamarin.Android 應用程式中的新 Android 棉花糖功能。 

## <a name="overview"></a>概觀

[Android 6.0 棉花糖](https://developer.android.com/about/versions/marshmallow/index.html), 是下一個主要 Android 版本后 Android 棒棒糖.
Xamarin.安卓支援安卓棉花糖,包括:

- **API 23/Android 6.0 綁定**&ndash;Android 6.0 為下面描述的新功能添加了許多新的 API;當您定位 API 級別 23 時,這些 API 可供 Xamarin.Android 應用使用。 有關 Android 6.0 API 的更多資訊,請參閱[Android 6.0 API](https://developer.android.com/preview/api-overview.html)。 

[![運行棉花糖的平板電腦和手機的英雄圖像](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

雖然棉花糖版本主要關注"拋光和品質",但它也提供了許多感興趣的新功能Xamarin.Android開發人員。 這些功能包括： 

- **運行時許可權**&ndash;此增強功能使用戶能夠在運行時逐案批准安全許可權。 

- **身份驗證改進**&ndash;從 Android 棉花糖開始,應用現在可以使用指紋感測器對使用者進行身份驗證,新的*確認認證據*功能最大限度地減少了輸入密碼的需求。 

- **應用連結**&ndash;此功能通過自動將應用與 Web 域關聯,説明消除彈出**應用選擇程式**的必要性。 

- **直接共用**&ndash;您可以定義*直接共用目標*,使共用快速直觀地為用戶服務;此功能允許uers與其他應用共享內容。 

- **語音交互**&ndash;此新 API 允許您在應用中建構對話語音功能。 

- **4K 顯示模式**&ndash;在 Android 棉花糖中,你的應用可以在支援它的硬體上請求 4K 顯示解析度。 

- **新的音訊功能**&ndash;從棉花糖開始,Android現在支援MIDI協定。 它還提供了新的類來創建數位音訊捕獲和播放物件,並且它提供了用於關聯音訊和輸入設備的新 API 挂鉤。 

- **新的視頻功能**&ndash;棉花糖提供了一個新的類,説明應用同步呈現音訊和視頻流;此類還支援動態播放速率。 

- **工作**&ndash;Android 棉花糖包括針對公司擁有的單用戶設備的增強型控制。 它支援設備擁有者靜默安裝和卸載應用、自動接受系統更新、改進的證書管理、數據使用情況跟蹤、許可權管理和工作狀態通知。 

- **材料設計支援庫**&ndash;新的*設計支援庫*提供設計元件和圖案,讓您更輕鬆地在應用中構建材料設計的外觀。 

此外,許多核心Android庫更新都發佈了與Android M,這些更新提供了新的功能,Android M和早期版本的Android。

此外,許多核心Android庫更新與Android棉花糖發佈,這些更新提供了新功能,Android棉花糖和早期版本的Android。 本文介紹如何開始使用 Android 棉花糖構建應用,並概述了 Android 6.0 中的新功能亮點。 

## <a name="requirements"></a>需求

在基於 Xamarin 的應用程式中使用新的 Android 棉花糖功能需要以下功能: 

- **Xamarin.安卓**&ndash;Xamarin.Android 5.1.7.12 或更高版本必須安裝和配置視覺工作室或 Xamarin 工作室。

- 適用於 Mac 或**視覺化工作室**&ndash;的**視覺化工作室**如果您正在使用適用於 Mac 的可視化工作室,則需要 5.9.7.22 版或更高版本。 如果您使用的是 Visual Studio,則需要 Visual Studio 的 Xamarin 工具 3.11.1537 或更高版本。 

- **Android SDK** &ndash; Android SDK 6.0 (API 23) 或更高版本必須通過 Android SDK 管理器安裝。

- **Java 開發人員工具組**&ndash;Xamarin.Android 需要[JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高版本(如果您正在開發 API 級別 24 或更高(JDK 1.8 還支援早於 24 的 API 級別,包括棉花糖)。 如果使用自定義控制件或表單預覽器,則需要 64 位元版本的 JDK 1.8。

如果您專門為 API 級別 23 或更早開發,則可以繼續使用[JDK 1.7。](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 

## <a name="getting-started"></a>開始使用

要開始使用 Xamarin.Android 的 Android 棉花糖,您必須下載並安裝最新的工具和 SDK 包,然後才能創建 Android 棉花糖專案: 

1. 從**穩定**通道安裝最新的 Xamarin 更新。 

2. 安裝 Android 6.0 棉花糖 SDK 套件和工具。

3. 創建一個新的 Xamarin.Android 專案,該專案面向 Android 6.0 棉花糖(API 級別 23)。 

4. 為安卓棉花糖配置模擬器或設備。

以下各節將介紹這些步驟:

### <a name="install-xamarin-updates"></a>安裝 Xamarin 更新

要更新 Xamarin,以便它包括對 Android 6.0 棉花糖的支援,請將更新通道更改為 **"穩定"** 並安裝所有更新。 有關從更新通道安裝更新的詳細資訊,請參閱[變更更新通道](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel)。 

### <a name="install-the-android-60-sdk"></a>安裝 Android 6.0 SDK

要為 Android 棉花糖創建 Xamarin.Android 專案,您必須首先使用 Android SDK 管理器來安裝 Android 6.0 SDK:

- 啟動 Android SDK 管理員(在 Mac 視覺化工作室中,使用**工具> SDK 管理器**;在可視化工作室中,使用**工具> Android > Android SDK 管理器**)並安裝最新的 Android SDK 工具:

    [![在 Android SDK 管理員中選擇 Android SDK 工具](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

- 此外,安裝最新的**Android 6.0** SDK 套件:

    [![在 Android SDK 管理員中選擇 Android 6.0 SDK 套件](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

您必須安裝 Android SDK 工具版本 24.3.4 或更高版本。
有關使用 Android SDK 管理員安裝 Android 6.0 SDK 的詳細資訊,請參閱[SDK 管理員](https://developer.android.com/tools/help/sdk-manager.html)。

### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin.安卓專案

創建新的 Xamarin.安卓專案。 如果您是使用 Xamarin 的 Android 開發的新使用者,請參閱[Hello、Android](~/android/get-started/hello-android/index.md)以瞭解創建 Android 專案。 

創建 Android 專案時,必須將版本設置配置為以 Android 6.0 MarshMallow 為目標。 要將專案定位為棉花糖,您必須為 API 級別**23(Xamarin.Android v6.0 支援)** 配置專案。 有關設定 Android API 等級的更多資訊,請參閱[瞭解 Android API 級別](~/android/app-fundamentals/android-api-levels.md)。

### <a name="configure-an-emulator-or-device"></a>設定模擬器或裝置

如果您使用的是模擬器,請啟動 Android AVD 管理員,並使用以下設定建立新裝置:

- 設備:Nexus 5、6 或 9。
- 目標: Android 6.0 - API 級別 23
- ABI: x86

例如,此虛擬設備配置為類比 Nexus 5:

[![使用 Nexus 5 設備、Android 6.0 目標和英特爾原子 (x86) 配置 AVD](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

如果您正在使用物理設備(如 Nexus 5、6 或 9),則可以安裝 Android 棉花糖的預覽映射。 有關將設備更新到 Android 棉花糖的詳細資訊,請參閱[硬體系統映射](https://developer.android.com/preview/download.html#images)。

## <a name="new-features"></a>新功能

Android 棉花糖中引入的許多更改都側重於改善 Android 用戶體驗、提高性能和修復錯誤。 然而,棉花糖也引入了一些廣泛的變化,Android平臺的基礎知識。 以下各節重點介紹這些增強功能,並提供連結,以説明您開始使用應用中的新 Android 棉花糖功能。 

### <a name="runtime-permissions"></a>執行時權限

自安卓棒棒糖以來,Android許可權系統得到了顯著優化和簡化。 在 Android 棉花糖中,使用者在運行時而不是在安裝時逐案授予許可權。 為了在 Android 棉花糖上支援此功能,以及以後,您設計應用以提示使用者在運行時(在需要許可權的上下文中)獲得許可權。 此更改使用戶能夠更輕鬆地立即開始使用你的應用,因為它簡化了安裝和升級應用的過程。 

有關在 Xamarin.Android 應用中實現執行時權限的更多詳細資訊(包括代碼範例),請參閱[在 Android 棉花糖中請求執行時許可權](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/)。
Xamarin 還提供了一個範例應用程式,用於說明執行時權限在 Android 棉花糖(以及更高版本)中的工作原理:[執行時權限](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions)。

此範例應用程式示範以下內容:

- 如何在運行時檢查和請求許可權。
- 如何聲明 Android M 設備的許可權。

要使用此示例應用,

1. 點擊 **「相機**」或「**聯繫人」** 按鈕可顯示權限請求對話方塊。
2. 授予查看「相機」或「聯繫人」片段的許可權。

有關 Android 棉花糖中新的執行時權限功能的詳細資訊,請參閱[使用系統許可權](https://developer.android.com/preview/features/runtime-permissions.html)。

### <a name="authentication-enhancements"></a>認證增強功能

Android 棉花糖包括兩個身份驗證增強功能,可説明消除對密碼的需求:

- **指紋身份驗證**&ndash;使用指紋掃描對使用者進行身份驗證。

- **根據**&ndash;設備解鎖的時間確認憑據對使用者進行身份驗證。

接下來描述的連結和示例應用可以説明您熟悉這些新功能。

#### <a name="fingerprint-authentication"></a>指紋驗證

在支援指紋掃描硬體的設備上,可以使用新`FingerPrintManager`類對用戶進行身份驗證。
有關 Android 棉花糖中的指紋身份驗證功能的詳細資訊,請參閱[指紋身份驗證](https://developer.android.com/preview/api-overview.html#fingerprint-authentication)。

Xamarin 提供了一個範例應用程式,說明如何使用註冊指紋對應用程式中的使用者進行身份驗證:[指紋對話](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)。

要使用此示例應用,

1. 觸摸 **「購買**」按鈕可打開指紋身份驗證對話方塊。
2. 掃描您註冊的指紋以進行身份驗證。

請注意,此示例應用需要具有指紋讀取器的設備。
此應用程式不儲存您的指紋(或密碼)。

#### <a name="voice-interactions"></a>語音交互

Android 棉花糖中引入的新語音交互功能允許應用使用者使用其語音來確認操作並從選項清單中選擇。 有關語音交互的詳細資訊,請參閱[語音交互 API 的概述](https://developers.google.com/voice-actions/interaction/)。 

有關在 Xamarin.Android 應用中實現語音交互的更多詳細資訊(包括代碼範例),請參閱[使用語音互動向 Android 應用新增對話](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/)。
範例應用程式可用於說明如何在 Xamarin.Android 應用中使用語音互動 API:[語音交互](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions)。

#### <a name="confirm-credential"></a>確認認證

使用 Android Marshmallow 的新*確認認證*功能,您可以根據裝置解鎖的時間對使用者進行身份驗證,從而免除使用者不必記住和輸入特定於應用的密碼。
此,請使用`SetUserAuthenticationValidityDurationSeconds`新的方法`KeyGenerator`。 使用`KeyGuardManager``CreateConfirmDeviceCredentialIntent`''的方法從應用內重新驗證使用者。 有關 Android 棉花糖中此新功能的詳細資訊,請參閱[確認認證](https://developer.android.com/preview/api-overview.html#confirm-credential)。

Xamarin 提供了一個範例應用程式,用於說明如何在應用中使用裝置認證(如 PIN、模式或密碼):[確認認證](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)

要使用此示例應用,

1. 在設備上設置安全鎖螢幕介面 (**安全>安全>螢幕鎖定**)。
2. 點擊 **「購買**」按鈕並確認安全鎖定螢幕憑據。

### <a name="chrome-custom-tabs"></a>鉻自訂標籤

當使用者點擊網址 時,應用程式開發人員將面臨選擇:應用可以啟動瀏覽器或使用基於的應用程式`WebView`。 啟動瀏覽器的&ndash;兩個選項都是無法自定義的重上下文切換,`WebView`而 s 不與瀏覽器共享狀態。 此外,使用`WebView`s 會增加額外的維護開銷。 

*Chrome 自定義標籤*允許您輕鬆優雅地顯示具有 Chrome 功能的網站,而無需讓用戶離開你的應用。 此功能使應用能夠更多地控制使用者的 Web 體驗;它使本機和 Web 內容之間的過渡更加無縫,而無需`WebView`求助於 。 通過自定義以下內容,你的應用還可以影響 Chrome 的外觀和感覺: 

- 工具列顏色

- 輸入與離開動畫

- Chrome 工具列和溢出選單中的自訂操作

- Chrome 預啟動和內容預取(可加快載入速度)

要利用 Xamarin.Android 應用程式中的此功能,請下載並安裝[Android 支援自訂選項卡庫](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/)。
有關此功能的詳細資訊,請參閱 Chrome[自定義選項卡](https://developer.chrome.com/multidevice/android/customtabs)。

### <a name="material-design-support-library"></a>材料設計支援庫

Android棒棒糖引入了[材料設計](https://www.google.com/design/spec/material-design/introduction.html)作為一種新的設計語言來刷新Android體驗(有關在Xamarin.Android應用程式中使用材料設計的資訊,請參閱[材料主題](~/android/user-interface/material-theme.md))。 借助 Android 棉花糖,Google 推出了*Android 設計支援庫*,使應用開發人員更容易採用材料設計的外觀。 此函式庫包括以下元件:

- **協調員佈局**&ndash;`CoordinatorLayout`新 小部件與類似,`FrameLayout`但比更強大。 可用作`CoordinatorLayout`子視圖的容器或頂級布局,並且它提供了一`layout_anchor`個 屬性,可用於相對於其他視圖錨定視圖。

- **摺疊工具列**&ndash;`CollapsingToolbarLayout`新 是摺疊的應用欄`Toolbar`,是 中的包裝器。 (請注意,*應用欄*是以前稱為*操作欄*的。

- **浮動操作按鈕**&ndash;表示應用介面上的主要操作的圓形按鈕。

- &ndash;**編輯文字的浮動標籤**使用`TextInputLayout`新的 小部件`EditText`(包裝 )在使用者輸入文本時隱藏提示時顯示浮動標籤。

- **導航檢視**&ndash;新`NavigationView`小部件可説明您以更易於用戶導航的方式使用導航抽屜。

- **零食桿**&ndash;`SnackBar`新小部件是一種輕量級反饋機制(類似於 Toast),在螢幕底部顯示一條簡短的消息,顯示在螢幕上所有其他元素之上。

- **材質選項卡**&ndash;`TabLayout`新 小部件提供用於顯示選項卡的水平佈局,以便實現應用中的頂級導航。

要利用 Xamarin.Android 應用程式中[的設計支援庫](https://developer.android.com/tools/support-library/features.html#design),請下載並安裝 Xamarin [Xamarin 支援庫設計](https://www.nuget.org/packages/Xamarin.Android.Support.Design/)NuGet 包。

有關在 Xamarin.Android 應用程式中使用材料設計支援庫的更多詳細資訊(包括代碼示例),請參閱[Android 支援設計庫](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/)。有關詳細資訊。"
Xamarin 提供了一個範例應用程式,演示了 Xamarin.Android &ndash; [乳酪廣場](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-cheesesquare)上新的 Android 設計庫。
此範例展示設計函式庫的以下功能:

- 折疊工具列
- 浮動操作按鈕
- 檢視錨定
- NavigationView
- 小吃店

有關設計庫的詳細資訊,請參閱 Android 開發人員部落格中的[Android 設計支援庫](https://android-developers.googleblog.com/2015/05/android-design-support-library.html)。

### <a name="additional-library-updates"></a>其他函式庫更新

除了Android棉花糖,谷歌還宣佈了幾個核心Android庫的相關更新。 Xamarin 透過幾個預覽版本 NuGet 套件為這些更新提供 Xamarin.Android 支援: 

- [谷歌Play服務](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services)&ndash;最新版本的谷歌Play服務包括新的*應用邀請*功能,這使得使用者可以與朋友分享他們的應用程式。 有關此功能的詳細資訊,請參閱[透過 Google 的應用邀請擴展應用的覆寫面](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/)。 

- [Android 支援庫](https://www.nuget.org/packages?q=xamarin+support+library)&ndash;這些 NuGets 提供的功能僅適用於庫 API,同時提供 Android 框架 API 的向後相容版本。 

- [Android可穿戴庫](https://www.nuget.org/packages/Xamarin.Android.Wear)&ndash;這個NuGet包括谷歌播放服務綁定。 最新版本的可穿戴庫為 Android Wear 平台帶來了新功能(包括自定義應用程式的輕鬆導航)。 

## <a name="summary"></a>總結

本文介紹了Android棉花糖,並解釋了如何安裝和配置最新的工具和包Xamarin.Android開發棉花糖。 它還概述了 Xamarin.Android 開發最令人興奮的新 Android 棉花糖功能。

## <a name="related-links"></a>相關連結

- [安卓 6.0 棉花糖](https://developer.android.com/about/versions/marshmallow/index.html)
- [取得 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [功能概述](https://developer.android.com/preview/api-overview.html)
- [版本資訊](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1.99/index.md)
- [執行時權限 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions)
- [確認認證例( 例如範例 )](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)
- [指紋對話(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
