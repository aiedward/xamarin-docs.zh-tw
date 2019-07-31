---
title: 在 Xamarin. Mac 中複製並貼上
description: 本文說明如何使用貼夾, 以在 Xamarin. Mac 應用程式中提供複製和貼上。 它會示範如何使用可在多個應用程式之間共用的標準資料類型, 以及如何在指定的應用程式中支援自訂資料。
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 61b9d84d6d5882d447a78e6583a399013f8919ef
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656553"
---
# <a name="copy-and-paste-in-xamarinmac"></a>在 Xamarin. Mac 中複製並貼上

_本文說明如何使用貼夾, 以在 Xamarin. Mac 應用程式中提供複製和貼上。它會示範如何使用可在多個應用程式之間共用的標準資料類型, 以及如何在指定的應用程式中支援自訂資料。_

## <a name="overview"></a>總覽

在 Xamarin. C# Mac 應用程式中使用和 .net 時, 您可以存取相同的貼上 (複製和貼上) 支援, 而開發人員在目標中運作。

在本文中, 我們將討論在 Xamarin 應用程式中使用此夾的兩個主要方式:

1. **標準資料類型**-由於「外掛程式」作業通常會在兩個不相關的應用程式之間執行, 因此這兩個應用程式都不知道另一個支援的資料類型。 若要最大化共用的可能性, 您可以將指定專案的多個標記法 (使用一組標準的常見資料類型) 保留給, 讓取用應用程式挑選最適合其需求的版本。
2. **自訂資料**-若要支援在您的 Xamarin 中複製和貼上複雜資料, 您可以定義將由貼夾處理的自訂資料類型。 例如, 可讓使用者複製並貼上多個資料類型和點所組成之複雜圖形的向量繪圖應用程式。

