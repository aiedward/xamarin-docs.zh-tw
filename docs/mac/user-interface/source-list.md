---
title: Xamarin.Mac 中的來源清單
description: 本文涵蓋在 Xamarin.Mac 應用程式中的來源清單。 它說明建立和維護在 Xcode 和 Interface Builder 中的來源清單，並與其互動以 C# 程式碼。
ms.prod: xamarin
ms.assetid: 651A3649-5AA8-4133-94D6-4873D99F7FCC
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 82e4dfb9add7002fd7d3568d0ec946ea38dfd530
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61290558"
---
# <a name="source-lists-in-xamarinmac"></a>Xamarin.Mac 中的來源清單

_本文涵蓋在 Xamarin.Mac 應用程式中的來源清單。它說明建立和維護在 Xcode 和 Interface Builder 中的來源清單，並與其互動以 C# 程式碼。_

使用時C#和.NET Xamarin.Mac 應用程式中的，您可以存取相同的來源清單工作的開發人員*Objective C*並*Xcode*沒有。 由於 Xamarin.Mac 直接與 Xcode 整合，您可以使用 Xcode 的_Interface Builder_來建立，並維護您的來源清單 （或選擇直接在 C# 程式碼中建立它們）。

來源清單是一種特殊的大綱檢視用來顯示動作，例如提要欄位，搜尋或 iTunes 中的來源。

