---
title: "複製並貼上"
description: "本文涵蓋剪貼版提供複製並貼上 Xamarin.Mac 應用程式中使用。 它會顯示如何處理與標準資料型別，可以在多個應用程式，以及如何支援自訂資料內指定的應用程式之間共用。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: ba937a6eae7f0f74bcf044f1248d49a421e82de5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="copy-and-paste"></a>複製並貼上

_本文涵蓋剪貼版提供複製並貼上 Xamarin.Mac 應用程式中使用。它會顯示如何處理與標準資料型別，可以在多個應用程式，以及如何支援自訂資料內指定的應用程式之間共用。_

## <a name="overview"></a>總覽

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取工作 Objective C 中的開發人員具有的相同剪貼版 （複製與貼上） 支援。

本文章中我們將會涵蓋 Xamarin.Mac 應用程式中使用剪貼版的兩種主要方式：

1. **標準的資料型別**-由於剪貼版作業通常執行兩個不相關的應用程式之間，沒有應用程式知道的其他支援的資料類型。 若要最大化共用的可能性，剪貼版可以保存多個表示指定的項目 （使用一組標準的通用資料型別），這允許取用應用程式選擇最適合用於其需要的版本。
2. **自訂資料**-若要支援的複製和貼上的複雜資料，您可以定義將由剪貼版的自訂資料類型您 Xamarin.Mac 內。 例如，向量繪圖應用程式，可讓使用者複製和貼上的多個資料類型和點所組成的複雜圖形。

