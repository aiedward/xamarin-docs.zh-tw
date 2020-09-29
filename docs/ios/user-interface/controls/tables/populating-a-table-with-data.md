---
title: 在 Xamarin 中使用資料填入資料表
description: 本檔說明如何在 Xamarin iOS 應用程式中使用資料填入資料表。 它會討論 UITableViewSource、資料格重複使用、新增索引，以及頁首和頁尾。
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 819e4f89b1443b2e1154aedfb51007ca38fdca02
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429917"
---
# <a name="populating-a-table-with-data-in-xamarinios"></a>在 Xamarin 中使用資料填入資料表

若要將資料列加入至 `UITableView` ，您需要執行子 `UITableViewSource` 類別，並覆寫資料表視圖呼叫以填入本身的方法。

本指南涵蓋：

- 子類別化 UITableViewSource
- 資料格重複使用
- 加入索引
- 加入頁首和頁尾

<a name="Subclassing_UITableViewSource"></a>

## <a name="subclassing-uitableviewsource"></a>子類別化 UITableViewSource

子 `UITableViewSource` 類別會指派給每一個子類別 `UITableView` 。 資料表視圖會查詢來源類別，以決定如何轉譯本身 (例如，需要多少資料列和每個資料列的高度（如果不同于預設的) ）。 最重要的是，來源會提供每個資料格視圖，並填入資料。

讓資料表顯示資料時，只有兩個必要的必要方法：

- **RowsInSection** –傳回  [`nint`](~/cross-platform/macios/nativetypes.md) 資料表應該顯示之資料列總數的計數。
- **GetCell** –  `UITableViewCell` 針對傳遞給方法的對應資料列索引，傳回填入資料的。

BasicTable 範例檔案 **TableSource.cs** 具有最簡單的可能執行 `UITableViewSource` 。 您可以在下面的程式碼片段中看到，它接受字串陣列以顯示在資料表中，並傳回包含每個字串的預設儲存格樣式：

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

            //if there are no cells to reuse, create a new one
            if (cell == null)
            { 
                cell = new UITableViewCell (UITableViewCellStyle.Default, CellIdentifier); 
            }

            cell.TextLabel.Text = item;

            return cell;
        }
}
```

`UITableViewSource`可以使用任何資料結構，從簡單的字串陣列 (如下列範例所示) 至清單 <> 或其他集合。 方法的執行會將 `UITableViewSource` 資料表與基礎資料結構隔離。

若要使用這個子類別，請建立字串陣列來建立來源，然後將它指派給的實例 `UITableView` ：

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

 [![執行的範例資料表](populating-a-table-with-data-images/image3.png)](populating-a-table-with-data-images/image3.png#lightbox)

大部分的資料表可讓使用者透過資料列來選取它，並執行一些其他動作 (例如播放歌曲、呼叫連絡人，或顯示其他螢幕) 。 為了達成此目的，我們需要做幾件事。 首先，讓我們建立 AlertController，以便在使用者按一下資料列時顯示訊息，方法是將下列內容新增至 `RowSelected` 方法：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

接下來，建立我們的 View Controller 實例：

```csharp
HomeScreen owner;
```

將函式新增至 UITableViewSource 類別，此類別會採用 view 控制器作為參數，並將其儲存在欄位中：

```csharp
public TableSource (string[] items, HomeScreen owner)
{
    ...
    this.owner = owner;

}
```

修改建立 UITableViewSource 類別的 ViewDidLoad 方法，以傳遞 `this` 參考：

```csharp
table.Source = new TableSource(tableItems, this);
```

最後，回到您 `RowSelected` 的方法，在快取 `PresentViewController` 的欄位上呼叫：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```

