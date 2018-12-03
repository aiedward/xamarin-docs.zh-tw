---
title: 使用 iOS 設計工具中的資料表
description: 前幾節中，我們探討使用資料表進行開發。 在此，第五個和最後一個區段中，我們將彙總我們已經學到目前為止，並建立使用分鏡腳本的基本項煩瑣清單應用程式。
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 303c96ae6cdbc9f5b327c971f962d6eac75a6fa1
ms.sourcegitcommit: f541a92b4f896474f6a5467ccff2028dafa6fee7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2018
ms.locfileid: "50983610"
---
# <a name="working-with-tables-in-the-ios-designer"></a>使用 iOS 設計工具中的資料表

分鏡腳本是 WYSIWYG 的方式，建立 iOS 應用程式，並支援在 Mac 和 Windows 上的 Visual Studio 內。 如需有關分鏡腳本的詳細資訊，請參閱[到分鏡腳本的簡介](~/ios/user-interface/storyboards/index.md)文件。 分鏡腳本也可讓您編輯儲存格的版面配置*在*資料表中，簡化了開發資料表和資料格

設定 iOS 設計工具中的表格檢視的屬性，當有兩種類型的儲存格內容，您可以選擇：**動態**或是**靜態**原型內容。

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>動態原型內容

A`UITableView`原型內容通常用途是顯示的資料清單，其中原型儲存格 （或資料格，您可以定義多個） 會重複使用的清單中的每個項目。 儲存格不需要具現化，它們會得出`GetView`方法，藉由呼叫`DequeueReusableCell`方法及其`UITableViewSource`。

 <a name="Static_Content" />


## <a name="static-content"></a>靜態內容

`UITableView`與靜態內容可讓您直接在設計介面上設計的資料表。 可以拖曳到資料表資料格，並變更屬性，然後將控制項加入自訂。

 <a name="Creating_a_Storyboard-driven_app" />


## <a name="creating-a-storyboard-driven-app"></a>建立分鏡腳本驅動的應用程式

