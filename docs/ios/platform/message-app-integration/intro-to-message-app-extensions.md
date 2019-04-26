---
title: 在 Xamarin.iOS 中的訊息應用程式擴充功能基本概念
description: 這篇文章示範如何包含訊息的應用程式擴充功能與在 Messages 應用程式整合，並向使用者呈現的新功能的 Xamarin.iOS 方案中。
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 7bd4a87843852e940da96f688371ddbecbf7e0b4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153827"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>在 Xamarin.iOS 中的訊息應用程式擴充功能基本概念

_這篇文章示範如何包含訊息的應用程式擴充功能與在 Messages 應用程式整合，並向使用者呈現的新功能的 Xamarin.iOS 方案中。_

新增至 iOS 10，訊息應用程式擴充功能則是與整合**訊息**給使用者的應用程式並為您介紹新功能。 延伸模組可以傳送文字、 貼紙、 媒體檔案和互動式訊息。

## <a name="about-message-app-extensions"></a>有關訊息的應用程式延伸模組

如上所述，訊息應用程式擴充功能與整合**訊息**給使用者的應用程式並為您介紹新功能。 延伸模組可以傳送文字、 貼紙、 媒體檔案和互動式訊息。 有兩種類型的訊息應用程式擴充功能：

- **貼紙套件**-包含使用者可以新增至訊息的貼紙的集合。 您可以建立貼紙的組件，而不需要撰寫任何程式碼。
- **iMessage 應用程式**-可以呈現自訂使用者介面中選取貼紙、 輸入文字，包括 （具有選擇性型別轉換） 的媒體檔案與建立、 編輯和互動傳送的訊息應用程式。

訊息應用程式擴充功能提供三種主要的內容類型：

- **互動式訊息**-是一種應用程式所產生的自訂訊息內容中，當使用者點選在訊息中，應用程式會在前景中啟動。
- **貼紙**-可以包含在使用者之間傳送的訊息應用程式所產生的映像。
- **其他支援的內容**-應用程式可以提供內容，例如相片、 影片、 文字或任何其他內容的連結類型一定已經在 Messages 應用程式支援。

新增至 iOS 10，訊息應用程式現在包含自己專用的內建的應用程式存放區。 將顯示包含訊息的應用程式擴充功能的任何應用程式，並將它升級這個存放區中。 新的 「 訊息 」 應用程式下拉式清單會顯示任何訊息的應用程式市集，以提供快速的存取權的使用者從已下載的應用程式。

也新增在 iOS 10 中，Apple 已新增可讓使用者輕鬆地找出應用程式內嵌應用程式屬性。 比方說，如果一位使用者將內容傳送至另一個從第 2 個使用者不需要的應用程式安裝 （例如貼紙的範例），傳送應用程式名稱被列在底下的訊息記錄中的內容。 如果在使用者點選應用程式的名稱，我們開啟訊息應用程式存放區並選取存放區中的應用程式。

訊息應用程式延伸模組是類似於現有的 iOS 應用程式開發人員熟悉如何建立，並將能存取所有標準的架構和標準的 iOS 應用程式的功能。 例如: 

- 他們可以存取應用程式內購買。
- 他們可以到 Apple Pay 的存取。
- 他們可以存取，例如相機的裝置硬體。

IOS 10 只支援訊息的應用程式擴充功能，不過，這些擴充功能所傳送的內容可在 watchOS 和 macOS 裝置上檢視。 新_最近項目頁面_加入 watchOS 3 中，會顯示最新的貼紙已傳送從電話，包括訊息應用程式擴充功能，並允許使用者從監看式傳送這些貼紙。

## <a name="about-the-messages-framework"></a>關於訊息架構

新增至 iOS 10、 訊息架構，請提供使用者的 iOS 裝置上的訊息應用程式擴充功能和訊息應用程式之間的介面。 當使用者啟動應用程式從 「 訊息 」 應用程式時，此架構可讓應用程式探索，並提供資料和配置其 UI 所需的內容。

一旦啟動應用程式，使用者會與它建立新的內容，透過訊息共用互動。 然後，應用程式會將新建立的內容傳送到處理的訊息應用程式使用訊息架構。

