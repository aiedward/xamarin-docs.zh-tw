---
title: 部署及測試
description: 穩定和部署指南
ms.prod: xamarin
ms.assetid: 2DBF3BF9-79E7-4E24-AF26-E34C972B0169
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 515ea8e63f8309c46a7d802af1daafcb0c483762
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="deployment-and-testing"></a>部署及測試

本節涵蓋測試應用程式和散發方式等主題。 本部分的主題包括偵錯工具、如何部署給測試人員、如何將應用程式發佈至 App Store 等。


##  <a name="app-distributioniosdeploy-testapp-distributionindexmd"></a>[應用程式散發](~/ios/deploy-test/app-distribution/index.md)

本文將介紹如何設定、建置、發佈 Xamarin.iOS 應用程式，用於透過各種方式進行散發，包括：

- [散發](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [內部 (Enterprise) 散發](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [臨機操作散發](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)

##  <a name="ipa-deploymentiosdeploy-testapp-distributionipa-supportmd"></a>[IPA 部署](~/ios/deploy-test/app-distribution/ipa-support.md)

臨機操作部署和企業 (Enterprise) 部署可讓開發人員建立套件，這些套件可針對測試或向公司內部使用者散發。 本文件介紹如何建立可使用 iTunes 同步到 iOS 裝置的 IPA。

## <a name="provisioningprovisioningindexmd"></a>[佈建](provisioning/index.md)

此指南集合涵蓋程式碼簽署及佈建的必要內容 (例如使用屬性清單)，以及如何針對應用程式服務佈建您的應用程式。 

## <a name="wireless-deploymentwireless-deploymentmd"></a>[無線部署](wireless-deployment.md)

 Xcode 9 引進了透過網路部署至 iOS 裝置或 Apple TV 的選項，使您不用在每次想要針對應用程式進行部署及偵錯時，都必須以實體纜線連接您的裝置。 此功能目前處於預覽狀態。

##  <a name="testflightiosdeploy-testtestflightmd"></a>[TestFlight](~/ios/deploy-test/testflight.md)

TestFlight 現在由 Apple 所擁有，是 Xamarin.iOS 應用程式進行 Beta 測試的主要方法。 本文會引導您完成 TestFlight 流程中的所有步驟：從上傳應用程式到使用 iTunes Connect。

##  <a name="debugging-in-xamariniosiosdeploy-testdebugging-in-xamarin-iosmd"></a>[在 Xamarin.iOS 中偵錯](~/ios/deploy-test/debugging-in-xamarin-ios.md)

Visual Studio 和 Visual Studio for Mac IDE 都支援在 iOS 模擬器和 iOS 裝置中為 Xamarin.iOS 進行偵錯。 本文介紹如何使用偵錯工具，以及如何設定其支援的各種選項。


##  <a name="touchunitiosdeploy-testtouchunitmd"></a>[Touch.Unit](~/ios/deploy-test/touch.unit.md)

本文件介紹如何為 Xamarin.iOS 專案建立單元測試。
用 Xamarin.iOS 進行單元測試，是透過使用 Touch.Unit 架構來完成。該架構包含 iOS 測試執行器和 [NUnitLite](http://www.nunitlite.com/) 架構的修改版本；此版本架構可提供一組熟悉的 API，用於編寫單元測試。



##  <a name="using-instruments-to-detect-native-leaks-using-markheapiosdeploy-testusing-instruments-to-detect-native-leaks-using-markheapmd"></a>[使用 Instruments 來偵測利用 MarkHeap 的原生流失](~/ios/deploy-test/using-instruments-to-detect-native-leaks-using-markheap.md)

本文介紹如何在任何 iOS 裝置與任何 Xamarin.iOS 應用程式中使用 Instruments。 也會介紹如何在模擬器中分析應用程式。



##  <a name="walkthrough---using-apples-instrument-tooliosdeploy-testwalkthrough-apples-instrumentmd"></a>[逐步解說 - 使用 Apple 的 Instrument 工具](~/ios/deploy-test/walkthrough-apples-instrument.md)

本文介紹如何使用 Apple 的 Instruments 工具，來診斷透過 Xamarin 建置的 iOS 應用程式記憶體問題。 文中將示範如何啟動 Instruments、拍攝堆積快照、分析記憶體成長。 也會示範如何使用 Instruments 顯示和鎖定造成記憶體問題的確切程式碼行數。

##  <a name="linking-on-ioslinkermd"></a>[在 iOS 上連結](linker.md)

說明連結器如何運作以將應用程式套件的大小降至最低，以及如何修改其設定和使用方式。

##  <a name="xamarinios-performanceperformancemd"></a>[Xamarin.iOS 效能](performance.md)

有許多技巧可增加利用 Xamarin.iOS 建置之應用程式的效能。 這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。

##  <a name="mtouchmtouchmd"></a>[mtouch](mtouch.md)

針對 mtouch.exe 的附註和資訊。mtouch.exe 為能將您的專案建置為可供 iOS 使用之應用程式的命令列工具。

## <a name="ios-build-mechanicsios-build-mechanicsmd"></a>[iOS 組建機制](ios-build-mechanics.md)

本指南探索如何測定應用程式的時間，以及如何使用可用來針對所有組建組態取得更快速組建的方法。