StoryboardTable 範例包含使用這兩種 UITableView 在分鏡腳本的簡單主版詳細資料應用程式。 本節的其餘部分會說明如何建置完成時，看起來像這樣的小型的待辦事項清單範例：

 [![範例畫面](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

將分鏡腳本，以建置使用者介面，這兩個畫面將會使用 UITableView。 使用主畫面*原型內容*版面配置資料列，並詳細資料畫面也使用*靜態內容*來建立使用自訂的資料格的版面配置，將資料輸入表單。

## <a name="walkthrough"></a>逐步解說

建立新的方案在 Visual Studio 中使用 **（建立） 新專案 > 單一檢視應用程式 (C#)**，並呼叫它_StoryboardTables_。

 [![建立新的 [專案] 對話方塊](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

解決方案會開啟並顯示某些C#檔案和`Main.storyboard`已經建立的檔案。 按兩下`Main.storyboard`在 iOS 設計工具中開啟的檔案。

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>修改分鏡腳本

分鏡腳本將編輯在三個步驟：

-  首先，所需的版面配置檢視控制器，並設定其屬性。
-  其次，藉由拖放物件拖曳至您的檢視中建立您的 UI
-  最後，將必要的 UIKit 類別新增至每個檢視，並提供各種控制項的名稱，以便它們可以在程式碼中參考。


分鏡腳本完成之後，程式碼可以加入工作的每個項目。

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>版面配置檢視控制器

第一項變更到分鏡腳本是刪除現有的詳細資料檢視，並取代 UITableViewController。 請依照下列步驟：

1.  選取底部的 檢視控制器的列，並將它刪除。
2.  拖曳**巡覽控制器**並**資料表檢視控制器**到分鏡腳本，從 [工具箱]。 
3.  從根檢視控制器的 segue 建立以剛加入第二個資料表檢視控制器中。 若要建立的 segue，控制項] + [拖曳*詳細資料格從*至新加入的 UITableViewController。 選擇選項**顯示**下方**Segue 的選取項目**。 
4.  選取新的 segue 您所建立，並指定其識別碼，這在程式碼中的 segue 的參考。 按一下 segue，然後輸入`TaskSegue`for**識別項**中**Properties Pad**，如下所示：    
  [![在 [屬性] 面板命名 segue](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. 接下來，設定兩個資料表檢視中選取它們，並使用 [屬性] 面板。 請務必選取 檢視並不是檢視控制器 – 您可以使用文件大綱，以協助使用選取項目。

6.  變更為根檢視控制器**內容： 動態原型**(將會標示為設計介面上的檢視**原型內容**):

    [![將內容屬性設定為動態的原型](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7.  變更新**UITableViewController**要**內容︰ 靜態的資料格**。 


8. 新 UITableViewController 必須具有其類別名稱和設定的識別碼。 選取檢視控制器，然後輸入_TaskDetailViewController_ for**類別**中**Properties Pad** – 這會建立新`TaskDetailViewController.cs`方案中的檔案填補。 請輸入**StoryboardID**作為_詳細_，如下列範例所示。 這將會稍後可用於載入此檢視的C#程式碼：  

    [![設定分鏡腳本識別碼](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. 分鏡腳本設計介面現在看起來應該像這樣 （根檢視控制器的導覽項目的標題已變更為 「 事半功倍的效果面板 」）：

    [![設計介面](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  



<a name="Create_the_UI" />

### <a name="create-the-ui"></a>建立 UI

現在，檢視和 segue 會設定，必須要加入的使用者介面項目。

#### <a name="root-view-controller"></a>根檢視控制器

首先，選取主版檢視控制器中的原型儲存格，並設定**識別碼**作為_taskcell_，如下所示。 這會用更新版本中的程式碼來擷取此 UITableViewCell 的執行個體：

 [![設定資料格識別碼](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

接下來，您必須建立一個按鈕，將會加入新的工作，如下所示：

[![列在導覽列中的按鈕項目](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

請執行下列動作： 

-  拖曳**列按鈕項目**從工具箱拖曳至_右邊的瀏覽列_。
-  在**Properties Pad**下方**列按鈕項目**選取**識別碼： 新增**(以便 *+* 加號按鈕)。 
-  指定它的名稱，如此就可以在程式碼中識別在稍後的階段。 請注意，您將需要提供根檢視控制器類別名稱 (例如**ItemViewController**) 可讓您設定之列按鈕項目的名稱。


#### <a name="taskdetail-view-controller"></a>TaskDetail 檢視控制器

詳細資料檢視需要更多的工作。 資料表檢視儲存格需要拖曳至檢視，並接著填入標籤、 文字檢視和按鈕。 以下螢幕擷取畫面顯示完成的 UI 使其具備兩個區段。 一個區段具有三個資料格，這三個標籤、 兩個文字欄位和一個切換，而第二個區段有一個資料格包含兩個按鈕：

 [![詳細資料檢視版面配置](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

若要建置完整的版面配置的步驟如下：

選取 [資料表] 檢視，然後開啟**屬性輸入板**。 更新下列屬性：

-  **章節**: _2_ 
-  **樣式**:_分組_
-  **分隔符號**: _None_
-  **選取項目**:_沒有選取項目_

選取上方的區段和下方**屬性 > 資料表檢視 區段**變更**資料列**來_3_，如下所示：


 [![將設定為三個資料列的上方區段](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

每個資料格開放**Properties Pad**和設定：

-  **樣式**:_自訂_
-  **識別項**： 選擇 （例如在每個資料格的唯一識別碼。 「_標題_"，"_備忘稿_"，"_完成_")。
-  拖曳所需的控制項，以產生螢幕擷取畫面所示的版面配置 (放置**UILabel**， **UITextField**並**UISwitch**正確的資料格，並設定標籤適當地 ie。標題、 記事及完成)。


在第二個區段中，設定**資料列**要_1_和抓取要使較高的儲存格的下方調整大小控點。

-  **設定識別項**： 為唯一的值 （例如。 「 儲存 」）。 
-  **設定背景**:_清除色彩_。
-  儲存格上拖曳兩個按鈕，並適當地設定其標題 (亦即_儲存_並_刪除_)，如下所示：

   [![下一節中設定兩個按鈕](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

此時您可能也想要設定您的儲存格和控制，以確保自動調整的版面配置上的條件約束。

### <a name="adding-uikit-class-and-naming-controls"></a>新增 UIKit 類別和命名控制項

有少數的最後一個步驟中建立我們的分鏡腳本。 首先我們必須提供每個控制項的名稱**身分識別 > 名稱**讓它們可以用於程式碼之後。 命名這些，如下所示：

-  **標題 UITextField** : _TitleText_
-  **資訊 UITextField** : _NotesText_
-  **UISwitch** : _DoneSwitch_
-  **刪除標記的 UIButton** : _DeleteButton_
-  **儲存標記的 UIButton** : _SaveButton_


<a name="Adding_Code" />

## <a name="adding-code"></a>加入程式碼

工作的其餘部分時，必須在 Mac 或 Windows 使用上的 Visual Studio 中C#。 請注意，程式碼中使用的屬性名稱會反映在上述逐步解說中設定。

我們想要建立的第一次`Chores`類別，會提供用來取得和設定的值的識別碼、 名稱、 記事和完成，則為 True，讓我們可以使用這些值，整個應用程式。

在您`Chores`類別新增下列程式碼：

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

接下來，建立`RootTableSource`類別繼承自`UITableViewSource`。 

這與非分鏡腳本資料表檢視之間的差異在於`GetView`方法並不需要具現化任何資料格 –`theDequeueReusableCell`方法一律會傳回執行個體的原型包含的資料格 （比對識別項）。

下列程式碼取自`RootTableSource.cs`檔案：

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

若要使用`RootTableSource`類別中，建立新的集合中`ItemViewController`的建構函式：

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

在 `ViewWillAppear`傳遞給來源的集合，並將資料表檢視：

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

如果您執行應用程式現在，主畫面會立即載入和顯示兩個工作的清單。 當工作都會被接觸到分鏡腳本所定義的 segue 會導致出現，詳細資料畫面，但它不會在目前顯示的任何資料。

若要在 segue 中的 [傳送參數]，覆寫`PrepareForSegue`方法和設定的屬性`DestinationViewController`(`TaskDetailViewController`在此範例中)。 目的地檢視控制器類別會有已具現化，但不是，但顯示給使用者 – 這表示您可以在類別上設定屬性，但無法修改任何 UI 控制項：

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

在  `TaskDetailViewController` `SetTask`方法會指派給屬性及其參數以便可以在 ViewWillAppear 來參考。 無法修改控制項的屬性`SetTask`因為可能不存在時`PrepareForSegue`稱為：

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

Segue 現在會開啟詳細資料畫面，並顯示所選的工作的資訊。 不幸的是沒有任何實作**儲存**並**刪除**按鈕。 實作之前的按鈕，新增這些方法才能**ItemViewController.cs**來更新基礎資料，然後關閉 詳細資料畫面：

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

接下來，您必須將按鈕加入`TouchUpInside`事件處理常式`ViewDidLoad`方法**TaskDetailViewController.cs**。 `Delegate`屬性參考`ItemViewController`特別因此我們可以呼叫時，才建立`SaveTask`和`DeleteTask`，這可以關閉此檢視其作業的一部分：

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

建置功能的最後一個剩餘項是建立新的工作。 在  **ItemViewController.cs**新增的方法，建立新工作，並開啟詳細資料檢視。 分鏡腳本的使用中的檢視具現化`InstantiateViewController`方法使用`Identifier`為該檢視表-在此範例中，將會是 [詳細資料]:

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

最後，連接中的導覽列中的按鈕**ItemViewController.cs**的`ViewDidLoad`呼叫它的方法：

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

您已完成的分鏡腳本範例-完成的應用程式看起來像是這樣：

[![完成的應用程式](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

此範例示範：

-  使用原型內容所在的儲存格所定義重複用來顯示資料的清單建立資料表。 
-  使用靜態內容來建置一個輸入的表單中建立資料表。 這包含變更的表格樣式，並新增區段、 資料格以及 UI 控制項。 
-  如何建立 segue 並覆寫`PrepareForSegue`方法來通知它所需要的任何參數的目標檢視。 
-  正在載入分鏡腳本檢視直接與`Storyboard.InstantiateViewController`方法。



## <a name="related-links"></a>相關連結

- [StoryboardTable （範例）](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
