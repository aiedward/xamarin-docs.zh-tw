---
title: iOS 平臺功能總覽
description: 本檔所連結的各種指南，會說明各種版本的 iOS 和其他 iOS 平臺功能所引進的功能。
ms.prod: xamarin
ms.assetid: 9F6A27E5-8A87-ADE2-D1EF-5684E7B8C999
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: aca9227bc5c885000bffe858e0fc8b34d3724dfd
ms.sourcegitcommit: 0c45e3f810947e3d43223aa01bf3e43a0defca65
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/21/2020
ms.locfileid: "90843495"
---
# <a name="ios-platform-features-overview"></a>iOS 平臺功能總覽

此頁面會列出最新的 iOS 版本，並醒目提示您可以使用 Xamarin 存取的一些 Apple 架構。

## <a name="ios-releases"></a>iOS 版本

| 版本 | 描述 |
|-------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [IOS 14 簡介](~/ios/platform/ios14/index.md) | 本檔說明 Xamarin. iOS 14。|
| [iOS 13 簡介](~/ios/platform/ios13/index.md) | 本檔說明 Xamarin. iOS 13。|
| [iOS 12 簡介](~/ios/platform/introduction-to-ios12/index.md) | 本檔說明在建立 Xamarin iOS 應用程式時可使用的 iOS 12 功能。|
| [iOS 11 簡介](~/ios/platform/introduction-to-ios11/index.md) | 本檔說明 iOS 11 和 Xcode 9 中的新功能和更新功能，例如 ARKit、Core ML、Core NFC、拖放、MapKit、PDFKit、SiriKit 和視覺。 它會連結至說明如何搭配使用這些功能與 Xamarin 的指南。 |
| [iOS 10 簡介](~/ios/platform/introduction-to-ios10/index.md) | iOS 10 包含數個新的 Api 和服務，可讓您以新的特性和功能開發應用程式。 使用 iOS 10，應用程式有新的功能，例如延伸地圖、訊息、電話和 Siri。 本節說明在 Xamarin iOS 應用程式中利用這些功能的方式。 |
| [iOS 9 簡介](~/ios/platform/introduction-to-ios9/index.md)   | 本節定義從 iOS 8 升級時在 iOS 9 中所做的變更，以及如何在 Xamarin iOS 應用程式中使用這些功能。 |
| [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)         | iOS 8 對 iOS 7 的作業系統進行了大量變更。 在這裡，我們會示範它們是什麼，以及如何使用它們。 |
| [iOS 7 簡介](~/ios/platform/introduction-to-ios7/index.md)   | 關於 iOS 7 中引進的主要新 Api，包括視圖控制器轉換、UIView 動畫的增強功能、UIKit Dynamics 和文字套件。 |
| [iOS 6 簡介](~/ios/platform/introduction-to-ios6/index.md)   | IOS 6 中引進的功能說明，包括集合視圖、Pass 套件、事件套件和社交架構。 |

## <a name="apple-pay"></a>[Apple Pay](~/ios/platform/apple-pay.md)

Apple Pay 是與 iOS 8 一起導入，可讓使用者透過其 iOS 裝置支付實體貨物，例如食物、娛樂和成員資格。 它適用于 iPhone 6 和 iPhone 6 Plus，也可以與店內購買的 Apple Watch 配對。 在 iPhone 上使用時，它會使用 Touch ID 來確認及授權交易給使用者的信用卡或轉帳卡。

## <a name="callkit"></a>[CallKit](~/ios/platform/callkit.md)

IOS 10 中的新 CallKit API 提供了一種方法，可讓 VOIP 應用程式與 iPhone UI 整合，並提供熟悉的介面和體驗給終端使用者。 使用此 API 時，使用者可以從 iOS 裝置的鎖定畫面來查看並與 VOIP 通話進行互動，並使用手機應用程式的 [我的最愛] 和 [**最近專案** **]** 視圖來管理連絡人。

## <a name="contacts-and-contactsui"></a>[Contacts 和 ContactsUI](~/ios/platform/contacts.md)

隨著 iOS 9 的推出，Apple 已發行兩個新的架構， `Contacts` 並 `ContactsUI` 取代了 ios 8 和更早版本所使用的現有通訊錄和通訊錄 UI 架構。

## <a name="document-picker"></a>[文件選擇器](~/ios/platform/document-picker.md)

檔選擇器可讓您在應用程式之間共用檔。 這些檔可能儲存在 iCloud 或不同應用程式的目錄中。 檔是透過使用者已安裝在其裝置上的 [檔提供者延伸](~/ios/platform/extensions.md) 模組集來共用。

## <a name="eventkit"></a>[EventKit](~/ios/platform/eventkit.md)