[![](source-list-images/source05.png "範例的來源 清單中選取")](source-list-images/source05.png#lightbox)

在本文中，我們將涵蓋在 Xamarin.Mac 應用程式中使用的來源清單的基本概念。 強烈建議您逐步[Hello，Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)並[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)各節中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於連線接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-source-lists"></a>來源清單簡介

如上所述，來源清單是一種特殊的大綱檢視用來顯示動作，例如提要欄位，搜尋或 iTunes 中的來源。 來源清單是一種資料表，可讓使用者展開或摺疊的階層式資料的資料列。 不同於資料表檢視中，來源清單中的項目不一般的清單中，其組織在階層中，例如硬碟機上檔案和資料夾。 如果來源清單中的項目包含其他項目，它可展開或摺疊，只要使用者。

[來源] 清單是特別指定樣式的大綱檢視 (`NSOutlineView`)，而其本身是 [資料表] 檢視的子類別 (`NSTableView`)，因此，將其行為繼承其父類別。 如此一來，大綱模式中，所支援的許多作業也支援的來源清單。 Xamarin.Mac 應用程式的這些功能的控制，而且可以設定要允許或禁止特定作業的來源清單的參數 （無論是程式碼或 Interface Builder 中）。

來源清單不會儲存它本身的資料，而是它會依賴資料來源 (`NSOutlineViewDataSource`) 提供資料列和資料行所需，以視需要為基礎。

來源清單的行為可以自訂所提供的大綱檢視委派子類別 (`NSOutlineViewDelegate`) 若要支援外框類型選取 [功能]，項目選取和編輯、 自訂追蹤和自訂檢視的個別項目。

因為來源清單會與資料表檢視 」 和 「 大綱檢視的大部分的行為和功能共用，您可能想要瀏覽我們[資料表檢視](~/mac/user-interface/table-view.md)並[大綱檢視](~/mac/user-interface/outline-view.md)文件，然後再繼續與此發行項。

<a name="Working_with_Source_Lists" />

## <a name="working-with-source-lists"></a>使用來源清單

來源清單是一種特殊的大綱檢視用來顯示動作，例如提要欄位，搜尋或 iTunes 中的來源。 不同於大綱檢視中，我們定義介面產生器中的來源清單之前，讓我們建立支援類別 xamarin.mac。

首先，讓我們來建立 新`SourceListItem`類別來保存資料來源清單。 在 **方案總管**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...** 選取**一般** > **空類別**，輸入`SourceListItem`如**名稱**然後按一下**新增**按鈕：

[![](source-list-images/source01.png "新增空的類別")](source-list-images/source01.png#lightbox)

請`SourceListItem.cs`檔案外觀如下所示： 

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

在 [**方案總管**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...** 選取**一般** > **空類別**，輸入`SourceListDataSource`如**名稱**然後按一下**新增**] 按鈕。 請`SourceListDataSource.cs`檔案外觀如下所示：

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

這會提供資料的來源清單。

在 [**方案總管**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...** 選取**一般** > **空類別**，輸入`SourceListDelegate`如**名稱**然後按一下**新增**] 按鈕。 請`SourceListDelegate.cs`檔案外觀如下所示：

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

這會提供我們的來源清單的行為。

最後，在**方案總管**，以滑鼠右鍵按一下專案，然後選取**新增** > **新檔...** 選取**一般** > **空類別**，輸入`SourceListView`如**名稱**然後按一下**新增** 按鈕。 請`SourceListView.cs`檔案外觀如下所示：

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

這會建立自訂、 可重複使用的子`NSOutlineView`(`SourceListView`)，我們可以使用我們所做的任何 Xamarin.Mac 應用程式中的磁碟機來源 清單中的色彩。

<a name="Creating_and_Maintaining_Source_Lists_in_Xcode" />

## <a name="creating-and-maintaining-source-lists-in-xcode"></a>建立和維護在 Xcode 中的來源清單

現在，讓我們設計介面產生器中我們來源清單。 按兩下`Main.storyboard`檔案以開啟它進行編輯介面產生器 中，並拖曳，分割檢視，從**程式庫偵測器**、 將它新增至檢視控制器，並將它調整大小以在檢視與設定**條件約束編輯器**:

[![](source-list-images/source00.png "編輯條件約束")](source-list-images/source00.png#lightbox)

接下來，將從來源清單**程式庫偵測器**、 將它新增至左側的 [分割] 檢視，並將它調整大小以在檢視與設定**條件約束編輯器**:

[![](source-list-images/source02.png "編輯條件約束")](source-list-images/source02.png#lightbox)

接下來，切換至**識別檢視**，選取 [來源] 清單中，並變更它的**類別**到`SourceListView`:

[![](source-list-images/source03.png "設定的類別名稱")](source-list-images/source03.png#lightbox)

最後，建立**插座**來源清單，稱為`SourceList`在`ViewController.h`檔案：

[![](source-list-images/source04.png "設定輸出")](source-list-images/source04.png#lightbox)

儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

<a name="Populating the Source List" />

## <a name="populating-the-source-list"></a>填入來源清單

讓我們編輯`RotationWindow.cs`檔案在 Visual Studio for Mac，並使它的`AwakeFromNib`方法外觀如下所示：

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

`Initialize ()`方法需要呼叫針對來源名單**插座**_之前_加入任何項目。 每個群組的項目，我們會建立父項目，並接著將子項目新增至該群組項目。 每個群組接著會新增至來源清單集合`SourceList.AddItem (...)`。 最後兩行載入資料的來源清單，並展開所有群組：

```csharp
// Display side list
SourceList.ReloadData ();
SourceList.ExpandItem (null, true);
```

最後，編輯`AppDelegate.cs`檔案，並讓`DidFinishLaunching`方法外觀如下所示：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    var rotation = new RotationWindowController ();
    rotation.Window.MakeKeyAndOrderFront (this);
}
```

如果我們執行我們的應用程式時，將會顯示下列：

[![](source-list-images/source05.png "執行範例應用程式")](source-list-images/source05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>總結

本文所深入了解在 Xamarin.Mac 應用程式中使用的來源清單。 我們看到如何建立和維護在 Xcode 的 Interface Builder 中列出的來源以及如何使用 C# 程式碼中的來源清單。

## <a name="related-links"></a>相關連結

- [MacOutlines （範例）](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料表檢視](~/mac/user-interface/table-view.md)
- [大綱檢視](~/mac/user-interface/outline-view.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [大綱檢視的簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
