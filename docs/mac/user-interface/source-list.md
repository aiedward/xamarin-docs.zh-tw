---
title: 來源清單
description: 本文件涵蓋使用 Xamarin.Mac 應用程式中的來源清單。 它說明建立和維護 Xcode 和介面產生器中的來源清單，並在 C# 程式碼中與它們互動。
ms.prod: xamarin
ms.assetid: 651A3649-5AA8-4133-94D6-4873D99F7FCC
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: a8d3a67768b9e47833d1819c3bf44774a52d2438
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="source-lists"></a>來源清單

_本文件涵蓋使用 Xamarin.Mac 應用程式中的來源清單。它說明建立和維護 Xcode 和介面產生器中的來源清單，並在 C# 程式碼中與它們互動。_

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取相同來源清單，工作的開發人員*OBJECTIVE-C*和*Xcode*沒有。 由於直接與 Xcode 整合 Xamarin.Mac，您可以使用 Xcode 的_介面產生器_建立，並維護您的來源清單 （或您可以選擇直接在 C# 程式碼中建立它們）。

來源清單是一種特殊的大綱檢視用來顯示動作，像是在搜尋或 iTunes 提要欄位的來源。

[![](source-list-images/source05.png "清單範例來源")](source-list-images/source05.png#lightbox)

在本文中，我們將討論的基本概念 Xamarin.Mac 應用程式中使用的來源清單。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於網路接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-source-lists"></a>Introduction to 來源清單

如前所述，來源清單是一種特殊的大綱檢視用來顯示動作，像是在搜尋或 iTunes 提要欄位的來源。 來源清單是一種可讓使用者展開或摺疊的階層式資料列。 與資料表檢視中，來源清單中的項目不是一般的清單中，它們會組織在階層中，例如硬碟機上檔案和資料夾。 如果來源清單中的項目會包含其他項目，它可以展開或摺疊，只要使用者。

來源清單是特別樣式的大綱檢視 (`NSOutlineView`)，而其本身是 [資料表] 檢視的子類別 (`NSTableView`)，因此，從其父類別繼承其大部分的行為。 如此一來，大綱模式中，所支援的許多作業也支援的來源清單。 Xamarin.Mac 應用程式控制了這些功能，而且可以設定來允許或禁止特定作業的來源清單的參數 （無論是在程式碼或介面產生器）。

來源清單不會儲存它本身的資料，而是它會仰賴資料來源 (`NSOutlineViewDataSource`) 來提供資料列和資料行所需，以視需要為基礎。

可以自訂來源清單的行為，藉由提供大綱檢視委派的子類別 (`NSOutlineViewDelegate`) 若要支援大綱型別以選取的功能，項目選取和編輯、 自訂的追蹤和自訂檢視的個別項目。

因為來源清單共用許多它的行為與功能以及資料表檢視表和大綱檢視，您可能想要瀏覽我們[資料表檢視](~/mac/user-interface/table-view.md)和[大綱檢視](~/mac/user-interface/outline-view.md)文件，然後再繼續與此發行項。

<a name="Working_with_Source_Lists" />

## <a name="working-with-source-lists"></a>使用來源清單

來源清單是一種特殊的大綱檢視用來顯示動作，像是在搜尋或 iTunes 提要欄位的來源。 不同於大綱檢視中，我們在介面產生器定義來源清單之前，我們來建立支援類別 Xamarin.Mac 中。

首先，我們來建立新`SourceListItem`類別，以保存資料來源清單。 在**方案總管 中**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...**選取**一般** > **空類別**，輸入`SourceListItem`如**名稱**按一下**新增**按鈕：

[![](source-list-images/source01.png "加入空的類別")](source-list-images/source01.png#lightbox)

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

在**方案總管 中**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...**選取**一般** > **空類別**，輸入`SourceListDataSource`如**名稱**按一下**新增** 按鈕。 請`SourceListDataSource.cs`檔案外觀如下所示：

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

這將提供的資料來源清單。

在**方案總管 中**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...**選取**一般** > **空類別**，輸入`SourceListDelegate`如**名稱**按一下**新增** 按鈕。 請`SourceListDelegate.cs`檔案外觀如下所示：

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

最後，在**方案總管 中**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...**選取**一般** > **空類別**，輸入`SourceListView`如**名稱**按一下**新增** 按鈕。 請`SourceListView.cs`檔案外觀如下所示：

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

這會建立自訂、 可重複使用的子類別`NSOutlineView`(`SourceListView`) 可讓我們，我們不提供任何 Xamarin.Mac 應用程式中的磁碟機 [來源] 清單。

<a name="Creating_and_Maintaining_Source_Lists_in_Xcode" />

## <a name="creating-and-maintaining-source-lists-in-xcode"></a>建立和維護在 Xcode 中的來源清單

現在，讓我們設計介面產生器中我們來源清單。 按兩下`Main.storyboard`檔案以開啟介面產生器中進行編輯，並拖曳，分割檢視從**程式庫偵測器**、 將它加入至檢視控制器，並將它設定為與檢視中調整大小**條件約束編輯器**:

[![](source-list-images/source00.png "編輯條件約束")](source-list-images/source00.png#lightbox)

接下來，將從來源清單**程式庫偵測器**、 將它加入至左側的 分割檢視，並將它與檢視中調整大小設定**條件約束編輯器**:

[![](source-list-images/source02.png "編輯條件約束")](source-list-images/source02.png#lightbox)

接下來，切換至**識別檢視**，選取 [來源] 清單中，並變更它的**類別**至`SourceListView`:

[![](source-list-images/source03.png "設定類別名稱")](source-list-images/source03.png#lightbox)

最後，建立**插座**我們來源清單稱為`SourceList`中`ViewController.h`檔案：

[![](source-list-images/source04.png "設定輸出")](source-list-images/source04.png#lightbox)

儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

<a name="Populating the Source List" />

## <a name="populating-the-source-list"></a>填入來源清單

開始編輯`RotationWindow.cs`檔案在 Visual Studio for Mac，並讓它的`AwakeFromNib`方法看起來像下列：

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

`Initialize ()`方法需要針對我們的來源清單呼叫**插座**_之前_加入任何項目。 每個群組的項目，我們會建立父項目，然後將子項目加入至該群組項目。 每個群組會再加入至來源清單集合`SourceList.AddItem (...)`。 最後兩行載入資料來源清單，並展開所有群組：

```csharp
// Display side list
SourceList.ReloadData ();
SourceList.ExpandItem (null, true);
```

最後，編輯`AppDelegate.cs`檔案並製作`DidFinishLaunching`方法看起來像下列：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    var rotation = new RotationWindowController ();
    rotation.Window.MakeKeyAndOrderFront (this);
}
```

當我們執行我們的應用程式時，以下將會顯示：

[![](source-list-images/source05.png "執行範例應用程式")](source-list-images/source05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已取得詳細的探討 Xamarin.Mac 應用程式中使用的來源清單。 我們了解如何建立和維護 Xcode 的介面產生器中會列出來源以及如何使用 C# 程式碼的來源清單。

## <a name="related-links"></a>相關連結

- [MacOutlines （範例）](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料表檢視](~/mac/user-interface/table-view.md)
- [大綱檢視](~/mac/user-interface/outline-view.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Introduction to 大綱檢視](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
