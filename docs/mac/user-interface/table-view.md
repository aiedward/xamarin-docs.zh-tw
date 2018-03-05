---
title: "資料表檢視"
description: "本文件涵蓋使用 Xamarin.Mac 應用程式中的資料表檢視。 它會描述 Xcode 介面產生器及與其互動的程式碼中建立的資料表檢視表。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 675B9405-D9A7-49F0-94AD-417F10A71D11
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: dfac551bbb7e6fd9214fe488170455c5916318ae
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="table-views"></a>資料表檢視

_本文件涵蓋使用 Xamarin.Mac 應用程式中的資料表檢視。它會描述 Xcode 介面產生器及與其互動的程式碼中建立的資料表檢視表。_

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取相同資料表檢視，工作的開發人員*OBJECTIVE-C*和*Xcode*沒有。 由於直接與 Xcode 整合 Xamarin.Mac，您可以使用 Xcode 的_介面產生器_建立，並維護資料表檢視 （或您可以選擇直接在 C# 程式碼中建立它們）。

資料表檢視中包含一或多個資料行的多個資料列中的資訊以表格格式顯示資料。 根據正在建立的資料表檢視的類型，使用者可以排序資料行、 重新組織資料行、 加入資料行、 移除資料行或編輯在資料表中所包含的資料。

[ ![](table-view-images/intro01.png "範例資料表")](table-view-images/intro01.png)

在本文中，我們將涵蓋在 Xamarin.Mac 應用程式中使用的資料表檢視的基本概念。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於網路接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="Introduction_to_Table_Views" />

## <a name="introduction-to-table-views"></a>資料表檢視簡介

資料表檢視中包含一或多個資料行的多個資料列中的資訊以表格格式顯示資料。 資料表檢視會顯示內捲動檢視 (`NSScrollView`) 及 macOS 10.7 從開始，您可以使用任何`NSView`而不是資料格 (`NSCell`) 來顯示資料列和資料行。 話雖如此，您仍然可以使用`NSCell`不過，您將通常子類別`NSTableCellView`並建立您自訂的資料列和資料行。

資料表檢視表不會儲存它本身的資料，而是它會仰賴資料來源 (`NSTableViewDataSource`) 來提供資料列和資料行所需，以視需要為基礎。

提供的資料表檢視委派子類別也可以自訂資料表檢視表的行為 (`NSTableViewDelegate`) 來支援資料表資料行管理，輸入 選取功能、 資料列選取和編輯、 自訂的追蹤，而且個別資料行的自訂檢視和資料列。

在建立資料表檢視時，Apple 建議下列：

* 可讓使用者按一下資料行標頭排序資料表。
* 建立會名詞或描述該資料行中顯示的資料的簡短的名詞片語的資料行標頭。

