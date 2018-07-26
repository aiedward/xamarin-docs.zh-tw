---
title: Xamarin.iOS 資料存取
description: 本文件所連結說明如何使用本機資料庫中的 Xamarin.iOS 應用程式的指南。 連結的內容討論 SQLite.NET、 ADO.NET 以及更多。
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 017118a74528718ea99fe218f443b8df83d7c52e
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241275"
---
# <a name="xamarinios-data-access"></a>Xamarin.iOS 資料存取

Xamarin.iOS 支援資料庫存取 Api，例如：

-  ADO.NET 架構。
-  SQLite NET 第 3 方程式庫。

本指南提供在說明如何存取您的 Xamarin.iOS 應用程式中的 SQLite 資料庫設定 ADO.NET 和 SQLite.NET 之前一般資料庫的高階概觀。 

這份文件中的程式碼大多是完全跨平台，並會在 iOS 或 Android 上執行，而不需修改。 有兩個所討論的範例應用程式：

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) -簡單的資料作業寫入以文字顯示結果顯示控制項;
-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) – 小型的工作應用程式，列出編輯簡單的資料結構中整合資料作業。

這兩個範例方案包含 iOS 和 Android 範例應用程式專案。

針對 Xamarin.Forms 應用程式，請閱讀[使用資料庫](~/xamarin-forms/app-fundamentals/databases.md)其中說明如何使用 SQLite 與 Xamarin.Forms PCL 程式庫。

## <a name="sections"></a>章節

-  [簡介](introduction.md)
-  [組態](configuration.md)
-  [使用 SQLite.NET ORM](using-sqlite-orm.md)
-  [使用 ADO.NET](using-adonet.md)
-  [在應用程式中使用資料](using-data-in-an-app.md)

## <a name="summary"></a>總結

這一章所述使用 SQLite 的資料庫引擎的 Xamarin.iOS 中的資料存取。 「 直接 」 使用 ADO.NET 語法就可以存取資料庫，或您可以包含 SQLite.NET ORM，並在 C# 中執行資料作業。

我們會檢閱兩個範例： 一個包含非常簡單的資料存取程式碼的輸出到文字欄位，以及簡單的應用程式，其中包含建立、 讀取、 更新和刪除功能。 此外，我們也會討論執行緒，以及如何植入您的應用程式，以預先填入的 SQLite 資料庫。

如需跨平台資料存取的其他範例，請參閱我們[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究。

## <a name="related-links"></a>相關連結

- [DataAccess Basic （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 進階 （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin.Forms 資料存取](~/xamarin-forms/app-fundamentals/databases.md)
