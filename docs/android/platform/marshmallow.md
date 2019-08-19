---
title: Marshmallow 功能
description: 這篇文章可協助您開始在使用 Xamarin 來開發 Android 6.0 Marshmallow 的應用程式中使用。
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 00df2edc1acc45c0302ecc793f4670eafa1dd91c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523740"
---
# <a name="marshmallow-features"></a>Marshmallow 功能

_這篇文章可協助您開始在使用 Xamarin 來開發 Android 6.0 Marshmallow 的應用程式中使用。_

本文概述 Android 6.0 Marshmallow 的新功能, 說明如何準備 Xamarin 以進行 Android Marshmallow 開發, 並提供範例應用程式的連結, 說明如何利用新的 Android MarshmallowXamarin Android 應用程式中的功能。 


## <a name="overview"></a>總覽

[Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)是 android 棒後的下一個主要 android 版本。
Xamarin 支援 Android Marshmallow, 其中包括:

- **API 23/Android 6.0**系結&ndash; Android 6.0 為下列新功能新增了許多新的 api; 當您以 API 層級23為目標時, 這些 api 可供 Xamarin Android 應用程式使用。 如需 Android 6.0 Api 的詳細資訊, 請參閱[android 6.0 api](https://developer.android.com/preview/api-overview.html)。 

[![執行 Marshmallow 之平板電腦和手機的主圖影像](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

雖然 Marshmallow 版本主要著重于「波蘭文和品質」, 但它也為 Xamarin Android 開發人員提供許多相關的新功能。 這些功能包括： 

- **執行時間許可權**&ndash;這項增強功能可讓使用者在執行時間以案例為基礎核准安全性許可權。 

- **驗證改善**從 Android Marshmallow 開始, 應用程式現在可以使用指紋感應器來驗證使用者, 而新的*確認認證*功能會將輸入密碼的需求降到最低。 &ndash; 

- **應用程式連結**這項功能可透過自動將應用程式與 web 網域產生關聯, 協助消除**應用程式選擇器**快顯的必要性。 &ndash; 

- **直接共用**您可以定義*直接共用目標*, 讓使用者快速且直覺地共用, 這項功能可讓使用者與其他應用程式共用內容。 &ndash; 

- **語音互動**&ndash;這個新的 API 可讓您在應用程式中建立對話語音功能。 

- **4K 顯示模式**&ndash;在 Android Marshmallow 中, 您的應用程式可以在支援的硬體上要求4k 顯示解析度。 

- **新的音訊功能**&ndash;從 Marshmallow 開始, Android 現在支援 MIDI 通訊協定。 它也提供新的類別來建立數位音訊捕捉和播放物件, 並提供新的 API 勾點來關聯音訊和輸入裝置。 

- **新的影片功能**&ndash; Marshmallow 提供新的類別, 可協助應用程式轉譯音訊和影片串流的同步; 此類別也提供動態播放速度的支援。 

- **Android For Work**&ndash; Marshmallow 包含適用于公司擁有的單一使用者裝置的增強型控制項。 它支援裝置擁有者、自動接受系統更新、改善的憑證管理、資料使用方式追蹤、版權管理和工作狀態通知, 以無訊息方式安裝和卸載應用程式。 

- **材質設計支援程式庫**新的*設計支援程式庫*提供設計元件和模式, 可讓您更輕鬆地在應用程式中建立材質設計外觀和風格。 &ndash; 

此外, 許多核心 Android 程式庫更新已與 Android M 一起發行, 而這些更新同時為 Android M 和舊版 Android 提供了新功能。

此外, 許多核心 Android 程式庫更新已與 Android Marshmallow 一起發行, 而這些更新同時提供 Android Marshmallow 和舊版 Android 的新功能。 本文說明如何使用 Android Marshmallow 開始建立應用程式, 並概述 Android 6.0 中的新功能重點。 

## <a name="requirements"></a>需求

若要使用以 Xamarin 為基礎的應用程式中新的 Android Marshmallow 功能, 需要下列各項: 

- 您必須安裝並設定 Visual Studio 或 Xamarin Studio 的**xamarin android** &ndash; 5.1.7.12 或更新版本。

- **Visual Studio for Mac**或**Visual Studio** &ndash;如果您使用 Visual Studio for Mac, 則需要5.9.7.22 或更新版本。 如果您使用 Visual Studio, 則需要適用于 Visual Studio 的 Xamarin 工具版本3.11.1537 或更新版本。 

- **Android SDK**&ndash; Android SDK 6.0 (API 23) 或更新版本必須透過 Android SDK 管理員安裝。

- **JAVA 開發人員套件**如果您要針對 API 層級24或更高版本進行開發, 則 Xamarin 會需要[jdk 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本 (jdk 1.8 也支援早于24的 api 層級, 包括 Marshmallow)。 &ndash; 如果您使用自訂控制項或表單預覽器, 則需要 JDK 1.8 的64位版本。

如果您是特別針對 API 層級23或更早的版本進行開發, 您可以繼續使用[JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 。 


## <a name="getting-started"></a>快速入門

若要開始使用 Android Marshmallow 搭配 Xamarin, 您必須先下載並安裝最新的工具和 SDK 套件, 才能建立 Android Marshmallow 專案: 

1. 從**穩定**的通道安裝最新的 Xamarin 更新。 

2. 安裝 Android 6.0 Marshmallow SDK 套件和工具。

3. 建立以 Android 6.0 Marshmallow 為目標的新 Xamarin Android 專案 (API 層級 23)。 

4. 設定適用于 Android Marshmallow 的模擬器或裝置。

下列各節將說明每個步驟:


### <a name="install-xamarin-updates"></a>安裝 Xamarin 更新

若要更新 Xamarin, 使其包含 Android 6.0 Marshmallow 的支援, 請將更新通道變更為**穩定**, 並安裝所有更新。 如需從更新通道安裝更新的詳細資訊, 請參閱[變更更新通道](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel)。 


### <a name="install-the-android-60-sdk"></a>安裝 Android 6.0 SDK

若要建立適用于 Android Marshmallow 的 Xamarin Android 專案, 您必須先使用 Android SDK 管理員來安裝 Android 6.0 SDK:

- 啟動 Android SDK 管理員 (在 Visual Studio for Mac 中, 使用 [**工具] > [SDK 管理員**]; 在 Visual Studio 中, 使用 [**工具] > [Android > Android SDK 管理員**]) 並安裝最新的 Android SDK Tools:

    [![在 Android SDK 管理員中選取 Android SDK 工具](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

- 此外, 請安裝最新的**Android 6.0** SDK 套件:

    [![在 Android SDK 管理員中選取 Android 6.0 SDK 套件](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

您必須安裝 Android SDK Tools 修訂24.3.4 或更新版本。
如需有關使用 Android SDK 管理員安裝 Android 6.0 SDK 的詳細資訊, 請參閱[SDK 管理員](https://developer.android.com/tools/help/sdk-manager.html)。



### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin Android 專案

建立新的 Xamarin Android 專案。 如果您不熟悉使用 Xamarin 進行 Android 開發的新手, 請參閱[Hello, Android](~/android/get-started/hello-android/index.md)以瞭解如何建立 android 專案。 

當您建立 Android 專案時, 您必須將版本設定設定為以 Android 6.0 MarshMallow 為目標。 若要將專案的目標設為 Marshmallow, 您必須為**API 層級23設定專案 (Xamarin. Android 6.0 版支援)** 。 如需設定 Android API 層級層級的詳細資訊, 請參閱[瞭解 ANDROID Api 層](~/android/app-fundamentals/android-api-levels.md)級。



### <a name="configure-an-emulator-or-device"></a>設定模擬器或裝置

如果您使用模擬器, 請啟動 Android AVD 管理員, 並使用下列設定建立新的裝置:

- 裝置:結點5、6或9。
- 目標：Android 6.0-API 層級23
- ABI: x86

例如, 此虛擬裝置已設定為模擬第5個結點:

[![使用結點5裝置、Android 6.0 目標和 Intel Atom (x86) 來設定 AVD](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

如果您使用的是實體裝置 (例如, 第5、6或9個), 您可以安裝 Android Marshmallow 的預覽影像。 如需將您的裝置更新至 Android Marshmallow 的詳細資訊, 請參閱[硬體系統映射](https://developer.android.com/preview/download.html#images)。



## <a name="new-features"></a>新功能

Android Marshmallow 中引進的許多變更著重于改善 Android 使用者體驗、提升效能, 以及修正錯誤。 不過, Marshmallow 也針對 Android 平臺的基本概念引進了一些廣泛的變更。 下列各節將強調這些增強功能, 並提供連結, 協助您開始使用應用程式中的新 Android Marshmallow 功能。 



### <a name="runtime-permissions"></a>執行時間許可權

Android 許可權系統已大幅優化並簡化, 因為 Android 棒了。 在 Android Marshmallow 中, 使用者會以案例為基礎, 在執行時間授與許可權, 而不是在安裝期間。 若要在 Android Marshmallow 和更新版本上支援這項功能, 您可以設計應用程式在執行時間提示使用者提供許可權 (在需要許可權的內容中)。 這項變更可讓使用者更輕鬆地立即開始使用您的應用程式, 因為它會簡化應用程式的安裝和升級流程。 

如需在 Xamarin Android 應用程式中執行執行時間許可權的詳細資訊 (包括程式碼範例), 請參閱[在 android 中要求執行時間許可權 Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) 。
Xamarin 也提供範例應用程式, 說明執行時間許可權在 Android Marshmallow (及更新版本) 中的工作方式:[RuntimePermissions](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions)。

這個範例應用程式會示範下列各項:

- 如何在執行時間檢查和要求許可權。
- 如何宣告 Android M 裝置的許可權。

若要使用此範例應用程式:

1. 請按 [**相機**] 或 [**連絡人**] 按鈕, 以顯示 [許可權要求] 對話方塊。
2. 授與查看相機或連絡人片段的許可權。

如需 Android Marshmallow 中新執行時間許可權功能的詳細資訊, 請參閱[使用系統許可權](https://developer.android.com/preview/features/runtime-permissions.html)。



### <a name="authentication-enhancements"></a>驗證增強功能

Android Marshmallow 包含兩種驗證增強功能, 可協助消除密碼的需求:

- **指紋驗證**&ndash;使用指紋掃描來驗證使用者。

- **確認認證**&ndash;根據裝置解除鎖定的時間長度來驗證使用者。

接下來所述的連結和範例應用程式可協助您熟悉這些新功能。


#### <a name="fingerprint-authentication"></a>指紋驗證

在支援指紋掃描硬體的裝置上, 您可以使用新`FingerPrintManager`的類別來驗證使用者。
如需 Android Marshmallow 中指紋驗證功能的詳細資訊, 請參閱[指紋驗證](https://developer.android.com/preview/api-overview.html#fingerprint-authentication)。

Xamarin 提供一個範例應用程式, 說明如何使用已註冊的指紋來驗證您應用程式中的使用者:[FingerprintDialog](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)。

若要使用此範例應用程式:

1. 觸碰 [**購買**] 按鈕以開啟 [指紋驗證] 對話方塊。
2. 在您已註冊的指紋中進行掃描以進行驗證。

請注意, 此範例應用程式需要具有指紋辨識器的裝置。
此應用程式不會儲存您的指紋 (或您的密碼)。



#### <a name="voice-interactions"></a>語音互動

Android Marshmallow 中引進的新語音互動功能, 可讓您應用程式的使用者使用其聲音來確認動作, 並從選項清單中選取。 如需語音互動的詳細資訊, 請參閱[語音互動 API 的總覽](https://developers.google.com/voice-actions/interaction/)。 

如需在 Xamarin Android 應用程式中執行語音互動的詳細資訊 (包括程式碼範例), 請參閱[使用語音互動將交談新增至 Android 應用程式](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/)。
提供範例應用程式, 說明如何在 Xamarin Android 應用程式中使用語音互動 API:[語音互動](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions)。



#### <a name="confirm-credential"></a>確認認證

使用 Android Marshmallow 的新 [*確認認證*] 功能, 您可以讓使用者不必記住並輸入應用程式特定的密碼, 其方式是根據裝置已解除鎖定的時間長度進行驗證。
若要這麼做, 請使用的`SetUserAuthenticationValidityDurationSeconds`新方法。 `KeyGenerator` `KeyGuardManager`使用的`CreateConfirmDeviceCredentialIntent`方法, 從您的應用程式內重新驗證使用者。 如需 Android Marshmallow 中這項新功能的詳細資訊, 請參閱[確認認證](https://developer.android.com/preview/api-overview.html#confirm-credential)。

Xamarin 提供範例應用程式, 說明如何在您的應用程式中使用裝置認證 (例如 PIN、模式或密碼):[ConfirmCredential](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)

若要使用此範例應用程式:

1. 在您的裝置上設定安全鎖定畫面 (**安全 > 安全性 > Screenlock**)。
2. 請按 [**購買**] 按鈕, 並確認安全鎖定畫面認證。



### <a name="chrome-custom-tabs"></a>Chrome 自訂索引標籤

當使用者按 URL 時, 應用程式開發人員會面臨選擇: 應用程式可以啟動瀏覽器, 或使用以為`WebView`基礎的應用程式內瀏覽器。 在&ndash;啟動瀏覽器時, 這兩個選項都是一種很繁重的`WebView`內容切換, 無法自訂, 而 s 則不會與瀏覽器共用狀態。 此外, 使用`WebView`可能會增加額外的維護負擔。 

*Chrome 自訂*索引標籤可讓您輕鬆且適當地顯示具有 Chrome 功能的網站, 而不需讓使用者離開您的應用程式。 這項功能可讓您的應用程式更充分掌控使用者的 web 體驗;它可讓原生和 web 內容之間的轉換更順暢, 而不`WebView`需要使用。 您的應用程式也可以自訂下列內容, 以影響 Chrome 的外觀與感覺: 

- 工具列色彩

- 輸入和結束動畫

- Chrome 工具列和溢位功能表中的自訂動作

- Chrome 預先啟動和內容預先提取 (以加快載入速度)

若要在您的 Xamarin Android 應用程式中利用這項功能, 請下載並安裝[Android 支援自訂](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/)索引標籤程式庫。
如需這項功能的詳細資訊, 請參閱[Chrome 自訂](https://developer.chrome.com/multidevice/android/customtabs)索引標籤。



### <a name="material-design-support-library"></a>材質設計支援程式庫

Android 棒糖引進了一種新的設計語言來重新整理 android 體驗 (如需在 Xamarin android 應用程式中使用[材質設計](http://www.google.com/design/spec/material-design/introduction.html)的相關資訊, 請參閱[材質主題](~/android/user-interface/material-theme.md))。 透過 Android Marshmallow, Google 引進了*Android 設計支援程式庫*, 讓應用程式開發人員更容易採用材質設計的外觀與風格。 此程式庫包含下列元件:

- **CoordinatorLayout**新的小工具類似于, `FrameLayout`但功能更強大。 `CoordinatorLayout` &ndash; 您可以使用`CoordinatorLayout`做為子視圖的容器, 或做為最上層的配置, 並`layout_anchor`提供可用來錨定相對於其他視圖之視圖的屬性。

- 折迭**工具列**新的是折迭的應用程式行`Toolbar`, 是的包裝函式。 `CollapsingToolbarLayout` &ndash; (請注意,*應用程式行*就是先前稱為*動作*列的內容)。

- **浮動動作按鈕**&ndash;表示應用程式介面上主要動作的圓角按鈕。

- **用於編輯文字的浮動標籤**當使用者輸入`TextInputLayout`文字時, 使用`EditText`新的小工具 (包裝) 來顯示浮動標籤。 &ndash;

- **流覽視圖**&ndash; 新`NavigationView`的小工具可協助您使用流覽選單, 讓使用者更容易流覽。

- **Snackbar**&ndash; 新`SnackBar`的 widget 是輕量的意見反應機制 (類似于快顯), 會在畫面底部顯示簡短的訊息, 並出現在螢幕上所有其他元素的上方。

- **材質**索引標籤&ndash; 新`TabLayout`的 widget 會提供水準配置, 以顯示索引標籤, 以在您的應用程式中執行最上層導覽。

若要在您的 Xamarin Android 應用程式中利用[設計支援程式庫](https://developer.android.com/tools/support-library/features.html#design), 請下載並安裝 Xamarin [Xamarin 支援程式庫設計](https://www.nuget.org/packages/Xamarin.Android.Support.Design/)NuGet 封裝。

如需在 Xamarin Android 應用程式中使用材質設計支援程式庫的詳細資訊 (包括程式碼範例), 請參閱[Android 支援設計程式庫的美觀材質設計](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/)。
Xamarin 提供範例應用程式, 示範如何在&ndash; [Cheesesquare](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-cheesesquare)上使用新的 android 設計程式庫。
這個範例會示範設計程式庫的下列功能:


- 折迭工具列
- 浮動動作按鈕
- 視圖錨定
- NavigationView
- Snackbar

如需設計程式庫的詳細資訊, 請參閱 Android 開發人員的 blog 中的[Android 設計支援程式庫](http://android-developers.blogspot.co.at/2015/05/android-design-support-library.html)。


### <a name="additional-library-updates"></a>其他程式庫更新

除了 Android Marshmallow, Google 也已宣佈數個核心 Android 程式庫的相關更新。 Xamarin 透過數個預覽版本的 NuGet 套件, 為這些更新提供了 Xamarin 支援: 

- [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services)最新版本的 Google Play Services 包含新的*應用程式邀請*功能, 讓使用者可以與朋友共用其應用程式。 &ndash; 如需這項功能的詳細資訊, 請參閱透過[Google 的應用程式邀請擴充應用程式的接觸](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/)。 

- [Android 支援程式庫](https://www.nuget.org/packages?q=xamarin+support+library)&ndash;這些 nuget 提供的功能僅適用于程式庫 api, 同時提供 Android framework api 的回溯相容版本。 

- [Android 穿戴式程式庫](https://www.nuget.org/packages/Xamarin.Android.Wear)&ndash;此 NuGet 包含 Google Play Services 系結。 最新版的穿戴式程式庫會將新功能 (包括自訂應用程式的更容易導覽) 帶入 Android 磨損平臺。 


## <a name="summary"></a>總結

本文介紹了 Android Marshmallow, 並說明如何在 Marshmallow 上安裝及設定適用于 Xamarin 的最新工具和套件開發。 此外, 也提供了最令人興奮的新 Android Marshmallow 功能, 以進行 Xamarin 的開發。


## <a name="related-links"></a>相關連結

- [Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)
- [取得 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [功能總覽](https://developer.android.com/preview/api-overview.html)
- [版本資訊](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1.99/index.md)
- [RuntimePermissions (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions)
- [ConfirmCredential (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)
- [FingerprintDialog (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
