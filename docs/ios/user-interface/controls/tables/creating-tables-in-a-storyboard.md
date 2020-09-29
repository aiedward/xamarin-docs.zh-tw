---
title: 在 iOS 設計工具中使用表格
description: 在先前的章節中，我們探討了如何使用資料表進行開發。 在這一節的第五個和最後一節中，我們將匯總我們到目前為止所學到的內容，並使用分鏡腳本建立基本的繁瑣清單應用程式。
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 963151c8f5f88373fd6d71a2bb74bd2dbe5d6ab5
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430084"
---
# <a name="working-with-tables-in-the-ios-designer"></a>在 iOS 設計工具中使用表格

分鏡腳本是一種用來建立 iOS 應用程式的 WYSIWYG 方式，在 Mac 和 Windows 的 Visual Studio 內都受到支援。 如需分鏡腳本的詳細資訊，請參閱分鏡指令檔 [簡介](~/ios/user-interface/storyboards/index.md) 。 分鏡腳本也可讓您編輯資料表 *中* 的資料格版面配置，以簡化使用資料表和資料格的開發工作

在 iOS 設計工具中設定資料表視圖的屬性時，您可以選擇兩種類型的資料格內容： **動態** 或 **靜態** 原型內容。

<a name="Prototype_Content"></a>

## <a name="dynamic-prototype-content"></a>動態原型內容

`UITableView`具有原型內容的是，通常是用來顯示原型資料格 (或資料格的資料清單，您可以為清單中的每個專案重複使用一個以上的) 。 儲存格不需要具現化，而是藉 `GetView` 由呼叫其的方法，在方法中取得 `DequeueReusableCell` `UITableViewSource` 。

 <a name="Static_Content"></a>

## <a name="static-content"></a>靜態內容

`UITableView`具有靜態內容的可讓您在設計介面上直接設計資料表。 您可以將資料格拖曳到資料表中，並藉由變更屬性和加入控制項來自訂。

 <a name="Creating_a_Storyboard-driven_app"></a>

## <a name="creating-a-storyboard-driven-app"></a>建立分鏡腳本驅動的應用程式

