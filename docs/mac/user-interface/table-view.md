---
title: Xamarin 中的資料表視圖
description: 本文說明如何在 Xamarin 應用程式中使用資料表視圖。 它描述如何在 Xcode 中建立資料表視圖，並在程式碼中 Interface Builder 及與其互動。
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: d8c5cc10b4bce507f7a1d7896a41730745b08cbd
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431636"
---
# <a name="table-views-in-xamarinmac"></a>Xamarin 中的資料表視圖

_本文說明如何在 Xamarin 應用程式中使用資料表視圖。它描述如何在 Xcode 中建立資料表視圖，並在程式碼中 Interface Builder 及與其互動。_

在 Xamarin 應用程式中使用 c # 和 .NET 時，您可以存取開發人員在 *c* 和 *Xcode* 中運作的相同資料表視圖。 因為 Xamarin 會直接與 Xcode 整合，所以您可以使用 Xcode 的 _Interface Builder_ 來建立和維護資料表視圖 (或選擇性地在 c # 程式碼) 中直接建立它們。

資料表視圖會以表格格式顯示資料，其中包含多個資料列中的一或多個資料行。 根據所建立的資料表檢視類型而定，使用者可以依資料行排序、重新組織資料行、加入資料行、移除資料行或編輯資料表中包含的資料。

