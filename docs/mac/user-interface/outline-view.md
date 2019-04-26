---
title: Xamarin.mac 的大綱檢視
description: 本文涵蓋在 Xamarin.Mac 應用程式中的大綱檢視。 它說明建立和維護在 Xcode 和 Interface Builder 中的大綱檢視，並以程式設計方式處理它們。
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: fd97dbbe102c5a755c4a8974cf1a952c0050ac7c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61292585"
---
# <a name="outline-views-in-xamarinmac"></a>Xamarin.mac 的大綱檢視

_本文涵蓋在 Xamarin.Mac 應用程式中的大綱檢視。它說明建立和維護在 Xcode 和 Interface Builder 中的大綱檢視，並以程式設計方式處理它們。_

使用時C#和.NET Xamarin.Mac 應用程式中的，您可以存取相同的大綱檢視的工作的開發人員*Objective C*並*Xcode*沒有。 由於 Xamarin.Mac 直接與 Xcode 整合，您可以使用 Xcode 的_Interface Builder_來建立，並維護您的大綱檢視 （或選擇直接在 C# 程式碼中建立它們）。

大綱檢視是一種資料表，可讓使用者展開或摺疊的階層式資料的資料列。 資料表檢視中，例如大綱檢視會顯示一組相關的項目資料與代表個別項目和代表這些項目之屬性的資料行的資料列。 不同於資料表檢視中，在大綱檢視中的項目不一般的清單中，其組織在階層中，例如硬碟機上檔案和資料夾。

