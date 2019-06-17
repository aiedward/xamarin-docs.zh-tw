---
title: Xamarin.Mac 應用程式基本概念
description: 本文件所連結描述各種概念需要了解開發 Xamarin.Mac 應用程式時的指南。
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 12/17/2015
ms.openlocfilehash: 376286b73c92cba40de183043b86cb4ffb5e699d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085301"
---
# <a name="xamarinmac-application-fundamentals"></a>Xamarin.Mac 應用程式基本概念

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[常見的模式和慣用語](~/mac/app-fundamentals/patterns.md)

透過 C# 公開的 Apple Api，在某些慣用語和模式出現一再重複。 如果您已利用 Xamarin.iOS 的程式設計體驗，這些可能看起來很熟悉。 文件會經常參考這些模式和慣用語重複，因此需要充分了解它們，將可協助您了解您所尋找的文件。

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[了解 Mac Api](~/mac/app-fundamentals/mac-apis.md)

針對大部分的時間使用 Xamarin.Mac 開發，您可以考慮、 讀取及撰寫 C# 中，而不需要太擔心與基礎的 Objective C Api。 但是有時候會需要讀取的 API 文件，向 Apple 將方案從堆疊溢位的答案轉譯為您的問題，或比較現有的範例。

## <a name="console-appsmacapp-fundamentalsconsolemd"></a>[主控台應用程式](~/mac/app-fundamentals/console.md)

您也可以建置 「 無周邊 」 的主控台應用程式存取原生 macOS Api 使用 Xamarin.Mac。

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[使用.xib 檔案](~/mac/app-fundamentals/xib.md)

本文涵蓋在 Xcode 的 Interface Builder 來建立和維護的 Xamarin.Mac 應用程式的使用者介面中建立的.xib 檔案。

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[.storyboard/.xib 以下使用者介面設計](~/mac/app-fundamentals/xibless-ui.md)

本文涵蓋建立 Xamarin.Mac 應用程式的使用者介面直接從 C# 程式碼，而不使用.storyboard 或.xib 檔案使用 Xcode 的 Interface Builder。

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[使用映像](~/mac/app-fundamentals/image.md)

這篇文章涵蓋使用 「 映像和在 Xamarin.Mac 應用程式中的圖示。 說明如何建立和維護的映像建立您的應用程式圖示和使用 C# 程式碼和 Xcode 的 Interface Builder 中的映像所需。

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)

這篇文章說明如何使用索引鍵-值撰寫程式碼，以便進行資料繫結至 UI 項目在 Xcode 的 Interface Builder 中所觀察的索引鍵 / 值。 您使用這項技術，大幅減少 C# 程式碼寫入 Xamarin.Mac 應用程式所需的工作量。 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[使用資料庫](~/mac/app-fundamentals/databases.md)

這篇文章說明如何使用索引鍵-值程式碼撰寫和觀察以便進行資料繫結至 UI 項目在 Xcode 的 Interface Builder 的 SQLite 資料庫的直接存取的索引鍵 / 值。 它也涵蓋了使用 SQLite.NET ORM 提供 SQLite 資料的存取權。

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[使用複製和貼上](~/mac/app-fundamentals/copy-paste.md)

這篇文章涵蓋剪貼板提供複製並貼上在 Xamarin.Mac 應用程式中使用。 它示範如何使用標準的資料類型，可以在多個應用程式，以及如何支援讓應用程式內的自訂資料之間共用。

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[沙箱 Xamarin.Mac 應用程式](~/mac/app-fundamentals/sandboxing.md)

本文章涵蓋沙箱 Xamarin.Mac 應用程式在 App Store 上發行。 它涵蓋了所有項目移入沙箱： 容器目錄、 權利、 使用者決定的權限、 權限的分隔和核心強制執行。

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[使用 avaudioplayer 播放音效的播放](~/mac/app-fundamentals/sounds.md)

這篇文章會示範如何使用協助程式類別，以控制播放音效使用 avaudioplayer 播放。

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[回報 bug](~/mac/app-fundamentals/troubleshooting.md)

有時，我們都停滯時從事的專案，無法取得 API，以便使用我們想要的方式或在嘗試解決的 bug。 我們在 Xamarin 的目標是讓您在撰寫您的行動和桌面應用程式，成功，我們提供了一些資源，幫助。
