---
title: "訊息應用程式擴充功能的基本概念"
description: "這篇文章顯示如何包含訊息的應用程式擴充功能中 Xamarin.iOS 方案與郵件應用程式整合，並對使用者顯示的新功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d9b6b5a778e0e4d5092d1036109f82896acf639b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="message-app-extension-basics"></a>訊息應用程式擴充功能的基本概念

_這篇文章顯示如何包含訊息的應用程式擴充功能中 Xamarin.iOS 方案與郵件應用程式整合，並對使用者顯示的新功能。_

新訊息應用程式擴充功能整合到 iOS 10、**訊息**應用程式並呈現給使用者的新功能。 擴充功能可以傳送文字、 貼紙、 媒體檔案和互動式的訊息。

## <a name="about-message-app-extensions"></a>訊息應用程式擴充功能

如前所述，訊息應用程式擴充功能與整合**訊息**應用程式並呈現給使用者的新功能。 擴充功能可以傳送文字、 貼紙、 媒體檔案和互動式的訊息。 有兩種類型的訊息應用程式擴充功能：

- **貼紙套件**-包含使用者可以新增至訊息的貼紙的集合。 可以建立不需要撰寫任何程式碼的貼紙的組件。
- **應用程式 iMessage** -可以呈現自訂使用者介面選取貼紙、 輸入文字，包括媒體檔案 （具有選擇性型別轉換） 以及建立、 編輯和傳送互動訊息的訊息應用程式內。

訊息應用程式擴充功能，提供三種主要的內容類型：

- **互動式訊息**-一種應用程式產生的自訂訊息內容，當使用者點選在訊息中，應用程式會啟動在前景。
- **貼紙**層可以包含在使用者之間傳送的訊息的應用程式所產生的映像。
- **支援的其他內容**-應用程式可以提供內容，例如相片、 影片、 文字或任何其他內容的連結類型一律已經支援訊息 」 應用程式。

新增 ios 10，訊息應用程式現在包含自己專用的內建的應用程式存放區。 將顯示任何包括訊息應用程式擴充功能的應用程式，並將其升級此存放區中。 新的郵件應用程式服務將會顯示已從來為使用者提供快速存取訊息應用程式市集下載任何應用程式。

也新增在 iOS 10、 Apple 已加入可讓使用者輕易地找到應用程式內嵌應用程式屬性。 比方說，如果一位使用者將內容傳送至另一個在第 2 個使用者不需要的應用程式安裝 （例如貼紙的範例），傳送應用程式名稱被列在訊息記錄中的內容。 如果在使用者點選應用程式的名稱，我們會開啟訊息應用程式存放區和存放區中選取的應用程式。

訊息應用程式擴充功能都與現有的 iOS 應用程式開發人員會熟悉如何建立，而且可以存取所有標準的架構和標準的 iOS 應用程式的功能。 例如: 

- 他們可以存取應用程式內購買。
- 他們擁有存取權 Apple Pay。
- 他們可以存取裝置的硬體，例如網路攝影機。

IOS 10 只支援訊息應用程式擴充功能，不過，這些擴充功能所傳送的內容在 watchOS 及 macOS 裝置上檢視。 新_最近頁面_加入 watchOS 3，會顯示最近使用的貼紙的電話，包括訊息應用程式延伸模組，從已傳送，並且允許使用者從監看式傳送這些貼紙。

## <a name="about-the-messages-framework"></a>有關訊息架構

新增到 iOS 10、 訊息架構，請提供使用者的 iOS 裝置上的訊息應用程式擴充功能和訊息應用程式之間的介面。 當使用者啟動應用程式從訊息應用程式內時，此架構可讓系統探索到的應用程式，並提供資料和配置其 UI 所需的內容。

一旦啟動應用程式時，使用者互動，以便建立訊息透過共用的新內容。 然後應用程式會將新建立的內容傳送到處理的訊息應用程式使用訊息架構。

