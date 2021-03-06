---
title: macOS Sierra 簡介
description: 本文將介紹適用于 Xamarin 開發人員 macOS Sierra 中的所有新的和修改過的 Api 和功能。
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 8715559db3f551773f41659a0c7c36adb29f0d1e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437231"
---
# <a name="introduction-to-macos-sierra"></a>macOS Sierra 簡介

有了新的 macOS Sierra，開發人員可以利用新的 Api，讓終端使用者以先前無法使用的方式與應用程式和網站互動。 例如，Apple 現在可讓客戶透過 Apple Pay 取得安全的選項，並提供裸機架構的增強功能，以提升應用程式的圖形和計算潛力。 

如需 macOS Sierra 的詳細資訊，請參閱 Apple 的 [macOS + 應用程式](https://developer.apple.com/macos/) 檔。

<a name="Whats-New-in-macOS-Sierra"></a>

## <a name="whats-new-in-macos-sierra"></a>macOS Sierra 的新功能

Apple 已在 macOS Sierra 中新增數個新的 Api 和服務，以及現有功能的許多增強功能，包括：

<a name="Apple-File-System"></a>

### <a name="apple-file-system"></a>Apple 檔案系統

使用 macOS Sierra，Apple 已發行新的 Apple 檔案系統作為 iOS、macOS、tvOS 和 watchOS 的新式檔案系統。 Apple 檔案系統已針對 Flash 和 SSD 儲存體進行優化，並提供下列功能：強式加密、寫入時複製中繼資料、空間共用、檔案和目錄的複製、快照集、快速目錄大小調整，以及不可部分完成的安全儲存基本專案。

如需詳細資訊，請參閱 Apple 的 [Apple 檔案系統指南](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999)。

<a name="Apple-Pay-Enhancements"></a>

### <a name="apple-pay-enhancements"></a>Apple Pay 增強功能

Apple 已對 macOS Sierra 中的 Apple Pay 進行了數個增強功能，讓使用者能夠從網站進行安全的付款。

在 macOS Sierra 中，已加入數個新的 Api，可與 macOS Sierra、iOS 和 watchOS 搭配使用，以支援動態付款網路和新的沙箱測試環境。

macOS Sierra 包含新的 ApplePay JAVAscript 架構，可讓開發人員直接將 Apple Pay 併入 iOS 和 macOS Safari 型網站。 針對支援 Apple Pay 的網站，使用者可以使用其 iPhone 或 Apple Watch 來授權付款。

如需詳細資訊，請參閱 Apple 的 [APPLEPAY JS 架構](https://developer.apple.com/reference/applepayjs) 參考。

<a name="Building-Modern-macOS-Apps"></a>

### <a name="building-modern-macos-apps"></a>建置現代化的 macOS 應用程式

新式的 macOS 應用程式，例如 Apple 的 Safari 網頁瀏覽器、分頁處理器和數位散佈表使用許多新技術，來呈現與傳統 UI 元素（例如浮動面板和多個開啟視窗）相關的統一、內容相關的消費者介面。

[![索引標籤式 Mac 視窗的範例](images/content08.png)](images/content08.png#lightbox)

我們的 [新式 MacOS Apps](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md) 指南涵蓋了開發人員可用來在 Xamarin 中建立新式 macOS 應用程式的幾個秘訣、功能和技巧。

<a name="CloudKit-Data-Sharing"></a>

### <a name="cloudkit-data-sharing"></a>CloudKit 資料共用

CloudKit 架構已擴充 macOS Sierra，可讓使用者快速且輕鬆地從其私用 iCloud 資料庫共用記錄或記錄集。

CloudKit 會提供完整的 UI 來傳送和接受共用記錄邀請，而使用者對可存取記錄的人員具有完整的讀取/寫入控制。

如需詳細資訊，請參閱 Apple 的 [CloudKit 架構參考](https://developer.apple.com/reference/clockkit) 和 [CloudKit JS 架構參考](https://developer.apple.com/reference/cloudkitjs)。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

<a name="Safari-App-Extensions-Support"></a>

### <a name="safari-app-extensions-support"></a>Safari 應用程式延伸模組支援

Safari 應用程式延伸模組可讓應用程式在與 macOS Sierra 緊密整合時，擴充 Safari 網頁瀏覽器的行為。 由於 macOS Safari 應用程式擴充功能與 iOS Safari 應用程式擴充功能類似，因此很容易就能從某個系統移植到另一個系統。

如需詳細資訊，請參閱 Apple 的 [Safari 應用程式延伸模組程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319)。

<a name="Security-and-Privacy-Enhancements"></a>

### <a name="security-and-privacy-enhancements"></a>安全性與隱私權的增強功能

Apple 已對 macOS Sierra 中的安全性和隱私權進行了許多增強功能，可協助應用程式改善應用程式的安全性，並確保使用者的隱私權，包括下列各項：

- 新的 `NSAllowsArbitraryLoadsInWebContent` 金鑰可以加入至應用程式的檔案 `Info.plist` ，並可讓網頁正確載入，而 Apple Transport SECURITY (ATS) 保護仍會針對應用程式的其餘部分啟用。
- Common Data Security 架構 (CDSA) API 已淘汰，應取代為 SecKey API 來產生非對稱金鑰。
- 針對所有的 SSL/TLS 連線，現在預設會停用 RC4 對稱式加密。 此外，安全傳輸 API 已不再支援 SSLv3，建議應用程式儘快停止使用 SHA-1 和3DES 密碼編譯。
- 因為 iOS 10 和 macOS Sierra 中的新剪貼簿可讓使用者在裝置之間進行複製和貼上，所以已擴充 API 來允許將剪貼簿限制在特定裝置，並在指定的時間點自動清除時間戳記。 此外，名為 pasteboards 的不會再保存，且應取代為共用的夾夾容器。
- 如果應用程式存取受保護的資料 (例如使用者的行事曆) ，它 _必須_ (在行事 `Info.plist` `NSCalendarUsageDescription` 曆) 的情況下，以正確的目的字串值索引鍵來宣告該意圖。
- 未透過 Mac App Store 傳遞的開發人員簽署的應用程式，現在可以利用 CloudKit、iCloud Keychain、iCloud 磁片磁碟機、遠端推播通知、MapKit 和 VPN 權利。
- macOS Sierra 不再支援傳遞外部程式碼或資料，以及其 zip 封存或未簽署的磁片映射中的程式碼簽署者應用程式，因為執行時間之前不知道執行時間路徑。

此外，在 macOS Sierra (或更新) 版本上執行的應用程式，必須以靜態方式宣告其意圖，方法是在其檔案中輸入一或多個隱私權特定金鑰 `Info.plist` ，向使用者說明應用程式希望取得存取權的原因。

因為 macOS Sierra 與 iOS 10 共用這些變更，請參閱我們的 iOS 10 [安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md) 指南以取得詳細資訊。

<a name="Smart-Card-Driver-Extension-Support"></a>

### <a name="smart-card-driver-extension-support"></a>智慧卡驅動程式擴充功能支援

使用 macOS Sierra，應用程式可以建立 `NSExtension` 以智慧卡驅動程式為基礎的智慧卡驅動程式，以允許從特定類型的智慧卡存取內容的唯讀許可權。 這項資訊接著會顯示在系統 keychain 內 (取代已淘汰的 Common Data Security 架構方法) 。

如需詳細資訊，請若參閱 Apple 的 [CryptoTokenKit 架構參考](https://developer.apple.com/reference/cryptotokenkit)。

<a name="Unified-Logging"></a>

### <a name="unified-logging"></a>整合記錄

整合記錄可為應用程式提供單一 API，以在系統的所有層級進行有效率的訊息傳遞。 透過整合式記錄，應用程式可以對多個記錄層級進行更細微的控制，包括隱私權控制和活動追蹤，以便更輕鬆地進行調試。 

當同時使用活動追蹤和記錄時，記錄會提供自動訊息相互關聯。

macOS Sierra 包含在應用程式/) 公用程式中 (的新主控台應用程式，該應用程式可從多個來源（包括已連線的裝置）顯示記錄資料。 它也支援 token 化和儲存的搜尋，並顯示跨多個進程的相關訊息之間的連接。

此外，您也可以使用命令列工具來查看和維護記錄訊息。

如需詳細資訊，請參閱 Apple 的 [記錄參考](https://developer.apple.com/documentation/os/logging)。

<a name="Wide-Color"></a>

### <a name="wide-color"></a>寬色域

macOS Sierra 在整個系統中擴充擴充範圍像素格式和寬範圍色彩空間的支援，包括核心圖形、核心影像、金屬和 AVFoundation 等架構。 在整個圖形堆疊中提供這項行為，可進一步分階段減緩支援彩色顯示器的裝置。

此外，已 `AppKit` 修改為在新的擴充 **sRGB** colorspace 中工作，讓您更輕鬆地混合寬色彩 gamuts 中的色彩，而不會大幅降低效能。

Apple 在使用寬色彩時提供下列最佳作法：

- `NSColor` 現在使用 sRGB 色彩空間，而且不會再將值放入 `0.0` 至的 `1.0` 範圍。 如果應用程式依賴先前的夾具行為，則必須針對 macOS Sierra 加以修改。
- 使用低層級的 API （例如核心圖形或金屬）來提供影像處理時，應用程式應該使用支援16位浮點值的擴充範圍色彩空間和像素格式。 若有需要，應用程式必須手動將色彩元件值的夾具。
- 核心圖形、核心影像和金屬效能著色器全都提供在兩個色彩空間之間轉換的新方法。

若要深入瞭解，請參閱我們的 [寬色彩指南簡介](~/ios/platform/wide-color.md) 。

<a name="Additional-Framework-Changes"></a>

## <a name="additional-framework-changes"></a>其他架構變更

除了上面所列的主要架構變更和新增專案，Apple 在 macOS Sierra 中進行了許多其他的次要架構變更。

若要深入瞭解，請參閱我們的 [其他架構變更](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md) 指南。

<a name="Deprecated-APIs"></a>

## <a name="deprecated-apis"></a>已被取代的 API

下列 Api 在 macOS Sierra 中已被取代：

- 不再支援 HFS 標準檔案系統。

如需棄用功能和變更的完整清單，請參閱 Apple 的 [macOS v 10.12 API 差異](https://developer.apple.com/library/archive/releasenotes/General/APIDiffsMacOS10_12/index.html) 檔。

## <a name="related-links"></a>相關連結

- [Mac 範例](/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [MacOS 10.12 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)