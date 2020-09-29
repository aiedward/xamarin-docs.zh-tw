---
title: iOS 10 簡介
description: 本文介紹適用于 Xamarin 的 iOS 10 中所有新的和修改過的 Api 和功能。 iOS 開發人員。
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 7bd7387302c1ec8b37ae4ffe3465fb1becece429
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436213"
---
# <a name="introduction-to-ios-10"></a>iOS 10 簡介

Apple 提供新的 iOS 10 SDK，可讓開發人員建立新類別的應用程式和功能。 IOS 應用程式現在可以擴充訊息、Siri、電話和地圖應用程式，為先前無法使用的使用者提供豐富且吸引人的功能。

如需 iOS 10 的詳細資訊，請參閱 Apple 的 [ios + 應用程式](https://developer.apple.com/ios/) 檔。

## <a name="whats-new-in-ios-10"></a>IOS 10 的新功能

Apple 已在 iOS 10 中新增數個新的 Api 和服務，以及現有功能的許多增強功能，包括：

## <a name="adapting-to-the-true-tone-display"></a>適應真色調顯示

Apple 的真實語氣顯示器技術會使用 iOS 裝置中的環境光源感應器，以動態方式調整顯示器的色彩和濃度，以符合目前的光源狀況。 iOS 10 提供新的 [UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31) 金鑰，可新增至應用程式的檔案 `Info.plist` ，並控制真正的色調如何套用標準色彩移位。 

有下列可用的值：

- `UIWhitePointAdaptivityStyleStandard`**預設值**-使用標準的點適應性。
- `UIWhitePointAdaptivityStyleReading` -用於以讀取為主的應用程式。
- `UIWhitePointAdaptivityStyleGame` -用於以遊戲為主的應用程式。
- `UIWhitePointAdaptivityStyleVideo` -適用于影片導向的應用程式。
- `UIWhitePointAdaptivityStylePhoto` -用於以攝影為焦點的應用程式，其色彩精確度比環境的白色點調整更重要。

## <a name="app-extensions"></a>應用程式擴充功能

Apple 在 iOS 10 中提供了數個新的應用程式擴充點：

- 呼叫目錄
- 意圖和意圖 UI
- 訊息
- 通知內容
- Notification Services
- 貼紙套件

此外，協力廠商鍵盤應用程式擴充功能有下列增強功能：

- 類別的新 `DocumentInputMode` 屬性 `UITextDocumentProxy` 可以決定檔的輸入語言，並允許鍵盤擴充功能與該語言相符。
- 新 `HandleInputModeList` 方法可讓鍵盤擴充功能顯示系統的鍵盤選擇器功能表，以回應所要點擊的地球按鍵。

如需詳細資訊，請參閱我們的 [延伸模組簡介](~/ios/platform/extensions.md)、 [訊息應用程式整合](~/ios/platform/message-app-integration/index.md)、 [主動式建議簡介](~/ios/platform/search/proactive-suggestions.md)、 [SiriKit 簡介](~/ios/platform/sirikit/index.md)、 [使用者通知簡介](~/ios/platform/user-notifications/index.md) 和 Apple 的 [應用程式延伸程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

## <a name="app-search-enhancements"></a>應用程式搜尋增強功能

IOS 10 中的核心焦點提供數個應用程式搜尋增強功能，例如：

- **使用差異隱私權) 集結群眾力量深層連結熱門程度 (** -提供在搜尋結果中推廣深層連結應用程式內容的方式。
- **應用程式內搜尋** -使用新的 `CSSearchQuery` 類別來提供應用程式內焦點搜尋功能，類似于 Mail、Messages 和 Notes 應用程式的運作方式。
- **搜尋接續** -可讓使用者在焦點或 Safari 中開始搜尋，然後開啟應用程式並繼續進行搜尋。
- **驗證結果** 的視覺效果-Apple 的 [應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool) 現在會在 preforming 測試時，顯示網站標記和深層連結的視覺標記法。
- **訊息應用程式映射共用** -讓提供的熱門應用程式內映射可透過訊息應用程式延伸模組進行共用 (，) 出現在焦點搜尋中。

若要深入瞭解，請參閱我們的 [應用程式搜尋增強功能](~/ios/platform/search/app-search-enhancements.md) 指南。

## <a name="apple-pay-enhancements"></a>Apple Pay 增強功能

Apple 針對 iOS 10 中的 Apple Pay 進行了數項增強，可讓使用者從網站進行安全的付款，並透過 Siri 和地圖進行互動。

使用 iOS 10，新增了數個新的 Api，可與 iOS 和 watchOS 搭配使用，以支援動態付款網路和新的沙箱測試環境。

此外，PassKit 架構已擴充為支援以外的 Apple Pay， `UIKit` 可讓卡片簽發者在其應用程式內呈現其卡。

若要深入瞭解，請參閱我們的 [Apple Pay 增強功能](~/ios/platform/apple-pay.md) 指南。

## <a name="alternate-app-icons"></a>替代的應用程式圖示

Apple 已在 iOS 10.3 中新增數個增強功能，可讓應用程式管理其圖示：

- `ApplicationIconBadgeNumber` -取得或設定跳板中應用程式圖示的徽章。
- `SupportsAlternateIcons` -如果 `true` 應用程式具有替代的圖示集。
- `AlternateIconName` -傳回目前選取之替代圖示的名稱， `null` 如果使用主要圖示則為。
- `SetAlternameIconName` -使用此方法將應用程式的圖示切換至指定的替代圖示。

若要深入瞭解，請參閱我們的 [替代應用程式圖示](~/ios/app-fundamentals/images-icons/alternate-app-icons.md) 指南。

## <a name="introduction-to-callkit"></a>CallKit 簡介

IOS 10 中的新 CallKit API 提供了一種方法，可讓 VOIP 應用程式與 iPhone UI 整合，並提供熟悉的介面和體驗給終端使用者。 使用此 API 時，使用者可以從 iOS 裝置的鎖定畫面來查看並與 VOIP 通話進行互動，並使用手機應用程式的 [我的最愛] 和 [**最近專案** **]** 視圖來管理連絡人。

此外，CallKit API 可讓您建立應用程式延伸模組，以將電話號碼與名稱 (呼叫者識別碼產生關聯) 或告知系統 (呼叫封鎖) 時應封鎖的數位。

若要深入瞭解，請參閱我們的 [Callkit 指南簡介](~/ios/platform/callkit.md) 。

## <a name="message-app-integration"></a>訊息應用程式整合

iOS 10 允許在與 **訊息** 應用程式整合的 Xamarin iOS 解決方案中包含訊息應用程式延伸模組，並為使用者提供新功能。 延伸模組可以傳送文字、貼紙、媒體檔案和互動式訊息。 有兩種類型的訊息應用程式延伸模組可供使用：

- **貼紙套件** ：包含使用者可新增至訊息的貼紙集合。 您無須撰寫任何程式碼，即可建立不乾膠貼紙套件。
- **IMessage 應用程式** -可以在訊息應用程式內顯示自訂消費者介面，以選取貼紙、輸入文字，包括具有選擇性類型轉換 (的媒體檔案) 以及建立、編輯和傳送互動訊息。

若要深入瞭解，請參閱我們的 [訊息應用程式整合](~/ios/platform/message-app-integration/index.md) 指南。

## <a name="news-publisher-enhancements"></a>新聞發行者增強功能

使用 iOS 10，Apple 將可讓來自主要雜誌和新組織的任何人都能註冊和產品，並將內容傳遞至 Apple News 應用程式。 若要深入瞭解，請參閱 Apple 的 [新聞資源](https://newsresources.apple.com/) 檔。

## <a name="providing-haptic-feedback"></a>提供 Haptic 意見反應

在 iPhone 7 和 iPhone 7 Plus 上，Apple 已包含新的 haptics 回應，可提供實際參與使用者的其他方式。 使用新的 tactile 意見反應選項，可讓使用者注意並強化其動作。

數個內建的 UI 元素已提供 haptic 的意見反應，例如選擇器、開關和滑杆。 iOS 10 現在新增了以程式設計方式使用類別的具體子類別來觸發 haptics 的功能 `UIFeedbackGenerator` 。

若要深入瞭解，請參閱我們 [提供的 Haptic 意見](~/ios/user-interface/ios-ui/haptic-feedback.md) 反應指南。

## <a name="proactive-suggestions"></a>主動式建議

iOS 10 提供新的方式來推動應用程式的互動，方法是讓系統在適當的時間，自動向使用者呈現有用的資訊。 就像 iOS 9 一樣，使用焦點、遞交與 Siri 建議將深層搜尋新增至應用程式，應用程式可以在下列位置公開可由系統向使用者呈現的功能：

- 應用程式切換器
- 鎖定畫面
- CarPlay
- 地圖
- Siri 互動
- QuickType 建議

應用程式會使用一系列的技術（例如 [NSUserActivity](xref:Foundation.NSUserActivity)、web 標記、核心焦點、MapKit、媒體播放機和 UIKit），將這項功能公開給系統。

若要深入瞭解，請參閱我們 [的主動式建議指南簡介](~/ios/platform/search/proactive-suggestions.md) 。

## <a name="request-app-review"></a>要求應用程式檢閱

IOS 10.3 的新功能，此 `RequestReview()` 方法可讓 ios 應用程式要求使用者對其進行評分或複習。 雖然可以在使用者體驗中有意義的任何時間點呼叫這個方法，但是審核程式是由 App Store 原則來控管和處理。 如此一來，此方法可能會或可能不會顯示警示，而且永遠不會呼叫以回應使用者動作，例如點擊按鈕。

若要深入瞭解，請參閱我們的 [要求應用程式審查](~/ios/platform/request-app-review.md) 指南。

## <a name="security-and-privacy-enhancements"></a>安全性與隱私權的增強功能

Apple 針對 iOS 10 中的安全性和隱私權進行了許多增強功能，可協助開發人員改善其應用程式的安全性，並確保使用者的隱私權。

因此，在 iOS 10 (或更新) 版本上執行的應用程式，必須以靜態方式宣告其意圖來存取特定功能或使用者資訊，方法是在其檔案中輸入一或多個隱私權特定金鑰 `Info.plist` ，向使用者說明應用程式希望取得存取權的原因。

若要深入瞭解，請參閱我們的 [安全性和隱私權增強](~/ios/app-fundamentals/security-privacy.md) 指南。

## <a name="sirikit"></a>SiriKit

SiriKit 是 iOS 10 的新功能，可讓 Xamarin iOS 應用程式提供可讓使用者在 iOS 裝置上使用 Siri 存取的服務。 這項功能會在一或多個使用新 **意圖** 和 **意圖 UI** 架構的應用程式擴充功能中提供。

SiriKit 支援下列服務網域：

- 正在呼叫音訊或影片。
- 預約。
- 管理 workouts。
- 消息。
- 正在搜尋相片。
- 傳送或接收款項。

當使用者要求包含其中一個應用程式延伸模組服務的 Siri 時，SiriKit 會傳送一個 **意圖** 物件給延伸模組，該物件會描述使用者的要求以及任何支援的資料。 然後，應用程式延伸模組會為指定的**意圖**產生適當的**回應**物件，詳述延伸模組如何處理要求。

雖然 Siri 通常會處理所有使用者互動，但應用程式延伸模組可以使用 **意圖 UI** 架構來呈現豐富的自訂消費者介面，其中包含應用程式的商標和其他資訊。

若要深入瞭解，請參閱我們的 [SiriKit 指南簡介](~/ios/platform/sirikit/index.md) 。

## <a name="speech-recognition"></a>語音辨識

iOS 10 包含新的語音 API，可讓應用程式從即時或錄製的音訊串流 () 為文字，以支援連續的語音辨識和轉譯語音。

由於語音辨識需要在 Apple 伺服器上傳輸和暫存資料，因此應用程式 _必須_ `NSSpeechRecognitionUsageDescription` 在其檔案中包含金鑰 `Info.plist` 並呼叫方法，以要求使用者執行辨識的許可權 `SFSpeechRecognizer.RequestAutorization` 。

若要深入瞭解，請參閱我們 [的語音辨識指南簡介](~/ios/platform/speech.md) 。

## <a name="user-notifications"></a>使用者通知

使用者通知架構是 iOS 10 的新手，可提供本機和遠端通知的傳遞和處理。 使用此架構，應用程式或應用程式擴充功能可以藉由指定一組條件（例如，一天中的位置或時間）來排程本機通知的傳遞。

此外，應用程式或延伸模組可以接收 (，而且可能會在將本機和遠端通知傳遞給使用者的 iOS 裝置時，修改) 。

新的使用者通知 UI 架構可讓應用程式或應用程式延伸模組在呈現給使用者時，自訂本機和遠端通知的外觀。

若要深入瞭解，請參閱我們的 [使用者通知架構](~/ios/platform/user-notifications/index.md) 指南。

## <a name="video-subscriber-account"></a>影片訂閱者帳戶

影片訂閱者帳戶架構是 iOS 10 的新功能，可讓支援已驗證串流或隨選影片的應用程式使用使用者的單一登入體驗來驗證其纜線或衛星電視提供者。

## <a name="wide-color"></a>寬色域

iOS 10 在整個系統中延伸了延伸範圍像素格式和寬範圍色彩空間的支援，包括核心圖形、核心影像、金屬和 AVFoundation 等架構。 在整個圖形堆疊中提供這項行為，可進一步分階段減緩支援彩色顯示器的裝置。

此外， [UIKit](xref:UIKit) 已經過修改，可在新的擴充的 **sRGB** colorspace 中工作，讓您更輕鬆地混合寬色彩 gamuts 中的色彩，而不會大幅降低效能。

Apple 在使用寬色彩時提供下列最佳作法：

- [UIColor](xref:UIKit.UIColor) 現在會使用 sRGB 色彩空間，而且不會再將值放入 `0.0` 至的 `1.0` 範圍。 如果應用程式依賴先前的夾具行為，則必須針對 iOS 10 加以修改。
- 在 iPad Pro 上執行自訂繪圖時，會為 sRGB 色彩空間設定繪圖環境 `UIView` 。
- 如果應用程式執行的自訂轉譯 `UIImages` ，請使用新的 [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) 類別來指定延伸範圍或標準範圍格式的使用。
- 使用低層級的 API （例如核心圖形或金屬）來提供影像處理時，開發人員應該使用支援16位浮點值的擴充範圍色彩空間和像素格式。 在必要時，開發人員必須手動將色彩元件值進行夾具。
- 核心圖形、核心影像和金屬效能著色器全都提供在兩個色彩空間之間轉換的新方法。

若要深入瞭解，請參閱我們的 [寬色彩指南簡介](~/ios/platform/wide-color.md) 。

## <a name="widget-enhancements"></a>Widget 增強功能

Apple 導入了多個 Widget 系統的增強功能，可確保 Widget 在新的 iOS 10 鎖定畫面上的任何背景都看起來很棒。 [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect)屬性已被取代，並已取代為新的[WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect)或[WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)屬性。 此外，widget 現在包含 [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) 屬性，可讓開發人員描述有多少內容可用，並允許使用者展開和折迭內容。

若要深入瞭解，請參閱我們的 [搜尋和主畫面 Widget 增強功能](~/ios/platform/search/widgets.md) 指南。

## <a name="additional-framework-changes"></a>其他架構變更

除了上面所列的主要架構變更和新增專案，Apple 在 iOS 10 中進行了許多其他的次要架構變更。

若要深入瞭解，請參閱我們的 [其他架構變更](~/ios/platform/introduction-to-ios10/additional-framework-changes.md) 指南。

## <a name="deprecated-apis"></a>已被取代的 API

以下是 iOS 10 中已淘汰的 Api：

- `CKDiscoverAllContactsOperation` `CKDiscoveredUserInfo` `CKDiscoverUserInfosOperation` `CKFetchRecordChangesOperation` 在 iOS 10 的 CloudKit 中，、和類別已被取代。 使用 [CKDiscoverAllUserIdentitiesOperation](xref:CloudKit.CKDiscoverUserIdentitiesOperation)、 [CKUserIdentity](xref:CloudKit.CKUserIdentity) 和 [CKFetchRecordZoneChangesOperation](xref:CloudKit.CKFetchRecordZoneChangesOperation) 類別 (可改為支援記錄共用) 。
- 數個 [CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) api (例如以區域為基礎和以查詢為基礎的訂用帳戶) 已淘汰。 請改用 [CKRecordZoneSubscription](xref:CloudKit.CKRecordZoneSubscription) 和 [CKQuerySubscription](xref:CloudKit.CKQuerySubscription) api。
- 與無所不在內容相關的[NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator)符號已淘汰。
- `ADBannerView`以及 `ADInterstitialAd` [UIViewController](xref:UIKit.UIViewController) 類別中的相關符號已被取代。
- 與浮點值相關的[SKUniform](https://developer.apple.com/reference/spritekit/skuniform)符號已被取代。
- `UILocalNotification`UIKit 的、、 `UIMutableUserNotificationAction` `UIMutableUserNotificationCategory` 、 `UIUserNotificationAction` `UIUserNotificationCategory` 和 `UIUserNotificationSettings` 類別已被取代。 請改用 [使用者通知](#user-notifications) 架構。
- `HandleActionForLocalNotification`、 `HandleActionForRemoteNotification` `DidReceiveLocalNotification` 和 `DidReceiveRemoteNotification` WatchKit 方法已被取代。 請改用 `HandleActionForNotification` 和 `DidReceiveNotification` 方法。
- `DidReceiveLocalNotification`WKExtensionDelegate 的和 `DidReceiveRemoteNotification` 方法已[WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)被取代。 建立 [UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) 的實例，此實例會執行適當的方法，並將它指派給 `Delegate` [UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter) 物件的屬性。
- **Game Center 的應用程式**已被取代，並已從 iOS 移除。 如果應用程式使用 GameKit，它 _必須_ 有自己的介面，以顯示 GameKit 功能，例如排行榜等等。

如需完整的棄用功能清單，請參閱 Apple 的 [ios 9.3 至 ios 10.0 API 差異](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html) 檔。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)