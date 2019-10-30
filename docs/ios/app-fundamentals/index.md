---
title: Xamarin iOS 應用程式基本概念
description: 本檔連結的各種指南，描述了 Xamarin iOS 開發的基本概念，例如應用程式傳輸安全性、背景處理、事件和執行緒。
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/21/2017
ms.openlocfilehash: 0ccdde29183645b93831b7261909714f9baf3fa4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010020"
---
# <a name="xamarinios-application-fundamentals"></a>Xamarin iOS 應用程式基本概念

本節提供開發人員在開發 Xamarin iOS （先前稱為 MonoTouch）應用程式時需要注意的一些常見工作或概念的指南。

## <a name="accessibilityiosapp-fundamentalsaccessibilitymd"></a>[協助工具選項](~/ios/app-fundamentals/accessibility.md)

本檔說明各種 Api 和工具，可用來協助建立可供多個使用者使用的應用程式。

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)

本文將介紹應用程式傳輸安全性在 iOS 9 應用程式上強制執行的安全性變更，以及這對您的 Xamarin iOS 專案所代表的意義。它會涵蓋 ATS 設定選項，並將涵蓋如何退出宣告 ATS （如有需要）。 因為預設會啟用 ATS，所以任何不安全的網際網路連線都會在 iOS 9 應用程式中引發例外狀況（除非您已明確允許）。

## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[背景](~/ios/app-fundamentals/backgrounding/index.md)

背景處理或背景處理是在另一個應用程式于前景執行時，讓應用程式在背景中執行工作的進程。 本指南可做為 iOS 中的背景處理簡介。

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[在程式碼中建立 iOS 應用程式](~/ios/app-fundamentals/ios-code-only.md)

本文將探討如何使用 Visual Studio 和 Visual Studio for Mac，完全以程式碼建立 iOS 應用程式。 它會示範如何從空的專案範本開始，藉由從 UIKit 建立 views 的階層，在控制器中建立應用程式畫面。 然後，它會討論如何建立可在控制器中載入的自訂視圖。

## <a name="exception-marshalingiosplatformexception-marshalingmd"></a>[例外狀況封送處理](~/ios/platform/exception-marshaling.md)

描述如何在原生和 managed 框架之間封送處理目標 C 和 managed 例外狀況。

## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[事件、通訊協定和委派](~/ios/app-fundamentals/delegates-protocols-and-events.md)

本文提供用來接收回呼以及使用資料填入使用者介面控制項的主要 iOS 技術。 這些技術包括事件、通訊協定和委派;本文說明其中的每個專案，以及如何使用每個C#。 它會示範 Xamarin 如何使用 iOS 控制項來公開熟悉的 .NET 事件，以及 Xamarin 如何提供對目標-C 概念（例如通訊協定和委派）的支援（如不應該與C#委派混淆的目標-c 的委派）。 本文也會提供範例，說明如何使用通訊協定作為目標-C 委派和非委派案例的基礎。

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[使用檔案系統](~/ios/app-fundamentals/file-system.md)

在 ios 中，您可以使用相同的 System.IO 類別來處理您在任何 .NET 應用程式中使用的檔案和目錄。 不過，儘管常見的類別和方法，iOS 仍會對可建立或存取的檔案進行一些限制，同時也提供特定目錄的特殊功能。 本文概述這些限制和功能，並示範檔案存取在 Xamarin iOS 應用程式中的運作方式。

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[使用影像](~/ios/app-fundamentals/images-icons/index.md)

本文將探討如何在 Xamarin 中使用影像，這兩個應用程式都支援影像（例如圖示、載入影像等）和應用程式內的影像（例如套用至控制項的影像）。 其中也涵蓋如何使用 Visual Studio for Mac 來併入影像，以及如何從程式碼與影像互動。

## <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[當地語系化](~/ios/app-fundamentals/localization/index.md)

本指南涵蓋將編碼新增至支援國際化的 Xamarin iOS 應用程式。

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[使用屬性清單](~/ios/app-fundamentals/index.md)

本檔介紹 Visual Studio for Mac 的圖形化和先進的屬性清單（. plist）編輯器，以使用 plist 和 plist。 其中說明如何設定適用于 iOS 應用程式的圖示和啟動影像，並示範如何在 Visual Studio for Mac 內指定應用程式功能（權利）。

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[使用安全性和隱私權](~/ios/app-fundamentals/security-privacy.md)

Apple 在 iOS 10 （及更新版本）中提供了許多安全性和隱私權方面的增強功能，可協助開發人員改善其應用程式的安全性，並確保使用者的隱私權。 本文將討論如何在 Xamarin iOS 應用程式中執行這些功能。

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[執行緒處理](~/ios/app-fundamentals/threading.md)

本文討論 Xamarin iOS 應用程式中的執行緒，並稍微談一下 .NET 執行緒集區、回應式應用程式和垃圾收集。

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[觸控](~/ios/app-fundamentals/touch/index.md)

現今許多裝置上的觸控式螢幕可讓使用者以自然且直覺的方式，快速且有效率地與裝置互動。 這種互動並不只限于簡單的觸控式偵測，也可以使用手勢。 例如，縮小到縮放手勢是一個很常見的範例，也就是捏合螢幕的一部分，使用者可以放大或縮小兩個手指。本指南會檢查 iOS 中的觸控和手勢。

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[使用使用者預設值](~/ios/app-fundamentals/user-defaults.md)

`NSUserDefaults` 類別可讓 iOS 應用程式和延伸模組以程式設計方式與全系統的預設系統互動。 藉由使用預設系統，使用者可以設定應用程式的行為或樣式，以符合其喜好設定（根據應用程式的設計）。 例如，若要呈現度量與英制測量的資料，或選取特定的 UI 主題。