如需詳細資訊，請參閱[內容檢視](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。

<a name="Creating-and-Maintaining-Table-Views-in-Xcode" />

## <a name="creating-and-maintaining-table-views-in-xcode"></a>建立和維護在 Xcode 中的資料表檢視

當您建立新的 Xamarin.Mac Cocoa 應用程式時，預設會取得標準的空白，視窗。 此 windows 中所定義`.storyboard`自動包含在專案中的檔案。 若要編輯您的 windows 設計中**方案總管] 中**，按兩下 [`Main.storyboard`檔案：

[ ![](table-view-images/edit01.png "選取 主要腳本")](table-view-images/edit01.png)

這會在 Xcode 的介面產生器中開啟視窗設計：

[ ![](table-view-images/edit02.png "編輯在 Xcode 中 UI")](table-view-images/edit02.png)

型別`table`到**程式庫偵測器**搜尋方塊讓您更輕鬆地尋找資料表 檢視的控制項：

[ ![](table-view-images/edit03.png "從程式庫中選取的資料表檢視")](table-view-images/edit03.png)

資料表檢視表拖曳中的檢視控制站**介面編輯器**，使其填滿的內容區域的檢視控制器，並將它設定為它壓縮和中的視窗會隨著**條件約束編輯器**:

[ ![](table-view-images/edit04.png "編輯條件約束")](table-view-images/edit04.png)

資料表中選取檢視**介面階層架構**和下列屬性可用於**屬性偵測器**:

[ ![](table-view-images/edit05.png "屬性偵測器")](table-view-images/edit05.png)

- **內容模式**-可讓您使用其中一個檢視 (`NSView`) 或資料格 (`NSCell`) 來顯示資料列和資料行中的資料。 從 macOS 10.7 開始，您應該使用的檢視。
- **群組資料列會浮動**-如果`true`，如同它們浮動資料表檢視表時，會繪製群組資料格。
- **資料行**-定義顯示的資料行數目。
- **標頭**-如果`true`，將資料行具有標頭。
- **重新排列**-如果`true`，使用者可以拖曳重新排序資料表中的資料行。
- **調整大小**-如果`true`，使用者可以拖曳資料行標頭，來調整欄位大小。
- **調整資料行大小**-控制如何在資料表就會自動設定欄的大小。
- **反白顯示**-選取儲存格時所使用的反白顯示資料表類型的控制項。
- **替代資料列**-如果`true`，其他資料列都會有不同的背景色彩。
- **水平格線**-選取的水平繪製儲存格之間的框線類型。
- **垂直格線**-選取的垂直繪製儲存格之間的框線類型。
- **格線色彩**-設定的儲存格的框線色彩。
- **背景**-設定的儲存格的背景色彩。
- **選取項目**-讓您控制使用者如何與資料表中選取資料格：
    - **多個**-如果`true`，使用者可以選取多個資料列和資料行。
    - **資料行**-如果`true`，使用者可以選取資料行。
    - **輸入選取**-如果`true`，使用者可以輸入要選取的資料列的字元。
    - **空白**-如果`true`，使用者不需要選取資料列或資料行，資料表可讓沒有選取項目完全。
- **自動儲存**-名稱的資料表格式會自動儲存。
- **資料行資訊**-如果`true`、 順序和資料行寬度將會自動儲存。
- **分行符號**-選取儲存格處理換行的方式。
- **最後一行會截斷**-如果`true`，資料格不符合它的範圍內的資料可以在將會被截斷。

> [!IMPORTANT]
> 除非您維護舊版 Xamarin.Mac 應用程式，`NSView`基礎的資料表的檢視應透過`NSCell`基礎資料表檢視。 `NSCell` 會被視為舊版，而且可能不支援從現在開始。

選取資料表資料行中**介面階層架構**和下列屬性可用於**屬性偵測器**:

[ ![](table-view-images/edit06.png "屬性偵測器")](table-view-images/edit06.png)

- **標題**-設定資料行的標題。
- **對齊**-設定儲存格內的文字對齊方式。
- **標題的字型**-選取儲存格的標頭文字的字型。
- **排序索引鍵**-是用來排序資料行中的索引鍵。 如果使用者無法排序此資料行，保留空白。
- **選取器**-是**動作**用來執行排序。 如果使用者無法排序此資料行，保留空白。
- **順序**-為資料行資料的排序次序。
- **調整大小**-選取的資料行重新調整大小類型。
- **可編輯**-如果`true`，使用者可以編輯儲存格的基礎資料表中的資料格。
- **隱藏**-如果`true`，隱藏資料行。

您也可以將它的控制代碼的 （置中對齊的資料行右邊） 左或向右拖曳，以調整資料行。

讓我們在我們的資料表檢視中選取每個資料行，並提供第一個資料行**標題**的`Product`和第二個`Details`。

選取資料表資料格檢視 (`NSTableViewCell`) 中**介面階層架構**和下列屬性可用於**屬性偵測器**:

[ ![](table-view-images/edit07.png "屬性偵測器")](table-view-images/edit07.png)

這些是所有的標準檢視的屬性。 您也可以調整大小的資料列，這個資料行的選擇。

選取的資料表檢視儲存格 (根據預設，這是`NSTextField`) 中**介面階層架構**和下列屬性可用於**屬性偵測器**:

[ ![](table-view-images/edit08.png "屬性偵測器")](table-view-images/edit08.png)

您必須在此處設定的標準文字欄位的所有屬性。 根據預設，標準的文字欄位用來顯示資料行中的資料格的資料。

選取資料表資料格檢視 (`NSTableFieldCell`) 中**介面階層架構**和下列屬性可用於**屬性偵測器**:

[ ![](table-view-images/edit09.png "屬性偵測器")](table-view-images/edit09.png)

最重要的設定如下：

- **版面配置**-選取這個資料行中的資料格會配置的方式。
- **使用單一 Line 模式**-如果`true`，資料格僅限於單一行。
- **第一個執行階段的版面配置寬度**-如果`true`，寬度設定為其 （手動或自動） 時偏好使用資料格就會顯示應用程式執行的第一次。
- **動作**-控制於何時編輯**動作**傳送之資料格。
- **行為**-如果儲存格可選取或編輯定義。
- **Rich Text** -如果`true`，資料格可以顯示格式化和樣式的文字。
- **復原**-如果`true`，資料格，它是承擔責任復原行為。

選取的資料表資料格檢視 (`NSTableFieldCell`) 中的資料表資料行底部**介面階層架構**:

[ ![](table-view-images/edit10.png "選取的資料表資料格檢視")](table-view-images/edit10.png)

這可讓您編輯用做為基底資料表資料格檢視_模式_針對給定的資料行建立的所有儲存格。

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>加入動作和出口

就像任何其他 Cocoa UI 控制項，我們需要公開我們資料表檢視表，並資料行及儲存格大小以 C# 程式碼使用**動作**和**插座**（根據所需的功能）。

處理程序也適用於我們想要公開 （expose） 任何資料表檢視項目：

1. 切換至**助理編輯器**，並確定`ViewController.h`選取檔案： 

    [ ![](table-view-images/edit11.png "小幫手編輯器")](table-view-images/edit11.png)
2. 選取 [資料表] 檢視，從**介面階層架構**，控制按一下並拖曳至`ViewController.h`檔案。
3. 建立**插座**資料表檢視表稱為`ProductTable`: 

    [ ![](table-view-images/edit13.png "設定輸出")](table-view-images/edit13.png)
4. 建立**插座**資料表資料行稱為`ProductColumn`和`DetailsColumn`: 

    [ ![](table-view-images/edit14.png "設定輸出")](table-view-images/edit14.png)
5. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

接下來，我們要撰寫程式碼顯示此資料表的某些資料執行應用程式時。

<a name="Populating_the_Table_View" />

## <a name="populating-the-table-view"></a>擴展資料表檢視

我們的資料表檢視表設計介面產生器中，並透過公開**插座**，接下來我們要建立 C# 程式碼以填入它。

首先，我們來建立新`Product`類別以包裝為個別的資料列的資訊。 在**方案總管 中**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...**選取**一般** > **空類別**，輸入`Product`如**名稱**按一下**新增**按鈕：

[ ![](table-view-images/populate01.png "建立空的類別")](table-view-images/populate01.png)

請`Product.cs`檔案外觀如下所示：

```csharp
using System;

namespace MacTables
{
    public class Product
    {
        #region Computed Propoperties
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

接下來，我們需要建立的子類別`NSTableDataSource`要求時，提供資料的資料表。 在**方案總管 中**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...**選取**一般** > **空類別**，輸入`ProductTableDataSource`如**名稱**按一下**新增** 按鈕。

編輯`ProductTableDataSource.cs`檔案，並讓它看起來如下所示：

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

這個類別已儲存的資料表檢視我們的項目，並覆寫`GetRowCount`傳回資料表中的資料列數目。

最後，我們需要建立的子類別`NSTableDelegate`以提供資料表中的行為。 在**方案總管 中**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...**選取**一般** > **空類別**，輸入`ProductTableDelegate`如**名稱**按一下**新增** 按鈕。

編輯`ProductTableDelegate.cs`檔案，並讓它看起來如下所示：

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

當我們建立的執行個體`ProductTableDelegate`，我們也將傳遞的執行個體中`ProductTableDataSource`資料表提供資料。 `GetViewForItem`方法負責傳回要顯示特定資料行和資料列的儲存格的檢視 （資料）。 可能的話，會重複使用現有的檢視，以顯示儲存格，如果不是必須建立新的檢視。

若要填入的資料表，讓我們來編輯`ViewController.cs`檔案並製作`AwakeFromNib`方法看起來像下列：

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

如果我們執行應用程式時，會顯示下列：

[ ![](table-view-images/populate02.png "執行範例應用程式")](table-view-images/populate02.png)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>資料行排序

讓我們可讓使用者按一下資料行標頭排序資料表中的資料。 首先，按兩下`Main.storyboard`檔案以開啟介面產生器中進行編輯。 選取`Product`資料行中，輸入`Title`如**排序索引鍵**，`compare:`的**選取器**選取`Ascending`如**順序**:

[ ![](table-view-images/sort01.png "設定排序索引鍵")](table-view-images/sort01.png)

選取`Details`資料行中，輸入`Description`如**排序索引鍵**，`compare:`的**選取器**選取`Ascending`如**順序**:

[ ![](table-view-images/sort02.png "設定排序索引鍵")](table-view-images/sort02.png)

儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

現在讓我們來編輯`ProductTableDataSource.cs`檔案，然後加入下列方法：

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

`Sort`方法可讓我們排序依據的資料來源中的資料指定`Product`遞增或遞減順序中的類別欄位。 覆寫`SortDescriptorsChanged`使用按一下欄標題時，就會呼叫的方法。 它會傳遞給**金鑰**我們介面產生器及該資料行的排序順序中所設定的值。

如果我們執行應用程式，然後按一下資料行標頭中，會依據該資料行排序資料列：

[ ![](table-view-images/sort03.png "執行範例應用程式")](table-view-images/sort03.png)

<a name="Row_Selection" />

## <a name="row-selection"></a>資料列選取

如果您想要允許使用者選取單一資料列中，按兩下`Main.storyboard`檔案以開啟介面產生器中進行編輯。 資料表中選取檢視**介面階層架構**並取消核取**多個**中的核取方塊**屬性偵測器**:

[ ![](table-view-images/select01.png "屬性偵測器")](table-view-images/select01.png)

儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。


接著，編輯`ProductTableDelegate.cs`檔案，然後加入下列方法：

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

這可讓使用者在資料表檢視中選取任意單一資料列。 傳回`false`如`ShouldSelectRow`的任何資料列，您不想要能夠選取使用者或`false`每個資料列，如果您不想讓使用者能夠選取任何資料列。

[資料表] 檢視 (`NSTableView`) 包含下列資料列選取項目所使用的方法：

- `DeselectRow(nint)` -會取消選取的資料表中給定的資料列。
- `SelectRow(nint,bool)` -選取指定的資料列。 傳遞`false`第二個參數來選取一次只有一個資料列。
- `SelectedRow` -傳回目前資料表中選取的資料列。
- `IsRowSelected(nint)` -傳回`true`如果給定的資料列已選取。

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>多個資料列選取

如果您想要允許使用者選取多個資料列中，按兩下`Main.storyboard`檔案以開啟介面產生器中進行編輯。 資料表中選取檢視**介面階層架構**並檢查**多個**中的核取方塊**屬性偵測器**:

[ ![](table-view-images/select02.png "屬性偵測器")](table-view-images/select02.png)

儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。


接著，編輯`ProductTableDelegate.cs`檔案，然後加入下列方法：

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

這可讓使用者在資料表檢視中選取任意單一資料列。 傳回`false`如`ShouldSelectRow`的任何資料列，您不想要能夠選取使用者或`false`每個資料列，如果您不想讓使用者能夠選取任何資料列。

[資料表] 檢視 (`NSTableView`) 包含下列資料列選取項目所使用的方法：

- `DeselectAll(NSObject)` -會取消選取的資料表中的所有資料列。 使用`this`中進行選取的物件傳送至第一個參數。 
- `DeselectRow(nint)` -會取消選取的資料表中給定的資料列。
- `SelectAll(NSobject)` -選取資料表中的所有資料列。 使用`this`中進行選取的物件傳送至第一個參數。
- `SelectRow(nint,bool)` -選取指定的資料列。 傳遞`false`第二個參數，請清除選取且選取單一資料列時，傳遞`true`延伸選取範圍，並包含此資料列。
- `SelectRows(NSIndexSet,bool)` -選取一組指定的資料列。 傳遞`false`第二個參數清除選取項目，然後選取只有這些資料列，傳遞`true`延伸選取範圍，並包含這些資料列。
- `SelectedRow` -傳回目前資料表中選取的資料列。
- `SelectedRows` -傳回`NSIndexSet`包含選取的資料列的索引。
- `SelectedRowCount` -傳回選取的資料列數目。
- `IsRowSelected(nint)` -傳回`true`如果給定的資料列已選取。

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>選取資料列的型別

如果您想要允許使用者使用選取的資料表檢視中輸入字元，並選取第一個資料列具有該字元中，按兩下`Main.storyboard`檔案以開啟介面產生器中進行編輯。 資料表中選取檢視**介面階層架構**並檢查**類型選取**中的核取方塊**屬性偵測器**:

[ ![](table-view-images/type01.png "設定選取項目類型")](table-view-images/type01.png)

儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

現在讓我們來編輯`ProductTableDelegate.cs`檔案，然後加入下列方法：

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

`GetNextTypeSelectMatch`方法會採用給定`searchString`並傳回第一個資料列`Product`，該字串中有的`Title`。

如果我們執行應用程式，並輸入字元，將選取的資料列：

[ ![](table-view-images/type02.png "執行範例應用程式")](table-view-images/type02.png)

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>重新排列資料行

如果您想要允許使用者在拖曳重新排列資料表檢視表中的資料行中，按兩下`Main.storyboard`檔案以開啟介面產生器中進行編輯。 資料表中選取檢視**介面階層架構**並檢查**排序**中的核取方塊**屬性偵測器**:

[ ![](table-view-images/reorder01.png "屬性偵測器")](table-view-images/reorder01.png)

如果我們提供的值**自動儲存**屬性，然後核取**資料行資訊**欄位中，我們對資料表的配置進行任何變更將自動為我們儲存和還原下一次應用程式會執行。

儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

現在讓我們來編輯`ProductTableDelegate.cs`檔案，然後加入下列方法：

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

`ShouldReorder`方法應傳回`true`它想要允許任何資料行拖曳重新排列成`newColumnIndex`，否則會傳回`false`;

如果我們執行應用程式時，我們可以拖曳資料行標頭周圍我們的資料行重新排列：

[ ![](table-view-images/reorder02.png "Reordered 資料行的範例")](table-view-images/reorder02.png)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>編輯儲存格

如果您想要允許使用者編輯指定的儲存格的值，請編輯`ProductTableDelegate.cs`檔案，並且變更`GetViewForItem`方法，如下所示：

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

現在如果我們執行應用程式時，使用者可以編輯的表格檢視中的資料格：

[ ![](table-view-images/editing01.png "舉例來說，在編輯儲存格")](table-view-images/editing01.png)

<a name="Using_Images_in_Table_Views" />

## <a name="using-images-in-table-views"></a>使用 資料表檢視中的 映像

要包含映像中的資料格的一部分`NSTableView`，您必須變更 [資料表] 檢視所傳回資料的`NSTableViewDelegate's``GetViewForItem`方法，以使用`NSTableCellView`而不是一般`NSTextField`。 例如: 

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

如需詳細資訊，請參閱[使用資料表檢視的影像](~/mac/app-fundamentals/image.md)區段我們[使用映像](~/mac/app-fundamentals/image.md)文件。

<a name="Adding-a-Delete-Button-to-a-Row" />

## <a name="adding-a-delete-button-to-a-row"></a>加入一個資料列的刪除按鈕

根據您的應用程式的需求，可能會有的情況下您要提供資料表中每個資料列的動作按鈕。 為此動作的範例，讓我們來展開加入上面所建立的資料表檢視範例**刪除**每個資料列上的按鈕。

首先，編輯`Main.storyboard`在 Xcode 的介面產生器中，選取 [資料表] 檢視和增加的資料行數目 （3)。 接下來，變更**標題**新的資料行的`Action`:

