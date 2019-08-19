---
title: iOS 10 簡介
description: 本文介紹 iOS 10 中適用于 Xamarin iOS 開發人員的所有新的和已修改的 Api 和功能。
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: edc585364df2e0b2129135e7bf5977c33a77a6e0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647429"
---
# <a name="introduction-to-ios-10"></a>iOS 10 簡介

透過新的 iOS 10 SDK, Apple 已包含新的 Api 和服務, 可讓開發人員建立新類別的應用程式和功能。 IOS 應用程式現在可以擴充訊息、Siri、電話和地圖應用程式, 為先前無法使用的使用者提供豐富、吸引人的功能。

如需 iOS 10 的詳細資訊, 請參閱 Apple 的[iOS + 應用程式](https://developer.apple.com/ios/)檔。

## <a name="whats-new-in-ios-10"></a>IOS 10 的新功能

Apple 在 iOS 10 中新增了數個新的 Api 和服務, 以及現有功能的許多增強功能, 包括:

## <a name="adapting-to-the-true-tone-display"></a>適應真正的音調顯示

Apple 的真正音調顯示技術會使用 iOS 裝置中的環境光線感應器, 以動態方式調整顯示器的色彩和濃度, 以符合目前的光源狀況。 iOS 10 提供新的[UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31)金鑰, 可新增至應用程式的`Info.plist`檔案, 並控制真正的色調如何套用標準色彩變換。 

可用的值如下:

- `UIWhitePointAdaptivityStyleStandard`**預設**-使用標準的點 adaptivity。
- `UIWhitePointAdaptivityStyleReading`-用於讀取導向的應用程式。
- `UIWhitePointAdaptivityStyleGame`-用於以遊戲為重點的應用程式。
- `UIWhitePointAdaptivityStyleVideo`-用於以影片為焦點的應用程式。
- `UIWhitePointAdaptivityStylePhoto`-用於以攝影為焦點的應用程式, 其色彩精確度比環境的點調整更重要。

## <a name="app-extensions"></a>應用程式延伸模組

Apple 在 iOS 10 中提供了數個新的應用程式擴充點:

- 呼叫目錄
- 意圖和意圖 UI
- 訊息
- 通知內容
- Notification Services
- 貼紙套件

此外, 協力廠商鍵盤應用程式延伸模組具有下列增強功能:

- `UITextDocumentProxy`類別的新`DocumentInputMode`屬性可以決定檔的輸入語言, 並允許鍵盤擴充功能與該語言對齊。
- 新`HandleInputModeList`的方法可讓鍵盤擴充功能顯示系統的鍵盤選擇器功能表, 以回應所按的地球按鍵。

如需詳細資訊, 請參閱我們的[擴充功能簡介](~/ios/platform/extensions.md)、[訊息應用程式整合](~/ios/platform/message-app-integration/index.md)、[主動式建議簡介](~/ios/platform/search/proactive-suggestions.md)、 [SiriKit 簡介](~/ios/platform/sirikit/index.md)、[使用者通知](~/ios/platform/user-notifications/index.md)和 Apple 的[簡介應用程式擴充程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

## <a name="app-search-enhancements"></a>應用程式搜尋增強功能

IOS 10 中的核心焦點為應用程式搜尋提供了數項增強功能, 例如:

- **集結群眾力量深層連結的熱門程度 (具有差異隱私權)** -提供在搜尋結果中升級深層連結應用程式內容的方法。
- **應用程式內搜尋**-使用新`CSSearchQuery`的類別來提供應用程式內焦點搜尋功能, 類似于郵件、訊息和便箋應用程式的工作方式。
- **搜尋接續**-允許使用者在焦點或 Safari 中開始搜尋, 然後開啟應用程式並繼續進行搜尋。
- **驗證結果的視覺效果**-Apple 的[應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool)現在會顯示網站標記的視覺標記法, 以及 preforming 測試時的深層連結。
- **訊息應用程式映射共用**-允許提供常用的應用程式內映射, 以在訊息中共用 (透過訊息應用程式延伸模組), 以顯示在焦點搜尋中。

若要深入瞭解, 請參閱我們的[應用程式搜尋增強功能](~/ios/platform/search/app-search-enhancements.md)指南。

## <a name="apple-pay-enhancements"></a>Apple Pay 增強功能

Apple 在 iOS 10 中提供了數項增強功能, Apple Pay 可讓使用者透過與 Siri 和 Maps 的互動, 從網站進行安全的付款。

在 iOS 10 中, 已新增數個可與 iOS 和 watchOS 搭配使用的新 Api, 以支援動態付款網路和新的沙箱測試環境。

此外, PassKit 架構已擴充為支援以外的`UIKit` Apple Pay, 讓卡片簽發者可以從其應用程式中呈現其卡片。

若要深入瞭解, 請參閱我們的[Apple Pay 增強功能](~/ios/platform/apple-pay.md)指南。

## <a name="alternate-app-icons"></a>替代的應用程式圖示

Apple 已將數個增強功能新增至 iOS 10.3, 讓應用程式可以管理其圖示:

- `ApplicationIconBadgeNumber`-取得或設定跳板中應用程式圖示的徽章。
- `SupportsAlternateIcons`-如果`true`應用程式有替代的圖示集。
- `AlternateIconName`-傳回目前選取的替代圖示或`null`使用主要圖示的名稱。
- `SetAlternameIconName`-使用此方法可將應用程式的圖示切換至指定的替代圖示。

若要深入瞭解, 請參閱我們的[替代應用程式圖示](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)指南。

## <a name="introduction-to-callkit"></a>CallKit 簡介

IOS 10 中新的 CallKit API 可讓 VOIP 應用程式與 iPhone UI 整合, 並為使用者提供熟悉的介面和經驗。 使用此 API, 使用者可以從 iOS 裝置的鎖定畫面觀看 VOIP 呼叫並與之互動, 並使用電話應用程式的我的最愛和**最近專案**views 來管理連絡人。

此外, CallKit API 可讓您建立應用程式延伸模組, 以便將電話號碼與名稱 (呼叫者識別碼) 建立關聯, 或告知系統應封鎖某個數位 (呼叫封鎖)。

若要深入瞭解, 請參閱我們[的 Callkit 簡介](~/ios/platform/callkit.md)指南。

## <a name="message-app-integration"></a>訊息應用程式整合

iOS 10 允許在與**訊息**應用程式整合的 Xamarin iOS 解決方案中包含訊息應用程式延伸模組, 並為使用者提供新功能。 延伸模組可以傳送文字、貼紙、媒體檔案和互動式訊息。 有兩種類型的訊息應用程式延伸模組可供使用:

- **不乾膠套件**-包含使用者可以新增至訊息的貼紙集合。 您不需要撰寫任何程式碼, 即可建立貼紙套件。
- **IMessage 應用程式**-可以在 [訊息] 應用程式中顯示自訂使用者介面, 以選取貼紙、輸入文字, 包括媒體檔案 (具有選擇性的類型轉換), 以及建立、編輯和傳送互動訊息。

若要深入瞭解, 請參閱我們的[訊息應用程式整合](~/ios/platform/message-app-integration/index.md)指南。

## <a name="news-publisher-enhancements"></a>新聞發行者增強功能

有了 iOS 10, Apple 將可讓來自主要雜誌和新組織的任何人都能註冊並提供內容給 Apple News 應用程式, 並將其發佈到您的產品, 並將其傳遞給您。 若要深入瞭解, 請參閱 Apple 的[新聞資源](https://newsresources.apple.com/)檔。

## <a name="providing-haptic-feedback"></a>提供 Haptic 意見反應

在 iPhone 7 和 iPhone 7 Plus 上, Apple 已包含新的 haptics 回應, 可提供其他方式來實際與使用者互動。 使用新的觸覺意見反應選項來取得使用者的注意並強化其動作。

數個內建的 UI 元素已提供 haptic 的意見反應, 例如選擇器、交換器和滑杆。 iOS 10 現在新增了使用`UIFeedbackGenerator`類別的具象子類別, 以程式設計方式觸發 haptics 的功能。

若要深入瞭解, 請參閱我們[提供的 Haptic 意見](~/ios/user-interface/ios-ui/haptic-feedback.md)反應指南。

## <a name="proactive-suggestions"></a>主動式建議

iOS 10 提供新的方式來驅動應用程式, 方法是允許系統在適當的時間內, 自動向使用者顯示有用的資訊。 就像 iOS 9 能夠使用焦點、遞交和 Siri 建議, 將深入搜尋新增至應用程式, 使用 iOS 10, 應用程式可以公開可由系統從下列位置呈現給使用者的功能:

- 應用程式切換器
- 鎖定畫面
- 了 carplay
- 地圖
- Siri 互動
- QuickType 建議

應用程式會使用[NSUserActivity](xref:Foundation.NSUserActivity)、web 標記、核心焦點、MapKit、媒體播放機和 UIKit 等技術集合, 將此功能公開給系統。

若要深入瞭解, 請參閱《[主動式建議簡介](~/ios/platform/search/proactive-suggestions.md)》指南。

## <a name="request-app-review"></a>要求應用程式檢閱

Ios 10.3 的`RequestReview()`新功能, 方法可讓 ios 應用程式要求使用者對其進行評分或檢查。 雖然此方法可以在使用者體驗中合理的任何時間點進行呼叫, 但審核程式是由 App Store 原則進行控管和處理。 因此, 此方法不一定會顯示警示, 而且永遠不會呼叫以回應使用者動作, 例如點擊按鈕。

若要深入瞭解, 請參閱我們的[要求應用程式審核](~/ios/platform/request-app-review.md)指南。

## <a name="security-and-privacy-enhancements"></a>安全性和隱私權增強功能

Apple 在 iOS 10 中提供了數項安全性和隱私權的增強功能, 可協助開發人員改善其應用程式的安全性, 並確保使用者的隱私權。

因此, 在 iOS 10 (或更新版本) 上執行的應用程式必須在其`Info.plist`檔案中輸入一或多個隱私權特定金鑰, 以靜態方式宣告其意圖, 以存取特定功能或使用者資訊, 並向使用者說明應用程式想要取得存取權的原因。

若要深入瞭解, 請參閱我們的[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)指南。

## <a name="sirikit"></a>SiriKit

SiriKit 是 iOS 10 的新手, 可讓 Xamarin iOS 應用程式提供在 iOS 裝置上使用 Siri 的使用者可存取的服務。 這項功能是使用新的**意圖**和**意圖 UI**架構, 在一或多個應用程式延伸模組中提供。

SiriKit 支援下列服務網域:

- 音訊或影片通話。
- 預約。
- 管理 workouts。
- 關鍵.
- 正在搜尋相片。
- 傳送或接收付款。

當使用者提出包含其中一個應用程式延伸模組服務的 Siri 要求時, SiriKit 會將描述使用者要求的**意圖**物件以及任何支援的資料傳送給此延伸模組。 然後, 應用程式延伸模組會針對指定的**意圖**產生適當的**回應**物件, 詳述延伸模組如何處理要求。

雖然 Siri 通常會處理所有使用者互動, 但應用程式延伸模組可以使用**意圖 UI**架構來呈現豐富的自訂使用者介面, 其中包含應用程式的商標和其他資訊。

若要深入瞭解, 請參閱我們[的 SiriKit 簡介](~/ios/platform/sirikit/index.md)指南。

## <a name="speech-recognition"></a>語音辨識

iOS 10 包含新的語音 API, 可讓應用程式支援連續語音辨識和轉譯語音 (從即時或錄製的音訊串流) 到文字。

因為語音辨識需要在 Apple 伺服器上傳輸和暫時儲存資料, 所以應用程式_必須_在其`NSSpeechRecognitionUsageDescription` `Info.plist`檔案中包含金鑰並呼叫,以要求使用者的許可權來執行辨識`SFSpeechRecognizer.RequestAutorization`方法。

若要深入瞭解, 請參閱我們[的語音辨識簡介](~/ios/platform/speech.md)指南。

## <a name="user-notifications"></a>使用者通知

IOS 10 的新手, 使用者通知架構允許傳遞和處理本機和遠端通知。 藉由使用此架構, 應用程式或應用程式延伸模組可以藉由指定一組條件 (例如位置或一天的時間) 來排程本機通知的傳遞。

此外, 應用程式或延伸模組可以在本機和遠端通知傳遞給使用者的 iOS 裝置時, 同時接收 (而且可能會修改)。

新的使用者通知 UI 架構可讓應用程式或應用程式延伸模組在呈現給使用者時, 自訂本機和遠端通知的外觀。

若要深入瞭解, 請參閱我們的[使用者通知架構](~/ios/platform/user-notifications/index.md)指南。

## <a name="video-subscriber-account"></a>影片訂閱者帳戶

針對 iOS 10 的新功能, 影片訂閱者帳戶架構可讓支援已驗證串流或隨選影片的應用程式使用使用者的單一登入體驗, 向其纜線或衛星電視提供者進行驗證。

## <a name="wide-color"></a>寬色域

iOS 10 延伸了擴充範圍像素格式的支援, 以及整個系統的寬範圍色彩空間, 包括核心圖形、核心影像、金屬和 AVFoundation 等架構。 在整個圖形堆疊中提供這種行為, 即可進一步分階段減緩具有寬色彩顯示的裝置支援。

此外, [UIKit](xref:UIKit)已修改為在新的擴充的**sRGB** colorspace 中工作, 讓您更輕鬆地混合寬色 gamuts 中的色彩, 而不會大幅降低效能。

使用寬色彩時, Apple 提供下列最佳作法:

- [UIColor](xref:UIKit.UIColor)現在會使用 sRGB 色彩空間, 而不會再將值設`0.0`為`1.0` to 範圍。 如果應用程式依賴先前的夾具行為, 則必須針對 iOS 10 進行修改。
- 在 iPad Pro 上執行自訂`UIView`繪圖時, 將會為 sRGB 色彩空間設定繪圖環境。
- 如果應用程式執行的自訂`UIImages`轉譯, 請使用新的[UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer)類別來指定擴充範圍或標準範圍格式的使用。
- 當使用低層級的 API (例如核心圖形或金屬) 來提供影像處理時, 開發人員應該使用支援16位浮點值的擴充範圍色彩空間和像素格式。 必要時, 開發人員必須手動將色彩元件值設為 [夾具]。
- 核心圖形、核心影像和金屬效能著色器全都提供了新的方法, 可在兩個色彩空間之間進行轉換。

若要深入瞭解, 請參閱我們[的寬色彩指南簡介](~/ios/platform/wide-color.md)。

## <a name="widget-enhancements"></a>Widget 增強功能

Apple 在 Widget 系統中引進了數項增強功能, 以確保小工具在任何存在於新 iOS 10 鎖定畫面上的背景上看起來很棒。 [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect)屬性已被取代, 並已被新的[WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect)或[WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)屬性取代。 此外, widget 現在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)屬性, 可讓開發人員描述有多少內容可供使用, 並可讓使用者展開和折迭內容。

若要深入瞭解, 請參閱我們的[搜尋和主畫面 Widget 增強功能](~/ios/platform/search/widgets.md)指南。

## <a name="additional-framework-changes"></a>其他架構變更

除了上面所列的主要架構變更和新增功能之外, Apple 也在 iOS 10 中進行了許多其他的次要架構變更。

若要深入瞭解, 請參閱我們的[其他架構變更](~/ios/platform/introduction-to-ios10/additional-framework-changes.md)指南。

## <a name="deprecated-apis"></a>已被取代的 API

下列 Api 在 iOS 10 中已被取代:

- 在`CKDiscoverAllContactsOperation`iOS 10 `CKDiscoverUserInfosOperation`的`CKFetchRecordChangesOperation` CloudKit 中,、 `CKDiscoveredUserInfo`和類別已被取代。 請改用[CKDiscoverAllUserIdentitiesOperation](xref:CloudKit.CKDiscoverUserIdentitiesOperation)、 [CKUserIdentity](xref:CloudKit.CKUserIdentity)和[CKFetchRecordZoneChangesOperation](xref:CloudKit.CKFetchRecordZoneChangesOperation)類別 (支援記錄共用)。
- 數個[CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) api (例如區域型和以查詢為基礎的訂用帳戶) 已被取代。 請改用[CKRecordZoneSubscription](xref:CloudKit.CKRecordZoneSubscription)和[CKQuerySubscription](xref:CloudKit.CKQuerySubscription) api。
- 與普及內容相關的[NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator)符號已被取代。
- [UIViewController](xref:UIKit.UIViewController) 類別中的`ADBannerView``ADInterstitialAd`和相關符號已被取代。
- 與浮點值相關的[SKUniform](https://developer.apple.com/reference/spritekit/skuniform)符號已被取代。
- UIKit `UILocalNotification`的`UIMutableUserNotificationAction`、、 `UIMutableUserNotificationCategory` 、和`UIUserNotificationSettings`類別已被取代。 `UIUserNotificationCategory` `UIUserNotificationAction` 請改用[使用者通知](#user-notifications)架構。
- `HandleActionForLocalNotification` 、`HandleActionForRemoteNotification`和WatchKit方法已被取代`DidReceiveRemoteNotification`。 `DidReceiveLocalNotification` 請改用`DidReceiveNotification`和方法。 `HandleActionForNotification`
- [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)的`DidReceiveLocalNotification`和`DidReceiveRemoteNotification`方法已被取代。 建立[UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate)的實例, 以執行適當的方法, 並將它指派`Delegate`給[UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter)物件的屬性。
- **Game Center 應用程式**已被取代, 並已從 iOS 移除。 如果應用程式使用 GameKit, 它_必須_提供自己的介面來顯示 GameKit 功能, 例如排行榜等。

如需棄用功能的完整清單, 請參閱 Apple 的[ios 9.3 至 ios 10.0 API 差異](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html)檔。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
