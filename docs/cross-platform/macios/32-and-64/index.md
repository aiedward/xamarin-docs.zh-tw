---
title: 32/64 位平臺考慮
description: 本檔說明以 Xamarin 或 Xamarin 應用程式的32位和64位架構為目標時, 要牢記在心的各種考慮。
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 7f41d65c8981d4ef6ffcf1b812fbbbb7e72e7719
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509752"
---
# <a name="3264-bit-platform-considerations"></a>32/64 位平臺考慮

雖然 iOS 和 macOS 過去都支援32和64位應用程式, 但 Apple 已逐漸淘汰32位支援。

從 iOS 11 開始, 32 位應用程式將不會再啟動, 而[App Store 的所有提交都必須支援64位](https://developer.apple.com/news/?id=06282017b)。

從2018年1月開始,[提交至 Mac App Store 的新應用程式必須支援64位](https://developer.apple.com/news/?id=06282017a), 而且現有的應用程式必須在6月2018日更新。

Xamarin 的 Classic API (`XamMac.dll`和`monotouch.dll`) 僅支援32位應用程式。 不過, 新的 xamarin 和 xamarin 應用程式預設會使用[Unified API](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS`和`Xamarin.Mac`), 因此可以視需要以32和64位為目標。

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>啟用 Xamarin iOS 應用程式的64位組建

> [!WARNING]
> 本節內容包含在歷史方面, 並可協助將舊版的 Xamarin iOS 專案移至 Unified API 並支援64位。 所有新的 Xamarin iOS 專案預設都會使用 Unified API 和目標64位。

針對已轉換為 Unified API 的 Xamarin iOS 行動應用程式, 開發人員必須手動將組建設定更新為目標64位:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在  **Solution Pad**中, 按兩下應用程式的專案, 以開啟 **專案選項** 視窗。
2. 選取 [ **IOS 組建**]。
3. 針對 iPhone 模擬器, 請在 [**支援的架構**] 下拉式清單中, 選取 [ **x86\_64** ] 或 [ **i386 + x86\_64**]:

   [![將支援的架構設定\_為 x86 64 或 i386\_+ x86 64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. 針對實體裝置, 請選取其中一個可用的**ARM64**組合:

   [將![支援的架構設定為其中一個 ARM64 組合]將(Images/Image02.png "支援的架構設定為其中一個 ARM64 組合")](Images/Image02-large.png#lightbox)

5. 按一下 [確定] 。
6. 執行乾淨的組建。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管**中, 以滑鼠右鍵按一下應用程式的專案, 然後選取 **屬性**。
2. 選取 [ **IOS 組建**]。
3. 針對 iPhone 模擬器, 請將**支援的架構**設定**為\_x86 64**或**i386 +\_x86 64**: 

   [![將支援的架構設定為 x86_64 或 i386\_+ x86 64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. 針對實體裝置, 請選取其中一個可用的**ARM64**組合:
    
   [將![支援的架構設定為其中一個 ARM64 組合]將(Images/VS01.png "支援的架構設定為其中一個 ARM64 組合")](Images/VS01-large.png#lightbox)

5. 儲存您的變更。
6. 執行乾淨的組建。

-----

只有 iPhone 5 (或更新版本) 中包含的 A6 處理器才支援 ARMv7s。 ARMv7 程式碼比 ARMv6 更快、更小, 只適用于 iPhone 3GS 和更新版本, Apple 在以 iPad 或最低 iOS 版本5.0 為目標時, 是由 Apple 所需。 ARMv6 適用于所有裝置, 但在 Xcode 4.5 和更新版本隨附的編譯器已不再支援。 

需要 ARM64 才能支援 iPhone 6 或其他64位裝置上的 iOS 8, 而且 Apple 會在 iTunes App Store 中提交新的或更新現有的應用程式時要求使用。

如需各種 iOS 裝置功能的完整探討, 請參閱 Apple 的[裝置相容性](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html)檔。

### <a name="64-bit-and-binary-size-increases"></a>64位和二進位大小的增加

在 Apple 從32到64位的轉換期間, iOS 應用程式必須在32位和64位硬體上執行。 因此, Xamarin 的 Unified API 可讓開發人員以兩者為目標。

以32位和64位架構為目標, 會大幅增加應用程式的大小。 不過, 這樣做可讓較新的裝置執行優化的程式碼, 同時仍然支援舊版裝置。

> [!IMPORTANT]
> 當您將 iOS 應用程式提交至 iTunes App Store 時, 如果收到下列訊息 _, 則會出現「警告 ITMS-9000:缺少64位支援。從2015年2月1日開始, 會上傳至 App Store 的新 iOS 應用程式必須包含64位支援, 並使用 iOS 8 SDK (包含在 Xcode 6 或更新版本中) 來建立。若要在您的專案中啟用64位, 建議使用「標準架構」的預設 Xcode 組建設定, 以建立具有32位和64位程式碼的單一二進位檔。」_ 您需要將支援的架構切換為其中一個可用的**ARM64**組合 (如上所示), 重新編譯並重新提交。

## <a name="mac"></a>Mac

> [!IMPORTANT]
> 從2018年1月開始, 所有提交至 Mac App Store 的新 Mac 應用程式都必須支援64位。 從6月2018開始, 現有的 Mac App Store 應用程式和其更新必須支援64位。 請參閱[Apple 的公告](https://developer.apple.com/news/?id=06282017a)和[指南, 其中說明如何將您的 Xamarin. Mac 應用程式更新為64位](~/cross-platform/macios/32-and-64/mac-64-bit.md)。

大部分的新式 Mac 電腦同時支援32位和64位應用程式。   MacOS 10.6 (雪 Leopard) 是最後一個在32位系統上執行的作業系統。   因為2010支援這兩個系統, 所以大部分的 Mac 都已發行。

不同于 iOS, 在最新版本的 macOS 中引進的許多新架構僅支援64位模式 (CloudKit、EventKit、GameController、LocalAuthentication、MediaLibrary、MultipeerConnectivity、NotificationCenter、GLKit、SpriteKit、社交、和 MapKit, 還有其他)。

Unified API 可以讓開發人員選擇他們想要產生的應用程式類型:32位或64位。

**32 位應用程式**會在32位和64位的 Mac 電腦上執行, 位址空間限制為32位, 而且所有程式庫都必須是32位。

如果您有32位相依性, 而這些相依性不是在64位模式中執行, 如果您想要下載較小的, 或移至64位時沒有效能優勢, 您通常會使用此模式。

此模式會受到限制, 因為您無法使用 macOS Mavericks 和 macOS Yosemite 中提供的許多架構。

**64 位應用程式**只會在64位的 Mac 裝置上執行。

針對 Mac, 這是慣用的作業模式, 因為目前使用的 Mac 大部分都支援64位模式, 而且您可以存取 Apple 所提供的一組完整架構。

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>啟用 Xamarin. Mac 應用程式的64位組建

如需使用 Xamarin 建立64位應用程式的相關資訊, 請參閱將[Xamarin 整合應用程式更新為64位](~/cross-platform/macios/32-and-64/mac-64-bit.md)指南。

## <a name="related-links"></a>相關連結

- [傳統與 Unified API 差異](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
