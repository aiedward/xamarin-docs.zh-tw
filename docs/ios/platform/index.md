---
title: iOS 平臺功能總覽
description: 本檔會連結各種不同的指南，說明各種 iOS 版本中引進的功能，以及其他 iOS 平臺功能。
ms.prod: xamarin
ms.assetid: 9F6A27E5-8A87-ADE2-D1EF-5684E7B8C999
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 78f8c8d1340c6bb1063e59ad03485d8b9b807c58
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488994"
---
# <a name="ios-platform-features-overview"></a>iOS 平臺功能總覽

此頁面會列出最新的 iOS 版本，並反白顯示您可以使用 Xamarin 來存取的一些 Apple framework。

## <a name="ios-releases"></a>iOS 版本

|  |  |
|-------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [IOS 13 簡介](~/ios/platform/ios13/index.md) | 本檔說明 Xamarin. iOS 13。|
| [iOS 12 簡介](~/ios/platform/introduction-to-ios12/index.md) | 本檔說明在建立 Xamarin iOS 應用程式時可使用的 iOS 12 功能。|
| [iOS 11 簡介](~/ios/platform/introduction-to-ios11/index.md) | 本檔說明 iOS 11 和 Xcode 9 中的新增和更新功能，例如 ARKit、核心 ML、核心 NFC、拖放、MapKit、PDFKit、SiriKit 和願景。 它會連結至說明如何搭配使用這些功能與 Xamarin 的指南。 |
| [iOS 10 簡介](~/ios/platform/introduction-to-ios10/index.md) | iOS 10 包含數個新的 Api 和服務，可讓您使用新特性和功能開發應用程式。 使用 iOS 10，應用程式有新的功能，例如擴充地圖、訊息、電話和 Siri。 本節說明如何在 Xamarin iOS 應用程式中利用這些功能的方式。 |
| [iOS 9 簡介](~/ios/platform/introduction-to-ios9/index.md)   | 本節定義從 iOS 8 升級時，在 iOS 9 中進行的變更，以及如何在 Xamarin iOS 應用程式中使用這些功能。 |
| [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)         | iOS 8 對 iOS 7 的作業系統進行大量變更。 在這裡，我們會示範它們是什麼，以及如何使用它們。 |
| [iOS 7 簡介](~/ios/platform/introduction-to-ios7/index.md)   | 關於 iOS 7 引進的主要新 Api，包括視圖控制器轉換、UIView 動畫、UIKit Dynamics 和文字套件的增強功能。 |
| [iOS 6 簡介](~/ios/platform/introduction-to-ios6/index.md)   | IOS 6 中引進的功能說明，包括集合視圖、傳遞套件、事件套件和社交架構。 |

## <a name="apple-payiosplatformapple-paymd"></a>[Apple Pay](~/ios/platform/apple-pay.md)

Apple Pay 是與 iOS 8 一起引進，可讓使用者透過其 iOS 裝置支付實體商品（例如食物、娛樂和成員資格）的費用。 它適用于 iPhone 6 和 iPhone 6 Plus，而且也可以與適用于商店內購買的 Apple Watch 配對。 在 iPhone 上使用時，它會使用 Touch ID 作為向使用者的信用卡或金融卡確認交易並授權的方式。

## <a name="callkitiosplatformcallkitmd"></a>[CallKit](~/ios/platform/callkit.md)

IOS 10 中新的 CallKit API 可讓 VOIP 應用程式與 iPhone UI 整合，並為使用者提供熟悉的介面和經驗。 有了這個 API，使用者就可以從 iOS 裝置的鎖定畫面觀看 VOIP 呼叫並與之**互動，並**使用電話應用程式的我的最愛和**最近專案**views 來管理連絡人。

## <a name="contacts-and-contactsuiiosplatformcontactsmd"></a>[連絡人和 ContactsUI](~/ios/platform/contacts.md)

隨著 iOS 9 的推出，Apple 發行了兩個新的架構，`Contacts` 和 `ContactsUI`，取代了 iOS 8 和更早版本所使用的現有通訊錄和通訊錄 UI 架構。

## <a name="document-pickeriosplatformdocument-pickermd"></a>[文件選擇器](~/ios/platform/document-picker.md)

檔選擇器可讓您在應用程式之間共用檔。 這些檔可能會儲存在 iCloud 或不同的應用程式目錄中。 檔會透過使用者已安裝在其裝置上的一組[檔提供者延伸](~/ios/platform/extensions.md)模組來共用。

## <a name="eventkitiosplatformeventkitmd"></a>[EventKit](~/ios/platform/eventkit.md)

iOS 有兩個內建的行事曆相關應用程式：行事曆應用程式和提醒應用程式。 這很簡單，足以瞭解行事曆應用程式管理行事歷數據的方式，但提醒應用程式比較不明顯。 提醒實際上在到期時、何時完成等方面，都有與其相關聯的日期。因此，iOS 會將所有行事歷數據（不論是行事曆事件或提醒）儲存在一個位置，稱為行事*曆資料庫*。