現在使用者可以觸及某個資料列，將會出現警示：

 [![選取的資料列警示](populating-a-table-with-data-images/image4.png)](populating-a-table-with-data-images/image4.png#lightbox)

## <a name="cell-reuse"></a>資料格重複使用

在此範例中，只有六個專案，因此不需要重複使用儲存格。 不過，當顯示數百或數千個數據列時，可能會浪費記憶體來建立數百個或數千個 `UITableViewCell` 物件（當畫面一次只能容納一段時）。

為了避免這種情況，當儲存格從畫面中消失時，其視圖會放置在佇列中以供重複使用。 當使用者滾動時，資料表會呼叫 `GetCell` 以要求新的視圖顯示，以重複使用目前未顯示的現有儲存格 () 直接呼叫 `DequeueReusableCell` 方法。 如果有可重複使用的資料格，則會傳回，否則會傳回 null，而您的程式碼必須建立新的資料格實例。

此範例中的這個程式碼片段會示範模式：

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

`cellIdentifier`針對不同類型的資料格有效地建立不同的佇列。 在此範例中，所有儲存格看起來都一樣，因此只會使用一個硬式編碼的識別碼。 如果有不同類型的資料格，則每個都有不同的識別碼字串，它們會在具現化時，以及從重複使用佇列要求時。

### <a name="cell-reuse-in-ios-6"></a>IOS 6 + 中的儲存格重複使用

iOS 6 新增了一個資料格重複使用模式，類似于收集視圖的簡介。 雖然先前顯示的重複使用模式仍支援回溯相容性，但這種新模式較理想，因為它會移除儲存格上的 null 檢查需求。

使用新模式時，應用程式會 `RegisterClassForCellReuse` 在控制器的函式中呼叫或，以註冊要使用的儲存格類別或 xib `RegisterNibForCellReuse` 。 然後，在清除佇列方法中的儲存格時 `GetCell` ，只要呼叫 `DequeueReusableCell` 傳遞您針對資料格類別或 xib 和索引路徑所註冊的識別碼即可。

例如，下列程式碼會在 UITableViewController 中註冊自訂資料格類別：

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

在註冊 MyCell 類別的情況下，可以在的方法中清除資料格， `GetCell` `UITableViewSource` 而不需要額外的 null 檢查，如下所示：

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

請注意，搭配自訂資料格類別使用新的重複使用模式時，您需要執行採用的函式 `IntPtr` ，如下列程式碼片段所示，否則目標 C 將無法建立 cell 類別的實例：

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

您可以在連結至本文的 **BasicTable** 範例中，查看上述主題的範例。

<a name="Adding_an_Index"></a>

## <a name="adding-an-index"></a>加入索引

索引可協助使用者滾動長清單，通常會依字母順序排序，雖然您可以依您想要的任何準則編制索引。 **BasicTableIndex**範例會從檔案載入較長的專案清單，以示範索引。 索引中的每個專案都會對應到資料表的「區段」。

 [![索引顯示](populating-a-table-with-data-images/image5.png)](populating-a-table-with-data-images/image5.png#lightbox)

若要支援「區段」，必須將資料表背後的資料分組，如此 BasicTableIndex 範例會 `Dictionary<>` 使用每個專案的第一個字母做為字典索引鍵，從字串陣列建立。

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

`UITableViewSource`然後子類別需要新增或修改下列方法，才能使用 `Dictionary<>` ：

- **NumberOfSections** –這個方法是選擇性的，資料表預設會假設一個區段。 顯示索引時，此方法應該會傳回索引中的專案數 (例如，如果索引包含英文字母) 的所有字母，則為26。
- **RowsInSection** –傳回指定區段中的資料列數目。
- **SectionIndexTitles** –傳回將用來顯示索引的字串陣列。 範例程式碼會傳回一系列的字母。

範例檔案 **BasicTableIndex/TableSource** 中的更新方法看起來像這樣：

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

<a name="Adding_Headers_and_Footers"></a>

## <a name="adding-headers-and-footers"></a>加入頁首和頁尾

頁首和頁尾可以用來以視覺方式將資料表中的資料列分組。 所需的資料結構與加入索引非常類似，a `Dictionary<>` 運作效果非常好。 此範例會依植物園類型將蔬菜分組，而不是使用字母來分組儲存格。
輸出如下所示：

 [![範例頁首和頁尾](populating-a-table-with-data-images/image6.png)](populating-a-table-with-data-images/image6.png#lightbox)

若要顯示頁首和頁尾，子 `UITableViewSource` 類別需要下列額外的方法：

- **TitleForHeader** –傳回要作為標頭使用的文字
- **TitleForFooter** –傳回用來作為頁尾的文字。

範例檔案 **BasicTableHeaderFooter/Code/TableSource** 中的更新方法看起來像這樣：

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

您可以使用 `GetViewForHeader` 和 `GetViewForFooter` 方法覆寫，進一步自訂頁首和頁尾的外觀 `UITableViewSource` 。

## <a name="related-links"></a>相關連結

- [WorkingWithTables (範例) ](/samples/xamarin/ios-samples/workingwithtables)