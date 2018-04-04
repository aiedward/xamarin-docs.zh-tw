---
title: CPU 架構
description: Xamarin.Android 支援數個 CPU 架構，包括 32 位元和 64 位元的裝置。 本文說明如何為目標的應用程式至一或多個的 Android 支援的 CPU 架構。
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: dea5aaa16891893f649d5ec56f3e6b1ee9a18683
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="cpu-architectures"></a>CPU 架構

_Xamarin.Android 支援數個 CPU 架構，包括 32 位元和 64 位元的裝置。本文說明如何為目標的應用程式至一或多個的 Android 支援的 CPU 架構。_

## <a name="cpu-architectures-overview"></a>CPU 架構概觀

當您準備您的應用程式版本時，您必須指定哪一個平台 CPU 架構應用程式支援。 單一 APK 可以包含機器碼，以支援多個不同的架構。 架構專屬的程式碼的每個集合聯*應用程式二進位介面*(ABI)。 每個 ABI 定義這個電腦程式碼預期在執行階段與 Android 互動的方式。
如需有關其運作方式的詳細資訊，請參閱[多核心裝置&amp;Xamarin.Android](~/android/deploy-test/multicore-devices.md)。


## <a name="how-to-specify-supported-architectures"></a>如何指定支援的架構

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

一般而言，您明確地選取架構 （或架構） 當您的應用程式時設定為**發行**。 當您的應用程式時設定為**偵錯**、**使用共用的執行階段**和**使用快速部署**已啟用選項，從而停用明確的架構選項。

在 Visual Studio 中，按兩下**屬性**您專案下**方案總管 中**選取**Android 選項**頁面。 按一下**封裝**索引標籤上，確認**使用共用的執行階段**已停用 （關閉此功能可讓您明確地選取 支援哪些 ABIs）。 按一下**進階** 索引標籤上和下**進階屬性**，檢查您想要支援的架構：

[![選取 armeabi 和 armeabi v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

一般而言，您明確地選取架構 （或架構） 當您的應用程式時設定為**發行**。 當您的應用程式時設定為**偵錯**、**使用共用單聲道的執行階段**和**快速組件部署**已啟用選項，這可以防止明確架構選取項目。

在 Visual Studio for Mac，找出您的專案中**方案**填補中，按一下您專案旁邊的齒輪圖示，然後選取**選項**。 在**專案選項**] 對話方塊中，按一下 [ **Android 建置**。 按一下**一般**索引標籤上，確認**使用共用單聲道的執行階段**已停用 （關閉此功能可讓您明確地選取 支援哪些 ABIs）。 按一下**進階** 索引標籤上和下**支援 ABIs**，檢查您想要支援的架構 ABIs:

[![選取 armeabi 和 armeabi v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----


Xamarin.Android 支援下列架構：

-   **armeabi** &ndash;至少 ARMv5TE 指令集支援的 arm Cpu。 請注意，`armeabi`不具備執行緒安全，而且不應該在多重 CPU 裝置上使用。

-   **armeabi v7a** &ndash; arm Cpu 硬體浮點運算與多個 CPU (SMP) 裝置。 請注意， `armeabi-v7a` ARMv5 裝置將不會執行機器碼。

-   **arm64 v8a** &ndash;以 64 位元 ARMv8 架構為基礎的 Cpu。

-   **x86** &ndash;支援 x86 （或 ia-32） 的 Cpu 指令集。 此指令集就相當於，Pentium Pro、 包括 MMX、 SSE、 SSE2 和 SSE3 指示。

-   **x86_64**支援 64 位元 x86 的 Cpu (也稱為*x64*和*AMD64*) 指令集。

Xamarin.Android 預設為`armeabi-v7a`如**發行**建置。 此設定會提供效能明顯優於`armeabi`。 如果您的目標 64 位元 ARM 平台 （例如 Nexus 9)，選取`arm64-v8a`。 如果您會將您的應用程式部署到 x86 裝置，選取`x86`。 目標 x86 裝置，如果使用 64 位元 CPU 架構選取`x86_64`。

## <a name="targeting-multiple-platforms"></a>針對多個平台

若要目標多個 CPU 架構，您可以選取一個以上的 ABI （但會犧牲較大的 APK 檔案大小）。 您可以使用**產生一個套件 (.apk)，每個選取的 ABI**選項 (述[設定封裝屬性](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) 建立個別的 APK 每一個受支援架構。

您沒有選取**arm64 v8a**或**x86_64**為目標的 64 位元裝置; 在 64 位元硬體上執行您的應用程式不需要 64 位元支援。 例如，64 位元 ARM 裝置 (例如[Nexus 9](http://www.google.com/nexus/9/)) 可以執行應用程式設定為`armeabi-v7a`。 64 位元支援的主要優點是可讓您的應用程式處理更多記憶體。

> [!NOTE]
> 實驗性功能的目前 64 位元執行階段支援。 請記住，64 位元執行階段會*不*64 位元裝置上執行您的應用程式所需。 

## <a name="additional-information"></a>其他資訊

在某些情況下，您可能需要建立每個架構的不同 APK (若要減少您 APK 大小，或因為您的應用程式共用具有專屬於特定的 CPU 架構的程式庫)。
如需這種方法的詳細資訊，請參閱[建置 ABI 特定 APKs](~/android/deploy-test/building-apps/abi-specific-apks.md)。
