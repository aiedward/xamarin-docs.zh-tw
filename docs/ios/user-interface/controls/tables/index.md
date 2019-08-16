---
title: 在 Xamarin 中使用資料表和資料格
description: 本檔連結的各種指南, 描述如何在 Xamarin iOS 應用程式中使用 UITableView 控制項來顯示資料。
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/06/2016
ms.openlocfilehash: 0dd3830b4903658d0b014eceaad9c57859963c04
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528638"
---
# <a name="working-with-tables-and-cells-in-xamarinios"></a>在 Xamarin 中使用資料表和資料格

本節介紹用來建立和顯示資料表的類別, 然後提供如何在 Xamarin 中使用它們的範例。 它將涵蓋使用資料表的預設面板、自訂配置、執行編輯, 以及使用 Xamarin iOS 設計工具以視覺化方式設計資料表。 有時顯示的是資料列清單 (例如音樂應用程式), 以及其他難以辨識資料表控制項的時間 (例如, 在連絡人應用程式中編輯, 或是在 [訊息] 應用程式中的交談)。

對於使用 Xamarin 的跨平臺應用程式, UITableView 控制項類似于 Android 中的 ListView 類別 (而 UITableViewSource 類別類似于 Android 的介面卡類別)。

這些文章將全面探討如何使用資料表, 包括:

- **資料表元件**-簡介和說明`UITableView`控制項的視覺元素。 
- **在資料表中顯示資料**–示範如何建立和填入資料表、使用不同的資料表和儲存格樣式, 以及藉由回收資料格物件來避免發生記憶體問題。 
- **Advanced usage** –建立自訂資料格, 並使用 UITableView 類別的編輯功能。 
- 以**視覺化方式建立資料表**–使用 Xamarin Designer for iOS 來建立具有分鏡腳本的資料表驅動介面。 

## <a name="contents"></a>內容

 [資料表元件&amp;功能](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [使用資料填入資料表](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [自訂資料表的外觀](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [編輯](~/ios/user-interface/controls/tables/editing.md)
 
 [資料列動作](~/ios/user-interface/controls/tables/row-action.md)

 [在分鏡腳本中建立資料表](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [自動調整資料列高度](~/ios/user-interface/controls/tables/autosizing-row-height.md)

## <a name="related-links"></a>相關連結

- [WorkingWithTables (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
- [分鏡腳本中的資料表 (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable)
- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
- [分鏡腳本 TableView 配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/storyboard_a_tableview)
- [MonoTouch 簡介](~/ios/user-interface/monotouch.dialog/index.md)
- [Github 上的 TableEditing 範例](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Github 上的 TableParts 範例](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Github 上的 TableAndCellStyles 範例](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [UITableView 類別參考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [UITableViewCell 類別參考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
