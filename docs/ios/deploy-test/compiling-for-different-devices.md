---
title: Xamarin.iOS 不同裝置的編譯
description: 本文件描述各種可以用來自訂不同裝置之 Xamarin.iOS 組建的組建組態選項。
ms.prod: xamarin
ms.assetid: 3B259248-887E-3E4F-E09C-7AD28C2A8CEE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 1f71179ccafc2daf65e792c4538bf47ea2df1e7d
ms.sourcegitcommit: 0177e06169da621ed9d5fa0f6118a628e8c92bd2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663702"
---
# <a name="compiling-for-different-devices-in-xamarinios"></a>針對 Xamarin 中的不同裝置進行編譯

可執行檔的組建屬性可從專案的 [iOS 組建] 屬性頁面來設定，若要找到此頁面，請在專案名稱上按一下滑鼠右鍵，然後瀏覽至 [選項] > [iOS 組建] (在 Visual Studio for Mac 中) 和 [屬性]\(在 Visual Studio 中)：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](compiling-for-different-devices-images/image1.png "The Projects iOS Build properties page")](compiling-for-different-devices-images/image1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](compiling-for-different-devices-images/image1a.png "The Projects iOS Build properties page")](compiling-for-different-devices-images/image1a.png#lightbox)

-----

除了 UI 上可用的組態選項外，您也可以將自己的命令列選項集傳遞至 [Xamarin.iOS 建置工具 (mtouch)](~/ios/deploy-test/mtouch.md)。

## <a name="sdk-options"></a>SDK 選項

Visual Studio for Mac 可讓您設定兩個與 SDK 相關的重要屬性：iOS SDK 版本 (用來建置軟體) 和部署目標 (或最小的必要 iOS 版本)。

iOS **SDK 版本**選項可讓您使用不同版本的 Apple 發佈 SDK，這會將 Xamarin.iOS 導向至其應該在建置期間參考的編譯器、連結器和程式庫。 以滑鼠右鍵按一下專案，然後在 [選項] 視窗中依序選擇 [**選項**] 和 [ **iOS 組建**]：

[![選擇 [選項] 視窗上的 [SDK 版本]](compiling-for-different-devices-images/sdk-version-sml.png)](compiling-for-different-devices-images/sdk-version.png#lightbox)

**部署目標**設定可用來選取應用程式執行所在之作業系統的最小必要版本。 這會在您專案的**plist**檔案中設定。 請挑選擁有您執行應用程式所需之所有 API 的最小版本。

[![在 plist 檔案中設定部署目標](compiling-for-different-devices-images/deployment-target-sml.png)](compiling-for-different-devices-images/deployment-target.png#lightbox)

一般來說，Xamarin.iOS API 會公開最新版 SDK 中的所有可用方法，而且如有必要，我們也會提供可讓您偵測功能是否可在執行階段使用的便利屬性 (例如，`UIDevice.UserInterfaceIdiom` 和 `UIDevice.IsMultitaskingSupported` 隨時都會在 Xamarin.iOS 上運作，我們會執幕後行在所有工作)。

## <a name="linking"></a>連結

請參閱我們的[連結器](~/ios/deploy-test/linker.md)專屬頁面，以深入了解連結器如何協助您減少可執行檔的大小，以及了解如何有效地使用連結器。

## <a name="code-generation-engine"></a>程式碼產生引擎

從 Xamarin.iOS 4.0 開始，Xamarin.iOS 就有兩個程式碼產生後端。 正規的 Mono 程式碼產生引擎，以及以 LLVM 最佳化編譯器為基礎的引擎。 這兩個引擎各有優缺點。

一般而言，在開發過程中，您可能會使用 Mono 程式碼產生引擎，因為它可讓您快速地逐一查看。 對於發行組建和 AppStore 部署，則建議您改用 LLVM 程式碼產生引擎。

相較於 Mono 引擎，LLVM 最佳化後端引擎能夠產生更快、更嚴謹的程式碼，但代價是編譯時間較久。

您可以從 Visual Studio for Mac 或 Visual Studio 的 [iOS 組建] 選項啟用這兩個引擎。

[![](compiling-for-different-devices-images/image2.png "Enabling LLVM")](compiling-for-different-devices-images/image2.png#lightbox)

[![](compiling-for-different-devices-images/image2a.png "Enabling LLVM")](compiling-for-different-devices-images/image2a.png#lightbox)

## <a name="architecture-support"></a>架構支援

### <a name="armv6-xamarinios-discontinued-support-for-armv6-with-v810"></a>ARMv6 (Xamarin.iOS 已在 8.10 版中斷對 ARMv6 的支援)

- iPhone (第一代)、3G
- iPod 第 1 代、第 2 代

### <a name="armv7"></a>ARMv7

- iPhone 3GS、4、4S
- iPad 1、2、3、Mini
- iPod 3、4、5 代

### <a name="armv7s"></a>ARMv7s

- iPhone 5
- iPhone 5c
- iPad 4

如果您只以 ARMv7s 處理器作為目標，所產生的程式碼會快一些，但除非您所編譯的 fat 二進位檔在套件中包含多個可執行檔，否則該程式碼將無法再於 ARMv7 或 ARMv6 系統上執行。

### <a name="arm64-xamarinios-started-supporting-arm64-in-v86"></a>ARM64 (Xamarin.iOS 已在 8.6 版開始支援 ARM64)

- iPhone 5s
- iPhone SE
- iPhone 6、6 Plus
- iPhone 6s、6s Plus
- iPhone 7、7 Plus
- iPhone 8、8 Plus
- iPhone X
- iPad Air
- iPad Air 2
- iPad Mini 2、3、4
- iPad Pro (所有機型)

請注意，提交至 App Store 的任何組建都必須包含 64 位元的支援，這是 [Apple](https://developer.apple.com/news/?id=12172014b) 所設下的需求。 此外，iOS 11 僅支援 64 位元應用程式。

### <a name="arm-thumb-2-support"></a>ARM 拇指2支援

Thumb 是 ARM 處理器所使用、更為精簡的指令集。 藉由啟用 Thumb 支援，您可以減少可執行檔的大小，但代價是執行時間較久。 ARMv7 和 ARMv7s 可支援 Thumb。

## <a name="conditional-framework-usage"></a>條件式架構使用方式

如果您的專案想要利用較新版 iOS 的某些功能，您可能需要有條件地依賴某些新架構。 其基本範例是想要在 iOS 4.0 或更新版本上執行時使用 iAd，但又要繼續支援 3.x 裝置。 為了實現此目的，您需要讓 Xamarin.iOS 知道您需要針對 iAd 架構進行「弱式」連結。 弱式繫結可確保系統只會在首次需要架構中的類別時，隨需載入架構。

如果要這麼做，請執行下列步驟：

- 開啟 [專案選項]，然後瀏覽至 [iOS 組建] 窗格。
- 針對您想要進行弱式連結的每個組態，在 [其他選項] 中新增 `'-gcc_flags "-weak_framework iAd"'`：

[![](compiling-for-different-devices-images/image3.png "Additional Options")](compiling-for-different-devices-images/image3.png#lightbox)

除此之外，您還必須確保您使用的類型不會在沒有這些類型的舊版 iOS 上執行。 有數種方法可達成此目的，但其中一個方式是剖析 `UIDevice.CurrentDevice.SystemVersion`。

## <a name="related-links"></a>相關連結

- [連結器](~/ios/deploy-test/linker.md)
