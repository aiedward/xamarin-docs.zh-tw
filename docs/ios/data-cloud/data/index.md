---
title: iOS 資料存取
description: 大部分的應用程式有某些需求，要將資料儲存在本機裝置上。 除非兩者小型的資料量，所以通常會要求資料庫和資料層應用程式來管理資料庫存取權。 iOS 具有 「 內建的 「 SQLite 資料庫引擎，並簡化了 Xamarin 的平台方式來儲存和擷取資料的存取。 本文件示範如何存取 SQLite 資料庫。
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 47f2567d81f61568aad639330dc5133856e31936
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="ios-data-access"></a>iOS 資料存取

_大部分的應用程式有某些需求，要將資料儲存在本機裝置上。除非兩者小型的資料量，所以通常會要求資料庫和資料層應用程式來管理資料庫存取權。iOS 具有 「 內建的 「 SQLite 資料庫引擎，並簡化了 Xamarin 的平台方式來儲存和擷取資料的存取。本文件示範如何存取 SQLite 資料庫。_

Xamarin.iOS 支援資料庫存取應用程式開發介面，例如：

-  ADO.NET 架構。
-  SQLite NET 3rd 合作對象文件庫。

本指南提供在說明如何存取 SQLite Xamarin.iOS 應用程式中的資料庫設定 ADO.NET 和 SQLite.NET 之前一般資料庫的高階概觀。 

大部分的這份文件中的程式碼是完全跨平台，並將 iOS 或 Android 上執行，而不需修改。 有兩個所討論的範例應用程式：

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) – 簡單的資料作業寫入以文字顯示結果顯示控制項。
-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) – 將資料作業整合到的小型工作應用程式列出並編輯簡單的資料結構。

這兩個範例方案包含 iOS 和 Android 的範例應用程式專案。

若是 Xamarin.Forms 應用程式，讀取[使用資料庫](~/xamarin-forms/app-fundamentals/databases.md)其中說明如何使用 PCL 程式庫中的 SQLite xamarin.forms。

## <a name="sections"></a>章節

-  [簡介](introduction.md)
-  [組態](configuration.md)
-  [使用 SQLite.NET ORM](using-sqlite-orm.md)
-  [使用 ADO.NET](using-adonet.md)
-  [在應用程式中使用資料](using-data-in-an-app.md)


## <a name="summary"></a>總結

本章節將討論 Xamarin.iOS SQLite 使用做為資料庫引擎中的資料存取。 「 直接 」 使用 ADO.NET 語法可以存取資料庫，或您可以包括 SQLite.NET ORM 以及執行在 C# 中的資料作業。

我們已檢閱透過兩個範例： 一個包含非常簡單的資料存取程式碼的輸出到文字欄位，以及簡單的應用程式，包括建立、 讀取、 更新和刪除功能。 我們也將討論的執行緒，以及如何植入您的應用程式，以預先填入的 SQLite 資料庫。

如需跨平台的資料存取的其他範例，請參閱我們[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究。

## <a name="related-links"></a>相關連結

- [DataAccess Basic （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [資料存取進階 （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 資料配方](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Xamarin.Forms 資料存取](~/xamarin-forms/app-fundamentals/databases.md)
