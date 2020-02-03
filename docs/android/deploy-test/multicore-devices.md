---
title: 多核心裝置和 Xamarin.Android
description: Android 可以在數種不同的電腦架構上執行。 本文件會討論可供 Xamarin.Android 應用程式運用的不同 CPU 架構。 本文件也會說明如何封裝 Android 應用程式以支援不同的 CPU 架構。 文中會介紹應用程式二進位介面 (ABI)，並提供在 Xamarin.Android 應用程式中要使用哪些 ABI 的相關指導方針。
ms.prod: xamarin
ms.assetid: D812883C-A14A-E74B-0F72-E50071E96328
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2019
ms.openlocfilehash: e27e73ac2c5164fa3431c8892b21a71c32fcd8ef
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724011"
---
# <a name="multi-core-devices--xamarinandroid"></a>多核心裝置和 Xamarin.Android

_Android 可以在數種不同的電腦架構上執行。本文件會討論可供 Xamarin.Android 應用程式運用的不同 CPU 架構。本文件也會說明如何封裝 Android 應用程式以支援不同的 CPU 架構。文中會介紹應用程式二進位介面 (ABI)，並提供在 Xamarin.Android 應用程式中要使用哪些 ABI 的相關指導方針。_

## <a name="overview"></a>總覽

Android 能夠建立「fat 二進位檔」，這是單一的 `.apk` 檔案，其中包含會支援多個不同 CPU 架構的機器碼。 為了實現這個功能，它會讓機器碼的每一段與應用程式二進位介面  建立關聯。 ABI 可用來控制哪些機器碼會在指定的硬體裝置上執行。 例如，為了讓 Android 應用程式在 x86 裝置上執行，就必須在編譯應用程式時納入 x86 ABI 支援。

具體來說，每個 Android 應用程式都會支援至少一個內嵌應用程式二進位介面  (EABI)。 EABI 是內嵌軟體程式專屬的慣例。 典型的 EABI 會描述下列資訊：

- CPU 指令集。

- 在執行階段儲存和載入之記憶體的位元組序。

- 物件檔案和程式庫的二進位格式，以及這些檔案和程式庫所允許或支援的內容類型。

- 用來在應用程式程式碼和系統之間傳遞資料的不同慣例 (例如：呼叫函式時暫存器和/或堆疊的使用方式、對齊條件約束等)。

- 列舉類型、結構、欄位和陣列的對齊和大小條件約束。

- 可在執行階段供機器碼使用的函式符號清單，通常來自一組非常具體的選定程式庫。

### <a name="armeabi-and-thread-safety"></a>armeabi 和執行緒安全性

下面會詳細討論應用程式二進位介面，但請務必記住，Xamarin.Android 所使用的 `armeabi` 執行階段*不具備執行緒安全性*。 如果具有 `armeabi` 支援的應用程式部署至 `armeabi-v7a` 裝置，將會發生許多奇怪且無法解釋的例外狀況。

由於 Android 4.0.0、4.0.1、4.0.2 和 4.0.3 中的錯誤，即使 `armeabi-v7a` 目錄存在，且該裝置是 `armeabi-v7a` 裝置，系統仍會從 `armeabi` 目錄挑選原生程式庫。

> [!NOTE]
> Xamarin.Android 會確保 `.so` 以正確的順序新增至 APK。 對於 Xamarin.Android 使用者來說，這個錯誤應該不會成為問題。

### <a name="abi-descriptions"></a>ABI 描述

Android 所支援的每個 ABI 皆可透過唯一名稱來加以識別。

#### <a name="armeabi"></a>armeabi

這是 ARM 式 CPU (至少支援 ARMv5TE 指令集) 的 EABI 名稱。 Android 會遵循由小到大的 ARM GNU/Linux ABI。 此 ABI 不支援硬體輔助浮點運算。 所有 FP 作業皆由軟體協助程式函式負責執行，這些函式來自編譯器的 `libgcc.a` 靜態程式庫。 `armeabi` 不支援 SMP 裝置。