[![](outline-view-images/populate03.png "執行範例應用程式")](outline-view-images/populate03.png#lightbox)

在本文中，我們將涵蓋在 Xamarin.Mac 應用程式中使用的大綱檢視的基本概念。 強烈建議您逐步[Hello，Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)並[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)各節中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於連線接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>大綱檢視的簡介

大綱檢視是一種資料表，可讓使用者展開或摺疊的階層式資料的資料列。 資料表檢視中，例如大綱檢視會顯示一組相關的項目資料與代表個別項目和代表這些項目之屬性的資料行的資料列。 不同於資料表檢視中，在大綱檢視中的項目不一般的清單中，其組織在階層中，例如硬碟機上檔案和資料夾。

如果在大綱檢視中的項目包含其他項目，它可展開或摺疊，只要使用者。 可展開的項目會顯示指向右方，當項目已摺疊和展開項目時，指向下方的洩漏三角形。 按一下洩漏三角形上，會導致要展開或摺疊的項目。

大綱檢視 (`NSOutlineView`) 是 [資料表] 檢視的子類別 (`NSTableView`)，因此，將其行為繼承其父類別。 如此一來，大綱檢視也支援許多資料表檢視中，例如選取資料列或資料行，藉由拖曳資料行標頭，等等，資料行重新調整位置所支援的作業。 Xamarin.Mac 應用程式具有這些功能的控制項，而且可以設定大綱檢視的參數 （無論是程式碼或 Interface Builder 中），來允許或禁止特定作業。

大綱檢視不會儲存它本身的資料，而是它會依賴資料來源 (`NSOutlineViewDataSource`) 提供資料列和資料行所需，以視需要為基礎。

大綱檢視的行為可以自訂所提供的大綱檢視委派子類別 (`NSOutlineViewDelegate`) 以支援外框資料行管理，輸入 選取功能、 資料列選取和編輯、 自訂追蹤和個人的自訂檢視資料行和資料列。

大綱檢視共用的大部分的行為和功能的資料表檢視，因為您可能想要瀏覽我們[資料表檢視](~/mac/user-interface/table-view.md)再繼續進行這篇文章的文件。

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>建立和維護在 Xcode 中的大綱檢視

當您建立新的 Xamarin.Mac 的 Cocoa 應用程式時，您會取得預設的標準的空白，視窗。 此 windows 定義於`.storyboard`自動包含在專案中的檔案。 若要編輯您的 windows 設計，在**方案總管**，按兩下 `Main.storyboard`檔案：

[![](outline-view-images/edit01.png "選取主要分鏡腳本")](outline-view-images/edit01.png#lightbox)

這會在 Xcode 的 Interface Builder 中開啟的視窗設計：

[![](outline-view-images/edit02.png "編輯在 Xcode 中的 UI")](outline-view-images/edit02.png#lightbox)

型別`outline`成**程式庫偵測器**來輕鬆地找到的大綱檢視控制項的 [搜尋] 方塊：

[![](outline-view-images/edit03.png "從程式庫中選取的大綱檢視")](outline-view-images/edit03.png#lightbox)

大綱檢視拖曳中的檢視控制器**介面編輯器**，使其填滿內容區域的檢視控制器，並將它設定為其中縮小和成長與中的視窗**條件約束編輯器**:

[![](outline-view-images/edit04.png "編輯條件約束")](outline-view-images/edit04.png#lightbox)

選取 [大綱] 檢視中**介面階層架構**及下列屬性可用於**屬性偵測器**:

[![](outline-view-images/edit05.png "屬性偵測器")](outline-view-images/edit05.png#lightbox)

- **說明資料行**-其中會顯示階層式資料的資料表資料行。
- **自動儲存外框資料行**-如果`true`，[外框] 欄將會自動儲存並還原應用程式執行之間。
- **縮排**-要縮排在展開的項目之下的資料行的數量。
- **遵循儲存格縮排**-如果`true`，縮排標記將會縮排，以及儲存格。
- **自動儲存展開項目**-如果`true`，展開/摺疊的狀態項目將會自動儲存並還原應用程式執行之間。
- **內容模式**-可讓您使用其中一個檢視 (`NSView`) 或資料格 (`NSCell`) 若要顯示的資料列和資料行中的資料。 從 macOS 10.7 開始，您應該使用的檢視。
- **浮點數的資料列分組**-如果`true`，如同它們浮動的資料表檢視將會繪製群組資料格。
- **資料行**-定義顯示的資料行的數目。
- **標頭**-如果`true`，資料行，將會具有標頭。
- **重新排列**-如果`true`，使用者將能夠拖曳重新排列資料表的資料行。
- **調整大小**-如果`true`，使用者將能夠拖曳以調整資料行的資料行標頭。
- **調整欄寬**-控制資料表就會自動大小的資料行的方式。
- **反白顯示**-選取儲存格時所使用的反白顯示資料表類型的控制項。
- **替代資料列**-如果`true`，其他資料列會有不同的背景色彩。
- **水平格線**-選取的水平繪製儲存格之間的框線類型。
- **垂直格線**-選取的儲存格之間以垂直方式繪製的框線類型。
- **格線色彩**-設定的儲存格的框線色彩。
- **背景**-設定資料格背景色彩。
- **選取項目**-可讓您控制使用者如何與資料表中選取資料格：
    - **多個**-如果`true`，使用者可以選取多個資料列和資料行。
    - **資料行**-如果`true`，使用者可以選取資料行。
    - **輸入 Select** -如果`true`，使用者可以輸入要選取的資料列的字元。
    - **空**-如果`true`，使用者不需要選取資料列或資料行，資料表是用來套用至空白行完全。
- **自動儲存**-名稱的資料表格式會自動儲存。
- **資料行資訊**-如果`true`、 順序和資料行寬度會自動儲存。
- **分行符號**-選取儲存格處理分行符號的方式。
- **最後一個可見行就會截斷**-如果`true`，儲存格將會被截斷，無法容納它的範圍內的資料來中。

> [!IMPORTANT]
> 您正在維護舊版的 Xamarin.Mac 應用程式，除非`NSView`根據的大綱檢視應透過`NSCell`基礎資料表檢視。 `NSCell` 會被視為舊版，而可能不支援從現在開始。

選取中的資料表資料行**介面階層架構**及下列屬性可用於**屬性偵測器**:

[![](outline-view-images/edit06.png "屬性偵測器")](outline-view-images/edit06.png#lightbox)

- **標題**-設定資料行的標題。
- **對齊**-設定資料格內的文字的對齊方式。
- **標題字型**-選取儲存格的標頭文字的字型。
- **排序鍵**-是用來排序資料行中的索引鍵。 如果使用者無法排序此資料行，保留空白。
- **選取器**-已**動作**用來執行排序。 如果使用者無法排序此資料行，保留空白。
- **順序**-是資料行資料的排序次序。
- **調整大小**-選取的資料行調整大小類型。
- **可編輯**-如果`true`，使用者可以編輯儲存格的基礎資料表中的資料格。
- **隱藏**-如果`true`，隱藏資料行。

您也可以藉由拖曳其控點的 （垂直置中的資料行的右邊對齊） 左或向右調整資料行。

讓我們在我們的資料表檢視中選取每個資料行，並提供第一個資料行**標題**的`Product`，第二個`Details`。

選取資料表資料格檢視 (`NSTableViewCell`) 中**介面階層架構**以及下列屬性可用於**屬性偵測器**:

[![](outline-view-images/edit07.png "屬性偵測器")](outline-view-images/edit07.png#lightbox)

這些是所有的標準檢視的屬性。 您也可以調整大小的資料列，這個資料行的選擇。

選取 資料表檢視儲存格 (根據預設，這是`NSTextField`) 中**介面階層架構**以及下列屬性可用於**屬性偵測器**:

[![](outline-view-images/edit08.png "屬性偵測器")](outline-view-images/edit08.png#lightbox)

您必須在這裡設定標準的文字欄位的所有屬性。 根據預設，標準的文字欄位用來顯示資料行中的資料格的資料。

選取資料表資料格檢視 (`NSTableFieldCell`) 中**介面階層架構**以及下列屬性可用於**屬性偵測器**:

[![](outline-view-images/edit09.png "屬性偵測器")](outline-view-images/edit09.png#lightbox)

最重要的設定如下：

- **版面配置**-選取此資料行中的儲存格配置的方式。
- **使用單一行模式**-如果`true`，儲存格是限制為單一行。
- **第一個執行階段配置寬度**-如果`true`，儲存格會偏好寬度設定為它 （手動或自動） 時它會顯示第一次執行應用程式。
- **動作**-控制於何時編輯**動作**傳送之資料格。
- **行為**-定義儲存格是否可選取或編輯。
- **Rtf** -如果`true`，儲存格可以顯示格式化和樣式的文字。
- **復原**-如果`true`，儲存格假設責任，它是復原行為。

選取資料表資料格檢視 (`NSTableFieldCell`) 中的資料表資料行底部**介面階層架構**:

[![](outline-view-images/edit11.png "選取資料表資料格檢視")](outline-view-images/edit10.png#lightbox)

這可讓您編輯用來作為基礎的資料表資料格檢視_模式_的指定資料行所建立的所有儲存格。

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>新增動作與出口

就如同任何其他 Cocoa UI 的控制項，我們需要公開我們的大綱檢視和它是資料行，並以 C# 程式碼使用的儲存格**動作**並**輸出**（根據所需的功能）。

此程序也適用於任何我們想要公開的大綱檢視項目：

1. 切換至**輔助編輯器**，並確定`ViewController.h`選取檔案： 

    [![](outline-view-images/edit11.png "選取正確的.h 檔案")](outline-view-images/edit11.png#lightbox)
2. 選取從大綱**介面階層架構**，control + 按一下並拖曳至`ViewController.h`檔案。
3. 建立**插座**大綱檢視稱為`ProductOutline`: 

    [![](outline-view-images/edit13.png "設定輸出")](outline-view-images/edit13.png#lightbox)
4. 建立**插座**的資料表資料行稱為`ProductColumn`和`DetailsColumn`: 

    [![](outline-view-images/edit14.png "設定輸出")](outline-view-images/edit14.png#lightbox)
5. 儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

接下來，我們將撰寫程式碼顯示外框的某些資料時，應用程式執行。

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>填入大綱檢視

使用我們的大綱檢視設計介面產生器 中，並透過公開**插座**，接下來我們需要建立 C# 程式碼來填入它。

首先，讓我們來建立 新`Product`類別以保有個別的資料列和群組的子產品的資訊。 在 **方案總管**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...** 選取**一般** > **空類別**，輸入`Product`如**名稱**然後按一下**新增**按鈕：

[![](outline-view-images/populate01.png "建立空的類別")](outline-view-images/populate01.png#lightbox)

請`Product.cs`檔案外觀如下所示：

```csharp
using System;
using Foundation;
using System.Collections.Generic;

namespace MacOutlines
{
    public class Product : NSObject
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Computed Properties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        public bool IsProductGroup {
            get { return (Products.Count > 0); }
        }
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

接下來，我們需要建立的子類別`NSOutlineDataSource`要求時，針對我們的大綱提供的資料。 在 [**方案總管**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...** 選取**一般** > **空類別**，輸入`ProductOutlineDataSource`如**名稱**然後按一下**新增**] 按鈕。

編輯`ProductTableDataSource.cs`檔案，並使它看起來如下：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDataSource : NSOutlineViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductOutlineDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetChildrenCount (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products.Count;
            } else {
                return ((Product)item).Products.Count;
            }

        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, NSObject item)
        {
            if (item == null) {
                return Products [childIndex];
            } else {
                return ((Product)item).Products [childIndex];
            }
                
        }

        public override bool ItemExpandable (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products [0].IsProductGroup;
            } else {
                return ((Product)item).IsProductGroup;
            }
        
        }
        #endregion
    }
}
```

這個類別會具有儲存體，我們的大綱檢視項目，且會覆寫`GetChildrenCount`資料表中傳回的資料列數目。 `GetChild` （如要求大綱檢視） 會傳回特定的父或子系項目和`ItemExpandable`定義指定的項目做為父系或子系。

最後，我們需要建立的子類別`NSOutlineDelegate`以提供我們的外框的行為。 在 [**方案總管**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...** 選取**一般** > **空類別**，輸入`ProductOutlineDelegate`如**名稱**然後按一下**新增**] 按鈕。

編輯`ProductOutlineDelegate.cs`檔案，並使它看起來如下：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDelegate : NSOutlineViewDelegate
    {
        #region Constants 
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductOutlineDataSource DataSource;
        #endregion

        #region Constructors
        public ProductOutlineDelegate (ProductOutlineDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)outlineView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Cast item
            var product = item as Product;

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = product.Title;
                break;
            case "Details":
                view.StringValue = product.Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

當我們建立的執行個體`ProductOutlineDelegate`，我們也會傳遞執行個體中`ProductOutlineDataSource`提供資料的外框。 `GetView`方法負責傳回一個檢視 （資料），來顯示特定資料行和資料列的資料格。 可能的話，會重複使用現有的檢視，以顯示儲存格，否則必須建立新的檢視。

若要填入外框，讓我們編輯`MainWindow.cs`檔案，並讓`AwakeFromNib`方法外觀如下所示：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create data source and populate
    var DataSource = new ProductOutlineDataSource ();

    var Vegetables = new Product ("Vegetables", "Greens and Other Produce");
    Vegetables.Products.Add (new Product ("Cabbage", "Brassica oleracea - Leaves, axillary buds, stems, flowerheads"));
    Vegetables.Products.Add (new Product ("Turnip", "Brassica rapa - Tubers, leaves"));
    Vegetables.Products.Add (new Product ("Radish", "Raphanus sativus - Roots, leaves, seed pods, seed oil, sprouting"));
    Vegetables.Products.Add (new Product ("Carrot", "Daucus carota - Root tubers"));
    DataSource.Products.Add (Vegetables);

    var Fruits = new Product ("Fruits", "Fruit is a part of a flowering plant that derives from specific tissues of the flower");
    Fruits.Products.Add (new Product ("Grape", "True Berry"));
    Fruits.Products.Add (new Product ("Cucumber", "Pepo"));
    Fruits.Products.Add (new Product ("Orange", "Hesperidium"));
    Fruits.Products.Add (new Product ("Blackberry", "Aggregate fruit"));
    DataSource.Products.Add (Fruits);

    var Meats = new Product ("Meats", "Lean Cuts");
    Meats.Products.Add (new Product ("Beef", "Cow"));
    Meats.Products.Add (new Product ("Pork", "Pig"));
    Meats.Products.Add (new Product ("Veal", "Young Cow"));
    DataSource.Products.Add (Meats);

    // Populate the outline
    ProductOutline.DataSource = DataSource;
    ProductOutline.Delegate = new ProductOutlineDelegate (DataSource);

}
```

如果我們執行應用程式，則會顯示下列：

[![](outline-view-images/populate02.png "摺疊的檢視")](outline-view-images/populate02.png#lightbox)

如果我們展開大綱檢視中的節點時，它看起來如下所示：

[![](outline-view-images/populate03.png "在展開之檢視")](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>資料行排序

讓我們讓使用者按一下資料行標頭來排序資料大綱 中。 首先，連按兩下`Main.storyboard`以開啟它進行編輯介面產生器中的檔案。 選取`Product`資料行中，輸入`Title`如**排序索引鍵**，`compare:`如**選取器**，然後選取`Ascending`的**順序**:

[![](outline-view-images/sort01.png "設定索引鍵的排序")](outline-view-images/sort01.png#lightbox)

儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

現在讓我們編輯`ProductOutlineDataSource.cs`檔案，並新增下列方法：

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
    }
}

public override void SortDescriptorsChanged (NSOutlineView outlineView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    outlineView.ReloadData ();
}
```

`Sort`方法，讓我們來排序依據的資料來源中的資料指定`Product`類別欄位以遞增或遞減順序。 覆寫`SortDescriptorsChanged`每次使用按一下資料行標題，則會呼叫方法。 它會傳遞**金鑰**我們在介面產生器及該資料行的排序順序中設定的值。

如果我們執行應用程式，然後按一下資料行標頭中，資料列會依該資料行：

[![](outline-view-images/sort02.png "已排序的輸出範例")](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>資料列選取範圍

如果您想要允許使用者選取單一資料列，按兩下`Main.storyboard`以開啟它進行編輯介面產生器中的檔案。 選取 [大綱] 檢視中**介面階層架構**並取消核取**多重**核取方塊**屬性偵測器**:

[![](outline-view-images/select01.png "屬性偵測器")](outline-view-images/select01.png#lightbox)

儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。


接著，編輯`ProductOutlineDelegate.cs`檔案，並新增下列方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

這可讓使用者在大綱檢視中選取任何一個資料列。 傳回`false`for`ShouldSelectItem`的任何項目，您不想要能夠選取使用者或`false`每一個項目，如果您不希望使用者能夠選取任何項目。

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>多個資料列選取範圍

如果您想要允許使用者選取多個資料列，按兩下`Main.storyboard`以開啟它進行編輯介面產生器中的檔案。 選取 [大綱] 檢視中**介面階層架構**並查看**多個**核取方塊**屬性偵測器**:

[![](outline-view-images/select02.png "屬性偵測器")](outline-view-images/select02.png#lightbox)

儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。


接著，編輯`ProductOutlineDelegate.cs`檔案，並新增下列方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

這可讓使用者在大綱檢視中選取任何一個資料列。 傳回`false`for`ShouldSelectRow`的任何項目，您不想要能夠選取使用者或`false`每一個項目，如果您不希望使用者能夠選取任何項目。

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>若要選取資料列的類型

如果您想要讓使用者使用選取的大綱檢視中輸入字元，然後選取第一個資料列具有該字元中，按兩下`Main.storyboard`以開啟它進行編輯介面產生器中的檔案。 選取大綱中**介面階層架構**，並檢查**型別選取**核取方塊**屬性偵測器**:

[![](outline-view-images/type01.png "編輯資料列型別")](outline-view-images/type01.png#lightbox)

儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

現在讓我們編輯`ProductOutlineDelegate.cs`檔案，並新增下列方法：

```csharp
public override NSObject GetNextTypeSelectMatch (NSOutlineView outlineView, NSObject startItem, NSObject endItem, string searchString)
{
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains (searchString)) {
            return product;
        }
    }

    // Not found
    return null;
}
```

`GetNextTypeSelectMatch`方法會採用給定`searchString`，並傳回第一個項目`Product`，該字串中有的`Title`。

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>重新排序資料行

如果您想要允許使用者拖曳重新排列在大綱檢視中的資料行中，按兩下`Main.storyboard`以開啟它進行編輯介面產生器中的檔案。 選取 [大綱] 檢視中**介面階層架構**並查看**Reordering**核取方塊**屬性偵測器**:

[![](outline-view-images/reorder01.png "屬性偵測器")](outline-view-images/reorder01.png#lightbox)

如果我們提供的值**Autosave**屬性，並檢查**資料行資訊**欄位中，我們會對資料表的配置進行任何變更將自動為我們儲存和還原下一次應用程式會執行。

儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

現在讓我們編輯`ProductOutlineDelegate.cs`檔案，並新增下列方法：

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

`ShouldReorder`方法應傳回`true`它想要允許任何資料行拖曳重新排列成`newColumnIndex`，否則會傳回`false`;

如果我們執行應用程式時，我們可以將周圍的資料行標頭拖曳來重新排列資料行：

[![](outline-view-images/reorder02.png "重新排列資料行的範例")](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>編輯資料格

如果您想要允許使用者編輯指定的資料格的值，請編輯`ProductOutlineDelegate.cs`檔案，並變更`GetViewForItem`方法，如下所示：

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.StringValue;
            break;
        case "Details":
            prod.Description = view.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = product.Title;
        break;
    case "Details":
        view.StringValue = product.Description;
        break;
    }

    return view;
}
```

現在如果我們執行應用程式時，使用者可以編輯 [資料表] 檢視中的資料格：

[![](outline-view-images/editing01.png "舉例來說，編輯儲存格")](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>在大綱檢視中使用映像

若要包含影像中的資料格的一部分`NSOutlineView`，您必須變更 [大綱] 檢視所傳回資料的方式`NSTableViewDelegate's``GetView`方法，以使用`NSTableCellView`而不是一般`NSTextField`。 例如: 

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
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
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

如需詳細資訊，請參閱[使用的映像使用 「 大綱檢視](~/mac/app-fundamentals/image.md)一節我們[映像使用](~/mac/app-fundamentals/image.md)文件。

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>資料繫結大綱檢視

在 Xamarin.Mac 應用程式中使用索引鍵-值撰寫程式碼和資料繫結技術，您可以大幅減少您必須撰寫和維護以填入和 UI 項目所使用的程式碼數量。 您也可以進一步減少您的備份資料的優點 (_資料模型_) 從您的 front end 使用者介面 (_模型-檢視-控制器_)，而導致容易維護、 更有彈性的應用程式設計。

索引鍵-值撰寫程式碼 (KVC) 是一種機制，間接地存取物件的屬性、 使用識別屬性，而不是透過執行個體變數存取它們的索引鍵 （特殊格式化的字串） 或存取子方法 (`get/set`)。 在 Xamarin.Mac 應用程式中實作索引鍵-值撰寫程式碼相容的存取子，就可以取得其他 macOS 功能，例如索引鍵-值觀察 (KVO)、 資料繫結、 核心資料、 Cocoa 繫結，以及 scriptabletype 的存取。

如需詳細資訊，請參閱[大綱檢視的資料繫結](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding)一節我們[資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)文件。

<a name="Summary" />

## <a name="summary"></a>總結

本文所深入了解在 Xamarin.Mac 應用程式中使用的大綱檢視。 我們會看到不同的類型，並使用大綱檢視、 如何建立和維護 Xcode 的 Interface Builder 中的大綱檢視以及如何使用 C# 程式碼中的大綱檢視。

## <a name="related-links"></a>相關連結

- [MacOutlines （範例）](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [MacImages (範例)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料表檢視](~/mac/user-interface/table-view.md)
- [來源清單](~/mac/user-interface/source-list.md)
- [資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [大綱檢視的簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
