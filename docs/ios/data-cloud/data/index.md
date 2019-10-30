---
title: Xamarin. iOS 資料存取
description: 本檔連結的指南會描述如何在 Xamarin iOS 應用程式中使用本機資料庫。 連結的內容會討論 SQLite.NET、ADO.NET 等等。
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/11/2016
ms.openlocfilehash: 163bd88349ee55af5f518a20364bbce7fe6052b0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008430"
---
# <a name="xamarinios-data-access"></a>Xamarin. iOS 資料存取

Xamarin 支援資料庫存取 Api，例如：

- ADO.NET 架構。
- SQLite-NET 協力廠商程式庫。

本指南在說明如何設定 ADO.NET 和 SQLite.NET 來存取您的 Xamarin iOS 應用程式中的 SQLite 資料庫之前，一般會先提供資料庫的高階總覽。 

本檔中的大部分程式碼都是跨平臺，並會在 iOS 或 Android 上執行，而不需要修改。 已討論兩個範例應用程式：

- [**DataAccess_Basic**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) –簡單的資料作業會將結果寫入文字顯示控制項;
- [**DataAccess_Advanced**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) –將資料作業整合到小型的工作應用程式，以列出和編輯簡單的資料結構。

這兩個範例解決方案都包含 iOS 和 Android 範例應用程式專案。

針對 Xamarin 應用程式，請參閱使用[資料庫](~/xamarin-forms/data-cloud/data/databases.md)，其中說明如何在具有 XAMARIN 的 PCL 程式庫中使用 SQLite。

## <a name="sections"></a>章節

- [簡介](introduction.md)
- [組態](configuration.md)
- [使用 SQLite.NET ORM](using-sqlite-orm.md)
- [使用 ADO.NET](using-adonet.md)
- [在應用程式中使用資料](using-data-in-an-app.md)

## <a name="summary"></a>總結

本章節使用 SQLite 做為資料庫引擎，討論了 Xamarin 中的資料存取。 您可以使用 ADO.NET 語法來「直接」存取資料庫，也可以在中C#包含 SQLite.NET ORM 和執行資料作業。

我們審查了兩個範例：一個包含非常簡單的資料存取程式碼，會輸出到文字欄位，以及一個包含建立、讀取、更新和刪除功能的簡單應用程式。 我們也討論過執行緒，以及如何使用預先填入的 SQLite 資料庫來植入您的應用程式。

如需跨平臺資料存取的其他範例，請參閱我們的[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究。

## <a name="related-links"></a>相關連結

- [DataAccess 基本（範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin. 表單資料存取](~/xamarin-forms/data-cloud/data/databases.md)
