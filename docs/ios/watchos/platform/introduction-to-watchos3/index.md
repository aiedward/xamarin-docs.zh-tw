---
title: WatchOS 3 簡介
description: 本文介紹的所有新的及修改應用程式開發介面和 watchOS 3 中可用的功能適用於 Xamarin 開發人員。
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/07/2017
ms.openlocfilehash: 506e3795538ceffc77301a608c504fc6ec2045a7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-watchos-3"></a>WatchOS 3 簡介

_本文介紹的所有新的及修改應用程式開發介面和 watchOS 3 中可用的功能適用於 Xamarin 開發人員。_

本文件將涵蓋下列主題：

- [功能 watchOS 3 中的新功能](#Whats-New-in-watchOS-3)
    - [Apple 支付增強功能](#Apple-Pay-Enhancements)Apple Watch 上加入應用程式內付款的支援。
    - [背景工作](#Background-Tasks)讓應用程式能夠更新它在背景中的資訊，使其當使用者需要它。
    - [複雜性增強功能](#Complications-Enhancements)已為 watchOS 3 提供的應用程式的新功能。
    - [新可用的 Framework](#Newly-Available-Frameworks) watchOS 應用程式公開。
    - [主動式建議](#Proactive-Suggestions)允許應用程式主動對使用者顯示資訊。
    * 數個[安全性和隱私權增強功能](#Security-and-Privacy-Enhancements)已對 watchOS 3。
    - [快照集和停駐](#Snapshots-and-Dock)使用者提供快速存取應用程式 watchOS 應用程式。
    - [使用者通知](#User-Notifications)提供給使用者的本機和遠端的通知。
    * 數個[監看式連線架構增強功能](#Watch-Connectivity-Framework-Enhancements)watchOS 3 中已經出現。
    * 數個[WatchKit 架構增強功能](#WatchKit-Framework-Enhancements)watchOS 3 中已經出現。
    - [健身應用程式增強功能](#Workout-App-Enhancements)健身提供下列新功能相關的 Apple Watch 應用程式。
- [其他架構變更](#Additional-Framework-Changes)作了整個 watchOS 3。
- [已被取代 Api](#Deprecated-APIs) watchOS 3 中。

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>功能 watchOS 3 中的新功能

Apple 已加入數個新的 Api 和服務中 watchOS 3 以及現有功能，包括許多增強功能：

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Apple Pay 增強功能

在 watchOS 3，已展開 PassKit 架構以支援安全、 在應用程式付款 （實體貨物與服務） 的 Apple Watch 上執行的應用程式。

使用新[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)和[PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)呈現，並回應使用者可以在其中授權付款要求介面的類別。

若要進一步了解，請參閱我們[Apple 支付增強功能](~/ios/watchos/platform/apple-pay.md)指南。

<a name="Background-Tasks" />

## <a name="background-tasks"></a>背景工作

watchOS 3 會介紹幾個背景工作的應用程式可以用來更新他們開啟之前，使用者需要其資訊確保它包含的內容。

下列新的背景工作可：

- **背景應用程式重新整理**- [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask)工作可以讓此應用程式更新其狀態在背景中的。 通常這包括另一個工作，例如下載新的內容，從網際網路使用[NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)。
- **背景重新整理快照集**- [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)工作可以讓此應用程式更新其內容和 UI，系統會將用來填入停駐的快照集之前。
- **背景監看式連線**- [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask)配對 iPhone 從接收背景資料時，啟動工作的應用程式。
- **URL 的工作階段的背景**- [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask)背景傳送需要授權，或完成 （成功或錯誤） 時，啟動工作的應用程式。

若要進一步了解，請參閱我們[背景工作](~/ios/watchos/platform/background-tasks.md)指南。

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>複雜性增強功能

複雜功能是提供有用的資訊一眼的小型視覺元素。 根據選取的監看式字體，使用者會具有自訂 watch 錶面與一或多個複雜功能的能力。

watchOS 3 提供應用程式，讓使用者可以存取其資訊在摘要 watch 錶面從建立一或多個監看式應用程式的複雜功能的能力。

此外，複雜性會提供下列優點：

- 使用者點選直接從 watch 錶面複雜功能，可以快速啟動應用程式。
- 監看式朝原因上有一個應用程式的複雜性系統上，以保留應用程式在已備妥要啟動狀態會嘗試啟動應用程式在背景中，將它保存在記憶體，讓它額外時間來更新。
- 複雜性會保證至少 50 推送更新每日。
- 當應用程式包含的複雜性時，它會顯示在 Apple Watch 朝資源庫 (請參閱 Apple[新增至圖庫的複雜性](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery)文件的詳細資訊)。

在 watchOS 3，ClockKit framework 現在包含幾個新的範本，超大型的複雜性，例如[CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext)和[CLKComplicationTemplateExtraLargeRingImage](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). 此外，若要建立可當地語系化的文字，使用 新方法[CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider)類別。

若要進一步了解，請參閱我們[watchOS 3 快速互動技術](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>新的、 可用的架構

watchOS 3 包含數個現有的 「 Apple 」 架構，先前不可用，例如：

- **SceneKit** -使用 SceneKit 包含 3D 模型到其中包括大部分的物件系統的功能適用於其他平台像陰影，動畫的光源物理的監看式應用程式的 UI。 不支援 3D 空間中的音訊、 自訂金屬或 OpenGL 的著色器、 Core 映像篩選器和實體為基礎的資料。
- **SpriteKit** -使用 SpriteKit 呈現和動畫小，其中包括大部分的動作、 物理、 光線及物件的系統等其他平台上可用的功能的應用程式監看式應用程式的 UI 中。 不支援 3D 空間中的音訊、 視訊播放和核心映像篩選器。
- **AVFoundation** -若要管理及播放音訊。
- **CloudKit** -若要監看式應用程式與 iCloud 容器之間移動資料。
- **核心音訊**-若要管理的代表音訊資料流、 複雜的緩衝區和時間值的資料類型。
- **GameKit** -若要建立社交遊戲。

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>主動式建議

watchOS 3 可讓應用程式，以主動呈現資訊中的使用者提供內容。 若要支援這項功能， [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity)現在包含`MapItem`屬性，可讓應用程式的其他應用程式提供位置資訊供日後使用。

若要進一步了解，請參閱我們[簡介主動式建議](~/ios/watchos/platform/proactive-suggestions.md)指南。

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>安全性和隱私權增強功能

Apple 已對安全性和隱私權可協助開發人員改善其應用程式的安全性，並確保使用者的隱私權 watchOS 3 中的數個增強功能。

如此一來，watchOS 3 （或更新版本） 上執行的應用程式必須以靜態方式宣告試圖輸入一或多個隱私權特定中索引鍵來存取特定功能或使用者資訊及其`Info.plist`解釋為什麼應用程式想要存取的使用者的檔案。

因為 watchOS 3 與 iOS 10 共用這些變更，請參閱我們的 iOS 10[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)指南以取得詳細的資訊。

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>快照集和停駐

WatchOS 3，在 Apple 已加入停駐讓使用者可以釘選自己喜愛的應用程式及快速存取。 當使用者按下 Apple Watch 並排 按鈕時，將會顯示組件庫的已釘選的應用程式的快照集。 使用者可以撥動左邊或右邊，以尋找所需的應用程式，然後點選應用程式來啟動它以執行的應用程式介面取代快照集。

系統會定期接受快照集的應用程式的 UI，並會使用這些快照集來填入文件。watchOS 可讓應用程式更新其內容和 UI 之前擷取這個快照集。

如需詳細資訊，請參閱我們[背景工作](~/ios/watchos/platform/background-tasks.md)指南與 Apple [WKSnapshotRefreshBackgroundTask 參考](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)。

<a name="User-Notifications" />

## <a name="user-notifications"></a>使用者通知

WatchOS 3 中導入的使用者通知架構支援本機和遠端的通知給 Apple Watch 的傳遞。 使用此架構來排程例如時間的日次或位置的特定條件為基礎的通知，以及接收和處理通知。

若要進一步了解，請參閱我們[watchOS 3 快速互動技術](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>監看連接架構增強功能

新`HasContentPending`屬性[WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession)類別會指出工作階段具有需要處理在背景中接收到資料。 和`RemainingComplicationUserInfoTransfers`屬性傳回的剩餘時間的 iOS 應用程式可以更新其 watchOS 複雜功能。

若要進一步了解，請參閱我們[背景工作](~/ios/watchos/platform/background-tasks.md)指南。

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>WatchKit 架構增強功能

watchOS 3 包含數個 WatchKit 架構包括下列增強功能：

- 應用程式可以取得數位皇冠狀態使用新[WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer)類別，並接收更新，且使用者滾動皇冠使用[WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate)類別。
- [WKExtension](https://developer.apple.com/reference/watchkit/wkextension)類別現在包含`ApplicationState`方法和[WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate)應用程式可以用來追蹤應用程式的執行階段狀態的常數。 `WKExtension` 也提供可用來排程背景工作的兩個新方法。
- [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)現在包含新`ApplicationWillEnterForeground`，`ApplicationDidEnterBackground`和`HandleBackgroundTasks`監視應用程式的狀態中的變更和處理背景工作更新的方法。
- 新[WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer)類別已新增至提供下列類型的筆勢辨識監看式應用程式： [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer)， [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer)， [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer)和[WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer)。
- 新[WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera)類別會提供介面的任何 HomeKit 附加 IP 相機。
- 新[WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie)類別可讓應用程式，以顯示電影"海報"，當使用者點選它被執行的電影。
- 新[WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton)類別可讓應用程式將會起始時點選付款要求其 UI 中出現 Apple Pay 按鈕。
- 新[WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene)類別代表在 Apple Watch 上顯示 SceneKit 場景的介面。
- 新[WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene)類別代表在 Apple Watch 上顯示 SpriteKit 場景的介面。

若要進一步了解，請參閱我們[watchOS 3 快速互動技術](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>健身應用程式增強功能

新增至 watchOS 3，健身相關的應用程式具有 Apple Watch 在背景中執行的能力。 若要啟用這項功能 （和存取 HealthKit 資料），應用程式必須包含`WKBackgroundModes`中的索引鍵`Info.plist`檔案具有值`workout-processing`。

此外，開發人員現在能夠啟動 watchOS 健身應用程式從配對的 iPhone 上的 iOS 應用程式版本。

若要進一步了解，請參閱我們[健身應用程式增強功能](~/ios/watchos/platform/workout-apps.md)指南。

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>其他架構變更

主要架構變更和加入上面所列的項目，除了 Apple 進行了許多其他次要架構變更 watchOS 3 中。

若要進一步了解，請參閱我們[其他架構變更](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md)指南。

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>已被取代的 API

WatchOS 3 中，已被取代下列 Api:

- `UILocalNotification` UIKit 的類別已被取代，應該使用使用者通知架構，來取代。

請參閱 Apple [watchOS 2.2 watchOS 3.0 API 差異](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html)deprecations 和變更的文件的完整清單。


## <a name="related-links"></a>相關連結

- [watchOS 範例](https://developer.xamarin.com/samples/watchos/all/)
- [什麼是 watchOS 3 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
