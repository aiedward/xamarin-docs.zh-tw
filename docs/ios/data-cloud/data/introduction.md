---
title: "簡介"
ms.topic: article
ms.prod: xamarin
ms.assetid: FDAC0771-4749-4758-865A-F1BD190CA54B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: f270248ad3b43d7a4c818b29390b3391d89ddd14
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="introduction"></a>簡介

## <a name="when-to-use-a-database"></a>使用資料庫的時機

手機和平板電腦還是行動裝置的儲存和處理能力會增加，而落後其桌面&amp;膝上型電腦的對應。 基於這個理由值得花一些時間來規劃您的應用程式的資料儲存體架構，而不是只假設資料庫正確的答案所有時間。 有數個不同的選項，使其符合不同的需求，例如：

-  **喜好設定**– iOS 提供內建的機制來儲存資料的簡單索引鍵-值配對。 如果您儲存簡單的使用者設定或小型資料 （例如個人化資訊） 然後使用平台的原生功能來儲存這種類型的資訊。 適用於 iOS 您也可以利用這項資料，同時用於備份和同步處理具有多個裝置之使用者的 iCloud 同步處理。
-  **文字檔**– 使用者輸入或快取的下載內容 （例如。 HTML) 可以直接在檔案系統上儲存。 使用適當的檔案命名慣例可協助您組織的檔案，以及尋找資料。
-  **序列化資料檔案**– 物件可以保存為 XML 或 JSON，在檔案系統上。 .NET framework 包含進行序列化和還原序列化物件的簡單的程式庫。 您可以使用適當的名稱來組織資料檔案。
-  **資料庫**– SQLite database engine 是可用的 iOS 和適用於儲存結構化的資料，您需要查詢、 排序或進行操作。 資料庫儲存體適合用於許多內容與資料的清單。
-  **影像檔**– 雖然很可能在行動裝置上的資料庫中儲存二進位資料，建議您將其儲存直接在檔案系統中。 必要時您可以關聯之映像與其他資料的資料庫中儲存的檔名。 當處理大型影像或大量的影像，它是計劃中刪除您不再需要避免耗用所有使用者的存放裝置空間檔案快取策略很好的作法。


如果資料庫是您的應用程式的右邊的儲存體機制，此文件的其餘部分將討論如何使用 SQLite Xamarin 平台上。

## <a name="advantages-of-using-a-database"></a>使用資料庫的優點

有許多行動應用程式中使用 SQL database 優勢：

-  SQL 資料庫允許有效率的儲存結構化的資料。
-  複雜的查詢，可以擷取特定的資料。
-  可以排序查詢結果。
-  查詢結果可以彙總資料。
-  使用現有的資料庫技術的開發人員可以利用其專業知識，來設計資料庫和資料存取程式碼。
-  連接的應用程式的伺服器元件中的資料模型可能會重複使用 （整個或部分） 的行動應用程式中。


## <a name="sqlite-database-engine"></a>SQLite Database Engine

SQLite 是一種開放原始碼資料庫引擎，已由 Apple 採用其行動裝置平台。 SQLite database engine 是內建 ios，所以開發人員利用它的任何額外的工作。 SQLite 是適用於跨平台行動開發的因為：

-  Database engine 是小型、 快速和輕鬆移植。
-  資料庫會儲存在單一檔案，可以輕鬆地管理行動裝置上。
-  跨平台很容易使用的檔案格式： 是否 32 或 64 位元和大-或由小到大系統。
-  它會實作大部分的 SQL92 標準。


SQLite 設計為小型且快速，因為某些注意事項上有其用途：

-  不支援某些 OUTER join 語法。
-  只有資料表重新命名，並支援 ADDCOLUMN。 您無法執行您的結構描述的其他修改。
-  檢視處於唯讀狀態。


您可以進一步了解 SQLite 網站- [SQLite.org](http://SQLite.org) -但是您需要使用 SQLite 來搭配 Xamarin 的所有資訊包含在這份文件，而且相關聯的範例。 SQLite database engine 是內建所有 iOS 版本。
未涵蓋在本章中，雖然 SQLite 是也可用於 Windows Phone 和 Windows 應用程式。

## <a name="windows-and-windows-phone"></a>Windows 及 Windows Phone

SQLite 也可用在 Windows 平台，雖然這份文件未涵蓋這些平台。
深入了解[Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)和[Tasky Pro](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/case_study%3A_tasky)案例研究，並檢閱[Tim Heuer 部落格](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx)。



## <a name="related-links"></a>相關連結

- [DataAccess Basic （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [資料存取進階 （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 資料配方](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Xamarin.Forms 資料存取](~/xamarin-forms/app-fundamentals/databases.md)
