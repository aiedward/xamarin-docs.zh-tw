---
title: "在 iOS 設計工具中使用的資料表"
description: "上一節我們探索開發使用資料表。 在這個中，第五個和最後一個區段中，我們將彙總我們已經為止學會，並建立基本的例行工作清單應用程式使用分鏡腳本。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: e46038b21327fe8847d2c04ee1ba16960f6a059b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-tables-in-the-ios-designer"></a>在 iOS 設計工具中使用的資料表

分鏡腳本 WYSIWYG 如何建立 iOS 應用程式，並且在 Visual Studio 在 Mac 和 Windows 支援。 如需有關分鏡腳本的詳細資訊，請參閱[簡介到分鏡腳本](~/ios/user-interface/storyboards/index.md)文件。 分鏡腳本也可讓您編輯儲存格的版面配置*中*資料表，可簡化開發搭配資料表和資料格

設定 iOS 設計工具中的資料表檢視的屬性，當有兩種類型的儲存格內容，您可以選擇從：**動態**或**靜態**原型內容。

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>原型動態內容

A`UITableView`與原型內容通常用於顯示資料的清單其中原型儲存格 （或資料格，您可以定義多個） 會重複使用的清單中的每個項目。 資料格不需要具現化，在取得這些中`GetView`方法藉由呼叫`DequeueReusableCell`方法及其`UITableViewSource`。

 <a name="Static_Content" />


## <a name="static-content"></a>靜態內容

`UITableView`s 靜態內容和允許直接在設計介面上設計的資料表。 資料格可以拖曳至資料表，並藉由變更屬性，以及將控制項加入自訂。

 <a name="Creating_a_Storyboard-driven_app" />


## <a name="creating-a-storyboard-driven-app"></a>建立分鏡腳本驅動的應用程式