StoryboardTable 範例包含簡單的主從階層應用程式，它會在分鏡腳本中使用這兩種類型的 Ios uitableview 範例。 本節的其餘部分將說明如何建立在完成時看起來像這樣的小型待辦事項清單範例：

 [![範例畫面](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

使用者介面將以分鏡腳本建立，而這兩個畫面都會使用 Ios uitableview 範例。 主畫面使用 *原型內容* 來配置資料列，而且詳細資料畫面使用 *靜態內容* 來建立使用自訂資料格版面配置的資料輸入表單。

## <a name="walkthrough"></a>逐步解說

使用 (建立) 新專案]，在 Visual Studio 中建立新的方案 ... ** > 單一視圖應用程式 (c # ) **，然後呼叫它 _StoryboardTables_。

 [![[建立新專案] 對話方塊](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

此方案將會開啟一些 c # 檔案，以及 `Main.storyboard` 已建立的檔案。 按兩下該檔案， `Main.storyboard` 以在 IOS 設計工具中開啟檔案。

<a name="Modifying_the_Storyboard"></a>

## <a name="modifying-the-storyboard"></a>修改分鏡腳本

腳本會以三個步驟進行編輯：

- 首先，配置必要的視圖控制器並設定其屬性。
- 其次，將物件拖放到您的視圖上，以建立您的 UI
- 最後，將必要的 UIKit 類別新增至每個視圖，並為各種控制項提供名稱，以便在程式碼中加以參考。

完成分鏡腳本之後，就可以加入程式碼，讓一切都能正常運作。

<a name="Layout_The_View_Controllers"></a>

### <a name="layout-the-view-controllers"></a>版面配置視圖控制器

腳本的第一項變更是刪除現有的詳細資料檢視，並以 UITableViewController 取代。 請遵循下列步驟：

1. 選取視圖控制器底部的橫條，然後將它刪除。
2. 將 **流覽控制器** 和 **資料表視圖控制器** 從 [工具箱] 拖曳至腳本。 
3. 從根視圖控制器建立 segue 至剛新增的第二個數據表查看控制器。 若要建立 segue，請 *從 [詳細資料] 資料格* 將 Control + 拖曳至新加入的 UITableViewController。 選擇 [在**Segue 選取範圍**下**顯示**] 選項。 
4. 選取您所建立的新 segue，並為其提供可在程式碼中參考此 segue 的識別碼。 按一下 [segue]，並 `TaskSegue` 在**Properties Pad**中輸入**識別碼**，如下所示：    
  [![屬性面板中的命名 segue](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. 接下來，選取並使用 Properties Pad 來設定兩個數據表的視圖。 請務必選取 [View，not View Controller] –您可以使用 [檔大綱] 來協助進行選取。

6. 將 [根視圖控制器] 變更為 [  **內容：動態原型** ]， (Design Surface 上的視圖會標示  **原型內容** ) ：

    [![將 Content 屬性設定為動態原型](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7. 將 [新 **UITableViewController** ] 變更為 [  **內容：靜態資料格**]。 

8. 新的 UITableViewController 必須設定其類別名稱和識別碼。 選取 [ **Properties Pad**中**類別**的 [視圖控制器] 和 [輸入] _TaskDetailViewController_ –這會 `TaskDetailViewController.cs` 在 Solution Pad 中建立新的檔案。 輸入 **StoryboardID** 作為 _詳細資料_，如下列範例所示。 稍後將用來在 c # 程式碼中載入此視圖：  

    [![設定分鏡腳本識別碼](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. 分鏡腳本設計介面現在看起來應該像這樣 (根視圖控制器的導覽專案標題已變更為「繁瑣的面板」 ) ：

    [![設計介面](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  

<a name="Create_the_UI"></a>

### <a name="create-the-ui"></a>建立 UI

現在已設定 views 和 segue，必須加入使用者介面元素。

#### <a name="root-view-controller"></a>根檢視控制器

首先，選取主視圖控制器中的原型儲存格，並將 **識別碼** 設定為 _taskcell_，如下所示。 稍後會在程式碼中用來取出此 UITableViewCell 的實例：

 [![設定資料格識別碼](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

接下來，您必須建立按鈕來新增工作，如下所示：

[![巡覽列中的 bar 按鈕專案](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

執行下列動作： 

- 將 [ **橫條] 按鈕專案** 從 [工具箱] 拖曳至導覽列的 _右側_。
- 在 [ **Properties Pad**] 的 [ **橫條] 按鈕專案** 下，選取 [  **識別碼：新增** (]，讓它成為 *+*) 的加號按鈕。 
- 為其命名，以在稍後的階段中識別程式碼。 請注意，您必須為根視圖控制器提供類別名稱 (例如 **ItemViewController**) ，讓您可以設定橫條按鈕專案的名稱。

#### <a name="taskdetail-view-controller"></a>TaskDetail View 控制器

詳細資料檢視需要更多工作。 資料表視圖資料格必須拖曳到視圖上，然後再填入標籤、文字視圖和按鈕。 下列螢幕擷取畫面顯示完成的 UI 與兩個區段。 其中一個區段有三個數據格、三個標籤、兩個文字欄位和一個參數，而第二個區段有一個具有兩個按鈕的儲存格：

 [![詳細資料檢視版面配置](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

建立完整版面配置的步驟如下：

選取資料表視圖，然後開啟 **屬性面板**。 更新下列屬性：

- **區段**： _2_ 
- **樣式**： _群組_
- **分隔符號**： _無_
- **選取專案**： _沒有選取專案_

選取頂端區段，並在 [ **屬性 > 資料表視圖區段** ] 下，將資料 **列** 變更為 _3_，如下所示：

 [![將最上層區段設定為三個數據列](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

針對每個儲存格開啟 **Properties Pad** 並設定：

- **樣式**：  _自訂_
- **識別碼**：為每個資料格選擇唯一的識別碼 (例如 「_標題_」、「_附注_」、「_完成_」 ) 。
- 拖曳必要的控制項以產生螢幕擷取畫面中顯示的版面配置 (將 **UILabel**、 **UITextField** 和 **UISwitch** 放置在正確的儲存格上，然後適當地設定標籤，即標題、附注和完成) 。

在第二個區段中，將資料 **列** 設定為 _1_ ，並抓取資料格的底部調整大小控點，使其更高。

- **將識別碼設定**為唯一值 (例如： [儲存] ) 。 
- **設定 [背景**：  _清除色彩_ ]。
- 將兩個按鈕拖曳至儲存格，並適當地設定其標題 (例如 _儲存_ 和 _刪除_) ，如下所示：

   [![在下一節中設定兩個按鈕](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

至此，您可能也會想要在儲存格和控制項上設定條件約束，以確保可調整的版面配置。

### <a name="adding-uikit-class-and-naming-controls"></a>新增 UIKit 類別和命名控制項

建立分鏡腳本有幾個最後的步驟。 首先，我們必須為每個控制項提供身分 **識別 > 名稱** 下的名稱，以便稍後在程式碼中使用。 將這些命名如下：

- **標題 UITextField** ： _TitleText_
- **附注 UITextField** ： _NotesText_
- **UISwitch** ： _DoneSwitch_
- **刪除 UIButton** ： _DeleteButton_
- **儲存 UIButton** ： _SaveButton_

<a name="Adding_Code"></a>

## <a name="adding-code"></a>加入程式碼

其餘的工作會在 Mac 或 Windows 上使用 c # Visual Studio 完成。 請注意，程式碼中使用的屬性名稱會反映上述逐步解說中所設定的屬性名稱。

首先我們要建立一個 `Chores` 類別，它會提供一個方法來取得和設定 ID、Name、note 和 Done 布林值的值，讓我們可以在整個應用程式中使用這些值。

在您的 `Chores` 類別中新增下列程式碼：

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

接著，建立 `RootTableSource` 繼承自的類別 `UITableViewSource` 。 

這與非分鏡腳本資料表視圖之間的差異在於， `GetView` 方法不需要具現化任何儲存格– `theDequeueReusableCell` 方法一律會傳回原型資料格的實例， (具有相符的識別碼) 。

以下是來自檔案的程式碼 `RootTableSource.cs` ：

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

若要使用 `RootTableSource` 類別，請在的函式中建立新的集合 `ItemViewController` ：

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

在中 `ViewWillAppear` ，將集合傳遞至來源並指派給資料表視圖：

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

如果您現在執行應用程式，主畫面現在將會載入並顯示兩個工作的清單。 當您接觸到腳本所定義的 segue 時，將會出現詳細資料畫面，但目前不會顯示任何資料。

若要在 segue 中「傳送參數」，請覆寫 `PrepareForSegue` 方法，並在 `DestinationViewController` `TaskDetailViewController` 此範例) 的 (上設定屬性。 目的地視圖控制器類別已具現化，但尚未顯示給使用者，這表示您可以設定類別上的屬性，但不能修改任何 UI 控制項：

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

在方法中，會將 `TaskDetailViewController` `SetTask` 其參數指派給屬性，以便在 ViewWillAppear 中參考它們。 當呼叫時，無法在中修改控制項屬性， `SetTask` 因為可能不存在 `PrepareForSegue` ：

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

Segue 現在會開啟詳細資料畫面，並顯示選取的工作資訊。 可惜的是，[ **儲存** ] 和 [ **刪除** ] 按鈕沒有任何執行。 在執行按鈕之前，請將下列方法新增至 **ItemViewController.cs** ，以更新基礎資料並關閉詳細資料畫面：

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

接下來，您必須將按鈕的 `TouchUpInside` 事件處理常式新增至 `ViewDidLoad` **TaskDetailViewController.cs**的方法。 的 `Delegate` 屬性參考是 `ItemViewController` 特別建立的，因此我們可以呼叫 `SaveTask` 和，這會在其作業中 `DeleteTask` 關閉此視圖：

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

最後剩下的功能是建立新的工作。 在 **ItemViewController.cs** 中，新增建立新工作的方法，並開啟詳細資料檢視。 若要從分鏡腳本將視圖具現化，請 `InstantiateViewController` `Identifier` 針對該視圖使用方法，在此範例中會是「詳細資料」：

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

最後，在 **ItemViewController.cs**的方法中，連接導覽列中的按鈕 `ViewDidLoad` 來呼叫它：

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

這會完成分鏡腳本範例–完成的應用程式看起來像這樣：

[![完成的應用程式](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

此範例示範：

- 使用原型內容建立資料表，其中儲存格會定義為重複使用以顯示資料的清單。 
- 使用靜態內容建立資料表，以建立輸入表單。 這包括變更資料表樣式，以及加入區段、資料格和 UI 控制項。 
- 如何建立 segue 並覆寫  `PrepareForSegue` 方法，以通知目標視圖其所需的任何參數。 
- 直接使用方法載入分鏡腳本  `Storyboard.InstantiateViewController` 。

## <a name="related-links"></a>相關連結

- [StoryboardTable (範例) ](/samples/xamarin/ios-samples/storyboardtable)
- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)