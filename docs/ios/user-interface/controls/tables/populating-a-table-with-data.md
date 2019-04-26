---
title: 使用 Xamarin.iOS 中的資料填入資料表
description: 本文件說明如何以填入資料表，以與 Xamarin.iOS 應用程式中的資料。 它討論 UITableViewSource，儲存格重複使用，新增索引，和頁首和頁尾。
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 5363e3a2210bdcf1efb870ac808ecb37584de6a7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61037253"
---
# <a name="populating-a-table-with-data-in-xamarinios"></a>使用 Xamarin.iOS 中的資料填入資料表

若要將資料列加入`UITableView`您需要實作`UITableViewSource`子類別並覆寫資料表檢視的方法呼叫來填入本身。

本指南涵蓋：

- 子類別化 UITableViewSource
- 儲存格重複使用
- 新增索引
- 加入頁首和頁尾


<a name="Subclassing_UITableViewSource" />

## <a name="subclassing-uitableviewsource"></a>子類別化 UITableViewSource

A`UITableViewSource`子類別會指派至每個`UITableView`。 資料表檢視查詢的來源類別，以決定如何呈現其本身 （適用於範例中，會需要多少資料列和如果不同於預設值，每個資料列的高度）。 最重要的是，來源會提供以資料填入每個資料格檢視。

只有兩個資料表顯示資料所需的必要方法有：

