---
title: 使用在 Xamarin.iOS 中的資料列動作
description: 本指南示範如何使用 UISwipeActionsConfiguration 或 UITableViewRowAction 建立資料表的資料列的自訂的撥動動作
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/25/2017
ms.openlocfilehash: 6d41f37d4a63db710bb04e35e6e1a4be0dd4f7a4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408221"
---
# <a name="working-with-row-actions-in-xamarinios"></a>使用在 Xamarin.iOS 中的資料列動作

_本指南示範如何使用 UISwipeActionsConfiguration 或 UITableViewRowAction 建立資料表的資料列的自訂的撥動動作_

![示範在資料列上的撥動動作](row-action-images/action02.png)

iOS 提供兩種方式可在資料表上執行動作：`UISwipeActionsConfiguration`和`UITableViewRowAction`。

`UISwipeActionsConfiguration` 引進了 iOS 11 中，用來定義一組應採取的動作發生使用者 swipes_任一方向_資料表檢視中的資料列。 此行為會類似於原生 Mail.app 

`UITableViewRowAction`類別用來定義使用者 swipes 左水平資料表檢視中的資料列時的動作。
例如，當編輯撥動中所剩餘的資料列的資料表會顯示**刪除**預設按鈕。 藉由附加多個執行個體`UITableViewRowAction`類別來`UITableView`，可以定義多個自訂動作，每個都有它自己的文字、 格式設定和行為。


## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

有三個步驟，才能實作撥動動作`UISwipeActionsConfiguration`:

1. 覆寫`GetLeadingSwipeActionsConfiguration`及/或`GetTrailingSwipeActionsConfiguration`方法。 這些方法會傳回`UISwipeActionsConfiguration`。 
2. 具現化`UISwipeActionsConfiguration`傳回。 此類別接受陣列`UIContextualAction`。
3. 建立 `UIContextualAction`。

這些會在下列各節更詳細地說明。

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1.實作 SwipeActionsConfigurations 方法

`UITableViewController` (以及`UITableViewSource`並`UITableViewDelegate`) 包含兩個方法：`GetLeadingSwipeActionsConfiguration`和`GetTrailingSwipeActionsConfiguration`，用來在資料表檢視資料列上實作一組的撥動動作。 從左到右的語言中的畫面左邊及從右至左的語言在畫面右手邊，開頭的撥動動作就是指撥動。 

下列範例 (來自[TableSwipeActions](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)範例) 示範如何實作前置的撥動組態。 從 [內容] 動作，會說明建立兩個動作[以下](#create-uicontextualaction)。 這些動作接著會在傳遞至新初始化[ `UISwipeActionsConfiguration` ](#create-uiswipeactionsconfigurations)，可做為傳回值。


```csharp
public override UISwipeActionsConfiguration GetLeadingSwipeActionsConfiguration(UITableView tableView, NSIndexPath indexPath)
{
    //UIContextualActions
    var definitionAction = ContextualDefinitionAction(indexPath.Row);
    var flagAction = ContextualFlagAction(indexPath.Row);

    //UISwipeActionsConfiguration
    var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction });
    
    leadingSwipe.PerformsFirstActionWithFullSwipe = false;
    
    return leadingSwipe;
}  
```

<a name="create-uiswipeactionsconfigurations" />

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2.具現化 `UISwipeActionsConfiguration`

具現化`UISwipeActionsConfiguration`利用`FromActions`方法來加入的新陣列`UIContextualAction`s，如下列程式碼片段所示：

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

請務必請注意，您的動作顯示的順序取決於如何傳遞到您的陣列。 例如，前置 swipes 上面的程式碼顯示的動作，因此：

![領先的撥動動作顯示的資料表資料列](row-action-images/action03.png)

針對尾端 swipes，如下圖所示，將會顯示動作：

![結尾的撥動動作顯示的資料表資料列](row-action-images/action04.png)

此程式碼片段也會利用新`PerformsFirstActionWithFullSwipe`屬性。 根據預設，這個屬性設定為 true，表示使用者 swipes 完整的資料列時，會發生在陣列中的第一個動作。 如果您有不具破壞性動作 (例如 「 刪除 」，這可能不是理想的行為與您因此應該將它設`false`。

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>建立 `UIContextualAction`

內容相關的動作是實際建立使用者 swipes 資料表資料列時，會顯示的動作。

若要初始化的動作，您必須提供`UIContextualActionStyle`、 一個的標題，以及`UIContextualActionHandler`。 `UIContextualActionHandler`採用三個參數： 動作、 動作中，顯示的檢視和完成處理常式：

```csharp
public UIContextualAction ContextualFlagAction(int row)
{
    var action = UIContextualAction.FromContextualActionStyle
                    (UIContextualActionStyle.Normal,
                        "Flag",
                        (FlagAction, view, success) => {
                            var alertController = UIAlertController.Create($"Report {words[row]}?", "", UIAlertControllerStyle.Alert);
                            alertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, null)); 
                            alertController.AddAction(UIAlertAction.Create("Yes", UIAlertActionStyle.Destructive, null));
                            PresentViewController(alertController, true, null);
                            
                            success(true);
                        });

    action.Image = UIImage.FromFile("feedback.png");
    action.BackgroundColor = UIColor.Blue;

    return action;
}
```

各種 visual 屬性的詳細資訊，例如背景色彩或影像的動作可以進行編輯。 上述程式碼片段示範將影像加入到其中的動作，並設定它的背景色彩為藍色。

一旦已建立的內容相關的動作，他們可以使用來初始化`UISwipeActionsConfiguration`在`GetLeadingSwipeActionsConfiguration`方法。

## <a name="uitableviewrowaction"></a>UITableViewRowAction

若要定義一或多個自訂的資料列的動作`UITableView`，您必須建立的執行個體`UITableViewDelegate`類別並覆寫`EditActionsForRow`方法。 例如: 

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using Foundation;
using UIKit;

namespace BasicTable
{
    public class TableDelegate : UITableViewDelegate
    {
        #region Constructors
        public TableDelegate ()
        {
        }

        public TableDelegate (IntPtr handle) : base (handle)
        {
        }

        public TableDelegate (NSObjectFlag t) : base (t)
        {
        }

        #endregion

        #region Override Methods
        public override UITableViewRowAction[] EditActionsForRow (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewRowAction hiButton = UITableViewRowAction.Create (
                UITableViewRowActionStyle.Default,
                "Hi",
                delegate {
                    Console.WriteLine ("Hello World!");
                });
            return new UITableViewRowAction[] { hiButton };
        }
        #endregion
    }
}
```

靜態`UITableViewRowAction.Create`方法用來建立新`UITableViewRowAction`將顯示**Hi**按鈕使用者 swipes 保留水平資料表中的資料列時。 更新版本的新執行個體`TableDelegate`建立並附加至`UITableView`。 例如：

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

執行上述的程式碼並將使用者 swipes 留在資料表資料列中，當**Hi**  按鈕將會顯示，而不是**刪除**預設會顯示的按鈕：

[![](row-action-images/action01.png "正在顯示而不是 [刪除] 按鈕的 [Hi] 按鈕")](row-action-images/action01.png#lightbox)

如果在使用者點選**Hi**  按鈕，`Hello World!`會寫出至主控台，在 Visual Studio for Mac 或 Visual Studio 偵錯模式中執行應用程式時。



## <a name="related-links"></a>相關連結

- [TableSwipeActions （範例）](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)
- [WorkingWithTables （範例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
