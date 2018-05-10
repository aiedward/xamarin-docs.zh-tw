---
title: 32/64 位元平台考量
description: 32 位元和 64 位元應用程式的架構為目標時的考量
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 223da6b490e09b2fa27ab3bbf8fa123b5fa8070c
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="3264-bit-platform-considerations"></a>32/64 位元平台考量

IOS 和 macOS 在過去支援 32 和 64 位元應用程式，而 Apple 已逐漸取代 32 位元支援。

從 iOS 11，開始將不會再啟動 32 位元應用程式，以及[所有提交至應用程式存放區必須都支援 64 位元](https://developer.apple.com/news/?id=06282017b)。

從 1 月 2018年[新送出至 Mac App Store 的應用程式必須支援 64 位元](https://developer.apple.com/news/?id=06282017a)，和根據年 6 月 2018年，必須更新現有的應用程式。

Xamarin 的傳統應用程式開發介面 (`XamMac.dll`和`monotouch.dll`) 支援只有 32 位元應用程式。 不過，新的 Xamarin.iOS 和 Xamarin.Mac 應用程式使用[統一的 API](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS`和`Xamarin.Mac`) 根據預設，因此也 32 和 64 位元，視目標。

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>啟用 64 位元建置的 Xamarin.iOS 應用程式

> [!WARNING]
> 本節是歷史原因，以及協助將舊的 Xamarin.iOS 專案移至統一的 API 及支援 64 位元。 所有新的 Xamarin.iOS 專案都會使用統一的 API 和 64 位元目標的預設值。

對於 Xamarin.iOS 行動應用程式已轉換成統一的 API，開發人員必須手動更新的組建設定目標 64 位元：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在**方案板**，按兩下要開啟的應用程式的專案**專案選項**視窗。
2. 選取**iOS 建置**。
3. 適用於 iPhone 模擬器，在**支援的架構**下拉式清單中，選取  **x86\_64**或**i386 + x86\_64**:

   [![設定支援的架構為 x86\_64 或 i386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. 實體裝置，請選取其中一個可用**ARM64**組合：

   [![設定 ARM64 組合的其中一個支援的架構](Images/Image02.png "ARM64 組合的其中一個設定支援的架構")](Images/Image02-large.png#lightbox)

5. 按一下 [確定 **Deploying Office Solutions**]。
6. 執行乾淨的組建。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在**方案總管 中**，以滑鼠右鍵按一下應用程式的專案，然後選取**屬性**。
2. 選取**iOS 建置**。
3. 適用於 iPhone 模擬器中，設定**支援的架構**為**x86\_64**或**i386 + x86\_64**: 

   [![將支援的架構設定為 x86_64 或 i386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. 實體裝置，請選取其中一個可用**ARM64**組合：
    
   [![設定 ARM64 組合的其中一個支援的架構](Images/VS01.png "ARM64 組合的其中一個設定支援的架構")](Images/VS01-large.png#lightbox)

5. 儲存您的變更。
6. 執行乾淨的組建。

-----

ARMv7s 只支援 A6 處理器，包含在 iPhone 5 （或更高）。 ARMv7 程式碼會更快速且小於 ARMv6、 只適用於 iPhone 3GS 和更新版本，和 iPad 或 5.0 的最小的 iOS 版本為目標時，會將 Apple 所需。 ARMv6 適用於所有裝置，但是編譯器隨附 Xcode 4.5 及更新版本已不再支援。 

提交新的或更新結束 iTunes App Store 中的應用程式時需要 apple 和 ARM64 才能支援 iOS 8 iPhone 6 或其他 64 位元裝置上。

如各種 iOS 裝置的能力將詳細討論，請參閱 Apple[裝置相容性](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html)文件。

### <a name="64-bit-and-binary-size-increases"></a>64 位元和二進位大小會增加

從 32 位元到 64 位元，iOS 的 Apple 轉換期間的應用程式必須在 32 位元和 64 位元硬體上執行。 因為這個緣故，Xamarin 的統一的 API 可讓開發人員同時為目標。

針對 32 位元和 64 位元架構，會大幅增加應用程式的大小。 不過，這樣做，將允許較新的裝置，同時還支援較舊的裝置執行最佳化程式碼。

> [!IMPORTANT]
> 如果您收到下列訊息提交至 iTunes App Store 的 iOS 應用程式時_」 警告 ITMS 9000： 遺漏 64 位元支援。從 2015 年 2 月 1 日，新的 iOS App store 上傳的應用程式必須包含 64 位元支援，以及以建置 iOS 8 SDK，包含在 Xcode 6 或更新版本。若要啟用 64 位元，在專案中，我們建議使用 Xcode 組建設定 「 標準架構 」 的預設值來建立單一的二進位檔，以 32 位元和 64 位元程式碼。 」_ 您必須切換到其中一個可用的支援的架構**ARM64** （如上所示） 的組合，重新編譯和重新提交。

## <a name="mac"></a>Mac

> [!IMPORTANT]
> 從開始年 1 月 2018年，所有新 Mac 應用程式提交至 Mac App Store 必須支援 64 位元。 以年 6 月 2018年啟動 64 位元必須支援現有 Mac App Store 應用程式和其更新。 請參閱[Apple 通知](https://developer.apple.com/news/?id=06282017a)和[說明如何更新到 64 位元 Xamarin.Mac 應用程式的指南](~/cross-platform/macios/32-and-64/mac-64-bit.md)。

大多數最新型的 Mac 電腦支援 32 位元和 64 位元應用程式。   MacOS 10.6 (雪 Leopard) 是 32 位元系統上執行最後一個作業系統。   大部分的 Mac 發行之後 2010年支援這兩個系統。

不同於 iOS、 多個 macOS 最近版本中引進的新架構才支援在 64 位元模式下 （CloudKit EventKit、 GameController、 LocalAuthentication、 MediaLibrary、 MultipeerConnectivity、 NotificationCenter、 GLKit、 SpriteKit、 身分證，和 MapKit，和其他項目）。

統一的 API 可讓開發人員選擇他們想要產生何種應用程式： 32 位元或 64 位元。

**32 位元應用程式**將會在 32 位元和 64 位元的 Mac 電腦上執行，具有位址空間限制為 32 位元，並要求所有的程式庫為 32 位元。

如果您有無法在 64 位元模式中執行的 32 位元相依性，如果您想要擁有較小的下載，或如果移動到 64 位元的任何效能優勢，您通常會使用此模式。

此模式會限制您無法為能夠使用 macOS Mavericks 或 macOS Yosemite 許多架構。

**64 位元應用程式**只會在 64 位元 Mac 裝置上執行。

適用於 Mac，這是作業的慣用的模式，大部分使用中的 Mac 現在支援 64 位元模式，以及您可以存取一組完整的 Apple 提供的架構。

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>啟用 64 位元 Xamarin.Mac 應用程式的組建

建置使用 Xamarin.Mac 的 64 位元應用程式的相關資訊，請參閱[更新 Xamarin.Mac 整合到 64 位元的應用程式](~/cross-platform/macios/32-and-64/mac-64-bit.md)指南。

## <a name="related-links"></a>相關的連結

- [傳統的 vs 統一的 API 差異](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
