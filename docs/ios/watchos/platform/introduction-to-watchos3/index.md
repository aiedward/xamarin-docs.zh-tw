---
title: watchOS 3 簡介
description: 本文介紹適用于 Xamarin 開發人員的 watchOS 3 中的所有新的和已修改的 Api 和功能。
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 10/07/2017
ms.openlocfilehash: b167ddca86148818c534b7a9b0e338f8a4406b15
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767760"
---
# <a name="introduction-to-watchos-3"></a>watchOS 3 簡介

_本文介紹適用于 Xamarin 開發人員的 watchOS 3 中的所有新的和已修改的 Api 和功能。_

本檔將涵蓋下列主題：

- [WatchOS 3 的新功能](#Whats-New-in-watchOS-3)
  - [Apple Pay 增強功能](#Apple-Pay-Enhancements)會在 Apple Watch 上新增應用程式內付款支援。
  - [背景](#Background-Tasks)工作可讓應用程式在背景中更新其資訊，以便在使用者需要時準備就緒。
  - 為應用程式提供新功能的 watchOS 3 已進行[複雜的增強](#Complications-Enhancements)功能。
  - [新推出](#Newly-Available-Frameworks)的架構已公開給 watchOS apps。
  - [主動式建議](#Proactive-Suggestions)可讓應用程式主動向使用者顯示資訊。
  - 已對 watchOS 3 進行幾項[安全性和隱私權增強](#Security-and-Privacy-Enhancements)。
  - [快照集和 Dock](#Snapshots-and-Dock)可讓使用者快速存取應用程式 watchOS 應用程式。
  - [使用者通知](#User-Notifications)會為使用者提供本機和遠端通知。
  - WatchOS 3 中有數個[Watch 連線架構的增強功能](#Watch-Connectivity-Framework-Enhancements)。
  - 已在 watchOS 3 中進行數個[WatchKit 架構的增強功能](#WatchKit-Framework-Enhancements)。
  - [健身應用程式增強功能](#Workout-App-Enhancements)可為健身相關 Apple Watch 應用程式提供新的功能。
- WatchOS 3 中的[其他架構變更](#Additional-Framework-Changes)已進行。
- WatchOS 3 中已[淘汰的 api](#Deprecated-APIs) 。

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>WatchOS 3 的新功能

Apple 已在 watchOS 3 中新增數個新的 Api 和服務，以及現有功能的許多增強功能，包括：

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Apple Pay 增強功能

在 watchOS 3 中，已擴充 PassKit 架構，以允許在 Apple Watch 上執行的應用程式支援安全的應用程式內付款（這兩者都是實體貨物和服務）。

使用新的[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)和[PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)類別來呈現和回應使用者可以授權付款要求的介面。

若要深入瞭解，請參閱我們的[Apple Pay 增強功能](~/ios/watchos/platform/apple-pay.md)指南。

<a name="Background-Tasks" />

## <a name="background-tasks"></a>背景工作

watchOS 3 引進數個背景工作，應用程式可以使用這些工作來更新其資訊，確保其在開啟它之前，擁有使用者所需的內容。

下列為可用的新背景工作：

- **背景應用程式**重新整理- [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask)工作可讓應用程式在背景中更新其狀態。 通常這會包含另一項工作，例如使用[NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)從網際網路下載新內容。
- **背景快照**集重新整理- [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)工作可讓應用程式在系統取得將用來填入 Dock 的快照之前，更新其內容和 UI。
- **背景監看**連線-當應用程式從配對的 iPhone 收到背景資料時，會啟動[WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask)工作。
- **背景 URL 會話**-背景傳輸需要授權或完成（成功或發生錯誤）時，會為應用程式啟動[WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask)工作。

若要深入瞭解，請參閱我們的[背景](~/ios/watchos/platform/background-tasks.md)工作指南。

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>複雜的增強功能

複雜之處是小型視覺元素，提供有用的資訊一目了然。 根據選取的監看式臉部，使用者可以自訂具有一或多個複雜的監看面。

watchOS 3 讓應用程式能夠針對 watch 應用程式建立一或多個複雜的功能，讓使用者可以從監看面快速存取其資訊。

此外，複雜性也提供下列優點：

- 使用者可以直接從監看表面上點擊複雜的方式來快速啟動應用程式。
- 將應用程式的其中一個複雜性放在監看面上，會導致系統將應用程式保持在「立即啟動」狀態，讓它嘗試在背景啟動應用程式、將它保留在記憶體中，並提供更多時間來更新。
- 每日至少會保證有50個推播更新的複雜性。
- 當應用程式包含複雜的複雜性時，它會在 Apple Watch 臉部資源庫中加以精選（如需詳細資訊，請參閱 Apple 對圖庫檔的[增加複雜性](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery)）。

在 watchOS 3 中，ClockKit 架構現在包含數個新的範本，可用於額外的大型複雜，例如[CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext)和[CLKComplicationTemplateExtraLargeRingImage](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage)。 此外，若要建立可當地語系化的文字，請使用[CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider)類別的新方法。

若要深入瞭解，請參閱[watchOS 3 的快速互動技術](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>新推出的架構

watchOS 3 包含幾個先前無法使用的現有 Apple 架構，例如：

- **SceneKit** -使用 SceneKit 將3d 模型包含在監看式應用程式的 UI 中，包括其他平臺（例如光源、陰影、動畫、物理學和物件系統）上可用的大部分功能。 不支援3D 空間音訊、自訂金屬或 OpenGL 著色器、核心影像篩選和以實體為基礎的材質。
- **SpriteKit** -使用 SpriteKit 在應用程式監看式應用程式的 UI 中呈現並建立動畫，包括其他平臺（例如動作、物理、光源和物件）所提供的大部分功能。 不支援3D 空間音訊、影片播放和核心影像篩選。
- **AVFoundation** -用來管理和播放音訊。
- **CloudKit** -在監看式應用程式和 iCloud 容器之間移動資料。
- **核心音訊**-用以管理代表音訊資料流程、複雜緩衝區和時間值的資料類型。
- **GameKit** -建立社交遊戲。

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>主動式建議

watchOS 3 允許應用程式在指定的內容中主動向使用者呈現資訊。 為了支援這項功能， [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity)現在包含`MapItem`屬性，可讓應用程式提供位置資訊供其他應用程式稍後使用。

若要深入瞭解，請參閱《[主動式建議簡介](~/ios/watchos/platform/proactive-suggestions.md)》指南。

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>安全性和隱私權增強功能

Apple 在 watchOS 3 的安全性和隱私權方面做了幾項增強，可協助開發人員改善其應用程式的安全性，並確保使用者的隱私權。

因此，在 watchOS 3 （或更新版本）上執行的應用程式必須在其`Info.plist`檔案中輸入一或多個隱私權特定金鑰，以靜態方式宣告其意圖，以存取特定功能或使用者資訊，並向使用者說明應用程式想要取得存取權的原因。

由於 watchOS 3 與 iOS 10 共用這些變更，請參閱我們的 iOS 10[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)指南，以取得詳細資訊。

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>快照集和停駐

在 watchOS 3 中，Apple 已新增 Dock，讓使用者可以釘選其最愛的應用程式並快速存取。 當使用者按下 Apple Watch 的側邊按鈕時，將會顯示已釘選的應用程式快照集資源庫。 使用者可以向左或向右滑動以尋找所需的應用程式，然後點一下應用程式來啟動它，將快照集取代為執行中應用程式的介面。

系統會定期取得應用程式 UI 的快照，並使用這些快照集來填入檔。watchOS 可讓應用程式在取得此快照集之前，更新其內容和 UI 的機會。

如需詳細資訊，請參閱我們的[背景](~/ios/watchos/platform/background-tasks.md)工作指南和 Apple 的[WKSnapshotRefreshBackgroundTask 參考](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)。

<a name="User-Notifications" />

## <a name="user-notifications"></a>使用者通知

WatchOS 3 中引進的使用者通知架構支援將本機和遠端通知傳遞至 Apple Watch。 使用此架構來根據特定條件（例如當天時間或位置），以及接收和處理通知來排定通知。

若要深入瞭解，請參閱[watchOS 3 的快速互動技術](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>觀看連線能力架構的增強功能

`HasContentPending`WCSession 類別[的新](https://developer.apple.com/reference/watchconnectivity/wcsession)屬性指出會話已在背景中接收需要處理的資料。 `RemainingComplicationUserInfoTransfers`和屬性會傳回 iOS 應用程式可以更新其 watchOS 複雜的剩餘時間。

若要深入瞭解，請參閱我們的[背景](~/ios/watchos/platform/background-tasks.md)工作指南。

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>WatchKit Framework 增強功能

watchOS 3 包含 WatchKit 架構的數個增強功能，包括下列各項：

- 應用程式可以使用新的[WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer)類別來取得 Digital Crown 的狀態，並在使用者使用[WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate)類別來旋轉 Crown 時接收更新。
- [WKExtension](https://developer.apple.com/reference/watchkit/wkextension)類別現在包含`ApplicationState`方法和[WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate)常數，應用程式可以使用它來追蹤應用程式的執行時間狀態。 `WKExtension`也提供兩種可用來排程背景工作的新方法。
- [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)現在包含新`ApplicationWillEnterForeground` `ApplicationDidEnterBackground`的和`HandleBackgroundTasks`方法，可監視應用程式狀態的變更並處理背景工作更新。
- 已加入新的[WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer)類別，以提供下列類型的手勢辨識給監看式應用程式：[WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer)、 [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer)、 [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer)和[WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer)。
- 新的[WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera)類別會為任何 HomeKit 連接的 IP 攝影機提供介面。
- 新的[WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie)類別可讓應用程式顯示電影「海報」，該影片會在使用者點擊時由執行中的電影取代。
- 新的[WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton)類別可讓應用程式在其 UI 中顯示 [Apple Pay] 按鈕，以在攻絲時起始付款要求。
- 新的[WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene)類別會呈現介面，以在 Apple Watch 上顯示 SceneKit 場景。
- 新的[WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene)類別會呈現介面，以在 Apple Watch 上顯示 SpriteKit 場景。

若要深入瞭解，請參閱[watchOS 3 的快速互動技術](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>體能訓練應用程式增強功能

WatchOS 3 的新功能：健身相關應用程式可在 Apple Watch 的背景中執行。 若要啟用這項功能（並取得 HealthKit 資料的存取權），應用程式`WKBackgroundModes`必須`Info.plist`在檔案中包含具有值`workout-processing`的金鑰。

此外，開發人員現在可以從配對 iPhone 上的 iOS 應用程式版本啟動 watchOS 健身應用程式。

若要深入瞭解，請參閱我們的[健身應用程式增強功能](~/ios/watchos/platform/workout-apps.md)指南。

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>其他架構變更

除了上面所列的主要架構變更和新增功能之外，Apple 也在 watchOS 3 中進行了許多其他的次要架構變更。

若要深入瞭解，請參閱我們的[其他架構變更](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md)指南。

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>已被取代的 API

下列 Api 在 watchOS 3 中已被取代：

- UIKit `UILocalNotification`的類別已被取代，應取代為使用者通知架構。

如需棄用功能和變更的完整清單，請參閱 Apple 的[watchOS 2.2 To watchOS 3.0 API 差異](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html)檔。

## <a name="related-links"></a>相關連結

- [watchOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
- [WatchOS 3 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
