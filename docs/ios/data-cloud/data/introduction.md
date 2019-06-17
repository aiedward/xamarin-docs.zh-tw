---
title: 在 Xamarin.iOS 應用程式中的資料儲存體簡介
description: 本文件描述的資料儲存在 Xamarin.iOS 應用程式的各種方法，並提供 SQLite 的優點有關的特定資訊。
ms.prod: xamarin
ms.assetid: B1994468-FD06-4FD9-96B3-FCEBB13A972A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: 06fd77293302a3d6d5d44def9d0a83e3f1a57ede
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217733"
---
# <a name="introduction-to-data-storage-in-xamarinios-apps"></a>在 Xamarin.iOS 應用程式中的資料儲存體簡介

## <a name="when-to-use-a-database"></a>使用資料庫的時機

雖然行動裝置的儲存體和處理功能會增加，手機和平板電腦仍會落後他們的桌面&amp;膝上型電腦的對應項目。 基於這個理由值得花一些時間來規劃您的應用程式的資料儲存體架構，而非只假設資料庫正確的答案所有時間。 有許多不同的選項符合不同的需求，例如：

-  **喜好設定**– iOS 提供內建的機制，用來儲存資料的簡單索引鍵 / 值組。 如果您要儲存簡單的使用者設定或小型資料 （例如個人化資訊） 則使用平台的原生功能，來儲存這種類型的資訊。 適用於 iOS 您也可以利用這項資料，同時用於備份和同步處理具有多個裝置的使用者的 iCloud 同步處理。
-  **文字檔**– 使用者輸入或快取的下載內容 （例如。 可以直接在檔案系統上儲存 HTML)。 您可以使用適當的檔案命名慣例來協助您組織的檔案，並尋找資料。
-  **序列化資料檔案**– 物件可以保存為 XML 或 JSON 檔案系統上。 .NET framework 包含程式庫，讓序列化和還原序列化物件的簡單。 您可以使用適當的名稱來組織資料檔案。
-  **資料庫**– SQLite 資料庫引擎是可用的 iOS、 以及適用於儲存結構化的資料，您需要查詢、 排序或進行操作。 資料庫儲存體被適合具有許多屬性的資料清單。
-  **影像檔**– 雖然可以在行動裝置上的資料庫中儲存二進位資料，但建議您將它們儲存在檔案系統直接。 必要時您可以在映像相關聯的其他資料的資料庫中儲存檔案名稱。 在處理大型映像或大量的影像，最好規劃會刪除您不再需要以避免消耗所有使用者的儲存空間的檔案快取策略。


如果資料庫是您的應用程式的正確的儲存機制，此文件的其餘部分將討論如何在 Xamarin 平台上使用 SQLite。

## <a name="advantages-of-using-a-database"></a>使用資料庫的優點

有許多行動應用程式中使用 SQL database 的優點：

-  SQL database 可讓您有效率地儲存結構化資料。
-  使用複雜的查詢可以擷取特定資料。
-  可以排序查詢結果。
-  可彙總查詢結果。
-  使用現有的資料庫技術的開發人員可以利用他們的知識來設計資料庫和資料存取程式碼。
-  從連接的應用程式的伺服器元件的資料模型可能會重複使用 （全部或部分） 的行動應用程式中。


## <a name="sqlite-database-engine"></a>SQLite 資料庫引擎

SQLite 是已被 Apple 採用其行動裝置平台的開放原始碼資料庫引擎。 SQLite 資料庫引擎是內建於 iOS，因此開發人員充分發揮它的任何其他工作。 SQLite 是適合用來跨平台行動開發的因為：

-  Database engine 是小型、 快速且容易移植。
-  資料庫會儲存在單一檔案，可以輕鬆地管理行動裝置上。
-  跨平台很容易使用的檔案格式： 是否 32 或 64 位元，與巨量-或由小到大系統。
-  它會實作 SQL92 標準的大部分。


SQLite 設計為小型且快速，因為有一些注意事項用法：

-  不支援某些外部聯結語法。
-  只有資料表重新命名，並且支援 ADDCOLUMN。 您無法執行您的結構描述的其他修改。
-  檢視是唯讀的。


您可以深入了解 SQLite 網站- [SQLite.org](http://SQLite.org) -但是您必須搭配 Xamarin 使用 SQLite 的所有資訊包含在這份文件和相關聯的範例。 SQLite 資料庫引擎是內建所有 iOS 版本。
雖然未涵蓋在本章中，SQLite 是也可用於 Windows Phone 和 Windows 應用程式。

## <a name="windows-and-windows-phone"></a>Windows 及 Windows Phone

SQLite 也可用在 Windows 平台，雖然這份文件未涵蓋這些平台。
深入了解[Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)並[Tasky Pro](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/case_study%3A_tasky)案例研究，並檢閱[Tim Heuer 的部落格](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx)。



## <a name="related-links"></a>相關連結

- [DataAccess Basic （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 進階 （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin.Forms 資料存取](~/xamarin-forms/app-fundamentals/databases.md)
