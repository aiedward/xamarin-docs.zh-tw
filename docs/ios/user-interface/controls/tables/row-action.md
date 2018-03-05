---
title: "使用資料列的動作"
description: "本指南示範如何建立自訂撥動動作為資料表資料列與 UISwipeActionsConfiguration 或 UITableViewRowAction"
ms.topic: article
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/25/2017
ms.openlocfilehash: e9d3e2eecd4c03e7b3046e1ad86dd8a0d70a7f73
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-row-actions"></a>使用資料列的動作

_本指南示範如何建立自訂撥動動作為資料表資料列與 UISwipeActionsConfiguration 或 UITableViewRowAction_

![示範撥動動作上的資料列](row-action-images/action02.png)

iOS 提供兩種方式在資料表上執行動作：`UISwipeActionsConfiguration`和`UITableViewRowAction`。

`UISwipeActionsConfiguration` iOS 11 中引進，而用以定義一組應該採取的動作放置時使用者 swipes_任一方向_資料表檢視中的資料列。 此行為是類似於原生 Mail.app 

`UITableViewRowAction`類別用來定義當使用者 swipes 水平剩餘的資料表檢視中的資料列會進行的動作。
例如，當編輯撥動中所剩餘的資料列的資料表會顯示**刪除**預設按鈕。 藉由附加多個執行個體`UITableViewRowAction`類別`UITableView`，可以定義多個自訂動作，每個都有它自己的文字、 格式設定和行為。


## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

有三個步驟，才能實作撥動動作`UISwipeActionsConfiguration`:

1. 覆寫`GetLeadingSwipeActionsConfiguration`及/或`GetTrailingSwipeActionsConfiguration`方法。 這些方法會傳回`UISwipeActionsConfiguration`。 
2. 具現化`UISwipeActionsConfiguration`傳回。 這個類別會取得陣列的`UIContextualAction`。
3. 建立 `UIContextualAction`。

下列各節將詳細說明這些。

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1.實作 SwipeActionsConfigurations 方法

`UITableViewController` (以及`UITableViewSource`和`UITableViewDelegate`) 包含兩個方法：`GetLeadingSwipeActionsConfiguration`和`GetTrailingSwipeActionsConfiguration`，可用來在資料表檢視資料列上實作一組撥動動作。 從左到右語言螢幕左邊和以由右至左語言螢幕右邊撥動參考前置撥動動作。 

下列範例 (從[TableSwipeActions](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)範例) 示範如何實作前置撥動組態。 從 [內容] 動作，說明建立兩個動作[下方](#create-uicontextualaction)。 這些動作接著會在傳遞至新初始化[ `UISwipeActionsConfiguration` ](#create-uiswipeactionsconfigurations)，用做為傳回值。


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

具現化`UISwipeActionsConfiguration`使用`FromActions`方法，將加入的新陣列`UIContextualAction`s，如下列程式碼片段所示：

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

請務必注意您的動作顯示的順序是取決於如何傳遞到您的陣列。 例如，前置 swipes 上方的程式碼顯示的動作，因此：

![前置撥動動作顯示在資料表資料列](row-action-images/action03.png)

針對尾端 swipes，動作會顯示在下圖所示：

![尾端撥動動作顯示在資料表資料列](row-action-images/action04.png)

此程式碼片段也會使用新的`PerformsFirstActionWithFullSwipe`屬性。 根據預設，這個屬性設定為 true，表示使用者 swipes 完整的資料列時，會發生在陣列中的第一個動作。 如果您有不是破壞性動作 (例如 「 刪除 」，這可能不是理想的行為與您因此應該將它設`false`。

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>建立 `UIContextualAction`

此內容相關的動作為實際建立會在使用者 swipes 資料表資料列時顯示的動作。

若要初始化的動作，您必須提供`UIContextualActionStyle`、 標題和`UIContextualActionHandler`。 `UIContextualActionHandler`接受三個參數： 動作，即檢視中，顯示動作和完成處理常式：

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

可以編輯各種視覺內容，例如背景色彩或影像的動作。 上述程式碼片段示範將影像加入到其中的動作，並設定它的背景色彩為藍色。

一旦已建立的內容相關的動作，他們可以使用來初始化`UISwipeActionsConfiguration`中`GetLeadingSwipeActionsConfiguration`方法。

## <a name="uitableviewrowaction"></a>UITableViewRowAction

若要定義一個或多個自訂的資料列動作`UITableView`，您必須建立的執行個體`UITableViewDelegate`類別並覆寫`EditActionsForRow`方法。 例如: 

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

靜態`UITableViewRowAction.Create`方法用來建立新`UITableViewRowAction`將顯示**Hi**按鈕時使用者 swipes 剩餘的資料表中的資料列的水平。 更新版本的新執行個體`TableDelegate`會建立並附加至`UITableView`。 例如: 

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

當執行上述程式碼，且使用者 swipes 剩餘的資料表資料列， **Hi**按鈕將會顯示，而不是**刪除**預設會顯示的按鈕：

[ ![](row-action-images/action01.png "而不是 [刪除] 按鈕顯示 [Hi] 按鈕")](row-action-images/action01.png)

如果在使用者點選**Hi**  按鈕，`Hello World!`會寫出至 Visual Studio 中的主控台 Mac 或 Visual Studio 中偵錯模式執行應用程式時。



## <a name="related-links"></a>相關連結

- [TableSwipeActions （範例）](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)
- [WorkingWithTables （範例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
