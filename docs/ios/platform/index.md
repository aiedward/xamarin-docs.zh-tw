---
title: iOS 平台功能概觀
description: 描述各種 iOS 版本中引進的功能和其他 iOS 平台功能的各種指南的這個文件連結。
ms.prod: xamarin
ms.assetid: 9F6A27E5-8A87-ADE2-D1EF-5684E7B8C999
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/25/2018
ms.openlocfilehash: 6703e922a628504e0afdcf56533d74741131581a
ms.sourcegitcommit: a6ba6ed086bcde4f52fb05f83c59c68e8aa5e436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540150"
---
# <a name="ios-platform-features-overview"></a>iOS 平台功能概觀

您可以使用 Xamarin.iOS 存取最新的 iOS 版本，以及反白顯示一些 Apple 架構本網頁列出。

## <a name="ios-releases"></a>iOS 版本

|  |  |
|-------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [IOS 13 簡介預覽](~/ios/platform/ios13/index.md) | 本文件說明 Xamarin.iOS 13 預覽。|
| [iOS 12 簡介](~/ios/platform/introduction-to-ios12/index.md) | 建置 Xamarin.iOS 應用程式時，本文件將說明 iOS 12 功能可供使用。|
| [iOS 11 簡介](~/ios/platform/introduction-to-ios11/index.md) | 本文件說明 iOS 11 和 Xcode 9，例如 ARKit、 核心 ML、 Core NFC、 拖曳和卸除、 MapKit、 PDFKit、 SiriKit 和願景的全新和更新功能。 它會連結到指南描述如何使用 Xamarin.iOS 中使用這些功能。 |
| [iOS 10 簡介](~/ios/platform/introduction-to-ios10/index.md) | iOS 10 包含數個新的 Api 和服務，可讓您開發以新特性和功能的應用程式。 使用 iOS 10，應用程式會有新的功能，例如擴充對應、 訊息、 電話和 Siri。 本節說明各項，在 Xamarin.iOS 應用程式中善用這些功能的方法。 |
| [iOS 9 簡介](~/ios/platform/introduction-to-ios9/index.md)   | 此區段會定義從 iOS 8，以及如何在 Xamarin.iOS 應用程式中使用這些功能升級時，在 iOS 9 中所做的變更。 |
| [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)         | iOS 8 作業系統進行大量變更，從 iOS 7。 在這裡，我們示範它們是什麼，以及如何使用它們。 |
| [iOS 7 簡介](~/ios/platform/introduction-to-ios7/index.md)   | 關於在 iOS 7 中引進的主要新 Api，包括檢視控制器轉換，UIView 動畫、 UIKit Dynamics 和文字套件的增強功能。 |
| [iOS 6 簡介](~/ios/platform/introduction-to-ios6/index.md)   | IOS 6，包括集合檢視、 傳遞套件、 活動套件和社交架構所引進的功能的說明。 |

## <a name="apple-payiosplatformapple-paymd"></a>[Apple Pay](~/ios/platform/apple-pay.md)

Apple Pay 引進了與 iOS 8 中，讓使用者支付實體產品，例如食物、 娛樂與透過其 iOS 裝置的成員資格。 它是適用於 iPhone 6 和 iPhone 6 加號，並也可以與在市集購買的 Apple Watch 配對。 在 iPhone 上使用時，它會使用 Touch ID，來確認和授權使用者的信用卡或轉帳卡的交易。

## <a name="callkitiosplatformcallkitmd"></a>[CallKit](~/ios/platform/callkit.md)

在 iOS 10 中新的 CallKit API 提供 VOIP 與 iPhone UI 整合並提供熟悉的介面給終端使用者體驗的應用程式的方式。 使用此 API 使用者可以檢視和互動 VOIP 電話從 iOS 裝置鎖定畫面及管理使用的手機應用程式的連絡人**我的最愛**並**最近**檢視。

## <a name="contacts-and-contactsuiiosplatformcontactsmd"></a>[連絡人和 ContactsUI](~/ios/platform/contacts.md)

使用 iOS 9 的引進，Apple 已發行兩個新的架構`Contacts`和`ContactsUI`、 該取代現有的通訊錄及 iOS 8 及更早版本所使用的地址通訊錄 UI 架構。

## <a name="document-pickeriosplatformdocument-pickermd"></a>[文件選擇器](~/ios/platform/document-picker.md)

文件選擇器可讓應用程式之間共用的文件。 在 iCloud 或不同的應用程式的目錄中，可能會儲存這些文件。 透過一組所共用的文件[文件提供者擴充](~/ios/platform/extensions.md)使用者在其裝置上安裝。

## <a name="eventkitiosplatformeventkitmd"></a>[EventKit](~/ios/platform/eventkit.md)

