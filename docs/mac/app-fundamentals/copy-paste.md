---
title: 複製並貼入 Xamarin.Mac
description: 這篇文章涵蓋剪貼板提供複製並貼上在 Xamarin.Mac 應用程式中使用。 它示範如何使用標準的資料類型，可以在多個應用程式，以及如何支援自訂資料中指定的應用程式之間共用。
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: f9e05b6d16210021257fe3958966739e526aed18
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61378561"
---
# <a name="copy-and-paste-in-xamarinmac"></a>複製並貼入 Xamarin.Mac

_這篇文章涵蓋剪貼板提供複製並貼上在 Xamarin.Mac 應用程式中使用。它示範如何使用標準的資料類型，可以在多個應用程式，以及如何支援自訂資料中指定的應用程式之間共用。_

## <a name="overview"></a>總覽

在 Xamarin.Mac 應用程式中使用 C# 和.NET，就在 OBJECTIVE-C 開發人員會有相同剪貼板 （複製並貼上） 支援的存取權。

這篇文章中，我們將涵蓋在 Xamarin.Mac 應用程式中使用剪貼板的兩個主要方式：

1. **標準的資料型別**-剪貼板通常執行作業之間兩個不相關的應用程式，因為沒有應用程式知道的其他支援的資料類型。 若要最大化共用的可能性，剪貼板可以保存多個表示指定的項目 （使用一組標準的一般資料類型），這允許使用的應用程式以挑選最適合用於其需求的版本。
2. **自訂資料**-若要支援複製並貼上您 Xamarin.Mac，您可以定義將由剪貼板的自訂資料類型內的複雜資料。 例如，向量繪圖應用程式，可讓使用者複製和貼上多個資料類型和點所組成的複雜圖形。

