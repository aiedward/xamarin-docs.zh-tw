---
title: 了解 Android API 層級
description: Xamarin.Android 會有數個 Android API 層級設定，判斷您的應用程式與多個版本的 Android 的相容性。 本指南將說明這些設定代表什麼、 如何進行設定，以及何種影響它們在執行階段對您的應用程式。
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/02/2018
ms.openlocfilehash: 3b060567b47395bc213627c9378de4fca9db41bb
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403334"
---
# <a name="understanding-android-api-levels"></a>了解 Android API 層級

_Xamarin.Android 會有數個 Android API 層級設定，判斷您的應用程式與多個版本的 Android 的相容性。本指南將說明這些設定代表什麼、 如何進行設定，以及何種影響它們在執行階段對您的應用程式。_


## <a name="quick-start"></a>快速入門

Xamarin.Android 會公開三個 Android API 層級的專案設定：

-   [目標 Framework](#framework) &ndash;指定要用於建立您的應用程式的架構。 在使用此 API 層級*編譯*xamarin.android 的時間。

-   [最低 Android 版本](#minimum)&ndash;指定最舊的 Android 版本，您想要支援您的應用程式。 在使用此 API 層級*執行*android 時間。

-   [目標 Android 版本](#target)&ndash;指定您的應用程式的 Android 版本要在上執行。 在使用此 API 層級*執行*android 時間。

您可以將 API 層級設定為您的專案之前，您必須安裝該 API 層級的 SDK 平台元件。 如需有關下載及安裝 Android SDK 元件的詳細資訊，請參閱 < [Android SDK 安裝程式](~/android/get-started/installation/android-sdk.md)。

> [!NOTE]
> 從 2018 年 8 月開始，Google Play 主控台將會需要新的應用程式目標 API 層級 26 (Android 8.0) 或更高版本。
現有的應用程式必須以 API 層級 26 或更高版本在 2018 年 11 月的開頭為目標。 如需詳細資訊，請參閱 <<c0> [ 改善應用程式安全性和效能，多年來解決的 Google Play 上](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html)。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

一般來說，所有三個的 Xamarin.Android API 層級會設定為相同的值。 在上**應用程式**頁面上，將**使用 (目標 Framework) 的 Android 版本編譯**為最新的穩定 API 版本 （或至少具有所有所需的功能的 Android 版本）。
在下列螢幕擷取畫面中，目標 Framework 設定為**Android 7.1 (API 層級 25-Nougat)**:

[![目標 Framework 版本的預設值來使用 Android 版本編譯](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

在上**Android 資訊清單**頁面上，將最低 Android 版本設定為**使用 SDK 版本編譯使用**並將目標 Android 版本設定為相同的值 （在下列的目標 Framework 版本螢幕擷取畫面，目標 Android 架構設定為**Android 7.1 (Nougat)**):

[![最小值和目標 Android 版本設為 目標 Framework 版本](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

如果您想要維護回溯相容性的較早版本的 Android，設定**目標的最低 Android 版本**Android 的最舊版本，您想要支援您的應用程式。 (請注意，API 層級 14 所需的最低 API 層級[Google Play 服務，以及 Firebase 支援](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)。)下列範例組態中支援 API 層級 14 透過 API 層級 25 的 Android 的版本：

[![編譯使用 API 層級 25 Nougat，設定為 API 層級為 14 的最低 Android 版本](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

一般來說，所有三個的 Xamarin.Android API 層級會設定為相同的值。 設定**目標 framework**為最新的穩定 API 版本 （或至少具有所有所需的功能的 Android 版本）。 若要設定**目標 framework**，瀏覽至**建置 > 一般**中**專案選項**。 在下列螢幕擷取畫面中，目標 Framework 設定為**使用最新安裝的平台 (8.0)**:

[![預設為使用最新安裝的平台的目標 framework](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

可以在下找到的最小值和目標 Android 版本設定**建置 > Android 應用程式**中**專案選項**。 若要設定的最低 Android 版本**自動-使用目標 framework 版本**並將目標 Android 版本設定為 目標 Framework 版本與相同的值。 在下列螢幕擷取畫面中，目標 Android 架構設為**Android 8.0 （API 層級 26）** 以符合上述的目標 Framework 設定：

[![在 專案選項中設定的目標和架構層級](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

如果您想要維護回溯相容性的較早版本的 Android，變更**最低 Android 版本**Android 的最舊版本，您想要支援您的應用程式。 請注意，API 層級 14 所需的最低 API 層級[Google Play 服務，以及 Firebase 支援](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)。
例如，下列組態支援 Android 版本早在 API 層級 14:

[![最小值和目標版本設為 自動-使用目標 framework 版本](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----


如果您的應用程式支援多個的 Android 版本，您的程式碼必須包含執行階段檢查，以確保您的應用程式，適用於最低 Android 版本設定 (請參閱[Android 版本的執行階段會檢查](#runtimechecks)如下如需詳細資訊)。 如果您正在使用或建立程式庫，請參閱[API 層級和程式庫](#libraries)以下的最佳作法設定 API 層級的程式庫設定。



## <a name="android-versions-and-api-levels"></a>Android 版本和 API 層級

每個 Android 版本的 Android 平台發展及新的 Android 版本發行時，會指派唯一整數識別碼，稱為*API 層級*。 因此，每個 Android 版本會對應至單一的 Android API 層級。 使用者會安裝在舊版也為最新的 Android 應用程式，因為真實世界的 Android 應用程式必須設計成使用多個 Android API 層級。


### <a name="android-versions"></a>Android 版本

每個 Android 版本有多個名稱：

-   Android 版本，例如**Android 7.1**
-   程式碼的名稱，例如_Nougat_
-   對應的 API 層級，例如**API 層級 25**

Android 的程式碼名稱可能對應至多個版本和 API 層級 （如下列清單所示），但每個 Android 版本對應到一個 API 層級。

此外，定義 Xamarin.Android*建置版本代碼*會對應到目前已知的 Android API 層級。 下列清單可協助您 API 層級、 Android 版本、 程式碼名稱，以及 Xamarin.Android 建置版本程式碼之間進行轉換。

-   **API 27 (Android 8.1)** &ndash; _Oreo_，2017 年 12 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.OMr1`

-   **API 26 (Android 8.0)** &ndash; _Oreo_，2017 年 8 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.O`

-   **API 25 (Android 7.1)** &ndash; _Nougat_2016 年 12 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.NMr1`

-   **API 24 (Android 7.0)** &ndash; _Nougat_2016 年 8 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.N`

-   **API 23 (Android 6.0)** &ndash; _Marshmallow_2015 年 8 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.M`

-   **API 22 (Android 5.1)** &ndash; _棒棒糖符號_2015 年 3 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.LollipopMr1`

-   **(Android 5.0) 的 API 21** &ndash; _棒棒糖符號_，2014 年 11 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Lollipop`

-   **API 20 (Android 4.4W)** &ndash; _Kitkat 監看式_，2014 年 6 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.KitKatWatch`

-   **API 19 (Android 4.4)** &ndash; _Kitkat_，2013 年 10 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.KitKat`

-   **(Android 4.3) 的 API 18** &ndash; _Jelly Bean_，2013 年 7 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.JellyBeanMr2`

-   **API 17 (Android 4.2-4.2.2)** &ndash; _Jelly Bean_、 2012 年 11 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.JellyBeanMr1`

-   **API 16 (Android 4.1-4.1.1)** &ndash; _Jelly Bean_、 2012 年 6 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.JellyBean`

-   **API 15 (Android 4.0.3-4.0.4)** &ndash; _Ice Cream Sandwich_2011 年 12 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.IceCreamSandwichMr1`

-   **API 14 (Android 4.0-4.0.2)** &ndash; _Ice Cream Sandwich_2011 年 10 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.IceCreamSandwich`

-   **(Android 3.2) 的 API 13** &ndash; _Honeycomb_2011 年 6 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.HoneyCombMr2`

-   **API 12 (Android 3.1.x)** &ndash; _Honeycomb_2011 年推出。 建置版本的程式碼 `Android.OS.BuildVersionCodes.HoneyCombMr1`

-   **API 11 （Android 3.0.x 最）** &ndash; _Honeycomb_2011 年 2 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.HoneyComb`

-   **API 10 (Android 2.3.3-2.3.4)** &ndash; _Gingerbread_2011 年 2 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.GingerBreadMr1`

-   **API 9 (Android 2.3-2.3.2)** &ndash; _Gingerbread_，2010 年 11 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.GingerBread`

-   **API 8 （Android 2.2.x 版本）** &ndash; _Froyo_，2010 年 6 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Froyo`

-   **API 7 (Android 2.1.x)** &ndash; _Eclair_，2010 年 1 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.EclairMr1`

-   **API 6 (Android 2.0.1)** &ndash; _Eclair_2009 年 12 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Eclair01`

-   **API 5 (Android 2.0)** &ndash; _Eclair_2009 年 11 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Eclair`

-   **API 4 (Android 1.6)** &ndash; _甜甜圈_2009 年 9 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Donut`

-   **API 3 (Android 1.5)** &ndash; _Cupcake_2009 年推出。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Cupcake`

-   **API 2 (Android 1.1)** &ndash; _基底_2009 年 2 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Base11`

-   **API 1 (Android 1.0)** &ndash; _基底_，2008 年 10 月發行。 建置版本的程式碼 `Android.OS.BuildVersionCodes.Base`


如這份清單中所指示，經常發行新的 Android 版本&ndash;有時每年的數個版本。 如此一來，可能會執行您的應用程式的 Android 裝置的 universe 包含各種不同的舊版和新版的 Android 版本。 您要如何保證您的應用程式將會一致且可靠地執行這麼多的不同版本的 Android 上？ Android 的 API 層級可以協助您管理這個問題。


### <a name="android-api-levels"></a>Android API 層級

每個 Android 裝置會在執行完全*一個*API 層級&ndash;這個 API 層級保證是唯一的每個 Android 平台版本。 API 層級會精確識別您的應用程式可以呼叫; 的 API 集的版本它會識別身為開發人員資訊清單的項目、 權限等，您的程式碼的組合。 Android 的系統的 API 層級可協助您判斷應用程式是否相容的 Android 系統映像，才能在裝置上安裝應用程式的 Android。

建置應用程式時，它會包含下列的 API 層級的資訊：

-   *目標*建置應用程式上執行的 Android API 層級。

-   *最小*Android 裝置必須執行您的應用程式的 Android API 層級。 

若要確保正確地執行應用程式所需的功能可在 Android 裝置上安裝時，會使用這些設定。 如果沒有，應用程式會封鎖該裝置上執行。 比方說，如果 Android 裝置的 API 層級低於您指定應用程式的最低 API 層級，在 Android 裝置會防止使用者安裝您的應用程式。


## <a name="project-api-level-settings"></a>API 層級的專案設定

下列各節說明如何使用 SDK 管理員來準備開發環境，針對您想要的目標，API 層級後面接著詳細說明如何設定*目標 Framework*，*最小值Android 版本*，並*目標 Android 版本*在 Xamarin.Android 中的設定。


### <a name="android-sdk-platforms"></a>Android SDK 平台

您可以在 Xamarin.Android 中選取的目標或最低 API 層級之前，您必須安裝對應的 Android SDK 平台版本為該 API 層級。 目標 Framework 」、 「 最低 Android 版本，和 「 目標 Android 版本的可用選項的範圍僅限於您已安裝的 Android SDK 的範圍版本。 您可以使用 SDK 管理員，確認已安裝必要的 Android SDK 版本，而且您可以使用它來加入任何新的 API 層級所需的應用程式。 如果您不熟悉如何安裝 API 層級，請參閱[Android SDK 安裝程式](~/android/get-started/installation/android-sdk.md)。

<a name="framework" />

### <a name="target-framework"></a>目標 Framework

*目標 Framework* (也稱為`compileSdkVersion`) 是在建置階段編譯您的應用程式的特定 Android 架構版本 （API 層級）。 此設定指定的 Api 應用程式*預期*時它執行時，但它沒有任何作用所在 Api 可實際用於您的應用程式安裝時使用。 如此一來，變更目標 Framework 設定不會變更執行階段行為。

目標 Framework 會識別您的應用程式會針對連結的程式庫版本&ndash;這會決定您的應用程式中，您可以使用哪些 Api。 例如，如果您想要[NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) Android 5.0 Lollipop 中引進的方法，您必須將目標架構設為**API Level 21 (Lollipop)** 或更新版本。 如果您專案的目標架構的 api 層級設定這類**API Level 19 (KitKat)** ，並嘗試呼叫`SetCategory`程式碼中的方法，您會收到編譯錯誤。

我們建議，您一律使用編譯*最新*可用的目標 Framework 版本。 如此一來您提供很有幫助的警告訊息可能會由您的程式碼呼叫任何被取代的 api。 當您使用最新的支援程式庫版本時使用的最新的目標 Framework 版本是特別重要&ndash;每個程式庫預期您的應用程式是編譯該支援程式庫的最低 API 層級或更大。 


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要存取 Visual Studio 中的目標 Framework 設定，請開啟 專案屬性中的**方案總管**，然後選取**應用程式**頁面：

[![應用程式專案 [屬性] 頁面](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

選取下的下拉式選單中的 API 層級中設定目標 Framework**使用 Android 版本編譯**如上所示。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要存取在 Visual Studio for Mac 的目標 Framework 設定，以滑鼠右鍵按一下專案名稱，然後選取**選項**; 這會開啟**專案選項**對話方塊。 在這個對話方塊中，瀏覽至**建置 > 一般**如下所示：

[![建置一般區段中的 [專案選項] 頁面](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

選取右邊的下拉式選單中的 API 層級中設定目標 Framework**目標 framework**如上所示。

-----


<a name="minimum" />

### <a name="minimum-android-version"></a>最低 Android 版本

*最低 Android 版本*(也稱為`minSdkVersion`) 是最舊的 Android OS 版本 （亦即，最低 API 層級），可以安裝和執行您的應用程式。 根據預設，應用程式只能安裝在與目標架構設定的裝置上或更高版本;如果最低 Android 版本設定為*較低*比目標 Framework 設定中，您的應用程式也可以執行舊版 Android 上。 比方說，如果您設定目標 Framework **Android 7.1 (Nougat)** 並將最低 Android 版本設定為**Android 4.0.3 (Ice Cream Sandwich)**，您的應用程式可以安裝在任何平台，從 API 層級 15API 層級 25 日 （含)。

雖然您的應用程式可能會成功建置，並在此範圍的平台上安裝，但這不保證，即會順利*執行*所有這些平台上。 例如，如果您的應用程式安裝在**Android 5.0 (Lollipop)** 和您的程式碼呼叫的 API，僅適用於**Android 7.1 (Nougat)** 和更新版本中，您的應用程式會發生執行階段錯誤，並可能會損毀。 因此，必須確定您的程式碼&ndash;在執行階段&ndash;，它會呼叫只在 Android 裝置上執行所支援的 Api。 換句話說，您的程式碼必須包含明確的執行階段檢查，以確保您的應用程式只會不夠新，無法支援它們的裝置上使用較新的 Api。
[如需 Android 版本的執行階段會檢查](#runtimechecks)稍後在本指南中，說明如何將這些執行階段檢查新增至您的程式碼。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要存取 Visual Studio 中的最低 Android 版本設定，請開啟 專案屬性中的**方案總管**，然後選取**Android 資訊清單**頁面。 在下拉式功能表底下**最低 Android 版本**您可以選取您的應用程式的最低 Android 版本：

[![最低 Android 目標選項設定為使用 SDK 版本進行編譯](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

如果您選取**使用 SDK 版本編譯使用**，最低 Android 版本將會與目標架構設定相同。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要存取的最低 Android 版本，在 Visual Studio for Mac，以滑鼠右鍵按一下專案名稱，然後選取**選項**; 這會開啟**專案選項**對話方塊。 瀏覽至**建置 > Android 應用程式**。
使用右邊的下拉式選單**最低 Android 版本**，您可以設定您的應用程式的最低 Android 版本：

[![設定為自動-使用目標 framework 版本的最低 Android 版本](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

如果您選取**自動&ndash;使用目標 framework 版本**，最低 Android 版本將會與目標架構設定相同。

-----


<a name="target" />

### <a name="target-android-version"></a>目標 Android 版本

*目標 Android 版本*(也稱為`targetSdkVersion`) 是 API 層級的 Android 裝置的應用程式所執行的預期。 Android 使用此設定來判斷是否要啟用任何相容性行為&ndash;這可確保您的應用程式會繼續以您預期的方式運作。 Android 會使用您的應用程式的目標 Android 版本設定，來找出哪些行為變更可以套用至您的應用程式，而不會破壞它 （這是 Android 提供往後相容性的方式）。

目標 Framework 和目標 Android 版本而定，同時擁有非常類似的名稱，不是相同的項目。 目標 Framework 設定目標 API 層級資訊與通訊 Xamarin.Android 以供*編譯時期*，而目標 Android 版本進行通訊的目標 API 層級資訊至 Android 用於*執行階段*（當應用程式是在裝置上安裝並執行）。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要存取這項設定，在 Visual Studio 中的，開啟 專案屬性中的**方案總管**，然後選取**Android 資訊清單**頁面。 在下拉式功能表底下**目標 Android 版本**您可以為您的應用程式中選取的目標 Android 版本：

[![目標 Android 版本設定為使用 SDK 版本進行編譯](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

我們建議您明確設定為最新版的 Android，您用來測試您的應用程式的目標 Android 版本。 在理想情況下，它應該設定為最新的 Android SDK 版本&ndash;這可讓您使用新的 Api 之前執行的行為變更。 對於大部分的開發人員，我們*則否*建議設定的目標 Android 版本**使用 SDK 版本編譯使用**。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要存取此設定在 Visual Studio for Mac，以滑鼠右鍵按一下專案名稱，然後選取**選項**; 這會開啟**專案選項**對話方塊。 瀏覽至**建置 > Android 應用程式**。 使用右邊的下拉式選單**目標 Android 版本**，為您的應用程式的目標 Android 版本：

[![目標 Android 版本設定為自動-使用目標 framework 版本](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

我們建議您明確設定為最新版的 Android，您用來測試您的應用程式的目標 Android 版本。 在理想情況下，它應該設定為最新可用 Android SDK 版本&ndash;這可讓您使用新的 Api 之前執行的行為變更。 對於大部分的開發人員，我們不建議將目標 Android 版本設定為**自動-使用目標 framework 版本**。

-----

一般情況下，目標 Android 版本應該受到最小 Android 版本 」 和 「 目標 Framework。 即：

**最低 Android 版本 < = 目標 Android 版本 < = 目標 Framework**

如需有關 SDK 層級的詳細資訊，請參閱 Android 開發人員[會使用 sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html)文件。


<a name="runtimechecks" />

## <a name="runtime-checks-for-android-versions"></a>Android 版本的執行階段檢查

因為每個新版本的 Android 發行時，架構 API 會更新以提供新或取代功能。 少數的例外狀況，從舊的 Android 版本的 API 功能傳送轉寄到較新的 Android 版本，而不需要修改。 如此一來，如果您的應用程式在特定的 Android API 層級上執行，它通常能夠在更新版本的 Android API 層級，而不需要修改上執行。 但是，如果您也想要舊版 Android 上執行應用程式？

如果您選取的最低 Android 版本*較低*比您的目標 Framework 設定，某些 Api 可能無法使用您的應用程式在執行階段。 不過，您的應用程式仍然可以執行在先前的裝置，但以降低的功能。 針對每個對應至您的最低 Android 版本設定的 Android 平台未提供的 API，您的程式碼必須明確檢查的值`Android.OS.Build.VERSION.SdkInt`屬性來判斷應用程式執行的平台的 API 層級。 如果是 API 層級*較低*支援 API 的最低 Android 版本比您想要呼叫，則您的程式碼必須設法將正常運作，而不需進行此 API 呼叫。

例如，假設我們想要使用[NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/)方法來分類上執行時的通知**Android 5.0 Lollipop** （和更新版本），但我們仍希望我們的應用程式在舊版的 Android 上執行的這類**Android 4.1 Jelly Bean** (其中`SetCategory`不提供)。 在本指南開頭的 Android 版本資料表的參考，我們發現的組建版本程式碼**Android 5.0 Lollipop**是`Android.OS.BuildVersionCodes.Lollipop`。 為了支援舊版的 Android where`SetCategory`是無法使用，我們的程式碼可以偵測在執行階段 API 層級有條件地呼叫`SetCategory`僅當 API 層級是大於或等於棒棒糖符號的組建版本代碼：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

在此範例中，我們的應用程式目標架構設定為**Android 5.0 (API Level 21)** 且其最低 Android 版本設為**Android 4.1 (API 層級 16)**。 因為`SetCategory`位在 API 層級`Android.OS.BuildVersionCodes.Lollipop`及更新版本中，此程式碼範例會呼叫`SetCategory`只時實際可用&ndash;會*不*嘗試呼叫`SetCategory`時 API層級會是 16、 17、 18、 19、 或 20。 功能會減少這些稍早的 Android 版本，只有的通知時，不會排序正確 （因為它們不會依類型分類），但仍發佈通知來提醒使用者。 我們的應用程式仍可運作，但其功能稍微就會受到影響。

一般情況下，組建版本檢查可協助您決定在執行階段之間的新方式，與舊有的方式執行的程式碼。 例如: 

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

沒有快速又簡單的規則，說明如何減少或較舊缺少一或多個 Api 的 Android 版本上執行時修改您的應用程式功能。 在某些情況下 (例如，在`SetCategory`上述範例中)，便無法使用時，只要省略 API 呼叫。 不過，在其他情況下，您可能需要實作替代功能的時機`Android.OS.Build.VERSION.SdkInt`上偵測到能早於 API 層級，您的應用程式必須提供其最佳的體驗。

<a name="libraries" />

## <a name="api-levels-and-libraries"></a>API 層級和程式庫

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

當您建立 Xamarin.Android 程式庫專案 （例如類別程式庫或繫結程式庫） 時，您可以設定目標 Framework 設定&ndash;沒有可用的最低 Android 版本 」 和 「 目標 Android 版本設定。 這是因為沒有任何**Android 資訊清單**頁面：

[![只有在編譯使用 Android 版本 選項可供](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

當您建立 Xamarin.Android 程式庫專案時，沒有任何**Android 應用程式**頁面上，您可以在其中設定最低 Android 版本 」 和 「 目標 Android 版本&ndash;目標的最低 Android 版本找不到可用的 android 版本設定。
這是因為沒有任何**建置 > Android 應用程式**頁面):

[![建置沒有最小值和目標版本選項的一般頁面](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

目標 Android 版本設定的最低 Android 版本沒有因為產生的程式庫不是一個獨立應用程式&ndash;程式庫可以根據隨封裝的應用程式的任何 Android 版本上執行。 您可以指定為程式庫有何*編譯*，但您無法預測哪一個平台 API 層級將在上執行的程式庫。 這一點，使用或建立程式庫時，應該要觀察以下最佳作法：

-   **使用 Android 程式庫時**&ndash;如果您要在您的應用程式中使用 Android 程式庫，請務必將 api 層級設定也就您的應用程式目標架構*至少高達*目標程式庫的 [架構] 設定。

-   **建立 Android 程式庫時**&ndash;如果您要建立 Android 程式庫供其他應用程式，請務必將其目標 Framework 設定為 需要以編譯的最低 API 層級。

這些最佳作法建議來協助防止這種情況的程式庫嘗試呼叫不是在執行階段 （這可能會導致應用程式損毀） 可用的 API。 如果您的程式庫開發人員，您應該盡量限制您的 API 呼叫總數的 API 介面區的小型且妥善建立子集的使用量。 這樣做有助於確保您的程式庫可以安全地使用更多的 Android 版本。


## <a name="summary"></a>總結

本指南說明如何將 Android API 層級用來管理不同版本的 Android 應用程式相容性。 這會提供詳細的步驟，用於設定 Xamarin.Android*目標 Framework*，*最低 Android 版本*，並*目標 Android 版本*專案設定。 使用 Android SDK 管理員安裝 SDK 套件的指示包含範例，示範如何撰寫程式碼來處理不同的 API 層級，在執行階段，並說明如何管理 API 層級建立，或使用 Android 文件庫時提供它。 它也會提供與 API 層級 （例如 Android 4.4) 的 Android 版本號碼、 Android 版本名稱 （例如 Kitkat)，以及 Xamarin.Android 建置版本代碼的完整清單。


## <a name="related-links"></a>相關連結

- [Android SDK 安裝](~/android/get-started/installation/android-sdk.md)
- [SDK CLI 工具變更](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [挑選您 compileSdkVersion、 minSdkVersion 和 targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [什麼是 API 層級？](http://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [名稱、 標籤和組建編號](https://source.android.com/source/build-numbers)