iOS 有兩個行事曆相關應用程式內建： 行事曆應用程式，並提醒應用程式。 夠簡單了解如何行事曆應用程式管理行事曆的資料，但提醒應用程式是較不明顯。 提醒實際上可以有與其相關聯的原因，完成之時在時的日期等。因此，iOS 會儲存所有行事曆資料，無論行事曆事件或提醒，在單一位置，稱為*行事曆資料庫*。

## <a name="ios-extensionsiosplatformextensionsmd"></a>[iOS 延伸模組](~/ios/platform/extensions.md)

專門擴充功能，為 iOS 8 中引進`UIViewControllers`，會依標準的內容內的 iOS 滿足下列條件為內**通知中心**，如使用者所要求執行自訂的鍵盤類型特製化輸入或其他內容，例如編輯的相片的延伸模組可以在其中提供的特殊效果的篩選器。

## <a name="graphics-and-animation-in-iosiosplatformgraphics-animation-iosindexmd"></a>[圖形和動畫在 iOS 中](~/ios/platform/graphics-animation-ios/index.md)

圖形和動畫在 iOS 中的涵蓋 iOS CoreImage、 Core Graphics 等核心動畫中的核心圖形概念。

## <a name="handoffiosplatformhandoffmd"></a>[Handoff](~/ios/platform/handoff.md)

Apple 已引進遞移式 iOS 8 和 OS X Yosemite (10.10) 提供通用的機制，讓使用者傳輸已啟動他們的裝置，其中的活動中執行相同的應用程式或支援相同的活動的另一個應用程式的另一個裝置。

## <a name="healthkitiosplatformhealthkitmd"></a>[HealthKit](~/ios/platform/healthkit.md)

健全狀況套件會提供安全的資料存放區，如使用者的健康狀態相關資訊。 健全狀況套件應用程式可能會與使用者的明確權限，讀取和寫入此資料存放區以及加入相關的資料時收到通知。 應用程式可以呈現資料，或使用者可以使用 Apple 提供的健全狀況應用程式，來檢視其所有資料的儀表板。

## <a name="homekitiosplatformhomekitmd"></a>[HomeKit](~/ios/platform/homekit.md)

Apple 已引進 HomeKit 在 iOS 8 探索及通訊與使用者的首頁中的家用自動化裝置提供通用架構中。 HomeKit 提供設定裝置和設定動作，以控制它們的常見平台。

## <a name="in-app-purchasingiosplatformin-app-purchasingindexmd"></a>[App 內購買](~/ios/platform/in-app-purchasing/index.md)

iOS 應用程式可以銷售數位產品或服務使用 StoreKit – 一組以進行使用者透過其 Apple id。 與財務交易 Apple 伺服器進行通訊的 iOS 所提供的 Api StoreKit Api 會擷取產品資訊和進行交易與 – 沒有任何使用者介面元件。 實作應用程式內購買的應用程式必須建置自己的使用者介面，並追蹤自訂程式碼，以提供必要的產品或服務給使用者的購買項目。

## <a name="ios-gaming-apisiosplatformgamingindexmd"></a>[iOS 遊戲 Api](~/ios/platform/gaming/index.md)

Apple 已改進數個技術在 iOS 9 遊戲 Api 可讓您更輕鬆地在 Xamarin.iOS 應用程式中實作的遊戲圖形和音訊。 這些包括這兩種方便開發的高階架構和 iOS 裝置的 GPU 經改善的速度和圖形功能的力量。

## <a name="message-app-integrationiosplatformmessage-app-integrationindexmd"></a>[訊息應用程式整合](~/ios/platform/message-app-integration/index.md)

新增至 iOS 10，訊息應用程式擴充功能則是與整合**訊息**給使用者的應用程式並為您介紹新功能。 延伸模組可以傳送文字、 貼紙、 媒體檔案和互動式訊息。

## <a name="multitasking-for-ipadiosplatformmultitaskingmd"></a>[適用於 iPad 的多工](~/ios/platform/multitasking.md)

iOS 9 新增多工作業支援同時執行兩個應用程式，在特定的 iPad 的硬體上。 適用於 iPad 的多工支援透過下列功能：透過投影片、 分割檢視及 Picture in Picture。

## <a name="passkitiosplatformpasskitmd"></a>[PassKit](~/ios/platform/passkit.md)

Passbook 是應用程式適用於 Iphone 和 iPod 接觸 ios 6。 它會儲存和顯示條碼及其他資訊，以連結客戶與 '真實世界' 在電話上的交易。 傳遞所產生的商家並傳送至客戶透過電子郵件、 Url 或從零售商自己 iOS 應用程式。 Passbook 儲存和組織在手機上的所有階段，並根據日期/時間或裝置的位置在鎖定畫面上顯示傳遞提醒。

本文件介紹 Passbook，使用 Xamarin.iOS，傳遞套件 API，並討論如何在您的伺服器上實作傳遞。

## <a name="photokitiosplatformphotokitmd"></a>[PhotoKit](~/ios/platform/photokit.md)