[![執行應用程式範例](copy-paste-images/intro01.png "執行的應用程式範例")](copy-paste-images/intro01-large.png#lightbox)

在本文中，我們將涵蓋在 Xamarin.Mac 應用程式中支援複製和貼上作業剪貼板所使用基本的概念。 強烈建議您逐步[Hello，Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)並[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)各節中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`屬性用來連接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

## <a name="getting-started-with-the-pasteboard"></a>剪貼板使用者入門

剪貼板提供標準化的機制，來在交換內指定的應用程式或應用程式之間的資料。 剪貼板 Xamarin.Mac 應用程式中一般用法是處理複製並貼上作業，但也支援一些其他作業 （例如拖曳和卸除和應用程式服務）。

若要取得您踏出快速，我們要開始使用 pasteboards Xamarin.Mac 應用程式中的簡單、 實用的介紹。 稍後，我們將提供深入的說明剪貼板的運作方式和使用的方法。

針對此範例中，我們將建立簡單的文件架構應用程式管理包含映像檢視的視窗。 使用者將能夠複製並貼到文件在應用程式和以或從其他應用程式或在相同的應用程式內的多個視窗之間的映像。

### <a name="creating-the-xamarin-project"></a>建立 Xamarin 專案

首先，我們要建立新文件架構 Xamarin.Mac 應用程式我們，將會加入複製並貼上的支援。

請執行下列動作：

1. 啟動 Visual Studio for Mac，然後按一下 **新專案...** 連結。
2. 選取 [ **Mac** > **應用程式** > **Cocoa 應用程式**，然後按一下**下一步]** 按鈕： 

    [![建立新的 Cocoa 應用程式專案](copy-paste-images/sample01.png "建立新的 Cocoa 應用程式專案")](copy-paste-images/sample01-large.png#lightbox)
3. 請輸入`MacCopyPaste`for**專案名稱**並保留所有項目維持為預設值。 按一下 下一步： 

    [![設定專案的名稱](copy-paste-images/sample01a.png "設定專案的名稱")](copy-paste-images/sample01a-large.png#lightbox)

4. 按一下 **建立**按鈕： 

    [![確認新的專案設定](copy-paste-images/sample02.png "確認新的專案設定")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>新增 NSDocument

接下來我們將在其中加入自訂`NSDocument`將做為應用程式的使用者介面的背景儲存體的類別。 它會包含單一的映像檢視，而且知道如何將影像複製到預設剪貼板檢視以及如何採用預設剪貼板的映像，並顯示在 映像檢視。

Xamarin.Mac 專案中以滑鼠右鍵按一下**Solution Pad** ，然後選取**新增** > **新檔...**:

![專案中加入 NSDocument](copy-paste-images/sample03.png "NSDocument 加入專案")

輸入 `ImageDocument` 作為 [名稱]，然後按一下 [新增] 按鈕。 編輯**ImageDocument.cs**類別，並使它看起來如下：

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

讓我們看看一些程式碼的詳細說明如下。

下列程式碼提供的映像是否可用，測試是否存在預設剪貼版上的影像資料的屬性`true`會傳回 else `false`:

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

下列程式碼會將映像複製到預設剪貼板附加的影像檢視：

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

和下列程式碼貼上從預設剪貼板映像，並顯示在 附加的影像檢視 （如果剪貼板包含有效的映像）：

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

使用本文件中的位置中，我們將建立 Xamarin.Mac 應用程式的使用者介面。

### <a name="building-the-user-interface"></a>建置使用者介面

按兩下**Main.storyboard**檔案以在 Xcode 中開啟它。 接下來，也加入一個工具列和映像，並設定它們，如下所示：

[![編輯工具列](copy-paste-images/sample04.png "編輯工具列")](copy-paste-images/sample04-large.png#lightbox)

新增 複製並貼上**影像的工具列項目**工具列的左邊。 我們將使用這些快速鍵為複製並貼上從 [編輯] 功能表。 接下來，新增四個**影像的工具列項目**到工具列的右邊。 我們將使用這些來填入也具有一些預設映像的映像。

如需有關使用 「 工具列的詳細資訊，請參閱我們[工具列](~/mac/user-interface/toolbar.md)文件。

接下來，我們也公開 下列的輸出和我們的工具列項目和映像的動作：

[![建立輸出和動作](copy-paste-images/sample05.png "建立輸出和動作")](copy-paste-images/sample05-large.png#lightbox)

如需有關使用輸出和動作的詳細資訊，請參閱[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)一節我們[Hello，Mac](~/mac/get-started/hello-mac.md)文件。

### <a name="enabling-the-user-interface"></a>啟用使用者介面

我們在 Xcode 和我們會透過輸出和動作公開的 UI 項目中建立的使用者介面，我們需要加入可讓 UI 程式碼。 按兩下**ImageWindow.cs**中的檔案**Solution Pad** ，使它看起來如下所示：

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

讓我們看看這段程式碼的詳細說明如下。

首先，我們會公開的執行個體`ImageDocument`前面所建立的類別：

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

藉由使用`Export`，`WillChangeValue`並`DidChangeValue`，我們已設定`Document`屬性，以允許用於索引鍵-值編碼與在 Xcode 中的資料繫結。

我們也會公開的映像從映像也我們加入我們的 UI 在 Xcode 中使用下列屬性：

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

當主視窗已載入，並顯示時，我們建立的執行個體我們`ImageDocument`類別，並以它為下列程式碼連接 UI 的映像：

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

最後，為了回應使用者按一下的複製和貼上的工具列項目，我們要呼叫的執行個體`ImageDocument`類別，以執行實際工作：

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>啟用 檔案 和 編輯功能表

我們要做的最後一件事就是啟用**的新** 功能表項目**檔案**（若要建立的主視窗的新執行個體） 的功能表，並啟用**剪下**，**複製**並**貼上**中的功能表項目**編輯**功能表。

若要啟用**的新** 功能表項目時，編輯**AppDelegate.cs**檔案，並新增下列程式碼：

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

如需詳細資訊，請參閱[使用多個 Windows](~/mac/user-interface/window.md)一節我們[Windows](~/mac/user-interface/window.md)文件。

若要啟用**剪下**，**複製**並**貼上**功能表項目，編輯**AppDelegate.cs**檔案，並新增下列程式碼：

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

針對每個功能表項目中，我們取得目前、 最上層、 索引鍵 視窗，並將它轉換成我們`ImageWindow`類別：

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

在我們呼叫從該處`ImageDocument`類別執行個體的該視窗來處理複製並貼上動作。 例如： 

```csharp
window.Document.CopyImage (sender);
```

我們只想**剪下**，**複製**並**貼上**功能表項目，如果沒有可以存取映像上預設剪貼板或以及目前使用中視窗的映像中的資料。

讓我們新增**EditMenuDelegate.cs** Xamarin.Mac 專案的檔案，並使它看起來如下：

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

同樣地，我們取得的目前最上層的視窗，並使用其`ImageDocument`類別執行個體所需的影像資料是否存在。 接著，我們使用`MenuWillHighlightItem`方法，以啟用或停用每個項目會根據此狀態。

編輯**AppDelegate.cs**檔案，並讓`DidFinishLaunching`方法外觀如下所示：
 
```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

首先，我們停用自動啟用和停用 [編輯] 功能表中功能表項目。 接下來，我們將附加的執行個體`EditMenuDelegate`前面所建立的類別。

如需詳細資訊，請參閱我們[功能表](~/mac/user-interface/menu.md)文件。

### <a name="testing-the-app"></a>測試應用程式

所需的一切就緒，我們已準備好測試應用程式項目。 建置和執行應用程式，並且會顯示主要的介面：

![執行應用程式](copy-paste-images/run01.png "執行應用程式")

如果您開啟 [編輯] 功能表，請注意**剪下**，**複製**並**貼上**也或預設剪貼板，因為映像中沒有映像已停用：

![開啟 [編輯] 功能表](copy-paste-images/run02.png "開啟 [編輯] 功能表")

如果您也將映像新增至映像，並重新開啟 [編輯] 功能表，則會立即啟用項目：

![顯示 [編輯] 功能表項目會啟用](copy-paste-images/run03.png "顯示 [編輯] 功能表項目已啟用")

如果您複製映像，並選取**新增**從 [檔案] 功能表中，您可以將該映像貼到新的視窗：

![貼入新的視窗中的映像](copy-paste-images/run04.png "貼入新的視窗中的映像")

在下列章節中，我們將詳細的說明了使用剪貼板 Xamarin.Mac 應用程式中。

## <a name="about-the-pasteboard"></a>關於剪貼板

在 macOS （之前稱為 OS X） 剪貼板 (`NSPasteboard`) 提供數個伺服器處理，例如複製和貼上、 拖曳及卸除和應用程式服務的支援。 在下列章節中，我們將探討幾個重要的剪貼板概念。

### <a name="what-is-a-pasteboard"></a>剪貼板是什麼？

`NSPasteboard`類別提供標準化的機制，用來交換應用程式之間或給定應用程式中的資訊。 剪貼板的主要功能是處理複製和貼上作業：

1. 當使用者選取的應用程式中的項目，並使用**剪下**或是**複製**功能表項目，一或多個選取的項目表示會置於剪貼板。
2. 當使用者使用**貼上**功能表項目 （在相同的應用程式或另一個），它可以處理資料的版本會從剪貼板複製，並新增至應用程式。

較不明顯剪貼板用途包括尋找、 拖曳、 拖曳和置放，以及應用程式服務作業：

- 在使用者在拖曳作業時，將資料複製到剪貼板。 如果卸除到另一個應用程式，結束拖曳作業，則該應用程式會將資料複製剪貼板。
- 針對翻譯服務，要轉譯的資料會複製到剪貼板提出要求的應用程式。 應用程式服務，從剪貼板擷取資料，轉譯，則剪貼板上一步 貼上資料。

其最簡單的形式，pasteboards 用來指定應用程式內或應用程式之間移動資料，因此存在於應用程式的程序之外的特殊的全域記憶體區域。 雖然 pasteboards 的觀念是輕鬆 grasps，有數個更複雜的詳細資料，必須考量。 這些會在下方詳細說明。

### <a name="named-pasteboards"></a>具名的 pasteboards

剪貼板可以是公用或私用，並可用於各種應用程式或多個應用程式之間的用途。 macOS 提供數個標準 pasteboards，每個都有特定的定義完善的使用方式：

- `NSGeneralPboard` 針對-預設剪貼板**剪下**，**複製**並**貼上**作業。
- `NSRulerPboard` -支援**剪下**，**複製**並**貼上**作業**尺規**。
- `NSFontPboard` -支援**剪下**，**複製**並**貼上**作業`NSFont`物件。
- `NSFindPboard` -支援特定應用程式尋找可以共用的搜尋文字的面板。
- `NSDragPboard` -支援**拖放**作業。

大部分的情況下，您將使用其中一個系統定義 pasteboards。 但可能會要求您建立您自己 pasteboards 的情況。 在這些情況下，您可以使用`FromName (string name)`方法的`NSPasteboard`類別來建立自訂的剪貼板具有指定名稱。

（選擇性） 您可以呼叫`CreateWithUniqueName`方法的`NSPasteboard`類別來建立唯一命名的剪貼板。

### <a name="pasteboard-items"></a>剪貼板項目

每一筆剪貼板應用程式寫入的資料會被視為_剪貼板項目_，剪貼板可以保存多個項目在相同的時間。 如此一來，應用程式可以寫入多個版本的資料複製到剪貼板 （例如，純文字和格式化的文字） 和擷取應用程式可以讀取關閉的資料，它可以處理 （例如僅限純文字）。

### <a name="data-representations-and-uniform-type-identifiers"></a>資料表示和統一的型別識別項

剪貼板作業通常會採用兩個 （或以上） 之間的應用程式，不知道彼此或資料類型的每個可以處理。 上一節所述，若要最大化可能共用的詳細資訊上的剪貼板可以保存多個表示要複製並貼上的資料。

每個表示法會識別透過統一型別識別項 (UTI)，也就是單純的簡單字串可唯一識別要呈現的日期類型 (如需詳細資訊，請參閱 Apple 的[統一的型別識別項概觀](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319)文件)。 

如果您要建立的自訂資料類型 （例如，向量繪圖應用程式中的繪圖物件），您可以建立您自己的 UTI 來唯一識別在複製和貼上作業。

當應用程式會準備要貼上從剪貼板複製資料時，它必須尋找最符合其功能，（如果有的話） 的表示法。 這通常是最豐富可用的類型 （例如格式化的文字的文字處理應用程式），回到 必要 （純文字的簡單的文字編輯器） 提供了最簡單形式。

<a name="Promised_Data" />

### <a name="promised-data"></a>承諾的資料

一般而言，您應該提供多個要複製的資料，以最大化應用程式之間共用的表示法。 不過，由於時間或記憶體的限制，它可能不切實際實際上寫入剪貼板中的每個資料類型。

在此情況下，您可對剪貼板中的第一個資料表示法，並接收應用程式可以要求不同的表示法，可以產生在即時之前貼上作業。

當您將初始的項目放在剪貼板時，您會指定一或多個其他表示相互轉換可用物件符合所提供`NSPasteboardItemDataProvider`介面。 這些物件會視情況下，提供額外的表示法，依照接收的應用程式的要求。

### <a name="change-count"></a>變更計數

會維護每個剪貼板_變更計數_遞增每個時間的新擁有者宣告。 應用程式可以判斷是否剪貼板的內容已變更它會檢查它檢查的值變更計數的最後一次。

使用`ChangeCount`並`ClearContents`方法`NSPasteboard`類別，以修改給定的剪貼板變更計數。

## <a name="copying-data-to-a-pasteboard"></a>將資料複製到剪貼板

您第一次存取剪貼板、 清除任何現有的內容和寫入的資料，如才能剪貼板的多個表示法，來執行複製作業。

例如：

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

一般而言，您將只會寫入一般剪貼板，因為我們已經在上述範例中。 您將傳送至任何物件`WriteObjects`方法*必須*符合`INSPasteboardWriting`介面。 數個內建類別 (例如`NSString`， `NSImage`， `NSURL`， `NSColor`， `NSAttributedString`，和`NSPasteboardItem`) 自動符合這個介面。

如果您要寫入剪貼版中的自訂資料類別必須符合`INSPasteboardWriting`介面，或執行個體中包裝`NSPasteboardItem`類別 (請參閱[自訂資料型別](#Custom_Data_Types)下一節)。

## <a name="reading-data-from-a-pasteboard"></a>剪貼板中讀取資料

如上所述，若要最大化應用程式之間共用資料的可能性多種表示複製的資料可能會寫入剪貼板。 負責接收的應用程式，以選取可將其功能最豐富的版本 （如果有的話）。

### <a name="simple-paste-operation"></a>簡單的貼上作業

您就會讀取資料剪貼板使用`ReadObjectsForClasses`方法。 這將需要兩個參數：

1. 陣列`NSObject`基礎類別類型，您想要從剪貼板讀取。 您應該訂購這最想要的資料類型第一次，與任何其餘的型別，以遞減喜好設定。
2. 字典，其中包含額外的條件約束 （例如，限制為特定 URL 的內容類型） 或空的字典，如果需要任何進一步的條件約束。

此方法會傳回符合的準則，我們傳入的項目陣列，並因此最多包含相同數目的資料類型的要求。 它也可能是，都要求的型別不存在，會傳回空陣列。

例如，下列程式碼檢查`NSImage`存在於一般剪貼板並將它顯示 image well-影像中，如果是這樣：

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

根據正在建立 Xamarin.Mac 應用程式的類型，它可以處理多個表示正在貼上的資料。 在此情況下，有兩種案例，從剪貼板擷取資料：

1. 單一呼叫`ReadObjectsForClasses`方法，並提供所有您想要 （依偏好順序） 表示法的陣列。
2. 多次呼叫`ReadObjectsForClasses`方法要求的不同的陣列類型的每一次。

請參閱**簡單的貼上作業**區段上方剪貼板從擷取資料的更多詳細資料。

### <a name="checking-for-existing-data-types"></a>檢查現有的資料類型

有些時候您可能想要檢查剪貼板是否包含指定的資料表示法，而不會實際將資料從剪貼板 (例如啟用**貼上**有效資料存在時，才的功能表項目)。

呼叫`CanReadObjectForClasses`剪貼板，以查看它是否包含指定的型別方法。

比方說，下列程式碼會判斷是否一般剪貼板包含`NSImage`執行個體：

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

### <a name="reading-urls-from-the-pasteboard"></a>剪貼板讀取 url

根據給定的 Xamarin.Mac 應用程式的函式，它可能需要 Url 讀取剪貼板，但只符合一組指定的準則 （例如指向檔案或 Url 的特定資料類型）。 在此情況下，您可以指定其他搜尋準則使用的第二個參數`CanReadObjectForClasses`或`ReadObjectsForClasses`方法。

<a name="Custom_Data_Types" />

## <a name="custom-data-types"></a>自訂資料類型

有些的時候，當您將需要儲存剪貼板 Xamarin.Mac 應用程式從您自己的自訂類型。 例如，向量繪圖應用程式，可讓使用者複製和貼上繪製物件。

在此情況下，您將需要設計您的自訂資料類別，讓它繼承自`NSObject`符合幾個介面和 (`INSCoding`，`INSPasteboardWriting`和`INSPasteboardReading`)。 或者，您可以使用`NSPasteboardItem`來封裝要複製或貼上的資料。

兩個選項會在下方詳細說明。

### <a name="using-a-custom-class"></a>使用自訂類別

這一節我們將會擴充我們在這份文件開頭建立簡單的範例應用程式，並新增自訂類別來追蹤視窗之間的映像，我們會複製並貼上的資訊。

將新類別加入專案，並呼叫它**ImageInfo.cs**。 編輯檔案，並讓它看起來如下：

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

下列各節中，我們將詳細的說明了此類別。

#### <a name="inheritance-and-interfaces"></a>繼承和介面

可以寫入或讀取剪貼板自訂資料類別之前，必須符合`INSPastebaordWriting`和`INSPasteboardReading`介面。 此外，它必須繼承自`NSObject`而且也符合`INSCoding`介面：

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

類別也必須公開到 OBJECTIVE-C 使用`Register`指示詞，而且必須公開 （expose) 的任何必要的屬性或方法使用`Export`。 例如: 

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

我們會公開兩個資料欄位的這個類別將包含-映像的名稱和類型 （jpg、 png）。 

如需詳細資訊，請參閱 <<c0> [ 公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，其中說明`Register`和`Export`屬性用來連接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

#### <a name="constructors"></a>建構函式

（正常公開到 Objective C） 的兩個建構函式必須為我們的自訂資料類別，使它能夠讀取從剪貼板：

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

首先，我們會公開_空_下方的預設 OBJECTIVE-C 方法的建構函式`init`。

接下來，我們會公開`NSCoding`相容建構函式是用來匯出名稱下方貼上時，建立物件的新執行個體從剪貼板`initWithCoder`。

這個建構函式會採用`NSCoder`(藉由建立`NSKeyedArchiver`剪貼板寫入時)，會擷取索引鍵/值配對的資料，並將它儲存為資料類別的屬性欄位。

#### <a name="writing-to-the-pasteboard"></a>剪貼板寫入

藉由符合`INSPasteboardWriting`介面，我們要公開 （expose） 兩種方法，並選擇性第三個方法，以便可以寫入剪貼板類別。

首先，我們要告訴剪貼板何種資料類型可以寫入自訂類別的表示法：

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

每個表示法會識別透過統一型別識別項 (UTI)，也就是單純的簡單字串可唯一識別要呈現的資料類型 (如需詳細資訊，請參閱 Apple 的[統一的型別識別項概觀](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319)文件)。

針對我們的自訂格式，我們建立我們自己 UTI: [com.xamarin.image-資訊] （請注意，是以反向的表示法，如同應用程式識別碼）。 我們的類別也是能夠將標準字串寫入至剪貼板 (`public.text`)。 

接下來，我們需要以實際取得寫入至剪貼板所要求的格式建立物件：

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

針對`public.text`類型，我們會傳回一項簡單、 格式化`NSString`物件。 自訂`com.xamarin.image-info`類型，我們會使用`NSKeyedArchiver`而`NSCoder`來編碼索引鍵/值配對的保存檔的自訂資料類別介面。 我們必須實作下列方法，以實際處理的編碼方式：

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

個別的索引鍵/值組編碼器會寫入，且將使用我們在上面新增第二個建構函式已解碼。

（選擇性），我們可以包含下列方法來定義任何選項，將資料寫入至剪貼板時：

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

目前只有`WritingPromised`選項可用，並只承諾並且剪貼板實際上不會寫入指定的型別時，應使用。 如需詳細資訊，請參閱[承諾資料](#Promised_Data)上一節。

使用這些方法在位置中，下列程式碼可用來寫入剪貼板我們的自訂類別：

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>剪貼板讀取

藉由符合`INSPasteboardReading`介面中，我們需要公開三種方法，讓自訂資料類別可讀取剪貼板。

首先，我們要告訴剪貼板何種資料類型的自訂類別可以從剪貼簿讀取的表示法：

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

同樣地，這些定義為簡單的 Uti 和中所定義的類型一樣**寫入剪貼板**上一節。

接下來，我們需要告知剪貼板_如何_是將使用下列方法會讀取每個 UTI 類型：

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

針對`com.xamarin.image-info`類型，我們要告訴我們建立的索引鍵/值組的解碼剪貼板`NSKeyedArchiver`當剪貼板來撰寫類別，藉由呼叫`initWithCoder:`我們加入至類別的建構函式。

最後，我們需要新增下列方法來讀取剪貼板其他 UTI 資料表示法：

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

使用就地這些方法，您可以從使用下列程式碼剪貼板，讀取自訂資料類別：

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

可能有您需要將自訂項目寫入不需要建立自訂類別剪貼板或您想要提供常見的格式，只有所需的資料時的時間。 這些情況下，您可以使用`NSPasteboardItem`。

A`NSPasteboardItem`可微調控制資料寫入至剪貼板，專為暫存的存取-它應該受到處置的寫入剪貼板之後。

#### <a name="writing-data"></a>將資料寫入

若要將您自訂資料寫入至`NSPasteboardItem`您必須提供自訂`NSPasteboardItemDataProvider`。 將新類別加入專案，並呼叫它**ImageInfoDataProvider.cs**。 編輯檔案，並讓它看起來如下：

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

當我們使用自訂資料類別，我們需要使用`Register`和`Export`公開以 OBJECTIVE-C 的指示詞 類別必須繼承自`NSPasteboardItemDataProvider`，而且必須實作`FinishedWithDataProvider`和`ProvideDataForType`方法。

使用`ProvideDataForType`方法，以提供的資料，會包裝在`NSPasteboardItem`，如下所示：

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

在此情況下，我們會儲存有關我們的映像 （名稱和 ImageType） 的兩項資訊，並撰寫簡單的字串到 (`public.text`)。

類型寫入資料至剪貼板，使用下列程式碼：

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

若要從剪貼板讀取資料，使用下列程式碼：

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

本文所深入了解使用剪貼板在 Xamarin.Mac 應用程式中支援複製和貼上作業。 首先，它還會引進一個簡單的範例，可讓您熟悉標準 pasteboards 作業。 接下來，深入了解剪貼板，以及如何讀取和寫入資料，從它所需。 最後，它會討論過使用的自訂資料類型支援的複製和貼上的應用程式中的複雜資料型別。



## <a name="related-links"></a>相關連結

- [MacCopyPaste （範例）](https://developer.xamarin.com/samples/mac/MacCopyPaste/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [剪貼板程式設計指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/) \(英文\)
