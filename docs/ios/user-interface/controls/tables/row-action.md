---
title: 在 Xamarin 中使用資料列動作
description: 本指南示範如何使用 UISwipeActionsConfiguration 或 UITableViewRowAction，為數據表資料列建立自訂的滑動動作
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/25/2017
ms.openlocfilehash: 9f15d586a0ca209fec088fc48ca975efae4ab8fc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768356"
---
# <a name="working-with-row-actions-in-xamarinios"></a>在 Xamarin 中使用資料列動作

_本指南示範如何使用 UISwipeActionsConfiguration 或 UITableViewRowAction，為數據表資料列建立自訂的滑動動作_

![在資料列上示範滑動動作](row-action-images/action02.png)

iOS 提供兩種在資料表上執行動作的方式`UISwipeActionsConfiguration` ： `UITableViewRowAction`和。

`UISwipeActionsConfiguration`是在 iOS 11 中引進，而且可用來定義一組動作，當使用者在資料表視圖中的資料列上撥動_任一方向_時，就會發生此情況。 此行為類似于原生郵件應用程式。

`UITableViewRowAction`類別是用來定義當使用者在資料表視圖中的資料列上水準撥動時，將會發生的動作。
例如，編輯資料表時，根據預設，資料列上的向左輕刷會顯示 [**刪除**] 按鈕。 藉由將`UITableViewRowAction`類別的多個實例附加`UITableView`至，可以定義多個自訂動作，每個都有自己的文字、格式和行為。

## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

使用`UISwipeActionsConfiguration`執行輕刷動作需要三個步驟：

1. 覆`GetLeadingSwipeActionsConfiguration`寫和/ `GetTrailingSwipeActionsConfiguration`或方法。 這些方法`UISwipeActionsConfiguration`會傳回。
2. 具現`UISwipeActionsConfiguration`化要傳回的。 這個類別會接受的陣列`UIContextualAction`。
3. 建立 `UIContextualAction`。

下列各節會更詳細地說明這些功能。

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1.執行 SwipeActionsConfigurations 方法

`UITableViewController`（和`UITableViewSource`和`UITableViewDelegate`）包含兩個方法： `GetLeadingSwipeActionsConfiguration`和`GetTrailingSwipeActionsConfiguration`，用來在資料表視圖資料列上執行一組滑動動作。 前置的「滑動」動作指的是從畫面左側以左至右的語言，以及從右至左的語言中，從畫面的右手邊滑動。

下列範例（來自[TableSwipeActions](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions)範例）示範如何執行領先的滑動設定。 從內容相關的動作中會建立兩個動作，[如下](#create-uicontextualaction)所述。 這些動作接著會傳遞至新初始化[`UISwipeActionsConfiguration`](#create-uiswipeactionsconfigurations)的，做為傳回值使用。

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

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2.具現化`UISwipeActionsConfiguration`

使用方法來`UIContextualAction`加入的新陣列，以具現化，如下列程式碼片段所示： `FromActions` `UISwipeActionsConfiguration`

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

請務必注意，動作顯示的順序取決於如何將其傳入您的陣列。 例如，上述主要撥動的程式碼會顯示動作，如下所示：

![在資料表資料列上顯示的前置滑動動作](row-action-images/action03.png)

對於尾端的撥動，將會顯示如下圖所示的動作：

![資料表資料列上顯示的尾端滑動動作](row-action-images/action04.png)

此程式碼片段也會使用新`PerformsFirstActionWithFullSwipe`的屬性。 根據預設，這個屬性會設定為 true，這表示當使用者撥動完整的資料列時，陣列中的第一個動作就會發生。 如果您有不具破壞性的動作（例如 "Delete"），這可能不是理想的行為，因此您應該將它`false`設定為。

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>建立`UIContextualAction`

內容相關的動作是您實際建立動作的位置，當使用者撥動資料表資料列時，就會顯示此動作。

若要初始化動作，您必須提供`UIContextualActionStyle`、標題`UIContextualActionHandler`和。 `UIContextualActionHandler`會採用三個參數：動作、顯示動作的視圖，以及完成處理常式：

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

您可以編輯各種視覺屬性，例如背景色彩或動作影像。 上述程式碼片段示範如何將影像加入至動作，並將其背景色彩設定為藍色。

建立內容動作之後，他們就可以使用來初始化`UISwipeActionsConfiguration` `GetLeadingSwipeActionsConfiguration`方法中的。

## <a name="uitableviewrowaction"></a>UITableViewRowAction

若要定義的`UITableView`一或多個自訂資料列動作，您必須建立`UITableViewDelegate`類別的實例，並覆寫`EditActionsForRow`方法。 例如：

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

靜態`UITableViewRowAction.Create`方法是用來建立新`UITableViewRowAction`的，當使用者在資料表中的資料列上水準撥動時，會顯示 [ **Hi** ] 按鈕。 之後， `TableDelegate`會建立新的實例，並將其附加`UITableView`至。 例如：

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

當上述程式碼執行且使用者撥動在資料表資料列時，將會顯示 [ **Hi** ] 按鈕，而不是預設顯示的 [**刪除**] 按鈕：

[![](row-action-images/action01.png "顯示的是 [Hi] 按鈕，而不是 [刪除] 按鈕")](row-action-images/action01.png#lightbox)

如果使用者按下 [ **Hi** ] 按鈕`Hello World!` ，則會在應用程式于 [debug] 模式中執行時，在 Visual Studio for Mac 或 Visual Studio 中寫出至主控台。

## <a name="related-links"></a>相關連結

- [TableSwipeActions （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions)
- [WorkingWithTables （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