相片套件是新的架構，可讓應用程式來查詢系統映像庫，並建立自訂使用者介面來檢視和修改其內容。 它包含數個類別，代表影像和視訊資產，以及資產，例如專輯和資料夾的集合。

## <a name="request-app-reviewiosplatformrequest-app-reviewmd"></a>[要求應用程式檢閱](~/ios/platform/request-app-review.md)

剛接觸 iOS 10.3，`RequestReview()`方法可讓 iOS 應用程式以要求使用者評等或檢閱它。 使用者從 App Store 安裝的傳送應用程式中呼叫這個方法時，iOS 10 會處理整個的評等，和開發人員適用的檢閱程序。 此程序應用程式存放區原則所控管，因為警示可能會或可能不會顯示。

## <a name="search-apisiosplatformsearchindexmd"></a>[搜尋 API](~/ios/platform/search/index.md)

搜尋已展開於 iOS 9，提供絕佳的新方法，來存取資訊和 Xamarin.iOS 應用程式內的功能。 使用新的應用程式搜尋 Api，應用程式內容會透過焦點以及 Safari 搜尋結果中，遞移式及 Siri 提醒及建議的搜尋。 這可讓使用者快速存取活動和您的應用程式內的資訊。

## <a name="sirikitiosplatformsirikitindexmd"></a>[SiriKit](~/ios/platform/sirikit/index.md)

新增至 iOS 10，SiriKit 可讓 iOS 應用程式提供使用應用程式擴充功能和新的 iOS 裝置上使用 Siri 和對應的應用程式的使用者可以存取的服務**意圖**並**Intents UI**架構。

## <a name="social-frameworkiosplatformsocial-frameworkmd"></a>[社交架構](~/ios/platform/social-framework.md)

社交架構提供統一的 API 與包括社交網路互動_Twitter_並_Facebook_，以及_SinaWeibo_中國中的使用者。

## <a name="speech-recognitioniosplatformspeechmd"></a>[語音辨識](~/ios/platform/speech.md)

iOS 10 包含新的語音 API 可讓應用程式，以支援連續的語音辨識和轉譯的語音功能 （即時或錄製音訊資料流） 成為文字。

## <a name="textkitiosplatformtextkitmd"></a>[TextKit](~/ios/platform/textkit.md)

文字套件是新的 API，提供功能強大的文字版面配置和轉譯功能。 它建置在核心文字架構的低層級之上，但更容易就能使用比核心文字。

## <a name="3d-touchiosplatform3d-touchmd"></a>[3D Touch](~/ios/platform/3d-touch.md)

這篇文章會提供和簡介使用新的 3D 觸控 Api 將壓力機密筆勢新增至您的 Xamarin.iOS 應用程式執行新的 iphone 6s 和 iPhone 6s Plus 裝置。

## <a name="touch-idiosplatformtouchidmd"></a>[Touch ID](~/ios/platform/touchid.md)

Touch ID 是在 iOS 7 引進，做為驗證使用者-密碼類似。 不過，已限制為解除鎖定裝置、 使用應用程式存放區、 使用 iTunes 及驗證只 iCloud 鑰匙圈。

## <a name="user-notificationsiosplatformuser-notificationsindexmd"></a>[使用者通知](~/ios/platform/user-notifications/index.md)

新 iOS 10，使用者通知架構可讓傳遞和本機及遠端通知的處理。 使用此架構，應用程式或應用程式擴充功能可以排程傳遞本機通知藉由指定一組條件，例如位置或一天的時間。

## <a name="wide-coloriosplatformwide-colormd"></a>[寬色域](~/ios/platform/wide-color.md)

iOS 10 和 macOS Sierra 增強的擴充範圍像素格式和整個系統，包括架構，例如核心圖形、 Core 映像、 裸機和 AVFoundation 寬色域圖色彩空間的支援。 藉由提供整個圖形堆疊在這種行為，進一步降低與寬的色彩顯示裝置的支援。

## <a name="binding-objective-cbinding-objective-cindexmd"></a>[繫結 Objective-C](binding-objective-c/index.md)

IOS 上工作時，您可能會遇到您要使用第三方 Objective C 程式庫的情況。 在這些情況下，您可以使用 MonoTouch 的繫結專案建立 C# 繫結至原生的 Objective C 程式庫。 專案會使用相同的工具，我們使用 iOS Api，將以C#。 本文件說明如何將繫結 Objective C Api。

## <a name="referencing-native-librariesnative-interopmd"></a>[參考原生程式庫](native-interop.md)

Xamarin.iOS 可支援使用原生 C 程式庫和 OBJECTIVE-C 程式庫連結。 本文件討論如何連結您原生的 C 程式庫與您的 Xamarin.iOS 專案。

## <a name="embedded-frameworksembedded-frameworksmd"></a>[內嵌的架構](embedded-frameworks.md)

說明如何在 Xamarin.iOS 應用程式中內嵌 OBJECTIVE-C 使用者架構。
