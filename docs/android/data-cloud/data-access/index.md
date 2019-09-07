---
title: Xamarin. Android 資料存取
description: 大部分的應用程式都需要將資料儲存在本機裝置上。 除非完整的資料量很小，否則應用程式通常需要資料庫和資料層來管理資料庫存取。  Android 具有 SQLite 資料庫引擎的內建功能，而且 Xamarin 的平臺簡化了儲存和取出資料的存取。 本檔說明如何以跨平臺方式存取 SQLite 資料庫。
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 2343603199661ea39b1f0af172ce0ccf48a2cd66
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754581"
---
# <a name="xamarinandroid-data-access"></a>Xamarin. Android 資料存取

_大部分的應用程式都需要將資料儲存在本機裝置上。除非完整的資料量很小，否則應用程式通常需要資料庫和資料層來管理資料庫存取。Android 具有 SQLite 資料庫引擎的內建功能，而且 Xamarin 的平臺簡化了儲存和取出資料的存取。本檔說明如何以跨平臺方式存取 SQLite 資料庫。_

## <a name="data-access-overview"></a>資料存取總覽

大部分的應用程式都需要將資料儲存在本機裝置上。 除非完整的資料量很小，否則應用程式通常需要資料庫和資料層來管理資料庫存取。 Android 的 SQLite 資料庫引擎都是「內建」，而 Xamarin 的平臺會簡化資料的存取，這是由 SQLite Data Provider 隨附。

Xamarin 支援資料庫存取 Api，例如：

- ADO.NET 架構。
- SQLite-NET 協力廠商程式庫。

本節中大部分的程式碼都是跨平臺，並會在 iOS 或 Android 上執行，而不需要修改。 已討論兩個範例應用程式：

- [**DataAccess_Basic**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)&ndash;簡單的資料作業會將結果寫入文字顯示控制項;

- [**DataAccess_Advanced**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)&ndash;將資料作業整合到小型的工作應用程式，以列出和編輯簡單的資料結構。

這兩個範例解決方案都包含 iOS 和 Android 範例應用程式專案。

針對 Xamarin 應用程式，請參閱使用[資料庫](~/xamarin-forms/data-cloud/data/databases.md)，其中說明如何在具有 XAMARIN 的 PCL 程式庫中使用 SQLite。

本節中的主題會使用 SQLite 做為資料庫引擎，來討論 Xamarin 中的資料存取。 您可以使用 ADO.NET 語法來「直接」存取資料庫，也可以在中C#包含 SQLite.NET ORM 和執行資料作業。

系統會檢查兩個範例：一個包含非常簡單的資料存取程式碼，會輸出到文字欄位，以及一個包含建立、讀取、更新和刪除功能的簡單應用程式。 同時也會討論執行緒，以及如何使用預先填入的 SQLite 資料庫來植入您的應用程式。

如需跨平臺資料存取的其他範例，請參閱我們的[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究。

## <a name="related-links"></a>相關連結

- [DataAccess 基本（範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin. 表單資料存取](~/xamarin-forms/data-cloud/data/databases.md)