## <a name="ios-extensionsiosplatformextensionsmd"></a>[iOS 延伸模組](~/ios/platform/extensions.md)

IOS 8 中引進的擴充功能是 iOS 在標準內容（例如，在**通知中心**內）所提供的特製化 `UIViewControllers`，如同使用者用來執行特定輸入或其他內容的自訂鍵盤類型，像是編輯可提供特殊效果篩選的相片。

## <a name="graphics-and-animation-in-iosiosplatformgraphics-animation-iosindexmd"></a>[IOS 中的圖形和動畫](~/ios/platform/graphics-animation-ios/index.md)

IOS 中的圖形和動畫涵蓋 iOS 中的核心圖形概念，例如 CoreImage、核心圖形和核心動畫。

## <a name="handoffiosplatformhandoffmd"></a>[Handoff](~/ios/platform/handoff.md)

Apple 在 iOS 8 和 OS X Yosemite （10.10）中引進了移交，以提供一般機制，讓使用者將其中一個裝置上啟動的活動傳送至另一個執行相同應用程式的裝置，或另一個支援相同活動的應用程式。

## <a name="healthkitiosplatformhealthkitmd"></a>[HealthKit](~/ios/platform/healthkit.md)

健康情況套件為使用者的健康狀態相關資訊提供安全的資料存放區。 健康情況套件應用程式可能會有使用者的明確許可權、讀取和寫入此資料存放區，並在新增相關資料時接收通知。 應用程式可以呈現資料，或使用者可以使用 Apple 提供的健康情況應用程式來查看其所有資料的儀表板。

## <a name="homekitiosplatformhomekitmd"></a>[HomeKit](~/ios/platform/homekit.md)

Apple 在 iOS 8 中引進了 HomeKit，可提供通用的架構，讓您在使用者的家裡探索家庭自動化裝置並與之通訊。 HomeKit 提供通用的平臺，讓您可以設定裝置，並設定動作來控制它們。

## <a name="in-app-purchasingiosplatformin-app-purchasingindexmd"></a>[應用程式內購買](~/ios/platform/in-app-purchasing/index.md)

iOS 應用程式可以使用 StoreKit 來銷售數位產品或服務– iOS 所提供的一組 Api，可與 Apple 的伺服器通訊，透過其 Apple ID 與使用者進行財務交易。 StoreKit Api 主要是與抓取產品資訊並進行交易相關，而不是使用者介面元件。 執行應用程式內購買的應用程式必須建立自己的使用者介面，並以自訂程式碼追蹤購買的專案，為使用者提供必要的產品或服務。

## <a name="ios-gaming-apisiosplatformgamingindexmd"></a>[iOS 遊戲 Api](~/ios/platform/gaming/index.md)

Apple 在 iOS 9 中對遊戲 Api 進行了幾項技術改進，讓您更輕鬆地在 Xamarin iOS 應用程式中執行遊戲圖形和音訊。 其中包括透過高階架構的輕鬆開發，以及運用 iOS 裝置的 GPU 功能來改善速度和圖形功能。

## <a name="message-app-integrationiosplatformmessage-app-integrationindexmd"></a>[訊息應用程式整合](~/ios/platform/message-app-integration/index.md)

IOS 10 的新功能，訊息應用程式延伸模組會與「**訊息**」應用程式整合，並為使用者提供新功能。 延伸模組可以傳送文字、貼紙、媒體檔案和互動式訊息。

## <a name="multitasking-for-ipadiosplatformmultitaskingmd"></a>[適用於 iPad 的多工](~/ios/platform/multitasking.md)

iOS 9 增加了多工支援，可在特定 iPad 硬體上同時執行兩個應用程式。 透過下列功能可支援 iPad 的多工作業： [滑過]、[分割視圖] & 圖片中的圖片。

## <a name="passkitiosplatformpasskitmd"></a>[PassKit](~/ios/platform/passkit.md)

Passbook 是適用于 Iphone 和 iPod 與 iOS 6 的應用程式。 它會儲存並顯示條碼和其他資訊，以在其手機上連結「真實世界」的客戶交易。 Pass 是由商家所產生，並透過電子郵件、Url 或商家本身的 iOS 應用程式來傳送給客戶。 Passbook 會儲存並組織電話上的所有 Pass，並根據裝置的日期/時間或位置，在鎖定畫面上顯示「通知」。

本檔介紹使用 Pass 套件 API 搭配 Xamarin 的 Passbook，並討論如何在您的伺服器上執行傳遞。

## <a name="photokitiosplatformphotokitmd"></a>[PhotoKit](~/ios/platform/photokit.md)

相片套件是新的架構，可讓應用程式查詢系統映射庫，並建立自訂使用者介面來查看和修改其內容。 其中包含一些代表影像和影片資產的類別，以及一系列的資產，例如專輯和資料夾。

