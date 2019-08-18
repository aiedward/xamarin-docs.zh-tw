---
title: CPU 架構
description: Xamarin 支援數種 CPU 架構, 包括32位和64位的裝置。 本文說明如何將應用程式的目標設為一或多個支援 Android 的 CPU 架構。
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2019
ms.openlocfilehash: 16e805488969aadb0d0b8aa5c892248b7fa403c9
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521215"
---
# <a name="cpu-architectures"></a>CPU 架構

_Xamarin 支援數種 CPU 架構, 包括32位和64位的裝置。本文說明如何將應用程式的目標設為一或多個支援 Android 的 CPU 架構。_

## <a name="cpu-architectures-overview"></a>CPU 架構總覽

當您準備要發行的應用程式時, 您必須指定應用程式支援的平臺 CPU 架構。 單一 APK 可以包含機器碼，以支援多個不同的架構。 架構特定程式碼的每個集合都與*應用程式二進位介面*(ABI) 相關聯。 每個 ABI 都會定義此機器程式碼在執行時間應該如何與 Android 互動。
如需其運作方式的詳細資訊, 請參閱[多核心&amp;裝置 Xamarin. Android](~/android/deploy-test/multicore-devices.md)。


## <a name="how-to-specify-supported-architectures"></a>如何指定支援的架構

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

一般而言, 當您的應用程式設定為 [**發行**] 時, 您可以明確地選取架構 (或架構)。 當您的應用程式設定為進行**Debug**時, 會啟用 [**使用共用運行**時間] 和 [**使用快速部署**] 選項, 以停用明確的架構選擇。

在 Visual Studio 中, 以滑鼠右鍵按一下**方案總管**底下的專案, 然後選取 [**屬性**]。 在 [ **Android 選項**] 頁面底下, 核取 [**封裝屬性**] 區段, 並確認 [**使用共用運行**時間] 已停用 (關閉此功能可讓您明確選取要支援的 abi)。 按一下 [ **Advanced** ] 按鈕, 然後在 [**支援的架構**] 底下, 檢查您要支援的架構:

[![選取 armeabi 和 armeabi-armeabi-v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

一般而言, 當您的應用程式設定為 [**發行**] 時, 您可以明確地選取架構 (或架構)。 當您的應用程式設定為進行**Debug**時, 會啟用 [**使用共用 Mono 運行**時間] 和 [**快速元件部署**] 選項, 以避免明確選取架構。

在 [Visual Studio for Mac] 的 [ **Solution** pad] 中找出您的專案, 按一下專案旁的齒輪圖示, 然後選取 [**選項**]。 在 [**專案選項**] 對話方塊中, 按一下 [ **Android 組建**]。 按一下 [**一般**] 索引標籤, 並確認已停**用 [使用共用 Mono 運行**時間] (關閉此功能可讓您明確選取要支援的 abi)。 按一下 [ **Advanced** ] 索引標籤, 然後在 [**支援的 abi**] 底下, 檢查您要支援之架構的 abi:

[![選取 armeabi 和 armeabi-armeabi-v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----


Xamarin.Android 支援下列架構：

- **armeabi**&ndash;以 ARM 為基礎的 cpu, 至少支援 ARMv5TE 指令集。 請注意`armeabi` , 不是安全線程, 不應用於多 CPU 裝置。

> [!NOTE]
> 從[Xamarin. Android 9.2](https://docs.microsoft.com/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture), `armeabi`已不再支援。

- **armeabi-armeabi-v7a**&ndash;以 ARM 為基礎的 cpu, 包含硬體浮點運算和多個 CPU (SMP) 裝置。 請注意`armeabi-v7a` , 機器程式碼不會在 ARMv5 裝置上執行。

- **arm64-arm64-v8a**&ndash;以64位 ARMv8 架構為基礎的 cpu。

- **x86**&ndash;支援 x86 (或 IA-32) 指令集的 cpu。 這個指令集相當於 Pentium Pro 的, 包括 MMX、SSE、SSE2 和 SSE3 指令。

- **x86_64**支援64位 x86 (也稱為*x64*和*AMD64*) 指令集的 cpu。

針對發行組建, 會`armeabi-v7a`預設為。 此設定提供`armeabi`的效能明顯優於。 如果您的目標是64位 ARM 平臺 (如 [第9個]), 請`arm64-v8a`選取 []。 如果您要將應用程式部署至 x86 裝置, 請`x86`選取 []。 如果目標 x86 裝置使用64位 CPU 架構, 請選取`x86_64`。

## <a name="targeting-multiple-platforms"></a>以多個平臺為目標

若要以多個 CPU 架構為目標, 您可以選取一個以上的 ABI (代價是較大的 APK 檔案大小)。 您可以使用 [**每個選取的 ABI 產生一個套件 (. apk)** ] 選項 (如[設定封裝屬性](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)中所述), 為每個支援的架構建立個別的 apk。

您不需要選取 [ **arm64-arm64-v8a** ] 或 [ **x86_64** ], 以64位裝置為目標;在64位硬體上執行您的應用程式時, 不需要64位支援。 例如, 64 位 ARM 裝置 (例如 [[結點 9](http://www.google.com/nexus/9/)]) 可以執行針對所設定的`armeabi-v7a`應用程式。 啟用64位支援的主要優點, 是讓您的應用程式可以處理更多的記憶體。

> [!NOTE]
> 從 2018 年 8 月開始，新應用程式都必須以 API 層級 26 為目標，而且從 2019 年 8 月開始，除了 32 位元版本之外，應用程式也都[必須提供 64 位元版本](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html)。

## <a name="additional-information"></a>其他資訊

在某些情況下, 您可能需要為每個架構建立不同的 APK (以縮減 APK 的大小, 或因為您的應用程式具有特定 CPU 架構專屬的共用程式庫)。
如需此方法的詳細資訊, 請參閱[建立 ABI 特定的 apk](~/android/deploy-test/building-apps/abi-specific-apks.md)。
