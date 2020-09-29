---
title: 使用 Xamarin 編輯資料表
description: 本檔說明如何編輯 Xamarin 中的資料表。 它討論滑動至刪除、編輯模式和資料列插入。
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 3a33c2191f39ea72113a1d7eab660e1a172f752f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430870"
---
# <a name="editing-tables-with-xamarinios"></a>使用 Xamarin 編輯資料表

您可以覆寫子類別中的方法，以啟用資料表編輯功能 `UITableViewSource` 。 最簡單的編輯行為是可使用單一方法覆寫來執行的輕量刪除手勢。
在編輯模式中，可以使用資料表來完成更複雜的編輯 (包括移動資料列) 。

## <a name="swipe-to-delete"></a>滑動以刪除

滑動至刪除功能是使用者預期的 iOS 自然手勢。 

 [![滑動至刪除的範例](editing-images/image10.png)](editing-images/image10.png#lightbox)

有三種方法覆寫會影響滑動手勢，以顯示儲存格中的 [ **刪除** ] 按鈕：

- **CommitEditingStyle** –資料表來源會偵測是否已覆寫這個方法，並自動啟用滑動刪除筆勢。 方法的執行應該  `DeleteRows` 在上呼叫，  `UITableView` 使儲存格消失，而且也會從模型中移除基礎資料 (例如，陣列、字典或資料庫) 。 
- **CanEditRow** –如果覆寫 CommitEditingStyle，則會假設所有資料列都是可編輯的。 如果這個方法是實值，並針對某些特定資料列傳回 false (，或針對所有資料) 列傳回 false，則在該資料格中將無法使用「滑動至刪除」手勢。 
- **TitleForDeleteConfirmation** –選擇性地指定 [  **刪除** ] 按鈕的文字。 如果未執行此方法，則按鈕文字會是 "Delete"。 

這些方法會在類別中執行，如下所示 `TableSource` ：

```csharp
public override void CommitEditingStyle (UITableView tableView, UITableViewCellEditingStyle editingStyle, Foundation.NSIndexPath indexPath)
{
    switch (editingStyle) {
        case UITableViewCellEditingStyle.Delete:
            // remove the item from the underlying data source
            tableItems.RemoveAt(indexPath.Row);
            // delete the row from the table
            tableView.DeleteRows (new NSIndexPath[] { indexPath }, UITableViewRowAnimation.Fade);
            break;
        case UITableViewCellEditingStyle.None:
            Console.WriteLine ("CommitEditingStyle:None called");
            break;
    }
}
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override string TitleForDeleteConfirmation (UITableView tableView, NSIndexPath indexPath)
{   // Optional - default text is 'Delete'
    return "Trash (" + tableItems[indexPath.Row].SubHeading + ")";
}
```

在此範例中， `UITableViewSource` 已更新為使用 `List<TableItem>` (而不是字串陣列) 作為資料來源，因為它支援在集合中加入和刪除專案。

## <a name="edit-mode"></a>編輯模式

當資料表處於編輯模式時，使用者會在每個資料列上看到紅色的「停止」小工具，這會在觸及時顯示 [刪除] 按鈕。 此資料表也會顯示「控點」圖示，表示可以拖曳資料列來變更順序。
**TableEditMode**範例會執行這些功能，如下所示。

 [![TableEditMode 範例會執行這些功能，如下所示](editing-images/image11.png)](editing-images/image11.png#lightbox)

有許多不同 `UITableViewSource` 的方法會影響資料表的編輯模式行為：

- **CanEditRow** –是否可以編輯每個資料列。 傳回 false 以防止在編輯模式中進行滑動至刪除和刪除。 
- **CanMoveRow** –傳回 true 表示啟用移動 ' handle ' 或 false 以防止移動。 
- **EditingStyleForRow** –當資料表處於編輯模式時，此方法的傳回值會決定儲存格顯示紅色刪除圖示或綠色新增圖示。 `UITableViewCellEditingStyle.None`如果資料列不應該是可編輯的，則傳回。 
- **MoveRow** –移動資料列時呼叫，以便修改基礎資料結構，使其符合資料表中所顯示的資料。 

前三個方法的實值相當簡單，除非您想要使用 `indexPath` 來變更特定資料列的行為，只要將整份資料表的傳回值硬式編碼即可。

```csharp
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you don't allow re-ordering
}
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    return UITableViewCellEditingStyle.Delete; // this example doesn't support Insert
}
```

其實 `MoveRow` 作更複雜，因為它需要改變基礎資料結構，以符合新的順序。 因為資料會實作為下列程式碼，所以會 `List` 刪除其舊位置的資料項目，並將其插入新的位置。 如果資料儲存在具有 ' order ' 資料行的 SQLite 資料庫資料表中 (例如) ，則這個方法會改為需要執行一些 SQL 作業來重新排列該資料行中的數位。

```csharp
public override void MoveRow (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    var item = tableItems[sourceIndexPath.Row];
    var deleteAt = sourceIndexPath.Row;
    var insertAt = destinationIndexPath.Row;
    
    // are we inserting 
    if (destinationIndexPath.Row < sourceIndexPath.Row) {
        // add one to where we delete, because we're increasing the index by inserting
        deleteAt += 1;
    } else {
        // add one to where we insert, because we haven't deleted the original yet
        insertAt += 1;
    }
    tableItems.Insert (insertAt, item);
    tableItems.RemoveAt (deleteAt);
}
```

最後，若要讓資料表進入編輯模式，[ **編輯** ] 按鈕需要呼叫 `SetEditing` 如下

```csharp
table.SetEditing (true, true);
```

當使用者完成編輯時，[ **完成** ] 按鈕應該會關閉編輯模式：

```csharp
table.SetEditing (false, true);
```

## <a name="row-insertion-editing-style"></a>資料列插入編輯樣式

資料表內的資料列插入是罕見的使用者介面，標準 iOS 應用程式中的主要範例是「 **編輯連絡人** 」畫面。 這個螢幕擷取畫面顯示資料列插入功能的運作方式–在編輯模式中，當按下) 將額外的資料列插入資料時，會 (另一個資料列。 編輯完成時，將會移除暫時 ** (新增) ** 資料列。

 [![編輯完成時，將會移除暫存的加入新資料列](editing-images/image12.png)](editing-images/image12.png#lightbox)

有許多不同的方法 `UITableViewSource` 會影響資料表的編輯模式行為。 在範例程式碼中，這些方法的執行方式如下：

- **EditingStyleForRow** –  `UITableViewCellEditingStyle.Delete` 針對包含資料的資料列傳回，然後  `UITableViewCellEditingStyle.Insert` 針對最後一個資料列傳回， (將會特別新增以做為插入按鈕) 。 
- **CustomizeMoveTarget** –當使用者移動資料格時，此選擇性方法的傳回值可能會覆寫其選擇的位置。 這表示您可以防止它們在某些位置中「卸載」資料格，例如此範例會防止在  ** (加入新的) ** 資料列之後，移動任何資料列。 
- **CanMoveRow** –傳回 true 表示啟用移動 ' handle ' 或 false 以防止移動。 在此範例中，最後一個資料列的移動 ' handle ' 是隱藏的，因為它的目的只是要做為插入按鈕的伺服器。 

我們也會新增兩個自訂方法來新增「插入」資料列，然後在不再需要時將它再次移除。 它們是從 [ **編輯** ] 和 [ **完成** ] 按鈕呼叫：

- **WillBeginTableEditing** –當您接觸到 [  **編輯** ] 按鈕時，它會呼叫  `SetEditing` 以編輯模式來放置資料表。 這會觸發 WillBeginTableEditing 方法，在此方法中，我們會在資料表結尾顯示  ** (新增) ** 資料列，作為「插入按鈕」。 
- **DidFinishTableEditing** –觸及 [完成] 按鈕時  `SetEditing` ，再次呼叫以關閉編輯模式。 當您不再需要編輯時，範例程式碼會從資料表中移除  ** (加入新的) ** 資料列。 

這些方法覆寫會在範例檔案 **TableEditModeAdd/Code/TableSource**中執行：

```csharp
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    if (tableView.Editing) {
        if (indexPath.Row == tableView.NumberOfRowsInSection (0) - 1)
            return UITableViewCellEditingStyle.Insert;
        else
            return UITableViewCellEditingStyle.Delete;
    } else // not in editing mode, enable swipe-to-delete for all rows
        return UITableViewCellEditingStyle.Delete;
}
public override NSIndexPath CustomizeMoveTarget (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath proposedIndexPath)
{
    var numRows = tableView.NumberOfRowsInSection (0) - 1; // less the (add new) one
    if (proposedIndexPath.Row >= numRows)
        return NSIndexPath.FromRowSection(numRows - 1, 0);
    else
        return proposedIndexPath;
} 
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return indexPath.Row < tableView.NumberOfRowsInSection (0) - 1;
}
```

當啟用或停用資料表的編輯模式時，這兩個自訂方法可用來新增和移除 ** (加入新的) ** 資料列：

```csharp
public void WillBeginTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // insert the 'ADD NEW' row at the end of table display
    tableView.InsertRows (new NSIndexPath[] { 
            NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0), 0) 
        }, UITableViewRowAnimation.Fade);
    // create a new item and add it to our underlying data (it is not intended to be permanent)
    tableItems.Add (new TableItem ("(add new)"));
    tableView.EndUpdates (); // applies the changes
}
public void DidFinishTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // remove our 'ADD NEW' row from the underlying data
    tableItems.RemoveAt ((int)tableView.NumberOfRowsInSection (0) - 1); // zero based :)
    // remove the row from the table display
    tableView.DeleteRows (new NSIndexPath[] { NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0) - 1, 0) }, UITableViewRowAnimation.Fade);
    tableView.EndUpdates (); // applies the changes
}
```

最後，此程式碼會具現化 [ **編輯** ] 和 [ **完成** ] 按鈕，並使用可在接觸時啟用或停用編輯模式的 lambda：

```csharp
done = new UIBarButtonItem(UIBarButtonSystemItem.Done, (s,e)=>{
    table.SetEditing (false, true);
    NavigationItem.RightBarButtonItem = edit;
    tableSource.DidFinishTableEditing(table);
});
edit = new UIBarButtonItem(UIBarButtonSystemItem.Edit, (s,e)=>{
    if (table.Editing)
        table.SetEditing (false, true); // if we've half-swiped a row
    tableSource.WillBeginTableEditing(table);
    table.SetEditing (true, true);
    NavigationItem.LeftBarButtonItem = null;
    NavigationItem.RightBarButtonItem = done;
});
```

這個資料列插入 UI 模式不常使用，不過您也可以使用 `UITableView.BeginUpdates` 和方法，在 `EndUpdates` 任何資料表中插入或移除資料格的動畫。 使用這些方法的規則是，和呼叫之間所傳回值的差異， `RowsInSection` `BeginUpdates` `EndUpdates` 必須符合使用 `InsertRows` 和方法新增/刪除的資料格的淨數目 `DeleteRows` 。 如果基礎資料來源未變更為符合資料表視圖的插入/刪除，將會發生錯誤。

## <a name="related-links"></a>相關連結

- [WorkingWithTables (範例) ](/samples/xamarin/ios-samples/workingwithtables)