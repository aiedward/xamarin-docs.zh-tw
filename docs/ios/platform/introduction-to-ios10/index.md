---
title: "IOS 10 簡介"
description: "本文介紹的所有新的及修改應用程式開發介面和適用於 iOS 10 功能 Xamarin.iOS 開發人員。"
ms.topic: article
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: fbbbeb41ad83d8f673da2b2e7b0efe5645de4075
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="introduction-to-ios-10"></a>IOS 10 簡介

_本文介紹的所有新的及修改應用程式開發介面和適用於 iOS 10 功能 Xamarin.iOS 開發人員。_

## <a name="introducing-ios-10"></a>介紹 iOS 10

使用新的 iOS 10 SDK，Apple 已包含新的 Api 和服務，可讓開發人員建立新的類別目錄的應用程式和功能。 IOS 應用程式現在可以擴充訊息、 Siri、 電話和對應的應用程式提供豐富、 更吸引人的功能無法使用先前的使用者。

如需 iOS 10 的詳細資訊，請參閱 Apple [iOS 應用程式 +](https://developer.apple.com/ios/)文件。

## <a name="whats-new-in-ios-10"></a>新功能 iOS 10

Apple 已加入數個新的 Api 和服務中 iOS 10 以及現有功能，包括許多增強功能：


## <a name="adapting-to-the-true-tone-display"></a>調整，則為 True 的色調顯示

Apple 的則為 True 的色調顯示技術在 iOS 裝置中，會使用環境光感應器，來動態調整色彩和顯示畫面來符合目前的光源條件的濃度。 iOS 10 提供新[UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31)可以加入至應用程式的金鑰`Info.plist`檔案，並控制，則為 True 的色調套用標準色彩排班的方式。 

可用的值如下：

- `UIWhitePointAdaptivityStyleStandard` **預設**-使用標準的白色點 adaptivity。
- `UIWhitePointAdaptivityStyleReading` -用於讀取為主的應用程式。
- `UIWhitePointAdaptivityStyleGame` -用於遊戲為主的應用程式。
- `UIWhitePointAdaptivityStyleVideo` -用於視訊為主的應用程式。
- `UIWhitePointAdaptivityStylePhoto` -用於攝影為主應用程式色彩逼真度所在環境的白色點調整比更重要。

<a name="app-extensions" />

## <a name="app-extensions"></a>應用程式擴充功能

Apple 已提供數個新的應用程式的擴充點中 iOS 10:

- 呼叫目錄
- 對應方式和 UI 對應方式
- 訊息
- 通知內容
- Notification Services
- 貼紙的組件

此外，第 3 個合作對象鍵盤應用程式擴充功能具有下列的增強功能：

- 新`DocumentInputMode`屬性`UITextDocumentProxy`可以判斷文件的輸入的語言，並讓鍵盤延伸配合該語言。
- 新`HandleInputModeList`方法可讓顯示地球索引鍵被佔用的回應中系統的鍵盤選取器功能表的鍵盤延伸模組。

如需詳細資訊，請參閱我們[延伸模組簡介](~/ios/platform/extensions.md)，[訊息應用程式整合](~/ios/platform/message-app-integration/index.md)，[簡介主動式建議](~/ios/platform/search/proactive-suggestions.md)， [Introduction to SiriKit](~/ios/platform/sirikit/index.md)，[使用者通知簡介](~/ios/platform/user-notifications/index.md)與 Apple[應用程式擴充功能的程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

## <a name="app-search-enhancements"></a>應用程式搜尋的增強功能

在 iOS 10 中的核心精選應用程式搜尋提供數個增強功能，例如：

- **（與差異隱私權） Crowdsourced 深層連結普及率**-提供方法來升級深層連結應用程式在搜尋結果中的內容。
- **應用程式內搜尋**-使用新`CSSearchQuery`類別，以提供應用程式內 Spotlight 搜尋功能類似郵件、 訊息和附註的應用程式的運作方式。
- **搜尋接續**-可讓使用者在精選或 Safari，開始搜尋，然後開啟應用程式，並繼續搜尋。
- **驗證結果的視覺效果**-Apple[應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool)preforming 測試時，現在會顯示網站的標記和深層連結的視覺表示法。
- **訊息應用程式映像共用**-可讓共用中 （透過訊息應用程式擴充功能） 的訊息出現在 Spotlight 搜尋所提供的常用在應用程式映像。

若要進一步了解，請參閱我們[應用程式搜尋的增強功能](~/ios/platform/search/app-search-enhancements.md)指南。

## <a name="apple-pay-enhancements"></a>Apple Pay 增強功能

Apple 已中 iOS 10 可讓使用者從網站，並透過 Siri 與地圖互動的安全付款，以進行的 Apple Pay 數個增強功能。

使用 iOS 10，數個新的應用程式開發介面已加入使用 iOS 和 watchOS 支援動態付款網路與新的沙箱測試環境。

此外，PassKit 架構已擴充，能夠支援 Apple Pay 之外`UIKit`和允許卡片來呈現其卡從自己的應用程式中的簽發者。

若要進一步了解，請參閱我們[Apple 支付增強功能](~/ios/platform/apple-pay.md)指南。

## <a name="alternate-app-icons"></a>替代的應用程式圖示

Apple iOS 10.3 提供可讓應用程式管理它的圖示已加入數個增強功能：

 - `ApplicationIconBadgeNumber` -取得或設定應用程式圖示的徽章 Springboard 中。
 - `SupportsAlternateIcons` -如果`true`應用程式有替代組圖示。
 - `AlternateIconName` -傳回目前選取的替代圖示的名稱或`null`如果使用主要的圖示。
 - `SetAlternameIconName` -使用這個方法指定替代的圖示切換應用程式的圖示。

若要進一步了解，請參閱我們[替代的應用程式圖示](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)指南。


## <a name="introduction-to-callkit"></a>CallKit 簡介

在 iOS 10 中新的 CallKit API 提供 VOIP 與 iPhone UI 整合提供了熟悉的介面和終端使用者體驗的應用程式的方式。 使用此 API 中，使用者可以檢視和互動 VOIP 呼叫從 iOS 裝置鎖定畫面與管理連絡人使用的手機應用程式**我的最愛**和**最近**檢視。

此外，CallKit API 會提供建立應用程式擴充功能，可以將電話號碼 （呼叫者識別碼） 名稱產生關聯，或告知時應該是數字，系統封鎖 （呼叫封鎖） 的能力。

若要進一步了解，請參閱我們[簡介 Callkit](~/ios/platform/callkit.md)指南。

## <a name="message-app-integration"></a>訊息應用程式整合

iOS 10 可讓整合 Xamarin.iOS 方案中的訊息應用程式擴充包含**訊息**應用程式並呈現給使用者的新功能。 擴充功能可以傳送文字、 貼紙、 媒體檔案和互動式的訊息。 有兩種類型的訊息應用程式擴充功能：

- **貼紙套件**-包含使用者可以新增至訊息的貼紙的集合。 可以建立不需要撰寫任何程式碼的貼紙的組件。
- **應用程式 iMessage** -可以呈現自訂使用者介面選取貼紙、 輸入文字，包括媒體檔案 （具有選擇性型別轉換） 以及建立、 編輯和傳送互動訊息的訊息應用程式內。

若要進一步了解，請參閱我們[訊息應用程式整合](~/ios/platform/message-app-integration/index.md)指南。

## <a name="news-publisher-enhancements"></a>新聞發行者增強功能

使用 iOS 10，Apple 會允許任何人從主要的雜誌和新消息和獨立的 「 發行者 」 來註冊與產品的組織，並將內容傳遞至 Apple 新聞應用程式。 若要進一步了解，請參閱 Apple[新聞資源](https://newsresources.apple.com/)文件。

## <a name="providing-haptic-feedback"></a>提供 Haptic 意見反應

IPhone 7 和 iPhone 上 7 加上、 Apple 已包含了提供其他方式來實際連絡使用者的新 haptics 回覆。 使用新的 tactile 意見反應選項來取得使用者注意並強化其動作。

數個內建的 UI 項目已提供 haptic 回饋，例如選擇器、 交換器和滑桿。 iOS 10 現在增加的能力以程式設計方式觸發程序使用的具體子類別的 haptics`UIFeedbackGenerator`類別。

若要進一步了解，請參閱我們[提供 Haptic 意見反應](~/ios/user-interface/ios-ui/haptic-feedback.md)指南。

## <a name="proactive-suggestions"></a>主動式建議

iOS 10 會呈現驅動 engagement 應用程式的新方式讓系統主動有用的資訊會自動向使用者呈現在適當的時間。 就像 iOS 9 提供新增的 iOS 10 應用程式可以公開從系統中的下列位置可以呈現給使用者的功能與使用精選、 傳遞和 Siri 建議的應用程式深層的搜尋功能：

- 應用程式切換器
- 鎖定畫面
- CarPlay
- 地圖
- Siri 互動
- QuickType 建議 

應用程式公開此功能，以使用技術的集合，例如系統[NSUserActivity](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/)，網頁標記中，核心精選、 MapKit、 Media Player 和 UIKit。

若要進一步了解，請參閱我們[簡介主動式建議](~/ios/platform/search/proactive-suggestions.md)指南。

## <a name="request-app-review"></a>要求檢閱應用程式

Ios 10.3，新`RequestReview()`方法可讓要求使用者評等或檢閱 iOS 應用程式。 雖然可以呼叫這個方法，在任何時間點，也可以在使用者經驗，檢閱程序是控管，而且由應用程式存放區原則處理。 如此一來，這個方法可能會或可能不會顯示警示，並永遠不會呼叫以回應使用者動作，例如點選按鈕。

若要進一步了解，請參閱我們[要求應用程式檢閱](~/ios/platform/request-app-review.md)指南。

## <a name="security-and-privacy-enhancements"></a>安全性和隱私權增強功能

Apple 已對安全性和隱私權 iOS 10 可協助開發人員改善其應用程式的安全性，並確保使用者的隱私權中的數個增強功能。

如此一來，10 （或更新版本） 在 iOS 上執行的應用程式必須以靜態方式宣告試圖輸入一或多個隱私權特定中索引鍵來存取特定功能或使用者資訊及其`Info.plist`解釋為什麼應用程式想要存取的使用者的檔案。

若要進一步了解，請參閱我們[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)指南。

## <a name="sirikit"></a>SiriKit

新增 ios 10 SiriKit 允許 Xamarin.iOS 應用程式提供的 iOS 裝置上使用 Siri 使用者可以存取的服務。 這個功能提供一個或多個應用程式擴充功能使用新**對應方式**和**目的 UI**架構。

SiriKit 支援下列服務網域：

- 音訊或視訊電話。
- 預約的賽車。
- 管理健身。
- 訊息。
- 搜尋相片。
- 傳送或接收付款。

當使用者提出要求的 Siri 涉及其中一個應用程式擴充功能的服務時，SiriKit 傳送副檔名**意圖**物件，描述使用者的要求，以及任何支援的資料。 應用程式擴充功能會產生適當**回應**物件指定**意圖**，詳細說明如何擴充功能來處理要求。

應用程式擴充功能時使用 Siri 通常會處理所有使用者互動，可以使用**意圖 UI** framework 提供豐富、 自訂使用者介面並且包含應用程式的商標和其他資訊。

若要進一步了解，請參閱我們[簡介 SiriKit](~/ios/platform/sirikit/index.md)指南。

## <a name="speech-recognition"></a>語音辨識

iOS 10 包含新的語音 API 可讓應用程式支援連續的語音辨識，並同時語音 （從即時或錄製音訊資料流） 成文字。

因為語音辨識要求的傳輸和 Apple 的伺服器，應用程式上的資料暫存儲存體_必須_要求使用者的權限，才能藉由執行辨識`NSSpeechRecognitionUsageDescription`中的索引鍵及其`Info.plist`檔案與呼叫`SFSpeechRecognizer.RequestAutorization`方法。

若要進一步了解，請參閱我們[簡介語音辨識](~/ios/platform/speech.md)指南。

## <a name="user-notifications"></a>使用者通知

新增 iOS 10、 傳遞和處理的本機和遠端通知架構可讓使用者通知。 使用此架構，在應用程式或應用程式擴充功能可以排程傳遞本機通知藉由指定的一組條件，例如位置或一天的時間。

此外，應用程式或延伸模組可以接收 （且可能會修改） 本機和遠端的通知時傳送至使用者的 iOS 裝置。

新的使用者通知 UI 架構可讓應用程式或自訂的本機和遠端通知的外觀，提供給使用者的應用程式擴充功能。

若要進一步了解，請參閱我們[使用者通知架構](~/ios/platform/user-notifications/index.md)指南。

## <a name="video-subscriber-account"></a>視訊的訂閱者帳戶

新增 ios 10，視訊訂閱者帳戶架構可讓應用程式的支援驗證的資料流或 video-on-demand 向他們的有線電視或衛星電視提供者使用的單一登入體驗給終端使用者。

## <a name="wide-color"></a>寬的色彩

iOS 10 擴充的擴充範圍像素格式和整個系統中包括架構，例如核心圖形、 Core 映像、 裸機和 AVFoundation 寬一系列色彩空間的支援。 藉由提供整個圖形堆疊此行為，是進一步降低與寬的色彩顯示裝置的支援。

此外， [UIKit](https://developer.xamarin.com/api/namespace/UIKit/)已修改才能在新擴充**sRGB** colorspace，輕鬆地混合中不會顯著的效能遺失寬色彩色階的色彩。

Apple 時使用寬的色彩，請提供下列最佳作法：

- [UIColor](https://developer.xamarin.com/api/type/UIKit.UIColor/)現在使用 sRGB 色彩空間，而且將不再 clamp 值`0.0`至`1.0`範圍。 如果應用程式依賴之前有縮短時間差行為時，它必須針對 iOS 10 進行修改。
- 執行自訂時，將對 sRGB 色彩空間設定繪圖環境`UIView`iPad Pro 上繪製。
- 如果應用程式執行的自訂轉譯`UIImages`，使用新[UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer)類別來指定要使用的擴充範圍或標準範圍的格式。
- 當使用例如核心圖形或金屬低階 API 提供映像處理時，開發人員應該使用的擴充的範圍的色彩空間和像素格式支援 16 位元浮點值。 在需要時，開發人員就必須手動 clamp 色彩元件值。
- 核心圖形、 Core 映像和金屬效能著色器所有提供的兩個色彩空間之間進行轉換的新方法。

若要進一步了解，請參閱我們[簡介寬色彩](~/ios/platform/wide-color.md)指南。

## <a name="widget-enhancements"></a>小工具增強功能

Apple 已引進了數個增強功能 Widget 系統，以確保 widget 看起來很棒，於任何存在於新的 iOS 10 鎖定畫面的背景。 [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect)屬性已被取代，已取代為新[WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect)或[WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)屬性。 此外，widget 現在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)屬性，可讓開發人員描述多少內容可供使用，並可讓使用者展開和摺疊的內容。

若要進一步了解，請參閱我們[搜尋和首頁畫面 Widget 增強功能](~/ios/platform/search/widgets.md)指南。

## <a name="additional-framework-changes"></a>其他架構變更

主要架構變更和加入上面所列的項目，除了 Apple 進行了許多其他次要架構變更 iOS 10 中。

若要進一步了解，請參閱我們[其他架構變更](~/ios/platform/introduction-to-ios10/additional-framework-changes.md)指南。

## <a name="deprecated-apis"></a>已被取代的 API

IOS 10 中，已被取代下列 Api:

- `CKDiscoverAllContactsOperation`， `CKDiscoveredUserInfo`，`CKDiscoverUserInfosOperation`和`CKFetchRecordChangesOperation`類別已被取代 CloudKit ios 10。 使用[CKDiscoverAllUserIdentitiesOperation](https://developer.xamarin.com/api/type/CloudKit.CKDiscoverUserIdentitiesOperation/)， [CKUserIdentity](https://developer.xamarin.com/api/type/CloudKit.CKUserIdentity/)和[CKFetchRecordZoneChangesOperation](https://developer.xamarin.com/api/type/CloudKit.CKFetchRecordZoneChangesOperation/) （其支援記錄共用） 類別改為。
- 數個[CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) Api （例如區域為基礎和以查詢為基礎的訂閱） 已被取代。 使用[CKRecordZoneSubscription](https://developer.xamarin.com/api/type/CloudKit.CKRecordZoneSubscription/)和[CKQuerySubscription](https://developer.xamarin.com/api/type/CloudKit.CKQuerySubscription/) Api 改為。
- [NSPersistentStoreCoordnator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/)無所不在內容相關的符號已被取代。
- `ADBannerView``ADInterstitialAd`和相關的符號[UIViewController](https://developer.xamarin.com/api/type/UIKit.UIViewController/)類別已被取代。
- [SKUniform](https://developer.apple.com/reference/spritekit/skuniform)相關到浮點值的符號已被取代。
- `UILocalNotification`， `UIMutableUserNotificationAction`， `UIMutableUserNotificationCategory`， `UIUserNotificationAction`，`UIUserNotificationCategory`和`UIUserNotificationSettings`UIKit 的類別已被取代。 使用[使用者通知](#User-Notifications)framework 改為。
- `HandleActionForLocalNotification`， `HandleActionForRemoteNotification`，`DidReceiveLocalNotification`和`DidReceiveRemoteNotification`WatchKit 方法已被取代。 使用`HandleActionForNotification`和`DidReceiveNotification`方法改為。
- `DidReceiveLocalNotification`和`DidReceiveRemoteNotification`方法[WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)已被取代。 建立的執行個體[UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) ，實作適當的方法，並將它指派給`Delegate`屬性[UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter)物件。
- **Game Center 應用程式**已被取代，從 iOS 中移除。 如果應用程式使用 GameKit，它_必須_呈現自己的介面，以顯示 GameKit 功能，例如排行榜等等。

請參閱 Apple [iOS 9.3 iOS 10.0 的 API 差異](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html)deprecations 的文件的完整清單。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
- [在 iOS 10 最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