iOS 有兩個內建的行事曆相關應用程式：行事曆應用程式和提醒應用程式。 您可以直接瞭解行事曆應用程式管理行事歷數據的方式，但提醒應用程式比較不明顯。 提醒在到期時、完成時間等方面，實際上可以有相關的日期。因此，iOS 會將所有行事歷數據（不論是行事曆活動或提醒）儲存在一個位置（稱為行事 *曆資料庫*）中。

## <a name="ios-extensions"></a>[iOS 延伸模組](~/ios/platform/extensions.md)

在 iOS 8 中引進的延伸模組，是 `UIViewControllers` 由 ios 在標準內容（例如，在 **通知中心**內）所呈現的自訂鍵盤型別，是由使用者要求以執行特殊輸入或其他內容（例如編輯可提供特殊效果篩選的相片）的自訂鍵盤類型。

## <a name="graphics-and-animation-in-ios"></a>[iOS 中的圖形和動畫](~/ios/platform/graphics-animation-ios/index.md)

IOS 中的圖形和動畫涵蓋 iOS 的核心圖形概念，例如 CoreImage、核心圖形和核心動畫。

## <a name="handoff"></a>[遞交](~/ios/platform/handoff.md)

Apple 推出了 iOS 8 和 OS X Yosemite (10.10) 中提供一種通用機制，可讓使用者將其裝置上所啟動的活動轉移到另一個執行相同應用程式的裝置，或另一個支援相同活動的應用程式。

## <a name="healthkit"></a>[HealthKit](~/ios/platform/healthkit.md)

健康情況套件會針對使用者的健康情況相關資訊，提供安全的資料存放區。 健康情況套件應用程式可能會有使用者的明確許可權、讀取和寫入此資料存放區，以及在加入相關資料時接收通知。 應用程式可以顯示資料，或使用者可以使用 Apple 提供的健康情況應用程式來查看其所有資料的儀表板。

## <a name="homekit"></a>[HomeKit](~/ios/platform/homekit.md)

Apple 在 iOS 8 中引進了 HomeKit，以提供在使用者家裡探索及與家庭自動化裝置通訊的通用架構。 HomeKit 提供常用的平臺來設定裝置，並設定控制這些裝置的動作。

## <a name="in-app-purchasing"></a>[應用程式內購買](~/ios/platform/in-app-purchasing/index.md)

iOS 應用程式可以使用 StoreKit （iOS 所提供的一組 Api）來銷售數位產品或服務，這組 Api 會與 Apple 的伺服器通訊，以透過其 Apple ID 與使用者進行財務交易。 StoreKit Api 主要是要處理產品資訊和執行交易，而不是使用者介面元件。 執行應用程式內購買的應用程式必須建立自己的使用者介面，並使用自訂程式碼來追蹤購買的專案，以提供所需的產品或服務給使用者。

## <a name="ios-gaming-apis"></a>[iOS 遊戲 Api](~/ios/platform/gaming/index.md)

Apple 針對 iOS 9 中的遊戲 Api 進行了幾項技術改進，讓您更輕鬆地在 Xamarin iOS 應用程式中執行遊戲圖形和音訊。 這些功能包括透過高階架構輕鬆進行開發，並利用 iOS 裝置 GPU 的強大功能來改善速度和圖形能力。

## <a name="message-app-integration"></a>[訊息應用程式整合](~/ios/platform/message-app-integration/index.md)

IOS 10 的新功能，訊息應用程式延伸模組會與 **訊息** 應用程式整合，並為使用者提供新功能。 延伸模組可以傳送文字、貼紙、媒體檔案和互動式訊息。

## <a name="multitasking-for-ipad"></a>[適用於 iPad 的多工](~/ios/platform/multitasking.md)

iOS 9 針對在特定 iPad 硬體上同時執行兩個應用程式，增加多工支援。 您可以透過下列功能，支援 iPad 的多工：滑過、分割視圖 & 圖片中的圖片。

## <a name="passkit"></a>[PassKit](~/ios/platform/passkit.md)

Passbook 是與 iOS 6 Iphone 和 iPod 的應用程式。 它會儲存並顯示條碼及其他資訊，以將其手機上的客戶交易與「真實世界」連結。 Pass 會由商家產生，並透過電子郵件、Url 或從商家本身的 iOS 應用程式傳送給客戶。 Passbook 會儲存並組織手機上的所有通過，並根據裝置的日期/時間或位置，在鎖定畫面上顯示「通過提醒」。

本檔介紹 Passbook、如何搭配使用 Pass 套件 API 與 Xamarin，以及討論如何在您的伺服器上執行通過。

## <a name="photokit"></a>[PhotoKit](~/ios/platform/photokit.md)

