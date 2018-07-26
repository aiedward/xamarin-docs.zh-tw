---
title: 資料表和資料格在 Xamarin.iOS 中的工作
description: 說明如何在 Xamarin.iOS 應用程式中顯示與 UITableView 控制項的資料的各種指南的這個文件連結。
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/06/2016
ms.openlocfilehash: ea5b6ba532d577bd503529065eef803acf3a7aa9
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241694"
---
# <a name="working-with-tables-and-cells-in-xamarinios"></a>資料表和資料格在 Xamarin.iOS 中的工作

本節將介紹用來建立並顯示資料表的類別，然後提供範例，示範如何在 Xamarin.iOS 中使用它們。 它將涵蓋用於自訂配置，實作編輯和使用 Xamarin iOS 設計工具以視覺化方式設計資料表的資料表中的預設外觀。 有時候顯示顯然是 （例如音樂應用程式） 的資料列，而有時候很難辨識表格控制項 （例如編輯連絡人 」 應用程式或交談中訊息 」 應用程式中） 的清單。

使用跨平台應用程式使用 Xamarin.Android 的人 UITableView 控制項是類似於在 Android 中的 ListView 類別 （而且 UITableViewSource 類別類似於 Android 的配接器類別）。

這些文章將採取的完整介紹使用資料表，包括：

-   **資料表組件**– 簡介，並解釋的視覺化元素`UITableView`控制項。 
-   **在資料表中顯示資料**-示範如何建立並填入資料表，使用不同的資料表和儲存格樣式及回收儲存格物件，以避免記憶體問題。 
-   **進階用法**– 建置自訂的資料格，並使用 UITableView 類別的編輯功能。 
-   **以視覺化方式建立資料表**-使用適用於 iOS 的 Xamarin 設計工具，以使用分鏡腳本建立表格驅動介面。 

## <a name="contents"></a>內容

 [資料表組件&amp;功能](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [使用資料填入資料表](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [自訂資料表的外觀](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [編輯](~/ios/user-interface/controls/tables/editing.md)
 
 [資料列動作](~/ios/user-interface/controls/tables/row-action.md)

 [在分鏡腳本中建立資料表](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [自動調整資料列高度](~/ios/user-interface/controls/tables/autosizing-row-height.md)

## <a name="related-links"></a>相關連結

- [WorkingWithTables （範例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables/)
- [分鏡腳本 （範例） 中的資料表](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
- [分鏡腳本 TableView 配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/storyboard_a_tableview)
- [MonoTouch.Dialog 簡介](~/ios/user-interface/monotouch.dialog/index.md)
- [在 Github 上的 TableEditing 範例](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [在 Github 上的 TableParts 範例](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [在 Github 上的 TableAndCellStyles 範例](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [UITableView 類別參考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [UITableViewCell 類別參考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