> [!IMPORTANT]
> Xamarin.Android 的 `armeabi` 程式碼不具備安全執行緒，所以不應用於具有多 CPU 的 `armeabi-v7a` 裝置 (說明如下)。 在單核心的 `armeabi-v7a` 裝置上使用 `armeabi` 程式碼很安全。

#### <a name="armeabi-v7a"></a>armeabi-v7a

這是另一種 ARM 式 CPU 指令集，其可延伸上述的 `armeabi` EABI。 `armeabi-v7a` EABI 可支援硬體浮點運算和多個 CPU (SMP) 的裝置。 相較於使用 `armeabi` 的應用程式，使用 `armeabi-v7a` EABI 的應用程式效能應該會大幅提升。

> [!NOTE]
> `armeabi-v7a` 機器碼將無法在 ARMv5 裝置上執行。

#### <a name="arm64-v8a"></a>arm64-v8a

這是以 ARMv8 CPU 架構為基礎的 64 位元指令集。 Nexus 9  使用了此架構。
Xamarin.Android 5.1 引進對此架構的支援 (如需詳細資訊，請簪參閱 [64 位元執行階段支援](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#64-bit-runtime-support))。

#### <a name="x86"></a>x86

對於支援一般稱為 x86  或 IA-32  之指令集的 CPU，這是其 ABI 的名稱。 此 ABI 會對應至 Pentium Pro 指令集 (包括 MMX、SSE、SSE2 和 SSE3 指令集) 的指令。 它不會包含任何其他選擇性的 IA-32 指令集擴充，例如：

- MOVBE 指令。
- 補充的 SSE3 擴充 (SSSE3)。
- SSE4 的任何變化。

> [!NOTE]
> Google TV 雖然在 x86 上執行，但並不受 Android 的 NDK 支援。

#### <a name="x86_64"></a>x86_64

對於支援 64 位元 x86 指令集 (也稱為 x64  或 AMD64  ) 的 CPU，這是其 ABI 的名稱。 Xamarin.Android 5.1 引進對此架構的支援 (如需詳細資訊，請簪參閱 [64 位元執行階段支援](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#64-bit-runtime-support))。

#### <a name="apk-file-format"></a>APK 檔案格式

Android 應用程式套件是保存了 Android 應用程式所需之所有程式碼、資產、資源和憑證的檔案格式。 此套件是 `.zip` 檔案，但所使用的副檔名卻是 `.apk`。 展開時，Xamarin.Android 所建立之 `.apk` 的內容如以下螢幕擷取畫面所示：

[![.apk 的內容](multicore-devices-images/00.png)](multicore-devices-images/00.png#lightbox)

`.apk` 檔案內容的簡短描述：

- **AndroidManifest.xml** &ndash; 這是 `AndroidManifest.xml` 檔案，格式為二進位 XML。

- **classes.dex** &ndash; 這包含應用程式程式碼，此程式碼編譯為 `dex` 檔案格式，由 Android 執行階段 VM 使用。

- **resources.arsc** &ndash; 此檔案包含應用程式所有的先行編譯資源。

- **lib** &ndash; 此目錄保存每個 ABI 的編譯程式碼。 它會針對上一節所述的每個 ABI 各包含一個子資料夾。 在上述螢幕擷取畫面中，我們所討論的 `.apk` 同時具有 `armeabi-v7a` 和 `x86` 的原生程式庫。

- **META-INF** &ndash; 此目錄 (如果有的話) 用於儲存簽署資訊、套件與延伸模組組態資料。

- **res** &ndash; 此目錄保存未編譯為 `resources.arsc` 的資源。

> [!NOTE]
> `libmonodroid.so` 檔案是 Xamarin.Android 應用程式所需的所有原生程式庫。

#### <a name="android-device-abi-support"></a>Android 裝置 ABI 支援

每個 Android 裝置最多支援在兩個 ABI 中執行機器碼：

- **「主要」ABI** &ndash; 這會對應到系統映像中使用的機器碼。

- **「次要」ABI** &ndash; 這是系統映像也支援的選擇性 ABI。

例如，典型的 ARMv5TE 裝置只會有 `armeabi` 的主要 ABI，ARMv7 裝置則會指定 `armeabi-v7a` 的主要 ABI 和 `armeabi` 的次要 ABI。 典型的 x86 裝置只會指定 `x86` 的主要 ABI。

### <a name="android-native-library-installation"></a>Android 原生程式庫安裝

在套件安裝期間，`.apk` 內的原生程式庫會解壓縮至應用程式的原生程式庫目錄，一般是 `/data/data/<package-name>/lib`，之後則稱為 `$APP/lib`。

Android 的原生程式庫安裝行為會因為 Android 版本的不同而有很大的變化。

#### <a name="installing-native-libraries-pre-android-40"></a>安裝原生程式庫：Pre-Android 4.0

4\.0 Ice Cream Sandwich 之前的 Android 只會從 `.apk` 內的單一 ABI  解壓縮原生程式庫。 此一時期的 Android 應用程式會先嘗試解壓縮主要 ABI 的所有原生程式庫，如果這樣的程式庫不存在，Android 就會解壓縮次要 ABI 的所有原生程式庫。 完全不會進行「合併」。

例如，請設想應用程式是安裝在 `armeabi-v7a` 裝置上的情況。 同時支援 `armeabi` 和 `armeabi-v7a` 的 `.apk,` 之中具有下列 ABI `lib` 目錄和檔案：

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

安裝完成後，原生程式庫目錄將會包含：

```shell
$APP/lib/libtwo.so # from the armeabi-v7a directory in the apk
```

換句話說，未安裝任何 `libone.so`。 這會造成問題，因為 `libone.so` 不存在，因此應用程式無法在執行階段載入。 這並非預期的行為，所以已記錄為錯誤，並重新分類為「[如預期般運作](https://code.google.com/p/android/issues/detail?id=9089)」。

因此，以 4.0 之前的 Android 版本作為目標時，就必須為應用程式會支援的每個  ABI 提供所有  原生程式庫，也就是 `.apk` 應包含：

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libone.so
lib/armeabi-v7a/libtwo.so
```

#### <a name="installing-native-libraries-android-40-ndash-android-403"></a>安裝原生程式庫：Android 4.0 &ndash; Android 4.0.3

Android 4.0 Ice Cream Sandwich 變更了解壓縮邏輯。 它會列舉所有原生程式庫，看看檔案的主檔名是否已解壓縮，如果下列兩個條件皆符合，則會將媒體櫃解壓縮：

- 其尚未解壓縮。

- 原生程式庫的 ABI 符合目標的主要或次要 ABI。

符合這些條件將會允許「合併」行為；也就是說，如果我們的 `.apk` 具有下列內容：

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

則安裝完成後，原生程式庫目錄將會包含：

```shell
$APP/lib/libone.so
$APP/lib/libtwo.so
```

很不幸地，此行為會依順序而有所不同，如以下文件所示 - [Issue 24321:Galaxy Nexus 4.0.2 uses armeabi native code when both armeabi and armeabi-v7a is included in apk](https://code.google.com/p/android/issues/detail?id=25321) (問題 24321：Galaxy Nexus 4.0.2 會在 apk 內同時含有 armeabi 和 armeabi-v7a 時使用 armeabi 機器碼)。

原生程式庫會「按照順序」(例如，依 unzip 所列) 來處理，而且會解壓縮第一個相符項目  。 因為 `.apk` 包含 `libtwo.so` 的 `armeabi` 和 `armeabi-v7a` 版本，而 `armeabi` 最先列出，所以會解壓縮 `armeabi` 版本，而不是  `armeabi-v7a` 版本：

```shell
$APP/lib/libone.so # armeabi
$APP/lib/libtwo.so # armeabi, NOT armeabi-v7a!
```

此外，即使同時指定了 `armeabi` 和 `armeabi-v7a` ABI (如下面的＜宣告支援的 ABI＞  一節所述)，Xamarin.Android 還是會於 中建立下列項目。
`csproj`:

```xml
<AndroidSupportedAbis>armeabi,armeabi-v7a</AndroidSupportedAbis>
```

因此，會先在 `.apk` 中找到 `armeabi` `libmonodroid.so`，且會解壓縮 `armeabi` `libmonodroid.so`，即使 `armeabi-v7a` `libmonodroid.so` 存在並已針對目標最佳化也是一樣。 這也會導致令人費解的執行階段錯誤，因為 `armeabi` 不具備 SMP 安全性。

##### <a name="installing-native-libraries-android-404-and-later"></a>安裝原生程式庫：Android 4.0.4 和更新版本

Android 4.0.4 變更了解壓縮邏輯：它會列舉所有原生程式庫，讀取檔案的主檔名，然後解壓縮主要 ABI 版本 (如果有的話) 或次要 ABI (如果有的話)。 這允許「合併」行為；也就是說，如果我們的 `.apk` 具有下列內容：

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

則安裝完成後，原生程式庫目錄將會包含：

```shell
$APP/lib/libone.so # from armeabi
$APP/lib/libtwo.so # from armeabi-v7a
```

### <a name="xamarinandroid-and-abis"></a>Xamarin.Android 和 ABI

Xamarin.Android 支援下列 64 位元  架構：

- `arm64-v8a`
- `x86_64`

> [!NOTE]
> 從 2018 年 8 月開始，新應用程式都必須以 API 層級 26 為目標，而且從 2019 年 8 月開始，除了 32 位元版本之外，應用程式也都[必須提供 64 位元版本](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html)。

Xamarin.Android 支援下列 32 位元架構：

- `armeabi` ^
- `armeabi-v7a`
- `x86`

> [!NOTE]
> **^** 從 [Xamarin.Android 9.2](https://docs.microsoft.com/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture) 開始，已不再支援 `armeabi`。

Xamarin.Android 目前未提供 `mips` 的支援。

### <a name="declaring-supported-abis"></a>宣告支援的 ABI

根據預設，Xamarin.Android 會預設為 `armeabi-v7a` (若為**發行**組建)，以及預設為 `armeabi-v7a` 和 `x86` (若為**偵錯**組建)。 透過 Xamarin.Android 專案的專案選項即可設定不同 ABI 的支援。 在 Visual Studio 中，可於專案 [屬性]  之 [Android 選項]  頁面中的 [進階]  索引標籤底下設定此項目，如下列螢幕擷取畫面所示：

![[Android 選項] 的 [進階屬性]](multicore-devices-images/vs-abi-selections.png)

在 Visual Studio for Mac 中，可於 [專案選項]  之 [Android 組建]  頁面的 [進階]  索引標籤底下選取所支援的架構，如下列螢幕擷取畫面所示：

[![Android 組建所支援的 ABI](multicore-devices-images/xs-abi-selections-sml.png)](multicore-devices-images/xs-abi-selections.png#lightbox)

有時候您可能必須宣告其他 ABI 支援，例如在下列情況時：

- 將應用程式部署至 `x86` 裝置。

- 將應用程式部署至 `armeabi-v7a` 裝置，以確保執行緒安全性。

## <a name="summary"></a>總結

本文件討論了可作為 Android 應用程式執行所在的不同 CPU 架構。 文中介紹了應用程式二進位介面，以及 Android 如何使用此介面來支援不同的 CPU 架構。
接著討論如何在 Xamarin.Android 應用程式中指定 ABI 支援，並點出在僅供 `armeabi` 使用的 `armeabi-v7a` 裝置上使用 Xamarin.Android 應用程式時會引發的問題。

## <a name="related-links"></a>相關連結

- [Android NDK](https://developer.android.com/tools/sdk/ndk/index.html)
- [問題 9089：Nexus One - 如果 armeabi-v7a 上有至少一個文件庫，就不會從 armeabi 載入任何原生程式庫](https://code.google.com/p/android/issues/detail?id=9089)
- [Issue 24321:Galaxy Nexus 4.0.2 uses armeabi native code when both armeabi and armeabi-v7a is included in apk](https://code.google.com/p/android/issues/detail?id=25321) (問題 24321：Galaxy Nexus 4.0.2 會在 apk 內同時含有 armeabi 和 armeabi-v7a 時使用 armeabi 機器碼)
