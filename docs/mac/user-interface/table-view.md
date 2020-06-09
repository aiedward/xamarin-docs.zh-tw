---
title: Xamarin 中的資料表視圖
description: 本文說明如何在 Xamarin. Mac 應用程式中使用資料表的 views。 其中說明如何在 Xcode 中建立資料表視圖和 Interface Builder，並在程式碼中與其互動。
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 5c3e486d3202a71cc82dfef8910908e5cb472cd2
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574193"
---
# <a name="table-views-in-xamarinmac"></a>Xamarin 中的資料表視圖

_本文說明如何在 Xamarin. Mac 應用程式中使用資料表的 views。其中說明如何在 Xcode 中建立資料表視圖和 Interface Builder，並在程式碼中與其互動。_

在 Xamarin. Mac 應用程式中使用 c # 和 .NET 時，您可以存取開發人員以*目標-C*和*Xcode*執行的相同資料表流覽。 因為 Xamarin 會直接與 Xcode 整合，所以您可以使用 Xcode 的_Interface Builder_來建立和維護資料表的視圖（或選擇直接在 c # 程式碼中建立它們）。

資料表視圖會以表格式格式顯示資料，其中包含多個資料列中的一或多個資訊資料行。 根據所建立的資料表檢視類型，使用者可以依資料行排序、重新組織資料行、加入資料行、移除資料行或編輯資料表中包含的資料。

