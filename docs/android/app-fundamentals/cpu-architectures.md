---
title: CPU 架構
description: Xamarin 支援數個 CPU 架構，包括32位和64位裝置。 本文說明如何將應用程式的目標設為一或多個 Android 支援的 CPU 架構。
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2019
ms.openlocfilehash: 40cbc8b18c9f0bd177d2c8829aa0eac72fc6e2b8
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456180"
---
# <a name="cpu-architectures"></a>CPU 架構

_Xamarin 支援數個 CPU 架構，包括32位和64位裝置。本文說明如何將應用程式的目標設為一或多個 Android 支援的 CPU 架構。_

## <a name="cpu-architectures-overview"></a>CPU 架構總覽

當您準備要發行的應用程式時，您必須指定應用程式支援的平臺 CPU 架構。 單一 APK 可以包含機器碼，以支援多個不同的架構。 架構特定程式碼的每個集合都與 *應用程式二進位介面* 相關聯， (ABI) 。 每個 ABI 都會定義此電腦程式代碼在執行時間如何與 Android 互動。
如需其運作方式的詳細資訊，請參閱 [ &amp; Xamarin 的多重核心裝置](~/android/deploy-test/multicore-devices.md)。

## <a name="how-to-specify-supported-architectures"></a>如何指定支援的架構

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

一般而言，當您的應用程式設定為 **發行**時，您可以明確地選取架構 (或架構) 。 當您的應用程式設定為要進行 **調試**程式時，會啟用 [ **使用共用運行** 時間] 和 [ **使用快速部署** ] 選項，以停用明確的架構選取。

在 Visual Studio 中，以滑鼠右鍵按一下 [ **方案總管** ] 底下的專案，然後選取 [ **屬性**]。 在 [ **Android 選項** ] 頁面上，核取 [ **封裝屬性** ] 區段，並確認 [ **使用共用運行** 時間] 已停用 (關閉此功能可讓您明確地選取要支援) 的 abi。 按一下 [ **Advanced** ] 按鈕，然後在 [ **支援的架構**] 下，檢查您想要支援的架構：

[![選取 armeabi 和 armeabi->armeabi-v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

一般而言，當您的應用程式設定為 **發行**時，您可以明確地選取架構 (或架構) 。 當您的應用程式設定為要進行 **調試**程式時，會啟用 [ **使用共用 Mono 運行** 時間] 和 [ **快速元件部署** ] 選項，以防止明確地選取架構。

在 [Visual Studio for Mac] 中，在 [ **Solution** pad] 中找出您的專案，按一下專案旁邊的齒輪圖示，然後選取 [ **選項**]。 在 [ **專案選項** ] 對話方塊中，按一下 [ **Android 組建**]。 按一下 [ **一般** ] 索引標籤，並確認已停 **用 [使用共用 Mono 運行** 時間] (關閉此功能可讓您明確地選取要支援) 的 abi。 按一下 [ **Advanced** ] 索引標籤，並在 [ **支援的 abi**] 底下，檢查您想要支援的架構 abi：

[![選取 armeabi 和 armeabi->armeabi-v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----

Xamarin.Android 支援下列架構：

- **armeabi** &ndash; 支援至少 ARMv5TE 指令集的 ARM 型 Cpu。 請注意，不 `armeabi` 是安全線程，不應該在多 CPU 裝置上使用。

> [!NOTE]
>  從 [Xamarin.Android 9.2](/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture) 開始，已不再支援 `armeabi`。

- **armeabi->armeabi-v7a** &ndash; 以 ARM 為基礎的 Cpu，具有硬體浮點運算和多個 CPU (SMP) 裝置。 請注意， `armeabi-v7a` 電腦程式代碼不會在 ARMv5 裝置上執行。

- **arm64-arm64-v8a** &ndash; 以64位 ARMv8 架構為基礎的 Cpu。

- **x86** &ndash; 支援 x86 (或 IA-32) 指令集的 Cpu。 此指令集相當於 Pentium Pro 的設定，包括 MMX、SSE、SSE2 和 SSE3 指令。

- **x86_64** 支援64位 x86 (也稱為   *x64* 和 *AMD64*) 指令集的 cpu。

`armeabi-v7a`針對**發行**組建，Xamarin 預設為。 這種設定提供的效能明顯優於 `armeabi` 。 如果您的目標是64位 ARM 平臺 (例如) 的 [結點 9]，請選取 [] `arm64-v8a` 。 如果您要將應用程式部署至 x86 裝置，請選取 [] `x86` 。 如果目標 x86 裝置使用64位的 CPU 架構，請選取 [] `x86_64` 。

## <a name="targeting-multiple-platforms"></a>以多個平臺為目標

若要以多個 CPU 架構為目標，您可以選取一個以上的 ABI (，但代價是) 較大的 APK 檔案大小。 您可以使用 [ **產生單一套件 (. apk) 每個所選的 ABI** 選項 (在 [ [設定封裝屬性](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) 中說明，為每個支援的架構建立個別的 apk。

您不需要選取 **arm64-arm64-v8a** 或 **x86_64** 以64位裝置為目標;在64位硬體上執行您的應用程式時，不需要64位支援。 例如，64位 ARM 裝置 (例如 [，) 的](https://www.google.com/nexus/9/) ARM 裝置可以執行針對設定的應用程式 `armeabi-v7a` 。 啟用64位支援的主要優點是讓您的應用程式可以處理更多的記憶體。

> [!NOTE]
> 從 2018 年 8 月開始，新應用程式都必須以 API 層級 26 為目標，而且從 2019 年 8 月開始，除了 32 位元版本之外，應用程式也都[必須提供 64 位元版本](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html)。

## <a name="additional-information"></a>其他資訊

在某些情況下，您可能需要為每個 (架構建立個別的 APK，以縮減 APK 的大小，或因為您的應用程式具有特定 CPU 架構) 專屬的共用程式庫。
如需此方法的詳細資訊，請參閱 [建立 ABI 專屬 apk](~/android/deploy-test/building-apps/abi-specific-apks.md)。