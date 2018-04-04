---
title: Xamarin.Android 資料存取
description: 大部分的應用程式有某些需求，要將資料儲存在本機裝置上。 除非兩者小型的資料量，所以通常會要求資料庫和資料層應用程式來管理資料庫存取權。  Android 有 '內建' SQLite 資料庫引擎，並簡化了 Xamarin 的平台方式來儲存和擷取資料的存取。 本文件示範如何以跨平台方式存取 SQLite 資料庫。
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 508a8f54723bfdd30b1c8ea8d4b6c1d422ae24e9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinandroid-data-access"></a>Xamarin.Android 資料存取

_大部分的應用程式有某些需求，要將資料儲存在本機裝置上。除非兩者小型的資料量，所以通常會要求資料庫和資料層應用程式來管理資料庫存取權。Android 有 '內建' SQLite 資料庫引擎，並簡化了 Xamarin 的平台方式來儲存和擷取資料的存取。本文件示範如何以跨平台方式存取 SQLite 資料庫。_

## <a name="data-access-overview"></a>資料存取概觀

大部分的應用程式有某些需求，要將資料儲存在本機裝置上。 除非兩者小型的資料量，所以通常會要求資料庫和資料層應用程式來管理資料庫存取權。 Android 這兩個具有 「 內建的 「 Sqlite 資料庫引擎和資料的存取權簡化了 Xamarin 的平台隨附於 SQLite 資料提供者。

Xamarin.Android 支援資料庫存取應用程式開發介面，例如：

-  ADO.NET 架構。
-  SQLite NET 3rd 合作對象文件庫。

本節中的程式碼的大部分是完全跨平台，並將 iOS 或 Android 上執行，而不需修改。 有兩個所討論的範例應用程式：

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash;簡單的資料作業寫入以文字顯示結果顯示控制項。

-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash;將資料作業整合到的小型工作應用程式列出並編輯簡單的資料結構。

這兩個範例方案包含 iOS 和 Android 的範例應用程式專案。

若是 Xamarin.Forms 應用程式，讀取[使用資料庫](~/xamarin-forms/app-fundamentals/databases.md)其中說明如何使用 PCL 程式庫中的 SQLite xamarin.forms。

本節中的主題將討論 Xamarin.Android SQLite 使用做為資料庫引擎中的資料存取。 使用 ADO.NET 語法，藉以 「 直接 」 可存取資料庫，或您可以包括 SQLite.NET ORM 以及執行在 C# 中的資料作業。

檢閱兩個範例： 一個包含非常簡單的資料存取程式碼的輸出到文字欄位，以及簡單的應用程式，包括建立、 讀取、 更新和刪除功能。 執行緒處理，以及如何植入預先填入的 SQLite 資料庫的應用程式也將討論。

如需跨平台的資料存取的其他範例，請參閱我們[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究。


## <a name="related-links"></a>相關連結

- [DataAccess Basic （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [資料存取進階 （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 資料配方](https://developer.xamarin.com/recipes/android/data/)
- [Xamarin.Forms 資料存取](~/xamarin-forms/app-fundamentals/databases.md)
