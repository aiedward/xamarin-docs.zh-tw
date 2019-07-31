---
title: Xamarin 中的來源清單
description: 本文說明如何在 Xamarin. Mac 應用程式中使用來源清單。 其中說明如何建立和維護 Xcode 和 Interface Builder 中的來源清單, 以及如何C#在程式碼中與其互動。
ms.prod: xamarin
ms.assetid: 651A3649-5AA8-4133-94D6-4873D99F7FCC
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 33ef45fa08748e70ef376e43cb5ed9b12ba55198
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655265"
---
# <a name="source-lists-in-xamarinmac"></a>Xamarin 中的來源清單

_本文說明如何在 Xamarin. Mac 應用程式中使用來源清單。其中說明如何建立和維護 Xcode 和 Interface Builder 中的來源清單, 以及如何C#在程式碼中與其互動。_

在 Xamarin. C# Mac 應用程式中使用和 .net 時, 您可以存取開發人員在*目標-C*和*Xcode*中工作的相同來源清單。 因為 Xamarin 會直接與 Xcode 整合, 所以您可以使用 Xcode 的_Interface Builder_來建立和維護來源清單 (或選擇直接在程式碼中C#建立它們)。

[來源清單] 是一種特殊類型的大綱視圖, 用來顯示動作的來源, 例如搜尋工具或 iTunes 中的側條。

