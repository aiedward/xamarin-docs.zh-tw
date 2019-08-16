---
title: 在 iOS 設計工具中使用表格
description: 在先前的章節中, 我們探討了使用資料表進行開發。 在這第五個和最後一節中, 我們將匯總我們到目前為止所學到的內容, 並使用分鏡腳本建立一個基本的工作清單應用程式。
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 46729df70d08b8d6d1b5b953d74f5619a5dc5858
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528681"
---
# <a name="working-with-tables-in-the-ios-designer"></a>在 iOS 設計工具中使用表格

分鏡腳本是一種建立 iOS 應用程式的 WYSIWYG 方式, 而且在 Mac 和 Windows 的 Visual Studio 內都受到支援。 如需有關分鏡腳本的詳細資訊, 請參閱分鏡腳本[簡介](~/ios/user-interface/storyboards/index.md)檔。 分鏡腳本也可以讓您編輯資料表*中*的資料格版面配置, 以簡化使用資料表和資料格的開發工作

在 iOS 設計工具中設定資料表視圖的屬性時, 有兩種類型的資料格內容可供您選擇:**動態**或**靜態**原型內容。

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>動態原型內容

具有`UITableView`原型內容的通常是用來顯示清單中每個專案的原型資料格 (如您可以定義一個以上的儲存格)。 資料格不需要具現化, 而是在`GetView`方法中藉由`DequeueReusableCell`呼叫其`UITableViewSource`的方法取得。

 <a name="Static_Content" />


## <a name="static-content"></a>靜態內容

`UITableView`具有靜態內容的可以在設計介面上直接設計資料表。 資料格可以拖曳到資料表中, 並藉由變更屬性和加入控制項來自訂。

 <a name="Creating_a_Storyboard-driven_app" />


## <a name="creating-a-storyboard-driven-app"></a>建立分鏡腳本驅動的應用程式

