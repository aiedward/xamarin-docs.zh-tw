---
title: Marshmallow 功能
description: 這篇文章可協助您開始使用 Xamarin.Android 開發 Android 6.0 Marshmallow 應用程式使用。
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 4f0bcd25662d3def719a89ccf833e845eb1728f2
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242234"
---
# <a name="marshmallow-features"></a>Marshmallow 功能

_這篇文章可協助您開始使用 Xamarin.Android 開發 Android 6.0 Marshmallow 應用程式使用。_

這篇文章概述在 Android 6.0 Marshmallow 的新功能、 說明如何準備 Xamarin.Android 開發 Android Marshmallow，和提供的連結，說明如何使用新的 Android Marshmallow 的範例應用程式在 Xamarin.Android 應用程式的功能。 


## <a name="overview"></a>總覽

[Android 6.0 Marshmallow](http://developer.android.com/about/versions/marshmallow/index.html)，是下一步 的主要 Android Android Lollipop 之後的版本。
Xamarin.Android 支援 Android Marshmallow，並且包含：

-   **API 23/Android 6.0 繫結** &ndash; Android 6.0 加入許多新的 Api，如下所述的新功能，這些 API 可供 Xamarin.Android 應用程式，當您的目標 API 層級 23。 如需有關 Android 6.0 Api 的詳細資訊，請參閱[Android 6.0 Api](http://developer.android.com/preview/api-overview.html)。 

[![Hero 平板電腦和手機執行 Marshmallow 映像](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

雖然 Marshmallow 發行主要著重於 「 波蘭文和品質 」，它也會提供許多的新功能感興趣的 Xamarin.Android 開發人員。 這些功能包括： 

-   **執行階段權限**&ndash;這項增強功能可讓使用者在執行階段核准的情況為基礎的安全性權限。 

-   **驗證改進**&ndash;從 Android Marshmallow 開始，應用程式現在可以使用指紋感應器來驗證使用者和新*確認認證*功能將需要輸入最小化密碼。 

-   **應用程式連結**&ndash;這項功能，有助於省去無須**應用程式選擇器**顯自動將應用程式與 web 網域產生關聯。 

-   **直接共用**&ndash;您可以定義*直接共用目標*，讓共用快速且直覺式的使用者，這項功能可讓提供與其他應用程式的共用內容。 

-   **語音互動**&ndash;這個新的 API 可讓您在您的應用程式中建置交談式語音功能。 

-   **4 K 顯示模式**&ndash;在 Android Marshmallow，您的應用程式可以要求 4k 上支援硬體的顯示器解析度。 

-   **音訊的新功能** &ndash; Marshmallow 從開始，Android 現在支援 MIDI 通訊協定。 它也提供新的類別來建立數位音訊擷取和播放的物件，並提供新的 API 攔截程序來將音訊及輸入裝置產生關聯。 

-   **視訊的新功能** &ndash; Marshmallow 提供新的類別，可協助應用程式呈現音訊和視訊資料流同步; 這個類別也提供支援動態的播放速率。 

-   **Android for Work** &ndash; Marshmallow 包含增強型的控制項，為公司擁有的單一使用者的裝置。 它支援無訊息安裝，並解除安裝的裝置擁有者的應用程式，自動接受度的系統更新、 改善的憑證管理、 追蹤資料使用方式、 權限管理和工作狀態通知。 

-   **材料設計支援程式庫**&ndash;新*設計支援程式庫*提供設計元件和模式，可讓您更輕鬆地在您的應用程式中建置 Material Design 外觀與風格。 

此外，許多核心 Android 程式庫更新發行 Android m，而且這些更新的 Android M 及舊版的 Android 提供的新功能。

此外，許多核心 Android 程式庫更新出版與 Android Marshmallow，而且這些更新能提供 Android Marshmallow 和舊版的 Android 的新功能。 這篇文章說明如何開始使用 Android Marshmallow，建置應用程式，並提供新功能的概觀會反白顯示在 Android 6.0。 

## <a name="requirements"></a>需求

需要下列項目以 Xamarin 為基礎的應用程式中使用新的 Android Marshmallow 功能： 

-   **Xamarin.Android** &ndash; Xamarin.Android 5.1.7.12 或更新版本必須安裝並設定為使用 Visual Studio 或 Xamarin Studio。

-   **Visual Studio for Mac**或是**Visual Studio** &ndash;如果您使用 Visual Studio for Mac，版 5.9.7.22 或更新版本。 如果您使用 Visual Studio 中，版本 3.11.1537 或更新版本的 Visual Studio 的 Xamarin 工具需要。 

-   **Android SDK** &ndash; Android SDK 6.0 (API 23) 或更新版本必須安裝 Android SDK 管理員透過。

-   **Java Developer Kit** &ndash; Xamarin.Android 需要[JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本，如果您正在開發的 API 層級 24 或更大 （JDK 1.8 也支援 API 層級早於 24，包括 Marshmallow）。 如果您使用自訂控制項或表單預覽程式需要 64 位元版本的 JDK 1.8。

您可以繼續使用[JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)如果您是開發特別針對 API 層級 23 或更早版本。 


## <a name="getting-started"></a>快速入門

若要開始使用 Xamarin.Android 的 Android Marshmallow，您必須下載並安裝最新工具和 SDK 封裝，才能建立 Android Marshmallow 專案： 

1.  安裝最新的 Xamarin 更新，從**穩定**通道。 

2.  安裝 Android 6.0 Marshmallow SDK 套件和工具。

3.  建立新的 Xamarin.Android 專案目標 Android 6.0 Marshmallow (API 層級 23)。 

4.  設定 Android Marshmallow 的模擬器或裝置。

下列各節說明每個步驟：


### <a name="install-xamarin-updates"></a>安裝 Xamarin 更新

若要將 Xamarin 更新，使其包含支援 Android 6.0 Marshmallow，變更更新通道**穩定**並安裝所有更新。 如需安裝更新，從更新通道的詳細資訊，請參閱[變更更新頻道](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel)。 


### <a name="install-the-android-60-sdk"></a>Android 6.0 SDK 安裝

若要建立 Android Marshmallow 的 Xamarin.Android 專案，您必須先使用 Android SDK 管理員安裝 Android 6.0 SDK:

-   啟動 Android SDK 管理員 (在 Visual Studio for Mac 中，使用**工具 > SDK 管理員**; 在 Visual Studio 中，使用**工具 > Android > Android SDK 管理員**) 並安裝最新的 Android SDK Tools:

    [![選取 Android SDK Manager 中的 Android SDK 工具](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

-   此外，安裝最新**Android 6.0** SDK 套件：

    [![選取 Android 6.0 SDK 套件中 Android SDK 管理員](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

您必須安裝 Android SDK Tools 修訂 24.3.4 或更新版本。
如需使用 Android SDK 管理員安裝 Android 6.0 SDK 的詳細資訊，請參閱[SDK 管理員](http://developer.android.com/tools/help/sdk-manager.html)。



### <a name="start-a-xamarinandroid-project"></a>啟動 Xamarin.Android 專案

建立新的 Xamarin.Android 專案。 如果您是使用 Xamarin 進行 Android 開發的新手，請參閱[Hello，Android](~/android/get-started/hello-android/index.md)來了解如何建立 Android 專案。 

當您建立 Android 專案時，您必須設定目標 Android 6.0 MarshMallow 的版本設定。 若要目標 Marshmallow 您的專案，您必須設定專案以**API 層級 23 (Xamarin.Android v6.0 支援)**。 如需有關設定 Android API 層級的層級的詳細資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。



### <a name="configure-an-emulator-or-device"></a>設定模擬器或裝置

如果您正使用模擬器，請啟動 Android AVD Manager，並建立新的裝置，使用下列設定：

-   裝置： Nexus 5、 6 或 9。
-   目標： Android 6.0 API 層級 23
-   ABI: x86

例如，此虛擬裝置設定為模擬 Nexus 5:

[![設定使用 Nexus 5 裝置、 Android 6.0 目標和 Intel Atom (x86) AVD](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

如果您使用實體裝置，例如 Nexus 5，6，或 9 時，您可以安裝 Android Marshmallow 的預覽映像。 如需有關如何將裝置升級至 Android Marshmallow 的詳細資訊，請參閱[硬體系統映像](http://developer.android.com/preview/download.html#images)。



## <a name="new-features"></a>新功能

許多 Android Marshmallow 所做的變更著重在改善 Android 使用者經驗、 提升效能，並修正 bug。 不過，Marshmallow 也引進了一些廣泛的變更 Android 平台的基本概念。 下列各節會反白顯示這些增強功能，並提供連結可幫助您開始使用您的應用程式中的新的 Android Marshmallow 功能。 



### <a name="runtime-permissions"></a>執行階段權限

已大幅最佳化並簡化自 Android Lollipop Android 權限的系統。 在 Android Marshmallow，使用者授與的權限的案例的基礎，在執行階段，而不是在安裝時間。 為了支援這項功能，在 Android Marshmallow 及更新版本，您可以設計您的應用程式會提示使用者輸入在執行階段 （在其中需要的權限的內容） 的權限。 這項變更可讓使用者更輕鬆地開始使用您的應用程式立即因為它可簡化安裝和升級您的應用程式的程序時，才。 

請參閱[要求執行階段權限在 Android Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/)如需詳細資訊 （包括程式碼範例） 實作在 Xamarin.Android 應用程式的執行階段權限。
Xamarin 也會提供說明如何在 Android Marshmallow （和更新版本），執行階段權限的運作方式的範例應用程式： [RuntimePermissions](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions)。

此範例應用程式示範下列作業：

-   如何檢查以及在執行階段的要求權限。
-   如何宣告適用於 Android M 裝置的權限。

若要使用此範例應用程式：

1.  點選**相機**或是**連絡人**按鈕來顯示權限要求 對話方塊。
2.  授與權限來檢視數位相機或連絡人的片段。

如需有關 Android Marshmallow 的新執行階段權限功能的詳細資訊，請參閱[使用系統權限](https://developer.android.com/preview/features/runtime-permissions.html)。



### <a name="authentication-enhancements"></a>驗證增強功能

Android Marshmallow 包含兩個驗證增強功能，可協助消除密碼的需求：

-   **指紋驗證**&ndash;使用指紋掃描來驗證使用者。

-   **確認認證**&ndash;驗證根據多久已解除鎖定裝置的使用者。

連結和說明接下來的範例應用程式可協助您熟悉這些新功能。


#### <a name="fingerprint-authentication"></a>指紋驗證

在裝置上支援的指紋掃描硬體，您可以使用新`FingerPrintManager`類別來驗證使用者。
如需在 Android Marshmallow 的指紋驗證功能的詳細資訊，請參閱[指紋驗證](https://developer.android.com/preview/api-overview.html#fingerprint-authentication)。

Xamarin 提供的範例應用程式，說明如何使用已註冊的指紋來驗證您的應用程式中的使用者： [FingerprintDialog](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog)。

若要使用此範例應用程式：

1.  觸控**採購**按鈕可開啟指紋驗證對話方塊。
2.  掃描在您已註冊的指紋，來進行驗證。

請注意此範例應用程式需要具有指紋辨識器的裝置。
此應用程式不會儲存您的指紋 （或您的密碼）。



#### <a name="voice-interactions"></a>語音互動

Android Marshmallow 中導入新的語音互動功能可讓您的應用程式的使用者使用他們的聲音確認動作，並從選項清單中選取。 如需詳細的語音互動的詳細資訊，請參閱[語音互動 API 的概觀](https://developers.google.com/voice-actions/interaction/)。 

請參閱[新增至 Android 應用程式的語音互動的交談](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/)如需詳細資訊 （包括程式碼範例） 在 Xamarin.Android 應用程式中實作語音互動。
範例應用程式可說明如何使用 Xamarin.Android 應用程式中的語音互動 API:[語音互動](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions)。



#### <a name="confirm-credential"></a>確認認證

使用新*確認認證*功能的 Android Marshmallow，您可以免費的使用者不需要記住並輸入應用程式特定的密碼，藉由根據多久已解除鎖定其裝置進行驗證。
若要這樣做，您使用新`SetUserAuthenticationValidityDurationSeconds`方法的`KeyGenerator`。 使用`KeyGuardManager`的`CreateConfirmDeviceCredentialIntent`重新驗證您的應用程式使用者的方法。 如需 Android Marshmallow 這項新功能的詳細資訊，請參閱[確認認證](https://developer.android.com/preview/api-overview.html#confirm-credential)。

Xamarin 提供說明如何使用裝置認證 （例如 PIN、 圖樣或密碼） 在您的應用程式中的範例應用程式： [ConfirmCredential](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential/)

若要使用此範例應用程式：

1.  設定您的裝置上安全的鎖定畫面 (**Secure > 安全性 > Screenlock**)。
2.  點選**採購**按鈕，然後確認安全鎖定螢幕的認證。



### <a name="chrome-custom-tabs"></a>Chrome 自訂索引標籤

當使用者點選 URL 時，應用程式開發人員會面臨選擇： 應用程式可以啟動瀏覽器，或使用為基礎的應用程式內瀏覽器`WebView`。 這兩個選項會呈現挑戰&ndash;啟動瀏覽器是不是可自訂，同時大量的內容切換`WebView`s 不會共用狀態與瀏覽器。 此外，使用`WebView`s 可以新增額外的維護負擔。 

*Chrome 自訂索引標籤*可讓您輕鬆且從容地顯示和強大的 Chrome 的網站，而不需要您保留您的應用程式的使用者。 這項功能可讓您的應用程式更充分掌控使用者的 web 體驗;它讓原生之間的轉換和 web 內容更順暢，而不必訴諸於`WebView`。 Chrome 的外觀與感覺藉由下列自訂，也會影響您的應用程式： 

-   工具列色彩

-   進入或離開動畫

-   Chrome 工具列和溢位功能表中的自訂動作

-   Chrome 啟動前和內容預先擷取 （適用於更快速載入）

若要在您的 Xamarin.Android 應用程式中利用這項功能，請下載並安裝[Android 支援自訂索引標籤文件庫](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/)。
如需這項功能的詳細資訊，請參閱[Chrome 自訂索引標籤](https://developer.chrome.com/multidevice/android/customtabs)。



### <a name="material-design-support-library"></a>材料設計支援程式庫

Android Lollipop 引進[Material Design](http://www.google.com/design/spec/material-design/introduction.html)做為新的設計語言，若要重新整理 Android 體驗 (請參閱[材料佈景主題](~/android/user-interface/material-theme.md)Xamarin.Android 應用程式中使用材料設計相關資訊)。 使用 Android Marshmallow，Google 導入了*Android 設計支援程式庫*，讓應用程式輕鬆採用材料的開發人員設計的外觀及操作。 此程式庫包含下列元件：

-   **CoordinatorLayout** &ndash;新`CoordinatorLayout`小工具是類似，但比功能更強大`FrameLayout`。 您可以使用`CoordinatorLayout`做為子檢視的容器，或作為最上層的配置，它提供`layout_anchor`可用來錨點檢視相對於其他檢視的屬性。

-   **摺疊工具列**&ndash;新`CollapsingToolbarLayout`是一種摺疊的應用程式列是包裝函式， `Toolbar`。 (請注意，*應用程式列*是什麼之前稱為*動作列*。)

-   **浮動的動作按鈕**&ndash;圓鈕，表示您的應用程式介面的主要動作。

-   **浮動編輯文字的標籤**&ndash;會使用新`TextInputLayout`小工具 (其包裝`EditText`) 隱藏時提示使用者輸入文字時顯示的浮動的標籤。

-   **導覽檢視**&ndash;新`NavigationView`小工具可協助您使用 [瀏覽] 下拉式清單中的使用者更輕鬆地瀏覽的方式。

-   **Snackbar** &ndash;新`SnackBar`小工具是輕量型的意見反應機制 （類似於快顯通知），會顯示在畫面中，不會優先於所有出現在螢幕上的其他項目底部的簡短訊息。

-   **Material 的索引標籤**&ndash;新`TabLayout`小工具提供水平配置在您的應用程式中實作最上層導覽的方式顯示索引標籤。

若要善用[設計支援程式庫](http://developer.android.com/tools/support-library/features.html#design)在您的 Xamarin.Android 應用程式，下載並安裝 Xamarin [Xamarin 支援程式庫設計](https://www.nuget.org/packages/Xamarin.Android.Support.Design/)NuGet 套件。

請參閱[美觀的 Android 支援設計程式庫的 Material Design](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/)如需詳細資訊 （包括程式碼範例） 使用 Xamarin.Android 應用程式中的材料設計支援程式庫。
Xamarin 提供的範例應用程式，示範新的 Android 設計程式庫，在 Xamarin.Android &ndash; [Cheesesquare](https://developer.xamarin.com/samples/monodroid/android5.0/Cheesesquare)。
這個範例會示範設計程式庫的下列功能：


-   摺疊的工具列
-   浮動的動作按鈕
-   檢視錨定
-   NavigationView
-   Snackbar

如需有關設計程式庫的詳細資訊，請參閱[Android 設計支援程式庫](http://android-developers.blogspot.co.at/2015/05/android-design-support-library.html)Android 開發人員部落格。


### <a name="additional-library-updates"></a>其他程式庫的更新

除了 Android Marshmallow，Google 宣幾個核心的 Android 程式庫的相關的更新。 Xamarin 提供 Xamarin.Android 支援透過數個預覽版本的 NuGet 套件的更新： 

-   [Google Play 服務](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; Google Play 服務的最新版本包含新*應用程式邀請*功能，可讓使用者能夠與朋友分享其應用程式。 如需這項功能的詳細資訊，請參閱[展開您的應用程式使用 Google 的應用程式邀請的觸達](http://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/)。 

-   [Android 支援程式庫](https://www.nuget.org/packages?q=xamarin+support+library)&ndash;這些 Nuget 提供功能，同時提供回溯相容性的 framework 版本為 Android Api 僅適用於程式庫 Api。 

-   [Android 到穿戴程式庫](https://www.nuget.org/packages/Xamarin.Android.Wear)&ndash;此 NuGet 包含 Google Play 服務繫結。 到穿戴程式庫的最新版本帶入 Android Wear 的平台的新功能 （包括自訂的應用程式的瀏覽更加輕鬆）。 


## <a name="summary"></a>總結

這篇文章介紹 Android Marshmallow，並說明如何安裝和設定上 Marshmallow 的最新的工具和適用於 Xamarin.Android 開發的封裝。 它也會提供適用於 Xamarin.Android 開發的最令人興奮的新 Android Marshmallow 功能的概觀。


## <a name="related-links"></a>相關連結

- [Android 6.0 Marshmallow](http://developer.android.com/about/versions/marshmallow/index.html)
- [取得 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [功能概觀](https://developer.android.com/preview/api-overview.html)
- [版本資訊](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1.99/)
- [RuntimePermissions （範例）](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions)
- [ConfirmCredential （範例）](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential)
- [FingerprintDialog （範例）](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog)
