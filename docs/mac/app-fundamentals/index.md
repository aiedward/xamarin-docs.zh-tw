---
title: "應用程式基本概念"
description: "描述各種概念需要了解開發 Xamarin.Mac 應用程式時的指南的這個文件連結。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/17/2015
ms.openlocfilehash: 9b64647fdb455978c3833ce1bc37f5e8784b9a78
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="application-fundamentals"></a>應用程式基本概念

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[常見的模式和慣用語](~/mac/app-fundamentals/patterns.md)

Apple 公開的 Api 透過 C#，整個特定語言和模式會一再重複。 如果您有使用 Xamarin.iOS 程式設計經驗，這些可能看起來很熟悉。 文件將通常參考這些模式和慣用語重複，因此需要充分了解它們可協助您充分您找到的文件的意義。

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[了解 Mac 應用程式開發介面](~/mac/app-fundamentals/mac-apis.md)

針對大部分的時間使用 Xamarin.Mac 進行開發，您可以考慮、 讀取及 C# 撰寫不太擔心基礎 OBJECTIVE-C api。 不過，有時候您會需要讀取 API 文件，向 Apple 針對您的問題，轉譯的回應堆疊溢位的方案，或比較現有的範例。

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[使用.xib 檔案](~/mac/app-fundamentals/xib.md)

本文涵蓋.xib 檔案 Xcode 的建立和維護 Xamarin.Mac 應用程式的使用者介面的介面產生器中建立的工作。

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[使用者介面設計小於.storyboard/.xib](~/mac/app-fundamentals/xibless-ui.md)

本文涵蓋直接從 C# 程式碼建立 Xamarin.Mac 應用程式的使用者介面，而不使用.storyboard 或.xib 檔使用 Xcode 的介面產生器。

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[使用映像](~/mac/app-fundamentals/image.md)

本文涵蓋了使用映像和 Xamarin.Mac 應用程式中的圖示。 其中涵蓋建立和維護映像建立您的應用程式圖示，然後使用 C# 程式碼和 Xcode 的介面產生器中的映像所需。

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[資料繫結和索引鍵-值撰寫程式碼](~/mac/app-fundamentals/databinding.md)

本文說明如何使用索引鍵-值編碼並觀察以便進行資料繫結至 UI 項目在 Xcode 的介面產生器中的索引鍵-值。 您使用這項技術，可大幅減少 C# 程式碼數量，以供需要寫 Xamarin.Mac 應用程式。 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[使用資料庫](~/mac/app-fundamentals/databases.md)

本文說明如何使用索引鍵-值編碼並觀察以便進行資料繫結至在 Xcode 的介面產生器中的 UI 項目 SQLite 資料庫直接存取的索引鍵-值。 其中也涵蓋使用 SQLite.NET ORM 提供 SQLite 資料的存取權。

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[使用複製和貼上](~/mac/app-fundamentals/copy-paste.md)

本文涵蓋剪貼版提供複製並貼上 Xamarin.Mac 應用程式中使用。 它會顯示如何處理與標準資料型別，可以在多個應用程式，以及如何支援自訂授與應用程式中的資料之間共用。

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[沙箱 Xamarin.Mac 應用程式](~/mac/app-fundamentals/sandboxing.md)

本文涵蓋沙箱 Xamarin.Mac 應用程式，App Store 上的版本。 它涵蓋的所有項目移入沙箱： 容器目錄、 權利，決定使用者的權限、 權限分隔和核心強制執行。

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[播放音效與 AVAudioPlayer](~/mac/app-fundamentals/sounds.md)

本文示範如何控制播放的聲音使用 AVAudioPlayer 使用協助程式類別。

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[回報 bug](~/mac/app-fundamentals/troubleshooting.md)

有時候，我們都堵塞無法取得 API，以依照我們想要的方式運作，或嘗試解決 bug 的專案上工作時。 Xamarin 的目標是要讓您成功撰寫您的行動裝置版和桌面應用程式，以及我們提供了一些資源可幫助。