[執行![中應用程式的範例]執行(copy-paste-images/intro01.png "中應用程式的範例")](copy-paste-images/intro01-large.png#lightbox)

在本文中, 我們將討論在 Xamarin. Mac 應用程式中使用剪貼簿的基本概念, 以支援複製和貼上作業。 強烈建議您先流覽[Hello, Mac](~/mac/get-started/hello-mac.md)文章, 特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介, 其中涵蓋了我們將在中使用的重要概念和技巧。本文。

您可能想要查看[Xamarin. Mac 內部](~/mac/internals/how-it-works.md)檔的「將[類別/方法公開C#至目標-C](~/mac/internals/how-it-works.md) 」一節, 它會說明用來將`Register` C#類別`Export`連線到的和屬性。目標-C 物件和 UI 元素。

## <a name="getting-started-with-the-pasteboard"></a>開始使用入門板

「上架」提供了一種標準化的機制, 可在指定的應用程式內或應用程式之間交換資料。 在 Xamarin. Mac 應用程式中, 貼夾的一般用法是處理複製和貼上作業, 不過也支援一些其他作業 (例如, 拖曳 & 卸載和應用程式服務)。

為了讓您快速入門, 我們將從在 Xamarin 應用程式中使用 pasteboards 的簡單、實用簡介開始著手。 稍後, 我們將提供有關「上顯板」運作方式的深入說明, 以及所使用的方法。

在此範例中, 我們將建立以檔為基礎的簡單應用程式, 以管理包含影像視圖的視窗。 使用者將能夠在應用程式中的檔之間, 以及從相同應用程式內的其他應用程式或多個視窗之間複製和貼上影像。

### <a name="creating-the-xamarin-project"></a>建立 Xamarin 專案

首先, 我們將建立以新檔為基礎的 Xamarin. Mac 應用程式, 我們將在其中新增的複製和貼上支援。

請執行下列動作：

1. 啟動 Visual Studio for Mac, 然後按一下 [**新增專案 ...** ] 連結。
2. 選取 [ **Mac**  >  **app**  >  **Cocoa 應用程式**], 然後按 [**下一步]** 按鈕: 

    [![建立新的 Cocoa 應用程式專案](copy-paste-images/sample01.png "建立新的 Cocoa 應用程式專案")](copy-paste-images/sample01-large.png#lightbox)
3. 針對`MacCopyPaste` [**專案名稱**] 輸入, 並保留其他所有內容做為預設值。 按 [下一步]: 

    [![設定專案的名稱](copy-paste-images/sample01a.png "設定專案的名稱")](copy-paste-images/sample01a-large.png#lightbox)

4. 按一下 [**建立**] 按鈕: 

    [![確認新的專案設定](copy-paste-images/sample02.png "確認新的專案設定")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>新增 NSDocument

接下來, 我們將`NSDocument`新增自訂類別, 以作為應用程式使用者介面的背景儲存體。 它會包含單一影像視圖, 並知道如何將影像從視圖複製到預設的貼夾, 以及如何從預設的貼上取出影像, 並將其顯示在影像視圖中。

以滑鼠右鍵按一下**Solution Pad**中的 [Xamarin] 專案, 然後選取 [**加入** > **新**檔案]:

![將 NSDocument 新增至專案](copy-paste-images/sample03.png "將 NSDocument 新增至專案")

輸入 `ImageDocument` 作為 [名稱]  ，然後按一下 [新增]  按鈕。 編輯**ImageDocument.cs**類別, 使其看起來如下所示:

```csharp
using System;
using AppKit;
using Foundation;
using ObjCRuntime;

namespace MacCopyPaste
{
    [Register("ImageDocument")]
    public class ImageDocument : NSDocument
    {
        #region Computed Properties
        public NSImageView ImageView {get; set;}

        public ImageInfo Info { get; set; } = new ImageInfo();

        public bool ImageAvailableOnPasteboard {
            get {
                // Initialize the pasteboard
                NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
                Class [] classArray  = { new Class ("NSImage") };

                // Check to see if an image is on the pasteboard
                return pasteboard.CanReadObjectForClasses (classArray, null);
            }
        }
        #endregion

        #region Constructor
        public ImageDocument ()
        {
        }
        #endregion

        #region Public Methods
        [Export("CopyImage:")]
        public void CopyImage(NSObject sender) {

            // Grab the current image
            var image = ImageView.Image;

            // Anything to process?
            if (image != null) {
                // Get the standard pasteboard
                var pasteboard = NSPasteboard.GeneralPasteboard;

                // Empty the current contents
                pasteboard.ClearContents();

                // Add the current image to the pasteboard
                pasteboard.WriteObjects (new NSImage[] {image});

                // Save the custom data class to the pastebaord
                pasteboard.WriteObjects (new ImageInfo[] { Info });

                // Using a Pasteboard Item
                NSPasteboardItem item = new NSPasteboardItem();
                string[] writableTypes = {"public.text"};

                // Add a data provier to the item
                ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
                var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

                // Save to pasteboard
                if (ok) {
                    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
                }
            }

        }

        [Export("PasteImage:")]
        public void PasteImage(NSObject sender) {

            // Initialize the pasteboard
            NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
            Class [] classArray  = { new Class ("NSImage") };

            bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
                NSImage image = (NSImage)objectsToPaste[0];

                // Display the new image
                ImageView.Image = image;
            }

            Class [] classArray2 = { new Class ("ImageInfo") };
            ok = pasteboard.CanReadObjectForClasses (classArray2, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
                ImageInfo info = (ImageInfo)objectsToPaste[0];

            }

        }
        #endregion
    }
}
```

我們將在下面詳細說明一些程式碼。

下列程式碼會提供屬性, 以測試預設的剪貼簿上是否存在影像資料, 如果有可用的影像, `true`則會傳回其他: `false`

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

下列程式碼會將影像從連結的影像視圖複製到預設的貼夾:

```csharp
[Export("CopyImage:")]
public void CopyImage(NSObject sender) {

    // Grab the current image
    var image = ImageView.Image;

    // Anything to process?
    if (image != null) {
        // Get the standard pasteboard
        var pasteboard = NSPasteboard.GeneralPasteboard;

        // Empty the current contents
        pasteboard.ClearContents();

        // Add the current image to the pasteboard
        pasteboard.WriteObjects (new NSImage[] {image});

        // Save the custom data class to the pastebaord
        pasteboard.WriteObjects (new ImageInfo[] { Info });

        // Using a Pasteboard Item
        NSPasteboardItem item = new NSPasteboardItem();
        string[] writableTypes = {"public.text"};

        // Add a data provider to the item
        ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
        var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

        // Save to pasteboard
        if (ok) {
            pasteboard.WriteObjects (new NSPasteboardItem[] { item });
        }
    }

}
```

下列程式碼會從預設的貼夾中貼入影像, 並將其顯示在附加的影像視圖中 (如果貼夾包含有效的影像):

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0]
    }
}
```

本檔備妥之後, 我們將建立 Xamarin 應用程式的使用者介面。

### <a name="building-the-user-interface"></a>建立使用者介面

按兩下**主要**的分鏡腳本檔案, 在 Xcode 中開啟它。 接下來, 新增工具列和影像, 並設定它們, 如下所示:

[![編輯工具列](copy-paste-images/sample04.png "編輯工具列")](copy-paste-images/sample04-large.png#lightbox)

在工具列的左側新增 [複製並貼上**影像] 工具列專案**。 我們會使用這些快捷方式, 從 [編輯] 功能表複製並貼上。 接下來, 將四個**影像工具列專案**新增至工具列的右邊。 我們將使用這些來填入影像, 並提供一些預設影像。

如需有關使用工具列的詳細資訊, 請參閱我們的[工具列](~/mac/user-interface/toolbar.md)檔。

接下來, 讓我們針對工具列專案和影像的效果, 公開下列輸出和動作:

[![建立輸出和動作](copy-paste-images/sample05.png "建立輸出和動作")](copy-paste-images/sample05-large.png#lightbox)

如需使用「輸出」和「動作」的詳細資訊, 請參閱[Hello, Mac](~/mac/get-started/hello-mac.md)檔的「[輸出與動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」一節。

### <a name="enabling-the-user-interface"></a>啟用使用者介面

藉由在 Xcode 中建立的使用者介面, 以及透過輸出和動作公開的 UI 元素, 我們需要新增程式碼以啟用 UI。 按兩下**Solution Pad**中的**ImageWindow.cs**檔案, 讓它看起來如下所示:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCopyPaste
{
    public partial class ImageWindow : NSWindow
    {
        #region Private Variables
        ImageDocument document;
        #endregion

        #region Computed Properties
        [Export ("Document")]
        public ImageDocument Document {
            get {
                return document;
            }
            set {
                WillChangeValue ("Document");
                document = value;
                DidChangeValue ("Document");
            }
        }

        public ViewController ImageViewController {
            get { return ContentViewController as ViewController; }
        }

        public NSImage Image {
            get {
                return ImageViewController.Image;
            }
            set {
                ImageViewController.Image = value;
            }
        }
        #endregion

        #region Constructor
        public ImageWindow (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create a new document instance
            Document = new ImageDocument ();

            // Attach to image view
            Document.ImageView = ImageViewController.ContentView;
        }
        #endregion

        #region Public Methods
        public void CopyImage (NSObject sender)
        {
            Document.CopyImage (sender);
        }

        public void PasteImage (NSObject sender)
        {
            Document.PasteImage (sender);
        }

        public void ImageOne (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image01.jpg");

            // Set image info
            Document.Info.Name = "city";
            Document.Info.ImageType = "jpg";
        }

        public void ImageTwo (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image02.jpg");

            // Set image info
            Document.Info.Name = "theater";
            Document.Info.ImageType = "jpg";
        }

        public void ImageThree (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image03.jpg");

            // Set image info
            Document.Info.Name = "keyboard";
            Document.Info.ImageType = "jpg";
        }

        public void ImageFour (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image04.jpg");

            // Set image info
            Document.Info.Name = "trees";
            Document.Info.ImageType = "jpg";
        }
        #endregion
    }
}
```

我們將在下面詳細說明此程式碼。

首先, 我們會公開先前建立之`ImageDocument`類別的實例:

```csharp
private ImageDocument _document;
...

[Export ("Document")]
public ImageDocument Document {
    get { return _document; }
    set {
        WillChangeValue ("Document");
        _document = value;
        DidChangeValue ("Document");
    }
}
```

藉由`Export`使用`WillChangeValue` 、 `DidChangeValue`和, 我們已設定`Document`屬性以允許 Xcode 中的索引鍵/值編碼和資料系結。

我們也會從影像中公開影像, 我們在 Xcode 中使用下列屬性新增至 UI:

```csharp
public ViewController ImageViewController {
    get { return ContentViewController as ViewController; }
}

public NSImage Image {
    get {
        return ImageViewController.Image;
    }
    set {
        ImageViewController.Image = value;
    }
}
```

載入並顯示主視窗時, 我們會建立`ImageDocument`類別的實例, 並使用下列程式碼將 UI 的影像附加至它:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create a new document instance
    Document = new ImageDocument ();

    // Attach to image view
    Document.ImageView = ImageViewController.ContentView;
}
```

最後, 為了回應使用者按一下 [複製] 和 [貼上] 工具列專案, 我們會呼叫`ImageDocument`類別的實例來執行實際工作:

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>啟用 [檔案] 和 [編輯] 功能表

我們需要做的最後一件事, 就是從 [檔案]功能表中啟用 [**新增**] 功能表項目 (以建立主視窗的新實例), 並從 [**編輯**] 功能表啟用 [**剪**下]、[**複製**] 和 [**貼**上] 功能表項目。

若要啟用**新**的功能表項目, 請編輯**AppDelegate.cs**檔案, 並新增下列程式碼:

```csharp
public int UntitledWindowCount { get; set;} =1;
...

[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

如需詳細資訊, 請參閱[Windows](~/mac/user-interface/window.md)檔的使用[多個視窗](~/mac/user-interface/window.md)一節。

若要啟用**剪**下、**複製**和**貼**上功能表項目, 請編輯**AppDelegate.cs**檔案, 並新增下列程式碼:

```csharp
[Export("copy:")]
void CopyImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);
}

[Export("cut:")]
void CutImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);

    // Clear the existing image
    window.Image = null;
}

[Export("paste:")]
void PasteImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Paste the image from the clipboard
    window.Document.PasteImage (sender);
}
```

針對每個功能表項目, 我們會取得最新、最上層的索引鍵視窗, `ImageWindow`並將其轉換為類別:

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

我們會從該處呼叫`ImageDocument`該視窗的類別實例, 以處理複製和貼上動作。 例如： 

```csharp
window.Document.CopyImage (sender);
```

我們只想要讓**剪**下、**複製**和**貼**上功能表項目在預設的貼上, 或在目前使用中視窗的影像中有影像資料時, 才可供存取。

讓我們將**EditMenuDelegate.cs**檔案新增到 Xamarin. Mac 專案, 讓它看起來如下所示:

```csharp
using System;
using AppKit;

namespace MacCopyPaste
{
    public class EditMenuDelegate : NSMenuDelegate
    {
        #region Override Methods
        public override void MenuWillHighlightItem (NSMenu menu, NSMenuItem item)
        {
        }

        public override void NeedsUpdate (NSMenu menu)
        {
            // Get list of menu items
            NSMenuItem[] Items = menu.ItemArray ();

            // Get the key window and determine if the required images are available
            var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
            var hasImage = (window != null) && (window.Image != null);
            var hasImageOnPasteboard = (window != null) && window.Document.ImageAvailableOnPasteboard;

            // Process every item in the menu
            foreach(NSMenuItem item in Items) {
                // Take action based on the menu title
                switch (item.Title) {
                case "Cut":
                case "Copy":
                case "Delete":
                    // Only enable if there is an image in the view
                    item.Enabled = hasImage;
                    break;
                case "Paste":
                    // Only enable if there is an image on the pasteboard
                    item.Enabled = hasImageOnPasteboard;
                    break;
                default:
                    // Only enable the item if it has a sub menu
                    item.Enabled = item.HasSubmenu;
                    break;
                }
            }
        }
        #endregion
    }
}
```

同樣地, 我們會取得最新的最上層視窗`ImageDocument` , 並使用其類別實例來查看所需的影像資料是否存在。 然後, 我們會`MenuWillHighlightItem`使用方法, 根據此狀態來啟用或停用每個專案。

編輯**AppDelegate.cs**檔案, 讓`DidFinishLaunching`方法看起來如下所示:
 
```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

首先, 我們會停用 [編輯] 功能表中的 [自動啟用] 和 [停用] 功能表項目。 接下來, 我們會附加先前建立`EditMenuDelegate`之類別的實例。

如需詳細資訊, 請參閱我們的[功能表](~/mac/user-interface/menu.md)檔。

### <a name="testing-the-app"></a>測試應用程式

一切就緒之後, 我們就可以開始測試應用程式了。 建立並執行應用程式, 並顯示主要介面:

執行![應用程式]執行(copy-paste-images/run01.png "應用程式")

如果您開啟 [編輯] 功能表, 請注意 [**剪**下]、[**複製**] 和 [**貼**上] 已停用, 因為影像中沒有影像, 或在預設的貼夾中:

![開啟 [編輯] 功能表](copy-paste-images/run02.png "開啟 [編輯] 功能表")

如果您將影像新增至影像, 並重新開啟 [編輯] 功能表, 現在會啟用專案:

![顯示 [編輯] 功能表項目已啟用](copy-paste-images/run03.png "顯示 [編輯] 功能表項目已啟用")

如果您複製映射並從 [檔案] 功能表中選取 [**新增**], 您可以將該影像貼入新視窗:

![將影像貼入新視窗](copy-paste-images/run04.png "將影像貼入新視窗")

在下列各節中, 我們將詳細說明如何在 Xamarin. Mac 應用程式中使用這些夾。

## <a name="about-the-pasteboard"></a>關於粘貼板

在 macOS (先前稱為 OS X) 中, 貼夾`NSPasteboard`() 提供數個伺服器進程的支援, 例如複製 & 貼入、拖曳 & 卸載和應用程式服務。 在下列各節中, 我們將進一步探討幾個主要的「上轉夾」概念。

### <a name="what-is-a-pasteboard"></a>什麼是粘貼板？

`NSPasteboard`類別提供標準化的機制, 可在應用程式之間或在指定的應用程式中交換資訊。 剪貼夾的主要功能是用來處理複製和貼上作業:

1. 當使用者選取應用程式中的專案, 並使用 [**剪**下] 或 [**複製**] 功能表項目時, 會將所選項目的一個或多個表示放在剪貼簿上。
2. 當使用者使用 [貼上] 功能表項目 (在同一個應用程式內或不同的專案) 時, 它可以處理的資料版本會從**剪貼**板複製並新增至應用程式。

較不明顯的存放夾使用包括尋找、拖曳、拖放和應用程式服務作業:

- 當使用者起始拖曳作業時, 拖曳的資料會複製到 [粘貼]。 如果拖曳作業以拖放到另一個應用程式結束, 該應用程式就會從「存放夾」複製資料。
- 針對轉譯服務, 所要轉譯的資料會由要求的應用程式複製到「上夾」。 應用程式服務、從貼上抓取資料、進行轉譯, 然後將資料貼入貼上。

在其最簡單的形式中, pasteboards 是用來在指定的應用程式內移動資料, 或在應用程式與因此之間的特殊全域記憶體區域中, 存在於應用程式的進程之外。 雖然 pasteboards 的概念很容易 grasps, 但還是有幾個複雜的詳細資料必須考慮。 下面將詳細說明這些功能。

### <a name="named-pasteboards"></a>名為 pasteboards

一種是公用或私用的, 可用於應用程式內或多個應用程式之間的各種用途。 macOS 提供數個標準 pasteboards, 每個都有特定且妥善定義的使用方式:

- `NSGeneralPboard`-**剪**下、**複製**和**貼**上作業的預設貼夾。
- `NSRulerPboard`-支援**尺規**上的**剪**下、**複製**和**貼**上作業。
- `NSFontPboard`-支持對象的**剪**下、**複製**和`NSFont` **貼**上作業。
- `NSFindPboard`-支援可共用搜尋文字的應用程式特定 [尋找] 面板。
- `NSDragPboard`-支援**拖曳 & Drop**作業。

在大多數情況下, 您會使用其中一個系統定義的 pasteboards。 但在某些情況下, 您可能需要建立自己的 pasteboards。 在這些情況下, 您可以使用`FromName (string name)` `NSPasteboard`類別的方法, 以指定的名稱來建立自訂的粘貼夾。

(選擇性) 您可以呼叫`CreateWithUniqueName` `NSPasteboard`類別的方法, 以建立唯一命名的「上夾」。

### <a name="pasteboard-items"></a>粘貼板專案

應用程式寫入至剪貼簿的每個資料片段都會被視為剪貼_板專案_, 而剪貼片可以同時保留多個專案。 如此一來, 應用程式就可以將多個資料版本複製到一個檔案夾 (例如, 純文字和格式化的文字), 而抓取應用程式只能讀取它可以處理的資料 (例如純文字)。

### <a name="data-representations-and-uniform-type-identifiers"></a>資料表示和統一類型識別碼

貼上作業通常會在兩個 (或更多) 不知道彼此知識的應用程式或每個可以處理的資料類型之間進行。 如上一節所述, 若要最大化共用資訊的可能性, 貼夾可以保留所複製和貼上之資料的多種標記法。

每個表示都是透過統一的類型識別碼 (UTI) 來識別, 這只是一個簡單的字串, 可唯一識別所呈現的日期類型 (如需詳細資訊, 請參閱 Apple 的[統一類型識別碼總覽](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319)檔)。 

如果您要建立自訂資料類型 (例如, 向量繪圖應用程式中的繪圖物件), 您可以建立自己的 UTI, 在複製和貼上作業中唯一識別它。

當應用程式準備要貼上從剪貼簿複製的資料時, 它必須找出最適合其功能的標記法 (如果有的話)。 這通常會是最豐富的類型 (例如文字處理應用程式的格式化文字), 會回到所需的最簡單表單 (簡單文字編輯器的純文字)。

<a name="Promised_Data" />

### <a name="promised-data"></a>承諾的資料

一般來說, 您應該盡可能提供要複製的資料, 以最大化應用程式之間的共用。 不過, 由於時間或記憶體的限制, 實際將每種資料類型寫入貼夾中可能不可行。

在這種情況下, 您可以將第一個資料表示放在貼上, 而接收應用程式可以要求不同的標記法, 這可在貼上作業之前立即產生。

當您將初始專案放在貼上時, 您會指定一個或多個可用的標記法是由符合`NSPasteboardItemDataProvider`介面的物件所提供。 根據接收應用程式的要求, 這些物件會依需求提供額外的標記法。

### <a name="change-count"></a>變更計數

每個粘貼夾都會維護每次宣告新的擁有者時, 都會遞增的_變更計數_。 應用程式可以藉由檢查變更計數的值, 來判斷在上一次檢查後, 檔案夾的內容是否已經變更。

使用`NSPasteboard`類別`ChangeCount`的`ClearContents`和方法, 以修改指定的粘貼器變更計數。

## <a name="copying-data-to-a-pasteboard"></a>將資料複製到粘貼夾

執行複製作業的方式, 是先存取一個夾, 清除任何現有的內容, 並視需要將資料的標記法寫入至粘貼器所需的數量。

例如：

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

一般來說, 您只會寫入一般的「夾」, 如同我們在上述範例中所做的一樣。 您傳送至`WriteObjects`方法的任何物件都*必須*符合`INSPasteboardWriting`介面。 有數個內建類別 (例如`NSString` `NSURL`、 `NSImage`、、 `NSColor` `NSAttributedString`、和`NSPasteboardItem`) 會自動符合此介面。

如果您要將自訂資料類別寫入至檔案夾, 它必須符合`INSPasteboardWriting`介面, 或包裝在`NSPasteboardItem`類別的實例中 (請參閱下面的[自訂資料類型](#Custom_Data_Types)一節)。

## <a name="reading-data-from-a-pasteboard"></a>從粘貼夾讀取資料

如上所述, 若要最大化在應用程式之間共用資料的可能性, 已複製資料的多個表示可能會寫入至粘貼夾。 接收端應用程式會根據其功能 (如果有的話) 選取最豐富的版本。

### <a name="simple-paste-operation"></a>簡單貼上作業

您可以使用`ReadObjectsForClasses`方法, 從剪貼簿讀取資料。 它需要兩個參數:

1. 您想要`NSObject`從粘貼夾讀取之型類別類型的陣列。 您應該先使用最需要的資料類型來排序, 並以遞減的喜好設定來進行任何剩餘的類型。
2. 包含其他條件約束 (例如限制特定 URL 內容類型) 的字典, 如果不需要進一步的條件約束, 則為空的字典。

方法會傳回符合我們傳入之準則的專案陣列, 因此最多隻會包含所要求的相同資料類型數目。 也可能沒有任何要求的型別存在, 而且會傳回空陣列。

例如, 下列程式碼會檢查`NSImage`是否存在於一般的夾裡, 並在影像中顯示它 (如果有的話):

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0];
            }
}
```

### <a name="requesting-multiple-data-types"></a>要求多個資料類型

根據所建立之 Xamarin. Mac 應用程式的類型, 它可能可以處理要貼上之資料的多種標記法。 在這種情況下, 有兩種方式可從 [粘貼] 抓取資料:

1. 對`ReadObjectsForClasses`方法進行單一呼叫, 並提供您想要的所有標記法的陣列 (以慣用的順序)。
2. 對`ReadObjectsForClasses`方法多次呼叫, 每次要求一個不同的類型陣列。

如需從貼上抓取資料的詳細資訊, 請參閱上方的**簡單貼**上作業一節。

### <a name="checking-for-existing-data-types"></a>檢查現有的資料類型

有時候, 您可能會想要檢查貼上是否包含指定的資料標記法, 而不會實際讀取貼夾中的資料 (例如, 只有在有效資料存在時才啟用 [**貼入**] 功能表項目)。

呼叫粘貼`CanReadObjectForClasses`夾的方法, 以查看它是否包含指定的類型。

例如, 下列程式碼會判斷一般的上夾是否包含`NSImage`實例:

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

### <a name="reading-urls-from-the-pasteboard"></a>從剪貼簿讀取 url

根據指定的 Xamarin 應用程式的功能, 可能需要從檔案夾讀取 Url, 但必須符合一組指定的準則 (例如指向特定資料類型的檔案或 Url)。 在這種情況下, 您可以使用`CanReadObjectForClasses`或`ReadObjectsForClasses`方法的第二個參數來指定其他搜尋條件。

<a name="Custom_Data_Types" />

## <a name="custom-data-types"></a>自訂資料類型

有時候, 您需要將自己的自訂類型儲存到 Xamarin. Mac 應用程式中的 [粘貼]。 例如, 可讓使用者複製並貼上繪圖物件的向量繪圖應用程式。

在這種情況下, 您必須設計您的資料自訂類別, 使其`NSObject`繼承自, 而且它會符合幾`INSCoding`個介面`INSPasteboardReading`(、 `INSPasteboardWriting`和)。 (選擇性) 您可以使用`NSPasteboardItem`來封裝要複製或貼上的資料。

下面將詳細說明這兩個選項。

### <a name="using-a-custom-class"></a>使用自訂類別

在本節中, 我們將在本檔開頭所建立的簡單範例應用程式中進行擴充, 並新增自訂類別來追蹤我們在 windows 中複製和貼上的影像相關資訊。

將新類別新增至專案, 並將其命名為**ImageInfo.cs**。 編輯檔案, 使其看起來如下所示:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfo")]
    public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
    {
        #region Computed Properties
        [Export("name")]
        public string Name { get; set; }

        [Export("imageType")]
        public string ImageType { get; set; }
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfo ()
        {
        }
        
        public ImageInfo (IntPtr p) : base (p)
        {
        }

        [Export ("initWithCoder:")]
        public ImageInfo(NSCoder decoder) {

            // Decode data
            NSString name = decoder.DecodeObject("name") as NSString;
            NSString type = decoder.DecodeObject("imageType") as NSString;

            // Save data
            Name = name.ToString();
            ImageType = type.ToString ();
        }
        #endregion

        #region Public Methods
        [Export ("encodeWithCoder:")]
        public void EncodeTo (NSCoder encoder) {

            // Encode data
            encoder.Encode(new NSString(Name),"name");
            encoder.Encode(new NSString(ImageType),"imageType");
        }

        [Export ("writableTypesForPasteboard:")]
        public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
            string[] writableTypes = {"com.xamarin.image-info", "public.text"};
            return writableTypes;
        }

        [Export ("pasteboardPropertyListForType:")]
        public virtual NSObject GetPasteboardPropertyListForType (string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSKeyedArchiver.ArchivedDataWithRootObject(this);
            case "public.text":
                return new NSString(string.Format("{0}.{1}", Name, ImageType));
            }

            // Failure, return null
            return null;
        }

        [Export ("readableTypesForPasteboard:")]
        public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
            string[] readableTypes = {"com.xamarin.image-info", "public.text"};
            return readableTypes;
        }

        [Export ("readingOptionsForType:pasteboard:")]
        public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSPasteboardReadingOptions.AsKeyedArchive;
            case "public.text":
                return NSPasteboardReadingOptions.AsString;
            }

            // Default to property list
            return NSPasteboardReadingOptions.AsPropertyList;
        }

        [Export ("initWithPasteboardPropertyList:ofType:")]
        public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return new ImageInfo();
            case "public.text":
                return new ImageInfo();
            }

            // Failure, return null
            return null;
        }
        #endregion
    }
}
    