-   **RowsInSection** – 傳回[ `nint` ](https://developer.xamarin.com/guides/cross-platform/macios/nativetypes/)應該會顯示資料表的資料列總數的計數。
-   **GetCell** – 傳回`UITableCellView`填入傳遞給方法的對應資料列索引的資料。


BasicTable 範例檔案**TableSource.cs**具有的最簡單的可能實作`UITableViewSource`。 您可以看到下列程式碼片段中，它會接受要顯示資料表中的字串陣列，並傳回包含每個字串的預設儲存格樣式：

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

A`UITableViewSource`可以使用任何資料結構，從簡單的字串陣列 （如本範例所示） 來清單 <> 或另一個集合。 實作`UITableViewSource`方法會隔離從基礎資料結構的資料表。

若要使用這個子類別，建立字串陣列，以建構來源，然後將它指派給執行個體`UITableView`:

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

 [![](populating-a-table-with-data-images/image3.png "執行的範例資料表")](populating-a-table-with-data-images/image3.png#lightbox)

大部分的資料表允許使用者接觸的資料列來選取它，並執行某些其他動作 （例如播放歌曲，或呼叫連絡人，或顯示另一個畫面）。 若要達到此目的，有幾件事，我們要做。 首先，我們將建立來顯示一則訊息，當使用者按一下資料列加入至下列 AlertController`RowSelected`方法：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

接下來，建立我們的檢視控制器的執行個體：

```csharp
HomeScreen owner;
```
加入它做為參數的檢視控制器，並將它儲存在欄位 UITableViewSource 類別建構函式：

```csharp
public TableSource (string[] items, HomeScreen owner)
{
    ...
    this.owner = owner;

}
```
修改 ViewDidLoad 方法 UITableViewSource 類別來傳遞的建立位置`this`參考：

```csharp
table.Source = new TableSource(tableItems, this);
```
最後，回到您`RowSelected`方法中，呼叫`PresentViewController`快取的欄位：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```


現在使用者可以接觸的資料列，並會出現警示：



 [![](populating-a-table-with-data-images/image4.png "資料列選取的警示")](populating-a-table-with-data-images/image4.png#lightbox)


## <a name="cell-reuse"></a>儲存格重複使用

在此範例中有只有六個項目，因此沒有任何所需的儲存格重複使用。 顯示時數百或數千個資料列，不過，它會浪費記憶體來建立數百或數千個`UITableViewCell`物件時只有少數適合螢幕大小一次。

若要避免此情況下，，當從它的檢視放置在佇列中供重複使用畫面消失時儲存格。 當使用者捲動時，資料表就會呼叫`GetCell`來要求新的檢視，以顯示 – 重複使用現有的儲存格 （也就目前沒有正在顯示） 只需呼叫`DequeueReusableCell`方法。 如果儲存格是可重複使用它將會傳回，否則會傳回 null，而且您的程式碼必須建立新的資料格執行個體。

此程式碼片段的範例程式碼示範的模式：

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

`cellIdentifier`有效地建立個別的佇列，不同類型的資料格。 在此範例中的所有儲存格看起來相同因此只有一個的硬式編碼為使用識別項。 如果有不同類型的資料格他們應該擁有不同的識別碼字串，它們會具現化和重複使用的佇列要求時。

### <a name="cell-reuse-in-ios-6"></a>在 iOS 6 + 中的資料格重複使用

iOS 6 新增了一個使用 「 集合檢視類似的資料格重複使用模式。 雖然仍然支援現有的重複使用模式，如上所示，針對回溯相容性，這種新模式是因為它不需要的儲存格上的 null 檢查。

使用新的模式應用程式註冊的資料格類別或藉由呼叫要使用 xib`RegisterClassForCellReuse`或`RegisterNibForCellReuse`控制器的建構函式中。 然後，當清除佇列的儲存格在`GetCell`方法，只要呼叫`DequeueReusableCell`傳遞您註冊在 cell 類別或 xib 和索引路徑的識別項。

例如，下列程式碼會註冊自訂儲存格類別 UITableViewController 中：

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

MyCell 類別登錄的情況下，與儲存格可以從佇列中清除`GetCell`方法的`UITableViewSource`而不需要額外的 null 檢查，如下所示的下方：

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

請注意，使用新重複使用模式時的自訂儲存格類別，您需要實作的建構函式`IntPtr`，下列程式碼片段所示，否則 Objective C 無法建構資料格類別的執行個體：

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

您可以看到範例的主題中所述**BasicTable**範例連結到這份文件。

<a name="Adding_an_Index" />

## <a name="adding-an-index"></a>新增索引

索引可協助捲動較長的清單，雖然您可以建立索引通常會依字母順序的排序使用者在您想依據任何準則。 **BasicTableIndex**範例來示範索引的檔案從載入長清單的項目。 在索引中的每個項目會對應至資料表的 'section'。

 [![](populating-a-table-with-data-images/image5.png "索引顯示")](populating-a-table-with-data-images/image5.png#lightbox)

若要支援資料表背後的資料必須要進行分組，因此 BasicTableIndex 範例會建立 ' sections'`Dictionary<>`做為字典索引鍵使用的第一個字母，每個項目的字串陣列中：

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

`UITableViewSource`子類別，則需要下列方法加入或修改成使用`Dictionary<>`:

-   **NumberOfSections** – 此方法是選擇性的根據預設，此表格假設一個區段。 顯示索引時此方法應傳回的項目數，在索引中 (例如，26 如果索引包含英文字母的所有字母)。
-   **RowsInSection** – 在指定的區段中傳回的資料列數目。
-   **SectionIndexTitles** – 傳回字串陣列，用以顯示的索引。 範例程式碼傳回代號的陣列。


中的範例檔案的更新的方法**BasicTableIndex/TableSource.cs**看起來像這樣：

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

索引通常僅能搭配一般的資料表樣式。


<a name="Adding_Headers_and_Footers" />

## <a name="adding-headers-and-footers"></a>加入頁首和頁尾

頁首和頁尾可以用來以視覺方式分組的資料表資料列。 所需的資料結構是非常類似於新增索引 –`Dictionary<>`實際上都能順利運作。 而不是使用英文字母來分組的資料格，此範例中將會分組蔬菜 botanical 的類型。
輸出顯示如下：

 [![](populating-a-table-with-data-images/image6.png "範例頁首和頁尾")](populating-a-table-with-data-images/image6.png#lightbox)

若要顯示頁首和頁尾`UITableViewSource`子類別需要這些額外的方法：

-   **TitleForHeader** – 傳回要用做為標頭的文字
-   **TitleForFooter** – 傳回要做為頁尾的文字。


中的範例檔案的更新的方法**BasicTableHeaderFooter/Code/TableSource.cs**看起來像這樣：

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

您可以進一步自訂外觀的頁首和頁尾的檢視物件，使用`GetViewForHeader`並`GetViewForFooter`方法會覆寫上`UITableViewSource`。


## <a name="related-links"></a>相關連結

- [WorkingWithTables （範例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
