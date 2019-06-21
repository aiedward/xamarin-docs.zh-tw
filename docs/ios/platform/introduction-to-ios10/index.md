---
title: iOS 10 簡介
description: 這篇文章會介紹的所有新的及修改 Api 和功能可在 iOS 10 中適用於 Xamarin.iOS 開發人員。
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: e84f5acdd1660633521d1a99325155b614b87e1b
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268902"
---
# <a name="introduction-to-ios-10"></a>iOS 10 簡介

使用新的 iOS 10 SDK，Apple 已包含新的 Api 和服務，可讓開發人員建立新的類別目錄的應用程式和功能。 IOS 應用程式現在可以擴充訊息、 Siri、 電話和對應的應用程式提供豐富且吸引人的功能已無法使用先前的使用者。

如需有關 iOS 10 的詳細資訊，請參閱 Apple [iOS 應用程式 +](https://developer.apple.com/ios/)文件。

## <a name="whats-new-in-ios-10"></a>新功能 iOS 10

Apple 已加入數個新的 Api 和服務，在 iOS 10 及現有功能，包括許多增強功能：

## <a name="adapting-to-the-true-tone-display"></a>適應，則為 True 的色調顯示

Apple 的則為 True 的色調顯示技術使用在 iOS 裝置的環境的光感應器，以動態方式調整色彩和濃度，以符合目前的光源狀況的顯示。 iOS 10 提供的新[UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31)可新增至應用程式的金鑰`Info.plist`檔案，並控制，則為 True 的色調套用標準色彩排班的方式。 

可用的值如下：

- `UIWhitePointAdaptivityStyleStandard` **預設**-使用標準的白色點 adaptivity。
- `UIWhitePointAdaptivityStyleReading` -用於讀取為主的應用程式。
- `UIWhitePointAdaptivityStyleGame` -用於遊戲為主的應用程式。
- `UIWhitePointAdaptivityStyleVideo` -用於視訊為主的應用程式。
- `UIWhitePointAdaptivityStylePhoto` -攝影為主的應用程式的情況下使用色彩逼真度比環境的白色點調整更重要。

## <a name="app-extensions"></a>應用程式擴充功能

Apple 提供數個新應用程式的擴充點在 iOS 10:

- 呼叫目錄
- 意圖和意圖 UI
- 訊息
- 通知內容
- Notification Services
- 貼紙的組件

此外，第 3 個合作對象鍵盤應用程式擴充功能會有下列增強功能：

- 新`DocumentInputMode`屬性`UITextDocumentProxy`類別可以判斷文件的輸入的語言，讓鍵盤擴充功能，以配合該語言。
- 新`HandleInputModeList`方法可讓全球各地索引鍵所點選的回應顯示系統的 [鍵盤] 選擇器功能表的鍵盤擴充功能。

如需詳細資訊，請參閱我們[延伸模組簡介](~/ios/platform/extensions.md)，[訊息應用程式整合](~/ios/platform/message-app-integration/index.md)，[主動式建議簡介](~/ios/platform/search/proactive-suggestions.md)， [SiriKit 簡介](~/ios/platform/sirikit/index.md)，[簡介使用者通知](~/ios/platform/user-notifications/index.md)與 Apple[應用程式擴充功能的程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

## <a name="app-search-enhancements"></a>應用程式搜尋增強功能

在 iOS 10 中的核心焦點搜尋應用程式提供數個增強功能，例如：

- **群眾外包深層連結的熱門程度 （含差異隱私權）** -可用來提升搜尋結果中的深層連結應用程式內容。
- **應用程式內搜尋**-使用新`CSSearchQuery`類別，以提供應用程式內的郵件、 訊息和資訊的應用程式的運作方式類似的 Spotlight 搜尋能力。
- **搜尋接續**-可讓使用者在焦點或 Safari 中開始搜尋，然後開啟應用程式，並繼續搜尋。
- **驗證結果的視覺效果**-Apple[應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool)preforming 測試時，現在會顯示網站的標記和深層連結的視覺表示法。
- **訊息應用程式映像共用**-讓 Spotlight 搜尋中出現共用 （透過訊息應用程式的擴充功能） 的訊息中提供的熱門應用程式內映像。

若要深入了解，請參閱我們[應用程式搜尋增強功能](~/ios/platform/search/app-search-enhancements.md)指南。

## <a name="apple-pay-enhancements"></a>Apple Pay 的增強功能

Apple 有了許多改進功能到 Apple Pay 在 iOS 10 可讓使用者做出安全的付款，從網站，並透過與 Siri 與地圖互動。

使用 iOS 10，數個新的 Api 已新增使用 iOS 和 watchOS 支援動態付款網路與新的沙箱測試環境。

此外，已擴充 「 PassKit 架構支援 Apple Pay 之外`UIKit`，並允許以呈現來自其卡片，他們的應用程式內的卡片簽發者。

若要深入了解，請參閱我們[Apple 用多少付多少的增強功能](~/ios/platform/apple-pay.md)指南。

## <a name="alternate-app-icons"></a>替代的應用程式圖示

Apple iOS 10.3 可讓應用程式來管理它的圖示已新增數個增強功能：

- `ApplicationIconBadgeNumber` -取得或設定跳板系列中的應用程式圖示的徽章。
- `SupportsAlternateIcons` -如果`true`應用程式有另一組的圖示。
- `AlternateIconName` -傳回目前選取的替代圖示的名稱或`null`如果使用的主要圖示。
- `SetAlternameIconName` -使用這個方法來指定替代的圖示切換應用程式的圖示。

若要深入了解，請參閱我們[替代的應用程式圖示](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)指南。

## <a name="introduction-to-callkit"></a>CallKit 簡介

在 iOS 10 中新的 CallKit API 提供 VOIP 與 iPhone UI 整合並提供熟悉的介面給終端使用者體驗的應用程式的方式。 使用此 API 時，使用者可以檢視和互動 VOIP 電話從 iOS 裝置鎖定畫面及管理使用的手機應用程式的連絡人**我的最愛**並**最近**檢視。

此外，CallKit API 會提供建立應用程式延伸模組，其可以 （呼叫者識別碼） 的名稱相關聯的電話號碼，或告知系統應該是數字，可讓您封鎖 （封鎖呼叫） 的能力。

若要深入了解，請參閱我們[Callkit 簡介](~/ios/platform/callkit.md)指南。

## <a name="message-app-integration"></a>訊息應用程式整合

iOS 10 可讓與整合的 Xamarin.iOS 方案中的訊息應用程式擴充功能納入**訊息**給使用者的應用程式並為您介紹新功能。 延伸模組可以傳送文字、 貼紙、 媒體檔案和互動式訊息。 有兩種類型的訊息應用程式擴充功能：

- **貼紙套件**-包含使用者可以新增至訊息的貼紙的集合。 您可以建立貼紙的組件，而不需要撰寫任何程式碼。
- **iMessage 應用程式**-可以呈現自訂使用者介面中選取貼紙、 輸入文字，包括 （具有選擇性型別轉換） 的媒體檔案與建立、 編輯和互動傳送的訊息應用程式。

若要深入了解，請參閱我們[訊息的應用程式整合](~/ios/platform/message-app-integration/index.md)指南。

## <a name="news-publisher-enhancements"></a>新聞發行者增強功能

與 iOS 10，Apple 會允許任何人從主要的雜誌和新組織部落客和獨立的發行者，以註冊和產品，並將內容傳遞給 Apple News 應用程式。 若要進一步了解，請參閱 Apple[新聞資源](https://newsresources.apple.com/)文件。

## <a name="providing-haptic-feedback"></a>提供 Haptic 意見反應

IPhone 7 和 iPhone 上 7 Plus，Apple 已包含了提供實際接觸使用者的其他方式的新 haptics 回應。 取得使用者的注意力，並強化其動作中使用新的 tactile 的意見反應選項。

數個內建的 UI 項目已提供 haptic 意見反應，例如選擇器、 切換和滑桿。 iOS 10 現在將能夠以程式設計方式觸發程序使用的具體子類別的 haptics`UIFeedbackGenerator`類別。

若要深入了解，請參閱我們[提供 Haptic 意見反應](~/ios/user-interface/ios-ui/haptic-feedback.md)指南。

## <a name="proactive-suggestions"></a>主動式建議

iOS 10 會呈現駕駛 engagement 應用程式的新方式讓系統主動很有幫助的資訊會自動向使用者呈現在適當的時間。 就如同 iOS 9 會提供能夠將深層搜尋新增至應用程式與 iOS 10 應用程式可以公開 （expose) 功能，可以呈現給使用者，從系統中的下列位置使用 Spotlight、 遞移式及 Siri 建議：

- 應用程式切換器
- 在鎖定畫面
- CarPlay
- 地圖
- Siri 互動
- QuickType 建議

應用程式未公開這項功能才能使用的技術集合，例如系統[NSUserActivity](xref:Foundation.NSUserActivity)，網頁標記中，核心焦點、 MapKit、 Media Player 和 UIKit。

若要深入了解，請參閱我們[主動式建議簡介](~/ios/platform/search/proactive-suggestions.md)指南。

## <a name="request-app-review"></a>要求應用程式檢閱

剛接觸 iOS 10.3，`RequestReview()`方法可讓 iOS 應用程式以要求使用者評等或檢閱它。 時隨時派得上用場的使用者經驗，就可以呼叫這個方法，檢閱程序將控管中，並由應用程式存放區原則。 如此一來，這個方法可能會或可能不會顯示警示，並永遠不可叫用以回應使用者動作，例如點選按鈕，即可。

若要深入了解，請參閱我們[要求的應用程式審查](~/ios/platform/request-app-review.md)指南。

## <a name="security-and-privacy-enhancements"></a>安全性和隱私權增強功能

Apple 已對安全性和隱私權，在 iOS 10 可協助開發人員改進其應用程式的安全性，並確保使用者的隱私權的數個增強功能。

如此一來，在 iOS 10 （或更新版本） 上執行的應用程式必須以靜態方式會宣告輸入一或多個隱私權特定中索引鍵來存取特定功能或使用者資訊的意圖其`Info.plist`說明為什麼應用程式想要存取使用者的檔案。

若要深入了解，請參閱我們[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)指南。

## <a name="sirikit"></a>SiriKit

新增至 iOS 10，SiriKit 可讓 Xamarin.iOS 應用程式提供的 iOS 裝置上使用 Siri 使用者可以存取的服務。 這項功能提供一或多個應用程式擴充功能使用新**意圖**並**Intents UI**架構。

SiriKit 支援下列服務網域：

- 音訊或視訊通話。
- 預約好好體驗吧。
- 管理健身。
- 訊息傳遞。
- 正在搜尋相片。
- 傳送或接收付款。

當使用者提出的要求使用 Siri 牽涉到的其中一個應用程式擴充功能的服務時，SiriKit 傳送延伸模組**意圖**物件，描述使用者的要求，以及任何支援的資料。 應用程式擴充功能則會產生適當**回應**物件的給定**意圖**，詳細說明如何擴充功能來處理要求。

雖然 Siri 通常會處理所有使用者互動，也可以使用應用程式擴充功能**意圖 UI**架構來呈現豐富的自訂使用者介面提供應用程式的商標和其他資訊。

若要深入了解，請參閱我們[SiriKit 簡介](~/ios/platform/sirikit/index.md)指南。

## <a name="speech-recognition"></a>語音辨識

iOS 10 包含新的語音 API 可讓應用程式，以支援連續的語音辨識和轉譯的語音功能 （即時或錄製音訊資料流） 成為文字。

因為語音辨識需要傳輸與 Apple 的伺服器，應用程式上的資料暫存儲存體_必須_要求使用者的權限來執行辨識加`NSSpeechRecognitionUsageDescription`中的索引鍵及其`Info.plist`檔案，並呼叫`SFSpeechRecognizer.RequestAutorization`方法。

若要深入了解，請參閱我們[語音辨識簡介](~/ios/platform/speech.md)指南。

## <a name="user-notifications"></a>使用者通知

新 iOS 10，使用者通知架構可讓傳遞和本機及遠端通知的處理。 使用此架構，應用程式或應用程式擴充功能可以排程傳遞本機通知藉由指定一組條件，例如位置或一天的時間。

此外，應用程式或延伸模組可以接收 （並可能修改） 本機及遠端通知時傳送至使用者的 iOS 裝置。

新的使用者通知 UI 架構可讓應用程式或自訂本機及遠端通知的外觀，提供給使用者的應用程式擴充功能。

若要深入了解，請參閱我們[使用者通知架構](~/ios/platform/user-notifications/index.md)指南。

## <a name="video-subscriber-account"></a>影片的訂閱者帳戶

新增適用於 iOS 10，影片訂閱者帳戶架構可讓應用程式該支援驗證串流或點播視訊來向他們的有線電視或衛星電視提供者供使用者使用單一登入體驗。

## <a name="wide-color"></a>寬色域

iOS 10 延伸的延伸範圍像素格式和整個系統，包括架構，例如核心圖形、 Core 映像、 裸機和 AVFoundation 寬色域圖色彩空間的支援。 藉由提供整個圖形堆疊在這種行為，進一步降低與寬的色彩顯示裝置的支援。

此外， [UIKit](xref:UIKit)已經過修改才能在新擴充**sRGB** colorspace，方便您混合使用中而不會顯著的效能遺失的寬色域色階的色彩。

使用各種色彩時，Apple 提供以下最佳作法：

- [UIColor](xref:UIKit.UIColor)現在會使用 sRGB 色彩空間，並將無法再將值`0.0`至`1.0`範圍。 如果應用程式依賴舊版的 clamp 行為時，它將會需要修改適用於 iOS 10。
- 將繪圖的環境設定 sRGB 色彩空間時執行自訂`UIView`iPad Pro 上繪製。
- 如果應用程式執行的自訂轉譯`UIImages`，使用新[UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer)類別，以指定要使用的擴充範圍或標準範圍的格式。
- 當使用低層級的 API，例如核心圖形或金屬提供映像處理時，開發人員應該使用的延伸的範圍的色彩空間和像素格式支援 16 位元浮點值。 必要時，開發人員必須以手動方式將色彩元件值。
- Core 圖形、 Core 映像和金屬效能著色器都有提供兩個色彩空間之間進行轉換的新方法。

若要深入了解，請參閱我們[寬色域簡介](~/ios/platform/wide-color.md)指南。

## <a name="widget-enhancements"></a>小工具的增強功能

Apple 已引進數個增強功能的小工具系統，以確保 widget 好看任何存在於新的 iOS 10 的鎖定畫面的背景。 [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect)屬性已被取代，已取代為新[WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect)或[WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)屬性。 此外，widget 現在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)屬性，可讓開發人員描述多少內容可供使用，並可讓使用者展開和摺疊的內容。

若要深入了解，請參閱我們[搜尋和主畫面 Widget 增強功能](~/ios/platform/search/widgets.md)指南。

## <a name="additional-framework-changes"></a>其他的架構變更

除了主要的架構變更和上面所列的內容，Apple 已變更許多其他的次要 framework 在 iOS 10。

若要深入了解，請參閱我們[額外的架構變更](~/ios/platform/introduction-to-ios10/additional-framework-changes.md)指南。

## <a name="deprecated-apis"></a>已被取代的 API

IOS 10 中，已被取代的下列 Api:

- `CKDiscoverAllContactsOperation`， `CKDiscoveredUserInfo`，`CKDiscoverUserInfosOperation`和`CKFetchRecordChangesOperation`類別已被取代 CloudKit 適用於 iOS 10。 使用[CKDiscoverAllUserIdentitiesOperation](xref:CloudKit.CKDiscoverUserIdentitiesOperation)， [CKUserIdentity](xref:CloudKit.CKUserIdentity)並[CKFetchRecordZoneChangesOperation](xref:CloudKit.CKFetchRecordZoneChangesOperation) （其支援記錄共用） 類別改為。
- 數個[CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) Api （例如區域為基礎和以查詢為基礎訂用帳戶） 已被取代。 使用[CKRecordZoneSubscription](xref:CloudKit.CKRecordZoneSubscription)並[CKQuerySubscription](xref:CloudKit.CKQuerySubscription) Api 改。
- [NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator)無所不在的內容相關的符號已被取代。
- `ADBannerView``ADInterstitialAd`和相關中的符號[UIViewController](xref:UIKit.UIViewController)類別已被取代。
- [SKUniform](https://developer.apple.com/reference/spritekit/skuniform)與浮點值的符號已被取代。
- `UILocalNotification`， `UIMutableUserNotificationAction`， `UIMutableUserNotificationCategory`， `UIUserNotificationAction`，`UIUserNotificationCategory`和`UIUserNotificationSettings`UIKit 類別已被取代。 使用[使用者通知](#user-notifications)framework 改。
- `HandleActionForLocalNotification`， `HandleActionForRemoteNotification`，`DidReceiveLocalNotification`和`DidReceiveRemoteNotification`WatchKit 方法已被取代。 使用`HandleActionForNotification`和`DidReceiveNotification`方法改為。
- `DidReceiveLocalNotification`並`DidReceiveRemoteNotification`種[WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)已被取代。 建立的執行個體[UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) ，實作適當的方法，並將它指派給`Delegate`屬性[UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter)物件。
- **Game Center 應用程式**已被取代，並從 iOS 中移除。 如果應用程式使用 GameKit，它_必須_呈現它自己的介面，以顯示 GameKit 功能，例如排行榜等等。

請參閱 Apple [iOS 10.0 API 差異的 iOS 9.3](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html)棄用功能的文件的完整清單。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
