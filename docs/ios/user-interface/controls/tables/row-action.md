---
title: 在 Xamarin 中使用資料列動作
description: 本指南示範如何使用 UISwipeActionsConfiguration 或 UITableViewRowAction，為數據表資料列建立自訂的滑動動作
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: cc83fa65fd040b30d71a30fb703da866c5d0824c
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570813"
---
# <a name="working-with-row-actions-in-xamarinios"></a>在 Xamarin 中使用資料列動作

_本指南示範如何使用 UISwipeActionsConfiguration 或 UITableViewRowAction，為數據表資料列建立自訂的滑動動作_

![在資料列上示範滑動動作](row-action-images/action02.png)

iOS 提供兩種在資料表上執行動作的方式： `UISwipeActionsConfiguration` 和 `UITableViewRowAction` 。

`UISwipeActionsConfiguration`是在 iOS 11 中引進，而且可用來定義一組動作，當使用者在資料表視圖中的資料列上撥動_任一方向_時，就會發生此情況。 此行為類似于原生郵件應用程式。

`UITableViewRowAction`類別是用來定義當使用者在資料表視圖中的資料列上水準撥動時，將會發生的動作。
例如，編輯資料表時，根據預設，資料列上的向左輕刷會顯示 [**刪除**] 按鈕。 藉由將類別的多個實例附加 `UITableViewRowAction` 至 `UITableView` ，可以定義多個自訂動作，每個都有自己的文字、格式和行為。

## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

使用執行輕刷動作需要三個步驟 `UISwipeActionsConfiguration` ：

1. 覆寫 `GetLeadingSwipeActionsConfiguration` 和/或 `GetTrailingSwipeActionsConfiguration` 方法。 這些方法會傳回 `UISwipeActionsConfiguration` 。
2. 具現化 `UISwipeActionsConfiguration` 要傳回的。 這個類別會接受的陣列 `UIContextualAction` 。
3. 建立 `UIContextualAction`。

下列各節會更詳細地說明這些功能。

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. 執行 SwipeActionsConfigurations 方法

`UITableViewController`（和 `UITableViewSource` 和 `UITableViewDelegate` ）包含兩個方法： `GetLeadingSwipeActionsConfiguration` 和 `GetTrailingSwipeActionsConfiguration` ，用來在資料表視圖資料列上執行一組滑動動作。 前置的「滑動」動作指的是從畫面左側以左至右的語言，以及從右至左的語言中，從畫面的右手邊滑動。

下列範例（來自[TableSwipeActions](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions)範例）示範如何執行領先的滑動設定。 從內容相關的動作中會建立兩個動作，[如下](#create-uicontextualaction)所述。 這些動作接著會傳遞至新初始化的 [`UISwipeActionsConfiguration`](#create-uiswipeactionsconfigurations) ，做為傳回值使用。

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

<a name="create-uiswipeactionsconfigurations"></a>

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. 具現化`UISwipeActionsConfiguration`

`UISwipeActionsConfiguration`使用 `FromActions` 方法來加入的新陣列，以具現化 `UIContextualAction` ，如下列程式碼片段所示：

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

請務必注意，動作顯示的順序取決於如何將其傳入您的陣列。 例如，上述主要撥動的程式碼會顯示動作，如下所示：

![在資料表資料列上顯示的前置滑動動作](row-action-images/action03.png)

對於尾端的撥動，將會顯示如下圖所示的動作：

![資料表資料列上顯示的尾端滑動動作](row-action-images/action04.png)

此程式碼片段也會使用新的 `PerformsFirstActionWithFullSwipe` 屬性。 根據預設，這個屬性會設定為 true，這表示當使用者撥動完整的資料列時，陣列中的第一個動作就會發生。 如果您有不具破壞性的動作（例如 "Delete"），這可能不是理想的行為，因此您應該將它設定為 `false` 。

<a name="create-uicontextualaction"></a>

### <a name="create-a-uicontextualaction"></a>建立 `UIContextualAction`

內容相關的動作是您實際建立動作的位置，當使用者撥動資料表資料列時，就會顯示此動作。

若要初始化動作，您必須提供 `UIContextualActionStyle` 、標題和 `UIContextualActionHandler` 。 `UIContextualActionHandler`會採用三個參數：動作、顯示動作的視圖，以及完成處理常式：

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

建立內容動作之後，他們就可以使用來初始化 `UISwipeActionsConfiguration` 方法中的 `GetLeadingSwipeActionsConfiguration` 。

## <a name="uitableviewrowaction"></a>UITableViewRowAction

若要定義的一或多個自訂資料列動作 `UITableView` ，您必須建立類別的實例 `UITableViewDelegate` ，並覆寫 `EditActionsForRow` 方法。 例如：

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

靜態 `UITableViewRowAction.Create` 方法是用來建立新 `UITableViewRowAction` 的，當使用者在資料表中的資料列上水準撥動時，會顯示 [ **Hi** ] 按鈕。 之後，會建立新的實例 `TableDelegate` ，並將其附加至 `UITableView` 。 例如：

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

當上述程式碼執行且使用者撥動在資料表資料列時，將會顯示 [ **Hi** ] 按鈕，而不是預設顯示的 [**刪除**] 按鈕：

[![](row-action-images/action01.png "The Hi button being displayed instead of the Delete button")](row-action-images/action01.png#lightbox)

如果使用者按下 [ **Hi** ] 按鈕，則 `Hello World!` 會在應用程式于 [debug] 模式中執行時，在 Visual Studio for Mac 或 Visual Studio 中寫出至主控台。

## <a name="related-links"></a>相關連結

- [TableSwipeActions （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions)
- [WorkingWithTables （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
