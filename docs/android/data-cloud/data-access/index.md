---
title: Xamarin.Android Data Access
description: 大部分的應用程式有一些需求，將資料儲存在本機裝置上。 除非資料總量很微小，這通常需要的資料庫和資料層應用程式來管理資料庫存取權。  Android 提供 '內建' SQLite 資料庫引擎，並簡化 Xamarin 平台來儲存和擷取資料的存取。 本文件說明如何存取方式跨平台的 SQLite 資料庫。
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 6858e290d93007d6054ba0ef63dce86e6e2e53e3
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649627"
---
# <a name="xamarinandroid-data-access"></a>Xamarin.Android Data Access

_大部分的應用程式有一些需求，將資料儲存在本機裝置上。除非資料總量很微小，這通常需要的資料庫和資料層應用程式來管理資料庫存取權。Android 提供 '內建' SQLite 資料庫引擎，並簡化 Xamarin 平台來儲存和擷取資料的存取。本文件說明如何存取方式跨平台的 SQLite 資料庫。_

## <a name="data-access-overview"></a>資料存取概觀

大部分的應用程式有一些需求，將資料儲存在本機裝置上。 除非資料總量很微小，這通常需要的資料庫和資料層應用程式來管理資料庫存取權。 這兩個 android 提供 SQLite 資料庫引擎 」 內建 」 和 Xamarin 平台的 SQLite 資料提供者所隨附的簡化資料的存取權。

Xamarin.Android 支援資料庫存取 Api，例如：

- ADO.NET 架構。
- SQLite NET 第 3 方程式庫。

在本節中的程式碼大多是完全跨平台，並會在 iOS 或 Android 上執行，而不需修改。 有兩個所討論的範例應用程式：

- [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash;簡單的資料作業寫入以文字顯示結果顯示控制項;

- [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash;整合小型的工作應用程式，列出編輯簡單的資料結構中的資料作業。

這兩個範例方案包含 iOS 和 Android 範例應用程式專案。

針對 Xamarin.Forms 應用程式，請閱讀[使用資料庫](~/xamarin-forms/data-cloud/data/databases.md)其中說明如何使用 SQLite 與 Xamarin.Forms PCL 程式庫。

在本節中的主題將討論在 Xamarin.Android 使用 SQLite 的資料庫引擎中的資料存取。 「 直接 」 可以使用 ADO.NET 語法，可以存取資料庫，或您可以包含 SQLite.NET ORM，並在 C# 中執行資料作業。

兩個範例會檢閱： 一個包含非常簡單的資料存取程式碼的輸出到文字欄位，以及簡單的應用程式，其中包含建立、 讀取、 更新和刪除功能。 也會討論執行緒和如何植入您的應用程式，以預先填入的 SQLite 資料庫。

如需跨平台資料存取的其他範例，請參閱我們[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究。


## <a name="related-links"></a>相關連結

- [DataAccess Basic （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 進階 （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin.Forms 資料存取](~/xamarin-forms/data-cloud/data/databases.md)
