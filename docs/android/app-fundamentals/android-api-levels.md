---
title: 瞭解 Android API 層級
description: Xamarin 有數種 Android API 層級設定，可決定您的應用程式與多個 Android 版本的相容性。 本指南說明這些設定的意義、如何進行設定，以及它們在執行時間對您的應用程式有何影響。
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: bb41a6e4ea477ed0a7932df441678e53a304aa35
ms.sourcegitcommit: 8fa0cb9ccbc107d697aa5b9113a4e5d1e75d6eb9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303098"
---
# <a name="understanding-android-api-levels"></a>了解 Android API 層級

_Xamarin 有數種 Android API 層級設定，可決定您的應用程式與多個 Android 版本的相容性。本指南說明這些設定的意義、如何進行設定，以及它們在執行時間對您的應用程式有何影響。_

## <a name="quick-start"></a>快速入門

Xamarin 會公開三個 Android API 層級專案設定：

- [目標 Framework](#framework) &ndash; 指定要在建立應用程式時使用的架構。 在 *編譯* 時期，Xamarin 會使用此 API 層級。

- [最低 Android 版本](#minimum) &ndash; 指定您想要讓應用程式支援的最舊 Android 版本。 Android 會在 *運行* 時間使用此 API 層級。

- [目標 Android 版本](#target) &ndash; 指定您的應用程式要在其上執行的 Android 版本。 Android 會在 *運行* 時間使用此 API 層級。

在您可以為專案設定 API 層級之前，您必須先安裝該 API 層級的 SDK 平臺元件。 如需下載和安裝 Android SDK 元件的詳細資訊，請參閱 [Android SDK 安裝程式](~/android/get-started/installation/android-sdk.md)。

> [!NOTE]
> 從2020年8月開始，Google Play 主控台需要新的應用程式以 API 層級 29 (Android 10.0) 或更高版本。
自2020年11月起，現有的應用程式需要以 API 層級29或更高版本為目標。 如需詳細資訊，請參閱播放主控台檔中的「建立及設定您的應用程式」中 [的「播放主控台」的目標 API 層級需求](https://support.google.com/googleplay/android-developer/answer/9859152#targetsdk) 。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

一般來說，所有三個 Xamarin 的 Android API 層級都會設定為相同的值。 在 [ **應用程式** ] 頁面上，將 [ **使用 Android 版 (目標 Framework 編譯)** ] 設定為最新的穩定 API 版本 (或至少為具有您所需之所有功能的 Android 版本) 。
在以下螢幕擷取畫面中，目標 Framework 設定為 **Android 7.1 (API 層級 25-Nougat)**：

[![使用 Android 版本進行編譯的目標 Framework 版本預設](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

在 [ **Android 資訊清單** ] 頁面上，使用 [SDK 版本] 將 [最低 android 版本] 設定為 [ **使用編譯** ]，並將目標 android 版本設為與目標 Framework 版本相同的值 (在下列螢幕擷取畫面中，目標 android Framework 會設定為 **android 7.1 (Nougat)**) ：

[![設定為目標 Framework 版本的最小和目標 Android 版本](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

如果您想要維持與舊版 Android 的回溯相容性，請將 **最低 android 版本的目標** 設為您想要讓應用程式支援的最舊 android 版本。  (請注意，API 層級14是 [Google Play 服務和 Firebase 支援](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)所需的最低 api 層級。 ) 下列範例設定支援從 api 層級14到 api 層級25的 Android 版本：

[![使用 API 層級 25 Nougat 進行編譯，最低 Android 版本設定為 API 層級14](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

一般來說，所有三個 Xamarin 的 Android API 層級都會設定為相同的值。 將 [ **目標 framework** ] 設定為最新的穩定 API 版本 (或至少設定為具有您所需之所有功能的 Android 版本) 。 若要設定 **目標架構**，請在 **專案選項** 中流覽至 **[組建 > 一般**]。 在下列螢幕擷取畫面中，目標 Framework 設定為 **使用最新安裝的平臺 (8.0)**：

[![目標 framework 預設為使用最新安裝的平臺](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

您可以在 [在 **專案選項** 中 **組建 > android 應用程式**] 下找到 [最小和目標 android 版本] 設定。 將最低 Android 版本設定為 **自動使用目標 framework 版本** ，並將目標 android 版本設為與目標 framework 版本相同的值。 在以下螢幕擷取畫面中，目標 Android Framework 設定為 **android 8.0 (API 層級 26)** 以符合上述的目標 Framework 設定：

[![在專案選項中設定目標和架構層級](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

如果您想要維持與舊版 Android 的回溯相容性，請將 **最低 android 版本** 變更為您想要讓應用程式支援的最舊 android 版本。 請注意，API 層級14是 [Google Play 服務和 Firebase 支援](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)所需的最低 api 層級。
例如，下列設定支援早于 API 層級14的 Android 版本：

[![最低和目標版本設為自動使用目標 framework 版本](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----

如果您的應用程式支援多個 Android 版本，您的程式碼必須包含執行時間檢查，以確保您的應用程式能使用最低的 Android 版本設定 (請參閱下方 [Android 版本的執行時間檢查](#runtimechecks) ，以取得詳細資料) 。 如果您正在使用或建立程式庫，請參閱以下的 [Api 層級和程式庫](#libraries) ，以取得設定程式庫的 API 層級設定的最佳作法。

## <a name="android-versions-and-api-levels"></a>Android 版本和 API 層級

隨著 Android 平臺的發展和新的 Android 版本發行，每個 Android 版本都會被指派一個唯一的整數識別碼，稱為 *API 層級*。 因此，每個 Android 版本都對應至單一 Android API 層級。 因為使用者會在舊版以及最新的 Android 版本上安裝應用程式，所以實際的 Android 應用程式必須設計為可使用多個 Android API 層級。

### <a name="android-versions"></a>Android 版本

Android 的每個版本都有多個名稱：

- Android 版本，例如 **android 9.0**
- 程式碼 (或甜點) 名稱，例如 _圓形圖_
- 對應的 API 層級，例如 **api 層級 28**

Android 程式碼名稱可能對應至多個版本和 API 層級 (如下表所示) ，但每個 Android 版本只對應至一個 API 層級。

此外，Xamarin 會定義對應至目前已知 Android API 層級的 *組建版本代碼* 。 下表可協助您在 API 層級、Android 版本、程式碼名稱和 Xamarin. Android 組建版本代碼之間轉譯 (組建版本代碼定義于 `Android.OS` 命名空間) 中：

[!include[](~/android/includes/api-levels.md)]

如下表所示，新的 Android 版本通常會在每年有多個版本的情況下發行 &ndash; 。 因此，執行您應用程式的 Android 裝置的 universe 包含各種舊版和較新的 Android 版本。 您要如何保證應用程式能以一致且可靠的方式在許多不同版本的 Android 上執行？ Android 的 API 層級可協助您管理此問題。

### <a name="android-api-levels"></a>Android API 層級

每個 Android 裝置只會在 *一個* api 層級執行， &ndash; 此 api 層級保證每個 android 平臺版本都是唯一的。 API 層級會精確地識別您的應用程式可以呼叫的 API 集版本;它會識別您針對開發人員撰寫程式碼的資訊清單元素、許可權等組合。 Android 的 API 層級系統可協助 Android 在裝置上安裝應用程式之前，判斷應用程式是否與 Android 系統映射相容。

建立應用程式時，它會包含下列 API 層級資訊：

- 建立應用程式以在其上執行的 Android *目標* API 層級。

- Android 裝置執行您的應用程式所需的 *最低* android API 層級。 

這些設定是用來確保在安裝時可在 Android 裝置上正確執行應用程式所需的功能。 如果沒有，應用程式就會被封鎖而無法在該裝置上執行。 例如，如果 Android 裝置的 API 層級低於您為應用程式指定的最小 API 層級，Android 裝置將會防止使用者安裝您的應用程式。

## <a name="project-api-level-settings"></a>專案 API 層級設定

下列各節說明如何使用 SDK 管理員針對您想要作為目標的 API 層級準備您的開發環境，然後詳細說明如何在 Xamarin 中設定 *目標 Framework*、 *最低 Android 版本* 和 *目標 android 版本* 設定。

### <a name="android-sdk-platforms"></a>Android SDK 平臺

您必須先安裝對應于該 API 層級的 Android SDK 平臺版本，才可以在 Xamarin 中選取目標或最小 API 層級。 適用于目標 Framework、最低 Android 版本和目標 Android 版本的可用選項範圍僅限於您已安裝的 Android SDK 版本範圍。 您可以使用 SDK 管理員來確認是否已安裝必要的 Android SDK 版本，並且可以使用它來新增您應用程式所需的任何新 API 層級。 如果您不熟悉如何安裝 API 層級，請參閱 [Android SDK 安裝程式](~/android/get-started/installation/android-sdk.md)。

<a name="framework"></a>

### <a name="target-framework"></a>目標 Framework

*目標 Framework* (也稱為 `compileSdkVersion`) 是 (API 層級的特定 Android Framework 版本，) 您的應用程式會在組建時進行編譯。 這項設定會指定您的應用程式在執行時所 *預期* 使用的 api，但不會影響應用程式在安裝時實際可使用的 api。 因此，變更目標 Framework 設定並不會變更執行時間行為。

目標 Framework 會識別您的應用程式連結的程式庫版本， &ndash; 這項設定會決定您可以在應用程式中使用的 api。 例如，如果您想要使用在 Android 5.0 棒裡引進的 [NotificationBuilder. SetCategory](xref:Android.App.Notification.Builder.SetCategory*) 方法，則必須將目標 Framework 設定為 **API 層級 21 (棒)** 或更新版本。 如果您將專案的目標架構設定為 API 層級（例如 **Api 層級19） (KitKat)** 並嘗試 `SetCategory` 在您的程式碼中呼叫方法，您將會收到編譯錯誤。

建議您一律使用 *最新* 的可用目標 Framework 版本進行編譯。 這樣做可為您的程式碼可能呼叫的任何已淘汰的 Api，提供有用的警告訊息。 當您使用最新的支援程式庫版本時，使用最新的目標 Framework 版本特別重要，因為 &ndash; 每個程式庫都預期您的應用程式會在支援程式庫的最小 API 層級或更高的版本

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要存取 Visual Studio 中的 [目標 Framework] 設定，請在 **方案總管** 中開啟專案屬性，然後選取 [ **應用程式** ] 頁面：

[![專案屬性的應用程式頁面](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

在 [ **使用 Android 版本編譯** ] 下的下拉式功能表中選取 API 層級，以設定目標 Framework，如上所示。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要存取 Visual Studio for Mac 中的 [目標 Framework] 設定，請在專案名稱上按一下滑鼠右鍵，然後選取 [ **選項**]。這會開啟 [ **專案選項** ] 對話方塊。 在此對話方塊中，流覽至 **Build > 一般** ，如下所示：

[![專案選項頁面的 [建立一般] 區段](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

在 [ **目標 framework** ] 右邊的下拉式功能表中選取 API 層級（如上所示），以設定目標 framework。

-----

<a name="minimum"></a>

### <a name="minimum-android-version"></a>最低 Android 版本

*最低 android 版本* (也稱為 `minSdkVersion`) 是最舊版本的 android OS (也就是可安裝及執行應用程式的最低 API 層級) 。 根據預設，應用程式只能安裝在符合目標 Framework 設定或更高版本的裝置上;如果最低 Android 版本 *設定低於目標* Framework 設定，則您的應用程式也可以在舊版 android 上執行。 例如，如果您將目標 Framework 設定為 **android 7.1 (Nougat)** 並將最低 android 版本設定為 **Android 4.0.3 (霜淇淋三明治)**，則您的應用程式可以安裝在 api 層級15到 api 層級25（含）的任何平臺上。

雖然您的應用程式可能成功地在此範圍的平臺上建立並安裝，但這並不保證會在所有平臺上順利 *執行* 。 例如，如果您的應用程式安裝在 **android 5.0 (的棒)** ，而您的程式碼呼叫的 API 僅適用于 **Android 7.1 (Nougat)** 和更新版本，您的應用程式將會收到執行階段錯誤，而且可能會損毀。 因此，您的程式碼必須 &ndash; 在執行時間確保 &ndash; 它只會呼叫其執行所在的 Android 裝置所支援的 api。 換句話說，您的程式碼必須包含明確的執行時間檢查，以確保您的應用程式只在最近足以支援它們的裝置上使用較新的 Api。
本指南稍後[的執行時間檢查 Android 版本](#runtimechecks)，說明如何將這些執行時間檢查新增至您的程式碼。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要存取 Visual Studio 中的 [最低 Android 版本] 設定，請在 **方案總管** 中開啟專案屬性，然後選取 [ **Android 資訊清單** ] 頁面。 在 [ **最小 android 版本** ] 下的下拉式功能表中，您可以選取應用程式的最低 android 版本：

[![使用 SDK 版本進行編譯的最低 Android 至目標選項設定](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

如果您選取 [使用 **以 SDK 版本進行編譯**]，最低 Android 版本會與目標 Framework 設定相同。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要存取 Visual Studio for Mac 中的最低 Android 版本，請以滑鼠右鍵按一下專案名稱，然後選取 [ **選項**]。這會開啟 [ **專案選項** ] 對話方塊。 流覽至 **Build > Android 應用程式**。
使用 **最低 android 版本** 右邊的下拉式功能表，您可以設定應用程式的最低 android 版本：

[![設定為自動使用目標 framework 版本的最低 Android 版本](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

如果您選取 [ **自動 &ndash; 使用目標 framework 版本**]，最小的 Android 版本會與目標 framework 設定相同。

-----

<a name="target"></a>

### <a name="target-android-version"></a>目標 Android 版本

*目標 Android 版本* (也稱為「 `targetSdkVersion`) 」是應用程式預期要執行的 ANDROID 裝置的 API 層級。 Android 會使用此設定來判斷是否要啟用任何相容性行為， &ndash; 如此可確保您的應用程式會以您預期的方式繼續運作。 Android 會使用您應用程式的目標 Android 版本設定，找出哪些行為變更可以套用至您的應用程式，而不需要中斷 (這是 Android 提供向前相容性) 的方式。

目標 Framework 和目標 Android 版本（具有非常類似的名稱）不是相同的做法。 目標 Framework 設定會將目標 API 層級資訊傳達給 Xamarin，以在 *編譯時期* 使用，而目標 android 版本則會將目標 api 層級資訊傳達給 android 以在 *執行時間* 使用， (在裝置上安裝並執行應用程式時) 。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要存取 Visual Studio 中的這項設定，請在 **方案總管** 中開啟專案屬性，然後選取 [ **Android 資訊清單** ] 頁面。 在 [ **目標 android 版本** ] 下的下拉式功能表中，您可以選取應用程式的目標 android 版本：

[![使用 SDK 版本設定為編譯的 Android 版本目標](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

建議您將目標 Android 版本明確設定為您用來測試應用程式的最新 Android 版本。 在理想的情況下，它應該設定為最新的 Android SDK 版本，如此一來， &ndash; 您就可以使用新的 api，然後再進行行為變更。 對於大部分的開發人員， *建議您不要* 將目標 Android 版本設定為 **使用使用 SDK 版本的編譯**。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要存取 Visual Studio for Mac 中的這項設定，請在專案名稱上按一下滑鼠右鍵，然後選取 [ **選項**]。這會開啟 [ **專案選項** ] 對話方塊。 流覽至 **Build > Android 應用程式**。 使用 **目標 android 版本** 右邊的下拉式功能表，您可以為您的應用程式設定目標 android 版本：

[![將 Android 版本設為自動使用的目標 framework 版本](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

建議您將目標 Android 版本明確設定為您用來測試應用程式的最新 Android 版本。 在理想的情況下，它應該設定為最新的可用 Android SDK 版本，如此一來， &ndash; 您就可以使用新的 api，然後再進行行為變更。 對於大部分的開發人員，我們不建議將目標 Android 版本設為 **自動使用的目標 framework 版本**。

-----

一般情況下，目標 Android 版本應受限於最低 Android 版本和目標 Framework。 即：

**最低 Android 版本 <= 目標 Android 版本 <= 目標 Framework**

如需 SDK 層級的詳細資訊，請參閱 Android 開發人員 [使用-sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) 檔。

<a name="runtimechecks"></a>

## <a name="runtime-checks-for-android-versions"></a>Android 版本的執行時間檢查

每個新版本的 Android 發行時，framework API 都會更新，以提供新的或取代的功能。 有幾個例外狀況，舊版 Android 的 API 功能將會在不需要修改的情況下，轉往較新的 Android 版本。 如此一來，如果您的應用程式是在特定的 Android API 層級上執行，則它通常可以在較新的 Android API 層級執行，而不需要修改。 但是如果您也想要在舊版 Android 上執行應用程式，該怎麼辦？

如果您選取 *低於* 目標 Framework 設定的最低 Android 版本，某些 api 可能無法在執行時間提供給您的應用程式使用。 不過，您的應用程式仍可在較舊的裝置上執行，但功能較少。 針對每個在 Android 平臺上無法使用的 API （與您的最低 Android 版本設定相對應），您的程式碼必須明確檢查屬性的值， `Android.OS.Build.VERSION.SdkInt` 以判斷應用程式執行所在平臺的 API 層級。 如果 API 層級 *低於* 支援您要呼叫之 Api 的最低 Android 版本，則您的程式碼必須找出可正常運作的方式，而不需要進行此 api 呼叫。

例如，假設我們想要使用 [NotificationBuilder. SetCategory](xref:Android.App.Notification.Builder.SetCategory*) 方法，在 **android 5.0 棒** (和) 更新版本上執行時分類通知，但我們仍希望應用程式在舊版 android （例如 **android 4.1 Jelly (Bean** ）上執行，而 `SetCategory` 不) 使用。 參考本指南開頭的 Android 版本資料表，我們會看到 **android 5.0 棒棒** 的組建版本代碼是 `Android.OS.BuildVersionCodes.Lollipop` 。 為了支援較舊版本的 Android `SetCategory` （無法使用），我們的程式碼可以在執行時間偵測 api 層級，而且 `SetCategory` 只有在 api 層級大於或等於棒棒的組建版本程式碼時，才會有條件地呼叫：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

在此範例中，我們的應用程式的目標 Framework 設定為 **android 5.0 (API 層級 21)** ，其最低 android 版本會設定為 **ANDROID 4.1 (API 層級 16)**。 因為 `SetCategory` 在 api 層級和更新版本中都有提供 `Android.OS.BuildVersionCodes.Lollipop` ，此範例 `SetCategory` 程式碼只會在實際可用時呼叫，而 &ndash; *not* `SetCategory` 當 API 層級為16、17、18、19或20時，將不會嘗試呼叫此程式碼。 這些較舊的 Android 版本的功能只會在通知未正確排序的範圍內減少 (因為它們並未依類型) 分類，但仍會發佈通知以警示使用者。 我們的應用程式仍可運作，但其功能稍微降低。

一般情況下，組建版本檢查可協助您的程式碼在執行時間決定是否以新的方式進行，而不是以舊的方式進行。 例如：

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

沒有快速且簡單的規則，說明如何在缺少一或多個 Api 的舊版 Android 上執行時，減少或修改應用程式的功能。 在某些情況下 (例如 `SetCategory` 上述範例中的) ，當 API 呼叫無法使用時，就已足夠省略它。 不過，在其他情況下，您可能需要執行替代的功能，當偵測 `Android.OS.Build.VERSION.SdkInt` 到的時機小於應用程式呈現其最佳體驗所需的 API 層級時。

<a name="libraries"></a>

## <a name="api-levels-and-libraries"></a>API 層級和程式庫

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

當您建立 Xamarin. Android 程式庫專案 (例如類別庫或系結程式庫) 時，您只能設定目標 Framework 設定 &ndash; [最低 android 版本] 和 [目標 Android 版本] 設定無法使用。 這是因為沒有 **Android 資訊清單** 頁面：

[![只有 [使用 Android 版本編譯] 選項可用](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

當您建立 Xamarin Android 程式庫專案時，沒有 **Android 應用程式** 頁面，您可以在其中設定最低 android 版本和目標 Android 版本 &ndash; 最低 Android 版本和目標 android 版本設定無法使用。
這是因為沒有任何 **組建 > Android 應用程式** 頁面：

[![沒有最小和目標版本選項的 [建立一般] 頁面](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

因為產生的程式庫不是獨立應用程式，所以無法使用最低 Android 版本和目標 Android 版本設定，因為程式庫不是獨立應用程式，可在 &ndash; 任何 Android 版本上執行，視其所封裝的應用程式而定。 您可以指定如何 *編譯* 程式庫，但無法預測程式庫將在哪個平臺 API 層級上執行。 請記住，當取用或建立程式庫時，應該觀察以下最佳作法：

- **使用 Android 程式庫時** &ndash; 如果您在應用程式中使用 Android 程式庫，請務必將應用程式的目標 Framework 設定設定為至少與程式庫的目標 Framework 設定 *最高* 的 API 層級。

- **建立 Android 程式庫時** &ndash; 如果您要建立其他應用程式所使用的 Android 程式庫，請務必將其目標 Framework 設定設定為所需的最低 API 層級，才能進行編譯。

建議使用這些最佳作法，以協助防止程式庫嘗試呼叫 (執行時間無法使用的 API 時，可能導致應用程式損毀) 的情況。 如果您是程式庫開發人員，您應該努力將 API 呼叫的使用量限制在總 API 介面區的小型且妥善建立的子集。 這麼做有助於確保您的程式庫可在更廣泛的 Android 版本上安全地使用。

## <a name="summary"></a>摘要

本指南說明如何使用 Android API 層級來管理不同 Android 版本之間的應用程式相容性。 它提供了設定 Xamarin. Android *目標 Framework*、 *最低 Android 版本* 和 *目標 android 版本* 專案設定的詳細步驟。 本檔提供使用 Android SDK 管理員安裝 SDK 套件的指示，包含如何在執行時間撰寫程式碼以處理不同 API 層級的範例，並說明如何在建立或使用 Android 程式庫時管理 API 層級。 此外也提供完整的清單，將 API 層級與 Android 版本號碼相關聯 (例如 Android 4.4) 、Android 版本名稱 (例如 Kitkat) ，以及 Xamarin. Android 組建版本代碼。

## <a name="related-links"></a>相關連結

- [Android SDK 安裝](~/android/get-started/installation/android-sdk.md)
- [SDK CLI 工具變更](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [挑選您的 >compilesdkversion、>minsdkversion 和 targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [什麼是 API 層級？](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [Codenames、標記和組建編號](https://source.android.com/source/build-numbers)
