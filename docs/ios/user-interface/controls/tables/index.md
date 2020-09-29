---
title: 在 Xamarin 中使用資料表和資料格
description: 本檔所連結的各種指南，會說明如何在 Xamarin iOS 應用程式中使用 Ios uitableview 範例控制項來顯示資料。
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/06/2016
ms.openlocfilehash: fc2a76d97fe6c3bb52877bd58707872068f25968
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430806"
---
# <a name="working-with-tables-and-cells-in-xamarinios"></a>在 Xamarin 中使用資料表和資料格

本節介紹用來建立和顯示資料表的類別，然後提供如何在 Xamarin 中使用這些類別的範例。 它將涵蓋如何使用資料表的預設面板、自訂配置、執行編輯，以及使用 Xamarin iOS 設計工具以視覺化方式設計資料表。 有時顯示是資料列的清單， (例如「音樂」應用程式) 和其他時候很難辨識表格控制項 (例如在「連絡人」應用程式中編輯，或是在「訊息」應用程式) 中的對話。

針對使用 Xamarin 的跨平臺應用程式，Ios uitableview 範例控制項類似于 Android 中的 ListView 類別 (而且 UITableViewSource 類別類似于 Android 的介面卡類別) 。

這些文章將全面探討如何使用資料表，包括：

- **資料表元件** -介紹和說明控制項的視覺元素  `UITableView` 。 
- **在資料表中顯示資料** -示範如何建立和擴展資料表、使用不同的資料表和資料格樣式，以及藉由回收資料格物件來避免記憶體問題。 
- **Advanced usage** –建立自訂資料格，以及使用 ios uitableview 範例類別的編輯功能。 
- 以**視覺化方式建立資料表**–使用 Xamarin Designer for iOS 建立具有分鏡腳本的表格驅動介面。 

## <a name="contents"></a>目錄

 [資料表元件 &amp; 功能](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [使用資料填入表格](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [自訂表格外觀](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [編輯](~/ios/user-interface/controls/tables/editing.md)

 [資料列動作](~/ios/user-interface/controls/tables/row-action.md)

 [在腳本中建立資料表](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)

 [自動調整列高](~/ios/user-interface/controls/tables/autosizing-row-height.md)

## <a name="related-links"></a>相關連結

- [WorkingWithTables (範例) ](/samples/xamarin/ios-samples/workingwithtables)
- [分鏡腳本中的資料表 (範例) ](/samples/xamarin/ios-samples/storyboardtable)
- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
- [TableView 配方的分鏡腳本](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/storyboard_a_tableview)
- [Monotouch.dll 簡介。對話方塊](~/ios/user-interface/monotouch.dialog/index.md)
- [Github 上的 TableEditing 範例](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Github 上的 TableParts 範例](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Github 上的 TableAndCellStyles 範例](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [Ios uitableview 範例類別參考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [UITableViewCell 類別參考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)