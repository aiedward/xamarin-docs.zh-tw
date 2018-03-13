---
title: "編輯"
ms.topic: article
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: eb8bae676a4b5c682cdb204c6d38ffc1112b483a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="editing"></a>編輯

藉由覆寫方法中的啟用資料表的編輯功能`UITableViewSource`子類別。 最簡單的編輯行為是可以用單一方法覆寫來實作撥動至刪除筆勢。
編輯模式中的資料表可以完成更複雜的編輯 （包括移動資料列）。

## <a name="swipe-to-delete"></a>要刪除的撥動

若要刪除功能撥動是自然軌跡中的使用者預期的 iOS。 

 [![](editing-images/image10.png "刪除撥動的範例")](editing-images/image10.png#lightbox)

有三個影響撥動手勢，以顯示的方法覆寫**刪除**在資料格中的按鈕：

-   **CommitEditingStyle** – 如果此方法會覆寫，而且會自動啟用撥動至刪除筆勢，偵測到資料表來源。 方法的實作應該呼叫`DeleteRows`上`UITableView`造成消失，以及從您的模型 （例如陣列、 字典或資料庫） 將移除基礎資料之儲存格。 
-   **CanEditRow** – 如果 CommitEditingStyle 會覆寫，會假設是可編輯的所有資料列。 如果此方法的實作，且會傳回 false （或某些特定的資料列的所有資料列） 然後撥動至刪除筆勢將無法使用該資料格中。 
-   **TitleForDeleteConfirmation** – （選擇性） 指定的文字**刪除** 按鈕。 如果未實作這個方法的按鈕文字將會 「 刪除 」。 


這些方法中實作`TableSource`類別如下所示：

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

此範例中`UITableViewSource`已更新為使用`List<TableItem>`（而不是字串陣列） 做為資料來源，因為它支援加入和刪除項目集合。


## <a name="edit-mode"></a>編輯模式

資料表處於編輯模式時使用者會看到紅色的 'stop' widget 會顯示 [刪除] 按鈕時接觸到每個資料列上。 資料表也會顯示 '控制代碼' 圖示，以表示資料列，可以拖曳來變更順序。
**TableEditMode**範例會實作這些功能所示。

 [![](editing-images/image11.png "TableEditMode 範例所示，實作這些功能")](editing-images/image11.png#lightbox)

有多種不同的方法上`UITableViewSource`會影響資料表的編輯模式的行為：

-   **CanEditRow** – 是否可以編輯每個資料列。 傳回 false，以防止撥動至刪除和編輯模式中的刪除。 
-   **CanMoveRow** – 傳回 true，以啟用移動 '控制代碼' 或 false，以避免移動。 
-   **EditingStyleForRow** – 當資料表是在編輯模式中，這個方法的傳回值會決定資料格會顯示紅色刪除圖示或綠色加入圖示。 傳回`UITableViewCellEditingStyle.None`如果資料列不是可編輯的。 
-   **MoveRow** – 當資料列，讓您可以修改基礎資料結構符合資料表中所顯示的資料移動時呼叫。 


前三個方法的實作是很直接的 – 除非您想要使用`indexPath`若要變更特定資料列的行為，只是硬傳回值的整個資料表。

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

`MoveRow`實作會稍微複雜，因為它需要變更基礎資料結構以符合新的順序。 因為資料會實作為`List`下列程式碼刪除資料的項目在舊的位置，並將它插入新的位置。 如果資料 （例如） 儲存在具有 'order' 資料行 SQLite 資料庫資料表中，這個方法會改為需要執行一些重新排列的數字，該資料行中的 SQL 作業。

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

最後，若要取得資料表至編輯模式，則**編輯**按鈕需要呼叫`SetEditing`以這種方式

```csharp
table.SetEditing (true, true);
```

使用者完成時，編輯**完成**按鈕應該關閉編輯模式：

```csharp
table.SetEditing (false, true);
```


## <a name="row-insertion-editing-style"></a>資料列插入編輯樣式

從資料表中的資料列插入是不常見的使用者介面-標準的 iOS 應用程式中的主要範例是**編輯連絡人**螢幕。 這個螢幕擷取畫面顯示資料列插入功能的運作方式，在編輯模式，沒有其他資料列的 （當按下） 將額外的資料列插入至資料。 編輯完成時，暫存**（新增）**移除資料列。

 [![](editing-images/image12.png "當編輯完成時，暫存加入新資料列會移除")](editing-images/image12.png#lightbox)

有多種不同的方法上`UITableViewSource`會影響資料表的編輯模式行為。 這些方法已實作，如下所示的範例程式碼：

-   **EditingStyleForRow** – 傳回`UITableViewCellEditingStyle.Delete`的資料列包含資料，並傳回`UITableViewCellEditingStyle.Insert`最後一個資料列 （這會特別成插入按鈕行為加入）。 
-   **CustomizeMoveTarget** – 雖然使用者所移動的資料格傳回的值從這個選擇性的方法可以覆寫其選擇的位置。 這表示您可以防止 '卸除' 中的特定位置 – 例如此範例，防止之後移動任何資料列儲存格**（新增）**資料列。 
-   **CanMoveRow** – 傳回 true，以啟用移動 '控制代碼' 或 false，以避免移動。 在範例中，最後一個資料列會有 '控' 隱藏，因為這要作為插入按鈕只到伺服器。 


我們也加入兩個自訂的方法，加入 'insert' 的資料列，然後移除它再次不再需要時。 從呼叫**編輯**和**完成**按鈕：

-   **WillBeginTableEditing** – 當**編輯**按鈕接觸到它呼叫`SetEditing`，將資料表置於編輯模式。 這會觸發 WillBeginTableEditing 方法顯示其中**（新增）**結尾要做為 '插入按鈕' 資料表的資料列。 
-   **DidFinishTableEditing** – 當 完成 按鈕會接觸到`SetEditing`再次呼叫以關閉 編輯模式。 範例程式碼會移除**（新增）**編輯資料表中的資料列已不再需要。 


範例檔案中實作這些方法的覆寫**TableEditModeAdd/Code/TableSource.cs**:

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

這兩個自訂方法可用來加入和移除**（新增）**啟用或停用資料表的編輯模式時的資料列：

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

最後，此程式碼會具現化**編輯**和**完成**按鈕，以啟用或停用編輯模式，當它們接觸 lambda:

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

此資料列插入 UI 模式不常用，不過您也可以使用`UITableView.BeginUpdates`和`EndUpdates`方法來建立動畫的插入或移除任何資料表中的資料格。 使用這些方法的規則是所傳回值的差異`RowsInSection`之間`BeginUpdates`和`EndUpdates`呼叫必須符合的網路與新增/刪除的資料格數目`InsertRows`和`DeleteRows`方法。 如果基礎資料來源不會變更為符合插入/刪除資料表檢視表上會發生錯誤。


## <a name="related-links"></a>相關連結

- [WorkingWithTables （範例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