相片套件是一個新的架構，可讓應用程式查詢系統映射庫，並建立自訂使用者介面來查看和修改其內容。 它包含一些類別，這些類別代表影像和影片資產，以及專輯和資料夾等資產集合。

## <a name="request-app-review"></a>[要求應用程式評論](~/ios/platform/request-app-review.md)

IOS 10.3 的新功能，此 `RequestReview()` 方法可讓 ios 應用程式要求使用者對其進行評分或複習。 當使用者從 App Store 安裝的貨運應用程式中呼叫這個方法時，iOS 10 將會處理開發人員的整個評等和審核程式。 由於此程式是由 App Store 原則所控制，因此可能會出現或可能不會顯示警示。

## <a name="search-apis"></a>[搜尋 API](~/ios/platform/search/index.md)

IOS 9 中的搜尋已擴充，可提供絕佳的新方式來存取 Xamarin iOS 應用程式內的資訊和功能。 使用新的應用程式搜尋 Api，應用程式內容可透過焦點和 Safari 搜尋結果、交付和 Siri 提醒和建議來進行搜尋。 這可讓使用者快速存取應用程式內的活動和資訊。

## <a name="sirikit"></a>[SiriKit](~/ios/platform/sirikit/index.md)

SiriKit 可讓 iOS 應用程式在 ios 裝置上使用 Siri 和地圖應用程式，使用應用程式擴充功能和新的 **意圖** 和 **意圖 UI** 架構，提供可供使用者存取的服務。

## <a name="social-framework"></a>[社交架構](~/ios/platform/social-framework.md)

社交架構提供整合的 API，可與社交網路（包括 _Twitter_ 和 _Facebook_）互動，也提供適用于中國使用者的 _SinaWeibo_ 。

## <a name="speech-recognition"></a>[語音辨識](~/ios/platform/speech.md)

iOS 10 包含新的語音 API，可讓應用程式從即時或錄製的音訊串流 () 為文字，以支援連續的語音辨識和轉譯語音。

## <a name="textkit"></a>[TextKit](~/ios/platform/textkit.md)

文字套件是新的 API，可提供強大的文字版面配置和轉譯功能。 它是以低層級的核心文字架構為基礎，但比核心文字更容易使用。

## <a name="3d-touch"></a>[3D Touch](~/ios/platform/3d-touch.md)

本文將提供和簡介，說明如何使用新的 3D Touch Api，在新的 iPhone 6s 和 iPhone 6s Plus 裝置上執行的 Xamarin iOS 應用程式中新增壓力敏感性手勢。

## <a name="touch-id-and-face-id-with-xamarinios"></a>[使用 Xamarin 的 Touch ID 和臉部識別碼](~/ios/platform/touch-id-face-id.md)

Touch ID 和臉部識別碼是自 iOS 8 起提供的生物識別驗證系統。 本文和範例說明如何搭配使用 Touch ID 和臉部識別碼與 Xamarin。

## <a name="user-notifications"></a>[使用者通知](~/ios/platform/user-notifications/index.md)

使用者通知架構是 iOS 10 的新手，可提供本機和遠端通知的傳遞和處理。 使用此架構，應用程式或應用程式擴充功能可以藉由指定一組條件（例如，一天中的位置或時間）來排程本機通知的傳遞。

## <a name="wide-color"></a>[寬色域](~/ios/platform/wide-color.md)

iOS 10 和 macOS Sierra 增強了整個系統中擴充範圍像素格式和寬範圍色彩空間的支援，包括核心圖形、核心影像、金屬和 AVFoundation 等架構。 在整個圖形堆疊中提供這項行為，可進一步分階段減緩支援彩色顯示器的裝置。

## <a name="binding-objective-c"></a>[繫結 Objective C](binding-objective-c/index.md)

在 iOS 上工作時，您可能會遇到需要使用協力廠商目標 C 程式庫的情況。 在這些情況下，您可以使用 Monotouch.dll 的系結專案來建立原生目標 C 程式庫的 c # 系結。 專案會使用我們用來將 iOS Api 帶入 c # 的相同工具。 本檔說明如何系結目標 C Api。

## <a name="bind-ios-swift-libraries"></a>[繫結 iOS Swift 程式庫](binding-swift/index.md)

本檔說明如何建立快速程式碼的 c # 系結，讓您可以在 Xamarin iOS 應用程式中使用原生程式庫和 CocoaPods。

## <a name="referencing-native-libraries"></a>[參考原生程式庫](native-interop.md)

Xamarin 支援使用原生 C 程式庫和目標 C 程式庫來連結。 本檔討論如何連結您的原生 C 程式庫與您的 Xamarin. iOS 專案。

## <a name="embedded-frameworks"></a>[內嵌的架構](embedded-frameworks.md)

說明如何在 Xamarin iOS 應用程式中內嵌目標 C 使用者架構。
