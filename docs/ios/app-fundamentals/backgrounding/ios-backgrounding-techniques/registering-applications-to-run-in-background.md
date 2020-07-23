---
title: 註冊要在背景中執行的 Xamarin iOS 應用程式
description: 本檔說明如何註冊要在背景中執行的 Xamarin iOS 應用程式。 它討論音訊應用程式、VoIP 應用程式、外部配件和藍牙等等。
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: bef29bfc526a5f378368390c1ec25b1bbf1d8a5a
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932960"
---
# <a name="registering-xamarinios-apps-to-run-in-the-background"></a>註冊要在背景中執行的 Xamarin iOS 應用程式

註冊背景許可權的個別工作適用于某些應用程式，但如果不斷呼叫應用程式來執行重要、長時間執行的工作（例如透過 GPS 取得使用者的指示），會發生什麼事？ 這些應用程式應該改為註冊為已知的背景必要應用程式。

向 iOS 註冊代理程式更新，應用程式應獲得在背景執行工作所需的特殊許可權。

## <a name="application-registration-categories"></a>應用程式註冊類別

已註冊的應用程式可分為數個類別：

- **音訊**-可以註冊音樂播放機和其他使用音訊內容的應用程式，即使應用程式已不在前景中也能繼續播放音訊。 如果此類別中的應用程式嘗試在背景中執行「播放音訊」或「下載」以外的任何動作，iOS 會將其終止。
- **Voip** -語音 Over 網際網路通訊協定（voip）應用程式取得與音訊應用程式相同的許可權，以在背景中持續處理音訊。 也可以視需要對其供電的 VoIP 服務做出回應，讓其連線保持運作。
- **外部配件和藍牙**-保留給需要與 Bluetooth 裝置和其他外部硬體配件通訊的應用程式，在這些類別下註冊可讓應用程式保持連接到硬體。
- **Newsstand** -Newsstand 應用程式可以繼續在背景中同步處理內容。
- **位置**-利用 GPS 或網路位置資料的應用程式，可以在背景中傳送和接收位置更新。
- **Fetch （iOS 7 +）** -針對背景提取許可權註冊的應用程式可以定期檢查提供者是否有新的內容，並在使用者返回應用程式時向其呈現更新內容。
- **遠端通知（iOS 7 +）** -應用程式可以註冊以接收來自提供者的通知，並在使用者開啟應用程式之前，使用通知來啟動更新。 通知可以是推播通知的形式，或選擇以無訊息方式喚醒應用程式。

您可以在應用程式的*plist*中設定必要的 [**背景模式**] 屬性來註冊應用程式。 應用程式可以視需要在多個類別中註冊：

 [![設定背景模式](registering-applications-to-run-in-background-images/bgmodes.png)](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

如需註冊應用程式以取得背景位置更新的逐步指南，請參閱[背景位置](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md)逐步解說。

## <a name="application-does-not-run-in-background-property"></a>應用程式不會在 Background 屬性中執行

另一個可在*Info. plist*中設定的屬性是，*應用程式不會在背景*或 `UIApplicationExitsOnSuspend` 屬性中執行：

 [![正在停用背景執行](registering-applications-to-run-in-background-images/plist.png)](registering-applications-to-run-in-background-images/plist.png#lightbox)

這與在 iOS 7 + 中將 [背景應用程式重新整理] 設定設定為 [關閉] 的效果完全相同，不同之處在于它只能在開發人員端變更，並且適用于 iOS 4 和更新版本。 應用程式會在進入背景後立即暫停，而且將無法執行任何處理。

如果您的應用程式不是設計來處理背景處理，請使用這個屬性，因為這有助於避免非預期的行為。

## <a name="background-fetch-and-remote-notifications"></a>背景提取和遠端通知

背景提取和遠端通知是 iOS 7 引進的特殊註冊類別。 這些類別可讓應用程式從提供者接收新的內容，並在背景中更新。 下一節會更詳細地探討提取和遠端通知，同時也引進了位置感知，做為在 iOS 6 背景中更新應用程式的方法。
