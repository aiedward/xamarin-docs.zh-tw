---
title: MacOS Sierra 的簡介
description: 這篇文章會介紹的所有新的及修改 Api 和 macOS Sierra 中可用的功能適用於 Xamarin.Mac 開發人員。
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 5a944fd8f7dcfdcbb3f025c92b4afac35673416f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111008"
---
# <a name="introduction-to-macos-sierra"></a>MacOS Sierra 的簡介

使用新的 macOS Sierra，開發人員可以利用新的 Api，可讓使用者自己的應用程式與網站互動以過去無法使用的方式。 例如，Apple 現在可讓網站，讓客戶選擇支付安全地透過 Apple Pay 和增強功能，裸機 framework 提升應用程式的圖形和執行計算的可能。 

如需有關 macOS Sierra 的詳細資訊，請參閱 Apple [macOS 應用程式 +](https://developer.apple.com/macos/)文件。

<a name="Whats-New-in-macOS-Sierra" />

## <a name="whats-new-in-macos-sierra"></a>新功能 macOS Sierra

Apple 已加入數個新的 Api 和服務，以及現有功能，包括許多增強功能的 macOS Sierra 中：

<a name="Apple-File-System" />

### <a name="apple-file-system"></a>Apple 檔案系統

具有 macOS Sierra，Apple 已發行新的 Apple 檔案系統，為 iOS、 macOS、 tvOS 和 watchOS 現代的檔案系統。 Apple 檔案系統已針對 Flash 和 SSD 儲存體最佳化，並提供下列功能： 強式加密，寫入時複製中繼資料，空間共用、 複製檔案和目錄、 快照集、 快速的目錄大小和不可部分完成的安全儲存基本型別。

如需詳細資訊，請參閱 Apple [Apple 檔案系統指南](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999)。

<a name="Apple-Pay-Enhancements" />

### <a name="apple-pay-enhancements"></a>Apple Pay 的增強功能

Apple 有了許多改進功能到 Apple Pay 可讓使用者從網站的安全付款的 macOS Sierra 中。

具有 macOS Sierra，數個新的 Api 已新增使用 macOS Sierra、 iOS 及 watchOS 支援動態付款網路與新的沙箱測試環境。

macOS Sierra 包含新的 ApplePay Javascript 架構，可讓開發人員將 Apple Pay 直接在 iOS 和 macOS Safari 為基礎的網站。 支援 Apple Pay 的網站，使用者可以授權使用他們的 iPhone 或 Apple Watch 的付款。

如需詳細資訊，請參閱 Apple [ApplePay JS Framework](https://developer.apple.com/reference/applepayjs)參考。

<a name="Building-Modern-macOS-Apps" />

### <a name="building-modern-macos-apps"></a>建置現代化的 macOS 應用程式

現代化的 macOS 應用程式，例如 Apple Safari 網頁瀏覽器、 頁面文書處理器，並顯示統一且即時的使用者介面與傳統的 UI 項目，例如浮動面板的 立即執行的許多新技術和多個開啟的數字散佈工作表使用windows。

[![索引標籤式的 [Mac] 視窗的範例](images/content08.png)](images/content08.png#lightbox)

我們[建置現代化的 macOS 應用程式](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md)指南涵蓋幾個秘訣、 功能和技術的開發人員可用來建置現代化的 macOS 中的應用程式 Xamarin.Mac。

<a name="CloudKit-Data-Sharing" />

### <a name="cloudkit-data-sharing"></a>CloudKit 資料共用

CloudKit 架構已經過擴充，在 macOS Sierra 可讓使用者輕鬆快速地分享記錄或記錄集，從他們的私用 iCloud 資料庫中。

CloudKit 提供完整的 UI 來傳送和接受記錄的共用的邀請，使用者已完成的讀取/寫入控制已記錄的存取權的人員。

如需詳細資訊，請參閱 Apple [CloudKit Framework 參考](https://developer.apple.com/reference/clockkit)並[CloudKit JS Framework 參考](https://developer.apple.com/reference/cloudkitjs)。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

<a name="Safari-App-Extensions-Support" />

### <a name="safari-app-extensions-support"></a>Safari 應用程式延伸模組支援

Safari 應用程式擴充功能可讓應用程式，以將擴充的 Safari 網頁瀏覽器的行為，同時具有 macOS Sierra 緊密整合。 因為 macOS Safari 應用程式擴充功能運作方式類似 iOS Safari 應用程式擴充功能，可輕鬆連接埠從到另一個系統。

如需詳細資訊，請參閱 Apple [Safari 應用程式擴充功能程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319)。

<a name="Security-and-Privacy-Enhancements" />

### <a name="security-and-privacy-enhancements"></a>安全性和隱私權增強功能

Apple 已對安全性與隱私權 macOS Sierra 有助於改善應用程式的安全性，並確保使用者的隱私權，包括下列的應用程式中的數個增強功能：

- 新`NSAllowsArbitraryLoadsInWebContent`可以將金鑰新增至應用程式的`Info.plist`檔案，並可讓應用程式的其餘部分仍啟用 Apple Transport Security (ATS) 保護而正確載入的網頁。
- 常見的資料安全性架構 (CDSA) API 已被取代，應以 SecKey API，來產生非對稱金鑰來取代。
- 對於所有 SSL/TLS 連線，RC4 對稱編碼器現在預設會停用。 此外，安全傳輸 API 不再支援 SSLv3 與建議的應用程式停止儘速使用 sha-1 和 3DES 加密。
- 因為新的剪貼簿，在 iOS 10 和 macOS Sierra 允許複製並貼上裝置之間的使用者，所以 API 已擴充到允許的剪貼簿，受限於特定的裝置，並會自動清除在指定的時間點的時間戳記。 此外，具名的 pasteboards 不會再保存，並應該取代成共用剪貼板容器。
- 如果應用程式存取受保護的資料 （例如使用者的行事曆），它_必須_使用正確的目的字串值索引鍵，在宣告該意圖其`Info.plist`檔案 (`NSCalendarUsageDescription`在行事曆的情況下)。
- 不會傳遞透過 Mac App Store 的開發人員簽署應用程式現在可以利用 CloudKit iCloud 鑰匙圈、 iCloud 磁碟機，遠端的推播通知、 MapKit 和 VPN 的權利。
- macOS Sierra 不再支援傳遞外部的程式碼或資料，以及在其 zip 封存或不帶正負號的磁碟映像中的程式碼簽署者應用程式，因為執行階段之前不知道執行階段的路徑。

此外，在 macOS Sierra 上執行的應用程式 （或更新版本） 必須以靜態方式宣告輸入一或多個隱私權特定中索引鍵來存取特定功能或使用者資訊的意圖其`Info.plist`說明為什麼應用程式想要取得使用者的檔案存取權。

因為 macOS Sierra 與 iOS 10 共用這些變更，請參閱我們的 iOS 10[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)指南以取得詳細的資訊。

<a name="Smart-Card-Driver-Extension-Support" />

### <a name="smart-card-driver-extension-support"></a>智慧卡驅動程式延伸模組支援

此應用程式可以建立具有 macOS Sierra，`NSExtension`基礎內容的唯讀存取權讓特定類型的智慧卡的智慧卡驅動程式。 這項資訊接著會看到內 （取代已被取代的通用資料的安全性架構方法） 的系統 keychain 中。

如需詳細資訊，請參閱 Apple 的[CryptoTokenKit Framework 參考](https://developer.apple.com/reference/cryptotokenkit)。

<a name="Unified-Logging" />

### <a name="unified-logging"></a>統一的記錄

統一的記錄會提供以單一 API 應用程式有效率之傳訊系統的所有層級之間。 使用統一記錄應用程式會有更細微的控制，包括隱私權控制，以及以利偵錯追蹤的活動記錄的多個層級。 

追蹤和記錄的活動會一起使用時，記錄會提供自動訊息相互關聯。

macOS Sierra 包含新主控台應用程式 （在應用程式/公用程式） 能夠顯示來自多個來源，包括已連線的裝置的記錄資料。 它也支援語彙基元化和已儲存搜尋，並顯示相關的郵件，跨越多個處理序之間的連線。

此外，記錄檔訊息可以進行檢視，並使用命令列工具。

如需詳細資訊，請參閱 Apple[記錄參考](https://developer.apple.com/reference/os/1891852-logging)。

<a name="Wide-Color" />

### <a name="wide-color"></a>寬色域

macOS Sierra 延伸的延伸範圍像素格式和整個系統，包括架構，例如核心圖形、 Core 映像、 裸機和 AVFoundation 寬色域圖色彩空間的支援。 藉由提供整個圖形堆疊在這種行為，進一步降低與寬的色彩顯示裝置的支援。

此外，`AppKit`已經過修改才能在新擴充**sRGB** colorspace，方便您混合使用中而不會顯著的效能遺失的寬色域色階的色彩。

使用各種色彩時，Apple 提供以下最佳作法：

- `NSColor` 現在會使用 sRGB 色彩空間，並將無法再將值`0.0`至`1.0`範圍。 如果應用程式依賴舊版的 clamp 行為時，它將會需要修改適用於 macOS Sierra。
- 當使用低層級的 API，例如核心圖形或金屬提供映像處理時，應用程式應該使用的延伸的範圍的色彩空間和像素格式支援 16 位元浮點值。 必要時，應用程式必須以手動方式將色彩元件值。
- Core 圖形、 Core 映像和金屬效能著色器都有提供兩個色彩空間之間進行轉換的新方法。

若要深入了解，請參閱我們[寬色域簡介](~/ios/platform/wide-color.md)指南。

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>其他的架構變更

除了主要的架構變更和上面所列的內容，Apple 已變更許多其他的次要 framework macOS Sierra 中。

若要深入了解，請參閱我們[額外的架構變更](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md)指南。

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>已被取代的 API

下列 Api 已被取代，在 macOS Sierra 中：

- 不再支援 HFS 標準檔案系統。

請參閱 Apple [OS X v10.12 API 差異](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/APIDiffsMacOS10_12/index.html)棄用功能和變更的文件的完整清單。

## <a name="related-links"></a>相關連結

- [Mac 範例](https://developer.xamarin.com/samples/mac/)
- [在 OS X 10.12 最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