[![執行中應用程式的範例](copy-paste-images/intro01.png "執行的應用程式的範例")](copy-paste-images/intro01-large.png#lightbox)

在本文中，我們會使用在 Xamarin.Mac 應用程式中支援複製和貼上作業剪貼版的基本。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`屬性用來連接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

## <a name="getting-started-with-the-pasteboard"></a>剪貼版使用者入門

剪貼版提供的標準的機制來交換內指定的應用程式或應用程式之間的資料。 一般用途的剪貼版 Xamarin.Mac 應用程式中為處理複製和貼上作業，但也支援數個其他作業 （例如拖曳和卸除和應用程式服務）。

若要取得您踏出快速，我們將開頭的簡單、 實用簡介使用 pasteboards Xamarin.Mac 應用程式中。 更新版本中，我們將提供深入的說明剪貼版的運作方式和使用的方法。

此範例中，我們將建立簡單基礎的文件應用程式管理包含影像檢視的視窗。 使用者可以複製並貼上在應用程式以及或從其他應用程式或在相同的應用程式內的多個視窗的文件之間的映像。

### <a name="creating-the-xamarin-project"></a>建立 Xamarin 專案

首先，我們要建立新的文件 Xamarin.Mac app 我們，將會加入複製和貼上的支援。

請執行下列動作：

1. 啟動 Visual Studio 用於 Mac 和按一下**新的專案...**連結。
2. 選取**Mac** > **應用程式** > **Cocoa 應用程式**，然後按一下 **下一步**按鈕： 

    [![建立新的 Cocoa 應用程式專案](copy-paste-images/sample01.png "建立新的 Cocoa 應用程式專案")](copy-paste-images/sample01-large.png#lightbox)
3. 輸入`MacCopyPaste`如**專案名稱**並保留為預設值的其他項目。 按 下一步： 

    [![設定專案的名稱](copy-paste-images/sample01a.png "設定專案的名稱")](copy-paste-images/sample01a-large.png#lightbox)

4. 按一下**建立**按鈕： 

    [![確認新的專案設定](copy-paste-images/sample02.png "確認新的專案設定")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>新增 NSDocument

接下來我們要加入自訂`NSDocument`做為背景存放裝置的應用程式的使用者介面的類別。 它會包含單一影像檢視，並知道如何映像複製到剪貼版的預設檢視，以及如何取得從剪貼版的預設映像，並且在影像檢視中顯示。

中的 Xamarin.Mac 專案上按一下滑鼠右鍵**方案板**選取**新增** > **新的檔案...**:

![專案中加入 NSDocument](copy-paste-images/sample03.png "NSDocument 加入專案")

輸入 `ImageDocument` 作為 [名稱]，然後按一下 [新增] 按鈕。 編輯**ImageDocument.cs**類別，並讓它看起來如下所示：

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

讓我們看看一些程式碼的下面將詳細。

下列程式碼提供的屬性，以測試是否存在預設剪貼版上的影像資料的映像是否可以使用，`true`會傳回 else `false`:

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

下列程式碼會從預設剪貼版到附加的映像檢視複製映像：

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

和下列程式碼貼上從剪貼版的預設映像，並在附加的映像 檢視中顯示 （如果剪貼版包含有效的影像）：

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

與本文件中的位置中，我們將建立 Xamarin.Mac 應用程式的使用者介面。

### <a name="building-the-user-interface"></a>建立使用者介面

按兩下**Main.storyboard**在 Xcode 中開啟的檔案。 接下來，以及新增工具列和影像，並設定它們，如下所示：

[![編輯工具列](copy-paste-images/sample04.png "編輯工具列")](copy-paste-images/sample04-large.png#lightbox)

將複製並貼上**影像的工具列項目**工具列的左邊。 我們將使用這些快速鍵為複製和貼上從 [編輯] 功能表。 接下來，加入四個**影像的工具列項目**到工具列的右邊。 我們會使用這些來填入與某些預設映像的映像。

如需使用工具列的詳細資訊，請參閱我們[工具列](~/mac/user-interface/toolbar.md)文件。

接下來，讓我們來公開插座，下列動作，讓我們的工具列項目和影像格式：

[![建立插座和動作](copy-paste-images/sample05.png "建立插座和動作")](copy-paste-images/sample05-large.png#lightbox)

如需有關使用插座及動作的詳細資訊，請參閱[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段我們[Hello、 Mac](~/mac/get-started/hello-mac.md)文件。

### <a name="enabling-the-user-interface"></a>啟用使用者介面

我們在 Xcode 和我們插座和動作透過公開的 UI 項目中建立的使用者介面，我們需要加入程式碼來啟用的 UI。 按兩下**ImageWindow.cs**檔案**方案板**並看起來如下所示：

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

下面將詳細讓我們看看這段程式碼。

首先，我們已公開的執行個體`ImageDocument`前面所建立的類別：

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

使用`Export`，`WillChangeValue`和`DidChangeValue`，我們有安裝`Document`以便進行索引鍵-值撰寫程式碼，並在 Xcode 中的資料繫結的屬性。

我們也會公開映像從映像也我們新增到我們的 UI 在 Xcode 中使用下列屬性：

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

當主視窗已載入，並顯示時，我們建立的執行個體我們`ImageDocument`類別，然後進行附加 UI 的映像也為下列程式碼：

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

最後，為了回應使用者按一下複製和貼上的工具列項目，我們要呼叫的執行個體`ImageDocument`執行實際工作的類別：

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>啟用 檔案 和 編輯功能表

我們要做的最後一件事就是讓**新增**功能表項目從**檔案**功能表 （如果您要建立主視窗的新執行個體），並啟用**剪下**，**複製**和**貼上**功能表項目從**編輯**功能表。

若要啟用**新增**功能表項目、 編輯**d**檔案，然後加入下列程式碼：

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

如需詳細資訊，請參閱[使用多個視窗](~/mac/user-interface/window.md)區段我們[Windows](~/mac/user-interface/window.md)文件。

若要啟用**剪下**，**複製**和**貼上**編輯功能表項目， **d**檔案，然後加入下列程式碼：

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

對於每個功能表項目中，我們取得目前、 最上層、 索引鍵的視窗並將其轉換我們`ImageWindow`類別：

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

我們將從該處`ImageDocument`該視窗來處理複製並貼上動作的類別執行個體。 例如:  

```csharp
window.Document.CopyImage (sender);
```

我們只想要**剪下**，**複製**和**貼上**功能表項目，可供存取，如果沒有影像預設剪貼版或也目前使用中視窗的映像中的資料。

讓我們加入**EditMenuDelegate.cs** Xamarin.Mac 專案檔案，並讓它看起來如下所示：

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

同樣地，我們取得的目前最上層的視窗，並使用其`ImageDocument`必要的影像資料是否存在的類別執行個體。 然後我們使用`MenuWillHighlightItem`此狀態為基礎的方法，以啟用或停用每個項目。

編輯**d**檔案並製作`DidFinishLaunching`方法看起來像下列：
 
```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

首先，我們停用自動啟用及停用 [編輯] 功能表中功能表項目。 接下來，我們將附加的執行個體`EditMenuDelegate`前面所建立的類別。

如需詳細資訊，請參閱我們[功能表](~/mac/user-interface/menu.md)文件。

### <a name="testing-the-app"></a>測試應用程式

一切就緒，我們準備要測試應用程式。 建置並執行應用程式的主要介面會顯示：

![執行應用程式](copy-paste-images/run01.png "執行應用程式")

如果您開啟 [編輯] 功能表，請注意，**剪下**，**複製**和**貼上**會停用或預設剪貼版中，因為映像中任何映像：

![開啟 [編輯] 功能表](copy-paste-images/run02.png "開啟 [編輯] 功能表")

如果您也將影像加入至映像，並重新開啟 [編輯] 功能表，現在會啟用項目：

![顯示編輯功能表項目會啟用](copy-paste-images/run03.png "顯示編輯功能表項目已啟用")

如果您複製映像，並選取**新增**從 [檔案] 功能表中，您可以將該映像貼到新的視窗：

![將影像貼到新的視窗](copy-paste-images/run04.png "貼在新視窗中的映像")

在下列章節中，我們要花詳細的探討剪貼版 Xamarin.Mac 應用程式中使用。

## <a name="about-the-pasteboard"></a>關於剪貼版

（先前稱為 OS X） macOS 中剪貼版 (`NSPasteboard`) 提供數個伺服器處理，例如複製及貼上、 拖曳和卸除和應用程式服務的支援。 在下列章節中，我們將探討一些重要的剪貼版概念。

### <a name="what-is-a-pasteboard"></a>什麼是剪貼版？

`NSPasteboard`類別提供的標準的機制來交換應用程式之間，或指定應用程式中的資訊。 Main 函式剪貼版適用於處理複製和貼上作業：

1. 當使用者選取的應用程式中的項目與**剪下**或**複製**功能表項目，剪貼版上放置一個或多個選取的項目來呈現。
2. 當使用者使用**貼上**功能表項目 （在相同的應用程式或另一部），它可以處理資料的版本已從剪貼版的複製並加入至應用程式。

較不明顯剪貼版用途包括尋找、 拖曳、 拖曳和卸除，以及應用程式服務作業：

- 在使用者在拖曳作業時，拖曳資料會複製到剪貼版。 如果使用放到另一個應用程式結束拖曳作業，該應用程式會將資料複製剪貼版。
- 為轉譯服務来轉譯的資料會複製到剪貼版提出要求的應用程式。 應用程式服務，從剪貼版擷取資料，轉譯，然後貼上資料剪貼版的 上一步。

其最簡單的形式，pasteboards 可用來移動內部給定應用程式或應用程式之間的資料，因此存在於應用程式的程序之外的特殊全域記憶體區域。 雖然 pasteboards 概念輕鬆 grasps，有數個更複雜的詳細資料，必須考量。 這些將在下面將詳細討論。

### <a name="named-pasteboards"></a>具名的 pasteboards

剪貼版可以是公用或私用，並可用於各種用途的應用程式內或多個應用程式之間。 macOS 提供數個標準 pasteboards，每個都有特定的妥善定義的使用方式：

- `NSGeneralPboard` 針對-預設剪貼版**剪下**，**複製**和**貼上**作業。
- `NSRulerPboard` -支援**剪下**，**複製**和**貼上**作業**尺規**。
- `NSFontPboard` -支援**剪下**，**複製**和**貼上**作業`NSFont`物件。
- `NSFindPboard` -支援特定應用程式尋找可以共用搜尋文字的面板。
- `NSDragPboard` -支援**拖曳和卸除**作業。

大部分的情況下，您將使用系統定義 pasteboards 的其中一個。 不過，可能會需要您建立您自己 pasteboards 的情況。 在這些情況下，您可以使用`FromName (string name)`方法`NSPasteboard`類別來建立具有指定名稱的自訂剪貼版。

（選擇性） 您可以呼叫`CreateWithUniqueName`方法`NSPasteboard`類別以建立唯一的具名剪貼版。

### <a name="pasteboard-items"></a>剪貼版的項目

每個應用程式寫入至剪貼版的資料片段會被視為_剪貼版項目_，剪貼版可以保存多個項目，在相同的時間。 如此一來，應用程式可以撰寫多個版本的資料複製到剪貼版 （例如，純文字和格式化的文字） 和擷取應用程式可以讀取關閉的資料，可以處理 （例如，僅限純文字）。

### <a name="data-representations-and-uniform-type-identifiers"></a>資料表示和統一的型別識別項

剪貼版作業通常會採用兩個 （或以上） 之間的應用程式不知道彼此或類型的資料，每個可以處理。 上一節所述，若要共用的詳細資訊，可能會最大化上的剪貼版可以保存多個要複製和貼上的資料表示法。

各個表示法會識別透過統一類型識別項 (UTI)，也就是單純的唯一識別要呈現的日期類型的簡單字串 (如需詳細資訊，請參閱 Apple[統一的型別識別項概觀](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319)文件)。 

如果您要建立自訂資料類型 （例如向量繪圖應用程式中的繪圖物件），您可以建立您自己 UTI 來唯一識別在複製和貼上作業。

當應用程式正在準備從剪貼版複製資料貼上時，它必須尋找最符合其功能 （如果有的話） 的表示法。 這通常是豐富可用的類型 （例如格式化的文字文書處理應用程式），回到最簡單的形式提供必要 （純文字的簡單的文字編輯器）。

<a name="Promised_Data" />

### <a name="promised-data"></a>承諾的資料

一般而言，您應該提供最多複製盡可能以最大化應用程式之間共用的資料表示法。 不過，時間或記憶體條件約束，因為它可能不太實用，實際上寫入剪貼版中的每個資料類型。

在此情況下，您可以將第一個資料表示法置於剪貼版的接收應用程式可以要求不同的表示法，可以產生在作業之前貼上作業。

當您將初始項目放在剪貼版中時，您會指定一或多個其他表示相互轉換使用所提供的物件，並符合`NSPasteboardItemDataProvider`介面。 這些物件會提供額外的表示法，視所接收的應用程式要求。

### <a name="change-count"></a>變更計數

會維護每個剪貼版_變更計數_遞增每個時間的新擁有者宣告。 應用程式可以判斷是否剪貼版的內容已變更它會檢查它檢查的值變更計數的最後一次。

使用`ChangeCount`和`ClearContents`方法`NSPasteboard`類別以修改給定剪貼版變更的計數。

## <a name="copying-data-to-a-pasteboard"></a>將資料複製到剪貼版

您可以執行複製作業的第一個存取剪貼版、 清除任何現有的內容和寫入的資料時所需剪貼版的數量表示法。

例如: 

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

一般而言，您將只會寫入至一般剪貼版，如上述範例中，我們已完成。 您傳送給任何物件`WriteObjects`方法*必須*符合`INSPasteboardWriting`介面。 數個內建類別 (例如`NSString`， `NSImage`， `NSURL`， `NSColor`， `NSAttributedString`，和`NSPasteboardItem`) 自動符合這個介面。

如果您正在撰寫自訂資料類別來剪貼版必須符合`INSPasteboardWriting`介面，或執行個體中包裝`NSPasteboardItem`類別 (請參閱[自訂資料型別](#Custom_Data_Types)下一節)。

## <a name="reading-data-from-a-pasteboard"></a>從剪貼版讀取資料

如前所述，若要最大化潛在的應用程式之間共用的資料複製資料的多個表示法可能會寫入剪貼版的。 這是由接收應用程式，以選取可將其功能豐富的版本 （如果有的話）。

### <a name="simple-paste-operation"></a>簡單貼上作業

您可以讀取資料從剪貼版使用`ReadObjectsForClasses`方法。 它將需要兩個參數：

1. 陣列`NSObject`基礎類別類型，您想要從剪貼版的讀取。 您應該排序，這與最所需的資料類型第一次中, 支援的任何其餘類型排列偏好設定。
2. 字典，其中包含其他的條件約束 （例如限制到特定 URL 的內容類型） 或空的字典是否需要進一步的條件約束。

方法會傳回符合我們傳入之準則的項目陣列，並因此最多包含相同數目的資料類型的要求。 它也可能是，都要求的類型不存在，將會傳回空陣列。

例如，下列程式碼檢查`NSImage`一般剪貼版中存在，而且會顯示在映像以及：

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

根據 Xamarin.Mac 應用程式所建立的類型，它可能是能夠處理多個要貼上的資料表示法。 在此情況下，有兩種案例，從剪貼版的擷取資料：

1. 單一呼叫`ReadObjectsForClasses`方法並提供所有您想要 （依偏好的順序） 表示法的陣列。
2. 多次呼叫`ReadObjectsForClasses`方法不同的陣列的要求類型的每一次。

請參閱**簡單貼上作業**節，如需有關從剪貼版擷取資料。

### <a name="checking-for-existing-data-types"></a>檢查現有資料型別

有時候您可能想要檢查剪貼版是否包含指定的資料表示法，而不會實際資料從剪貼版的 (例如啟用**貼上**有效資料存在時，才的功能表項目)。

呼叫`CanReadObjectForClasses`以查看它是否包含指定的型別剪貼版方法。

比方說，下列程式碼會判斷是否一般剪貼版包含`NSImage`執行個體：

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

### <a name="reading-urls-from-the-pasteboard"></a>讀取剪貼版的 url

根據給定的 Xamarin.Mac 應用程式的函式，可能需要 Url 讀取剪貼版，但只有符合指定的準則 （例如，指向檔案或 Url 的特定資料類型） 的集合。 在此情況下，您可以指定其他搜尋準則使用的第二個參數`CanReadObjectForClasses`或`ReadObjectsForClasses`方法。

<a name="Custom_Data_Types" />

## <a name="custom-data-types"></a>自訂資料型別

但有些的時候，當您將需要從 Xamarin.Mac 應用程式，將您自己自訂的類型儲存至剪貼版。 比方說，一個繪圖應用程式，可讓使用者複製和貼上繪製物件的向量。

在此情況下，您必須設計您的自訂資料類別，讓它繼承自`NSObject`和其符合幾個介面 (`INSCoding`，`INSPasteboardWriting`和`INSPasteboardReading`)。 或者，您可以使用`NSPasteboardItem`來封裝要複製或貼上的資料。

將下面將詳細討論這兩個選項。

### <a name="using-a-custom-class"></a>使用自訂的類別

這一節我們將會擴充上簡單的範例應用程式，我們建立這份文件的開頭，而且加入了自訂類別來追蹤視窗之間，我們會複製並貼上影像的相關資訊。

將新類別加入至專案，並為它**ImageInfo.cs**。 編輯檔案，並讓它看起來如下所示：

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

下列各節中，我們將這個類別的詳細的檢視。

#### <a name="inheritance-and-interfaces"></a>繼承和介面

自訂資料類別可以寫入或讀取剪貼版之前，必須符合`INSPastebaordWriting`和`INSPasteboardReading`介面。 此外，它必須繼承自`NSObject`，而且也符合`INSCoding`介面：

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

類別也必須公開給 Objective C 使用`Register`指示詞，而且必須公開 （expose) 任何必要的屬性或方法使用`Export`。 例如: 

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

我們將兩個欄位的資料，此類別會包含-映像的名稱和類型 （jpg、 png 等等）。 

如需詳細資訊，請參閱[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它說明`Register`和`Export`屬性用來連接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

#### <a name="constructors"></a>建構函式

（正確公開 Objective C） 的兩個建構函式必須為自訂資料類別，使它能夠讀取從剪貼版：

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

首先，我們展現_空_下預設 OBJECTIVE-C 方法的建構函式`init`。

接下來，我們展現`NSCoding`相容建構函式將用於匯出的名稱下貼上時，建立物件的新執行個體從剪貼版的`initWithCoder`。

這個建構函式會採用`NSCoder`(因為由建立`NSKeyedArchiver`剪貼版的寫入時)，會擷取索引鍵/值配對的資料，並將它儲存為資料類別的屬性欄位。

#### <a name="writing-to-the-pasteboard"></a>寫入剪貼版

藉由符合`INSPasteboardWriting`介面，我們需要公開兩種方法，以及選擇性的第三個方法，以便可以寫入剪貼版的類別。

首先，我們要告知剪貼版的資料型別可以寫入自訂類別的表示法：

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

各個表示法會識別透過統一類型識別項 (UTI)，也就是單純的唯一識別要呈現的資料類型的簡單字串 (如需詳細資訊，請參閱 Apple[統一的型別識別項概觀](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319)文件)。

我們的自訂格式，我們會建立自己的 UTI: 「 com.xamarin.image-資訊 」 （請注意，反向就像是應用程式識別項的標記法）。 類別也是能夠寫入剪貼版的標準字串 (`public.text`)。 

接下來，我們需要建立物件與剪貼版的實際寫入要求的格式：

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

如`public.text`類型，我們會傳回簡單、 格式化`NSString`物件。 自訂`com.xamarin.image-info`類型，我們將使用`NSKeyedArchiver`和`NSCoder`来編碼到索引鍵/值配對封存的自訂資料類別的介面。 我們必須實作下列方法，以實際處理編碼：

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

個別的索引鍵/值組會寫入至編碼器，而且我們上述新增的第二個建構函式會解碼。

選擇性地，我們可以包含下列方法來定義的任何選項時將資料寫入至剪貼版：

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

目前只有`WritingPromised`選項提供，且指定的型別是只承諾，因此實際上不會寫入剪貼版時，應使用。 如需詳細資訊，請參閱[承諾資料](#Promised_Data)上一節。

利用這些方法中的位置，下列程式碼可用來寫入剪貼版的我們的自訂類別：

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>讀取剪貼版

藉由符合`INSPasteboardReading`我們要公開的三種方法，讓自訂資料類別可讀取剪貼版的介面。

首先，我們要告知剪貼版的何種資料類型的自訂類別可以從剪貼簿讀取的表示法：

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

同樣地，這些定義為簡單 UTIs 和我們中定義的類型相同**寫入剪貼版的**上一節。

接下來，我們要告知剪貼版的_如何_UTI 型別會使用下列方法來讀取：

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

如`com.xamarin.image-info`類型，我們會告知剪貼版的解碼我們建立的索引鍵/值組`NSKeyedArchiver`時寫入藉由呼叫剪貼版的類別`initWithCoder:`我們加入至類別的建構函式。

最後，我們需要將下列方法來讀取剪貼版的其他 UTI 資料表示法：

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

使用就地這些方法，自訂資料類別可讀取剪貼版，使用下列程式碼：

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

有些時候可能會當您需要將自訂項目寫入剪貼版不需要建立自訂類別或您想要提供常見的格式，只視需要的資料。 這些情況下，您可以使用`NSPasteboardItem`。

A`NSPasteboardItem`提供更細微的控制的資料寫入至剪貼版，適用於暫存的存取-它應該處置寫入剪貼版之後。

#### <a name="writing-data"></a>將資料寫入

若要將自訂資料寫入`NSPasteboardItem`您必須提供自訂`NSPasteboardItemDataProvider`。 將新類別加入至專案，並為它**ImageInfoDataProvider.cs**。 編輯檔案，並讓它看起來如下所示：

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

如同我們的自訂資料類別一樣，我們需要使用`Register`和`Export`指示詞，可將它公開至目標。 此類別必須繼承自`NSPasteboardItemDataProvider`，而且必須實作`FinishedWithDataProvider`和`ProvideDataForType`方法。

使用`ProvideDataForType`方法以提供的資料，都將包裝在`NSPasteboardItem`，如下所示：

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

在此情況下，我們會儲存兩個資訊片段 （名稱和 ImageType） 我們映像相關資訊，和寫入這些成簡單字串 (`public.text`)。

類型寫入資料至剪貼版中，使用下列程式碼：

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

若要從剪貼版的讀取資料，使用下列程式碼：

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

這篇文章已取得使用剪貼版在 Xamarin.Mac 應用程式中支援複製和貼上作業的詳細的檢視。 首先，它會引進，讓您熟悉標準 pasteboards 作業的簡單範例。 接下來，詳細的探討剪貼版，以及如何讀取和寫入資料，從它所需。 最後，它看使用自訂資料類型支援的複製和貼上的應用程式中的複雜資料型別。



## <a name="related-links"></a>相關連結

- [MacCopyPaste (sample)](https://developer.xamarin.com/samples/mac/MacCopyPaste/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [剪貼版程式設計指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