[![範例資料表](table-view-images/intro01.png)](table-view-images/intro01.png#lightbox)

在本文中，我們將討論在 Xamarin. Mac 應用程式中使用資料表視圖的基本概念。 強烈建議您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 文章，特別是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 和 [輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions) 章節的簡介，因為它涵蓋了我們將在本文中使用的重要概念和技術。

您可能會想要看看如何將 [c # 類別/方法公開到](~/mac/internals/how-it-works.md) [Xamarin 內部](~/mac/internals/how-it-works.md) 檔的目標 C 區段，它會說明 `Register` `Export` 用來將 C # 類別連接到目標 c 物件和 UI 元素的和命令。

<a name="Introduction_to_Table_Views"></a>

## <a name="introduction-to-table-views"></a>資料表視圖簡介

資料表視圖會以表格格式顯示資料，其中包含多個資料列中的一或多個資料行。 資料表視圖會顯示在捲軸 (`NSScrollView`) ，並從 macOS 10.7 開始，您可以使用任何 `NSView` () 的資料格 `NSCell` 來顯示資料列和資料行。 話雖如此，您仍然可以使用子 `NSCell` 類別 `NSTableCellView` 和建立自訂的資料列和資料行。

資料表視圖不會儲存本身的資料，而是依賴資料來源 (`NSTableViewDataSource`) ，視需要提供所需的資料列和資料行。

您可以藉由提供資料表視圖委派的子類別來自訂資料表視圖的行為 `NSTableViewDelegate` ， () 來支援資料表資料行管理、輸入來選取功能、選取和編輯資料列、自訂追蹤，以及個別資料行和資料列的自訂視圖。

建立資料表視圖時，Apple 建議下列各項：

- 允許使用者按一下資料行標頭來排序資料表。
- 建立資料行標頭，這是用來描述該資料行中所顯示之資料的名詞或簡短名詞片語。

如需詳細資訊，請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[內容視圖](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1)一節。

<a name="Creating-and-Maintaining-Table-Views-in-Xcode"></a>

## <a name="creating-and-maintaining-table-views-in-xcode"></a>在 Xcode 中建立和維護資料表視圖

當您建立新的 Xamarin Cocoa 應用程式時，預設會取得標準空白的視窗。 此視窗定義于 `.storyboard` 自動包含在專案中的檔案。 若要編輯您的 windows 設計，請在 [ **方案總管**中，按兩下該檔案 `Main.storyboard` ：

[![選取主要分鏡腳本](table-view-images/edit01.png)](table-view-images/edit01.png#lightbox)

這會開啟 Xcode Interface Builder 中的視窗設計：

[![在 Xcode 中編輯 UI](table-view-images/edit02.png)](table-view-images/edit02.png#lightbox)

`table`在 [連結**庫偵測器] 的**[搜尋] 方塊中輸入，可讓您更輕鬆地尋找資料表視圖控制項：

[![從程式庫選取資料表視圖](table-view-images/edit03.png)](table-view-images/edit03.png#lightbox)

將 [資料表] 視圖拖曳至 [ **介面編輯器**] 中的 [view controller]，使其填滿 view controller 的內容區域，然後將它設定為在 [ **條件約束編輯器**] 中，使用視窗縮小和成長的位置：

[![編輯條件約束](table-view-images/edit04.png)](table-view-images/edit04.png#lightbox)

在 **介面** 階層中選取資料表視圖， **屬性偵測器**中會提供下列屬性：

[![屬性偵測器](table-view-images/edit05.png)](table-view-images/edit05.png#lightbox)

- **內容模式** -可讓您在 `NSView` 資料列和資料行中，使用 [視圖] () 或資料格 (`NSCell`) 來顯示資料。 從 macOS 10.7 開始，您應該使用 Views。
- **浮動群組資料列** -如果 `true` ，資料表視圖會將群組的資料格繪製成浮動的資料格。
- 資料**行**-定義所顯示的資料行數目。
- **標頭** -如果 `true` ，資料行將會有標頭。
- 重新**排列**-如果 `true` ，使用者將能夠拖曳資料表中的資料行。
- 重設**大小**-如果 `true` ，使用者將能夠拖曳資料行標頭來調整資料行的大小。
- 資料**行**調整大小-控制資料表將如何自動調整資料行的大小。
- **醒目** 提示-控制選取資料格時，資料表所使用的反白顯示類型。
- **替代資料列** -如果 `true` ，其他資料列則會有不同的背景色彩。
- **水準方格** -選取在儲存格之間水準繪製的框線類型。
- **垂直方格** -選取垂直儲存格之間繪製的框線類型。
- **方格色彩** ：設定資料格框線色彩。
- **背景** -設定資料格背景色彩。
- **選取專案** -可讓您控制使用者可以如何選取資料表中的資料格，如下所示：
  - **多個** -如果 `true` ，使用者可以選取多個資料列和資料行。
  - 資料**行**-如果 `true` ，使用者可以選取資料行。
  - **輸入 select** -如果 `true` ，使用者可以輸入字元來選取資料列。
  - **空白** -如果 `true` 不需要使用者選取資料列或資料行，則資料表完全不允許任何選取。
- **自動儲存-資料表** 格式的名稱會自動儲存在下方。
- 資料**行資訊**-如果為，則會 `true` 自動儲存資料行的順序和寬度。
- **分行符號** -選取儲存格處理換行的方式。
- **截斷最後一個可見的行** -如果 `true` ，資料格中的資料格將會被截斷，無法放在其範圍內。

> [!IMPORTANT]
> 除非您要維護舊版的 Xamarin 應用程式，否則您 `NSView` 應該使用資料表視圖作為基礎的資料表 `NSCell` 。 `NSCell` 會被視為舊版，未來可能不會支援。

在 [ **介面** ] 階層中選取資料表資料行， **屬性偵測器**中會提供下列屬性：

[![屬性偵測器](table-view-images/edit06.png)](table-view-images/edit06.png#lightbox)

- **標題** -設定資料行的標題。
- **對齊** ：設定資料格內的文字對齊方式。
- **標題字型** -選取儲存格標頭文字的字型。
- **排序索引鍵** -這是用來排序資料行中資料的索引鍵。 如果使用者無法排序這個資料行，請保留空白。
- **選取器** -這是用來執行排序的 **動作** 。 如果使用者無法排序這個資料行，請保留空白。
- **Order** -是資料行資料的排序次序。
- 重設**大小**-選取資料行調整大小的類型。
- **可編輯** -如果 `true` ，使用者可以編輯資料格基礎資料表中的資料格。
- **Hidden** -如果 `true` 是，則資料行是隱藏的。

您也可以將資料行的控點拖曳 (垂直置中資料行右側) 左邊或右邊，以調整資料行的大小。

讓我們選取資料表視圖中的每個資料行，並提供第一個資料行的 **標題** `Product` 和第二個數據行 `Details` 。

選取 [資料表資料格視圖] (`NSTableViewCell` **介面** 階層中的) ， **屬性偵測器**中會提供下列屬性：

[![屬性偵測器](table-view-images/edit07.png)](table-view-images/edit07.png#lightbox)

這些都是標準視圖的所有屬性。 您也可以選擇在這裡調整此資料行的資料列大小。

選取資料表視圖資料格 (根據預設，這是 `NSTextField` **介面** 階層中的) ，而 **屬性偵測器**中提供下列屬性：

[![屬性偵測器](table-view-images/edit08.png)](table-view-images/edit08.png#lightbox)

您將在這裡設定標準文字欄位的所有屬性。 根據預設，標準文字欄位是用來顯示資料行中資料格的資料。

選取 [資料表資料格視圖] (`NSTableFieldCell` **介面** 階層中的) ， **屬性偵測器**中會提供下列屬性：

[![屬性偵測器](table-view-images/edit09.png)](table-view-images/edit09.png#lightbox)

以下是最重要的設定：

- 配置：選取此資料行中單元**格的配置**方式。
- **使用單行模式** -如果 `true` ，則資料格會限制為單一行。
- **第一個運行** 時間配置寬度-如果 `true` 是，儲存格會偏好為它設定的寬度 (手動或自動) 在應用程式第一次執行時顯示。
- **Action** -控制何時傳送儲存格的編輯 **動作** 。
- **行為** -定義資料格是否可選取或可編輯。
- **Rich Text** -如果 `true` ，資料格可以顯示格式化和樣式的文字。
- **復原** -如果是 `true` ，儲存格會承擔其復原行為的責任。

在介面階層中，選取資料表資料行底部的 [資料表資料格視圖] (`NSTableFieldCell`) ： **Interface Hierarchy**

[![選取資料表資料格視圖](table-view-images/edit10.png)](table-view-images/edit10.png#lightbox)

這可讓您編輯資料表資料格視圖，作為針對指定資料行所建立之所有資料格的基底 _模式_ 。

<a name="Adding_Actions_and_Outlets"></a>

### <a name="adding-actions-and-outlets"></a>新增動作和輸出

就像任何其他 Cocoa UI 控制項一樣，我們也需要根據) 所需的功能，使用 **動作** 和 **輸出** ，將我們的資料表視圖和資料行和資料格公開到 c # 程式碼 (。

針對我們想要公開的任何資料表視圖元素，此程式都相同：

1. 切換至 [ **助理編輯器** ]，並確定 `ViewController.h` 已選取檔案： 

    [![助理編輯器](table-view-images/edit11.png)](table-view-images/edit11.png#lightbox)
2. 從 **介面**階層中選取 [資料表] 視圖，然後按住 ctrl 並拖曳至檔案 `ViewController.h` 。
3. 建立名**Outlet**為的表格視圖的輸出 `ProductTable` ： 

    [![設定輸出](table-view-images/edit13.png)](table-view-images/edit13.png#lightbox)
4. 建立資料表資料行的 **輸出** ，也稱為 `ProductColumn` 和 `DetailsColumn` ： 

    [![設定輸出](table-view-images/edit14.png)](table-view-images/edit14.png#lightbox)
5. 儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

接下來，我們會撰寫程式碼，在應用程式執行時顯示資料表的一些資料。

<a name="Populating_the_Table_View"></a>

## <a name="populating-the-table-view"></a>填入資料表視圖

在 Interface Builder 中設計的資料表視圖，並透過 **輸出**公開，接下來我們需要建立 c # 程式碼來填入它。

首先，讓我們建立新的 `Product` 類別來保存個別資料列的資訊。 在 [**方案總管**中，以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新**檔案]。選取 **[一般**  >  **空白類別**]，輸入 `Product` **名稱**，然後按一下 [**新增**] 按鈕：

[![建立空白類別](table-view-images/populate01.png)](table-view-images/populate01.png#lightbox)

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

接下來，我們需要建立的子類別， `NSTableDataSource` 以在要求時提供資料表的資料。 在 [**方案總管**中，以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新**檔案]。選取 **[一般**  >  **空白類別**]，輸入 `ProductTableDataSource` **名稱**，然後按一下 [**新增**] 按鈕。

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

此類別具有資料表視圖專案的儲存體，並會覆寫 `GetRowCount` 以傳回資料表中的資料列數目。

最後，我們需要建立的子類別， `NSTableDelegate` 以提供資料表的行為。 在 [**方案總管**中，以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新**檔案]。選取 **[一般**  >  **空白類別**]，輸入 `ProductTableDelegate` **名稱**，然後按一下 [**新增**] 按鈕。

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

當我們建立的實例時 `ProductTableDelegate` ，我們也 `ProductTableDataSource` 會傳入提供資料表資料的實例。 `GetViewForItem`方法負責傳回 (資料) 的視圖，以顯示提供資料行和資料列的資料格。 如果可能的話，將會重複使用現有的視圖來顯示儲存格，如果您不需要建立新的 view。

若要填入資料表，讓我們編輯檔案，讓 `ViewController.cs` `AwakeFromNib` 方法看起來像下面這樣：

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

如果執行應用程式，則會顯示下列內容：

[![執行範例應用程式](table-view-images/populate02.png)](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column"></a>

## <a name="sorting-by-column"></a>依資料行排序

讓我們藉由按一下資料行標題，讓使用者排序資料表中的資料。 首先，按兩下檔案 `Main.storyboard` 將它開啟，以在 Interface Builder 中進行編輯。 針對選取 `Product` 器選取資料行，輸入 `Title` **排序關鍵字**， `compare:` 然後**Selector**選取 [ `Ascending` 作為**順序**]：

[![設定排序關鍵字](table-view-images/sort01.png)](table-view-images/sort01.png#lightbox)

針對選取 `Details` 器選取資料行，輸入 `Description` **排序關鍵字**， `compare:` 然後**Selector**選取 [ `Ascending` 作為**順序**]：

[![設定排序關鍵字](table-view-images/sort02.png)](table-view-images/sort02.png#lightbox)

儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

現在讓我們來編輯檔案 `ProductTableDataSource.cs` ，並新增下列方法：

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

`Sort`方法可讓我們根據指定的 `Product` 類別欄位，以遞增或遞減順序來排序資料來源中的資料。 `SortDescriptorsChanged`每次在資料行標題上按下滑鼠時，都會呼叫覆寫的方法。 它會傳遞我們在 Interface Builder 中設定的索引 **鍵值** ，以及該資料行的排序次序。

如果我們執行應用程式並按一下資料行標頭，資料列將會依該資料行排序：

[![執行範例應用程式](table-view-images/sort03.png)](table-view-images/sort03.png#lightbox)

<a name="Row_Selection"></a>

## <a name="row-selection"></a>選取資料列

如果您想要讓使用者選取單一資料列，請按兩下該檔案來開啟檔案，以便 `Main.storyboard` 在 Interface Builder 中進行編輯。 選取**介面**階層中的資料表視圖，並取消核取**屬性偵測器**中的**多個**核取方塊：

[![屬性偵測器](table-view-images/select01.png)](table-view-images/select01.png#lightbox)

儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

接著，編輯檔案 `ProductTableDelegate.cs` 並新增下列方法：

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

這可讓使用者選取資料表視圖中的任何單一資料列。 `false` `ShouldSelectRow` `false` 如果您不想讓使用者選取任何資料列，請針對您不想讓使用者選取的任何資料列，傳回。

資料表視圖 (`NSTableView`) 包含下列使用資料列選取的方法：

- `DeselectRow(nint)` -取消選擇資料表中的指定資料列。
- `SelectRow(nint,bool)` -選取指定的資料列。 傳遞 `false` 第二個參數，一次只能選取一個資料列。
- `SelectedRow` -傳回資料表中選取的目前資料列。
- `IsRowSelected(nint)` - `true` 如果選取了指定的資料列，就會傳回。

<a name="Multiple_Row_Selection"></a>

## <a name="multiple-row-selection"></a>多重資料列選取

如果您想要允許使用者選取多個資料列，請按兩下該檔案 `Main.storyboard` 以開啟該檔案，以便在 Interface Builder 中進行編輯。 選取 [**介面**] 階層中的 [資料表]，然後核取 [**屬性偵測器**] 中的 [**多**] 核取方塊：

[![屬性偵測器](table-view-images/select02.png)](table-view-images/select02.png#lightbox)

儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

接著，編輯檔案 `ProductTableDelegate.cs` 並新增下列方法：

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

這可讓使用者選取資料表視圖中的任何單一資料列。 `false` `ShouldSelectRow` `false` 如果您不想讓使用者選取任何資料列，請針對您不想讓使用者選取的任何資料列，傳回。

資料表視圖 (`NSTableView`) 包含下列使用資料列選取的方法：

- `DeselectAll(NSObject)` -取消選資料表中的所有資料列。 用於 `this` 第一個參數，以在執行選取的物件中傳送。 
- `DeselectRow(nint)` -取消選擇資料表中的指定資料列。
- `SelectAll(NSobject)` -選取資料表中的所有資料列。 用於 `this` 第一個參數，以在執行選取的物件中傳送。
- `SelectRow(nint,bool)` -選取指定的資料列。 傳遞 `false` 第二個參數則清除選取範圍，然後只選取單一資料列，傳遞 `true` 以延伸選取範圍並包含此資料列。
- `SelectRows(NSIndexSet,bool)` -選取指定的資料列集。 第二個參數的 [通過] 會 `false` 清除選取範圍，並只選取其中一個資料列，傳遞 `true` 以延伸選取範圍並包含這些資料列。
- `SelectedRow` -傳回資料表中選取的目前資料列。
- `SelectedRows` -傳回， `NSIndexSet` 其中包含所選取資料列的索引。
- `SelectedRowCount` -傳回選取的資料列數目。
- `IsRowSelected(nint)` - `true` 如果選取了指定的資料列，就會傳回。

<a name="Type_to_Select_Row"></a>

## <a name="type-to-select-row"></a>輸入以選取資料列

如果您想要允許使用者輸入已選取資料表視圖的字元，然後選取包含該字元的第一個資料列，請按兩下該檔案 `Main.storyboard` 以開啟該檔案，以便在 Interface Builder 中進行編輯。 選取 [**介面**] 階層中的 [資料表]，然後核取 [**屬性偵測器**] 中的 [**選取類型**] 核取方塊：

[![設定選取類型](table-view-images/type01.png)](table-view-images/type01.png#lightbox)

儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

現在讓我們來編輯檔案 `ProductTableDelegate.cs` ，並新增下列方法：

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

`GetNextTypeSelectMatch`方法會使用指定的 `searchString` ，並傳回第一個 `Product` 中有該字串的資料列 `Title` 。

如果執行應用程式並輸入字元，則會選取資料列：

[![執行範例應用程式](table-view-images/type02.png)](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns"></a>

## <a name="reordering-columns"></a>重新排列資料行

如果您想要允許使用者拖曳資料表視圖中的 [重新排列資料行]，請按兩下該檔案以開啟該檔案， `Main.storyboard` 以便在 Interface Builder 中進行編輯。 選取**介面**階層中的資料表視圖，並核取**屬性偵測器**中的 [重新**排列**] 核取方塊：

[![屬性偵測器](table-view-images/reorder01.png)](table-view-images/reorder01.png#lightbox)

如果我們提供 [ **自動** 儲存] 屬性的值，並檢查 [資料 **行資訊** ] 欄位，我們對資料表配置所做的任何變更都會自動儲存，並在下次執行應用程式時還原。

儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

現在讓我們來編輯檔案 `ProductTableDelegate.cs` ，並新增下列方法：

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
  return true;
}
```

`ShouldReorder`方法應該會 `true` 針對想要允許拖曳至的任何資料行傳回，否則會傳回 `newColumnIndex` `false` ;

如果執行應用程式，我們可以拖曳資料行標頭，以重新排列資料行的順序：

[![重新排序資料行的範例](table-view-images/reorder02.png)](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells"></a>

## <a name="editing-cells"></a>編輯資料格

如果您想要允許使用者編輯指定儲存格的值，請編輯該檔案 `ProductTableDelegate.cs` 並變更方法，如下所示 `GetViewForItem` ：

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

[![編輯儲存格的範例](table-view-images/editing01.png)](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views"></a>

## <a name="using-images-in-table-views"></a>在資料表視圖中使用影像

若要將影像包含在中的儲存格 `NSTableView` ，您必須變更資料表視圖的方法傳回資料的方式， `NSTableViewDelegate's` `GetViewForItem` 以使用 `NSTableCellView` 而非一般 `NSTextField` 。 例如：

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

如需詳細資訊，請參閱使用[影像](~/mac/app-fundamentals/image.md)檔的[使用影像與表格視圖](~/mac/app-fundamentals/image.md)。

<a name="Adding-a-Delete-Button-to-a-Row"></a>

## <a name="adding-a-delete-button-to-a-row"></a>將 [刪除] 按鈕新增至資料列

根據您的應用程式需求，有時候您可能需要為數據表中的每個資料列提供 [動作] 按鈕。 例如，讓我們展開上面建立的資料表視圖範例，以在每個資料列中包含 [ **刪除** ] 按鈕。

首先，請 `Main.storyboard` 在 Xcode 的 Interface Builder 中編輯，選取資料表視圖，並將資料行數目增加為三個 (3) 。 接下來，將新資料行的 **標題** 變更為 `Action` ：

[![編輯資料行名稱](table-view-images/delete01.png)](table-view-images/delete01.png#lightbox)

將變更儲存至腳本並返回 Visual Studio for Mac，以同步處理變更。

接著，編輯檔案 `ViewController.cs` 並新增下列公用方法：

```csharp
public void ReloadTable ()
{
  ProductTable.ReloadData ();
}
```

在相同的檔案中，修改在方法內建立新的資料表視圖委派，如下所示 `ViewDidLoad` ：

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

現在，編輯檔案 `ProductTableDelegate.cs` 以包含對 View Controller 的私人連線，並在建立委派的新實例時，將控制器作為參數：

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

接下來，將下列新的私用方法新增至類別：

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

這會取得之前在方法中完成的所有文字視圖設定， `GetViewForItem` 並將它們放在單一的可呼叫位置， (因為資料表的最後一個資料行不包含文字視圖，而按鈕) 。

最後，請編輯 `GetViewForItem` 方法，使其看起來如下所示：

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

讓我們更詳細地查看這段程式碼的幾個部分。 首先，如果正在建立新的，則 `NSTableViewCell` 會根據資料行的名稱來執行動作。 針對前兩個數據行 (**產品** 和 **詳細資料**) ， `ConfigureTextField` 會呼叫新的方法。

針對 [ **動作** ] 資料行，會建立新的，並將其 `NSButton` 新增至資料格做為子視圖：

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

按鈕的 `Tag` 屬性可用來保存目前正在處理的資料列數目。 稍後當使用者要求在按鈕的事件中刪除資料列時，將會使用此數目 `Activated` ：

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

在事件處理常式的開頭，我們會取得位於給定資料表資料列上的按鈕和產品。 然後會向使用者顯示警示，確認資料列刪除。 如果使用者選擇刪除資料列，則會從資料來源中移除指定的資料列，並重載資料表：

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

最後，如果要重複使用資料表視圖儲存格，而不是建立新的，則下列程式碼會根據正在處理的資料行來設定它：

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

針對 [ **動作** ] 資料行，會掃描所有子視圖直到 `NSButton` 找到為止，然後將它的 `Tag` 屬性更新為指向目前的資料列。

進行這些變更之後，當應用程式執行時，每個資料列都會有 [ **刪除** ] 按鈕：

[![具有刪除按鈕的表格視圖](table-view-images/delete02.png)](table-view-images/delete02.png#lightbox)

當使用者按一下 [ **刪除** ] 按鈕時，將會顯示警示，要求他們刪除指定的資料列：

[![刪除資料列警示](table-view-images/delete03.png)](table-view-images/delete03.png#lightbox)

如果使用者選擇 [刪除]，將會移除該資料列，而且資料表將會重新繪製：

[![刪除資料列之後的資料表](table-view-images/delete04.png)](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views"></a>

## <a name="data-binding-table-views"></a>資料系結資料表視圖

藉由在您的 Xamarin. Mac 應用程式中使用索引鍵/值編碼和資料系結技術，您可以大幅減少必須撰寫和維護的程式碼數量，以填入和使用 UI 元素。 您也可以從前端消費者介面 (_模型-視圖控制器_) ，進一步將支援資料 (_資料模型_分離) ，讓您更容易維護、更具彈性的應用程式設計。

索引鍵/值編碼 (KVC) 是一種機制，可讓您使用索引鍵 (特殊格式的字串來間接存取物件的屬性，) 識別屬性，而不是透過執行個體變數或存取子方法 () 來存取它們 `get/set` 。 藉由在您的 Xamarin 應用程式中執行符合金鑰值的程式碼存取子，您就可以存取其他 macOS 功能，例如索引鍵/值觀察 (KVO) 、資料系結、核心資料、Cocoa 系結和 scriptability。

如需詳細資訊，請參閱資料系結[和索引鍵/值編碼](~/mac/app-fundamentals/databinding.md)檔的「[資料表視圖資料](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding)系結」一節。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文詳細說明如何在 Xamarin 應用程式中使用資料表視圖。 我們看到資料表視圖的不同類型和用途、如何在 Xcode 的 Interface Builder 中建立和維護資料表視圖，以及如何在 c # 程式碼中使用資料表視圖。

## <a name="related-links"></a>相關連結

- [MacTables (範例) ](/samples/xamarin/mac-samples/mactables)
- [MacImages (範例)](/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [大綱檢視](~/mac/user-interface/outline-view.md)
- [來源清單](~/mac/user-interface/source-list.md)
- [資料繫結和機碼值編碼](~/mac/app-fundamentals/databinding.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)