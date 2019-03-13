---
title: WatchOS 3 簡介
description: 這篇文章會介紹的所有新的及修改 Api 和 watchOS 3 中可用的功能適用於 Xamarin 開發人員。
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2017
ms.openlocfilehash: 0428a0df157e359ab34a6a71dbba31bdeb6962fa
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121928"
---
# <a name="introduction-to-watchos-3"></a>WatchOS 3 簡介

_這篇文章會介紹的所有新的及修改 Api 和 watchOS 3 中可用的功能適用於 Xamarin 開發人員。_

本文件將涵蓋下列主題：

- [新功能 watchOS 3](#Whats-New-in-watchOS-3)
    - [Apple 用多少付多少的增強功能](#Apple-Pay-Enhancements)新增 Apple Watch 上的應用程式內的付款的支援。
    - [背景工作](#Background-Tasks)讓應用程式能夠更新其在背景中的資訊，因此當使用者需要它時很好。
    - [複雜功能的增強功能](#Complications-Enhancements)已針對 watchos 3 多所提供的新功能的應用程式。
    - [新的可用架構](#Newly-Available-Frameworks)watchOS 應用程式公開。
    - [主動式建議](#Proactive-Suggestions)可讓應用程式，以主動向使用者顯示資訊。
    * 數個[安全性和隱私權增強功能](#Security-and-Privacy-Enhancements)已對 watchOS 3。
    - [快照集和停駐](#Snapshots-and-Dock)提供快速存取應用程式的 watchOS 應用程式的使用者。
    - [使用者通知](#User-Notifications)本機及遠端通知提供給使用者。
    * 數個[監看式連線 Framework 增強功能](#Watch-Connectivity-Framework-Enhancements)已在 watchOS 3。
    * 數個[WatchKit Framework 增強功能](#WatchKit-Framework-Enhancements)已在 watchOS 3。
    - [健身應用程式增強功能](#Workout-App-Enhancements)健身提供新功能相關的 Apple Watch 應用程式。
- [其他的架構變更](#Additional-Framework-Changes)進行了整個 watchOS 3。
- [已被取代 Api](#Deprecated-APIs) watchOS 3 中。

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>新功能 watchOS 3

Apple 已加入數個新的 Api 和服務，在 watchOS 3 以及現有功能，包括許多增強功能：

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Apple Pay 的增強功能

WatchOS 3 中 「 PassKit 架構已經過擴充，可允許 Apple Watch 上執行的應用程式 （實體產品及服務） 的安全、 應用程式內付款的支援。

使用新[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)並[PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)呈現，並回應使用者可以授權讓付款要求介面的類別。

若要深入了解，請參閱我們[Apple 用多少付多少的增強功能](~/ios/watchos/platform/apple-pay.md)指南。

<a name="Background-Tasks" />

## <a name="background-tasks"></a>背景工作

watchOS 3 引進了數個應用程式可用來更新它的資訊確保其內容，使用者必須先開啟它的背景工作。

下列的新背景工作有：

- **背景應用程式重新整理**- [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask)工作可讓應用程式，以更新其在背景中的狀態。 通常這包括另一個工作，例如從網際網路使用下載新內容[NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)。
- **背景重新整理快照集**- [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)工作可以讓此應用程式之前，系統會將用來填入 Dock 建立快照集更新其內容和 UI。
- **背景監看式連線**- [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask)從配對的 iPhone 接收背景的資料時，啟動工作的應用程式。
- **URL 的工作階段的背景**- [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask)背景傳送需要授權，或完成 （成功或錯誤） 時，啟動工作的應用程式。

若要深入了解，請參閱我們[背景工作](~/ios/watchos/platform/background-tasks.md)指南。

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>複雜功能的增強功能

複雜功能是小型的視覺化元素，提供一目了然的實用資訊。 根據選取 watch 錶面的情況下，使用者必須能夠自訂錶面與一或多個複雜功能。

watchOS 3 可讓應用程式，讓使用者可以存取其資訊一眼，從 watch 錶面建立監看式應用程式的一或多個複雜功能的能力。

此外，複雜功能會提供下列優點：

- 使用者可以快速地啟動複雜功能直接從 watch 錶面上的點選應用程式。
- 監看式臉部原因有應用程式的複雜性的系統，讓應用程式保持在它嘗試啟動應用程式在背景中，已準備好上市狀態將它保存在記憶體，並提供它的額外時間來更新。
- 複雜性會保證至少 50 個每日的推播更新。
- 當應用程式包含複雜功能時，將功能在 Apple Watch Face 資源庫中 (請參閱 Apple[新增至資源庫的複雜功能](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery)文件的詳細資訊)。

在 watchOS 3 ClockKit framework 現在包含的超大型複雜性的數個新範本這類[CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext)和[CLKComplicationTemplateExtraLargeRingImage](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). 此外，若要建立可當地語系化文字，請使用 新方法[CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider)類別。

若要深入了解，請參閱我們[快速互動技術，針對 watchos 3 多](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>新的、 可用的架構

watchOS 3 包含數個現有的 Apple 架構，過去無法使用，例如：

- **SceneKit** -使用 SceneKit 加入 3D 模型納入其中包括大部分的物件系統的功能適用於其他平台例如光源、 陰影、 動畫、 物理的監看式應用程式的 UI。 不支援 3D 空間的音訊、 自訂金屬或 OpenGL 的著色器、 核心映像篩選器和實體為基礎的資料。
- **SpriteKit** -使用 SpriteKit 用於轉譯並以動畫顯示包括動作、 物理、 光源和粒子系統等其他平台上提供的功能大部分的應用程式監看式應用程式的 UI 中的 sprite。 不支援 3D 空間的音訊、 視訊播放和核心映像篩選條件。
- **AVFoundation** -若要管理及播放音訊。
- **CloudKit** -監看式應用程式和 iCloud 容器之間移動資料。
- **核心音訊**-若要管理的資料型別來表示音訊資料流、 複雜的緩衝區和時間值。
- **GameKit** -若要建立社交遊戲。

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>主動式建議

watchOS 3 可讓應用程式，以主動將資訊呈現給使用者，在指定內容。 若要支援此功能， [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity)現在包含`MapItem`屬性，可讓應用程式的其他應用程式提供位置資訊供日後使用。

若要深入了解，請參閱我們[主動式建議簡介](~/ios/watchos/platform/proactive-suggestions.md)指南。

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>安全性和隱私權增強功能

Apple 已對安全性與隱私權 watchOS 3 可協助開發人員改進其應用程式的安全性，並確保使用者的隱私權的數個增強功能。

如此一來，在 watchOS 3 （或更新版本） 上執行的應用程式必須以靜態方式會宣告輸入一或多個隱私權特定中索引鍵來存取特定功能或使用者資訊的意圖其`Info.plist`說明為什麼應用程式想要存取使用者的檔案。

因為 watchOS 3 與 iOS 10 共用這些變更，請參閱我們的 iOS 10[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)指南以取得詳細的資訊。

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>快照集和停駐

在 watchOS 3，Apple 已新增的停駐讓使用者可以釘選自己最愛的應用程式及快速存取。 當使用者按下 Apple Watch 側邊的按鈕時，將會顯示已釘選的應用程式的快照集的資源庫。 使用者可以撥動左邊或右邊，以尋找所需的應用程式，然後點選 應用程式，以啟動快照集取代為執行中應用程式的介面。

系統會定期擷取快照的應用程式的 UI，並會使用這些快照集來填入文件。watchOS 會讓應用程式有機會先更新其內容和 UI，才能建立此快照集。

如需詳細資訊，請參閱我們[背景工作](~/ios/watchos/platform/background-tasks.md)指南與 Apple [WKSnapshotRefreshBackgroundTask 參考](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)。

<a name="User-Notifications" />

## <a name="user-notifications"></a>使用者通知

WatchOS 3 中導入的使用者通知架構支援 Apple Watch 的本機和遠端通知的傳遞。 使用此架構來排程時間等的日期或位置的特定條件為基礎的通知，以及接收和處理通知。

若要深入了解，請參閱我們[快速互動技術，針對 watchos 3 多](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>觀看連線 Framework 增強功能

新`HasContentPending`的屬性[WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession)類別會指出，工作階段接收到的資料在背景中需要處理。 和`RemainingComplicationUserInfoTransfers`屬性會傳回剩餘時間的 iOS 應用程式可以更新其 watchOS 複雜功能。

若要深入了解，請參閱我們[背景工作](~/ios/watchos/platform/background-tasks.md)指南。

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>WatchKit Framework 增強功能

watchOS 3 包含數個 WatchKit 架構包括下列增強功能：

- 應用程式可以取得數位皇冠狀態使用新[WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer)類別，並接收更新，且使用者滾動皇冠 using [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate)類別。
- [WKExtension](https://developer.apple.com/reference/watchkit/wkextension)類別現在包含`ApplicationState`方法並[WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate)應用程式可用來追蹤應用程式的執行階段狀態的常數。 `WKExtension` 也提供可以用來排程背景工作的兩個新方法。
- [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)現在包含新`ApplicationWillEnterForeground`，`ApplicationDidEnterBackground`和`HandleBackgroundTasks`方法來監視應用程式的狀態中的變更和處理背景工作的更新。
- 新[WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer)類別已新增至提供下列類型的筆勢辨識 watch 應用程式： [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer)， [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer)， [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer)並[WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer)。
- 新[WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera)類別提供的介面，針對任何 HomeKit 附加 IP 相機。
- 新[WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie)類別可讓應用程式顯示電影 」 海報 」，在使用者點選它時，會將執行的電影被取代。
- 新[WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton)類別可讓應用程式將會起始時點選付款要求其 UI 中出現的 [Apple Pay] 按鈕。
- 新[WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene)類別呈現在 Apple Watch 上顯示的 SceneKit 場景的介面。
- 新[WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene)類別呈現在 Apple Watch 上顯示的 SpriteKit 場景的介面。

若要深入了解，請參閱我們[快速互動技術，針對 watchos 3 多](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>健身應用程式增強功能

新 watchOS 3 健身相關應用程式都將能夠在 Apple Watch 上的 在背景執行。 若要啟用這項功能 （和 HealthKit 資料存取），應用程式必須包含`WKBackgroundModes`中的索引鍵`Info.plist`具有值檔`workout-processing`。

此外，開發人員現在能夠啟動 watchOS 健身應用程式，從配對的 iPhone 上的 iOS 應用程式版本。

若要深入了解，請參閱我們[健身應用程式增強功能](~/ios/watchos/platform/workout-apps.md)指南。

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>其他的架構變更

除了主要的架構變更和上面所列的內容，Apple 已變更許多其他的次要 framework watchOS 3 中。

若要深入了解，請參閱我們[額外的架構變更](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md)指南。

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>已被取代的 API

WatchOS 3 中，已被取代的下列 Api:

- `UILocalNotification` UIKit 的類別已被取代，應以使用者通知架構，來取代。

請參閱 Apple [watchOS 2.2 watchOS 3.0 API 差異](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html)棄用功能和變更的文件的完整清單。


## <a name="related-links"></a>相關連結

- [watchOS 範例](https://developer.xamarin.com/samples/watchos/all/)
- [WatchOS 3 中最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
