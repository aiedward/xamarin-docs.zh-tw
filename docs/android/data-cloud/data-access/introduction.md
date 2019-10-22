---
title: Android 上的資料存放區簡介
ms.prod: xamarin
ms.assetid: FDAC0771-4749-4758-865A-F1BD190CA54B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 69d5222bb6c50870d0c42bea6ff71236e3d1580c
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70754556"
---
# <a name="introduction"></a>簡介

## <a name="when-to-use-a-database"></a>使用資料庫的時機

雖然行動裝置的儲存和處理功能日益增加，但手機和平板電腦仍然落後桌面和筆記本電腦的對應專案。 基於這個理由，建議您花一些時間為您的應用程式規劃資料儲存架構，而不只是假設資料庫隨時都是正確的答案。 有數個不同的選項可符合不同的需求，例如：

- **喜好**設定– Android 提供內建的機制，可儲存簡單的索引鍵/值組。 如果您要儲存簡單的使用者設定或小部分的資料（例如個人化資訊），則請使用平臺的原生功能來儲存這類資訊。
- **文字檔**–已下載內容的使用者輸入或快取（例如 HTML）可以直接儲存在檔案系統上。 使用適當的檔案命名慣例，協助您組織檔案和尋找資料。
- **序列化資料檔案**–物件可以在檔案系統上保存為 XML 或 JSON。 .NET framework 包含的程式庫可讓您輕鬆地序列化和還原序列化物件。 使用適當的名稱來組織資料檔案。
- **資料庫**– SQLite 資料庫引擎可在 Android 平臺上使用，而且適合用來儲存您需要查詢、排序或以其他方式操作的結構化資料。 資料庫儲存體適用于具有許多屬性的資料清單。
- **影像檔**案–雖然可以將二進位資料儲存在行動裝置上的資料庫中，但建議您直接將它們儲存在檔案系統中。 如有需要，您可以將檔案名儲存在資料庫中，以將影像與其他資料產生關聯。 處理大型影像或許多影像時，最好是規劃快取策略來刪除您不再需要的檔案，以避免耗用所有使用者的儲存空間。

如果資料庫是您應用程式的正確儲存機制，則本檔的其餘部分將討論如何在 Xamarin 平臺上使用 SQLite。

## <a name="advantages-of-using-a-database"></a>使用資料庫的優點

在您的行動應用程式中使用 SQL database 有幾個優點：

- SQL 資料庫可以有效率地儲存結構化資料。
- 您可以使用複雜的查詢來解壓縮特定的資料。
- 查詢結果可以進行排序。
- 查詢結果可以匯總。
- 具有現有資料庫技能的開發人員可以利用其知識來設計資料庫和資料存取程式碼。
- 來自已連線應用程式之伺服器元件的資料模型，可能會在行動應用程式中重複使用（全部或部分）。

## <a name="sqlite-database-engine"></a>SQLite 資料庫引擎

SQLite 是 Google 為其行動平臺採用的開放原始碼資料庫引擎。 SQLite 資料庫引擎內建于這兩個作業系統中，因此開發人員不需要額外的工作就能利用它。 SQLite 適用于跨平臺行動裝置開發，因為：

- 資料庫引擎既小又快速，而且容易攜帶。
- 資料庫儲存在單一檔案中，這在行動裝置上很容易管理。
- 跨平臺使用檔案格式很容易：不論是32或64位，以及大型或小到小的系統。
- 它會實行大部分的 SQL92 標準。

因為 SQLite 的設計是小型且快速的，所以它的用法有一些注意事項：

- 不支援某些外部聯結語法。
- 僅支援資料表重新命名和 ADDCOLUMN。 您無法對您的架構執行其他修改。
- Views 是唯讀的。

您可以在網站上深入瞭解 SQLite- [SQLite.org](http://SQLite.org) -不過，使用 Sqlite 搭配 Xamarin 所需的所有資訊都包含在本檔和相關聯的範例中。 自 Android 2 開始，Android 已支援 SQLite 資料庫引擎。
雖然本章未涵蓋，但 SQLite 也可以在 Windows Phone 和 Windows 應用程式上使用。

## <a name="windows-and-windows-phone"></a>Windows 及 Windows Phone

SQLite 也可以在 Windows 平臺上使用，雖然本檔未涵蓋這些平臺。
閱讀更多[Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)和[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究，並回顧[Tim Heuer 的 blog](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx)。

## <a name="related-links"></a>相關連結

- [DataAccess 基本（範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin. 表單資料存取](~/xamarin-forms/data-cloud/data/databases.md)
