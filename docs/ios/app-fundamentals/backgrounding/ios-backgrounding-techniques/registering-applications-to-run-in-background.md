---
title: "註冊在背景中執行的應用程式"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 5fcb41f4f60adc8ca5be761c2b9a7449387a89d0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="registering-applications-to-run-in-the-background"></a>註冊在背景中執行的應用程式

註冊個別工作的背景權限適用於某些應用程式，但如果應用程式經常執行重要、 長時間執行的工作，例如快速入門說明透過 GPS 使用者呼叫時，會發生什麼事？ 此類應用程式應該改為註冊為已知的背景需要應用程式。

註冊應用程式會通知 iOS 應用程式應該指定在背景中執行工作所需的特殊權限。

## <a name="application-registration-categories"></a>應用程式註冊類別

已註冊的應用程式可以分成數種類別：

-  **音訊**-音樂播放機和處理音訊內容的其他應用程式可能會註冊若要繼續播放音訊即使當應用程式已不在前景。 如果此類別中的應用程式嘗試執行任何動作播放音訊或在背景中的下載以外，iOS 會終止它。
-  **VoIP** -語音透過網際網路通訊協定 (VoIP) 應用程式取得的音訊的應用程式，才能讓處理音訊在背景中的授與的權限相同。 它們也可回應視電源它們，才能保持連線運作的 VoIP 服務。
-  **外部附屬應用程式和藍芽**-保留給需要與藍芽裝置和其他外部硬體附屬應用程式通訊的應用程式，註冊這些類別可讓應用程式能夠保持連線到的硬體。
-  **Newsstand** -A Newsstand 應用程式才能繼續同步處理在背景中的內容。
-  **位置**-應用程式使用的 GPS 或網路位置的資料可以傳送和接收位置更新，在背景中。
-  **擷取 (iOS 7 +)** -背景 fetch 權限可以定期呈現使用者以更新的內容，在傳回至應用程式時檢查新的內容提供者註冊的應用程式。
-  **遠端通知 (iOS 7 +)** -應用程式能夠向提供者，從接收通知，並開始進行更新之前在使用者開啟應用程式使用通知。 通知可以推播通知的形式，或選擇要以無訊息模式喚醒應用程式。


應用程式可以藉由設定註冊**需要背景模式**屬性在應用程式的*Info.plist*。 應用程式可以在許多類別，因為它需要註冊：

 [![](registering-applications-to-run-in-background-images/bgmodes.png "設定背景模式")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

登錄背景位置更新應用程式的逐步指引，請參閱[背景位置逐步解說](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md)。

## <a name="application-does-not-run-in-background-property"></a>應用程式不會執行在背景屬性

另一個屬性可在設定*Info.plist*是*應用程式不會在背景中執行*，或`UIApplicationExitsOnSuspend`屬性：

 [![](registering-applications-to-run-in-background-images/plist.png "停用執行的背景")](registering-applications-to-run-in-background-images/plist.png#lightbox)

這有完全相同的效果設定為在 iOS 7 +、 關閉，只不過它只在開發人員端，變更的背景重新整理應用程式設定，可適用於 iOS 4 和更新版本。 應用程式會將輸入背景之後，立即擱置，並不能進行任何處理。

如果您的應用程式不是設計用來處理背景處理，因為這有助於避免非預期的行為，請使用這個屬性。

## <a name="background-fetch-and-remote-notifications"></a>背景擷取和遠端的通知

背景擷取和遠端通知是特殊的註冊類別 iOS 7 中引進。 這些類別可讓應用程式接收來自提供者，新的內容，並在背景中更新。 下一節探討 fetch 和更詳細地，遠端通知和也導入定位知悉如下的方法，用於將應用程式在 iOS 6 上，在背景中的更新。
