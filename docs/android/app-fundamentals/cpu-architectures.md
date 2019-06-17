---
title: CPU 架構
description: Xamarin.Android 支援數種 CPU 架構，包括 32 位元和 64 位元的裝置。 這篇文章說明如何設定目標應用程式以一或多個 Android 支援的 CPU 架構。
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2019
ms.openlocfilehash: 46e628700771864c6a4b99edea550af694bf3a62
ms.sourcegitcommit: dd73477b1bccbd7ca45c1fb4e794da6b36ca163d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394687"
---
# <a name="cpu-architectures"></a>CPU 架構

_Xamarin.Android 支援數種 CPU 架構，包括 32 位元和 64 位元的裝置。這篇文章說明如何設定目標應用程式以一或多個 Android 支援的 CPU 架構。_

## <a name="cpu-architectures-overview"></a>CPU 架構概觀

當您準備發行您的應用程式時，您必須指定哪一個平台 CPU 架構應用程式支援。 單一 APK 可以包含機器碼，以支援多個不同的架構。 架構特有的程式碼的每個集合相關聯*應用程式二進位介面*(ABI)。 每個 ABI 會定義此機器的程式碼預期在執行階段與 Android 互動的方式。
如需其運作方式的詳細資訊，請參閱[多核心裝置&amp;Xamarin.Android](~/android/deploy-test/multicore-devices.md)。


## <a name="how-to-specify-supported-architectures"></a>如何指定支援的架構

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

一般而言，您明確地選取架構 （或架構） 當您的應用程式已針對**發行**。 當您的應用程式已針對**偵錯**，則**使用共用執行階段**並**使用 Fast Deployment**已啟用選項，其停用明確的架構的選取項目。

在 Visual Studio 中，以滑鼠右鍵按一下您的專案之下**方案總管**，然後選取**屬性**。 底下**Android 選項**頁面上，檢查**封裝屬性**區段，然後確認**使用共用執行階段**已停用 （關閉此功能可讓您明確選取要支援哪些 Abi）。 按一下 **進階** 按鈕和 **支援的架構**，檢查您想要支援的架構：

[![選取 armeabi 和 armeabi-v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

一般而言，您明確地選取架構 （或架構） 當您的應用程式已針對**發行**。 當您的應用程式已針對**偵錯**，則**使用共用 Mono 執行階段**並**快速部署組件**已啟用選項，這可以防止明確的架構在選取的項目。

在 Visual Studio for Mac 中，找出您的專案中**解決方案**填補中，按一下您的專案旁邊的齒輪圖示，然後選取**選項**。 在 [**專案選項**] 對話方塊中，按一下**Android 建置**。 按一下 **一般**索引標籤，然後確認**使用共用 Mono 執行階段**已停用 （關閉此功能可讓您明確選取要支援哪些 Abi）。 按一下 **進階**索引標籤和下方**支援的 abi >** ，檢查您想要支援的架構的 Abi:

[![選取 armeabi 和 armeabi-v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----


Xamarin.Android 支援下列架構：

-   **armeabi** &ndash;至少支援 ARMv5TE 指令集的 ARM 式 Cpu。 請注意，`armeabi`不具備執行緒安全，而且不應該在多重 CPU 的裝置上使用。

> [!NOTE]
> 至於[Xamarin.Android 9.2](https://docs.microsoft.com/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture)，`armeabi`不受支援。

-   **armeabi-v7a** &ndash;以 ARM 為基礎的 Cpu 使用硬體浮點運算和多個 CPU (SMP) 裝置。 請注意，`armeabi-v7a`機器的程式碼不會在 ARMv5 裝置上。

-   **arm64-v8a** &ndash; 64 位元 ARMv8 架構為基礎的 Cpu。

-   **x86** &ndash;支援 x86 （或 IA-32） 的 Cpu 指令集。 此指令集就相當於的 Pentium Pro、 包括 MMX SSE、 mmx、sse、sse2 和 SSE3 的指示。

-   **x86_64**支援 64 位元 x86 的 Cpu (也稱為*x64*並*AMD64*) 指令集。

Xamarin.Android 會預設為`armeabi-v7a`for**發行**組建。 此設定會提供效能明顯優於`armeabi`。 如果您的目標 （例如 Nexus 9) 的 64 位元 ARM 平台，選取  `arm64-v8a`。 如果您要部署您的應用程式到 x86 裝置，選取`x86`。 如果目標 x86 裝置會使用 64 位元 CPU 架構，選取  `x86_64`。

## <a name="targeting-multiple-platforms"></a>多個平台為目標

若要以多個 CPU 架構為目標，您可以選取一個以上的 ABI （代價是較大的 APK 檔案大小）。 您可以使用**產生一個套件 (.apk)，每個所選的 ABI**選項 (中所述[設定封裝屬性](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) 以建立個別的 APK，每個支援的架構。

您沒有選取**arm64-v8a**或是**x86_64** 64 位元裝置; 為目標不需要 64 位元支援在 64 位元硬體上執行您的應用程式。 比方說，64 位元 ARM 裝置 (例如[Nexus 9](http://www.google.com/nexus/9/)) 可以執行應用程式設定為`armeabi-v7a`。 啟用 64 位元支援的主要優點是，讓您的應用程式，以解決更多的記憶體。

> [!NOTE]
> 從 2018 年 8 月開始，新應用程式都必須以 API 層級 26 為目標，而且從 2019 年 8 月開始，除了 32 位元版本之外，應用程式也都[必須提供 64 位元版本](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html)。

## <a name="additional-information"></a>其他資訊

在某些情況下，您可能需要建立個別的 APK，每個架構 (若要減少您的 APK 的大小，或因為您的應用程式共用程式庫，專屬於特定的 CPU 架構)。
如需有關這種方法的詳細資訊，請參閱[建置 ABI 特定 Apk](~/android/deploy-test/building-apps/abi-specific-apks.md)。
