---
title: Xamarin 中的大綱視圖
description: 本文說明如何在 Xamarin. Mac 應用程式中使用大綱視圖。 其中說明如何在 Xcode 和 Interface Builder 中建立和維護大綱視圖，並以程式設計方式使用它們。
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: dd710f13d9324ce31e08641f214241e0433fe809
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004336"
---
# <a name="outline-views-in-xamarinmac"></a>Xamarin 中的大綱視圖

_本文說明如何在 Xamarin. Mac 應用程式中使用大綱視圖。其中說明如何在 Xcode 和 Interface Builder 中建立和維護大綱視圖，並以程式設計方式使用它們。_

在 Xamarin. C# Mac 應用程式中使用和 .net 時，您可以存取開發人員以*目標-C*和*Xcode*執行的相同大綱觀點。 因為 Xamarin 會直接與 Xcode 整合，所以您可以使用 Xcode 的_Interface Builder_來建立和維護大綱視圖（或選擇直接在程式碼中C#建立它們）。

大綱視圖是一種可讓使用者展開或折迭階層式資料列的資料表類型。 就像資料表的視圖一樣，大綱視圖會顯示一組相關專案的資料，其中包含代表個別專案的資料列，以及代表這些專案屬性的資料行。 不同于資料表視圖，大綱視圖中的專案不是在一般清單中，而是在階層中組織，例如硬碟上的檔案和資料夾。