StoryboardTable 範例包含一個簡單的主版詳細資料應用程式, 它會在腳本中使用這兩種類型的 UITableView。 本節的其餘部分將說明如何建立一個小型待辦事項清單範例, 這在完成時看起來會像這樣:

 [![範例畫面](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

使用者介面會以分鏡腳本建立, 而這兩個畫面都會使用 UITableView。 主畫面會使用*原型內容*來配置資料列, 而詳細資料畫面會使用*靜態內容*來建立使用自訂儲存格版面配置的資料輸入表單。

## <a name="walkthrough"></a>逐步解說

使用 **(建立) 新專案, 在 Visual Studio 中建立新的方案 。> 單一視圖應用程式C#()** , 並呼叫它_StoryboardTables_。

 [![[建立新專案] 對話方塊](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

方案將會開啟, 其中C#包含一些檔案`Main.storyboard`和已建立的檔案。 `Main.storyboard`按兩下檔案, 在 iOS 設計工具中開啟檔案。

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>修改分鏡腳本

分鏡腳本會以三個步驟進行編輯:

- 首先, 配置必要的視圖控制器並設定其屬性。
- 第二, 將物件拖放到您的視圖上, 以建立您的 UI
- 最後, 將必要的 UIKit 類別新增至每個視圖, 並提供各種控制項的名稱, 以便在程式碼中參考它們。


完成腳本之後, 您可以加入程式碼, 讓所有專案都能順利進行。

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>版面配置視圖控制器

腳本的第一次變更是刪除現有的詳細資料檢視, 並以 UITableViewController 取代它。 請遵循下列步驟：

1. 選取視圖控制器底部的列, 然後將它刪除。
2. 從 [工具箱] 將 [**流覽控制器**] 和 [**資料表視圖控制器**] 拖曳至分鏡腳本。 
3. 從根視圖控制器建立 segue 到剛才新增的第二個數據表視圖控制器。 若要建立 segue, 請*從 [詳細資料] 儲存格*控制 + 拖曳至新加入的 UITableViewController。 選擇 [ **Segue**選項] 底下的 [**顯示**] 選項。 
4. 選取您建立的新 segue, 並為它提供一個識別碼, 以在程式碼中參考此 segue。 按一下 segue, 然後`TaskSegue`在  **Properties Pad**中輸入作為**識別碼**, 如下所示:    
  [![在屬性面板中命名 segue](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. 接下來, 選取兩個數據表, 並使用 Properties Pad 來進行設定。 請務必選取 [View, not View Controller] –您可以使用 [檔大綱] 來協助選取。

6. 將根視圖控制器變更為**內容:動態原型** (Design Surface 上的視圖將會標示為**原型內容**):

    [![將內容屬性設定為動態原型](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7. 將新的**UITableViewController**變更為**Content:靜態資料**格。 


8. 新的 UITableViewController 必須設定其類別名稱和識別碼。 選取 視圖控制器, 然後在  **Properties Pad**中輸入**類別**的_TaskDetailViewController_ –這會在`TaskDetailViewController.cs` Solution Pad 中建立新的檔案。 輸入**StoryboardID**做為_詳細資料_, 如下列範例所示。 稍後會用來在程式碼中C#載入此視圖:  

    [![設定分鏡腳本識別碼](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. 分鏡腳本設計介面現在看起來應該像這樣 (根視圖控制器的導覽專案標題已變更為「繁瑣的面板」):

    [![設計介面](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  



<a name="Create_the_UI" />

### <a name="create-the-ui"></a>建立 UI

現在已設定 views 和 segue, 必須加入使用者介面元素。

#### <a name="root-view-controller"></a>根檢視控制器

首先, 選取主要視圖控制器中的 [原型] 資料格, 並將**識別碼**設定為_taskcell_, 如下所示。 稍後在程式碼中會用來抓取此 UITableViewCell 的實例:

 [![設定資料格識別碼](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

接下來, 您必須建立一個會加入新工作的按鈕, 如下所示:

[![巡覽列中的橫條按鈕專案](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

請執行下列動作： 

- 將 [**橫條] 按鈕專案**從 [工具箱] 拖曳至導覽列的_右邊_。
- 在 **Properties Pad** 下方 **列按鈕項目** 選取 **識別碼：新增** (以便 *+* 加號按鈕)。 
- 為它命名, 以便在稍後的程式碼中識別。 請注意, 您將需要為根視圖控制器提供類別名稱 (例如**ItemViewController**), 以允許您設定橫條按鈕專案的名稱。


#### <a name="taskdetail-view-controller"></a>TaskDetail View 控制器

詳細資料檢視需要執行更多工作。 資料表視圖儲存格必須拖曳到此視圖上, 然後以標籤、文字視圖和按鈕填入。 下列螢幕擷取畫面顯示完成的 UI, 其中包含兩個區段。 一個區段有三個數據格、三個標籤、兩個文字欄位和一個參數, 而第二個區段有一個具有兩個按鈕的資料格:

 [![詳細資料檢視版面配置](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

建立完整版面配置的步驟如下:

選取 [資料表] 視圖並開啟 [**屬性] 面板**。 更新下列屬性:

- **區段**:_2_ 
- **樣式**:_歸入_
- **分隔符號**:_無_
- **選取範圍**:_沒有選取專案_

選取頂端區段, 然後在 **屬性 > 資料表視圖 區段**中, 將資料**列**變更為_3_, 如下所示:


 [![將頂端區段設定為三個數據列](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

針對每個資料格, 開啟**Properties Pad**並設定:

- **樣式**:_自訂_
- **識別碼**:為每個資料格選擇唯一的識別碼 (例如 「_標題_」、「_附注_」、「_完成_」)。
- 拖曳所需的控制項, 以產生螢幕擷取畫面中顯示的配置 (將**UILabel**、 **UITextField**和**UISwitch**放在正確的資料格上, 並適當地設定標籤, 例如。標題、附注和完成)。


在第二個區段中, 將資料**列**設定為_1_ , 並抓取資料格的底部調整大小控點, 使其更高。

- **將識別碼: 設定**為唯一值 (例如 [儲存])。 
- **設定背景**:_清除色彩_。
- 將兩個按鈕拖曳到資料格上, 並適當地設定其標題 (即 [_儲存_] 和 [_刪除_]), 如下所示:

   [![在下一節中設定兩個按鈕](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

此時, 您可能也會想要在儲存格和控制項上設定條件約束, 以確保可調整的版面配置。

### <a name="adding-uikit-class-and-naming-controls"></a>加入 UIKit 類別和命名控制項

建立腳本的最後幾個步驟。 首先, 我們必須為每個控制項提供一個 [身分識別] 底下的名稱 **> 名稱**, 以便稍後在程式碼中使用。 將這些名稱命名如下:

- **標題 UITextField** :_TitleText_
- **附注 UITextField** :_NotesText_
- **UISwitch** :_DoneSwitch_
- **刪除 UIButton** :_DeleteButton_
- **儲存 UIButton** :_SaveButton_


<a name="Adding_Code" />

## <a name="adding-code"></a>加入程式碼

其餘的工作將會在 Mac 或 Windows 上的 Visual Studio 中, 使用C#來完成。 請注意, 程式碼中使用的屬性名稱會反映上述逐步解說中的設定。

首先我們要建立一個`Chores`類別, 它會提供一種方法來取得和設定識別碼、名稱、附注和完成的布林值, 讓我們可以在整個應用程式中使用這些值。

在您`Chores`的類別中, 新增下列程式碼:

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

接下來, 建立`RootTableSource`繼承自`UITableViewSource`的類別。 

這個範例和非分鏡腳本資料表的觀點`GetView` , 在於方法不需要具現化任何資料格– `theDequeueReusableCell`方法一律會傳回原型資料格的實例 (具有相符的識別碼)。

以下是來自`RootTableSource.cs`檔案的程式碼:

```csharp
public class RootTableSource : UITableViewSource
{
// there is NO database or storage of Tasks in this example, just an in-memory List<>
Chores[] tableItems;
string cellIdentifier = "taskcell"; // set in the Storyboard

    public RootTableSource(Chores[] items)
    {
        tableItems = items;
    }

public override nint RowsInSection(UITableView tableview, nint section)
{
  return tableItems.Length;
}

public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
  // in a Storyboard, Dequeue will ALWAYS return a cell, 
  var cell = tableView.DequeueReusableCell(cellIdentifier);
  // now set the properties as normal
  cell.TextLabel.Text = tableItems[indexPath.Row].Name;
  if (tableItems[indexPath.Row].Done)
    cell.Accessory = UITableViewCellAccessory.Checkmark;
  else
    cell.Accessory = UITableViewCellAccessory.None;
  return cell;
}
public Chores GetItem(int id)
{
  return tableItems[id];
}
```

若要使用`RootTableSource`類別, 請`ItemViewController`在的函式中建立新集合:

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

在`ViewWillAppear`中, 將集合傳遞至來源並指派給資料表視圖:

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

如果您現在執行應用程式, 主畫面現在會載入並顯示兩個工作的清單。 觸及腳本所定義的 segue 時, 將會顯示詳細資料畫面, 但目前不會顯示任何資料。

若要在 segue 中「傳送參數」, 請覆`PrepareForSegue`寫方法並`DestinationViewController`在上設定屬性 ( `TaskDetailViewController`在此範例中為)。 目的地視圖控制器類別會具現化, 但尚未向使用者顯示–這表示您可以設定類別的屬性, 但不能修改任何 UI 控制項:

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
    {
      if (segue.Identifier == "TaskSegue") { // set in Storyboard
        var navctlr = segue.DestinationViewController as TaskDetailViewController;
        if (navctlr != null) {
          var source = TableView.Source as RootTableSource;
          var rowPath = TableView.IndexPathForSelectedRow;
          var item = source.GetItem(rowPath.Row);
          navctlr.SetTask (this, item); // to be defined on the TaskDetailViewController
        }
      }
    }
```

在`TaskDetailViewController`方法中, 會將其參數指派給屬性, 以便在 viewwillappear: 每當中參考它們。 `SetTask` 無法在中`SetTask`修改控制項屬性, 因為當呼叫時`PrepareForSegue` , 可能不存在:

```csharp
Chore currentTask {get;set;}
    public ItemViewController Delegate {get;set;} // will be used to Save, Delete later

public override void ViewWillAppear (bool animated)
    {
      base.ViewWillAppear (animated);
      TitleText.Text = currentTask.Name;
      NotesText.Text = currentTask.Notes;
      DoneSwitch.On = currentTask.Done;
    }

    // this will be called before the view is displayed
    public void SetTask (ItemViewController d, Chore task) {
      Delegate = d;
      currentTask = task;
    }
```

Segue 現在會開啟詳細資料畫面, 並顯示選取的工作資訊。 可惜的是, 沒有 [**儲存**] 和 [**刪除**] 按鈕的執行。 在執行按鈕之前, 請將下列方法新增至**ItemViewController.cs** , 以更新基礎資料並關閉詳細資料畫面:

```csharp
public void SaveTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
        NavigationController.PopViewController(true);
}

public void DeleteTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
  chores.Remove(oldTask);
        NavigationController.PopViewController(true);
}
```

接下來, 您必須將按鈕的`TouchUpInside`事件處理常式新增至**TaskDetailViewController.cs**的`ViewDidLoad`方法。 已明確建立的`ItemViewController` `SaveTask` `DeleteTask`屬性參考, 因此我們可以呼叫和, 這會在其作業中關閉此視圖: `Delegate`

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

最後一項要建立的功能就是建立新的工作。 在**ItemViewController.cs**中, 新增建立新工作的方法, 並開啟詳細資料檢視。 若要從分鏡腳本具現化`InstantiateViewController`視圖, 請`Identifier`使用方法搭配該視圖的, 在此範例中會是「詳細資料」:

```csharp
public void CreateTask () 
    {
      // first, add the task to the underlying data
      var newId = chores[chores.Count - 1].Id + 1;
      var newChore = new Chore{Id = newId};
      chores.Add (newChore);

      // then open the detail view to edit it
      var detail = Storyboard.InstantiateViewController("detail") as TaskDetailViewController;
      detail.SetTask (this, newChore);
      NavigationController.PushViewController (detail, true);
    }
```

最後, 在**ItemViewController.cs**的`ViewDidLoad`方法的導覽列中, 連上按鈕來呼叫它:

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

這會完成分鏡腳本範例–完成的應用程式看起來像這樣:

[![完成的應用程式](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

此範例示範:

- 建立具有原型內容的資料表, 其中的資料格會定義為重複使用來顯示資料清單。 
- 建立具有靜態內容的資料表來建立輸入表單。 這包括變更資料表樣式, 以及新增區段、儲存格和 UI 控制項。 
- 如何建立 segue 並覆寫`PrepareForSegue`方法, 以通知目標視圖其所需的任何參數。 
- 使用`Storyboard.InstantiateViewController`方法直接載入分鏡腳本視圖。



## <a name="related-links"></a>相關連結

- [StoryboardTable (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable)
- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