[![](source-list-images/source05.png "範例來源清單")](source-list-images/source05.png#lightbox)

在本文中, 我們將討論在 Xamarin. Mac 應用程式中使用來源清單的基本概念。 強烈建議您先流覽[Hello, Mac](~/mac/get-started/hello-mac.md)文章, 特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介, 其中涵蓋了我們將在中使用的重要概念和技巧。本文。

您可能想要查看[Xamarin. Mac 內部](~/mac/internals/how-it-works.md)檔`Register`的將[類別/方法公開C#至目標-C](~/mac/internals/how-it-works.md)一節, 它會說明用來將C#類別連線到`Export`的和命令。目標-C 物件和 UI 元素。

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-source-lists"></a>來源清單簡介

如上所述, 「來源清單」是一種特殊類型的大綱視圖, 用來顯示動作的來源, 例如搜尋工具或 iTunes 中的側條。 [來源清單] 是一種資料表類型, 可讓使用者展開或折迭階層式資料的資料列。 不同于資料表視圖, 來源清單中的專案不是在一般清單中, 而是在階層中組織, 例如硬碟上的檔案和資料夾。 如果來源清單中的專案包含其他專案, 則可以由使用者展開或折迭。

[來源] 清單是特殊樣式的大綱視圖`NSOutlineView`(), 其本身是資料表視圖的子類別 (`NSTableView`), 因此會從其父類別繼承大部分的行為。 因此, 來源清單也支援大綱視圖所支援的許多作業。 Xamarin 應用程式具有這些功能的控制權, 而且可以設定來源清單的參數 (在程式碼或 Interface Builder 中), 以允許或禁止某些作業。

來源清單不會儲存其本身的資料, 而是依賴資料來源 (`NSOutlineViewDataSource`), 以視需要提供所需的資料列和資料行。

您可以自訂來源清單的行為, 方法是提供大綱視圖委派的子類別`NSOutlineViewDelegate`(), 以支援大綱類型來選取功能、專案選取和編輯、自訂追蹤, 以及個別專案的自訂瀏覽器。

由於來源清單會與資料表視圖和大綱視圖共用大部分的行為和功能, 因此您可能會想要先流覽[資料表的 views](~/mac/user-interface/table-view.md)和[大綱視圖](~/mac/user-interface/outline-view.md)檔, 再繼續進行本文。

<a name="Working_with_Source_Lists" />

## <a name="working-with-source-lists"></a>使用來源清單

[來源清單] 是一種特殊類型的大綱視圖, 用來顯示動作的來源, 例如搜尋工具或 iTunes 中的側條。 不同于大綱視圖, 在 Interface Builder 中定義來源清單之前, 讓我們先在 Xamarin 中建立支援類別。

首先, 讓我們建立新`SourceListItem`的類別來保存來源清單的資料。 在 **方案總管**中, 以滑鼠右鍵按一下專案, 然後選取 **加入** > **新檔案 ...** 。選取**一般**   > **空白類別**, 輸入`SourceListItem`作為 **名稱**, 然後按一下 **新增** 按鈕:

[![](source-list-images/source01.png "加入空的類別")](source-list-images/source01.png#lightbox)

`SourceListItem.cs`使檔案看起來如下所示: 

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListItem: NSObject, IEnumerator, IEnumerable
    {
        #region Private Properties
        private string _title;
        private NSImage _icon;
        private string _tag;
        private List<SourceListItem> _items = new List<SourceListItem> ();
        #endregion

        #region Computed Properties
        public string Title {
            get { return _title; }
            set { _title = value; }
        }

        public NSImage Icon {
            get { return _icon; }
            set { _icon = value; }
        }

        public string Tag {
            get { return _tag; }
            set { _tag=value; }
        }
        #endregion

        #region Indexer
        public SourceListItem this[int index]
        {
            get
            {
                return _items[index];
            }

            set
            {
                _items[index] = value;
            }
        }

        public int Count {
            get { return _items.Count; }
        }

        public bool HasChildren {
            get { return (Count > 0); }
        }
        #endregion

        #region Enumerable Routines
        private int _position = -1;

        public IEnumerator GetEnumerator()
        {
            _position = -1;
            return (IEnumerator)this;
        }

        public bool MoveNext()
        {
            _position++;
            return (_position < _items.Count);
        }

        public void Reset()
        {_position = -1;}

        public object Current
        {
            get 
            { 
                try 
                {
                    return _items[_position];
                }

                catch (IndexOutOfRangeException)
                {
                    throw new InvalidOperationException();
                }
            }
        }
        #endregion

        #region Constructors
        public SourceListItem ()
        {
        }

        public SourceListItem (string title)
        {
            // Initialize
            this._title = title;
        }

        public SourceListItem (string title, string icon)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
        }

        public SourceListItem (string title, string icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
        }

        public SourceListItem (string title, NSImage icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon, string tag)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
        }

        public SourceListItem (string title, NSImage icon, string tag, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
            this.Clicked = clicked;
        }
        #endregion

        #region Public Methods
        public void AddItem(SourceListItem item) {
            _items.Add (item);
        }

        public void AddItem(string title) {
            _items.Add (new SourceListItem (title));
        }

        public void AddItem(string title, string icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, string icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, NSImage icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon, string tag) {
            _items.Add (new SourceListItem (title, icon, tag));
        }

        public void AddItem(string title, NSImage icon, string tag, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, tag, clicked));
        }

        public void Insert(int n, SourceListItem item) {
            _items.Insert (n, item);
        }

        public void RemoveItem(SourceListItem item) {
            _items.Remove (item);
        }

        public void RemoveItem(int n) {
            _items.RemoveAt (n);
        }

        public void Clear() {
            _items.Clear ();
        }
        #endregion

        #region Events
        public delegate void ClickedDelegate();
        public event ClickedDelegate Clicked;

        internal void RaiseClickedEvent() {
            // Inform caller
            if (this.Clicked != null)
                this.Clicked ();
        }
        #endregion
    }
}
```

在 **方案總管**中, 以滑鼠右鍵按一下專案, 然後選取 **加入** > **新檔案 ...** 。選取**一般**   > **空白類別**, 輸入`SourceListDataSource`作為 **名稱**, 然後按一下 **新增** 按鈕。 `SourceListDataSource.cs`使檔案看起來如下所示:

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDataSource : NSOutlineViewDataSource
    {
        #region Private Variables
        private SourceListView _controller;
        #endregion

        #region Public Variables
        public List<SourceListItem> Items = new List<SourceListItem>();
        #endregion

        #region Constructors
        public SourceListDataSource (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Properties
        public override nint GetChildrenCount (NSOutlineView outlineView, Foundation.NSObject item)
        {
            if (item == null) {
                return Items.Count;
            } else {
                return ((SourceListItem)item).Count;
            }
        }

        public override bool ItemExpandable (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, Foundation.NSObject item)
        {
            if (item == null) {
                return Items [(int)childIndex];
            } else {
                return ((SourceListItem)item) [(int)childIndex]; 
            }
        }

        public override NSObject GetObjectValue (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            return new NSString (((SourceListItem)item).Title);
        }
        #endregion

        #region Internal Methods
        internal SourceListItem ItemForRow(int row) {
            int index = 0;

            // Look at each group
            foreach (SourceListItem item in Items) {
                // Is the row inside this group?
                if (row >= index && row <= (index + item.Count)) {
                    return item [row - index - 1];
                }

                // Move index
                index += item.Count + 1;
            }

            // Not found 
            return null;
        }
        #endregion
    }
}
```