[![](outline-view-images/populate03.png "An example app run")](outline-view-images/populate03.png#lightbox)

在本文中，我們將討論在 Xamarin. Mac 應用程式中使用大綱視圖的基本概念。 強烈建議您先流覽[Hello，Mac](~/mac/get-started/hello-mac.md)文章，特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介，其中涵蓋了我們將在中使用的重要概念和技巧。本文。

您可能想要看一下[Xamarin 內部](~/mac/internals/how-it-works.md)檔的「 C# [公開C#類別/方法到目標-C](~/mac/internals/how-it-works.md) 」一節，它會說明用來將類別連線到目標-c 的`Register`和`Export`命令物件和 UI 元素。

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>大綱視圖簡介

大綱視圖是一種可讓使用者展開或折迭階層式資料列的資料表類型。 就像資料表的視圖一樣，大綱視圖會顯示一組相關專案的資料，其中包含代表個別專案的資料列，以及代表這些專案屬性的資料行。 不同于資料表視圖，大綱視圖中的專案不是在一般清單中，而是在階層中組織，例如硬碟上的檔案和資料夾。

如果大綱視圖中的專案包含其他專案，則可以由使用者展開或折迭。 可擴充的專案會顯示洩漏三角形，在專案折迭時指向右邊，並在展開專案時向下指向。 按一下洩漏三角形會使專案展開或折迭。

大綱視圖（`NSOutlineView`）是資料表視圖（`NSTableView`）的子類別，因此從其父類別繼承大部分的行為。 因此，大綱視圖也支援資料表視圖所支援的許多作業，例如選取資料列或資料行、藉由拖曳資料行標頭來重新置放資料行等等。 Xamarin 應用程式具有這些功能的控制權，而且可以設定大綱視圖的參數（在程式碼或 Interface Builder 中），以允許或禁止某些作業。

大綱視圖不會儲存自己的資料，而是依賴資料來源（`NSOutlineViewDataSource`），視需要提供所需的資料列和資料行。

您可以自訂大綱視圖的行為，方法是提供大綱視圖委派的子類別（`NSOutlineViewDelegate`），以支援大綱資料行管理、類型來選取功能、資料列選取和編輯、自訂追蹤，以及個別資料行的自訂視圖和資料列。

由於大綱視圖會與資料表視圖共用大部分的行為和功能，因此您可能會想要先流覽資料表的[Views](~/mac/user-interface/table-view.md)檔，再繼續進行本文。

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>在 Xcode 中建立和維護大綱視圖

當您建立新的 Xamarin Cocoa 應用程式時，預設會取得標準的空白視窗。 此視窗會在專案中自動包含的 `.storyboard` 檔案中定義。 若要編輯您的 windows 設計，請在 **方案總管**中，按兩下 `Main.storyboard` 檔案：

[![](outline-view-images/edit01.png "Selecting the main storyboard")](outline-view-images/edit01.png#lightbox)

這會在 Xcode 的 Interface Builder 中開啟視窗設計：

[![](outline-view-images/edit02.png "Editing the UI in Xcode")](outline-view-images/edit02.png#lightbox)

在 [連結**庫偵測器] 的**[搜尋] 方塊中輸入 `outline`，讓您更輕鬆地找到大綱視圖控制項：

[![](outline-view-images/edit03.png "Selecting an Outline View from the Library")](outline-view-images/edit03.png#lightbox)

將大綱視圖拖曳至 [**介面編輯器**] 中的 view controller，使其填滿 view controller 的內容區域，並將其設定為在 [**條件約束編輯器**] 中的視窗縮小和成長位置：

[![](outline-view-images/edit04.png "Editing the constraints")](outline-view-images/edit04.png#lightbox)

在 [**介面**階層架構] 中選取 [大綱] 視圖，並在**屬性偵測器**中提供下列屬性：

[![](outline-view-images/edit05.png "The Attribute Inspector")](outline-view-images/edit05.png#lightbox)

- **外框資料行**-顯示階層式資料的資料表資料行。
- **自動儲存大綱資料行**-如果 `true`，則會在應用程式執行之間自動儲存和還原大綱資料行。
- **縮排**-在展開的專案下縮排資料行的數量。
- **縮排跟隨資料格**-如果 `true`，則縮排標記會與儲存格一起縮排。
- **自動儲存展開的專案**-如果 `true`，專案的展開/折迭狀態會在應用程式執行之間自動儲存和還原。
- **內容模式**-可讓您使用 Views （`NSView`）或儲存格（`NSCell`），在資料列和資料行中顯示資料。 從 macOS 10.7 開始，您應該使用 Views。
- **浮動群組資料列**-如果 `true`，資料表視圖會繪製已分組的資料格，如同它們是浮動的。
- 資料**行**-定義顯示的資料行數目。
- **標頭**-如果 `true`，則資料行會有標頭。
- 重新**排列**-如果 `true`，使用者將能夠在資料表中拖曳資料行。
- **調整大小**-如果 `true`，使用者將能夠拖曳資料行標頭來調整資料行的大小。
- 資料**行大小調整**-控制資料表自動調整資料行大小的方式。
- 醒目提示-控制選取資料格時，資料表所使用的**反白**顯示類型。
- **替代資料列**-如果 `true`，則其他資料列將會有不同的背景色彩。
- **水準格線**-選取在資料格之間水準繪製的框線類型。
- **垂直格線**-選取在資料格之間垂直繪製的框線類型。
- **方格色彩**-設定儲存格框線色彩。
- **背景**-設定儲存格背景色彩。
- **選取專案**-可讓您控制使用者如何在資料表中選取儲存格，如下所示：
  - **多個**-如果 `true`，使用者可以選取多個資料列和資料行。
  - 資料**行**-如果 `true`，使用者可以選取資料行。
  - **輸入 select** -如果 `true`，則使用者可以輸入字元來選取資料列。
  - **空白**-如果 `true`，則不需要使用者選取資料列或資料行，此資料表完全不允許選取任何專案。
- **自動儲存-資料表**格式的名稱會自動儲存在底下。
- 資料**行資訊**-如果 `true`，則會自動儲存資料行的順序和寬度。
- **分行符號**-選取儲存格處理分行符號的方式。
- **截斷最後一個可見行**-如果 `true`，資料格將會被截斷，而無法放入其範圍內。

> [!IMPORTANT]
> 除非您要維護舊版 Xamarin. Mac 應用程式，否則應該使用以 `NSView` 為基礎的大綱視圖，而不是以 `NSCell` 為基礎的資料表視圖。 `NSCell` 被視為舊版，可能不會繼續支援。

在 [**介面**階層架構] 中選取資料表資料行，並在屬性偵測**器**中提供下列屬性：

[![](outline-view-images/edit06.png "The Attribute Inspector")](outline-view-images/edit06.png#lightbox)

- **Title** -設定資料行的標題。
- **對齊**-設定資料格內的文字對齊方式。
- **標題字型**-選取儲存格的標頭文字字型。
- **排序索引鍵**-這是用來排序資料行中資料的索引鍵。 如果使用者無法排序此資料行，請保留空白。
- **選取器**-這是用來執行排序的**動作**。 如果使用者無法排序此資料行，請保留空白。
- **Order** -這是資料行資料的排序次序。
- 重設**大小**-選取資料行調整大小的類型。
- **可編輯**-如果 `true`，使用者可以編輯以資料格為基礎的資料表中的資料格。
- **Hidden** -如果 `true`，則會隱藏資料行。

您也可以將資料行的控點（垂直置中在資料行右側）拖曳到左邊或右方，以調整其大小。

讓我們選取資料表視圖中的每個資料行，並為第一個資料行指定 `Product` 的**標題**，並將第二個欄位 `Details`。

在 [**介面**階層架構] 中選取資料表資料格視圖（`NSTableViewCell`），並在屬性偵測**器**中提供下列屬性：

[![](outline-view-images/edit07.png "The Attribute Inspector")](outline-view-images/edit07.png#lightbox)

這些是標準視圖的所有屬性。 您也可以選擇在這裡調整此資料行的資料列大小。

在 [**介面**階層架構] 中選取 [資料表視圖] 資料格（根據預設，這是 `NSTextField`），而屬性偵測**器**中可使用下列屬性：

[![](outline-view-images/edit08.png "The Attribute Inspector")](outline-view-images/edit08.png#lightbox)

您將會在這裡設定標準文字欄位的所有屬性。 根據預設，標準文字欄位是用來顯示資料行中儲存格的資料。

在 [**介面**階層架構] 中選取資料表資料格視圖（`NSTableFieldCell`），並在屬性偵測**器**中提供下列屬性：

[![](outline-view-images/edit09.png "The Attribute Inspector")](outline-view-images/edit09.png#lightbox)

以下是最重要的設定：

- **版面**配置-選取此資料行中的儲存格的配置方式。
- **使用單行模式**-如果 `true`，則資料格限制為單一行。
- **第一個運行**時間配置寬度-如果 `true`，資料格會偏好在第一次執行應用程式時顯示的寬度（手動或自動）設定。
- **動作**-控制針對資料格傳送編輯**動作**的時機。
- **行為**-定義是否可選取或編輯儲存格。
- **Rtf** -如果 `true`，資料格可以顯示已格式化和已設定樣式的文字。
- **復原**-如果 `true`，資料格會承擔其復原行為的責任。

在**介面**階層中，選取資料表資料行底部的資料表資料格視圖（`NSTableFieldCell`）：

[![](outline-view-images/edit11.png "Selecting the table cell view")](outline-view-images/edit10.png#lightbox)

這可讓您針對針對指定資料行所建立的所有資料格，編輯當做基底_模式_使用的資料表資料格視圖。

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>新增動作和輸出

就像任何其他的 Cocoa UI 控制項一樣，我們還需要公開大綱視圖，以及使用C# **動作**和**輸出**（根據所需的功能）來撰寫程式碼的資料行和資料格。

對於我們想要公開的任何大綱視圖專案，程式都是相同的：

1. 切換至 [**助理編輯器**]，並確定已選取 [`ViewController.h`] 檔案：

    [![](outline-view-images/edit11.png "Selecting the correct .h file")](outline-view-images/edit11.png#lightbox)
2. 從**介面**階層中選取 [大綱] 視圖，並按一下 [控制項]，並將其拖曳至 [`ViewController.h`] 檔案。
3. 為大綱視圖建立名為 `ProductOutline`的**輸出**：

    [![](outline-view-images/edit13.png "Configuring an Outlet")](outline-view-images/edit13.png#lightbox)
4. 建立 [資料表] 資料行的**輸出**，也稱為 `ProductColumn` 和 `DetailsColumn`：

    [![](outline-view-images/edit14.png "Configuring an Outlet")](outline-view-images/edit14.png#lightbox)
5. 儲存您所做的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

接下來，我們將撰寫程式碼，以在應用程式執行時顯示外框的一些資料。

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>填入大綱視圖

透過在 Interface Builder 中設計的大綱視圖，並透過**插座**公開，接下來我們需要建立C#程式碼來填入它。

首先，讓我們建立新的 `Product` 類別，以保存個別資料列和子產品群組的資訊。 在 **方案總管**中，以滑鼠右鍵按一下專案，然後選取 **加入** > **新增檔案 ...** 選取**一般** > **空白類別**，輸入 `Product` 做為**名稱**，然後按一下 **新增** 按鈕：

[![](outline-view-images/populate01.png "Creating an empty class")](outline-view-images/populate01.png#lightbox)

讓 `Product.cs` 檔案看起來如下所示：

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

接下來，我們需要建立 `NSOutlineDataSource` 的子類別，以提供所要求之大綱的資料。 在 **方案總管**中，以滑鼠右鍵按一下專案，然後選取 **加入** > **新增檔案 ...** 選取**一般** > **空白類別**，輸入 `ProductOutlineDataSource` 做為**名稱**，然後按一下 **新增** 按鈕。

編輯 `ProductTableDataSource.cs` 檔案，使其看起來如下所示：

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

此類別具有大綱視圖專案的儲存體，並會覆寫 `GetChildrenCount` 以傳回資料表中的資料列數目。 `GetChild` 會傳回特定的父系或子專案（如大綱視圖所要求），而 `ItemExpandable` 會將指定的專案定義為父系或子系。

最後，我們需要建立 `NSOutlineDelegate` 的子類別，以提供大綱的行為。 在 **方案總管**中，以滑鼠右鍵按一下專案，然後選取 **加入** > **新增檔案 ...** 選取**一般** > **空白類別**，輸入 `ProductOutlineDelegate` 做為**名稱**，然後按一下 **新增** 按鈕。

編輯 `ProductOutlineDelegate.cs` 檔案，使其看起來如下所示：

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

當我們建立 `ProductOutlineDelegate`的實例時，我們也會傳入提供外框資料的 `ProductOutlineDataSource` 實例。 `GetView` 方法會負責傳回 view （data），以顯示 [提供資料行] 和 [資料列] 的資料格。 可能的話，如果不需要建立新的視圖，就會重複使用現有的視圖來顯示儲存格。

若要填入外框，讓我們編輯 `MainWindow.cs` 檔案，讓 `AwakeFromNib` 方法看起來如下所示：

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

如果我們執行應用程式，則會顯示下列內容：

[![](outline-view-images/populate02.png "The collapsed view")](outline-view-images/populate02.png#lightbox)

如果我們展開大綱視圖中的節點，它看起來會像下面這樣：

[![](outline-view-images/populate03.png "The expanded view")](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>依資料行排序

讓我們藉由按一下資料行標頭，允許使用者排序大綱中的資料。 首先，按兩下 `Main.storyboard` 檔案，在 Interface Builder 中開啟檔案進行編輯。 選取 [`Product`] 資料行，輸入 `Title` 作為 [**排序關鍵字**]，`compare:` 選取選取**器**，然後針對 [**訂單**] 選取 [`Ascending`]：

[![](outline-view-images/sort01.png "Setting the sort key order")](outline-view-images/sort01.png#lightbox)

儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

現在，讓我們編輯 `ProductOutlineDataSource.cs` 檔案，並新增下列方法：

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

`Sort` 方法可讓我們根據給定的 `Product` 類別欄位，以遞增或遞減的順序來排序資料來源中的資料。 每次在資料行標題上按下時，就會呼叫覆寫的 `SortDescriptorsChanged` 方法。 它會傳遞我們在 Interface Builder 中設定的索引**鍵值**，以及該資料行的排序次序。

如果我們執行應用程式並按一下資料行標頭，資料列就會依照該資料行排序：

[![](outline-view-images/sort02.png "Example of sorted output")](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>資料列選取

如果您想要允許使用者選取單一資料列，請按兩下 `Main.storyboard` 檔案，將它開啟，以便在 Interface Builder 中進行編輯。 選取**介面**階層中的 [大綱] 視圖，然後取消核取**屬性偵測器**中的 [**多個**] 核取方塊：

[![](outline-view-images/select01.png "The Attribute Inspector")](outline-view-images/select01.png#lightbox)

儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

接著，編輯 `ProductOutlineDelegate.cs` 檔案，並新增下列方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

這可讓使用者選取大綱視圖中的任何單一資料列。 如果您不想讓使用者能夠選取任何專案，請針對您不想讓使用者選取或 `false` 每個專案的任何專案，傳回 `ShouldSelectItem` 的 `false`。

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>多重資料列選取

如果您想要允許使用者選取多個資料列，請按兩下 `Main.storyboard` 檔案，在 Interface Builder 中開啟該檔案進行編輯。 選取**介面**階層中的 [大綱] 視圖，然後勾選 [屬性偵測**器**] 中的 [**多個**] 核取方塊：

[![](outline-view-images/select02.png "The Attribute Inspector")](outline-view-images/select02.png#lightbox)

儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

接著，編輯 `ProductOutlineDelegate.cs` 檔案，並新增下列方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

這可讓使用者選取大綱視圖中的任何單一資料列。 如果您不想讓使用者能夠選取任何專案，請針對您不想讓使用者選取或 `false` 每個專案的任何專案，傳回 `ShouldSelectRow` 的 `false`。

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>輸入以選取資料列

如果您想要允許使用者輸入已選取大綱視圖的字元，並選取含有該字元的第一個資料列，請按兩下 `Main.storyboard` 檔案，在 Interface Builder 中開啟它進行編輯。 選取**介面**階層中的 [大綱] 視圖，然後勾選 [**屬性偵測器**] 中的 [**類型選取**] 核取方塊：

[![](outline-view-images/type01.png "Editing the row type")](outline-view-images/type01.png#lightbox)

儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

現在，讓我們編輯 `ProductOutlineDelegate.cs` 檔案，並新增下列方法：

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

`GetNextTypeSelectMatch` 方法會接受給定的 `searchString`，並傳回其 `Title`中具有該字串之第一個 `Product` 的專案。

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>重新排列資料行

如果您想要允許使用者拖曳大綱視圖中的 [重新排列資料行]，請按兩下 `Main.storyboard` 檔案，在 Interface Builder 中將其開啟以進行編輯。 選取**介面**階層中的 [大綱] 視圖，然後勾選**屬性偵測器**中的 [重新**排列**] 核取方塊：

[![](outline-view-images/reorder01.png "The Attribute Inspector")](outline-view-images/reorder01.png#lightbox)

如果我們提供 [**自動**儲存] 屬性的值，並核取 [資料**行資訊**] 欄位，則我們對資料表的版面配置所做的任何變更，都會在下次執行應用程式時自動儲存並還原。

儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

現在，讓我們編輯 `ProductOutlineDelegate.cs` 檔案，並新增下列方法：

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

`ShouldReorder` 方法應該會針對想要允許將其拖曳到 `newColumnIndex`中的任何資料行傳回 `true`，否則會傳回 `false`;

如果我們執行應用程式，我們可以拖曳資料行標頭來重新排列資料行：

[![](outline-view-images/reorder02.png "Example of reordering columns")](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>編輯資料格

如果您想要允許使用者編輯指定資料格的值，請編輯 `ProductOutlineDelegate.cs` 檔案，然後變更 `GetViewForItem` 方法，如下所示：

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

現在，如果我們執行應用程式，使用者就可以編輯資料表視圖中的資料格：

[![](outline-view-images/editing01.png "An example of editing cells")](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>在大綱視圖中使用影像

若要在 `NSOutlineView` 中包含影像做為儲存格的一部分，您必須變更大綱視圖的 `NSTableViewDelegate's` `GetView` 方法傳回資料的方式，以使用 `NSTableCellView`，而不是一般的 `NSTextField`。 例如:

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

如需詳細資訊，請參閱我們使用[影像](~/mac/app-fundamentals/image.md)檔的[使用具有大綱視圖的影像](~/mac/app-fundamentals/image.md)一節。

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>資料系結大綱視圖

藉由在 Xamarin. Mac 應用程式中使用索引鍵/值編碼和資料系結技術，您可以大幅減少必須撰寫和維護的程式碼數量，以填入和使用 UI 元素。 您也可以進一步將您的支援資料（_資料模型_）與您的前端使用者介面（_模型視圖控制器_）分離，讓您更容易維護、更有彈性的應用程式設計。

索引鍵/值編碼（KVC）是一種機制，可使用索引鍵（特殊格式的字串）來識別屬性，而不是透過執行個體變數或存取子方法（`get/set`）來存取物件的屬性。 藉由在您的 Xamarin. Mac 應用程式中執行符合索引鍵/值的存取子，您可以存取其他 macOS 功能，例如索引鍵/值觀察（KVO）、資料系結、核心資料、Cocoa 系結和 scriptability。

如需詳細資訊，請參閱資料系結[和索引鍵-值編碼](~/mac/app-fundamentals/databinding.md)檔的[大綱視圖資料](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding)系結一節。

<a name="Summary" />

## <a name="summary"></a>總結

本文已詳細探討如何在 Xamarin. Mac 應用程式中使用大綱視圖。 我們看到了大綱視圖的不同類型和用法，如何在 Xcode 的 Interface Builder 中建立和維護大綱視圖，以及如何在程式碼中C#使用大綱視圖。

## <a name="related-links"></a>相關連結

- [MacOutlines （範例）](https://docs.microsoft.com/samples/xamarin/mac-samples/macoutlines)
- [MacImages (範例)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料表檢視](~/mac/user-interface/table-view.md)
- [來源清單](~/mac/user-interface/source-list.md)
- [資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [大綱視圖簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