[ ![](table-view-images/delete01.png "編輯資料行名稱")](table-view-images/delete01.png)

儲存變更到分鏡腳本，並返回 Visual Studio for Mac 同步處理變更。

接著，編輯`ViewController.cs`檔案，然後加入下列公用方法：

```csharp
public void ReloadTable ()
{
    ProductTable.ReloadData ();
}
```

在相同的檔案中，修改新的資料表檢視委派內建立`ViewDidLoad`方法，如下所示：

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

現在，編輯`ProductTableDelegate.cs`檔案可以包含檢視控制器的私用連接，並使控制站做為參數，建立委派的新執行個體時：

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

接下來，將下列新的私用方法加入類別：

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

這會將所有先前已正在進行中的文字檢視組態`GetViewForItem`方法並將它們放置在單一且可呼叫的位置 （因為資料表的最後一個資料行不包含文字檢視，但是按鈕）。

最後，編輯`GetViewForItem`方法並看起來如下所示：

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
                var btw = sender as NSButton;
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
            var btw = subview as NSButton;
            if (btw != null) {
                btn.Tag = row;
            }
        }
        break;
    }

    return view;
}
```

讓我們看看此程式碼，更詳細的數個區段。 首先，如果新`NSTableViewCell`是正在建立動作點是根據資料行的名稱。 前兩個資料行 (**產品**和**詳細資料**)，新`ConfigureTextField`方法呼叫。

如**動作**資料行中，新`NSButton`已建立並加入至做為子檢視的資料格：

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

按鈕的`Tag`屬性用來保存目前正在處理的資料列的數目。 這個數字稍後將會用在使用者要求的資料列中按鈕的刪除`Activated`事件：

```csharp
// Wireup events
button.Activated += (sender, e) => {
    // Get button and product
    var btw = sender as NSButton;
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

在開始此事件處理常式時，我們將按鈕和給定的資料表資料列上的產品。 然後警示會向使用者確認資料列刪除。 如果使用者選擇要刪除的資料列，從資料來源移除給定的資料列和資料表會重新載入：

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

最後，如果重複使用而不建立新的資料表檢視儲存格，下列程式碼會設定它會根據正在處理的資料行：

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
        var btw = subview as NSButton;
        if (btw != null) {
            btn.Tag = row;
        }
    }
    break;
}