這會提供來源清單的資料。

在 **方案總管**中, 以滑鼠右鍵按一下專案, 然後選取 **加入** > **新檔案 ...** 。選取**一般**   > **空白類別**, 輸入`SourceListDelegate`作為 **名稱**, 然後按一下 **新增** 按鈕。 `SourceListDelegate.cs`使檔案看起來如下所示:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDelegate : NSOutlineViewDelegate
    {
        #region Private variables
        private SourceListView _controller;
        #endregion

        #region Constructors
        public SourceListDelegate (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Methods
        public override bool ShouldEditTableColumn (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            return false;
        }

        public override NSCell GetCell (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            nint row = outlineView.RowForItem (item);
            return tableColumn.DataCellForRow (row);
        }

        public override bool IsGroupItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            NSTableCellView view = null;

            // Is this a group item?
            if (((SourceListItem)item).HasChildren) {
                view = (NSTableCellView)outlineView.MakeView ("HeaderCell", this);
            } else {
                view = (NSTableCellView)outlineView.MakeView ("DataCell", this);
                view.ImageView.Image = ((SourceListItem)item).Icon;
            }

            // Initialize view
            view.TextField.StringValue = ((SourceListItem)item).Title;

            // Return new view
            return view;
        }

        public override bool ShouldSelectItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return (outlineView.GetParent (item) != null);
        }

        public override void SelectionDidChange (NSNotification notification)
        {
            NSIndexSet selectedIndexes = _controller.SelectedRows;

            // More than one item selected?
            if (selectedIndexes.Count > 1) {
                // Not handling this case
            } else {
                // Grab the item
                var item = _controller.Data.ItemForRow ((int)selectedIndexes.FirstIndex);

                // Was an item found?
                if (item != null) {
                    // Fire the clicked event for the item
                    item.RaiseClickedEvent ();

                    // Inform caller of selection
                    _controller.RaiseItemSelected (item);
                }
            }
        }
        #endregion
    }
}
```

這會提供來源清單的行為。

最後, 在**方案總管**中, 以滑鼠右鍵按一下專案, 然後選取 [**加入** > **新檔案 ...** ]。選取 **[一般] [**  > **空白類別**], 輸入`SourceListView`作為 [**名稱**], 然後按一下 [**新增**] 按鈕。 `SourceListView.cs`使檔案看起來如下所示:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }
        
        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

這會建立 ( `NSOutlineView` `SourceListView`) 的自訂、可重複使用的子類別, 我們可以用來在我們所做的任何 Xamarin. Mac 應用程式中驅動來源清單。

<a name="Creating_and_Maintaining_Source_Lists_in_Xcode" />

## <a name="creating-and-maintaining-source-lists-in-xcode"></a>在 Xcode 中建立和維護來源清單

現在, 讓我們在 Interface Builder 中設計來源清單。 按兩下檔案以在 Interface Builder 中進行編輯, 並從連結**庫偵測器**拖曳分割視圖, 將它加入至 View 控制器, 並將它設定為在 [**條件約束編輯器**] 中以視圖調整大小: `Main.storyboard`

[![](source-list-images/source00.png "編輯條件約束")](source-list-images/source00.png#lightbox)

接下來, 從 [程式庫偵測**器**] 拖曳 [來源清單], 將它加入分割視圖的左側, 並設定為在 [**條件約束編輯器**] 中以視圖調整大小:

[![](source-list-images/source02.png "編輯條件約束")](source-list-images/source02.png#lightbox)

接下來, 切換至 [**識別] 視圖**, 選取 [來源] 清單, 並將它的**類別**變更為`SourceListView`:

[![](source-list-images/source03.png "設定類別名稱")](source-list-images/source03.png#lightbox)

最後, 針對檔案  中`SourceList` `ViewController.h`名為的來源清單建立一個輸出:

[![](source-list-images/source04.png "設定插座")](source-list-images/source04.png#lightbox)

儲存您的變更, 並返回 Visual Studio for Mac 以與 Xcode 同步。

<a name="Populating the Source List" />

## <a name="populating-the-source-list"></a>填入來源清單

讓我們在 Visual Studio for Mac `RotationWindow.cs`中編輯檔案, 讓它的`AwakeFromNib`方法看起來如下所示:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Populate source list
    SourceList.Initialize ();

    var library = new SourceListItem ("Library");
    library.AddItem ("Venues", "house.png", () => {
        Console.WriteLine("Venue Selected");
    });
    library.AddItem ("Singers", "group.png");
    library.AddItem ("Genre", "cards.png");
    library.AddItem ("Publishers", "box.png");
    library.AddItem ("Artist", "person.png");
    library.AddItem ("Music", "album.png");
    SourceList.AddItem (library);

    // Add Rotation 
    var rotation = new SourceListItem ("Rotation"); 
    rotation.AddItem ("View Rotation", "redo.png");
    SourceList.AddItem (rotation);

    // Add Kiosks
    var kiosks = new SourceListItem ("Kiosks");
    kiosks.AddItem ("Sign-in Station 1", "imac");
    kiosks.AddItem ("Sign-in Station 2", "ipad");
    SourceList.AddItem (kiosks);

    // Display side list
    SourceList.ReloadData ();
    SourceList.ExpandItem (null, true);

}
```

