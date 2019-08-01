---
title: 使用 Xamarin 編輯資料表
description: 本檔說明如何編輯 Xamarin 中的資料表。 它討論如何滑動以刪除、編輯模式及插入資料列。
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: a95e772ab0ba5fa6687ef941034f1de87f5d608a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655916"
---
# <a name="editing-tables-with-xamarinios"></a>使用 Xamarin 編輯資料表

資料表編輯功能是藉由覆寫子`UITableViewSource`類別中的方法來啟用。 最簡單的編輯行為是可以使用單一方法覆寫來實作為滑動的刪除手勢。
在編輯模式中, 可以使用資料表來完成更複雜的編輯 (包括移動資料列)。

## <a name="swipe-to-delete"></a>滑動以刪除

「滑動到刪除」功能是使用者預期的 iOS 自然手勢。 

 [![](editing-images/image10.png "刪除的滑動範例")](editing-images/image10.png#lightbox)

有三種方法覆寫會影響滑動手勢, 以在資料格中顯示 [**刪除**] 按鈕:

-   **CommitEditingStyle** –資料表來源會偵測是否已覆寫這個方法, 並自動啟用輕刷刪除手勢。 方法的執行應該`DeleteRows` `UITableView`在上呼叫, 使資料格消失, 同時也從您的模型中移除基礎資料 (例如, 陣列、字典或資料庫)。 
-   **CanEditRow** –如果覆寫 CommitEditingStyle, 則會假設所有資料列都可供編輯。 如果這個方法已實作為, 並傳回 false (針對某些特定資料列或所有資料列), 則該資料格中將無法使用「滑動至刪除」手勢。 
-   **TitleForDeleteConfirmation** –選擇性地指定 [**刪除**] 按鈕的文字。 如果未執行此方法, 按鈕文字將會是 "Delete"。 


這些方法會在`TableSource`類別中執行, 如下所示:

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

在此範例中`UITableViewSource` , 已更新為`List<TableItem>`使用 (而不是字串陣列) 做為資料來源, 因為它支援從集合中加入和刪除專案。


## <a name="edit-mode"></a>編輯模式

當資料表處於編輯模式時, 使用者會在每個資料列上看到紅色的「停止」 widget, 這會在觸及時顯示 [刪除] 按鈕。 資料表也會顯示 [控制碼] 圖示, 表示可以拖曳資料列來變更順序。
**TableEditMode**範例會依照所示來執行這些功能。

 [![](editing-images/image11.png "TableEditMode 範例會依照所示的方式來執行這些功能")](editing-images/image11.png#lightbox)

上`UITableViewSource`有許多不同的方法會影響資料表的編輯模式行為:

-   **CanEditRow** –是否可以編輯每個資料列。 傳回 false, 以避免在編輯模式中進行輕刷刪除和刪除。 
-   **CanMoveRow** –傳回 true 以啟用移動 ' handle ' 或 false 以防止移動。 
-   **EditingStyleForRow** –當資料表處於編輯模式時, 這個方法的傳回值會決定資料格是否顯示紅色刪除圖示或綠色的新增圖示。 `UITableViewCellEditingStyle.None`如果資料列不應該是可編輯的, 則傳回。 
-   **MoveRow** –在移動資料列時呼叫, 以便可以修改基礎資料結構, 以符合資料表中顯示的資料。 


前三個方法的實值相當簡單, 除非您想要使用`indexPath`來變更特定資料列的行為, 只要將整個資料表的傳回值硬式編碼即可。

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

`MoveRow`這項工作會稍微複雜一點, 因為它需要改變基礎資料結構, 以符合新的順序。 由於資料會實作為`List`下面的程式碼, 因此會刪除其舊位置的資料項目, 並將它插入新的位置。 如果資料是儲存在具有 ' order ' 資料行的 SQLite 資料庫資料表中 (例如), 這個方法就必須執行一些 SQL 作業來重新排列該資料行中的數位。

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

最後, 若要讓資料表進入編輯模式, [**編輯**] 按鈕必須呼叫`SetEditing` , 如下所示:

```csharp
table.SetEditing (true, true);
```

當使用者完成編輯時, [**完成**] 按鈕應該會關閉編輯模式:

```csharp
table.SetEditing (false, true);
```


## <a name="row-insertion-editing-style"></a>資料列插入編輯樣式

資料表內的資料列插入是不尋常的使用者介面, 標準 iOS 應用程式中的主要範例是 [**編輯連絡人**] 畫面。 這個螢幕擷取畫面顯示資料列插入功能的運作方式–在編輯模式中, 有一個額外的資料列 (當按下時) 會將額外的資料列插入資料中。 完成編輯時, 會移除暫存的 **(加入新的)** 資料列。

 [![](editing-images/image12.png "完成編輯時, 會移除暫時的 [加入新的資料列]")](editing-images/image12.png#lightbox)

上`UITableViewSource`有許多不同的方法會影響資料表的編輯模式行為。 在範例程式碼中, 這些方法已實作為下列方式:

-   **EditingStyleForRow** – `UITableViewCellEditingStyle.Delete`傳回包含`UITableViewCellEditingStyle.Insert`資料的資料列, 並傳回最後一列 (會特別新增為 [插入] 按鈕的行為)。 
-   **CustomizeMoveTarget** –當使用者移動資料格時, 此選擇性方法的傳回值可能會覆寫其位置的選擇。 這表示您可以防止它們在特定位置「卸載」資料格, 例如, 此範例會防止任何資料列在 [ **(加入新的)** ] 資料列之後移動。 
-   **CanMoveRow** –傳回 true 以啟用移動 ' handle ' 或 false 以防止移動。 在此範例中, 最後一個資料列的移動「控制碼」已隱藏, 因為它的目的只是做為 [插入] 按鈕。 


我們也加入了兩個自訂方法來新增「插入」資料列, 然後在不再需要時將它移除。 它們是從 [**編輯**] 和 [**完成**] 按鈕進行呼叫:

-   **WillBeginTableEditing** –觸及 [**編輯**] 按鈕時, 它`SetEditing`會呼叫將資料表置於編輯模式。 這會觸發 WillBeginTableEditing 方法, 在資料表的結尾顯示 **(加入新的)** 資料列, 以作為「插入按鈕」。 
-   **DidFinishTableEditing** –觸及`SetEditing` [完成] 按鈕時, 會再次呼叫以關閉編輯模式。 當不再需要編輯時, 範例程式碼會從資料表中移除 **(加入新的)** 資料列。 


這些方法覆寫會在範例檔案**TableEditModeAdd/Code/TableSource**中執行:

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

當資料表的編輯模式為 [已啟用] 或 [已停用] 時, 會使用這兩個自訂方法來加入和移除 **(加入新的)** 資料列:

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

最後, 此程式碼會使用 lambda 來具現化 [**編輯**] 和 [**完成**] 按鈕, 以在觸及時啟用或停用編輯模式:

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

不常使用此資料列插入 UI 模式, 不過您也可以使用`UITableView.BeginUpdates`和`EndUpdates`方法, 以動畫顯示在任何資料表中插入或移除儲存格。 使用這些方法的規則是`RowsInSection` , 在`BeginUpdates`和`EndUpdates`呼叫之間傳回的值差異, 必須符合使用`InsertRows`和`DeleteRows`方法新增/刪除的資料格的淨數目。 如果基礎資料來源未變更為符合資料表視圖上的插入/刪除, 則會發生錯誤。


## <a name="related-links"></a>相關連結

- [WorkingWithTables (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