在訊息架構和訊息應用程式擴充功能建置在預先存在的 iOS 應用程式擴充功能的技術之上。 如需有關應用程式擴充功能的詳細資訊，請參閱 Apple[應用程式擴充功能的程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

不同於其他 Apple 提供整個系統的擴充點，開發人員不必提供其訊息應用程式擴充功能的主機應用程式因為訊息應用程式本身做為容器。 不過，開發人員可以選擇包括新的或現有的 iOS 應用程式內訊息應用程式擴充功能，並傳送以及組合。

如果訊息的應用程式擴充功能隨附於 iOS 應用程式套件組合，裝置的 [首頁] 螢幕上和抽屜訊息應用程式從訊息應用程式內，將會顯示應用程式的圖示。 如果它不包含在應用程式套件組合，訊息應用程式擴充功能將只能顯示在訊息應用程式服務。

即使訊息應用程式擴充功能不在主機應用程式套件組合中，開發人員必須提供應用程式中的圖示的訊息應用程式擴充功能的組合，因為這是將在例如訊息應用程式抽屜或設定系統其他部分中顯示的圖示延伸模組。

## <a name="about-stickers"></a>關於貼紙

Apple 做為任何其他的訊息內容傳送內嵌 iMessage 使用者藉由允許貼紙進行通訊的新方式設計貼紙或它們可以附加到先前的訊息 （泡泡） 內的交談。

貼紙是什麼？

- 它們是訊息的應用程式擴充功能提供的映像。
- 它們可以是動態或靜態影像。
- 提供新的方式，來共用內部應用程式的映像內容。

有兩種方式建立貼紙：

1. 貼紙組件訊息的應用程式擴充功能可以從建立在 Xcode 內不包括任何程式碼。 只需要為貼紙] 和 [應用程式圖示的資產。
2. 藉由建立標準的訊息應用程式擴充功能，提供從透過訊息架構的程式碼貼紙。

### <a name="creating-sticker-packs"></a>建立的貼紙組件

貼紙的組件會從在 Xcode 內的特殊範本建立，而只是提供一組靜態的可用來當作貼紙的影像資產。 如前所述，它們不需要任何程式碼、 開發人員直接拖曳影像檔貼紙資產目錄內的貼紙套件資料夾。

要貼紙組件中包含影像，它必須符合下列需求：

- 影像必須是 PNG、 APNG、 GIF 或 JPEG 格式。 Apple 建議提供貼紙資產時，使用只有 PNG 和 APNG 格式。
- 動畫的貼紙只支援 APNG 和 GIF 格式。
- 因為透過交談中訊息 （泡泡） 由使用者貼紙映像應該提供透明背景。
- 個別的影像檔案必須是少於 500 kb。
- 映像不能小於 100 x 100 點或更大 206 x 206 指向。

> [!IMPORTANT]
> **注意：**貼紙映像應該一律在提供`@3x`300 x 300 到 618 x 618 像素的範圍中解析。 系統會自動產生`@2x`和`@1x`視需要執行階段版本。

Apple 建議測試以確保它們在所有可能的情況下尋找最佳的貼紙的影像資產針對各種不同的彩色的背景 （例如空白、 黑色、 紅色、 黃色和多重顏色） 和高於相片。

貼紙的組件可以提供其中一種可用的三種大小的貼紙：

- **小型**-100 x 100 點。
- **媒體**-136 x 136 點。 這是預設大小。
- **大型**-206 x 206 點。

您可以使用 Xcode 的屬性偵測器來設定整個貼紙組件的大小，並只提供符合要求的大小，為獲得最佳的結果訊息 」 應用程式內的貼紙瀏覽器中的影像資產。

如需詳細資訊，請參閱我們[冰淇淋產生器](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)應用程式與 Apple[訊息參考](https://developer.apple.com/reference/messages)。

## <a name="creating-a-custom-sticker-experience"></a>建立自訂的貼紙體驗

如果應用程式需要更多控制項或非貼紙組件所提供的彈性，它可以包含訊息的應用程式擴充功能，並提供自訂貼紙經驗透過訊息架構貼紙。

建立自訂的貼紙體驗的優點有哪些？

1. 允許應用程式自訂貼紙的應用程式使用者的顯示方式。 例如，若要使用格式非標準的格線版面配置中或在不同的彩色背景上存在的貼紙。
2. 可讓程式碼，而不是為靜態影像資產包含從動態建立的貼紙。
3. 允許動態地從開發人員的網頁伺服器下載，而不必發行的應用程式市集的新版本的貼紙影像資產。
4. 允許存取裝置的相機，建立貼紙上作業。
5. 讓使用者可以購買更多的貼紙從應用程式內，可讓應用程式內購買的。

若要建立自訂的貼紙體驗，請執行下列作業：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 啟動 Visual Studio for mac。
2. 開啟方案，以新增至訊息應用程式擴充功能。 
3. 選取**iOS** > **延伸** > **iMessage 延伸**按一下**下一步**按鈕： 

    [![](intro-to-message-app-extensions-images/message01.png "選取 iMessage 延伸模組")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. 輸入**副檔名**按一下**下一步**按鈕： 

    [![](intro-to-message-app-extensions-images/message02.png "輸入的擴充功能名稱")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. 按一下**建立**按鈕建置延伸模組： 

    [![](intro-to-message-app-extensions-images/message03.png "按一下 [建立] 按鈕")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 啟動 Visual Studio。
2. 開啟方案，以新增至訊息應用程式擴充功能。 
3. 選取**iOS** > **延伸** > **iMessage 延伸**按一下**下一步**按鈕： 

    [![](intro-to-message-app-extensions-images/message01w.png "選取 iMessage 延伸模組")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. 輸入**副檔名**按一下**確定**按鈕

-----

根據預設，`MessagesViewController.cs`檔案會加入至方案。 這是擴充功能的主要進入點，而且它繼承自`MSMessageAppViewController`類別。

訊息架構會提供向使用者呈現可用的貼紙的類別：

- `MSStickerBrowserViewController` -控制貼紙呈現的檢視。 它也符合`IMSStickerBrowserViewDataSource`介面，以傳回貼紙計數和特定瀏覽器索引的貼紙。
- `MSStickerBrowserView` -這是可用的貼紙將會顯示在檢視。
- `MSStickerSize` -決定瀏覽器檢視中顯示的貼紙的方格的個別資料格大小。

### <a name="creating-a-custom-sticker-browser"></a>建立自訂的貼紙瀏覽器

開發人員可以進一步自訂的貼紙經驗的使用者藉由提供自訂的貼紙瀏覽器 (`MSMessageAppBrowserViewController`) 中訊息的應用程式擴充功能。 自訂貼紙瀏覽器變更如何貼紙會向使用者顯示當選取要包含在訊息資料流中的貼紙。

請執行下列動作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在**方案板**，擴充功能的專案名稱上按一下滑鼠右鍵，然後選取**新增** > **新的檔案...**  >  **iOS |Apple Watch** > **介面控制器**。
2. 輸入`StickerBrowserViewController`如**名稱**按一下**新增**按鈕： 

    [![](intro-to-message-app-extensions-images/browser01.png "請輸入名稱 StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. 開啟`StickerBrowserViewController.cs`檔案進行編輯。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在**方案總管 中**，擴充功能的專案名稱上按一下滑鼠右鍵，然後選取**新增** > **新的檔案...**  >  **iOS |Apple Watch** > **介面控制器**。
2. 輸入`StickerBrowserViewController`如**名稱**按一下**新增**按鈕： 

    [![](intro-to-message-app-extensions-images/browser01w.png "請輸入名稱 StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. 開啟`StickerBrowserViewController.cs`檔案進行編輯。

-----

請`StickerBrowserViewController.cs`如下所示：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MonkeyStickers
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MSStickerSize stickerSize) : base (stickerSize)
        {
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            ...
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();
        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            return Stickers[(int)index];
        }
        #endregion
    }
}
```

查看上方的程式碼的詳細資料。 它會建立儲存體擴充功能提供貼紙：

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

並覆寫的兩個方法`MSStickerBrowserViewController`類別以提供資料給瀏覽器從這個資料存放區：

```csharp
public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
{
    return Stickers.Count;
}

public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
{
    return Stickers[(int)index];
}
```

`CreateSticker`方法取得從擴充功能的配套的影像資產的路徑，並使用它來建立的新執行個體`MSSticker`從這個資產，它將加入集合中：

```csharp
private void CreateSticker (string assetName, string localizedDescription)
{

    // Get path to asset
    var path = NSBundle.MainBundle.PathForResource (assetName, "png");
    if (path == null) {
        Console.WriteLine ("Couldn't create sticker {0}.", assetName);
        return;
    }

    // Build new sticker
    var stickerURL = new NSUrl (path);
    NSError error = null;
    var sticker = new MSSticker (stickerURL, localizedDescription, out error);
    if (error == null) {
        // Add to collection
        Stickers.Add (sticker);
    } else {
        // Report error
        Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
    }
}
```

`LoadSticker`方法從呼叫`ViewDidLoad`貼紙建立從具名的影像資產 （包含在應用程式套件組合），並將其加入至集合的貼紙。

若要實作自訂貼紙瀏覽器，編輯`MessagesViewController.cs`檔案，並讓它看起來如下所示：

```csharp
using System;
using UIKit;
using Messages;

namespace MonkeyStickers
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public StickerBrowserViewController BrowserViewController { get; set;}
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();


            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);
        }
        #endregion
    }
}
```

它會採用查看這段程式碼的詳細資料中，建立自訂瀏覽器的儲存體：

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

然後在`ViewDidLoad`方法，則會具現化，並設定新的瀏覽器：

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

然後它會將瀏覽器檢視來顯示它加入：

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>進一步的貼紙自訂

進一步的貼紙自訂，您可以在訊息的應用程式擴充功能包括兩個類別：

- `MSStickerView`
- `MSSticker`

使用上述方法，延伸模組可以支援貼紙的選取範圍，不會依賴標準的貼紙瀏覽器方法。 此外，貼紙顯示您可以切換不同的兩個不同的檢視模式：

- **Compact** -這是預設模式貼紙檢視會佔用下 25%的 [訊息] 檢視。
- **展開**-的貼紙檢視填滿整個 [訊息] 檢視。

這個貼紙檢視可以切換這些模式之間以程式設計方式或手動使用者。

看看下列範例會處理兩個不同的檢視模式之間切換。 兩個不同的檢視控制站就必須針對每個狀態。 `StickerBrowserViewController`控點**Compact**檢視和看起來像下面：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MessageExtension
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set; }
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MessagesViewController messagesAppViewController, MSStickerSize stickerSize) : base (stickerSize)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("add", "Add New Sticker");
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();

        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            // Wanting to add a new sticker?
            if (index == 0) {
                // Yes, ask controller to present add sticker interface
                MessagesAppViewController.AddNewSticker ();
                return null;
            } else {
                // No, return existing sticker
                return Stickers [(int)index];
            }
        }
        #endregion
    }
}
```

`AddStickerViewController`處理**加寬**貼紙檢視和外觀如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using Messages;

namespace MessageExtension
{
    public class AddStickerViewController : UIViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set;}
        public MSSticker NewSticker { get; set;}
        #endregion

        #region Constructors
        public AddStickerViewController (MessagesViewController messagesAppViewController)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Override Method
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Build interface to create new sticker
            var cancelButton = new UIButton (UIButtonType.RoundedRect);
            cancelButton.TouchDown += (sender, e) => {
                // Cancel add new sticker
                MessagesAppViewController.CancelAddNewSticker ();
            };
            View.AddSubview (cancelButton);

            var doneButton = new UIButton (UIButtonType.RoundedRect);
            doneButton.TouchDown += (sender, e) => {
                // Add new sticker to collection
                MessagesAppViewController.AddStickerToCollection (NewSticker);
            };
            View.AddSubview (doneButton);
            
            ...
        }
        #endregion
    }
}
```

`MessageViewController`實作這些檢視控制站，以磁碟機所要求的狀態：

```csharp
using System;
using UIKit;
using Messages;

namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public bool IsAddingSticker { get; set;}
        public StickerBrowserViewController BrowserViewController { get; set; }
        public AddStickerViewController AddStickerController { get; set;}
        #endregion

        #region Constructors
        protected MessagesViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Public Methods
        public void PresentStickerBrowser ()
        {
            // Is the Add sticker view being displayed?
            if (IsAddingSticker) {
                // Yes, remove it from view
                AddStickerController.RemoveFromParentViewController ();
                AddStickerController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);

            // Save mode
            IsAddingSticker = false;
        }

        public void PresentAddSticker ()
        {
            // Is the sticker browser being displayed?
            if (!IsAddingSticker) {
                // Yes, remove it from view
                BrowserViewController.RemoveFromParentViewController ();
                BrowserViewController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (AddStickerController);
            AddStickerController.DidMoveToParentViewController (this);
            View.AddSubview (AddStickerController.View);

            // Save mode
            IsAddingSticker = true;
        }

        public void AddNewSticker ()
        {
            // Switch to expanded view mode
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void CancelAddNewSticker ()
        {
            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }

        public void AddStickerToCollection (MSSticker sticker)
        {
            // Add sticker to collection
            BrowserViewController.Stickers.Add (sticker);

            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (this, MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Create new Add controller and configure it as well
            AddStickerController = new AddStickerViewController (this);
            AddStickerController.View.Frame = View.Frame;

            // Initially present the sticker browser
            PresentStickerBrowser ();
        }

        public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            base.DidTransition (presentationStyle);

            // Take action based on style
            switch (presentationStyle) {
            case MSMessagesAppPresentationStyle.Compact:
                PresentStickerBrowser ();
                break;
            case MSMessagesAppPresentationStyle.Expanded:
                PresentAddSticker ();
                break;
            }
        }
        #endregion
    }
}
```

當使用者要求至其可用的集合中，加入新的貼紙新`AddStickerViewController`可見的控制器和貼紙檢視由進入**加寬**檢視：

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

當使用者選擇新增貼紙時，將它加入至其可用的集合和**Compact**要求檢視：

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
}
```

`DidTransition`會覆寫方法以處理兩個模式之間切換：

```csharp
public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
{
    base.DidTransition (presentationStyle);

    // Take action based on style
    switch (presentationStyle) {
    case MSMessagesAppPresentationStyle.Compact:
        PresentStickerBrowser ();
        break;
    case MSMessagesAppPresentationStyle.Expanded:
        PresentAddSticker ();
        break;
    }
}
``` 

## <a name="summary"></a>總結

這篇文章已涵蓋包含訊息的應用程式擴充功能與整合的 Xamarin.iOS 方案**訊息**應用程式和呈現給使用者的新功能。 它涵蓋使用延伸模組來傳送文字、 貼紙、 媒體檔案和互動式的訊息。



## <a name="related-links"></a>相關連結

- [冰淇淋產生器 （範例）](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [訊息參考](https://developer.apple.com/reference/messages)
- [應用程式擴充功能的程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