您`Initialize ()`必須針對來源清單的**插座**呼叫方法,_才能_將任何專案加入其中。 針對每個專案群組, 我們會建立父專案, 然後將子專案加入至該群組專案。 然後, 每個群組都會新增至來源清單的`SourceList.AddItem (...)`集合。 最後兩行載入來源清單的資料, 並展開所有群組:

```csharp
// Display side list
SourceList.ReloadData ();
SourceList.ExpandItem (null, true);
```

最後, 編輯`AppDelegate.cs`檔案, `DidFinishLaunching`讓方法看起來如下所示:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    var rotation = new RotationWindowController ();
    rotation.Window.MakeKeyAndOrderFront (this);
}
```

如果我們執行應用程式, 將會顯示下列內容:

[![](source-list-images/source05.png "範例應用程式執行")](source-list-images/source05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>總結

本文已詳細探討如何在 Xamarin. Mac 應用程式中使用來源清單。 我們已瞭解如何在 Xcode 的 Interface Builder 中建立和維護來源清單, 以及如何在程式碼中C#使用來源清單。

## <a name="related-links"></a>相關連結

- [MacOutlines (範例)](https://docs.microsoft.com/samples/xamarin/mac-samples/macoutlines)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料表檢視](~/mac/user-interface/table-view.md)
- [大綱檢視](~/mac/user-interface/outline-view.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [大綱視圖簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
