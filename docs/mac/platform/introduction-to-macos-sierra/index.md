---
title: MacOS 利也簡介
description: 本文介紹的所有新的及修改應用程式開發介面和 macOS 利也提供功能 Xamarin.Mac 開發人員。
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 3b8211e4c38fd37040fab5b35be4709d4b926c91
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
ms.locfileid: "31044976"
---
# <a name="introduction-to-macos-sierra"></a>MacOS 利也簡介

新 macOS 利也，開發人員可以利用新的 api 可讓使用者無法使用先前的方式與他們的應用程式和網站互動。 例如，Apple 現在可讓網站，讓客戶選擇支付安全地透過 Apple Pay 和增強功能的金屬 framework 提升應用程式的圖形，並計算可能。 

如需有關 macOS 利也的詳細資訊，請參閱 Apple [macOS + 應用程式](https://developer.apple.com/macos/)文件。

<a name="Whats-New-in-macOS-Sierra" />

## <a name="whats-new-in-macos-sierra"></a>新功能 macOS 利也

Apple 加入數個新的 Api 和服務 macOS 利也以及現有功能，包括許多增強功能：

<a name="Apple-File-System" />

### <a name="apple-file-system"></a>Apple 檔案系統

MacOS 利也與 Apple 已發行新的 Apple 檔案系統，為 iOS、 macOS、 tvOS 和 watchOS 現代的檔案系統。 Apple 檔案系統已針對 Flash 和 SSD 儲存體最佳化，並提供下列功能： 增強式加密，寫入時複製中繼資料，空間共用、 複製檔案和目錄、 快照集，快速目錄調整大小和不可部分完成的安全儲存基本型別。

如需詳細資訊，請參閱 Apple [Apple 檔案系統指南](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999)。

<a name="Apple-Pay-Enhancements" />

### <a name="apple-pay-enhancements"></a>Apple Pay 增強功能

Apple 已 macOS 利也可讓使用者安全的付款，從網站中，以進行的 Apple Pay 數個增強功能。

MacOS 利也具有數個新的應用程式開發介面已加入使用利也 macOS、 iOS 和 watchOS 支援動態付款網路與新的沙箱測試環境。

macOS 利也包含新的 ApplePay Javascript 架構，可讓開發人員併入 Apple Pay 直接 iOS 和 macOS Safari 為基礎的網站。 支援 Apple Pay 的網站，使用者可以授權透過他們的 iPhone 或 Apple Watch 的付款。

如需詳細資訊，請參閱 Apple [ApplePay JS Framework](https://developer.apple.com/reference/applepayjs)參考。

<a name="Building-Modern-macOS-Apps" />

### <a name="building-modern-macos-apps"></a>建置現代化 macOS 應用程式

現代 macOS 應用程式，例如 Apple Safari 網頁瀏覽器、 頁面文書處理器，並顯示與傳統的 UI 項目，例如浮動面板的 離開沒有統一且即時的使用者介面的許多新技術，以及多個開啟的數字模式工作表使用windows。

[![索引標籤式的 Mac 視窗的範例](images/content08.png)](images/content08.png#lightbox)

我們[建置現代化 macOS 應用程式](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md)指南涵蓋了幾個秘訣、 功能和技術的開發人員可以使用建置中 Xamarin.Mac macOS 現代應用程式。

<a name="CloudKit-Data-Sharing" />

### <a name="cloudkit-data-sharing"></a>CloudKit 資料共用

CloudKit framework 已展開於 macOS 利也可讓使用者快速且輕鬆地共用記錄或從其私用 iCloud 資料庫的資料錄集。

CloudKit 提供完整的 UI 來傳送和接受記錄的共用的邀請，而且使用者必須完成的讀取/寫入控制可以存取記錄的人員。

如需詳細資訊，請參閱 Apple [CloudKit Framework 參考](https://developer.apple.com/reference/clockkit)和[CloudKit JS Framework 參考](https://developer.apple.com/reference/cloudkitjs)。

> [!IMPORTANT]
> Apple[提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)可協助開發人員適當地處理歐盟的一般資料保護規定 (GDPR)。

<a name="Safari-App-Extensions-Support" />

### <a name="safari-app-extensions-support"></a>Safari 應用程式擴充功能的支援

Safari 應用程式擴充功能允許應用程式時要緊密整合 macOS 利也擴充 Safari 網頁瀏覽器的行為。 因為 macOS Safari 應用程式擴充功能運作類似於 iOS Safari 應用程式擴充功能，可輕鬆連接埠從到另一個系統。

如需詳細資訊，請參閱 Apple [Safari 應用程式延伸模組程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319)。

<a name="Security-and-Privacy-Enhancements" />

### <a name="security-and-privacy-enhancements"></a>安全性和隱私權增強功能

Apple 已對安全性和隱私權 macOS 利也有助於改善應用程式的安全性和確保終端使用者的隱私權，包括下列的應用程式中的數個增強功能：

- 新`NSAllowsArbitraryLoadsInWebContent`金鑰可以加入應用程式的`Info.plist`檔案，並可讓網頁應用程式的其餘部分仍啟用 Apple 傳輸安全性 (ATS) 保護時正確載入。
- 常見資料安全性架構 (CDSA) 應用程式開發介面已被取代，應該使用 SecKey API 來產生非對稱金鑰來取代。
- 對於所有 SSL/TLS 連線，RC4 對稱式加密現在預設會停用。 此外，安全傳輸 API 不再支援 SSLv3，建議應用程式停止儘速使用 sha-1 和 3DES 加密。
- 因為新的剪貼簿中 iOS 10 及 macOS 利也可讓使用者複製和貼上裝置之間，已擴充 API，以允許剪貼簿限制為特定的裝置，並加上時間戳記會自動清除特定時點。 此外，具名的 pasteboards 將不再保存，而且應該取代共用剪貼版容器。
- 如果應用程式存取受保護的資料 （例如使用者的行事曆），它_必須_具有正確的目的字串值之索引鍵中宣告該意圖其`Info.plist`檔案 (`NSCalendarUsageDescription`在行事曆的情況下)。
- 不會傳遞透過 Mac App Store 的開發人員簽署應用程式現在可以利用 CloudKit、 iCloud Keychain、 iCloud 磁碟機，遠端的推播通知、 MapKit 和 VPN 的權利。
- macOS 利也不再支援傳遞外部程式碼或資料，以及其 zip 封存或不帶正負號的磁碟映像中的程式碼簽署應用程式，因為之前執行階段不知道的執行階段的路徑。

此外，macOS 利也上執行的應用程式 （或更新版本） 必須以靜態方式宣告其輸入一或多個隱私權特定中索引鍵來存取特定功能或使用者資訊的意圖其`Info.plist`解釋為什麼應用程式想要取得的使用者的檔案存取權。

因為 macOS 利也與 iOS 10 共用這些變更，請參閱我們的 iOS 10[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)指南以取得詳細的資訊。

<a name="Smart-Card-Driver-Extension-Support" />

### <a name="smart-card-driver-extension-support"></a>智慧卡驅動程式延伸模組支援

應用程式可以建立與 macOS 利也`NSExtension`基礎智慧卡驅動程式內容允許唯讀存取，從智慧卡的特定型別。 這項資訊接著會呈現系統 keychain （取代已被取代的常見資料安全性架構方法） 內。

如需詳細資訊，請參閱 Apple 的[CryptoTokenKit Framework 參考](https://developer.apple.com/reference/cryptotokenkit)。

<a name="Unified-Logging" />

### <a name="unified-logging"></a>統一的記錄

統一的記錄提供使用單一 API 的應用程式的有效訊息傳送到系統的所有層級。 使用統一記錄應用程式有多個層級的記錄包含隱私權控制與活動追蹤為方便偵錯更細微的控制。 

追蹤和記錄的活動會一起使用時，記錄會提供自動訊息相互關聯。

macOS 利也包含新主控台應用程式 （在應用程式/公用程式） 能夠顯示記錄資料從多個來源，包括連接的裝置。 它也支援語彙基元化和已儲存的搜尋，並會顯示多個處理序的相關訊息之間的連接。

此外，記錄檔訊息可以檢視與維護使用命令列工具。

如需詳細資訊，請參閱 Apple[記錄參考](https://developer.apple.com/reference/os/1891852-logging)。

<a name="Wide-Color" />

### <a name="wide-color"></a>寬的色彩

macOS 利也可延伸的擴充範圍像素格式和整個系統中包括架構，例如核心圖形、 Core 映像、 裸機和 AVFoundation 寬一系列色彩空間的支援。 藉由提供整個圖形堆疊此行為，是進一步降低與寬的色彩顯示裝置的支援。

此外，`AppKit`已修改才能在新擴充**sRGB** colorspace，輕鬆地混合中不會顯著的效能遺失寬色彩色階的色彩。

Apple 時使用寬的色彩，請提供下列最佳作法：

- `NSColor` 現在使用 sRGB 色彩空間，而且將不再 clamp 值`0.0`至`1.0`範圍。 如果應用程式依賴之前有縮短時間差行為時，它必須針對 macOS 利也進行修改。
- 當使用例如核心圖形或金屬低階 API 提供映像處理時，應用程式應該使用的擴充的範圍的色彩空間和像素格式支援 16 位元浮點值。 在需要時，應用程式必須以手動方式 clamp 色彩元件值。
- 核心圖形、 Core 映像和金屬效能著色器所有提供的兩個色彩空間之間進行轉換的新方法。

若要進一步了解，請參閱我們[簡介寬色彩](~/ios/platform/wide-color.md)指南。

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>其他架構變更

主要架構變更和加入上面所列的項目，除了 Apple 進行了許多其他次要架構變更 macOS 利也中。

若要進一步了解，請參閱我們[其他架構變更](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md)指南。

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>已被取代的 API

中 macOS 利也已被取代下列 Api:

- 不再支援 HFS 標準檔案系統。

請參閱 Apple [OS X v10.12 API 差異](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/APIDiffsMacOS10_12/index.html)deprecations 和變更的文件的完整清單。

## <a name="related-links"></a>相關連結

- [Mac 範例](https://developer.xamarin.com/samples/mac/)
- [OS X 10.12 中最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
