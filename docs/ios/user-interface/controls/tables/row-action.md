---
title: 在 Xamarin 中使用資料列動作
description: 本指南示範如何使用 UISwipeActionsConfiguration 或 UITableViewRowAction 建立資料表資料列的自訂滑動動作
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 2cb453996a43d1e70f4fb818c86f6215c213b988
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429738"
---
# <a name="working-with-row-actions-in-xamarinios"></a>在 Xamarin 中使用資料列動作

_本指南示範如何使用 UISwipeActionsConfiguration 或 UITableViewRowAction 建立資料表資料列的自訂滑動動作_

![示範資料列的滑動動作](row-action-images/action02.png)

iOS 提供兩種方式來對資料表執行動作： `UISwipeActionsConfiguration` 和 `UITableViewRowAction` 。

`UISwipeActionsConfiguration` 是在 iOS 11 中引進的，用來定義當使用者在資料表視圖中的資料列上撥動 _任何方向_ 時，應進行的一組動作。 此行為類似于原生的 Mail 應用程式。

`UITableViewRowAction`類別是用來定義當使用者在資料表視圖中以水準方式撥動資料列時，將會發生的動作。
例如，在編輯資料表時，根據預設，輕輕的資料列會顯示 [ **刪除** ] 按鈕。 藉由將多個類別實例附加 `UITableViewRowAction` 至 `UITableView` ，可以定義多個自訂動作，每個動作都有自己的文字、格式和行為。

## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

使用下列各項來執行滑動動作需要執行三個步驟 `UISwipeActionsConfiguration` ：

1. 覆寫 `GetLeadingSwipeActionsConfiguration` 和/或 `GetTrailingSwipeActionsConfiguration` 方法。 這些方法會傳回 `UISwipeActionsConfiguration` 。
2. 具現化 `UISwipeActionsConfiguration` 要傳回的。 這個類別會採用的陣列 `UIContextualAction` 。
3. 建立 `UIContextualAction`。

這些將在下列各節中更詳細地說明。

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. 執行 SwipeActionsConfigurations 方法

`UITableViewController` (以及 `UITableViewSource` `UITableViewDelegate`) 包含兩種方法： `GetLeadingSwipeActionsConfiguration` 和 `GetTrailingSwipeActionsConfiguration` ，用於在資料表視圖資料列上執行一組滑動動作。 前置的滑動動作是指從畫面的左側以左至右的語言，從畫面的右邊開始滑動，從右至左的語言開始。

下列範例 ([TableSwipeActions](/samples/xamarin/ios-samples/tableswipeactions) 範例中) 示範如何執行前置的滑動設定。 從內容相關的動作建立兩個動作[，如下所述。](#create-uicontextualaction) 這些動作會接著傳遞至新初始化的 [`UISwipeActionsConfiguration`](#create-uiswipeactionsconfigurations) ，用來作為傳回值。

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

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. 具現化 `UISwipeActionsConfiguration`

`UISwipeActionsConfiguration`使用 `FromActions` 方法來加入的新陣列，以具現化 `UIContextualAction` ，如下列程式碼片段所示：

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

請務必注意，您的動作顯示順序取決於如何將其傳遞至您的陣列。 例如，上述的前置撥動程式碼會顯示如下的動作：

![表格資料列上顯示的前置滑動動作](row-action-images/action03.png)

針對尾端的撥動，將會顯示這些動作，如下圖所示：

![資料表資料列上顯示的尾端滑動動作](row-action-images/action04.png)

此程式碼片段也會利用新的 `PerformsFirstActionWithFullSwipe` 屬性。 根據預設，這個屬性會設定為 true，這表示當使用者完全撥動資料列時，就會發生陣列中的第一個動作。 如果您有不具破壞性的動作 (例如「刪除」，這可能不是理想的行為，因此您應該將它設定為 `false` 。

<a name="create-uicontextualaction"></a>

### <a name="create-a-uicontextualaction"></a>建立 `UIContextualAction`

內容相關的動作是您實際上建立在使用者撥動資料表資料列時，將顯示的動作。

若要初始化動作，您必須提供 `UIContextualActionStyle` 、標題和 `UIContextualActionHandler` 。 `UIContextualActionHandler`會採用三個參數：動作、動作顯示所在的視圖，以及完成處理常式：

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

您可以編輯各種視覺屬性，例如動作的背景色彩或影像。 上述程式碼片段示範如何將影像新增至動作，並將其背景色彩設定為藍色。

一旦建立內容相關的動作之後，就可以使用初始化 `UISwipeActionsConfiguration` 方法中的 `GetLeadingSwipeActionsConfiguration` 。

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

靜態 `UITableViewRowAction.Create` 方法是用來建立新 `UITableViewRowAction` 的，當使用者在資料表中的資料列上撥動水準時，會顯示 [ **Hi** ] 按鈕。 稍後會建立的新實例 `TableDelegate` ，並將其附加至 `UITableView` 。 例如：

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

執行上述程式碼，並將使用者撥動到資料表資料列時，就會顯示 [ **Hi** ] 按鈕，而不是預設顯示的 [ **刪除** ] 按鈕：

[![顯示的是 [Hi] 按鈕，而不是 [刪除] 按鈕](row-action-images/action01.png)](row-action-images/action01.png#lightbox)

當使用者按下 [ **Hi** ] 按鈕 `Hello World!` 時，將會在 Visual Studio for Mac 中寫出到主控台，或在應用程式以偵錯工具模式執行時 Visual Studio。

## <a name="related-links"></a>相關連結

- [TableSwipeActions (範例) ](/samples/xamarin/ios-samples/tableswipeactions)
- [WorkingWithTables (範例) ](/samples/xamarin/ios-samples/workingwithtables)