## <a name="request-app-reviewiosplatformrequest-app-reviewmd"></a>[要求應用程式審查](~/ios/platform/request-app-review.md)

IOS 10.3 的新功能，`RequestReview()` 方法可讓 iOS 應用程式要求使用者對其進行評分或檢查。 當使用者已從 App Store 安裝的出貨應用程式中呼叫此方法時，iOS 10 將會處理開發人員的整個評等和審查流程。 由於此程式是由 App Store 原則所控管，因此不一定會顯示警示。

## <a name="search-apisiosplatformsearchindexmd"></a>[搜尋 API](~/ios/platform/search/index.md)

IOS 9 中的搜尋功能已擴充，可提供絕佳的新方式來存取 Xamarin iOS 應用程式中的資訊和功能。 藉由使用新的應用程式搜尋 Api，應用程式內容可透過焦點和 Safari 搜尋結果、遞交和 Siri 提醒與建議來進行搜尋。 這可讓使用者快速存取應用程式內的活動和資訊。

## <a name="sirikitiosplatformsirikitindexmd"></a>[SiriKit](~/ios/platform/sirikit/index.md)

IOS 10 的新功能，SiriKit 可讓 iOS 應用程式使用應用程式延伸模組和新的**意圖**和**意圖 UI**架構，在 Ios 裝置上透過 Siri 和 Maps 應用程式，提供使用者可存取的服務。

## <a name="social-frameworkiosplatformsocial-frameworkmd"></a>[社交架構](~/ios/platform/social-framework.md)

社交架構提供統一的 API 來與社交網路互動，包括_Twitter_和_Facebook_，以及適用于中國使用者的_SinaWeibo_ 。

## <a name="speech-recognitioniosplatformspeechmd"></a>[語音辨識](~/ios/platform/speech.md)

iOS 10 包含新的語音 API，可讓應用程式支援連續語音辨識和轉譯語音（從即時或錄製的音訊串流）到文字。

## <a name="textkitiosplatformtextkitmd"></a>[TextKit](~/ios/platform/textkit.md)

文字套件是新的 API，可提供強大的文字版面配置和呈現功能。 它建置於低層級核心文字架構之上，但比核心文字更容易使用。

## <a name="3d-touchiosplatform3d-touchmd"></a>[3D Touch](~/ios/platform/3d-touch.md)

本文將提供使用新的 3D Touch Api，並介紹如何將壓力敏感手勢新增至在新 iPhone 6s 和 iPhone 6s Plus 裝置上執行的您的 Xamarin iOS 應用程式。

## <a name="touch-id-and-face-id-with-xamariniosiosplatformtouch-id-face-idmd"></a>[使用 Xamarin 的 Touch ID 和臉部識別碼](~/ios/platform/touch-id-face-id.md)

「Touch ID」和「臉部識別碼」是自 iOS 8 之後可用的生物識別驗證系統。 本文和範例說明如何搭配使用 Touch ID 和臉部識別碼與 Xamarin。

## <a name="user-notificationsiosplatformuser-notificationsindexmd"></a>[使用者通知](~/ios/platform/user-notifications/index.md)

IOS 10 的新手，使用者通知架構允許傳遞和處理本機和遠端通知。 藉由使用此架構，應用程式或應用程式延伸模組可以藉由指定一組條件（例如位置或一天的時間）來排程本機通知的傳遞。

## <a name="wide-coloriosplatformwide-colormd"></a>[寬色域](~/ios/platform/wide-color.md)

iOS 10 和 macOS Sierra 增強了擴充範圍像素格式的支援，以及整個系統的寬範圍色彩空間，包括核心圖形、核心影像、金屬和 AVFoundation 等架構。 在整個圖形堆疊中提供這種行為，即可進一步分階段減緩具有寬色彩顯示的裝置支援。

## <a name="binding-objective-cbinding-objective-cindexmd"></a>[繫結 Objective-C](binding-objective-c/index.md)

在 iOS 上工作時，您可能會遇到需要使用協力廠商目標-C 程式庫的情況。 在這些情況下，您可以使用 MonoTouch 的系結專案來C#建立原生目標 C 程式庫的系結。 專案會使用我們用來將 iOS Api 帶入的相同工具C#。 本檔說明如何系結目標-C Api。

## <a name="referencing-native-librariesnative-interopmd"></a>[參考原生程式庫](native-interop.md)

Xamarin 支援使用原生 C 程式庫和目標 C 程式庫來進行連結。 本檔討論如何連結您的原生 C 程式庫與您的 Xamarin. iOS 專案。

## <a name="embedded-frameworksembedded-frameworksmd"></a>[內嵌的架構](embedded-frameworks.md)

說明如何在 Xamarin iOS 應用程式中內嵌目標-C 使用者架構。