```

如**動作**資料行，直到掃描所有的子檢視`NSButton`找到，則它是`Tag`屬性會更新為指向目前的資料列。

這些變更之後，當應用程式會執行每個資料列，將有**刪除**按鈕：

[ ![](table-view-images/delete02.png "[資料表] 檢視刪除按鈕")](table-view-images/delete02.png)

當使用者按一下**刪除** 按鈕，將會要求他們刪除給定的資料列會出現警示：

[ ![](table-view-images/delete03.png "刪除資料列警示")](table-view-images/delete03.png)

如果使用者選擇刪除，將會移除資料列，資料表將會重繪：

[ ![](table-view-images/delete04.png "資料表刪除資料列之後")](table-view-images/delete04.png)

<a name="Data_Binding_Table_Views" />

## <a name="data-binding-table-views"></a>資料繫結的資料表檢視

Xamarin.Mac 應用程式中使用索引鍵-值撰寫程式碼和資料繫結技術，您可以大幅減少您必須撰寫和維護以填入和 UI 項目所使用的程式碼數量。 您也可以進一步減少您的備份資料的優點 (_資料模型_) 從您的前端結束使用者介面 (_模型-檢視-控制器_)，而導致更輕鬆地維護，更有彈性的應用程式設計。

索引鍵-值撰寫程式碼 (KVC) 是用來間接存取物件的屬性、 使用以識別屬性，而非透過執行個體變數存取它們的索引鍵 （特殊格式化的字串） 或存取子方法的機制 (`get/set`)。 實作索引鍵-值撰寫程式碼相容的存取子 Xamarin.Mac 應用程式中，您就可以存取其他 macOS 功能，例如索引鍵-值觀察 (KVO)、 資料繫結、 核心資料、 Cocoa 繫結，以及 scriptabletype。

如需詳細資訊，請參閱[資料表檢視的資料繫結](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding)區段我們[資料繫結和索引鍵-值編碼](~/mac/app-fundamentals/databinding.md)文件。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已取得詳細的探討在 Xamarin.Mac 應用程式中使用的資料表檢視。 我們會看到不同的類型，並使用的資料表檢視、 如何建立和維護 Xcode 的介面產生器中的資料表檢視以及如何在 C# 程式碼中使用的資料表檢視。

## <a name="related-links"></a>相關連結

- [MacTables （範例）](https://developer.xamarin.com/samples/mac/MacTables/)
- [MacImages (範例)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [大綱檢視](~/mac/user-interface/outline-view.md)
- [來源清單](~/mac/user-interface/source-list.md)
- [資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)
