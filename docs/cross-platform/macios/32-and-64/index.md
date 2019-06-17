---
title: 32/64 位元平台考量
description: 本文件說明要牢記在心，以 Xamarin.iOS 或 Xamarin.Mac 應用程式的 32 位元和 64 位元架構為目標時的各種考量。
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 31eb0bfae58ecdca40548e46d1d9d95828be67b4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347807"
---
# <a name="3264-bit-platform-considerations"></a>32/64 位元平台考量

雖然 iOS 和 macOS 已在過去支援 32 和 64 位元應用程式，Apple 已逐漸取代 32 位元支援。

從 iOS 11，開始將無法再啟動 32 位元應用程式，並[所有提交至 App Store 必須都支援 64 位元](https://developer.apple.com/news/?id=06282017b)。

在 2018 年 1 月，從開始[提交至 Mac App Store 的新應用程式必須支援 64 位元](https://developer.apple.com/news/?id=06282017a)，與現有的應用程式必須由 2018 年 6 月更新。

Xamarin 的傳統 API (`XamMac.dll`和`monotouch.dll`) 支援僅限 32 位元應用程式。 不過，新 Xamarin.iOS 和 Xamarin.Mac 的應用程式使用[統一 API](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS`和`Xamarin.Mac`) 根據預設，因此也 32 和 64 位元，視需要的目標。

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>啟用 64 位元組建的 Xamarin.iOS 應用程式

> [!WARNING]
> 基於歷史原因，並協助將較舊的 Xamarin.iOS 專案移至統一 API 和支援 64 位元包含這一節。 所有新的 Xamarin.iOS 專案預設會使用統一的 API 和目標 64 位元。

Xamarin.iOS 行動應用程式的已轉換至統一的 API，開發人員必須手動更新的組建設定為目標的 64 位元：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在  **Solution Pad**，連按兩下 應用程式的專案，以開啟**專案選項**視窗。
2. 選取  **iOS 組建**。
3. 適用於 iPhone 模擬器中，在**支援的架構**下拉式清單中，選取**x86\_64**或是**i386 + x86\_64**:

   [![支援架構設為 x86\_64 或 i386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. 針對實體裝置，選取其中一個可用**ARM64**組合：

   [![將支援的架構設定為其中一個 ARM64 組合](Images/Image02.png "設定支援的架構，以其中一個 ARM64 組合")](Images/Image02-large.png#lightbox)

5. 按一下 [確定] 。
6. 執行乾淨的組建。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管**，以滑鼠右鍵按一下 應用程式的專案，然後選取**屬性**。
2. 選取  **iOS 組建**。
3. 適用於 iPhone 模擬器中，設定**支援的架構**設為**x86\_64**或是**i386 + x86\_64**: 

   [![將支援的架構設定為 x86_64 或 i386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. 針對實體裝置，選取其中一個可用**ARM64**組合：
    
   [![將支援的架構設定為其中一個 ARM64 組合](Images/VS01.png "設定支援的架構，以其中一個 ARM64 組合")](Images/VS01-large.png#lightbox)

5. 儲存您的變更。
6. 執行乾淨的組建。

-----

只有在 iPhone 5 （或更新版本） 包含 A6 處理器才支援 ARMv7s。 ARMv7 程式碼更快且小於 ARMv6、 只適用於 iPhone 3GS 和更新版本，和 Apple iPad 或 5.0 的最低 iOS 版本為目標時需要。 ARMv6 適用於所有裝置，但是隨附 Xcode 4.5 和更新版本的編譯器已不再支援。 

ARM64，才可支援 iOS 8 的 iPhone 6 或其他 64 位元的裝置，都必須由 Apple 提交新的或更新結束在 iTunes App Store 中的應用程式時。

有關各種不同的 iOS 裝置的功能的完整介紹，請參閱 Apple[裝置的相容性](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html)文件。

### <a name="64-bit-and-binary-size-increases"></a>64 位元和二進位的大小會增加

在 Apple 的轉換，從 32 位元到 64 位元，iOS 應用程式必須在 32 位元和 64 位元硬體上執行。 因為這個緣故，Xamarin 的統一 API 讓開發人員兩者。

以 32 位元和 64 位元架構為目標，可大幅提升應用程式的大小。 不過，這麼做，可讓較新的裝置，同時仍可支援較舊的裝置執行最佳化程式碼。

> [!IMPORTANT]
> 如果您在提交 iTunes App Store、 iOS 應用程式時，收到下列訊息 _」 警告 ITMS 9000:遺漏的 64 位元支援。從 2015 年 2 月 1 日起，新的 iOS 上傳至 App Store 的應用程式必須包含 64 位元支援，並以建置 iOS 8 的 SDK，包含在 Xcode 6 或更新版本。若要啟用 64 位元在專案中，我們建議使用的預設 Xcode 組建的 「 標準架構 」 的設定來建置單一的二進位檔，以 32 位元和 64 位元的程式碼。 」_ 您需要切換至其中一個可用的支援的架構**ARM64** （如上所示） 的組合，重新編譯和重新提交。

## <a name="mac"></a>Mac

> [!IMPORTANT]
> 從 2018 年 1 月開始，所有新 Mac 應用程式提交至 Mac App Store 必須支援 64 位元。 現有的 Mac App Store 應用程式和其更新，都必須支援 64 位元開始於 2018 年 6 月。 請參閱[Apple 的公告](https://developer.apple.com/news/?id=06282017a)並[說明如何更新為 64 位元的 Xamarin.Mac 應用程式的指南](~/cross-platform/macios/32-and-64/mac-64-bit.md)。

現今大部分的 Mac 電腦支援 32 位元和 64 位元應用程式。   MacOS 10.6 (Snow Leopard) 是 32 位元系統上執行的最後一個作業系統。   大部分的 Mac，釋出因為 2010年支援兩個系統。

不同於 iOS，許多最新版本的 macOS 中導入新的架構只支援在 64 位元模式下 （CloudKit、 EventKit、 GameController、 LocalAuthentication、 MediaLibrary、 MultipeerConnectivity、 NotificationCenter、 GLKit、 SpriteKit、 社交性和 MapKit，其他項目）。

統一的 API 讓開發人員可以選擇要產生何種應用程式：32 位元或 64 位元。

**32 位元應用程式**將 32 位元和 64 位元的 Mac 電腦上執行，具有位址空間限制為 32 位元，且需要所有的程式庫是 32 位元。

如果您有無法在 64 位元模式中執行的 32 位元相依性，如果您想要擁有較小的下載，或如果不有任何效能優勢，移至 64 位元，您通常會使用此模式。

此模式會限制，因為就不會使用適用於 macOS Mavericks 和 macOS Yosemite 的許多架構。

**64 位元應用程式**只有 64 位元的 Mac 裝置上執行。

針對 Mac，這是作業的慣用的模式，使用中的大部分 Mac 目前支援的 64 位元模式，以及您可以存取一組完整的 Apple 提供的架構。

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>啟用 64 位元的 Xamarin.Mac 應用程式組建

如需建置使用 Xamarin.Mac 的 64 位元應用程式的資訊，請參閱[更新 Xamarin.Mac 統一的應用程式，以 64 位元](~/cross-platform/macios/32-and-64/mac-64-bit.md)指南。

## <a name="related-links"></a>相關連結

- [傳統的 vs 統一的 API 差異](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
