---
title: Xamarin 中的大綱視圖
description: 本文說明如何在 Xamarin 應用程式中使用大綱視圖。 它描述如何在 Xcode 和 Interface Builder 中建立和維護大綱視圖，並以程式設計方式使用它們。
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: c5120fead9084b50912fb4ea1e8cfa100f4b3bbe
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436324"
---
# <a name="outline-views-in-xamarinmac"></a>Xamarin 中的大綱視圖

_本文說明如何在 Xamarin 應用程式中使用大綱視圖。它描述如何在 Xcode 和 Interface Builder 中建立和維護大綱視圖，並以程式設計方式使用它們。_

在 Xamarin 應用程式中使用 c # 和 .NET 時，您可以存取開發人員在 *C* 和 *Xcode* 中運作的相同大綱視圖。 由於 Xamarin 與 Xcode 直接整合，因此您可以使用 Xcode 的 _Interface Builder_ 來建立和維護大綱視圖 (或選擇性地直接在 c # 程式碼) 中建立它們。

大綱視圖是一種資料表類型，可讓使用者展開或折迭階層式資料的資料列。 如同資料表視圖，大綱視圖會顯示一組相關專案的資料，其中包含代表個別專案的資料列，以及代表這些專案屬性的資料行。 不同于資料表視圖，大綱視圖中的專案不在一般清單中，而是組織成階層，例如硬碟上的檔案和資料夾。

