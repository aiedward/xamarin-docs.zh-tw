---
title: 註冊在背景中執行的 Xamarin.iOS 應用程式
description: 本文件說明如何註冊的 Xamarin.iOS 應用程式，以在背景執行。 它討論音訊應用程式、 VoIP 應用程式、 外部的附屬應用程式和藍芽、 等等。
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a0a66571d0249ef6fd65ff382f14c38f48a8af37
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105164"
---
# <a name="registering-xamarinios-apps-to-run-in-the-background"></a>註冊在背景中執行的 Xamarin.iOS 應用程式

註冊個別工作的背景權限適用於某些應用程式，但如果應用程式持續執行重要 」、 「 長時間執行的工作，例如取得透過 GPS 使用者指示，說明如何呼叫時，會發生什麼事？ 這類的應用程式應該改為註冊為已知的背景必要應用程式。

註冊應用程式通知至 iOS 應用程式應該指定在背景中執行工作所需的特殊權限。

## <a name="application-registration-categories"></a>應用程式註冊類別

已註冊的應用程式可以分成數個類別：

-  **音訊**-音樂播放器和其他音訊內容所使用的應用程式可能會註冊以繼續播放音訊甚至當應用程式已不存在於前景。 如果此類別中的應用程式嘗試播放音訊或在背景中的下載以外執行任何動作時，iOS 會終止它。
-  **VoIP** -Voice Over Internet Protocol (VoIP) 應用程式取得的相同的權限授與音訊的應用程式，以保留處理在背景中的音訊。 它們也允許回應，視需要它們，以維持其連線開機的 VoIP 服務。
-  **外部配件和藍芽**-保留給需要與藍芽裝置和其他外部硬體附屬應用程式進行通訊的應用程式，註冊這些類別可讓應用程式，以保持連線到的硬體。
-  **Newsstand** -Newsstand 應用程式可以繼續在背景中的內容同步處理。
-  **位置**-應用程式使用的 GPS 或網路位置的資料可以傳送和接收位置更新在背景中。
-  **擷取 (iOS 7 +)** -的背景擷取權限可以在定期間隔中，向使用者顯示更新的內容傳回給應用程式時檢查新內容的提供者註冊的應用程式。
-  **遠端通知 (iOS 7 +)** -應用程式可以註冊以接收通知，從提供者，並開始更新之前在使用者開啟應用程式中使用的通知。 通知可以傳入 推播通知的形式，或選擇要以無訊息方式喚醒應用程式。


應用程式可以藉由設定登錄**必要的背景模式**應用程式的屬性*Info.plist*。 應用程式可以註冊多個類別，因為它需要︰

 [![](registering-applications-to-run-in-background-images/bgmodes.png "設定 背景模式")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

註冊背景位置更新應用程式的逐步指南，請參閱 <<c0> [ 背景位置逐步解說](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md)。

## <a name="application-does-not-run-in-background-property"></a>應用程式不會執行以背景屬性

中可設定的另一個屬性*Info.plist*是*應用程式不會在背景中執行*，或`UIApplicationExitsOnSuspend`屬性：

 [![](registering-applications-to-run-in-background-images/plist.png "停用背景執行")](registering-applications-to-run-in-background-images/plist.png#lightbox)

這有完全相同的效果設定背景重新整理應用程式設定為 關閉 iOS 7 + 中，但只能在開發人員端，加以變更，而且是適用於 iOS 4 和更新版本。 應用程式之後立即輸入背景，即會暫止，並不能執行任何處理。

如果您的應用程式不是設計用來處理背景處理，因為這有助於避免非預期的行為，請使用這個屬性。

## <a name="background-fetch-and-remote-notifications"></a>背景擷取及遠端通知

背景擷取和遠端通知是特殊的註冊類別在 iOS 7 中引進。 這些類別可讓應用程式，以接收來自提供者，新的內容，並在背景中更新。 下一節探討 fetch 和更高的詳細資訊，在遠端通知，並也引進定位知悉等功能，表示更新 iOS 6 在背景中的應用程式。