訊息架構和訊息應用程式擴充功能是建置在現有的 iOS 應用程式擴充功能的技術之上。 如需有關應用程式擴充功能的詳細資訊，請參閱 Apple[應用程式擴充功能程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

不同於其他 Apple 提供整個系統的擴充點，開發人員不需要提供其訊息的應用程式擴充功能的主機應用程式，因為訊息應用程式本身做為容器。 不過，開發人員有包括在新的或現有的 iOS 應用程式內訊息的應用程式延伸模組並將其傳送以及組合的選項。

如果 iOS 應用程式套件組合中包含訊息的應用程式延伸模組，則裝置主畫面上和在 Messages 應用程式從訊息 」 的應用程式抽屜中，將會顯示應用程式的圖示。 如果它不包含在應用程式套件組合中，訊息應用程式擴充功能將只能顯示在訊息的應用程式下拉式清單。

即使訊息應用程式延伸模組不會包含在主機應用程式套件組合中，開發人員必須提供訊息的應用程式擴充功能的配套中的應用程式圖示，這是將在例如訊息應用程式下拉式清單或設定系統的其他部分中顯示的圖示延伸模組。

## <a name="about-stickers"></a>關於貼紙

Apple 設計做為其他類型的訊息內容傳送內嵌新是 iMessage 使用者進行通訊，允許貼紙貼紙或它們可以附加至先前的訊息 （泡泡） 內的交談。

貼紙有哪些？

- 也就是訊息的應用程式擴充功能提供的映像。
- 它們可以是動態或靜態映像。
- 它們提供共用應用程式從映像內容的新方式。

有兩種方式可建立貼紙：

1. 貼紙組件訊息的應用程式擴充功能可以從建立在使用 Xcode 內不含任何程式碼。 只需要為貼紙與應用程式圖示的資產。
2. 藉由建立標準的訊息應用程式擴充功能可從程式碼，透過訊息架構的貼紙。

### <a name="creating-sticker-packs"></a>建立貼紙組件，

貼紙的組件在使用 Xcode 內的特殊範本建立，並只需提供一組靜態的可用來當做貼紙的影像資產。 如上所述，它們不需要任何程式碼，開發人員只需將映像檔拖曳到貼紙組件資料夾內貼紙資產目錄。

要包含在貼紙的組件中的影像，它必須符合下列需求：

- 影像必須是 PNG、 APNG、 GIF 或 JPEG 格式。 使用僅 PNG 和 APNG 格式提供貼紙資產時，Apple 建議。
- 動畫的貼紙僅支援 APNG 和 GIF 格式。
- 貼紙映像應該提供透明背景，因為可以將它們放在交談中訊息泡泡透過使用者。
- 個別的映像檔案必須是少於 500 kb。
- 映像不能小於 100 x 100 點，或更大 206 x 206 指向。

> [!IMPORTANT]
> 貼紙映像應該一律在提供`@3x`300 x 300 到 618 x 618 像素的範圍中解析。 系統會自動產生`@2x`和`@1x`在執行階段所需的版本。

Apple 建議測試針對各種不同的彩色的背景 （例如白色、 黑色、 紅色、 黃色和多彩色） 和透過相片，以確保它們在所有可能的情況下尋找最佳的貼紙影像資產。

貼紙的組件可以提供貼紙中其中三個可用的大小：

- **小型**-100 x 100 點。
- **中型**-第 136 x 第 136 點。 這是預設大小。
- **大型**-206 x 206 點。

您可以使用 Xcode 的屬性偵測器來設定整個貼紙套件的大小，而只提供符合所要求的大小，為獲得最佳結果，在 Messages 應用程式內貼紙瀏覽器中的影像資產。

如需詳細資訊，請參閱我們[Ice cream Builder](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)應用程式和 Apple[訊息參考](https://developer.apple.com/reference/messages)。

## <a name="creating-a-custom-sticker-experience"></a>建立自訂的貼紙體驗

如果應用程式需要更多控制或非貼紙的組件所提供的彈性，它可以包含訊息的應用程式擴充功能，並提供自訂貼紙體驗透過訊息 framework 貼紙。

建立自訂的貼紙體驗的優點有哪些？

1. 可讓應用程式，以自訂向應用程式的使用者顯示貼紙的方式。 例如，為了格式非標準的格線版面配置，或在不同的彩色背景上存在的貼紙。
2. 允許從程式碼，而不是作為靜態圖像資產以動態方式建立的貼紙。
3. 允許動態地從開發人員的 web 伺服器下載，而不需要發行至 App Store 的新版本的貼紙影像資產。
4. 若要建立貼紙上即時用來存取裝置的相機。
5. 因此，使用者可以購買更多的貼紙從應用程式，可讓應用程式內購買。

若要建立自訂的貼紙體驗，請執行下列作業：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 啟動 Visual Studio for mac。
2. 開啟方案，以新增至郵件應用程式擴充功能。 
3. 選取 [ **iOS** > **延伸模組** > **iMessage 延伸模組**然後按一下**下一步]** 按鈕： 

    [![](intro-to-message-app-extensions-images/message01.png "選取 iMessage 延伸模組")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. 請輸入**延伸模組名稱**然後按一下**下一步**按鈕： 

    [![](intro-to-message-app-extensions-images/message02.png "輸入擴充功能名稱")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. 按一下 **建立**建置擴充功能的按鈕： 

    [![](intro-to-message-app-extensions-images/message03.png "按一下 [建立] 按鈕")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 啟動 Visual Studio。
2. 開啟方案，以新增訊息的應用程式擴充功能。
3. 選取 * * iOS 延伸模組 > iMessage 延伸模組 (iOS) * *，按一下 [**下一步]** 按鈕：

    [![選取 iMessage 延伸模組 (iOS)](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. 請輸入**名稱**然後按一下**確定**按鈕

-----

根據預設，`MessagesViewController.cs`檔案會加入至方案。 這是延伸模組的主要進入點，並繼承自`MSMessageAppViewController`類別。

訊息架構會提供類別，可用的貼紙呈現給使用者：

- `MSStickerBrowserViewController` -控制貼紙將中看到的檢視。 它也符合`IMSStickerBrowserViewDataSource`介面，以傳回貼紙計數和瀏覽器索引貼紙。
- `MSStickerBrowserView` -這是可用的貼紙將會顯示在檢視。
- `MSStickerSize` -決定瀏覽器檢視中顯示的貼紙的方格的個別資料格大小。

### <a name="creating-a-custom-sticker-browser"></a>建立自訂的貼紙瀏覽器

開發人員可以進一步自訂使用者的貼紙體驗，藉由提供自訂的貼紙瀏覽器 (`MSMessageAppBrowserViewController`) 訊息的應用程式擴充功能中。 自訂貼紙瀏覽器變更貼紙的呈現方式向使用者當他們選取要包含在訊息資料流中的貼紙。

請執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在  **Solution Pad**，以滑鼠右鍵按一下擴充功能的專案名稱，然後選取**新增** > **新的檔案...**  >  **iOS |Apple Watch** > **介面控制器**。
2. 請輸入`StickerBrowserViewController`for**名稱**，按一下 **新增**按鈕： 

    [![](intro-to-message-app-extensions-images/browser01.png "輸入名稱 StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. 開啟`StickerBrowserViewController.cs`檔案進行編輯。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 [**方案總管] 中**、 擴充功能的專案名稱上按一下滑鼠右鍵，然後選取**新增** > **新的檔案...**  >  **iOS |Apple Watch** > **介面控制器**。
2. 請輸入`StickerBrowserViewController`for**名稱**，按一下 **新增**按鈕： 

    [![](intro-to-message-app-extensions-images/browser01.w157-sml.png "輸入名稱 StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
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

查看上述程式碼在 詳細資料。 它會建立儲存體的擴充功能會提供貼紙：

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

並覆寫的兩個方法`MSStickerBrowserViewController`類別以提供資料給這個資料存放區的瀏覽器：

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

`CreateSticker`方法取得擴充功能的配套中的影像資產的路徑，並使用它來建立的新執行個體`MSSticker`從這個資產，它將加入至集合：

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

`LoadSticker`方法從呼叫`ViewDidLoad`從具名的影像資產 （包含在應用程式的套件組合） 中建立貼紙，並將它新增至集合的貼紙。

若要實作自訂貼紙瀏覽器，編輯`MessagesViewController.cs`檔案，並使它看起來如下：

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

介紹這段程式碼詳細資料中，它會建立自訂的瀏覽器的儲存體：

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

然後在`ViewDidLoad`方法，它會具現化，並設定新的瀏覽器：

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

進一步的貼紙自訂方法，就是在訊息的應用程式擴充功能包括兩個類別：

- `MSStickerView`
- `MSSticker`

使用上述方法，延伸模組可支援不會依賴標準的貼紙瀏覽器方法的貼紙選取範圍。 此外，可以兩種不同的檢視模式之間切換貼紙顯示：

- **Compact** -這是預設模式貼紙檢視會佔用最下方的 25%的 [訊息] 檢視。
- **展開**-的貼紙檢視填滿整個 [訊息] 檢視。

此貼紙檢視可以這些模式之間以程式設計方式或以手動方式透過切換使用者。

看看下列範例中處理兩個不同的檢視模式之間切換。 兩個不同的檢視控制器都必須針對每個狀態。 `StickerBrowserViewController`控制代碼**Compact**檢視且看起來如下所示：

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

`AddStickerViewController`將處理**展開**貼紙檢視和外觀如下所示：

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

`MessageViewController`實作這些檢視控制器，以要求的狀態：

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

當使用者要求至其可用的集合中，加入新的貼紙新`AddStickerViewController`可見的控制器和貼紙檢視由進入**展開**檢視：

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

當使用者選擇新增貼紙時，將它加入至其可用的集合， **Compact**檢視要求：

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
}
```

`DidTransition`方法會覆寫來處理兩種模式之間切換：

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

這篇文章已涵蓋與整合的 Xamarin.iOS 方案中包含訊息的應用程式擴充功能**訊息**應用程式和使用者有新功能。 它涵蓋了使用擴充功能來傳送文字、 貼紙、 媒體檔案和互動式訊息。



## <a name="related-links"></a>相關連結

- [冰淇淋產生器 （範例）](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [訊息參考](https://developer.apple.com/reference/messages)
- [應用程式擴充功能的程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
