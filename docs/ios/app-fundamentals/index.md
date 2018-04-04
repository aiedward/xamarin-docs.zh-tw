---
title: 應用程式基本概念
description: 應用程式的核心概念
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/21/2017
ms.openlocfilehash: d8dc1e25de527357fe6ad3ad1328a930e0e4dc70
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="application-fundamentals"></a>應用程式基本概念

此章節提供一些較常見的工作項目或概念，開發人員需要開發 Xamarin.iOS (先前稱為 MonoTouch) 應用程式時應該注意的指南。

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)

本文將介紹應用程式傳輸安全性會強制執行 iOS 9 應用程式，這表示 Xamarin.iOS 專案的安全性變更會涵蓋 AT 組態選項，它將討論如何 AT，退出，視需要。 因為預設會啟用 AT，任何不安全的網際網路連線將會引發 9 的 iOS 應用程式中的例外狀況 （除非您已明確地允許它）。


## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[背景](~/ios/app-fundamentals/backgrounding/index.md)

背景處理或 backgrounding 是讓應用程式在前景執行另一個應用程式時，在背景中執行工作的程序。 本指南可做為背景處理在 iOS 中的簡介。


## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[事件、 通訊協定與委派](~/ios/app-fundamentals/delegates-protocols-and-events.md)

這篇文章會提供用來接收回撥，並填入資料的使用者介面控制項的索引鍵的 iOS 技術。 這些技術是事件、 通訊協定，以及委派。這篇文章解釋每一種是以及每個從 C# 的使用方式。 它會示範如何 Xamarin.iOS 使用 iOS 控制項來公開熟悉.NET 事件，以及如何 Xamarin.iOS Objective C 的概念，例如通訊協定與委派提供支援 （OBJECTIVE-C 委派不應該與 C# 委派混淆）。 本文也會提供顯示通訊協定的使用方式同時做為基礎的 OBJECTIVE-C 委派和非委派案例中的範例。

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[執行緒處理](~/ios/app-fundamentals/threading.md)

這篇文章討論執行緒 Xamarin.iOS 應用程式中，並位元參.NET 執行緒集區、 應用程式的回應和記憶體回收。&nbsp;

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[使用影像](~/ios/app-fundamentals/images-icons/index.md)

這篇文章探討如何 Xamarin.iOS 應用程式 （例如圖示載入影像等） 的支援映像和映像內應用程式 （例如映像套用到控制項） 中使用影像。 其中也涵蓋如何使用 Visual Studio for Mac 併入映像，以及如何從程式碼的映像與互動。

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[使用屬性清單](~/ios/app-fundamentals/index.md)

本文件介紹使用 Info.plist 與 Entitlements.plist Mac 的圖形化和進階屬性清單 (.plist) 編輯器的 Visual Studio。 它說明如何設定圖示和啟動映像以便 iOS 應用程式，並示範從指定的應用程式功能 （權利） 在 Visual Studio for mac。

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[使用檔案系統](~/ios/app-fundamentals/file-system.md)

Xamarin.iOS 可以使用相同的 System.IO 類別，才能使用，您會使用任何.NET 應用程式在 iOS 中檔案和目錄。 不過，儘管熟悉類別和方法，iOS 可以建立或存取的檔案會實作一些限制，而且也會提供特殊功能為特定目錄。 本文概述這些限制與功能，並示範檔案存取 Xamarin.iOS 應用程式中的運作方式。

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[在程式碼中建立 iOS 應用程式](~/ios/app-fundamentals/ios-code-only.md)

這篇文章探討如何建立 iOS 應用程式完全以程式碼使用 Visual Studio 和 Visual Studio for mac。 它會顯示如何從空白專案範本從 UIKit 建立檢視表的階層會建立在控制器中的應用程式螢幕啟動。 然後，它討論如何在控制器中建立可載入的自訂檢視。

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[使用使用者預設值](~/ios/app-fundamentals/user-defaults.md)

`NSUserDefaults`類別會提供適用於 iOS 的應用程式和擴充功能，以程式設計的方式與全系統預設系統互動的方式。 使用預設的系統，使用者可以設定應用程式的行為或樣式以符合他們的喜好設定 （根據應用程式的設計）。 例如，如果帝國度量呈現度量 vs 中的資料，或選取指定的 UI 佈景主題。

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[觸控](~/ios/app-fundamentals/touch/index.md)

在許多現今的裝置上的觸控式螢幕讓使用者快速且有效地與裝置互動以自然且直覺式的方式。 不只限於簡單觸控偵測這種互動 – 也可使用筆勢以及。 例如，縮小-放大手勢是螢幕的一個非常常見的範例 – 就與使用者都可以放大或縮小的兩指部分。本指南會檢查觸控與手勢在 iOS 中。

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[使用安全性和隱私權](~/ios/app-fundamentals/security-privacy.md)

Apple 已進行數個增強功能的安全性和隱私權中 iOS 10 （和大於） 可協助開發人員改善其應用程式的安全性，並確保使用者的隱私權。 本文將討論 Xamarin.iOS 應用程式中實作這些功能。

##  <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[當地語系化](~/ios/app-fundamentals/localization/index.md)

本指南涵蓋 Xamarin.iOS 應用程式，以支援國際化的編碼方式加入。