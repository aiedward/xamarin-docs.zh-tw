---
title: "使用資料表和資料格"
description: "顯示資料是使用 Xamarin.iOS UITableView"
ms.topic: article
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/06/2016
ms.openlocfilehash: f6abcaa3a771954785df83e80c7e46dd200e6986
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-tables-and-cells"></a>使用資料表和資料格


本節介紹用來建立及顯示資料表的類別，然後提供如何使用這些 Xamarin.iOS 中的範例。 會涵蓋自訂版面配置，實作編輯和使用 Xamarin iOS 設計工具以視覺化方式設計資料表的資料表，使用的預設外觀。 有時候顯示顯然是資料列 （例如音樂應用程式） 和有時候很難辨識表格控制項 （例如編輯連絡人應用程式中或交談中訊息 」 應用程式中） 的清單。

是跨平台應用程式與 Xamarin.Android 上使用，UITableView 控制項至清單檢視中的類別 Android （與類似 UITableViewSource 類別是類似於 Android 的配接器類別）。

這些文件會查看全方位使用的資料表，包括：

-   **資料表部分**– 簡介及說明的視覺化元素`UITableView`控制項。 
-   **在資料表中顯示資料**– 示範如何建立及擴展資料表，使用不同的資料表和儲存格樣式，以避免回收儲存格物件的記憶體問題。 
-   **進階使用**– 建置自訂儲存格，並使用 UITableView 類別的編輯功能。 
-   **以視覺化方式建立資料表**– 使用適用於 iOS 的 Xamarin 設計工具來建立表格驅動介面使用分鏡腳本。 


## <a name="contents"></a>內容

 [資料表部分&amp;功能](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [使用資料填入資料表](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [自訂資料表的外觀](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [編輯](~/ios/user-interface/controls/tables/editing.md)
 
 [資料列的動作](~/ios/user-interface/controls/tables/row-action.md)

 [在分鏡腳本中建立資料表](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [自動調整大小的資料列高度](~/ios/user-interface/controls/tables/autosizing-row-height.md)


## <a name="related-links"></a>相關連結

- [WorkingWithTables （範例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables/)
- [資料表中的分鏡腳本 （範例）](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [分鏡腳本的簡介](~/ios/user-interface/storyboards/index.md)
- [分鏡腳本 TableView 注定](https://developer.xamarin.com/recipes/ios/general/storyboard/storyboard_a_tableview)
- [MonoTouch.Dialog 簡介](~/ios/user-interface/monotouch.dialog/index.md)
- [Github 上的 TableEditing 範例](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Github 上的 TableParts 範例](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Github 上的 TableAndCellStyles 範例](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [UITableView 類別參考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [UITableViewCell 類別參考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
