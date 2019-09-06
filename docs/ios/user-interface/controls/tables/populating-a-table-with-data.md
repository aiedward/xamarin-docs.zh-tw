---
title: 以 Xamarin 中的資料填入資料表
description: 本檔說明如何在資料表中填入 Xamarin iOS 應用程式中的資料。 其中討論 UITableViewSource、資料格重複使用、加入索引，以及頁首和頁尾。
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: d409787661491a6922434a12157c494851644412
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291622"
---
# <a name="populating-a-table-with-data-in-xamarinios"></a>以 Xamarin 中的資料填入資料表

若要將資料列`UITableView`加入至，您必須`UITableViewSource`執行子類別，並覆寫資料表視圖所呼叫的方法來填入本身。

本指南涵蓋：

- 子類別化 UITableViewSource
- 資料格重複使用
- 加入索引
- 加入頁首和頁尾


<a name="Subclassing_UITableViewSource" />

## <a name="subclassing-uitableviewsource"></a>子類別化 UITableViewSource

子`UITableViewSource`類別會指派給每`UITableView`個。 資料表視圖會查詢來源類別，以決定如何呈現本身（例如，需要多少資料列，以及每個資料列的高度（如果不同于預設值）。 最重要的是，來源會提供每個資料格視圖，並填入資料。

若要讓資料表顯示資料，只需要兩個強制方法：

- **RowsInSection** – [`nint`](~/cross-platform/macios/nativetypes.md)傳回資料表應該顯示之資料列總數的計數。
- **GetCell** – `UITableCellView`針對傳遞至方法的對應資料列索引，傳回已填入資料的。


BasicTable 範例檔案**TableSource.cs**具有最簡單的`UITableViewSource`可能執行。 您可以在下面的程式碼片段中看到，它接受要顯示在資料表中的字串陣列，並傳回包含每個字串的預設儲存格樣式：

```csharp
public class TableSource : UITableViewSource {

        string[] TableItems;
        string CellIdentifier = "TableCell";

        public TableSource (string[] items)
        {
            TableItems = items;
        }

        public override nint RowsInSection (UITableView tableview, nint section)
        {
            return TableItems.Length;
        }

        public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewCell cell = tableView.DequeueReusableCell (CellIdentifier);
            string item = TableItems[indexPath.Row];

            //---- if there are no cells to reuse, create a new one
            if (cell == null)
            { cell = new UITableViewCell (UITableViewCellStyle.Default, CellIdentifier); }

            cell.TextLabel.Text = item;

            return cell;
        }
}
```

`UITableViewSource`可以使用任何資料結構，從簡單字串陣列（如本範例所示）到清單 < > 或其他集合。 `UITableViewSource`方法的執行會隔離基礎資料結構中的資料表。

若要使用這個子類別，請建立字串陣列來建立來源，然後將它指派給`UITableView`的實例：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    table = new UITableView(View.Bounds); // defaults to Plain style
    string[] tableItems = new string[] {"Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers"};
    table.Source = new TableSource(tableItems);
    Add (table);
}
```

產生的資料表看起來像這樣：

 [![](populating-a-table-with-data-images/image3.png "執行中的範例資料表")](populating-a-table-with-data-images/image3.png#lightbox)

大部分的資料表都可讓使用者觸及資料列來選取它，並執行其他動作（例如播放歌曲、呼叫連絡人，或顯示另一個畫面）。 為了達成此目的，我們需要做幾件事。 首先，讓我們建立一個 AlertController，在使用者按一下資料列時顯示訊息， `RowSelected`方法是將下列內容新增至方法：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

接下來，建立 View Controller 的實例：

```csharp
HomeScreen owner;
```

將函式新增至 UITableViewSource 類別，它會採用 view controller 做為參數，並將它儲存在欄位中：

```csharp
public TableSource (string[] items, HomeScreen owner)
{
    ...
    this.owner = owner;

}
```

修改建立 UITableViewSource 類別的 ViewDidLoad 方法，以傳遞`this`參考：

```csharp
table.Source = new TableSource(tableItems, this);
```

最後，回到您`RowSelected`的方法，在快取的欄位上呼叫： `PresentViewController`

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```


