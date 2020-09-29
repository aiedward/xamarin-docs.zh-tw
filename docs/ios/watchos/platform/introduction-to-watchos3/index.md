---
title: watchOS 3 簡介
description: 本文將介紹適用于 Xamarin 開發人員的 watchOS 3 中所有新的和修改過的 Api 和功能。
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/07/2017
ms.openlocfilehash: ec43c98b891ee1541be9b1ac2563e280730628e3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430501"
---
# <a name="introduction-to-watchos-3"></a>watchOS 3 簡介

_本文將介紹適用于 Xamarin 開發人員的 watchOS 3 中所有新的和修改過的 Api 和功能。_

本檔將討論下列主題：

- [WatchOS 3 的新功能](#Whats-New-in-watchOS-3)
  - [Apple Pay 增強功能](#Apple-Pay-Enhancements) 可為 Apple Watch 上的應用程式內付款新增支援。
  - [背景](#Background-Tasks) 工作可讓應用程式在背景中更新其資訊，使其在使用者需要時可供使用。
  - 為應用程式提供新功能的 watchOS 3 已進行[複雜的增強](#Complications-Enhancements)功能。
  - [新推出](#Newly-Available-Frameworks) 的架構已公開給 watchOS apps。
  - [主動式建議](#Proactive-Suggestions) 可讓應用程式主動向使用者顯示資訊。
  - 已對 watchOS 3 進行數項 [安全性和隱私權的增強功能](#Security-and-Privacy-Enhancements) 。
  - [快照集和停駐](#Snapshots-and-Dock) 可讓使用者快速存取 app watchOS apps。
  - [使用者通知](#User-Notifications) 會提供本機和遠端通知給使用者。
  - WatchOS 3 中有幾個 [監看式連線架構的增強功能](#Watch-Connectivity-Framework-Enhancements) 。
  - WatchOS 3 已進行數個 [WatchKit 架構的增強功能](#WatchKit-Framework-Enhancements) 。
  - [健身應用程式增強功能](#Workout-App-Enhancements) 可提供新的 Apple Watch 應用程式相關測驗功能。
- WatchOS 3 中已進行[其他架構變更](#Additional-Framework-Changes)。
- WatchOS 3 中已[淘汰的 api](#Deprecated-APIs) 。

<a name="Whats-New-in-watchOS-3"></a>

## <a name="whats-new-in-watchos-3"></a>WatchOS 3 的新功能

Apple 已在 watchOS 3 中新增數個新的 Api 和服務，以及現有功能的許多增強功能，包括：

<a name="Apple-Pay-Enhancements"></a>

## <a name="apple-pay-enhancements"></a>Apple Pay 增強功能

在 watchOS 3 中，PassKit 架構已經過擴充，可支援在 Apple Watch 上執行的應用程式) 實體貨物和服務的安全、應用程式內付款 (。

使用新的 [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) 和 [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) 類別來呈現並回應使用者可以授權付款要求的介面。

若要深入瞭解，請參閱我們的 [Apple Pay 增強功能](~/ios/watchos/platform/apple-pay.md) 指南。

<a name="Background-Tasks"></a>

## <a name="background-tasks"></a>背景工作

watchOS 3 引進了數個背景工作，可讓應用程式用來更新其資訊，以確保在其開啟之前，其具有使用者需要的內容。

以下是可用的新背景工作：

- **背景應用程式** 重新整理- [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) 工作可讓應用程式在背景中更新其狀態。 這通常會包含另一項工作，例如使用 [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)從網際網路下載新的內容。
- **背景快照** 集重新整理： [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) 工作可讓應用程式在系統取得將用來擴展 Dock 的快照之前，更新其內容和 UI。
- **背景監看** 式連線-從配對的 iPhone 接收背景資料時，會為應用程式啟動 [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) 工作。
- **背景 URL 會話** -背景傳輸需要授權或成功完成 (或) 錯誤時，即會啟動應用程式的 [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) 工作。

若要深入瞭解，請參閱我們的 [背景](~/ios/watchos/platform/background-tasks.md) 工作指南。

<a name="Complications-Enhancements"></a>

## <a name="complications-enhancements"></a>複雜的增強功能

複雜的是小型的視覺元素，提供有用的資訊。 根據選取的監看式臉部，使用者可以自訂一個或多個複雜的監看式臉部。

watchOS 3 讓應用程式能夠為 watch 應用程式建立一或多個複雜的功能，讓使用者可以從 watch 臉部存取其資訊。

此外，複雜功能提供下列優點：

- 使用者可以快速地啟動應用程式，方法是直接從監看式臉部中使用複雜的程式。
- 讓應用程式在監看表面上的複雜，讓系統將應用程式保持在立即啟動的狀態，讓應用程式在背景中啟動應用程式，並將它保留在記憶體中，並提供額外的時間來更新。
- 每日至少有50個推送更新保證會有複雜性。
- 當應用程式包含複雜功能時，將會在 Apple Watch 的臉部圖庫中加以精選。

在 watchOS 3 中，ClockKit framework 現在包含數個新的範本，可進行額外的大型複雜，例如 [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) 和 [CLKComplicationTemplateExtraLargeRingImage](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage)。 此外，若要建立可當地語系化的文字，請使用 [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) 類別的新方法。

若要深入瞭解，請參閱我們 [適用于 watchOS 3 的快速互動技術](~/ios/watchos/platform/quick-interaction-techniques.md) 指南。

<a name="Newly-Available-Frameworks"></a>

## <a name="newly-available-frameworks"></a>新推出的架構

watchOS 3 包含數個先前無法使用的現有 Apple framework，例如：

- **SceneKit** -使用 SceneKit 將3d 模型包含在 watch 應用程式的 UI 中，包括其他平臺（例如光源、陰影、動畫、物理和物件）上可用的大部分功能。 不支援3D 空間音訊、自訂金屬或 OpenGL 著色器、核心影像篩選和以實體為基礎的材質。
- **SpriteKit** -使用 SpriteKit 在應用程式監看式應用程式的 UI 中轉譯和建立 sprite 的動畫，包括其他平臺（例如動作、物理、光源和粒子系統）上可用的大部分功能。 不支援3D 空間音訊、影片播放和核心影像篩選。
- **AVFoundation** -用以管理及播放音訊。
- **CloudKit** -在 watch 應用程式和 iCloud 容器之間移動資料。
- **核心音訊** -管理用來代表音訊資料流程、複雜緩衝區和時間值的資料類型。
- **GameKit** -建立社交遊戲。

<a name="Proactive-Suggestions"></a>

## <a name="proactive-suggestions"></a>主動式建議

watchOS 3 可讓應用程式在指定的內容中主動呈現資訊給使用者。 為了支援這項功能， [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) 現在包含 `MapItem` 屬性，可讓應用程式提供位置資訊，供其他應用程式稍後使用。

若要深入瞭解，請參閱我們 [的主動式建議指南簡介](~/ios/watchos/platform/proactive-suggestions.md) 。

<a name="Security-and-Privacy-Enhancements"></a>

## <a name="security-and-privacy-enhancements"></a>安全性與隱私權的增強功能

Apple 對 watchOS 3 中的安全性和隱私權進行了許多增強，可協助開發人員改善其應用程式的安全性，並確保使用者的隱私權。

因此，在 watchOS 3 (或更新) 版本上執行的應用程式，必須以靜態方式宣告其意圖，方法是在其檔案中輸入一或多個隱私權特定金鑰 `Info.plist` ，向使用者說明應用程式希望取得存取權的原因。

由於 watchOS 3 與 iOS 10 共用這些變更，因此如需詳細資訊，請參閱我們的 iOS 10 [安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md) 指南。

<a name="Snapshots-and-Dock"></a>

## <a name="snapshots-and-dock"></a>快照集和停駐

在 watchOS 3 中，Apple 已新增 Dock，讓使用者可以釘選其最愛的應用程式並快速加以存取。 當使用者按下 Apple Watch 的側邊按鈕時，將會顯示已釘選的應用程式快照集的資源庫。 使用者可以向左或向右滑動以尋找所需的應用程式，然後按一下應用程式以啟動它，並將快照集取代為執行中的應用程式介面。

系統會定期取得應用程式 UI 的快照，並使用這些快照集來填入檔。watchOS 讓應用程式有機會在建立此快照集之前更新其內容和 UI。

如需詳細資訊，請參閱我們的 [背景](~/ios/watchos/platform/background-tasks.md) 工作指南和 Apple 的 [WKSnapshotRefreshBackgroundTask 參考](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) 。

<a name="User-Notifications"></a>

## <a name="user-notifications"></a>使用者通知

WatchOS 3 中引進的使用者通知架構支援將本機和遠端通知傳遞至 Apple Watch。 您可以使用此架構，根據特定條件（例如，一天的時間或位置，以及接收和處理通知）來排定通知。

若要深入瞭解，請參閱我們 [適用于 watchOS 3 的快速互動技術](~/ios/watchos/platform/quick-interaction-techniques.md) 指南。

<a name="Watch-Connectivity-Framework-Enhancements"></a>

## <a name="watch-connectivity-framework-enhancements"></a>觀賞連接架構的增強功能

`HasContentPending` [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession)類別的新屬性指出會話已在背景中收到需要處理的資料。 和 `RemainingComplicationUserInfoTransfers` 屬性會傳回 iOS 應用程式可更新其 WatchOS 複雜的剩餘時間。

若要深入瞭解，請參閱我們的 [背景](~/ios/watchos/platform/background-tasks.md) 工作指南。

<a name="WatchKit-Framework-Enhancements"></a>

## <a name="watchkit-framework-enhancements"></a>WatchKit 架構的增強功能

watchOS 3 包含 WatchKit 架構的數個增強功能，包括下列各項：

- 應用程式可使用新的 [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) 類別取得 Digital Crown 的狀態，並在使用者使用 [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) 類別旋轉 Crown 時接收更新。
- [WKExtension](https://developer.apple.com/reference/watchkit/wkextension)類別現在包含 `ApplicationState` 方法和[WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate)常數，可讓應用程式用來追蹤應用程式的執行時間狀態。 `WKExtension` 也提供兩種可以用來排程背景工作的新方法。
- [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)現在包含新的 `ApplicationWillEnterForeground` `ApplicationDidEnterBackground` 和方法， `HandleBackgroundTasks` 可監視應用程式狀態中的變更，並處理背景工作更新。
- 已加入新的 [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) 類別，可對 watch 應用程式提供下列類型的手勢辨識： [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer)、 [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer)、 [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) 和 [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer)。
- 新的 [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) 類別提供任何 HOMEKIT 附加 IP 攝影機的介面。
- 新的 [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) 類別可讓應用程式顯示電影「海報」，當使用者按它時，會由執行中的電影取代。
- 新的 [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) 類別可讓應用程式在其 UI 中顯示 Apple Pay 按鈕，以在點擊時起始付款要求。
- 新的 [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) 類別會顯示介面，以顯示 Apple Watch 上的 SceneKit 場景。
- 新的 [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) 類別會顯示介面，以顯示 Apple Watch 上的 SpriteKit 場景。

若要深入瞭解，請參閱我們 [適用于 watchOS 3 的快速互動技術](~/ios/watchos/platform/quick-interaction-techniques.md) 指南。

<a name="Workout-App-Enhancements"></a>

## <a name="workout-app-enhancements"></a>體能訓練應用程式增強功能

WatchOS 3 的新功能，測驗相關的應用程式能夠在 Apple Watch 的背景中執行。 若要啟用這項功能 (並取得 HealthKit 資料) 的存取權，應用程式必須 `WKBackgroundModes` 在檔案中包含 `Info.plist` 具有值的金鑰 `workout-processing` 。

此外，開發人員現在能夠從配對的 iPhone 上的 iOS 應用程式版本啟動 watchOS 測驗應用程式。

若要深入瞭解，請參閱我們的 [測驗應用程式增強功能](~/ios/watchos/platform/workout-apps.md) 指南。

<a name="Additional-Framework-Changes"></a>

## <a name="additional-framework-changes"></a>其他架構變更

除了上面所列的主要架構變更和新增專案，Apple 在 watchOS 3 中進行了許多其他的次要架構變更。

若要深入瞭解，請參閱我們的 [其他架構變更](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) 指南。

<a name="Deprecated-APIs"></a>

## <a name="deprecated-apis"></a>已被取代的 API

下列 Api 已在 watchOS 3 中淘汰：

- `UILocalNotification`UIKit 的類別已被取代，應取代為使用者通知架構。

如需棄用功能和變更的完整清單，請參閱 Apple 的 [watchOS 2.2 To watchOS 3.0 API 差異](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) 檔。

## <a name="related-links"></a>相關連結

- [watchOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2bwatchOS)
- [WatchOS 3 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)