[![執行範例應用程式](outline-view-images/populate03.png)](outline-view-images/populate03.png#lightbox)

在本文中，我們將討論在 Xamarin 應用程式中使用大綱視圖的基本概念。 強烈建議您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 文章，特別是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 和 [輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions) 章節的簡介，因為它涵蓋了我們將在本文中使用的重要概念和技術。

您可能會想要看看如何將 [c # 類別/方法公開到](~/mac/internals/how-it-works.md) [Xamarin 內部](~/mac/internals/how-it-works.md) 檔的目標 C 區段，它會說明 `Register` `Export` 用來將 C # 類別連接到目標 c 物件和 UI 元素的和命令。

<a name="Introduction_to_Outline_Views"></a>

## <a name="introduction-to-outline-views"></a>大綱視圖簡介

大綱視圖是一種資料表類型，可讓使用者展開或折迭階層式資料的資料列。 如同資料表視圖，大綱視圖會顯示一組相關專案的資料，其中包含代表個別專案的資料列，以及代表這些專案屬性的資料行。 不同于資料表視圖，大綱視圖中的專案不在一般清單中，而是組織成階層，例如硬碟上的檔案和資料夾。

如果大綱視圖中的某個專案包含其他專案，則可以由使用者展開或折迭。 可擴充的專案會顯示一個洩漏三角形，在專案折迭時指向右邊，並在展開專案時指向右邊。 按一下洩漏三角形會導致專案展開或折迭。

大綱視圖 (`NSOutlineView`) 是資料表視圖的子類別 (`NSTableView`) 因此，會繼承其父類別的許多行為。 如此一來，大綱視圖也支援資料表視圖支援的許多作業，例如選取資料列或資料行、將資料行拖曳至資料行標頭等。 Xamarin 應用程式具有這些功能的控制權，而且可以設定大綱視圖的參數 (在程式碼或 Interface Builder) 中，以允許或不允許某些作業。

大綱視圖不會儲存本身的資料，而是依賴資料來源 (`NSOutlineViewDataSource`) ，視需要提供所需的資料列和資料行。

您可以藉由提供大綱視圖委派的子類別來自訂大綱視圖的行為 `NSOutlineViewDelegate` ， () 來支援大綱資料行管理、輸入來選取功能、選取和編輯資料列、自訂追蹤，以及個別資料行和資料列的自訂視圖。

由於大綱視圖與資料表視圖共用大部分的行為和功能，因此您可能會想要先流覽 [資料表視圖](~/mac/user-interface/table-view.md) 檔，再繼續閱讀本文。

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode"></a>

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>在 Xcode 中建立和維護大綱視圖

當您建立新的 Xamarin Cocoa 應用程式時，預設會取得標準空白的視窗。 此視窗定義于 `.storyboard` 自動包含在專案中的檔案。 若要編輯您的 windows 設計，請在 [ **方案總管**中，按兩下該檔案 `Main.storyboard` ：

[![選取主要分鏡腳本](outline-view-images/edit01.png)](outline-view-images/edit01.png#lightbox)

這會開啟 Xcode Interface Builder 中的視窗設計：

[![在 Xcode 中編輯 UI](outline-view-images/edit02.png)](outline-view-images/edit02.png#lightbox)

`outline`在 [連結**庫偵測器] 的**[搜尋] 方塊中輸入，可讓您更輕鬆地找到大綱視圖控制項：

[![從文件庫選取大綱視圖](outline-view-images/edit03.png)](outline-view-images/edit03.png#lightbox)

將大綱視圖拖曳至 [ **介面編輯器**] 中的 [視圖控制器]，使其填滿視圖控制器的內容區域，然後將它設定為在 [ **條件約束編輯器**] 中使用視窗縮小和成長的位置：

[![編輯條件約束](outline-view-images/edit04.png)](outline-view-images/edit04.png#lightbox)

在 **介面** 階層中選取大綱視圖， **屬性偵測器**中會提供下列屬性：

[![屬性偵測器](outline-view-images/edit05.png)](outline-view-images/edit05.png#lightbox)

- **大綱資料行** -顯示階層式資料的資料表資料行。
- 自動儲存**大綱資料行**-如果為 `true` ，則會自動儲存大綱資料行，並在應用程式執行之間還原。
- **縮排** -展開的專案下要縮排的資料行數量。
- **縮排之後** 的資料格-如果 `true` 是，縮排標記將與儲存格一起縮排。
- **自動儲存展開的專案** -如果為，則會在 `true` 應用程式執行之間自動儲存和還原專案的展開/折迭狀態。
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
> 除非您要維護舊版 Xamarin. Mac 應用程式，否則您 `NSView` 應該使用以資料表為基礎的大綱視圖 `NSCell` 。 `NSCell` 會被視為舊版，未來可能不會支援。

在 [ **介面** ] 階層中選取資料表資料行， **屬性偵測器**中會提供下列屬性：

[![屬性偵測器](outline-view-images/edit06.png)](outline-view-images/edit06.png#lightbox)

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

[![屬性偵測器](outline-view-images/edit07.png)](outline-view-images/edit07.png#lightbox)

這些都是標準視圖的所有屬性。 您也可以選擇在這裡調整此資料行的資料列大小。

選取資料表視圖資料格 (根據預設，這是 `NSTextField` **介面** 階層中的) ，而 **屬性偵測器**中提供下列屬性：

[![屬性偵測器](outline-view-images/edit08.png)](outline-view-images/edit08.png#lightbox)

您將在這裡設定標準文字欄位的所有屬性。 根據預設，標準文字欄位是用來顯示資料行中資料格的資料。

選取 [資料表資料格視圖] (`NSTableFieldCell` **介面** 階層中的) ， **屬性偵測器**中會提供下列屬性：

[![屬性偵測器](outline-view-images/edit09.png)](outline-view-images/edit09.png#lightbox)

以下是最重要的設定：

- 配置：選取此資料行中單元**格的配置**方式。
- **使用單行模式** -如果 `true` ，則資料格會限制為單一行。
- **第一個運行** 時間配置寬度-如果 `true` 是，儲存格會偏好為它設定的寬度 (手動或自動) 在應用程式第一次執行時顯示。
- **Action** -控制何時傳送儲存格的編輯 **動作** 。
- **行為** -定義資料格是否可選取或可編輯。
- **Rich Text** -如果 `true` ，資料格可以顯示格式化和樣式的文字。
- **復原** -如果是 `true` ，儲存格會承擔其復原行為的責任。

在介面階層中，選取資料表資料行底部的 [資料表資料格視圖] (`NSTableFieldCell`) ： **Interface Hierarchy**

[![選取資料表資料格視圖](outline-view-images/edit11.png)](outline-view-images/edit10.png#lightbox)

這可讓您編輯資料表資料格視圖，作為針對指定資料行所建立之所有資料格的基底 _模式_ 。

<a name="Adding_Actions_and_Outlets"></a>

### <a name="adding-actions-and-outlets"></a>新增動作和輸出

就像任何其他 Cocoa UI 控制項一樣，我們也需要根據) 所需的功能，使用 **動作** 和 **輸出** ，將大綱視圖及其資料行和資料格公開到 c # 程式碼 (。

針對我們想要公開的任何大綱視圖專案，此程式都是相同的：

1. 切換至 [ **助理編輯器** ]，並確定 `ViewController.h` 已選取檔案：

    [![選取正確的 .h 檔案](outline-view-images/edit11.png)](outline-view-images/edit11.png#lightbox)
2. 從 **介面**階層選取大綱視圖，並按一下控制項並拖曳至檔案 `ViewController.h` 。
3. 建立大綱視圖的 **輸出** ，稱為 `ProductOutline` ：

    [![設定輸出](outline-view-images/edit13.png)](outline-view-images/edit13.png#lightbox)
4. 建立資料表資料行的 **輸出** ，也稱為 `ProductColumn` 和 `DetailsColumn` ：

    [![設定輸出](outline-view-images/edit14.png)](outline-view-images/edit14.png#lightbox)
5. 儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

接下來，我們將撰寫程式碼，以在執行應用程式時顯示大綱的一些資料。

<a name="Populating_the_Table_View"></a>

## <a name="populating-the-outline-view"></a>填入大綱視圖

在 Interface Builder 中設計的大綱視圖，並透過 **輸出**公開，接下來我們需要建立 c # 程式碼來填入它。

首先，讓我們建立新的 `Product` 類別，以保存個別資料列和子產品群組的資訊。 在 [**方案總管**中，以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新**檔案]。選取 **[一般**  >  **空白類別**]，輸入 `Product` **名稱**，然後按一下 [**新增**] 按鈕：

[![建立空白類別](outline-view-images/populate01.png)](outline-view-images/populate01.png#lightbox)

使檔案 `Product.cs` 看起來如下所示：

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

接下來，我們需要建立的子類別， `NSOutlineDataSource` 以提供所要求之大綱的資料。 在 [**方案總管**中，以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新**檔案]。選取 **[一般**  >  **空白類別**]，輸入 `ProductOutlineDataSource` **名稱**，然後按一下 [**新增**] 按鈕。

編輯檔案 `ProductTableDataSource.cs` ，使其看起來如下所示：

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

此類別具有大綱視圖專案的儲存體，並會覆寫 `GetChildrenCount` 以傳回資料表中的資料列數目。 會 `GetChild` 根據大綱視圖的要求，傳回特定的父專案或子專案 () ，而且會將 `ItemExpandable` 指定的專案定義為父代或子系。

最後，我們需要建立的子類別， `NSOutlineDelegate` 以提供大綱的行為。 在 [**方案總管**中，以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新**檔案]。選取 **[一般**  >  **空白類別**]，輸入 `ProductOutlineDelegate` **名稱**，然後按一下 [**新增**] 按鈕。

編輯檔案 `ProductOutlineDelegate.cs` ，使其看起來如下所示：

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

當我們建立的實例時 `ProductOutlineDelegate` ，我們也 `ProductOutlineDataSource` 會傳入提供外框資料的實例。 `GetView`方法負責傳回 (資料) 的視圖，以顯示提供資料行和資料列的資料格。 如果可能的話，將會重複使用現有的視圖來顯示儲存格，如果您不需要建立新的 view。

為了填入大綱，讓我們編輯檔案 `MainWindow.cs` ，讓 `AwakeFromNib` 方法看起來像下面這樣：

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

如果執行應用程式，則會顯示下列內容：

[![折迭的視圖](outline-view-images/populate02.png)](outline-view-images/populate02.png#lightbox)

如果我們展開大綱視圖中的節點，它看起來會如下所示：

[![展開的視圖](outline-view-images/populate03.png)](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column"></a>

## <a name="sorting-by-column"></a>依資料行排序

讓我們藉由按一下資料行標頭，讓使用者可以排序大綱中的資料。 首先，按兩下檔案 `Main.storyboard` 將它開啟，以在 Interface Builder 中進行編輯。 針對選取 `Product` 器選取資料行，輸入 `Title` **排序關鍵字**， `compare:` 然後**Selector**選取 [ `Ascending` 作為**順序**]：

[![設定排序索引鍵順序](outline-view-images/sort01.png)](outline-view-images/sort01.png#lightbox)

儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

現在讓我們來編輯檔案 `ProductOutlineDataSource.cs` ，並新增下列方法：

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

`Sort`方法可讓我們根據指定的 `Product` 類別欄位，以遞增或遞減順序來排序資料來源中的資料。 `SortDescriptorsChanged`每次在資料行標題上按下滑鼠時，都會呼叫覆寫的方法。 它會傳遞我們在 Interface Builder 中設定的索引 **鍵值** ，以及該資料行的排序次序。

如果我們執行應用程式並按一下資料行標頭，資料列將會依該資料行排序：

[![排序輸出的範例](outline-view-images/sort02.png)](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection"></a>

## <a name="row-selection"></a>選取資料列

如果您想要讓使用者選取單一資料列，請按兩下該檔案來開啟檔案，以便 `Main.storyboard` 在 Interface Builder 中進行編輯。 選取**介面**階層中的大綱視圖，並取消核取**屬性偵測器**中的**多個**核取方塊：

[![屬性偵測器](outline-view-images/select01.png)](outline-view-images/select01.png#lightbox)

儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

接著，編輯檔案 `ProductOutlineDelegate.cs` 並新增下列方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

這可讓使用者選取大綱視圖中的任何單一資料列。 `false` `ShouldSelectItem` `false` 如果您不想讓使用者選取任何專案，請針對您不想讓使用者選取的任何專案，或針對每個專案傳回。

<a name="Multiple_Row_Selection"></a>

## <a name="multiple-row-selection"></a>多重資料列選取

如果您想要允許使用者選取多個資料列，請按兩下該檔案 `Main.storyboard` 以開啟該檔案，以便在 Interface Builder 中進行編輯。 在**介面**階層中選取大綱視圖，並檢查**屬性偵測器**中的**多個**核取方塊：

[![屬性偵測器](outline-view-images/select02.png)](outline-view-images/select02.png#lightbox)

儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

接著，編輯檔案 `ProductOutlineDelegate.cs` 並新增下列方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

這可讓使用者選取大綱視圖中的任何單一資料列。 `false` `ShouldSelectRow` `false` 如果您不想讓使用者選取任何專案，請針對您不想讓使用者選取的任何專案，或針對每個專案傳回。

<a name="Type_to_Select_Row"></a>

## <a name="type-to-select-row"></a>輸入以選取資料列

如果您想要允許使用者輸入已選取大綱視圖的字元，然後選取包含該字元的第一個資料列，請按兩下該檔案 `Main.storyboard` 以開啟該檔案，以便在 Interface Builder 中進行編輯。 在**介面**階層中選取大綱視圖，然後選取**屬性偵測器**中的 [**選取類型**] 核取方塊：

[![編輯資料列類型](outline-view-images/type01.png)](outline-view-images/type01.png#lightbox)

儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

現在讓我們來編輯檔案 `ProductOutlineDelegate.cs` ，並新增下列方法：

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

`GetNextTypeSelectMatch`方法會使用指定的 `searchString` ，並傳回第一個 `Product` 中有該字串的專案 `Title` 。

<a name="Reordering_Columns"></a>

## <a name="reordering-columns"></a>重新排列資料行

如果您想要允許使用者拖曳大綱視圖中的 [重新排列資料行]，請按兩下該檔案以開啟該檔案， `Main.storyboard` 以便在 Interface Builder 中進行編輯。 在**介面**階層中選取大綱視圖，然後勾選**屬性偵測器**中的 [重新**排列**] 核取方塊：

[![屬性偵測器](outline-view-images/reorder01.png)](outline-view-images/reorder01.png#lightbox)

如果我們提供 [ **自動** 儲存] 屬性的值，並檢查 [資料 **行資訊** ] 欄位，我們對資料表配置所做的任何變更都會自動儲存，並在下次執行應用程式時還原。

儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

現在讓我們來編輯檔案 `ProductOutlineDelegate.cs` ，並新增下列方法：

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

`ShouldReorder`方法應該會 `true` 針對想要允許拖曳至的任何資料行傳回，否則會傳回 `newColumnIndex` `false` ;

如果執行應用程式，我們可以拖曳資料行標頭，以重新排列資料行的順序：

[![重新排列資料行的範例](outline-view-images/reorder02.png)](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells"></a>

## <a name="editing-cells"></a>編輯資料格

如果您想要允許使用者編輯指定儲存格的值，請編輯該檔案 `ProductOutlineDelegate.cs` 並變更方法，如下所示 `GetViewForItem` ：

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

[![編輯資料格的範例](outline-view-images/editing01.png)](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views"></a>

## <a name="using-images-in-outline-views"></a>在大綱視圖中使用影像

若要將影像包含在中的儲存格 `NSOutlineView` ，您必須變更大綱視圖的方法傳回資料的方式， `NSTableViewDelegate's` `GetView` 以使用 `NSTableCellView` 而非一般 `NSTextField` 。 例如：

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

如需詳細資訊，請參閱使用[影像](~/mac/app-fundamentals/image.md)檔的[使用影像和大綱視圖](~/mac/app-fundamentals/image.md)一節。

<a name="Data_Binding_Outline_Views"></a>

## <a name="data-binding-outline-views"></a>資料系結大綱視圖

藉由在您的 Xamarin. Mac 應用程式中使用索引鍵/值編碼和資料系結技術，您可以大幅減少必須撰寫和維護的程式碼數量，以填入和使用 UI 元素。 您也可以從前端消費者介面 (_模型-視圖控制器_) ，進一步將支援資料 (_資料模型_分離) ，讓您更容易維護、更具彈性的應用程式設計。

索引鍵/值編碼 (KVC) 是一種機制，可讓您使用索引鍵 (特殊格式的字串來間接存取物件的屬性，) 識別屬性，而不是透過執行個體變數或存取子方法 () 來存取它們 `get/set` 。 藉由在您的 Xamarin 應用程式中執行符合金鑰值的程式碼存取子，您就可以存取其他 macOS 功能，例如索引鍵/值觀察 (KVO) 、資料系結、核心資料、Cocoa 系結和 scriptability。

如需詳細資訊，請參閱資料系結[和索引鍵/值編碼](~/mac/app-fundamentals/databinding.md)檔的「[大綱視圖資料](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding)系結」一節。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文詳細說明如何在 Xamarin 應用程式中使用大綱視圖。 我們看到了大綱視圖的不同類型和使用方式、如何在 Xcode 的 Interface Builder 中建立和維護大綱視圖，以及如何在 c # 程式碼中使用大綱視圖。

## <a name="related-links"></a>相關連結

- [MacOutlines (範例) ](/samples/xamarin/mac-samples/macoutlines)
- [MacImages (範例)](/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料表檢視](~/mac/user-interface/table-view.md)
- [來源清單](~/mac/user-interface/source-list.md)
- [資料繫結和機碼值編碼](~/mac/app-fundamentals/databinding.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [大綱視圖簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)