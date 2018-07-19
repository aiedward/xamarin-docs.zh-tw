---
title: Xamarin.iOS 應用程式基本概念
description: 各種指南說明 Xamarin.iOS 開發，例如應用程式的傳輸安全性的基本概念的背景，事件，和執行緒處理此文件連結。
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/21/2017
ms.openlocfilehash: de337291554e81a2434dcc30c163f4789fc832eb
ms.sourcegitcommit: e98a9ce8b716796f15de7cec8c9465c4b6bb2997
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2018
ms.locfileid: "39111208"
---
# <a name="xamarinios-application-fundamentals"></a>Xamarin.iOS 應用程式基本概念

此章節提供一些較常見的工作項目或概念，開發人員需要開發 Xamarin.iOS (先前稱為 MonoTouch) 應用程式時應該注意的指南。

## <a name="accessibilityiosapp-fundamentalsaccessibilitymd"></a>[協助工具選項](~/ios/app-fundamentals/accessibility.md)

本文件說明各種 Api 和工具，可用來協助建置應用程式，使用者都可以存取多個越好。

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)

這篇文章將介紹應用程式的傳輸安全性會強制執行 iOS 9 應用程式時，這表示您的 Xamarin.iOS 專案的安全性變更，會涵蓋 ATS 組態選項，如有必要，它將會涵蓋了如何 ATS，退出。 因為預設會啟用 ATS，任何不安全的網際網路連線將會引發 iOS 9 應用程式中的例外狀況 （除非您已明確允許它）。

## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[背景](~/ios/app-fundamentals/backgrounding/index.md)

背景處理，或背景就是讓應用程式在背景中執行工作，而另一個應用程式會在前景執行的處理程序。 本指南可在 iOS 中處理的背景介紹。

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[在程式碼中建立 iOS 應用程式](~/ios/app-fundamentals/ios-code-only.md)

本文將探討如何建立 iOS 應用程式，完全以 Visual Studio 和 Visual Studio for mac 中使用的程式碼 它示範如何從 UIKit 中建立的檢視階層架構建置應用程式畫面在控制器中的空白專案範本開始。 然後，它還會討論如何建立可載入的自訂檢視控制器中。

## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[事件、 通訊協定與委派](~/ios/app-fundamentals/delegates-protocols-and-events.md)

本文介紹用來接收回撥，並填入資料的使用者介面控制項的索引鍵的 iOS 技術。 這些技術是事件、 通訊協定與委派;這篇文章說明何謂每一種，而且每個從 C# 的使用方式。 它會示範如何 Xamarin.iOS 使用 iOS 控制項來公開 （expose） 熟悉.NET 事件，以及如何 Xamarin.iOS Objective C 的概念，例如通訊協定與委派提供支援 （Objective C 委派不應該與 C# 委派混淆）。 這篇文章也提供顯示通訊協定使用的方式同時做為基礎的 Objective C 委派和非委派案例中的範例。

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[使用檔案系統](~/ios/app-fundamentals/file-system.md)

Xamarin.iOS 可以使用相同的 System.IO 類別來處理檔案和目錄，在 iOS 中，您可以使用任何.NET 應用程式。 不過，儘管熟悉的類別和方法，iOS 會實作一些限制，可以建立或存取的檔案，而且也提供特殊功能特定的目錄。 本文概述這些限制和功能，並示範如何在 Xamarin.iOS 應用程式中的檔案存取如何運作。

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[使用映像](~/ios/app-fundamentals/images-icons/index.md)

本文將探討如何在 Xamarin.iOS 應用程式支援的映像 （例如圖示，載入映像等） 和應用程式 （例如映像套用到控制項） 中的映像中使用映像。 其中也涵蓋如何使用 Visual Studio for Mac 將映像，以及如何互動的程式碼中的映像。

## <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[當地語系化](~/ios/app-fundamentals/localization/index.md)

本指南涵蓋新增至 Xamarin.iOS 應用程式以支援國際化的編碼方式。

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[使用屬性清單](~/ios/app-fundamentals/index.md)

本文件會介紹 Visual Studio for Mac 的圖形化和進階屬性清單 (.plist) 編輯器來使用 Info.plist 和 Entitlements.plist。 它也說明了設定圖示和啟動映像的 iOS 應用程式，並示範從指定的應用程式功能 （權利），在 Visual Studio for mac。

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[使用安全性和隱私權](~/ios/app-fundamentals/security-privacy.md)

Apple 進行了數個增強功能的安全性和隱私權，在 iOS 10 （和更新版本） 可協助開發人員改進其應用程式的安全性，並確保使用者的隱私權。 這篇文章將涵蓋在 Xamarin.iOS 應用程式中實作這些功能。

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[執行緒處理](~/ios/app-fundamentals/threading.md)

這篇文章討論執行緒在 Xamarin.iOS 應用程式，並稍微討論.NET 執行緒集區、 應用程式的回應，並回收。

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[觸控](~/ios/app-fundamentals/touch/index.md)

在許多現今的裝置上的觸控式螢幕可讓使用者快速且有效率地與裝置互動自然且直覺的方式。 此互動不是只限於簡單的觸控式偵測，就可以使用筆勢也。 比方說，捏合以縮放筆勢是一個非常常見的範例-捏合以使用者可以放大或縮小的兩個手指在螢幕的一部分。觸控和筆勢在 iOS 中的，會檢查本指南。

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[使用 使用者預設值](~/ios/app-fundamentals/user-defaults.md)

`NSUserDefaults`類別會提供適用於 iOS 的應用程式和延伸模組，以程式設計方式與全系統的預設系統互動的方式。 使用預設的系統，使用者可以設定應用程式的行為或設定樣式來符合他們的喜好設定 （根據應用程式的設計而定）。 例如，如果 Imperial 度量呈現計量 vs 中的資料，或選取特定的 UI 佈景主題。
