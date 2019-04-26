---
title: 編輯與 Xamarin.iOS 的資料表
description: 本文件說明如何編輯在 Xamarin.iOS 中的資料表。 它討論撥動以刪除、 編輯模式，以及資料列插入。
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 1267de341a88130c18254f414d2fbb1c42595a0c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61195884"
---
# <a name="editing-tables-with-xamarinios"></a>編輯與 Xamarin.iOS 的資料表

資料表的編輯功能藉由覆寫方法中的啟用`UITableViewSource`子類別。 最簡單的編輯行為是可以使用單一的方法覆寫來實作的撥動--刪除筆勢。
編輯模式中的資料表可以完成更複雜的編輯 （包括移動資料列）。

## <a name="swipe-to-delete"></a>撥動以刪除

撥動，以刪除功能是自然的手勢，在 iOS 中，使用者期望。 

 [![](editing-images/image10.png "刪除的撥動範例")](editing-images/image10.png#lightbox)

有三個方法覆寫會影響顯示的撥動手勢**刪除**在資料格中的按鈕：

-   **CommitEditingStyle** – 如果這個方法會覆寫，而且會自動啟用撥動--刪除筆勢，偵測到的資料表來源。 方法的實作應該呼叫`DeleteRows`上`UITableView`造成會消失，且也移除您的模型 （例如陣列、 字典或資料庫） 中的基礎資料的資料格。 
-   **CanEditRow** – 如果 CommitEditingStyle 會覆寫，所有資料列會假設為可編輯。 如果此方法的實作，且會傳回 false （某些特定的資料列，或所有資料列） 然後撥動--刪除筆勢將無法使用該資料格中。 
-   **TitleForDeleteConfirmation** -選擇性指定的文字**刪除** 按鈕。 如果未實作這個方法的按鈕文字將會 「 刪除 」。 


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

此範例中`UITableViewSource`已更新為使用`List<TableItem>`（而不是字串陣列） 做為資料來源，因為它支援加入和刪除項目從集合。


## <a name="edit-mode"></a>編輯模式

資料表處於編輯模式時使用者會看到紅色的 'stop' widget 會顯示 [刪除] 按鈕時接觸到每個資料列上。 資料表也會顯示 '控制代碼' 圖示，以表示資料列，可以拖曳來變更順序。
**TableEditMode**範例會實作這些功能，如所示。

 [![](editing-images/image11.png "TableEditMode 範例所示，實作這些功能")](editing-images/image11.png#lightbox)

有多種不同的方法上`UITableViewSource`會影響資料表的編輯模式的行為：

-   **CanEditRow** – 是否可以編輯每個資料列。 會傳回 false，以防止撥動--刪除和編輯模式中的刪除。 
-   **CanMoveRow** – 傳回 true，以啟用移動 '控制代碼' 或 false，以避免移動。 
-   **EditingStyleForRow** – 當資料表是處於編輯模式，這個方法的傳回值決定資料格會顯示紅色刪除圖示或綠色新增圖示。 傳回`UITableViewCellEditingStyle.None`如果資料列不是可編輯。 
-   **MoveRow** – 當資料列會移動，因此可以修改基礎資料結構，以符合的資料，因為它顯示在資料表中時呼叫。 


除非您想要使用的前三個方法的實作是相當直截了當-`indexPath`若要變更特定的資料列的行為，只是硬式編碼傳回值，整個資料表。

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

`MoveRow`實作是稍微複雜一點，因為它需要改變基礎資料結構以符合新的順序。 由於資料會實作為`List`下列程式碼會刪除資料的項目在舊的位置，並將它插入在新的位置。 如果資料已儲存在具有 'order' 資料行的 SQLite 資料庫資料表中 （舉例來說），這個方法會改為需要執行一些 SQL 作業，以重新排列該資料行中的數字。

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

最後，若要取得資料表至編輯模式，則**編輯** 按鈕，就需要呼叫`SetEditing`如下所示

```csharp
table.SetEditing (true, true);
```

當使用者完成時，編輯**完成**按鈕應該關閉編輯模式：

```csharp
table.SetEditing (false, true);
```


## <a name="row-insertion-editing-style"></a>資料列插入編輯樣式

從資料表中的資料列插入是一種常見的使用者介面-標準的 iOS 應用程式中的主要範例是**編輯連絡人**螢幕。 此螢幕擷取畫面顯示資料列插入功能的運作方式 – 編輯中沒有其他資料列的 （當按下） 的模式，請將插入資料的其他資料列。 編輯完成時，temporary **（新增）** 會移除資料列。

 [![](editing-images/image12.png "完成編輯時，暫時加入新資料列會移除")](editing-images/image12.png#lightbox)

有多種不同的方法上`UITableViewSource`會影響資料表的編輯模式行為。 這些方法已實作，如下所示的範例程式碼：

-   **EditingStyleForRow** – 傳回`UITableViewCellEditingStyle.Delete`資料列包含資料，並傳回`UITableViewCellEditingStyle.Insert`最後一個資料列 （這是專為做 [插入] 按鈕，將會新增）。 
-   **CustomizeMoveTarget** – 雖然使用者所移動的資料格傳回值，這個選用的方法可以覆寫其選擇的位置。 這表示您可以防止 '卸除' 特定位置 – 例如防止任何資料列之後移動這個範例中的資料格 **（新增）** 資料列。 
-   **CanMoveRow** – 傳回 true，以啟用移動 '控制代碼' 或 false，以避免移動。 在範例中，最後一個資料列已隱藏，因為它要伺服器為插入按鈕只移動 '控制代碼'。 


我們也加入兩個自訂的方法，加入 'insert' 的資料列，然後移除它一次時不再需要。 從呼叫**編輯**並**完成**按鈕：

-   **WillBeginTableEditing** – 當**編輯** 按鈕會觸及其呼叫`SetEditing`將資料表置於編輯模式。 這會觸發 WillBeginTableEditing 方法顯示 **（新增）** 做為 '插入按鈕' 資料表結尾處的資料列。 
-   **DidFinishTableEditing** – 當 完成 按鈕都會被接觸到`SetEditing`會再次呼叫以關閉 編輯模式。 範例程式碼會移除 **（新增）** 不再需要時編輯資料表中的資料列。 


在範例檔案中實作這些方法的覆寫**TableEditModeAdd/Code/TableSource.cs**:

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

這兩個自訂方法來新增及移除 **（新增）** 啟用或停用資料表的編輯模式時的資料列：

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

最後，此程式碼會具現化**編輯**並**完成**按鈕，以啟用或停用編輯模式，當它們接觸 lambda:

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

此資料列插入 UI 模式不常使用，不過您也可以使用`UITableView.BeginUpdates`和`EndUpdates`以動畫顯示的插入或移除任何資料表中的資料格的方法。 使用這些方法的規則是所傳回值的差異`RowsInSection`之間`BeginUpdates`並`EndUpdates`的呼叫必須符合與新增/刪除的資料格數目 net`InsertRows`和`DeleteRows`方法。 如果基礎資料來源不會變更為符合插入/刪除資料表檢視上就會發生錯誤。


## <a name="related-links"></a>相關連結

- [WorkingWithTables （範例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