```

在下列各節中, 我們將詳細探討這個類別。

#### <a name="inheritance-and-interfaces"></a>繼承和介面

您必須先符合`INSPastebaordWriting`和`INSPasteboardReading`介面, 才能在自訂資料類別中寫入或讀取該檔案。 此外, 它必須繼承自`NSObject` , 而且也會符合`INSCoding`介面:

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

類別也必須使用`Register`指示詞公開至目標-C, 而且必須使用`Export`來公開任何必要的屬性或方法。 例如：

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

我們會公開此類別將包含的兩個資料欄位, 也就是影像的名稱及其類型 (jpg、png 等等)。 

如需詳細資訊, 請參閱[Xamarin 內部](~/mac/internals/how-it-works.md)檔的將[類別/方法公開C#至目標-C](~/mac/internals/how-it-works.md)一節, 它會說明`Register`用`Export`來將C#類別連線到的和屬性。目標-C 物件和 UI 元素。

#### <a name="constructors"></a>建構函式

我們的自訂資料類別需要兩個已正確公開至目標-C 的函式, 以便從檔案夾讀取:

```csharp
[Export ("init")]
public ImageInfo ()
{
}

[Export ("initWithCoder:")]
public ImageInfo(NSCoder decoder) {

    // Decode data
    NSString name = decoder.DecodeObject("name") as NSString;
    NSString type = decoder.DecodeObject("imageType") as NSString;

    // Save data
    Name = name.ToString();
    ImageType = type.ToString ();
}
```

首先, 我們會在  的預設目標-C 方法`init`下公開空的函式。

接下來, 我們會`NSCoding`公開相容的函式, 此函式會在貼上匯出的`initWithCoder`名稱下時, 用來從貼夾建立物件的新實例。

此函式`NSCoder`會採用 ( `NSKeyedArchiver`當寫入到儲存夾時所建立)、解壓縮索引鍵/值配對的資料, 並將它儲存至資料類別的屬性欄位。

#### <a name="writing-to-the-pasteboard"></a>寫入到粘貼夾

藉由符合此`INSPasteboardWriting`介面, 我們需要公開兩個方法, 並選擇性地提供第三個方法, 以便將類別寫入至粘貼夾。

首先, 我們需要告訴剪貼簿, 自訂類別可以寫入哪些資料類型表示:

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

每個表示都是透過統一的類型識別碼 (UTI) 來識別, 這只是一個簡單的字串, 可唯一識別所呈現的資料類型 (如需詳細資訊, 請參閱 Apple 的[統一類型識別碼總覽](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319)檔)。

針對我們的自訂格式, 我們會建立自己的 UTI: ".com. image-info" (請注意, 正如應用程式識別碼)。 我們的類別也能夠將標準字串寫入到上夾 (`public.text`)。 

接下來, 我們需要以要求的格式建立物件, 以實際寫入至資料夾:

```csharp
[Export ("pasteboardPropertyListForType:")]
public virtual NSObject GetPasteboardPropertyListForType (string type) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSKeyedArchiver.ArchivedDataWithRootObject(this);
    case "public.text":
        return new NSString(string.Format("{0}.{1}", Name, ImageType));
    }

    // Failure, return null
    return null;
}
```

針對類型, 我們會傳回簡單的格式化`NSString`物件。 `public.text` 針對自訂`com.xamarin.image-info`類型, 我們會`NSKeyedArchiver`使用和`NSCoder`介面, 將自訂資料類別編碼成索引鍵/值配對的封存。 我們必須執行下列方法, 才能實際處理編碼:

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

個別的索引鍵/值組會寫入編碼器, 並使用我們在上面新增的第二個函式進行解碼。

或者, 我們可以包含下列方法, 以定義將資料寫入至 [粘貼] 時的任何選項:

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

目前只有`WritingPromised`選項可供使用, 而且應該在指定的型別只承諾, 而不是實際寫入到「上」的情況時使用。 如需詳細資訊, 請參閱上述的[承諾資料](#Promised_Data)一節。

這些方法都備妥之後, 您可以使用下列程式碼, 將我們的自訂類別寫入至貼夾:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>從剪貼簿讀取

藉由符合此`INSPasteboardReading`介面, 我們需要公開三種方法, 才能從「夾」中讀取自訂資料類別。

首先, 我們需要告訴貼夾自訂類別可以從剪貼簿讀取哪些資料類型表示:

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

同樣地, 這些會定義為簡單的 Uti, 而且與我們在上一節  中所定義的類型相同。

接下來, 我們需要使用下列方法, 告訴剪貼簿_如何_讀取每個 UTI 的類型:

```csharp
[Export ("readingOptionsForType:pasteboard:")]
public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSPasteboardReadingOptions.AsKeyedArchive;
    case "public.text":
        return NSPasteboardReadingOptions.AsString;
    }

    // Default to property list
    return NSPasteboardReadingOptions.AsPropertyList;
}
```

針對此`com.xamarin.image-info`類型, 我們會指示在將類別寫入至 [粘貼器] 時, 將使用所`NSKeyedArchiver`建立的索引鍵/值組解碼, 方法`initWithCoder:`是呼叫我們新增至類別的函式。

最後, 我們需要新增下列方法, 以從 [粘貼] 讀取其他 UTI 資料表示:

```csharp
[Export ("initWithPasteboardPropertyList:ofType:")]
public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

    // Take action based on the requested type
    switch (type) {
    case "public.text":
        return new ImageInfo();
    }

    // Failure, return null
    return null;
}
```

所有這些方法都備妥之後, 您就可以使用下列程式碼, 從貼夾讀取自訂資料類別:

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
var classArrayPtrs = new [] { Class.GetHandle (typeof(ImageInfo)) };
NSArray classArray = NSArray.FromIntPtrs (classArrayPtrs);

// NOTE: Sending messages directly to the base Objective-C API because of this defect:
// https://bugzilla.xamarin.com/show_bug.cgi?id=31760
// Check to see if image info is on the pasteboard
ok = bool_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("canReadObjectForClasses:options:"), classArray.Handle, IntPtr.Zero);

if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = NSArray.ArrayFromHandle<Foundation.NSObject>(IntPtr_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("readObjectsForClasses:options:"), classArray.Handle, IntPtr.Zero));
    ImageInfo info = (ImageInfo)objectsToPaste[0];
}
```