現在使用者可以觸碰一列，隨即會出現警示：



 [![](populating-a-table-with-data-images/image4.png "選取的資料列警示")](populating-a-table-with-data-images/image4.png#lightbox)


## <a name="cell-reuse"></a>資料格重複使用

在此範例中，只有六個專案，因此不需要重複使用資料格。 不過，當顯示數百或數千個數據列時，如果一次只有幾個畫面，就會浪費`UITableViewCell`記憶體來建立上百個或數千個物件。

若要避免這種情況，當儲存格從畫面中消失時，其 view 會放在佇列中以供重複使用。 當使用者滾動時，資料表會呼叫`GetCell`來要求要顯示的新視圖–若要重複使用現有的儲存格（目前未顯示），只要`DequeueReusableCell`呼叫方法即可。 如果資料格可以重複使用，則會傳回，否則會傳回 null，且您的程式碼必須建立新的資料格實例。

範例中的這個程式碼片段會示範模式：

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

會`cellIdentifier`針對不同類型的資料格，有效地建立個別的佇列。 在此範例中，所有資料格的外觀都相同，因此只會使用一個硬式編碼識別碼。 如果有不同類型的資料格，則兩者都應該具有不同的識別碼字串，兩者都是具現化時，以及從重複使用佇列要求的時間。

### <a name="cell-reuse-in-ios-6"></a>IOS 6 + 中的資料格重複使用

iOS 6 新增了一種資料格重複使用模式，類似于集合視圖簡介。 雖然先前所示的重複使用模式仍然支援回溯相容性，但這種新模式較適合，因為它不需要對儲存格進行 null 檢查。

使用新模式時，應用程式會在控制器的函式中呼叫`RegisterClassForCellReuse`或`RegisterNibForCellReuse` ，以註冊要使用的資料格類別或 xib。 然後，在清除佇列`GetCell`方法中的資料格時，只需呼叫`DequeueReusableCell`傳遞您為數據格類別或 xib 所註冊的識別碼，以及索引路徑。

例如，下列程式碼會在 UITableViewController 中註冊自訂的資料格類別：

```csharp
public class MyTableViewController : UITableViewController
{
  static NSString MyCellId = new NSString ("MyCellId");

  public MyTableViewController ()
  {
    TableView.RegisterClassForCellReuse (typeof(MyCell), MyCellId);
  }
  ...
}
```

註冊 MyCell 類別之後，就可以在的`GetCell`方法`UITableViewSource`中將資料格清除佇列，而不需要額外的 null 檢查，如下所示：

```csharp
class MyTableSource : UITableViewSource
{
  public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
  {
    // if cell is not available in reuse pool, iOS will create one automatically
    // no need to do null check and create cell manually
    var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

    // do whatever you need to with cell, such as assigning properties, etc.

    return cell;
  }
}
```

請注意，搭配自訂資料格類別使用新的重複使用模式時，您需要執行採用`IntPtr`的「處理常式」，如下列程式碼片段所示，否則目標 C 將無法建立資料格類別的實例：

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

您可以在連結到本文的**BasicTable**範例中，看到上述主題的範例。

<a name="Adding_an_Index" />

## <a name="adding-an-index"></a>加入索引

索引可協助使用者流覽長清單（通常是依字母順序排序，雖然您可以依據您想要的任何準則來編制索引）。 **BasicTableIndex**範例會從檔案載入更長的專案清單，以示範索引。 索引中的每個專案都會對應到資料表的「區段」。

 [![](populating-a-table-with-data-images/image5.png "索引顯示")](populating-a-table-with-data-images/image5.png#lightbox)

若要支援「區段」，必須將資料表後方的資料分組，因此 BasicTableIndex 範例會使用每`Dictionary<>`個專案的第一個字母做為字典索引鍵，從字串陣列建立：

```csharp
indexedTableItems = new Dictionary<string, List<string>>();
foreach (var t in items) {
    if (indexedTableItems.ContainsKey (t[0].ToString ())) {
        indexedTableItems[t[0].ToString ()].Add(t);
    } else {
        indexedTableItems.Add (t[0].ToString (), new List<string>() {t});
    }
}
keys = indexedTableItems.Keys.ToArray ();
```

然後子`Dictionary<>`類別需要新增或修改的下列方法，才能使用： `UITableViewSource`

- **NumberOfSections** –此方法是選擇性的，根據預設，資料表會假設一個區段。 顯示索引時，這個方法應該會傳回索引中的專案數（例如，如果索引包含英文字母的所有字母，則為26）。
- **RowsInSection** –傳回給定區段中的資料列數目。
- **SectionIndexTitles** –傳回將用來顯示索引的字串陣列。 範例程式碼會傳回字母陣列。


範例檔案**BasicTableIndex/TableSource**中的更新方法如下所示：

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return keys.Length;
}
public override nint RowsInSection (UITableView tableview, nint section)
{
    return indexedTableItems[keys[section]].Count;
}
public override string[] SectionIndexTitles (UITableView tableView)
{
    return keys;
}
```

索引通常僅適用于純資料表樣式。


<a name="Adding_Headers_and_Footers" />

## <a name="adding-headers-and-footers"></a>加入頁首和頁尾

頁首和頁尾可以用來以視覺化方式將資料表中的資料列分組。 所需的資料結構與加入索引`Dictionary<>`非常類似，但運作方式很良好。 這個範例會根據植物園類型來分組蔬菜，而不是使用字母將資料格分組。
輸出顯示如下：

 [![](populating-a-table-with-data-images/image6.png "範例標頭和頁尾")](populating-a-table-with-data-images/image6.png#lightbox)

若要顯示標頭和`UITableViewSource`頁尾，子類別需要下列額外的方法：

- **TitleForHeader** –傳回要當做標頭使用的文字
- **TitleForFooter** –傳回要當做頁尾使用的文字。


範例檔案**BasicTableHeaderFooter/Code/TableSource**中的更新方法如下所示：

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    return keys[section];
}
public override string TitleForFooter (UITableView tableView, nint section)
{
    return indexedTableItems[keys[section]].Count + " items";
}
```

您可以在上`GetViewForHeader` `UITableViewSource`使用和`GetViewForFooter`方法覆寫，以進一步自訂頁首和頁尾的外觀與 View 物件。


## <a name="related-links"></a>相關連結

- [WorkingWithTables （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
