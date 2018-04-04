---
title: 了解 Android API 層級
description: Xamarin.Android 的數個 Android API 層級設定會決定使用多個版本的 Android 應用程式的相容性。 本指南說明這些設定代表什麼意思，如何進行設定，以及效果為何他們對您的應用程式在執行階段。
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 8f284fefd260764c6f09d78d2518bfd115782cd2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="understanding-android-api-levels"></a>了解 Android API 層級

_Xamarin.Android 的數個 Android API 層級設定會決定使用多個版本的 Android 應用程式的相容性。本指南說明這些設定代表什麼意思，如何進行設定，以及效果為何他們對您的應用程式在執行階段。_


## <a name="quick-start"></a>快速入門

Xamarin.Android 會公開三個 Android API 層級的專案設定：

-   [目標 Framework](#framework) &ndash;指定哪一個架構來建置您的應用程式。 在使用此應用程式開發介面層級*編譯*Xamarin.Android 的時間。

-   [最低的 Android 版本](#minimum)&ndash;指定您想要您的應用程式支援的最舊的 Android 版本。 在使用此應用程式開發介面層級*執行*android 時間。

-   [Android 的版本為目標](#target)&ndash;指定 Android 應用程式的版本要在上執行。 在使用此應用程式開發介面層級*執行*android 時間。

您可以將應用程式開發介面層級設定為您的專案之前，您必須安裝 SDK 平台元件，該應用程式開發介面層級。 如需有關下載和安裝 Android SDK 元件的詳細資訊，請參閱[Android SDK 安裝程式](~/android/get-started/installation/android-sdk.md)。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

一般來說，所有三個 Xamarin.Android API 層級會設定為相同的值。 在**應用程式**頁面上，設定**使用 Android 版本 (目標 Framework) 編譯**最新穩定的 API 版本 （或至少具有所有所需的功能的 Android 版本）。
在下列螢幕擷取畫面中，目標 Framework 設定為**Android 7.1 (API 層級 25-Nougat)**:

[![目標 Framework 版本預設為使用 Android 版本編譯](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

在**Android 資訊清單**頁面上，設定 Android 最低版本為**使用 SDK 版本編譯使用**和相同的值 （在下列程式碼的目標 Framework 版本設定的目標 Android 版本螢幕擷取畫面，Android 目標架構設為**Android 7.1 (Nougat)**):

[![最小值和目標 Android 版本設為目標的 Framework 版本](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

如果您想要維持回溯相容性的較舊版本的 Android，設定**Android 最小版本到目標**Android 的最舊版本，您想要支援您的應用程式。 (請注意，應用程式開發介面層級 14 所需的最低 API 層級[Google Play 服務和 Firebase 支援](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)。)下列範例組態中可支援應用程式開發介面層級 14 到 25 的 API 層級的 Android 版本：

[![使用 API 層級 25 編譯 Nougat，設為應用程式開發介面層級 14 的 Android 最低版本](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

一般來說，所有三個 Xamarin.Android API 層級會設定為相同的值。 設定**目標 framework**最新穩定的 API 版本 （或至少具有所有所需的功能的 Android 版本）。 若要設定**目標 framework**，瀏覽至**建置 > 一般**中**專案選項**。 在下列螢幕擷取畫面中，目標 Framework 設定為**使用最新安裝的平台 (8.0)**:

[![預設為使用最新安裝的平台的目標 framework](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

您可以找到的最小值和目標 Android 版本設定**建置 > Android 應用程式**中**專案選項**。 設定 Android 最低版本為**Automatic-使用目標 framework 版本**和相同的值的目標 Framework 版本設定的目標 Android 版本。 在下列螢幕擷取畫面中，Android 目標架構設為**Android 8.0 (API level 26)**以符合上述的目標 Framework 設定：

[![專案選項中設定的目標和架構層級](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

如果您想要維持回溯相容性的較舊版本的 Android，變更**Android 最小版本**Android 的最舊版本，您想要支援您的應用程式。 請注意，應用程式開發介面層級 14 所需的最低 API 層級[Google Play 服務和 Firebase 支援](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)。
例如，下列組態支援 Android 版本早 API 層級 14:

[![最小值和目標版本設為 Automatic-使用目標 framework 版本](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----


如果您的應用程式支援多個的 Android 版本，您的程式碼必須包含執行階段檢查，以確保您的應用程式使用的 Android 最低的版本設定 (請參閱[Android 版本的執行階段會檢查](#runtimechecks)下方如需詳細資訊)。 如果您要使用或建立程式庫，請參閱[API 層級和文件庫](#libraries)以下的最佳作法設定應用程式開發介面層級的文件庫設定。



## <a name="android-versions-and-api-levels"></a>Android 版本和應用程式開發介面層級

每個 Android 版本 Android 平台發展，並有新的 Android 版本發行時，會指派唯一整數識別碼，稱為*API 層級*。 因此，每個 Android 版本會對應至單一的 Android API 層級。 使用者會安裝較舊的以及最新版本的 Android 應用程式，因為真實世界的 Android 應用程式必須設計成使用多個 Android API 層級。


### <a name="android-versions"></a>Android 版本

每個版本的 Android 會由多個名稱：

-   Android 的版本，例如**Android 7.1**
-   程式碼的名稱，例如_Nougat_
-   對應的 API 層級，例如**API 層級 25**

Android 的程式碼名稱可能對應到多個版本與應用程式開發介面層級 （如下列清單所示），但每個 Android 版本對應到一個應用程式開發介面層級。

此外，定義 Xamarin.Android*建置版本代碼*會對應到目前已知的 Android API 層級。 下列清單可協助您應用程式開發介面層級、 Android 版本、 程式碼名稱和 Xamarin.Android 建置版本的程式碼之間進行轉譯。

-   **應用程式開發介面 27 (Android 8.1)** &ndash; _Oreo_2017 年 12 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.OMr1`

-   **應用程式開發介面 26 (Android 8.0)** &ndash; _Oreo_2017 年 8 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.O`

-   **應用程式開發介面 25 (Android 7.1)** &ndash; _Nougat_2016 年 12 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.NMr1`

-   **應用程式開發介面 24 (Android 7.0)** &ndash; _Nougat_2016 年 8 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.N`

-   **API 23 (Android 6.0)** &ndash; _Marshmallow_2015 年 8 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.M`

-   **應用程式開發介面 22 (Android 5.1)** &ndash; _棒棒糖符號_2015 年 3 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.LollipopMr1`

-   **API 21 (Android 5.0)** &ndash; _棒棒糖符號_2014 年 11 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Lollipop`

-   **應用程式開發介面 20 (Android 4.4W)** &ndash; _Kitkat 監看式_2014 年 6 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.KitKatWatch`

-   **API 19 (Android 4.4)** &ndash; _Kitkat_，2013 年 10 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.KitKat`

-   **應用程式開發介面 18 (Android 4.3)** &ndash; _軟糖 Bean_，2013 年 7 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.JellyBeanMr2`

-   **應用程式開發介面 17 （Android 4.2 4.2.2 節）** &ndash; _軟糖 Bean_、 2012 年 11 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.JellyBeanMr1`

-   **應用程式開發介面 16 (Android 4.1-4.1.1)** &ndash; _軟糖 Bean_、 2012 年 6 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.JellyBean`

-   **API 15 (Android 4.0.3-4.0.4)** &ndash; _冰淇淋三明治_2011 年 12 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.IceCreamSandwichMr1`

-   **API 14 (Android 4.0-4.0.2)** &ndash; _冰淇淋三明治_2011 年 10 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.IceCreamSandwich`

-   **應用程式開發介面 13 (Android 3.2)** &ndash; _Honeycomb_2011 年 6 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.HoneyCombMr2`

-   **應用程式開發介面 12 (Android 3.1.x)** &ndash; _Honeycomb_2011 年發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.HoneyCombMr1`

-   **應用程式開發介面 11 (Android 3.0.x)** &ndash; _Honeycomb_2011 年 2 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.HoneyComb`

-   **應用程式開發介面 10 (Android 2.3.3-2.3.4)** &ndash; _Gingerbread_2011 年 2 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.GingerBreadMr1`

-   **應用程式開發介面 9 (Android 2.3-2.3.2)** &ndash; _Gingerbread_，2010 年 11 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.GingerBread`

-   **應用程式開發介面 8 (Android 2.2.x)** &ndash; _Froyo_，2010 年 6 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Froyo`

-   **應用程式開發介面 7 （Android 2.1.x 版）** &ndash; _Eclair_，2010 年 1 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.EclairMr1`

-   **應用程式開發介面 6 (Android 2.0.1)** &ndash; _Eclair_2009 年 12 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Eclair01`

-   **應用程式開發介面 5 (Android 2.0)** &ndash; _Eclair_2009 年 11 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Eclair`

-   **應用程式開發介面 4 (Android 1.6)** &ndash; _甜甜圈_2009 年 9 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Donut`

-   **應用程式開發介面 3 (Android 1.5)** &ndash; _Cupcake_2009 年發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Cupcake`

-   **(Android 1.1) 的 API 2** &ndash; _基底_2009 年 2 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Base11`

-   **應用程式開發介面 1 (Android 1.0)** &ndash; _基底_，2008 年 10 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Base`


因為此清單指出，經常發行新版的 Android&ndash;有時每年的數個版本。 如此一來，可能會執行您的應用程式的 Android 裝置的 universe 包含的各種不同的舊版和新版的 Android 版本。 您要如何保證您的應用程式將會一致且可靠地執行這麼多的不同版本的 Android？ Android 的應用程式開發介面層級可以協助您管理這個問題。


### <a name="android-api-levels"></a>Android API 層級

每個 Android 裝置在執行完全*一個*API 層級&ndash;此應用程式開發介面層級保證是唯一的每個 Android 平台版本。 應用程式開發介面層級會精確識別您的應用程式可以呼叫; API 集的版本它會識別身為開發人員的資訊清單項目、 權限等，您的程式碼組合。 Android 的應用程式開發介面層級的系統可協助 Android 應用程式相容的裝置上安裝應用程式之前的 Android 的系統映像。

建立應用程式時，它會包含下列應用程式開發介面層級資訊：

-   *目標*建置應用程式上執行的 Android API 層級。

-   *最小*Android 裝置必須擁有以便執行您的應用程式的 Android API 層級。 

這些設定會用來確保在安裝期間，正確地執行應用程式所需的功能是 Android 裝置上的可用。 如果沒有，則會封鎖該裝置上執行應用程式。 例如，如果 Android 裝置的應用程式開發介面層級低於您指定應用程式的最小 API 層級，在 Android 裝置會防止使用者安裝應用程式。


## <a name="project-api-level-settings"></a>應用程式開發介面層級的專案設定

下列各節將說明如何使用 SDK Manager 準備您的開發環境的應用程式開發介面層級做為目標，後面接著詳細說明如何設定*目標 Framework*，*最小值Android 版本*，和*目標 Android 版本*Xamarin.Android 中的設定。


### <a name="android-sdk-platforms"></a>Android SDK 平台

Xamarin.Android 中選取的目標或最小 API 層級之前，您必須安裝的 Android SDK 平台版本對應到該應用程式開發介面層級。 目標 Framework、 Android 最小版本，以及目標 Android 版本的可用選項的範圍僅限於您已安裝的 Android SDK 範圍版本。 您可以使用 SDK Manager 確認安裝所需的 Android SDK 版本，而且您可以使用它以加入您需要應用程式的任何新應用程式開發介面層級。 如果您不熟悉如何安裝應用程式開發介面層級，請參閱[Android SDK 安裝程式](~/android/get-started/installation/android-sdk.md)。

<a name="framework" />

### <a name="target-framework"></a>目標 Framework

*目標 Framework* (也稱為`compileSdkVersion`) 是在建置階段編譯您的應用程式特定的 Android 架構版本 （API 層級）。 此設定可指定哪些應用程式開發介面應用程式*預期*時執行，但它沒有任何作用所在應用程式開發介面是實際可用的應用程式在安裝時使用。 如此一來，變更目標 Framework 設定不會變更執行階段行為。

目標 Framework 識別您的應用程式會針對連結的程式庫版本&ndash;這會決定哪些應用程式開發介面，您可以使用您的應用程式中。 例如，如果您想要使用[NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) Android 5.0 棒棒糖符號中所導入的方法，您必須將目標架構設**（棒棒糖符號） 的應用程式開發介面層級 21**或更新版本。 如果您專案的目標 Framework 設定為應用程式開發介面層級，例如**API Level 19 (KitKat)**並再試一次呼叫`SetCategory`程式碼中的方法，就會產生編譯錯誤。

我們建議，您一律使用編譯*最新*可用的目標 Framework 版本。 如此一來您提供很有幫助的警告訊息的任何已被取代的 Api，可能會由您的程式碼呼叫。 當您使用最新的支援程式庫版本時使用的最新的目標 Framework 版本是特別重要&ndash;每個程式庫預期您的應用程式是編譯該支援程式庫的最小 API 層級或更高。 

> [!NOTE]
> 從 8 月 2018年，Google 播放主控台將會需要新的應用程式目標應用程式開發介面層級 26 (Android 8.0) 或更高版本。
現有的應用程式必須以 API 層級 26 或更高版本在 11 月版 2018年開頭為目標。 如需詳細資訊，請參閱[改善應用程式安全性和效能返回年的 Google Play 上](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html)。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要存取 Visual Studio 中的目標 Framework 設定，開啟 [專案屬性中的**方案總管] 中**選取**應用程式**頁面：

[![應用程式專案屬性 頁面](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

在下拉式功能表中選取應用程式開發介面層級設定目標 Framework**使用 Android 版本編譯**如上所示。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要存取 Visual Studio 中的目標 Framework 設定適用於 Mac，以滑鼠右鍵按一下專案名稱，然後選取**選項**; 這會開啟**專案選項**對話方塊。 在這個對話方塊中，瀏覽至**建置 > 一般**如下所示：

[![建立 [一般] 區段中的 [專案選項] 頁面](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

目標 Framework 設定右邊的下拉式選單中選取的應用程式開發介面層級**目標 framework**如上所示。

-----


<a name="minimum" />

### <a name="minimum-android-version"></a>最低的 Android 版本

*Android 最小版本*(也稱為`minSdkVersion`) 是 Android OS （亦即，最低 API 層級），可以安裝和執行應用程式的舊版本。 根據預設，應用程式只能安裝在與目標架構設定的裝置上或更高版本;如果 Android 最小值的版本設定為*低*比目標架構設定中，您的應用程式也可以執行 Android 的較早版本上。 例如，如果您將目標 Framework 設定為**Android 7.1 (Nougat)**和設定的最小值 Android 版本**Android 4.0.3 （冰淇淋三明治）**，您的應用程式可以安裝在任何平台應用程式開發介面層級 15應用程式開發介面層級 25 （含)。

雖然您的應用程式可能成功建置，並在此範圍的平台上安裝，但這不保證已成功將*執行*所有這些平台上。 例如，如果您的應用程式安裝在**Android 5.0 （棒棒糖符號）**和程式碼呼叫的 API 僅適用於**Android 7.1 (Nougat)**和更新版本中，您的應用程式將取得執行階段錯誤，並且可能會損毀。 因此，必須確定您的程式碼&ndash;在執行階段&ndash;呼叫只在 Android 裝置上執行所支援的 Api。 換句話說，您的程式碼必須包含明確的執行階段檢查，以確保您的應用程式會使用較新的 Api，只會不夠新，無法支援他們的裝置上。
[執行階段會檢查 Android 版本](#runtimechecks)稍後在本指南中，說明如何將這些執行階段檢查加入至您的程式碼。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要存取 Visual Studio 中的最小值 Android 版本設定，開啟 [專案屬性中的**方案總管] 中**選取**Android 資訊清單**頁面。 在底下的下拉式清單功能表**Android 最小版本**您可以為您的應用程式選取 Android 最小版本：

[![目標選項設定為使用 SDK 版本編譯的最小 Android](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

如果您選取**使用 SDK 版本編譯使用**，最小值 Android 版本會做為目標架構設定相同。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要存取 Visual Studio 中的目標 Framework 設定適用於 Mac，以滑鼠右鍵按一下專案名稱，然後選取**選項**; 這會開啟**專案選項**對話方塊。 瀏覽至**建置 > Android 應用程式**。
使用右邊的下拉式選單**Android 最小版本**，您可以設定您的應用程式的 Android 最低版本：

[![最低的 Android 版本設為 Automatic-使用目標 framework 版本](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

如果您選取**自動&ndash;使用目標 framework 版本**，最小值 Android 版本會做為目標架構設定相同。

-----


<a name="target" />

### <a name="target-android-version"></a>目標 Android 版本

*目標 Android 版本*(也稱為`targetSdkVersion`) 是應用程式開發介面層級的 Android 裝置，應用程式預期執行。 Android 使用此設定來判斷是否要啟用的任何相容性行為&ndash;這可確保您的應用程式會繼續依照您預期的方式運作。 Android 使用您的應用程式的目標 Android 版本設定，找出哪些行為變更可以套用至您的應用程式，而不會中斷它 （這是 Android 提供往後相容性的方式）。

目標 Framework 和目標 Android 版本，同時擁有非常相似的名稱，不是相同的動作。 目標 Framework 設定目標應用程式開發介面層級資訊與通訊 Xamarin.Android 用於*編譯時期*，而目標 Android 版本目標 API 層級資訊與通訊 Android 用於*執行階段*（當應用程式時在裝置上安裝並執行）。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要存取這項設定 Visual Studio 中的，開啟 [專案屬性中的**方案總管] 中**選取**Android 資訊清單**頁面。 在底下的下拉式清單功能表**目標 Android 版本**您可以為您的應用程式選取的目標 Android 版本：

[![設定為使用 SDK 版本編譯的目標 Android 版本](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

我們建議您明確設定為最新版的 Android 您用來測試您的應用程式的目標 Android 版本。 在理想情況下，它應該設定為最新版的 Android SDK&ndash;這可讓您使用新的 Api，透過的行為變更的工作之前。 大部分的開發人員，我們*不*建議您將設定的目標 Android 版本**使用 SDK 版本編譯使用**。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要存取 Visual Studio 中的目標 Framework 設定適用於 Mac，以滑鼠右鍵按一下專案名稱，然後選取**選項**; 這會開啟**專案選項**對話方塊。 瀏覽至**建置 > Android 應用程式**。
使用右邊的下拉式選單**目標 Android 版本**，您可以設定您的應用程式的目標 Android 版本：

[![目標 Android 版本設為 Automatic-使用目標 framework 版本](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

我們建議您明確設定為最新版的 Android 您用來測試您的應用程式的目標 Android 版本。 在理想情況下，它應該設定為最新可用的 Android SDK 版本&ndash;這可讓您使用新的 Api，透過的行為變更的工作之前。 大部分的開發人員，我們不建議設定的 Android 目標版本為**Automatic-使用目標 framework 版本**。

-----

一般情況下，目標 Android 版本應該至少 Android 版本和目標 Framework 所繫結。 即：

**最低的 Android 版本 < = 目標 Android 版本 < = 目標 Framework**

如需 SDK 層級的詳細資訊，請參閱 Android 開發人員[使用 sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html)文件。


<a name="runtimechecks" />

## <a name="runtime-checks-for-android-versions"></a>針對 Android 版本的執行階段檢查

Android 每一個新版本發行之後，架構應用程式開發介面會提供新的更新或取代功能。 有幾個例外狀況，從舊版的 Android API 功能傳送向前復原到較新的 Android 版本，而不需修改。 如此一來，如果您的應用程式在特定的 Android API 層級上執行，它將通常無法執行更新版本的 Android API 層級，而不需修改。 但要是您也希望在舊版的 Android 上執行您的應用程式嗎？

如果您選取的最小值 Android 版本*低*比您的目標 Framework 設定，某些應用程式開發介面可能無法使用您的應用程式在執行階段。 不過，您的應用程式仍然可以執行先前在裝置上，但功能會減少。 對應於 Android 最小值的版本設定的 Android 平台上未提供每個 api，您的程式碼必須明確檢查的值`Android.OS.Build.VERSION.SdkInt`屬性來判斷應用程式執行的平台的應用程式開發介面層級。 如果應用程式開發介面層級為*低*支援 API 的最小值 Android 版本比您想要呼叫，則必須設法將正常運作，而不需要進行此應用程式開發介面呼叫您的程式碼。

例如，假設我們想要使用[NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/)方法來分類上執行時的通知**Android 5.0 棒棒糖符號**（和更新版本），但我們仍想以我們的應用程式在舊版的 Android 上執行，例如**Android 4.1 軟糖 Bean** (其中`SetCategory`不提供)。 本指南的開頭的 Android 版本資料表的參考，我們看到的組建版本程式碼**Android 5.0 棒棒糖符號**是`Android.OS.BuildVersionCodes.Lollipop`。 若要支援舊版的 Android where`SetCategory`是無法使用，我們的程式碼才能偵測在執行階段的應用程式開發介面層級，以及有條件地呼叫`SetCategory`僅當應用程式開發介面層級是大於或等於棒棒糖符號建置版本的程式碼：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

在此範例中，我們的應用程式的目標 Framework 設定為**Android 5.0 (API 層級 21)**且其最小值 Android 版本設為**Android 4.1 (API 層級 16)**。 因為`SetCategory`在 API 層級`Android.OS.BuildVersionCodes.Lollipop`和更新版本中，此程式碼範例會呼叫`SetCategory`只時實際可用&ndash;會*不*嘗試呼叫`SetCategory`時應用程式開發介面層級為 16、 17、 18、 19、 或 20。 只有的通知時，不會排序正確 （因為它們不以類型分類），但仍來提醒使用者發佈通知功能會減少這些較舊的 Android 版本。 我們的應用程式仍能運作，但其功能稍微變差。

一般而言，建置版本檢查可協助您決定在執行階段之間做的事與舊的方法中的新方法的程式碼。 例如: 

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    // Do things the Lollipop way
}
else
{
    // Do things the pre-Lollipop way
}
```

沒有既快速又簡單的規則，說明如何減少或缺少一或多個應用程式開發介面的舊 Android 版本上執行時修改您的應用程式功能。 在某些情況下 (例如，在`SetCategory`上述範例)，便是在未提供省略的 API 呼叫。 不過，在其他情況下，您可能需要實作替代功能時`Android.OS.Build.VERSION.SdkInt`偵測到小於 API 層級，您的應用程式需要呈現其最佳的經驗。

<a name="libraries" />

## <a name="api-levels-and-libraries"></a>應用程式開發介面層級和文件庫

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

當您建立 Xamarin.Android 程式庫專案 （例如類別庫或繫結的程式庫） 時，您可以設定目標 Framework 設定&ndash;不提供 Android 最小版本和目標 Android 版本設定。 這是因為沒有任何**Android 資訊清單**頁面：

[![只有在編譯使用 Android 版本 選項可供](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

當您建立的 Xamarin.Android 程式庫專案時，沒有任何**Android 應用程式**頁面，您可以在其中設定 Android 最小版本和目標 Android 版本&ndash;Android 最小版本和目標無法使用 android 版本設定。
這是因為沒有任何**建置 > Android 應用程式**頁面):

[![建立沒有最小值和目標版本選項 [一般] 頁面](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

Android 最小版本和目標 Android 版本設定沒有因為產生的程式庫不是一個獨立應用程式&ndash;程式庫無法根據封裝於應用程式的任何 Android 版本上執行。 您可以指定程式庫是如何*編譯*，但您無法預測的平台應用程式開發介面層級將在上執行的程式庫。 這一點，使用或建立程式庫時，應該要觀察以下最佳作法：

-   **使用 Android 程式庫時**&ndash;如果您正在耗用 Android 程式庫，在您的應用程式，請務必設定您的應用程式的目標 Framework 的應用程式開發介面層級設定也就*至少最高*目標程式庫的 [架構] 設定。

-   **建立 Android 程式庫時**&ndash;如果您要建立 Android 程式庫，供其他應用程式，請務必將其目標架構設定為 需編譯的最小 API 層級。

這些最佳作法建議來協助防止文件庫嘗試呼叫無法在執行階段 （這可能會導致應用程式損毀） API 的情況。 如果您的程式庫開發人員，您應盡可能限制您使用的總應用程式開發介面的介面區小且妥善建立子集的 API 呼叫。 因此這樣做有助於確保，您的程式庫可以使用安全地在較寬的範圍的 Android 版本。


## <a name="summary"></a>總結

本指南說明如何使用 Android API 層級來管理不同版本的 Android 應用程式相容性。 它提供詳細的步驟來設定 Xamarin.Android*目標 Framework*， *Android 最小版本*，和*目標 Android 版本*專案設定。 使用 Android SDK 管理員來安裝 SDK 套件的指示包含有關如何撰寫程式碼來處理不同的應用程式開發介面層級，在執行階段的範例，以及說明如何管理應用程式開發介面層級建立或取用 Android 程式庫時提供該檔案。 它也會提供與應用程式開發介面層級相關 Android 版本號碼 （例如 Android 4.4)、 Android 版本名稱 （例如 Kitkat) 和 Xamarin.Android 組建版本代碼的完整清單。


## <a name="related-links"></a>相關連結

- [Android SDK 安裝](~/android/get-started/installation/android-sdk.md)
- [SDK CLI 工具變更](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [挑選您 compileSdkVersion、 minSdkVersion 和 targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [應用程式開發介面層級為何？](http://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [名稱、 標籤和組建編號](https://source.android.com/source/build-numbers)