### <a name="using-a-nspasteboarditem"></a>使用 NSPasteboardItem

有時候, 您可能需要將自訂專案寫入不一定要建立自訂類別的檔, 或者您想要以通用格式提供資料, 只需視需要。 在這些情況下, 您可以使用`NSPasteboardItem`。

`NSPasteboardItem`可讓您更精細地控制寫入到「夾」的資料, 並專為暫時存取而設計-在將其寫入至「檔案夾」之後, 應該將它處置。

#### <a name="writing-data"></a>寫入資料

若要將自訂資料寫入`NSPasteboardItem`至, 您必須提供自訂`NSPasteboardItemDataProvider`的。 將新類別新增至專案, 並將其命名為**ImageInfoDataProvider.cs**。 編輯檔案, 使其看起來如下所示:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfoDataProvider")]
    public class ImageInfoDataProvider : NSPasteboardItemDataProvider
    {
        #region Computed Properties
        public string Name { get; set;}
        public string ImageType { get; set;}
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfoDataProvider ()
        {
        }

        public ImageInfoDataProvider (string name, string imageType)
        {
            // Initialize
            this.Name = name;
            this.ImageType = imageType;
        }

        protected ImageInfoDataProvider (NSObjectFlag t){
        }

        protected internal ImageInfoDataProvider (IntPtr handle){

        }
        #endregion

        #region Override Methods
        [Export ("pasteboardFinishedWithDataProvider:")]
        public override void FinishedWithDataProvider (NSPasteboard pasteboard)
        {
            
        }

        [Export ("pasteboard:item:provideDataForType:")]
        public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
        {

            // Take action based on the type
            switch (type) {
            case "public.text":
                // Encode the data to string 
                item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
                break;
            }

        }
        #endregion
    }
}
```

如同我們對自訂資料類別所做的一樣, 我們必須`Register`使用`Export`和指示詞將它公開給目標-C。 類別必須繼承自`NSPasteboardItemDataProvider` , 而且必須`FinishedWithDataProvider`執行和`ProvideDataForType`方法。

使用方法來提供將包裝在中的`NSPasteboardItem`資料, 如下所示: `ProvideDataForType`

```csharp
[Export ("pasteboard:item:provideDataForType:")]
public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
{

    // Take action based on the type
    switch (type) {
    case "public.text":
        // Encode the data to string 
        item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
        break;
    }

}
```

在此情況下, 我們會儲存影像的兩個資訊 (Name 和 ImageType), 並將它們寫入至簡單的字串`public.text`()。

輸入 [將資料寫入至] 夾, 使用下列程式碼:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Using a Pasteboard Item
NSPasteboardItem item = new NSPasteboardItem();
string[] writableTypes = {"public.text"};

// Add a data provider to the item
ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

// Save to pasteboard
if (ok) {
    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
}
```

#### <a name="reading-data"></a>讀取資料

若要從上夾向後讀取資料, 請使用下列程式碼:

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
Class [] classArray  = { new Class ("NSImage") };

bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
    NSImage image = (NSImage)objectsToPaste[0];

    // Do something with data
    ...
}
            
Class [] classArray2 = { new Class ("ImageInfo") };
ok = pasteboard.CanReadObjectForClasses (classArray2, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
    
    // Do something with data
    ...
}
```

## <a name="summary"></a>總結

本文已詳細探討如何在 Xamarin. Mac 應用程式中使用貼文, 以支援複製和貼上作業。 首先, 它引進了一個簡單的範例, 讓您熟悉標準的 pasteboards 作業。 接下來, 它會深入探討「流覽夾」, 以及如何讀取和寫入資料。 最後, 它探討了如何使用自訂資料類型, 以支援在應用程式中複製和貼上複雜資料類型。



## <a name="related-links"></a>相關連結

- [MacCopyPaste (範例)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccopypaste)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [剪貼簿程式設計指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/) \(英文\)
