---
title: 瞭解 Android API 層級
description: Xamarin 有數個 Android API 層級設定, 可決定您的應用程式與多個 Android 版本的相容性。 本指南說明這些設定的意義、如何設定它們, 以及在執行時間對應用程式造成的影響。
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: 8c4050f439f3499289063c286afd255241bf0343
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521451"
---
# <a name="understanding-android-api-levels"></a>了解 Android API 層級

_Xamarin 有數個 Android API 層級設定, 可決定您的應用程式與多個 Android 版本的相容性。本指南說明這些設定的意義、如何設定它們, 以及在執行時間對應用程式造成的影響。_


## <a name="quick-start"></a>快速入門

Xamarin 會公開三個 Android API 層級專案設定:

- [目標 Framework](#framework)&ndash;指定要用來建立應用程式的架構。 這個 API 層級是在*編譯*時期由 Xamarin 所使用。

- [Android 最低版本](#minimum)&ndash;指定您想要讓應用程式支援的最舊 Android 版本。 Android 會在*運行*時間使用此 API 層級。

- 以[Android 版本為目標](#target)&ndash;指定您的應用程式要在其上執行的 Android 版本。 Android 會在*運行*時間使用此 API 層級。

您必須先安裝該 API 層級的 SDK 平臺元件, 才可以設定專案的 API 層級。 如需下載和安裝 Android SDK 元件的詳細資訊, 請參閱[Android SDK 設定](~/android/get-started/installation/android-sdk.md)。

> [!NOTE]
> 從2018年8月開始, Google Play 主控台將會要求新的應用程式以 API 層級 26 (Android 8.0) 或更高版本為目標。
自2018年11月起, 現有的應用程式將需要以 API 層級26或更新版本為目標。 如需詳細資訊，請參閱 [ 改善應用程式安全性和效能，多年來解決的 Google Play 上](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html)。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

一般來說, 所有三個 Xamarin. Android API 層級都會設定為相同的值。 在 [**應用程式**] 頁面上, 將 [**使用 Android 版本 (目標 Framework) 編譯**] 設定為最新的穩定 API 版本 (或至少為具有您所需之所有功能的 Android 版本)。
在下列螢幕擷取畫面中, [目標 Framework] 設定為 [ **Android 7.1 (API 層級 25-Nougat)** ]:

[![目標 Framework 版本預設為使用 Android 版本進行編譯](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

在 [ **Android 資訊清單**] 頁面上, 將 [android 最低版本] 設定為 [**使用 SDK 版本編譯**], 並將 [目標 android 版本] 設定為與目標 Framework 版本相同的值 (在下列螢幕擷取畫面中, 目標 android Framework 設定為**Android 7.1 (Nougat)** ):

[![最小值和目標 Android 版本設為目標 Framework 版本](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

如果您想要維持與舊版 Android 的回溯相容性, 請將 [**最小 android 版本] 設為目標**設為您想要讓應用程式支援的最舊版本 android。 (請注意, API 層級14是[Google Play 服務和 Firebase 支援](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)所需的最低 api 層級)。下列範例設定支援從 API 層級14到 API 層級25的 Android 版本:

[![使用 API 層級 25 Nougat 編譯, 最低 Android 版本設定為 API 層級14](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

一般來說, 所有三個 Xamarin. Android API 層級都會設定為相同的值。 將 [**目標 framework** ] 設為最新的穩定 API 版本 (或至少為具有您所需之所有功能的 Android 版本)。 若要設定**目標 framework**, 請在**專案選項**中流覽至 **[組建] > [一般**]。 在下列螢幕擷取畫面中, 目標 Framework 設定為**使用最新安裝的平臺 (8.0)** :

[![目標 framework 預設為使用最新安裝的平臺](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

可以在下找到的最小值和目標 Android 版本設定**建置 > Android 應用程式**中**專案選項**。 將 [最低 Android 版本] 設定為 [**自動使用目標 framework 版本**], 並將 [目標 android 版本] 設定為與目標 framework 版本相同的值。 在下列螢幕擷取畫面中, 目標 Android Framework 設定為**android 8.0 (API 層級 26)** , 以符合上述的目標 Framework 設定:

[![在專案選項中設定目標和架構層級](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

如果您想要維持與舊版 Android 的回溯相容性, 請將 [**最低 android 版本**] 變更為您想要讓應用程式支援的最舊版本 android。 請注意, API 層級14是[Google Play 服務和 Firebase 支援](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)所需的最低 api 層級。
例如, 下列設定支援 Android 版本, 早于 API 層級 14:

[![設定為自動使用目標 framework 版本的最低和目標版本](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----


如果您的應用程式支援多個 Android 版本, 您的程式碼必須包含執行時間檢查, 以確保您的應用程式可以使用最低的 Android 版本設定 (如需詳細資訊, 請參閱下列[Android 版本的執行時間檢查](#runtimechecks))。 如果您要使用或建立程式庫, 請參閱以下的[Api 層級和](#libraries)程式庫, 以取得設定程式庫的 API 層級設定的最佳做法。



## <a name="android-versions-and-api-levels"></a>Android 版本和 API 層級

隨著 Android 平臺演變併發行新的 Android 版本, 每個 Android 版本都會指派一個唯一的整數識別碼, 稱為*API 層級*。 因此, 每個 Android 版本都會對應至單一 Android API 層級。 因為使用者會將應用程式安裝在舊版和最新版本的 Android 上, 所以實際的 Android 應用程式必須設計成使用多個 Android API 層級。


### <a name="android-versions"></a>Android 版本

Android 的每個版本都有多個名稱:

- Android 版本, 例如**android 9.0**
- 程式碼 (或甜點) 名稱, 例如_圓形圖_
- 對應的 API 層級, 例如**api 層級 28**

Android 程式碼名稱可能對應至多個版本和 API 層級 (如下表所示), 但每個 Android 版本只會對應至一個 API 層級。

此外, Xamarin 會定義對應至目前已知 Android API 層級的*組建版本代碼*。 下表可協助您在 API 層級、android 版本、程式碼名稱和 Xamarin. android 組建版本代碼 (在`Android.OS`命名空間中定義組建版本代碼) 之間進行轉譯:

[!include[](~/android/includes/api-levels.md)]

如下表所示, 新的 Android 版本會經常&ndash;發行, 有時每年會有一個以上的版本。 因此, 可能會執行您應用程式的 Android 裝置, 包括各種舊版和較新的 Android 版本。 您要如何保證您的應用程式會以一致且可靠的方式在多個不同版本的 Android 上執行？ Android 的 API 層級可協助您管理此問題。


### <a name="android-api-levels"></a>Android API 層級

每個 android 裝置只會在*一個*api &ndash;層級執行。每個 android 平臺版本的此 api 層級保證都是唯一的。 API 層級會精確地識別您的應用程式可以呼叫的 API 集合版本;它會識別資訊清單元素、許可權等專案的組合, 以及您為開發人員撰寫的程式碼。 Android 的 API 層級系統可協助 Android 判斷應用程式在裝置上安裝應用程式之前, 是否與 Android 系統映射相容。

建立應用程式時, 它會包含下列 API 層級資訊:

- 建立應用程式所用的 Android*目標*API 層級。

- Android 裝置必須擁有才能執行應用程式的*最低*android API 層級。 

這些設定是用來確保正確執行應用程式所需的功能, 可在安裝時于 Android 裝置上使用。 如果不是, 則應用程式會遭到封鎖而無法在該裝置上執行。 例如, 如果 Android 裝置的 API 層級低於您為應用程式指定的最低 API 層級, 則 Android 裝置會阻止使用者安裝您的應用程式。


## <a name="project-api-level-settings"></a>專案 API 層級設定

下列各節說明如何使用 SDK 管理員, 為您想要設為目標的 API 層級準備您的開發環境, 並接著詳細說明如何設定*目標 Framework*、*最低 Android 版本*, 以及在 Xamarin android 中以 android 版本設定為目標。


### <a name="android-sdk-platforms"></a>Android SDK 平臺

您必須先安裝對應至該 API 層級的 Android SDK 平臺版本, 才可以在 Xamarin 中選取目標或最低 API 層級。 [目標 Framework]、[最低 Android 版本] 和 [目標 Android 版本] 的可用選項範圍僅限於您已安裝的 Android SDK 版本範圍。 您可以使用 SDK 管理員來確認所需的 Android SDK 版本已安裝, 而且您可以使用它來新增您的應用程式所需的任何新 API 層級。 如果您不熟悉如何安裝 API 層級, 請參閱[Android SDK 安裝程式](~/android/get-started/installation/android-sdk.md)。

<a name="framework" />

### <a name="target-framework"></a>目標 Framework

*目標 Framework* (也稱為`compileSdkVersion`) 是您的應用程式在組建時所編譯的特定 Android Framework 版本 (API 層級)。 此設定會指定您的應用程式在執行時*預期*使用的 api, 但不會影響應用程式在安裝時實際可用的 api。 因此, 變更目標 Framework 設定並不會變更執行時間行為。

目標 Framework 會識別您的應用程式所連結的連結&ndash;庫版本, 這項設定會決定您可以在應用程式中使用的 api。 例如, 如果您想要使用在 Android 5.0 棒 NotificationBuilder 中引進的[SetCategory](xref:Android.App.Notification.Builder.SetCategory*)方法, 則必須將目標 Framework 設定為**API 層級 21 (棒糖)** 或更新版本。 如果您將專案的目標架構設定為 api 層級 (例如**api 層級 19 (KitKat))** , 並嘗試`SetCategory`在您的程式碼中呼叫方法, 您會收到編譯錯誤。

我們建議您一律使用*最新*可用的目標 Framework 版本進行編譯。 這麼做會為您的程式碼可能呼叫的任何已淘汰 Api 提供有用的警告訊息。 當您使用最新的支援程式庫&ndash;時, 使用最新的目標 Framework 版本特別重要, 因為每個程式庫都預期您的應用程式會在該支援程式庫的最低 API 層級或更高的時間編譯

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要存取 Visual Studio 中的 [目標 Framework] 設定, 請在**方案總管**中開啟專案屬性, 然後選取 [**應用程式**] 頁面:

[![專案屬性的應用程式頁面](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

在 [**使用 Android 版本編譯**] 底下的下拉式功能表中選取 API 層級, 以設定目標 Framework, 如上所示。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要存取在 Visual Studio for Mac 的目標 Framework 設定，以滑鼠右鍵按一下專案名稱，然後選取**選項**; 這會開啟**專案選項**對話方塊。 在此對話方塊中, 流覽至 **[組建] > [一般**], 如下所示:

[![專案選項頁面的組建一般區段](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

在 [**目標 framework** ] 右邊的下拉式功能表中選取 API 層級, 以設定目標架構, 如上所示。

-----


<a name="minimum" />

### <a name="minimum-android-version"></a>Android 最低版本

*最低 android 版本*(也稱為`minSdkVersion`) 是最舊版本的 android OS (也就是最低的 API 層級), 可以安裝和執行您的應用程式。 根據預設, 應用程式只能安裝在符合目標 Framework 設定或更高版本的裝置上;如果 [最低 Android 版本] 設定低於 [目標 Framework] 設定, 您的應用程式也可以在舊版的 android 上執行。 例如, 如果您將 [目標 Framework] 設定為**android 7.1 (Nougat)** , 並將 [最低 android 版本] 設定為 [ **Android 4.0.3 (霜淇淋三明治)** ], 則您的應用程式可以安裝在任何平臺上, 從 api 層級15到 api 層級 25 (含)。

雖然您的應用程式可能會在這範圍的平臺上成功建立並安裝, 但這並不保證它會在所有這些平臺上順利*執行*。 例如, 如果您的應用程式安裝在**android 5.0 (棒糖)** 上, 且您的程式碼呼叫僅適用于**Android 7.1 (Nougat)** 和更新版本的 API, 您的應用程式將會收到執行階段錯誤, 並可能會當機。 因此, 您的程式碼&ndash;必須在&ndash;執行時間確保它只會呼叫其執行所在之 Android 裝置支援的 api。 換句話說, 您的程式碼必須包含明確的執行時間檢查, 以確保您的應用程式只會在最新的裝置上使用較新的 Api 來支援它們。
[Android 版本的執行時間檢查](#runtimechecks), 稍後在本指南中說明如何將這些執行時間檢查新增至您的程式碼。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要存取 Visual Studio 中的 [最低 Android 版本] 設定, 請在**方案總管**中開啟專案屬性, 然後選取 [ **Android 資訊清單**] 頁面。 在 [**最低 android 版本**] 底下的下拉式功能表中, 您可以選取應用程式的最低 android 版本:

[![設定為使用 SDK 版本編譯的最低 Android 至目標選項](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

如果您選取 [**使用 SDK 版本來編譯**], 最低的 Android 版本將會與目標 Framework 設定相同。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要存取 Visual Studio for Mac 中的最低 Android 版本, 請以滑鼠右鍵按一下專案名稱, 然後選取 [**選項**]。這會開啟 [**專案選項**] 對話方塊。 流覽至 [ **Build > Android 應用程式**]。
使用 [**最低 android 版本**] 右邊的下拉式功能表, 您可以設定應用程式的最低 android 版本:

[![設定為自動使用目標 framework 版本的最低 Android 版本](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

如果您選取 **[ &ndash;自動使用目標 framework 版本**], 最低的 Android 版本將會與目標 framework 設定相同。

-----


<a name="target" />

### <a name="target-android-version"></a>以 Android 版本為目標

*目標 android 版本*(也稱為`targetSdkVersion`) 是應用程式預期要執行的 android 裝置的 API 層級。 Android 使用此設定來判斷是否要啟用任何相容性&ndash;行為, 這可確保您的應用程式會以您預期的方式繼續運作。 Android 會使用您應用程式的 [目標 Android 版本] 設定, 來找出哪些行為變更可以套用至您的應用程式而不會中斷 (這是 Android 提供向前相容性的方式)。

目標 Framework 和目標 Android 版本雖然具有非常相似的名稱, 但並不相同。 目標 Framework 設定會將目標 API 層級資訊與 Xamarin 通訊, 以在*編譯時期*使用, 而目標 Android 版本則會將目標 api 層級資訊與 android 通訊, 以在*執行時間*使用 (當應用程式為已在裝置上安裝並執行。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中存取這項設定, 請在**方案總管**中開啟專案屬性, 然後選取 [ **Android 資訊清單**] 頁面。 在 [**目標 android 版本**] 底下的下拉式功能表中, 您可以選取應用程式的目標 android 版本:

[![使用 SDK 版本設定為編譯的目標 Android 版本](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

建議您明確地將目標 Android 版本設定為您用來測試應用程式的最新 Android 版本。 在理想的情況下, 它應該設定為最&ndash;新的 Android SDK 版本, 這可讓您在執行行為變更之前, 先使用新的 api。 對於大部分的開發人員, 我們*不*建議將目標 Android 版本設定為**使用 [使用 SDK 版本編譯**]。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要在 Visual Studio for Mac 中存取這項設定, 請以滑鼠右鍵按一下專案名稱, 然後選取 [**選項**]。這會開啟 [**專案選項**] 對話方塊。 流覽至 [ **Build > Android 應用程式**]。 使用 [**目標 android 版本**] 右邊的下拉式功能表, 您可以設定應用程式的目標 Android 版本:

[![目標 Android 版本設定為自動-使用目標 framework 版本](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

建議您明確地將目標 Android 版本設定為您用來測試應用程式的最新 Android 版本。 在理想的情況下, 它應該設定為最新&ndash;可用的 Android SDK 版本, 這可讓您在執行行為變更之前, 先使用新的 api。 對於大部分的開發人員, 我們不建議將目標 Android 版本設定為**自動使用目標 framework 版本**。

-----

一般來說, 目標 Android 版本應受限於最低 Android 版本和目標 Framework。 即：

**最低 Android 版本 < = 目標 Android 版本 < = 目標 Framework**

如需 SDK 層級的詳細資訊, 請參閱 Android 開發人員[使用-sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html)檔。


<a name="runtimechecks" />

## <a name="runtime-checks-for-android-versions"></a>Android 版本的執行時間檢查

隨著每個新版本的 Android 發行, 架構 API 會更新以提供新的或取代功能。 有少數例外狀況, 舊版 Android 的 API 功能會轉送給較新的 Android 版本, 而不需修改。 因此, 如果您的應用程式是在特定的 Android API 層級上執行, 則通常可以在較新的 Android API 層級上執行, 而不需要修改。 但如果您也想要在舊版 Android 上執行您的應用程式呢？

如果您選取*低於*目標 Framework 設定的最低 Android 版本, 您的應用程式在執行時間可能無法使用某些 api。 不過, 您的應用程式仍可在較舊的裝置上執行, 但功能較少。 對於與您的最低 android 版本設定相對應的 android 平臺上未提供的每個 api, 您的程式碼必須明確`Android.OS.Build.VERSION.SdkInt`檢查屬性的值, 以判斷應用程式執行所在平臺的 API 層級。 如果 API 層級*低於*支援您想要呼叫之 Api 的最低 Android 版本, 則您的程式碼必須找出可正常運作的方式, 而不需要進行此 api 呼叫。

例如, 假設我們想要使用[NotificationBuilder SetCategory](xref:Android.App.Notification.Builder.SetCategory*)方法, 在**Android 5.0 棒糖**(及更新版本) 上執行時將通知分類, 但我們仍想要讓應用程式在舊版的 Android (例如**Android 4.1 軟糖 Bean** (無法`SetCategory`使用的位置)。 參考本指南開頭的 Android 版本表, 我們看到**android 5.0 棒**的組建版本代碼為`Android.OS.BuildVersionCodes.Lollipop`。 若要支援無法使用的舊版`SetCategory` Android, 我們的程式碼可以在執行時間偵測 API 層級, 並`SetCategory`在 api 層級大於或等於棒型組建版本代碼時, 有條件地呼叫:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

在此範例中, 我們的應用程式的目標 Framework 設定為**android 5.0 (Api 層級 21)** , 而其最低 android 版本設定為**ANDROID 4.1 (api 層級 16)** 。 因為`SetCategory`在 api 層級`Android.OS.BuildVersionCodes.Lollipop`和更新版本中都有提供, 此`SetCategory`範例程式碼只會在&ndash;實際可用時呼叫, 而`SetCategory`不會在 API 層級為16、17、18、19或20時嘗試呼叫。 這些舊版 Android 的功能只會縮減為通知未正確排序的範圍 (因為不是依類型分類), 但仍會發佈通知以提醒使用者。 我們的應用程式仍可運作, 但其功能會稍微降低。

一般情況下, 組建版本檢查可協助您的程式碼在執行時間以新方式執行, 而不是以舊的方式進行操作。 例如：

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

沒有快速且簡單的規則, 說明如何在缺少一或多個 Api 的舊版 Android 版本上執行時, 減少或修改應用程式的功能。 在某些情況下 (例如上述`SetCategory`範例中的), 當 API 呼叫無法使用時, 就足以省略它。 不過, 在其他情況下, 當偵測到的時間小於應用程式`Android.OS.Build.VERSION.SdkInt`所需的 API 層級時, 您可能需要執行替代功能來呈現其最佳體驗。

<a name="libraries" />

## <a name="api-levels-and-libraries"></a>API 層級和程式庫

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

當您建立 Xamarin android 程式庫專案 (例如類別庫或系結程式庫) 時, 您可以只設定&ndash;目標 Framework, 而無法使用 android 版本的最小值和目標 android 版本設定。 這是因為沒有 [ **Android 資訊清單**] 頁面:

[![只有 [使用 Android 版本編譯] 選項可供使用](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

當您建立 Xamarin android 程式庫專案時, 沒有**android 應用程式**頁面, 您可以在其中設定最低 android 版本和目標 android 版本&ndash;最低 android 版本和目標 android 版本設定無法使用。
這是因為沒有**組建 > Android 應用程式**頁面:

[![不含最低和目標版本選項的組建一般頁面](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

最低 android 版本和目標 android 版本設定無法使用, 因為產生的程式庫不是獨立應用程式&ndash; , 程式庫可以在任何 Android 版本上執行, 視其封裝的應用程式而定。 您可以指定要如何*編譯*程式庫, 但無法預測該程式庫將在哪一個平臺 API 層級上執行。 記住這一點之後, 使用或建立程式庫時, 應該觀察下列最佳作法:

- **使用 Android 程式庫時**如果您要在應用程式中使用 Android 程式庫, 請務必將應用程式的 [目標 framework] 設定設定為至少比程式庫的 [目標 framework] 設定高的 API 層級。 &ndash;

- **建立 Android 程式庫時**&ndash;如果您要建立可供其他應用程式使用的 Android 程式庫, 請務必將其 [目標 Framework] 設定設定為編譯所需的最低 API 層級。

建議使用這些最佳作法, 以協助防止程式庫嘗試呼叫在執行時間無法使用的 API (這可能會造成應用程式當機) 的情況。 如果您是程式庫開發人員, 您應該致力於將 API 呼叫的使用限制為總 API 介面區的小型和共同建立子集。 這麼做有助於確保您的程式庫可在更廣泛的 Android 版本之間安全地使用。


## <a name="summary"></a>總結

本指南說明如何使用 Android API 層級來管理跨不同 Android 版本的應用程式相容性。 其中提供設定 [Xamarin]*目標 Framework*、[*最低 android 版本*] 及 [*目標 android 版本*] 專案設定的詳細步驟。 其中提供使用 Android SDK 管理員安裝 SDK 套件的指示, 內含如何撰寫程式碼以在執行時間處理不同 API 層級的範例, 並說明如何在建立或使用 Android 程式庫時管理 API 層級。 此外, 它也提供了將 API 層級與 Android 版本號碼 (例如 Android 4.4)、Android 版本名稱 (例如 Kitkat) 和 Xamarin 組建版本代碼相關聯的完整清單。


## <a name="related-links"></a>相關連結

- [Android SDK 安裝](~/android/get-started/installation/android-sdk.md)
- [SDK CLI 工具變更](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [挑選您的 compileSdkVersion、minSdkVersion 和 targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [什麼是 API 層級？](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [Codenames、標記和組建編號](https://source.android.com/source/build-numbers)