StoryboardTable 範例包含簡單的主從式應用程式中，使用這兩種 UITableView 分鏡腳本。 本章節的其餘部分描述如何建置完成時，看起來像這樣的小型的待辦事項清單範例：

 [![範例的螢幕](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

將分鏡腳本中，以建置使用者介面，而這兩個螢幕會使用 UITableView。 使用主畫面*原型內容*版面配置資料列，並詳細資料螢幕使用*靜態內容*建立使用自訂儲存格的版面配置，將資料輸入表單。

## <a name="walkthrough"></a>逐步解說

建立新的方案在 Visual Studio 使用**（建立） 新增專案 > 單一檢視 App(C#)**，並呼叫它_StoryboardTables_。

 [![建立新的專案 對話方塊](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

某些 C# 檔案即會開啟方案和`Main.storyboard`已經建立的檔案。 按兩下`Main.storyboard`iOS 設計工具中開啟的檔案。

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>修改分鏡腳本

分鏡腳本將進行編輯以三個步驟：

-  首先，所需的版面配置檢視控制站，並設定其屬性。
-  第二，藉由拖放至您的檢視物件中建立 UI
-  最後，將必要的 UIKit 類別加入至每個檢視，並提供各種控制項的名稱，以便它們可以在程式碼中參考。


分鏡腳本完成之後，可以加入程式碼，讓所有工作項目。

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>配置檢視控制器

分鏡腳本的第一個變更是刪除現有的詳細資料檢視，並取代 UITableViewController。 請依照下列步驟：

1.  選取檢視控制站的底部列，並加以刪除。
2.  拖曳**導覽控制站**和**資料表檢視控制器**到分鏡腳本從 [工具箱]。 
3.  從根檢視控制站建立 segue，剛加入的第二個資料表檢視控制站。 若要建立 segue，控制 + 拖曳*從詳細資料的儲存格*至新加入的 UITableViewController。 選擇**顯示*** 下**話題選取**。 
4.  選取新的話題您所建立，並指定其識別碼，這話題程式碼中的參考。 按一下 segue 並輸入`TaskSegue`的**識別碼**中**屬性板**，如下所示：    
  [![在 [屬性] 面板命名話題](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. 接下來，設定兩個資料表的檢視由選取並使用屬性填補。 請務必選取 檢視和檢視的控制器不 – 您可以使用文件大綱，以便選取項目。

6.  變更根檢視控制站當做**內容： 動態原型**(在設計介面上的檢視將會標示為**原型內容**):

    [![將內容屬性設定為動態的原型](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7.  變更新**UITableViewController**是**內容： 靜態的資料格**。 


8. 新 UITableViewController 必須有類別名稱和設定的識別項。 選取的檢視控制器 」 和 「 類型_TaskDetailViewController_如**類別**中**屬性板**– 這會建立新`TaskDetailViewController.cs`方案中的檔案填補。 輸入**StoryboardID**為_詳細_，如下列範例所示。 這將會稍後可用於載入此檢視的 C# 程式碼：  

    [![設定分鏡腳本識別碼](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. 分鏡腳本設計介面現在看起來應該像這樣 （在根檢視控制器的導覽項目標題已變更為"例行工作面板"）：

    [![設計介面](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  



<a name="Create_the_UI" />

### <a name="create-the-ui"></a>建立 UI

現在，檢視和 segues 會設定，需要加入的使用者介面項目。

#### <a name="root-view-controller"></a>根檢視控制器

首先，選取主版檢視控制器中的原型儲存格，並設定**識別碼**為_taskcell_，如下所示。 這會用稍後在程式碼來擷取此 UITableViewCell 的執行個體：

 [![設定資料格識別碼](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

接下來，您將需要建立一個按鈕，將會加入新的工作，如下所示：

[![列在導覽列中的按鈕項目](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

請執行下列動作： 

-  拖曳**工具列按鈕項目**從工具箱拖曳至_導覽列的右手邊_。
-  在**屬性板**下**工具列按鈕項目**選取**識別碼： 新增**(讓它 *+* 加號按鈕)。 
-  指定名稱，如此就可以在程式碼中識別在後續階段。 請注意，您將需要授與根目錄檢視控制器類別名稱 (例如**ItemViewController**) 可讓您設定列按鈕項目名稱。


#### <a name="taskdetail-view-controller"></a>TaskDetail 檢視控制器

詳細資料檢視需要更多的工作。 資料表檢視儲存格需要拖曳至檢視，然後填入標籤、 文字檢視和按鈕。 以下螢幕擷取畫面顯示兩個區段的 [完成] UI。 一個區段具有三個資料格、 三個標籤，這兩個文字欄位和一個切換，而第二個區段有一個資料格包含兩個按鈕：

 [![詳細資料檢視的配置](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

若要建立完整的版面配置的步驟如下：

選取 [資料表] 檢視並開啟**屬性板**。 更新下列屬性：

-  **區段**: _2_ 
-  **樣式**:_分組_
-  **分隔符號**:_無_
-  **選取項目**:_沒有選取項目_

選取頂部區段，並在**屬性 > 資料表的檢視區段**變更**資料列**至_3_，如下所示：


 [![將設定為三個資料列的上方區段](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

每個資料格開啟**屬性板**和設定：

-  **樣式**:_自訂_
-  **識別項**： 選擇 （例如每個資料格的唯一識別碼 「_標題_"，"_備忘稿_"，"_完成_")。
-  拖曳所需的控制項，以產生螢幕擷取畫面所示的配置 (放置**UILabel**， **UITextField**和**UISwitch**上正確的資料格，並將標籤設定適當地 ie。標題、 附註，完成)。


在第二個區段中，設定**列**至_1_和抓取底部調整大小控點，使其較高的資料格。

-  **設定識別項**： 唯一值 （例如。 「 儲存 」）。 
-  **設定背景**:_清除色彩_。
-  兩個按鈕拖曳至資料格，適當地設定其標題 (也就是_儲存_和_刪除_)，如下所示：

   [![在底部區段中設定兩個按鈕](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

此時您可能也要設定您的儲存格和控制項，以確保自動調整的版面配置上的條件約束。

### <a name="adding-uikit-class-and-naming-controls"></a>加入 UIKit 類別和命名控制項

建立我們分鏡腳本有少數的最後一個步驟。 首先我們必須提供每個我們控制項底下的名稱**識別 > 名稱**好讓它們可以用於程式碼之後。 名稱這些，如下所示：

-  **標題 UITextField** : _TitleText_
-  **附註 UITextField** : _NotesText_
-  **UISwitch** : _DoneSwitch_
-  **刪除 UIButton** : _DeleteButton_
-  **儲存 UIButton** : _SaveButton_


<a name="Adding_Code" />

## <a name="adding-code"></a>加入程式碼

Mac 或使用 C# Windows 上的 Visual Studio 中，將在完成工作的其餘部分。 請注意程式碼中使用的屬性名稱反映出在上述的逐步解說中設定。

我們想要建立的第一次`Chores`類別，會提供方法來取得和設定的值的識別碼、 名稱、 附註和完成，則為 True，讓我們可以使用這些值在整個應用程式。

在您`Chores`類別加入下列程式碼：

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

接下來，建立`RootTableSource`類別繼承自`UITableViewSource`。 

這與非分鏡腳本資料表檢視表之間的差異在於`GetView`方法不需要具現化的任何資料格 –`theDequeueReusableCell`方法一律會傳回原型包含的資料格 （比對識別項） 的執行個體。

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

若要使用`RootTableSource`類別，建立新的集合中`ItemViewController`的建構函式：

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

在`ViewWillAppear`傳遞給來源的集合，並將指派給資料表檢視：

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

如果您執行應用程式現在，主畫面將會立即載入並顯示兩個工作的清單。 工作會接觸到分鏡腳本所定義的 segue 會導致出現，詳細資料螢幕，但它不會顯示任何資料時。

若要在 segue 中的 [傳送參數]，覆寫`PrepareForSegue`方法上設定屬性和`DestinationViewController`(`TaskDetailViewController`在此範例中)。 目的地檢視控制器類別具現化會但不是暫時顯示給使用者 – 這表示您可以在類別上設定屬性，但不是能修改任何 UI 控制項：

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

在`TaskDetailViewController``SetTask`方法會指派它的參數屬性以便它們可以 ViewWillAppear 中參考。 無法修改控制項屬性`SetTask`因為可能不存在時`PrepareForSegue`稱為：

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

Segue 現在會開啟詳細資料螢幕，並顯示選取的工作資訊。 不幸的是沒有實作的**儲存**和**刪除**按鈕。 在實作之前按鈕，新增這些方法來**ItemViewController.cs**更新基礎資料並關閉 詳細資料螢幕：

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

接下來，您必須將按鈕加入`TouchUpInside`事件處理常式來`ViewDidLoad`方法**TaskDetailViewController.cs**。 `Delegate`屬性參考`ItemViewController`具體來說，我們就可以呼叫建立`SaveTask`和`DeleteTask`，這可以關閉此檢視其作業的一部分：

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

建置功能的最後剩餘部分是新的工作所建立。 在**ItemViewController.cs**加入的方法，建立新工作，並開啟詳細資料檢視。 具現化的檢視分鏡腳本使用`InstantiateViewController`方法`Identifier`適用於該檢視-在此範例中，將會是 'detail':

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

最後，連接導覽列中按鈕**ItemViewController.cs**的`ViewDidLoad`呼叫它的方法：

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

您已完成的分鏡腳本範例-完成的應用程式看起來像是這樣：

[![完成的應用程式](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

此範例示範：

-  建立具有原型內容的重複用來顯示資料的清單，其中定義資料格。 
-  使用靜態內容來建置輸入的表單中建立資料表。 這包含變更資料表樣式和加入區段、 資料格以及 UI 控制項。 
-  如何建立 segue 和覆寫`PrepareForSegue`方法來通知它需要任何參數的目標檢視。 
-  載入分鏡腳本檢視，直接與`Storyboard.InstantiateViewController`方法。



## <a name="related-links"></a>相關連結

- [StoryboardTable （範例）](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