[![](table-view-images/intro01.png "An example table")](table-view-images/intro01.png#lightbox)

在本文中，我們將討論在 Xamarin. Mac 應用程式中使用資料表視圖的基本概念。 強烈建議您先流覽[Hello，Mac](~/mac/get-started/hello-mac.md)文章，特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介，其中涵蓋了我們將在本文中使用的重要概念和技巧。

您可能想要查看[Xamarin 內部](~/mac/internals/how-it-works.md)檔的將[c # 類別/方法公開至目標-C](~/mac/internals/how-it-works.md)一節，它會說明 `Register` `Export` 用來將 C # 類別連接至目標 C 物件和 UI 元素的和命令。

<a name="Introduction_to_Table_Views"></a>

## <a name="introduction-to-table-views"></a>資料表視圖簡介

資料表視圖會以表格式格式顯示資料，其中包含多個資料列中的一或多個資訊資料行。 資料表視圖會顯示在捲軸（）內，而 `NSScrollView` 從 macOS 10.7 開始，您可以使用任何資料 `NSView` 格（ `NSCell` ）來顯示資料列和資料行。 不過，您仍然可以使用 `NSCell` ，但您通常會 `NSTableCellView` 建立自訂的資料列和資料行的子類別。

資料表視圖不會儲存自己的資料，而是依賴資料來源（），以視 `NSTableViewDataSource` 需要提供所需的資料列和資料行。

資料表視圖的行為可以藉由提供資料表視圖委派的子類別（）來進行自訂 `NSTableViewDelegate` ，以支援資料表資料行管理、針對選取功能、資料列選取和編輯、自訂追蹤，以及個別資料行和資料列的自訂視圖。

建立資料表視圖時，Apple 會建議下列各項：

- 允許使用者按一下資料行標頭來排序資料表。
- 建立資料行標頭，其為名詞或簡短名詞片語，可描述該資料行中所顯示的資料。

如需詳細資訊，請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[內容流覽](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1)一節。

<a name="Creating-and-Maintaining-Table-Views-in-Xcode"></a>

## <a name="creating-and-maintaining-table-views-in-xcode"></a>在 Xcode 中建立和維護資料表 Views

當您建立新的 Xamarin Cocoa 應用程式時，預設會取得標準的空白視窗。 此視窗會在專案中自動包含的檔案中定義 `.storyboard` 。 若要編輯您的 windows 設計，請在 [**方案總管**中按兩下該檔案 `Main.storyboard` ：

[![](table-view-images/edit01.png "Selecting the main storyboard")](table-view-images/edit01.png#lightbox)

這會在 Xcode 的 Interface Builder 中開啟視窗設計：

[![](table-view-images/edit02.png "Editing the UI in Xcode")](table-view-images/edit02.png#lightbox)

`table`在 [連結**庫偵測器] 的**[搜尋] 方塊中鍵入，讓您更輕鬆地尋找資料表視圖控制項：

[![](table-view-images/edit03.png "Selecting a Table View from the Library")](table-view-images/edit03.png#lightbox)

將 [資料表] 視圖拖曳至 [**介面編輯器**] 中的 view controller，使其填滿 view controller 的內容區域，並將它設定為在 [**條件約束編輯器**] 中的視窗縮小和成長的位置：

[![](table-view-images/edit04.png "Editing constraints")](table-view-images/edit04.png#lightbox)

在 [**介面**階層架構] 中選取資料表視圖，屬性偵測**器**中會提供下列屬性：

[![](table-view-images/edit05.png "The Attribute Inspector")](table-view-images/edit05.png#lightbox)

- **內容模式**-可讓您使用 Views （ `NSView` ）或 Cells （ `NSCell` ）來顯示資料列和資料行中的資料。 從 macOS 10.7 開始，您應該使用 Views。
- **浮動群組資料列**-如果 `true` ，資料表視圖會繪製群組的資料格，如同它們是浮動的。
- 資料**行**-定義顯示的資料行數目。
- **標頭**-若 `true` 為，則資料行將具有標頭。
- 重新**排列**-如果是 `true` ，使用者將能夠在資料表中拖曳資料行。
- **調整大小**-若為 `true` ，使用者將能夠拖曳資料行標頭來調整資料行的大小。
- 資料**行大小調整**-控制資料表自動調整資料行大小的方式。
- 醒目提示-控制選取資料格時，資料表所使用的**反白**顯示類型。
- **替代資料列**-如果 `true` 其他資料列會有不同的背景色彩。
- **水準格線**-選取在資料格之間水準繪製的框線類型。
- **垂直格線**-選取在資料格之間垂直繪製的框線類型。
- **方格色彩**-設定儲存格框線色彩。
- **背景**-設定儲存格背景色彩。
- **選取專案**-可讓您控制使用者如何在資料表中選取儲存格，如下所示：
  - **多個**-如果 `true` ，使用者可以選取多個資料列和資料行。
  - 資料**行**-如果 `true` 是，使用者可以選取資料行。
  - **輸入 select** -如果 `true` ，使用者可以輸入字元來選取資料列。
  - **空白**-如果 `true` 不需要使用者選取資料列或資料行，則資料表完全不允許選取。
- **自動儲存-資料表**格式的名稱會自動儲存在底下。
- 資料**行資訊**-若為，則會 `true` 自動儲存資料行的順序和寬度。
- **分行符號**-選取儲存格處理分行符號的方式。
- **截斷最後可見行**-如果 `true` ，資料格中的儲存格將會在其界限內無法容納。

> [!IMPORTANT]
> 除非您要維護舊版 Xamarin. Mac 應用程式，否則 `NSView` 應該將基礎資料表視圖用於以 `NSCell` 資料表為基礎的資料表。 `NSCell`被視為舊版，可能不會繼續支援。

在 [**介面**階層架構] 中選取資料表資料行，並在屬性偵測**器**中提供下列屬性：

[![](table-view-images/edit06.png "The Attribute Inspector")](table-view-images/edit06.png#lightbox)

- **Title** -設定資料行的標題。
- **對齊**-設定資料格內的文字對齊方式。
- **標題字型**-選取儲存格的標頭文字字型。
- **排序索引鍵**-這是用來排序資料行中資料的索引鍵。 如果使用者無法排序此資料行，請保留空白。
- **選取器**-這是用來執行排序的**動作**。 如果使用者無法排序此資料行，請保留空白。
- **Order** -這是資料行資料的排序次序。
- 重設**大小**-選取資料行調整大小的類型。
- **可編輯**-如果是 `true` ，使用者可以編輯以資料格為基礎的資料表中的資料格。
- **Hidden** -若為 `true` ，則隱藏資料行。

您也可以將資料行的控點（垂直置中在資料行右側）拖曳到左邊或右方，以調整其大小。

讓我們選取資料表視圖中的每個資料行，並提供第一個資料行的**標題** `Product` 和第二個數據行 `Details` 。

在介面階層中選取資料表資料格視圖（ `NSTableViewCell` ），**屬性偵測器**中可使用下列屬性： **Interface Hierarchy**

[![](table-view-images/edit07.png "The Attribute Inspector")](table-view-images/edit07.png#lightbox)

這些是標準視圖的所有屬性。 您也可以選擇在這裡調整此資料行的資料列大小。

在 [介面階層架構] 中選取 [資料表視圖] 資料格（根據預設，這是 `NSTextField` ），**屬性偵測器**中可使用下列屬性： **Interface Hierarchy**

[![](table-view-images/edit08.png "The Attribute Inspector")](table-view-images/edit08.png#lightbox)

您將會在這裡設定標準文字欄位的所有屬性。 根據預設，標準文字欄位是用來顯示資料行中儲存格的資料。

在介面階層中選取資料表資料格視圖（ `NSTableFieldCell` ），**屬性偵測器**中可使用下列屬性： **Interface Hierarchy**

[![](table-view-images/edit09.png "The Attribute Inspector")](table-view-images/edit09.png#lightbox)

以下是最重要的設定：

- **版面**配置-選取此資料行中的儲存格的配置方式。
- **使用單行模式**-如果 `true` 是，則資料格限制為單一行。
- **第一個運行**時間配置寬度-如果為 `true` ，當第一次執行應用程式時，儲存格會偏好為其設定的寬度（手動或自動）。
- **動作**-控制針對資料格傳送編輯**動作**的時機。
- **行為**-定義是否可選取或編輯儲存格。
- **Rtf** -如果 `true` ，資料格可以顯示格式化和樣式的文字。
- **復原**-如果資料 `true` 格會承擔其復原行為的責任。

在介面階層架構的資料表資料行底部，選取資料表資料格視圖（ `NSTableFieldCell` ）： **Interface Hierarchy**

[![](table-view-images/edit10.png "Selecting the Table Cell View")](table-view-images/edit10.png#lightbox)

這可讓您針對針對指定資料行所建立的所有資料格，編輯當做基底_模式_使用的資料表資料格視圖。

<a name="Adding_Actions_and_Outlets"></a>

### <a name="adding-actions-and-outlets"></a>新增動作和輸出

就像任何其他的 Cocoa UI 控制項一樣，我們必須使用**動作**和**輸出**（根據所需的功能），將資料表視圖及其資料行和資料格公開至 c # 程式碼。

對於我們想要公開的任何資料表 View 元素，此程式都是相同的：

1. 切換至 [**助理編輯器**]，並確定 `ViewController.h` 已選取檔案： 

    [![](table-view-images/edit11.png "The Assistant Editor")](table-view-images/edit11.png#lightbox)
2. 從**介面**階層中選取 [資料表] 視圖，並按一下 [控制項]，並將其拖曳至檔案 `ViewController.h` 。
3. 建立名**Outlet**為之資料表視圖的輸出 `ProductTable` ： 

    [![](table-view-images/edit13.png "Configuring an Outlet")](table-view-images/edit13.png#lightbox)
4. 建立 [資料表] 資料行的**輸出**，也稱為 `ProductColumn` `DetailsColumn` ： 

    [![](table-view-images/edit14.png "Configuring an Outlet")](table-view-images/edit14.png#lightbox)
5. 儲存您所做的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

接下來，我們會在應用程式執行時，撰寫程式碼來顯示資料表的一些資料。

<a name="Populating_the_Table_View"></a>

## <a name="populating-the-table-view"></a>填入資料表視圖

藉由在 Interface Builder 中設計並透過**插座**公開的資料表視圖，接下來我們需要建立 c # 程式碼來填入它。

首先，讓我們建立新的 `Product` 類別來保存個別資料列的資訊。 在 [**方案總管**中，以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新檔案 ...** ]。選取 **[一般] [**  >  **空白類別**]，輸入 `Product` 作為 [**名稱**]，然後按一下 [**新增**] 按鈕：

[![](table-view-images/populate01.png "Creating an empty class")](table-view-images/populate01.png#lightbox)

使檔案 `Product.cs` 看起來如下所示：

```csharp
using System;

namespace MacTables
{
  public class Product
  {
    #region Computed Properties
    public string Title { get; set;} = "";
    public string Description { get; set;} = "";
    #endregion

    #region Constructors
    public Product ()
    {
    }

    public Product (string title, string description)
    {
      this.Title = title;
      this.Description = description;
    }
    #endregion
  }
}

```

接下來，我們必須建立的子類別， `NSTableDataSource` 以提供所要求之資料表的資料。 在 [**方案總管**中，以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新檔案 ...** ]。選取 **[一般] [**  >  **空白類別**]，輸入 `ProductTableDataSource` 作為 [**名稱**]，然後按一下 [**新增**] 按鈕。

編輯檔案 `ProductTableDataSource.cs` ，使其看起來如下所示：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
  public class ProductTableDataSource : NSTableViewDataSource
  {
    #region Public Variables
    public List<Product> Products = new List<Product>();
    #endregion

    #region Constructors
    public ProductTableDataSource ()
    {
    }
    #endregion

    #region Override Methods
    public override nint GetRowCount (NSTableView tableView)
    {
      return Products.Count;
    }
    #endregion
  }
}

```

這個類別具有資料表視圖專案的儲存體，並會覆寫 `GetRowCount` 以傳回資料表中的資料列數目。

最後，我們需要建立的子類別， `NSTableDelegate` 以提供資料表的行為。 在 [**方案總管**中，以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新檔案 ...** ]。選取 **[一般] [**  >  **空白類別**]，輸入 `ProductTableDelegate` 作為 [**名稱**]，然後按一下 [**新增**] 按鈕。

編輯檔案 `ProductTableDelegate.cs` ，使其看起來如下所示：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
  public class ProductTableDelegate: NSTableViewDelegate
  {
    #region Constants 
    private const string CellIdentifier = "ProdCell";
    #endregion

    #region Private Variables
    private ProductTableDataSource DataSource;
    #endregion

    #region Constructors
    public ProductTableDelegate (ProductTableDataSource datasource)
    {
      this.DataSource = datasource;
    }
    #endregion

    #region Override Methods
    public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
    {
      // This pattern allows you reuse existing views when they are no-longer in use.
      // If the returned view is null, you instance up a new view
      // If a non-null view is returned, you modify it enough to reflect the new data
      NSTextField view = (NSTextField)tableView.MakeView (CellIdentifier, this);
      if (view == null) {
        view = new NSTextField ();
        view.Identifier = CellIdentifier;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = false;
      }

      // Setup view based on the column selected
      switch (tableColumn.Title) {
      case "Product":
        view.StringValue = DataSource.Products [(int)row].Title;
        break;
      case "Details":
        view.StringValue = DataSource.Products [(int)row].Description;
        break;
      }

      return view;
    }
    #endregion
  }
}
```

當我們建立的實例時 `ProductTableDelegate` ，我們也 `ProductTableDataSource` 會傳入可提供資料表資料的實例。 `GetViewForItem`方法會負責傳回 view （data），以顯示 [提供資料行] 和 [資料列] 的資料格。 可能的話，如果不需要建立新的視圖，就會重複使用現有的視圖來顯示儲存格。

若要填入資料表，讓我們編輯檔案，讓 `ViewController.cs` `AwakeFromNib` 方法看起來如下所示：

```csharp
public override void AwakeFromNib ()
{
  base.AwakeFromNib ();

  // Create the Product Table Data Source and populate it
  var DataSource = new ProductTableDataSource ();
  DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
  DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
  DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));

  // Populate the Product Table
  ProductTable.DataSource = DataSource;
  ProductTable.Delegate = new ProductTableDelegate (DataSource);
}
```

如果我們執行應用程式，則會顯示下列內容：

[![](table-view-images/populate02.png "A sample app run")](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column"></a>

## <a name="sorting-by-column"></a>依資料行排序

讓我們允許使用者按一下資料行標頭來排序資料表中的資料。 首先，按兩下檔案， `Main.storyboard` 在 Interface Builder 中開啟檔案進行編輯。 針對選取 `Product` 器選取資料行，並輸入做為 `Title` **排序關鍵字**， `compare:` 然後選取**Selector** `Ascending` **訂單**：

[![](table-view-images/sort01.png "Setting the sort key")](table-view-images/sort01.png#lightbox)

針對選取 `Details` 器選取資料行，並輸入做為 `Description` **排序關鍵字**， `compare:` 然後選取**Selector** `Ascending` **訂單**：

[![](table-view-images/sort02.png "Setting the sort key")](table-view-images/sort02.png#lightbox)

儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

現在讓我們編輯檔案 `ProductTableDataSource.cs` ，並新增下列方法：

```csharp
public void Sort(string key, bool ascending) {

  // Take action based on key
  switch (key) {
  case "Title":
    if (ascending) {
      Products.Sort ((x, y) => x.Title.CompareTo (y.Title));
    } else {
      Products.Sort ((x, y) => -1 * x.Title.CompareTo (y.Title));
    }
    break;
  case "Description":
    if (ascending) {
      Products.Sort ((x, y) => x.Description.CompareTo (y.Description));
    } else {
      Products.Sort ((x, y) => -1 * x.Description.CompareTo (y.Description));
    }
    break;
  }

}

public override void SortDescriptorsChanged (NSTableView tableView, NSSortDescriptor[] oldDescriptors)
{
  // Sort the data
  if (oldDescriptors.Length > 0) {
    // Update sort
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
  } else {
    // Grab current descriptors and update sort
    NSSortDescriptor[] tbSort = tableView.SortDescriptors; 
    Sort (tbSort[0].Key, tbSort[0].Ascending); 
  }
      
  // Refresh table
  tableView.ReloadData ();
}
```

`Sort`方法可讓我們根據指定的 `Product` 類別欄位，以遞增或遞減順序來排序資料來源中的資料。 `SortDescriptorsChanged`每次在資料行標題上按下時，就會呼叫覆寫的方法。 它會傳遞我們在 Interface Builder 中設定的索引**鍵值**，以及該資料行的排序次序。

如果我們執行應用程式並按一下資料行標頭，資料列就會依照該資料行排序：

[![](table-view-images/sort03.png "An example app run")](table-view-images/sort03.png#lightbox)

<a name="Row_Selection"></a>

## <a name="row-selection"></a>資料列選取

如果您想要允許使用者選取單一資料列，請按兩下該檔案， `Main.storyboard` 在 Interface Builder 中開啟檔案進行編輯。 選取**介面**階層架構中的資料表視圖，並取消選取**屬性偵測器**中的 [**多個**] 核取方塊：

[![](table-view-images/select01.png "The Attribute Inspector")](table-view-images/select01.png#lightbox)

儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

接著，編輯檔案 `ProductTableDelegate.cs` 並新增下列方法：

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

這可讓使用者選取資料表視圖中的任何單一資料列。 如果您不想讓使用者能夠選取任何資料列，請針對 `false` `ShouldSelectRow` 您不想讓使用者選取的任何資料列，傳回 `false` 。

資料表視圖（ `NSTableView` ）包含下列使用資料列選取的方法：

- `DeselectRow(nint)`-取消選擇資料表中的指定資料列。
- `SelectRow(nint,bool)`-選取指定的資料列。 `false`針對第二個參數傳遞，一次只選取一個資料列。
- `SelectedRow`-傳回在資料表中選取的目前資料列。
- `IsRowSelected(nint)`- `true` 如果已選取指定的資料列，則傳回。

<a name="Multiple_Row_Selection"></a>

## <a name="multiple-row-selection"></a>多重資料列選取

如果您想要允許使用者選取多個資料列，請按兩下該檔案， `Main.storyboard` 在 Interface Builder 中開啟檔案進行編輯。 選取**介面**階層中的 [資料表] 視圖，然後勾選 [屬性偵測**器**] 中的 [**多個**] 核取方塊：

[![](table-view-images/select02.png "The Attribute Inspector")](table-view-images/select02.png#lightbox)

儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

接著，編輯檔案 `ProductTableDelegate.cs` 並新增下列方法：

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

這可讓使用者選取資料表視圖中的任何單一資料列。 如果您不想讓使用者能夠選取任何資料列，請針對 `false` `ShouldSelectRow` 您不想讓使用者選取的任何資料列，傳回 `false` 。

資料表視圖（ `NSTableView` ）包含下列使用資料列選取的方法：

- `DeselectAll(NSObject)`-取消選擇資料表中的所有資料列。 `this`針對第一個參數使用，以在執行選取的物件中傳送。 
- `DeselectRow(nint)`-取消選擇資料表中的指定資料列。
- `SelectAll(NSobject)`-選取資料表中的所有資料列。 `this`針對第一個參數使用，以在執行選取的物件中傳送。
- `SelectRow(nint,bool)`-選取指定的資料列。 `false`針對第二個參數，請清除選取範圍，並只選取單一資料列，傳遞 `true` 以擴充選取範圍並包含此資料列。
- `SelectRows(NSIndexSet,bool)`-選取指定的資料列集。 `false`針對第二個參數，請清除選取專案，然後只選取其中一個資料列，傳遞 `true` 以擴充選取範圍並包含這些資料列。
- `SelectedRow`-傳回在資料表中選取的目前資料列。
- `SelectedRows`-傳回， `NSIndexSet` 其中包含所選資料列的索引。
- `SelectedRowCount`-傳回選取的資料列數目。
- `IsRowSelected(nint)`- `true` 如果已選取指定的資料列，則傳回。

<a name="Type_to_Select_Row"></a>

## <a name="type-to-select-row"></a>輸入以選取資料列

如果您想要允許使用者輸入已選取資料表視圖的字元，並選取含有該字元的第一個資料列，請按兩下該檔案， `Main.storyboard` 在 Interface Builder 中將其開啟以進行編輯。 選取**介面**階層架構中的資料表視圖，然後勾選**屬性偵測器**中的 [**類型選取**] 核取方塊：

[![](table-view-images/type01.png "Setting the selection type")](table-view-images/type01.png#lightbox)

儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

現在讓我們編輯檔案 `ProductTableDelegate.cs` ，並新增下列方法：

```csharp
public override nint GetNextTypeSelectMatch (NSTableView tableView, nint startRow, nint endRow, string searchString)
{
  nint row = 0;
  foreach(Product product in DataSource.Products) {
    if (product.Title.Contains(searchString)) return row;

    // Increment row counter
    ++row;
  }

  // If not found select the first row
  return 0;
}
```

`GetNextTypeSelectMatch`方法會使用指定的 `searchString` ，並傳回 `Product` 在其中具有該字串的第一個資料列 `Title` 。

如果我們執行應用程式並輸入一個字元，就會選取一個資料列：

[![](table-view-images/type02.png "A sample app run")](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns"></a>

## <a name="reordering-columns"></a>重新排列資料行

如果您想要允許使用者拖曳 [資料表] 視圖中的 [重新排列資料行]，請按兩下檔案將 `Main.storyboard` 其開啟，以在 Interface Builder 中進行編輯。 選取**介面**階層架構中的資料表視圖，然後勾選**屬性偵測器**中的 [重新**排列**] 核取方塊：

[![](table-view-images/reorder01.png "The Attribute Inspector")](table-view-images/reorder01.png#lightbox)

如果我們提供 [**自動**儲存] 屬性的值，並核取 [資料**行資訊**] 欄位，則我們對資料表的版面配置所做的任何變更，都會在下次執行應用程式時自動儲存並還原。

儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

現在讓我們編輯檔案 `ProductTableDelegate.cs` ，並新增下列方法：

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
  return true;
}
```

`ShouldReorder`方法應該 `true` 會針對想要允許將其拖曳至的任何資料行傳回，否則會傳回 `newColumnIndex` `false` ;

如果我們執行應用程式，我們可以拖曳資料行標頭來重新排列資料行：

[![](table-view-images/reorder02.png "An example of the reordered columns")](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells"></a>

## <a name="editing-cells"></a>編輯資料格

如果您想要允許使用者編輯指定資料格的值，請編輯檔案 `ProductTableDelegate.cs` 並變更方法，如下所示 `GetViewForItem` ：

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{
  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTextField view = (NSTextField)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTextField ();
    view.Identifier = tableColumn.Title;
    view.BackgroundColor = NSColor.Clear;
    view.Bordered = false;
    view.Selectable = false;
    view.Editable = true;

    view.EditingEnded += (sender, e) => {
          
      // Take action based on type
      switch(view.Identifier) {
      case "Product":
        DataSource.Products [(int)view.Tag].Title = view.StringValue;
        break;
      case "Details":
        DataSource.Products [(int)view.Tag].Description = view.StringValue;
        break; 
      }
    };
  }

  // Tag view
  view.Tag = row;

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.StringValue = DataSource.Products [(int)row].Title;
    break;
  case "Details":
    view.StringValue = DataSource.Products [(int)row].Description;
    break;
  }

  return view;
}
```

現在，如果我們執行應用程式，使用者就可以編輯資料表視圖中的資料格：

[![](table-view-images/editing01.png "An example of editing a cell")](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views"></a>

## <a name="using-images-in-table-views"></a>在資料表視圖中使用影像

若要將影像加入至中的儲存格 `NSTableView` ，您必須變更資料表視圖的方法傳回資料的方式， `NSTableViewDelegate's` `GetViewForItem` 以使用， `NSTableCellView` 而不是一般的 `NSTextField` 。 例如：

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTableCellView ();
    if (tableColumn.Title == "Product") {
      view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
      view.AddSubview (view.ImageView);
      view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
    } else {
      view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
    }
    view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
    view.AddSubview (view.TextField);
    view.Identifier = tableColumn.Title;
    view.TextField.BackgroundColor = NSColor.Clear;
    view.TextField.Bordered = false;
    view.TextField.Selectable = false;
    view.TextField.Editable = true;

    view.TextField.EditingEnded += (sender, e) => {

      // Take action based on type
      switch(view.Identifier) {
      case "Product":
        DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
        break;
      case "Details":
        DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
        break; 
      }
    };
  }

  // Tag view
  view.TextField.Tag = row;

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tags.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    break;
  case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    break;
  }

  return view;
}
```

如需詳細資訊，請參閱我們使用[影像](~/mac/app-fundamentals/image.md)檔中的搭配[資料表視圖使用影像](~/mac/app-fundamentals/image.md)一節。

<a name="Adding-a-Delete-Button-to-a-Row"></a>

## <a name="adding-a-delete-button-to-a-row"></a>將 [刪除] 按鈕加入至資料列

根據您應用程式的需求，有時候您可能需要為數據表中的每個資料列提供 [動作] 按鈕。 例如，讓我們展開上面所建立的資料表視圖範例，以在每個資料列上包含 [**刪除**] 按鈕。

首先， `Main.storyboard` 在 Xcode 的 Interface Builder 中編輯，選取資料表視圖，並將資料行數目增加為三（3）。 接下來，將新資料行的**標題**變更為 `Action` ：

[![](table-view-images/delete01.png "Editing the column name")](table-view-images/delete01.png#lightbox)

將變更儲存至分鏡腳本，並返回 Visual Studio for Mac 以同步處理變更。

接著，編輯檔案 `ViewController.cs` ，並新增下列公用方法：

```csharp
public void ReloadTable ()
{
  ProductTable.ReloadData ();
}
```

在相同的檔案中，修改在方法內建立新資料表視圖委派的方式，如下所示 `ViewDidLoad` ：

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

現在，編輯檔案 `ProductTableDelegate.cs` 以包含對 View 控制器的私用連接，並在建立委派的新實例時，將控制器當做參數使用：

```csharp
#region Private Variables
private ProductTableDataSource DataSource;
private ViewController Controller;
#endregion

#region Constructors
public ProductTableDelegate (ViewController controller, ProductTableDataSource datasource)
{
  this.Controller = controller;
  this.DataSource = datasource;
}
#endregion
```

接下來，在類別中新增下列新的私用方法：

```csharp
private void ConfigureTextField (NSTableCellView view, nint row)
{
  // Add to view
  view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
  view.AddSubview (view.TextField);

  // Configure
  view.TextField.BackgroundColor = NSColor.Clear;
  view.TextField.Bordered = false;
  view.TextField.Selectable = false;
  view.TextField.Editable = true;

  // Wireup events
  view.TextField.EditingEnded += (sender, e) => {

    // Take action based on type
    switch (view.Identifier) {
    case "Product":
      DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
      break;
    case "Details":
      DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
      break;
    }
  };

  // Tag view
  view.TextField.Tag = row;
}
```

這會採用先前在方法中完成的所有文字視圖設定 `GetViewForItem` ，並將它們放在單一的可呼叫位置（因為資料表的最後一個資料行不包含文字視圖，而是按鈕）。

最後，編輯 `GetViewForItem` 方法，使其看起來如下所示：

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTableCellView ();

    // Configure the view
    view.Identifier = tableColumn.Title;

    // Take action based on title
    switch (tableColumn.Title) {
    case "Product":
      view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
      view.AddSubview (view.ImageView);
      view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
      ConfigureTextField (view, row);
      break;
    case "Details":
      view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
      ConfigureTextField (view, row);
      break;
    case "Action":
      // Create new button
      var button = new NSButton (new CGRect (0, 0, 81, 16));
      button.SetButtonType (NSButtonType.MomentaryPushIn);
      button.Title = "Delete";
      button.Tag = row;

      // Wireup events
      button.Activated += (sender, e) => {
        // Get button and product
        var btn = sender as NSButton;
        var product = DataSource.Products [(int)btn.Tag];

        // Configure alert
        var alert = new NSAlert () {
          AlertStyle = NSAlertStyle.Informational,
          InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
          MessageText = $"Delete {product.Title}?",
        };
        alert.AddButton ("Cancel");
        alert.AddButton ("Delete");
        alert.BeginSheetForResponse (Controller.View.Window, (result) => {
          // Should we delete the requested row?
          if (result == 1001) {
            // Remove the given row from the dataset
            DataSource.Products.RemoveAt((int)btn.Tag);
            Controller.ReloadTable ();
          }
        });
      };

      // Add to view
      view.AddSubview (button);
      break;
    }

  }

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tag.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    view.TextField.Tag = row;
    break;
  case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    view.TextField.Tag = row;
    break;
  case "Action":
    foreach (NSView subview in view.Subviews) {
      var btn = subview as NSButton;
      if (btn != null) {
        btn.Tag = row;
      }
    }
    break;
  }

  return view;
}
```

讓我們更詳細地探討這段程式碼的幾個部分。 首先，如果要建立新的， `NSTableViewCell` 則會根據資料行的名稱來採取動作。 針對前兩個數據行（**Product**和**Details**），會呼叫新的 `ConfigureTextField` 方法。

針對 [**動作**] 資料行，會建立新的，並將其 `NSButton` 新增至資料格做為子視圖：

```csharp
// Create new button
var button = new NSButton (new CGRect (0, 0, 81, 16));
button.SetButtonType (NSButtonType.MomentaryPushIn);
button.Title = "Delete";
button.Tag = row;
...

// Add to view
view.AddSubview (button);
```

按鈕的 `Tag` 屬性可用來保存目前正在處理之資料列的數目。 稍後當使用者要求在按鈕的事件中刪除資料列時，將會使用這個數位 `Activated` ：

```csharp
// Wireup events
button.Activated += (sender, e) => {
  // Get button and product
  var btn = sender as NSButton;
  var product = DataSource.Products [(int)btn.Tag];

  // Configure alert
  var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
    MessageText = $"Delete {product.Title}?",
  };
  alert.AddButton ("Cancel");
  alert.AddButton ("Delete");
  alert.BeginSheetForResponse (Controller.View.Window, (result) => {
    // Should we delete the requested row?
    if (result == 1001) {
      // Remove the given row from the dataset
      DataSource.Products.RemoveAt((int)btn.Tag);
      Controller.ReloadTable ();
    }
  });
};
```

在事件處理常式開始時，我們會取得位於給定資料表資料列上的按鈕和產品。 然後會向使用者顯示警示，確認資料列刪除。 如果使用者選擇刪除資料列，則會從資料來源中移除給定的資料列，並重載資料表：

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

最後，如果要重複使用資料表 View 資料格，而不是建立新的，則下列程式碼會根據正在處理的資料行來設定它：

```csharp
// Setup view based on the column selected
switch (tableColumn.Title) {
case "Product":
  view.ImageView.Image = NSImage.ImageNamed ("tag.png");
  view.TextField.StringValue = DataSource.Products [(int)row].Title;
  view.TextField.Tag = row;
  break;
case "Details":
  view.TextField.StringValue = DataSource.Products [(int)row].Description;
  view.TextField.Tag = row;
  break;
case "Action":
  foreach (NSView subview in view.Subviews) {
    var btn = subview as NSButton;
    if (btn != null) {
      btn.Tag = row;
    }
  }
  break;
}

```

在 [**動作**] 資料行中，會掃描所有子視圖 `NSButton` ，直到找到為止，然後將它的 `Tag` 屬性更新為指向目前的資料列。

進行這些變更之後，當應用程式執行時，每個資料列都會有 [**刪除**] 按鈕：

[![](table-view-images/delete02.png "The table view with deletion buttons")](table-view-images/delete02.png#lightbox)

當使用者按一下 [**刪除**] 按鈕時，將會顯示警示，詢問他們是否要刪除指定的資料列：

[![](table-view-images/delete03.png "A delete row alert")](table-view-images/delete03.png#lightbox)

如果使用者選擇 [刪除]，將會移除資料列，而且會重新繪製資料表：

[![](table-view-images/delete04.png "The table after the row is deleted")](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views"></a>

## <a name="data-binding-table-views"></a>資料系結資料表的視圖

藉由在 Xamarin. Mac 應用程式中使用索引鍵/值編碼和資料系結技術，您可以大幅減少必須撰寫和維護的程式碼數量，以填入和使用 UI 元素。 您也可以進一步將您的支援資料（_資料模型_）與您的前端使用者介面（_模型視圖控制器_）分離，讓您更容易維護、更有彈性的應用程式設計。

索引鍵/值編碼（KVC）是一種機制，可讓您間接存取物件的屬性，使用索引鍵（特殊格式的字串）來識別屬性，而不是透過執行個體變數或存取子方法（）來存取它們 `get/set` 。 藉由在您的 Xamarin. Mac 應用程式中執行符合索引鍵/值的存取子，您可以存取其他 macOS 功能，例如索引鍵/值觀察（KVO）、資料系結、核心資料、Cocoa 系結和 scriptability。

如需詳細資訊，請參閱資料系結[和索引鍵-值編碼](~/mac/app-fundamentals/databinding.md)檔的[資料表視圖資料](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding)系結一節。

<a name="Summary"></a>

## <a name="summary"></a>總結

本文已詳細探討如何在 Xamarin. Mac 應用程式中使用資料表視圖。 我們已瞭解資料表視圖的不同類型和用法，如何在 Xcode 的 Interface Builder 中建立和維護資料表視圖，以及如何使用 c # 程式碼中的資料表瀏覽器。

## <a name="related-links"></a>相關連結

- [MacTables （範例）](https://docs.microsoft.com/samples/xamarin/mac-samples/mactables)
- [MacImages (範例)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [大綱檢視](~/mac/user-interface/outline-view.md)
- [來源清單](~/mac/user-interface/source-list.md)
- [資料繫結和機碼值編碼](~/mac/app-fundamentals/databinding.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)
