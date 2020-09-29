---
title: Xamarin 中的訊息應用程式延伸模組基本概念
description: 本文說明如何在與訊息應用程式整合的 Xamarin iOS 解決方案中包含訊息應用程式延伸模組，並為使用者提供新功能。
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 0a2ca9fea8135e0188829b8651e9ec15303b933d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436513"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>Xamarin 中的訊息應用程式延伸模組基本概念

_本文說明如何在與訊息應用程式整合的 Xamarin iOS 解決方案中包含訊息應用程式延伸模組，並為使用者提供新功能。_

IOS 10 的新功能，訊息應用程式延伸模組會與 **訊息** 應用程式整合，並為使用者提供新功能。 延伸模組可以傳送文字、貼紙、媒體檔案和互動式訊息。

## <a name="about-message-app-extensions"></a>關於訊息應用程式延伸模組

如上所述，訊息應用程式延伸模組會與 **訊息** 應用程式整合，並向使用者呈現新功能。 延伸模組可以傳送文字、貼紙、媒體檔案和互動式訊息。 有兩種類型的訊息應用程式延伸模組可供使用：

- **貼紙套件** ：包含使用者可新增至訊息的貼紙集合。 您無須撰寫任何程式碼，即可建立不乾膠貼紙套件。
- **IMessage 應用程式** -可以在訊息應用程式內顯示自訂消費者介面，以選取貼紙、輸入文字，包括具有選擇性類型轉換 (的媒體檔案) 以及建立、編輯和傳送互動訊息。

訊息應用程式延伸模組提供三種主要內容類型：

- **互動式訊息** -這是應用程式所產生的一種自訂訊息內容，當使用者按下訊息時，就會在前景中啟動應用程式。
- **貼紙** ：是應用程式所產生的影像，可包含在使用者之間傳送的訊息中。
- **其他支援的內容** -應用程式可以提供內容，例如相片、影片、文字或訊息應用程式一律支援的任何其他內容類型的連結。

新的 iOS 10，訊息應用程式現在包含自己專屬的內建 App Store。 包含訊息應用程式延伸模組的任何應用程式都會顯示並在此存放區中升級。 新的訊息應用程式隱藏式選單會顯示已從訊息 App Store 下載的任何應用程式，以提供使用者快速存取權。

此外，Apple 在 iOS 10 中新增了內嵌應用程式屬性，可讓使用者輕鬆地探索應用程式。 例如，如果某位使用者從第2位使用者未安裝的應用程式將內容傳送至另一個使用者 (例如) 的貼紙，則傳送應用程式的名稱會列在訊息歷程記錄的內容底下。 如果使用者按下應用程式的名稱，則會開啟 App Store 的訊息，並在存放區中選取應用程式。

訊息應用程式延伸模組類似于開發人員熟悉建立的現有 iOS 應用程式，並可存取標準 iOS 應用程式的所有標準架構和功能。 例如：

- 他們可以存取應用程式內購買。
- 他們有 Apple Pay 的存取權。
- 他們可以存取裝置硬體，例如相機。

只有 iOS 10 支援訊息應用程式延伸模組，不過，這些延伸模組所傳送的內容可在 watchOS 和 macOS 裝置上看到。 新增至 watchOS 3 的新 _最近專案頁面_ 將會顯示從電話傳送的最新貼紙，包括來自訊息應用程式延伸模組的貼紙，並允許使用者從 watch 傳送這些貼紙。

## <a name="about-the-messages-framework"></a>關於訊息架構

Message framework 是 iOS 10 的新功能，可在使用者的 iOS 裝置上的訊息應用程式延伸模組與訊息應用程式之間提供介面。 當使用者從訊息應用程式中啟動應用程式時，此架構可讓您探索應用程式，並提供配置其 UI 所需的資料和內容。

應用程式啟動之後，使用者會與其互動以建立新的內容，以透過訊息共用。 然後，應用程式會使用訊息架構，將新建立的內容傳送至訊息應用程式進行處理。

訊息架構和訊息應用程式延伸模組是以現有的 iOS 應用程式擴充技術為基礎。 如需應用程式擴充功能的詳細資訊，請參閱 Apple 的 [應用程式延伸程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

與 Apple 在整個系統中提供的其他擴充點不同，開發人員不需要為其訊息應用程式延伸模組提供主機應用程式，因為訊息應用程式本身是作為容器。 不過，開發人員可以選擇在新的或現有的 iOS 應用程式內包含訊息應用程式延伸模組，並隨配套傳送。

如果訊息應用程式延伸模組包含在 iOS 應用程式套件組合中，應用程式的圖示將會顯示在裝置的主畫面上，以及從訊息應用程式內的訊息應用程式中。 如果未包含在應用程式套件組合中，訊息應用程式延伸模組只會顯示在訊息應用程式的隱藏式選單中。

即使訊息應用程式延伸模組未包含在主機應用程式套件組合中，開發人員仍必須在訊息應用程式延伸模組的配套中提供應用程式圖示，因為這是將顯示在系統其他部分的圖示，例如訊息應用程式的隱藏式選單或設定。

## <a name="about-stickers"></a>關於貼紙

Apple 設計貼紙作為 iMessage 使用者進行通訊的新方式，可讓貼文以內嵌方式傳送為任何其他郵件內容，或者可以附加到交談內部的先前訊息。

什麼是貼紙？

- 它們是訊息應用程式延伸模組所提供的影像。
- 它們可以是動畫或靜態影像。
- 它們提供從應用程式內部共用影像內容的新方式。

有兩種方式可以建立貼紙：

1. 您可以從 Xcode 內部建立貼紙套件訊息應用程式延伸模組，而不需要包含任何程式碼。 所有需要的是貼紙和應用程式圖示的資產。
2. 藉由建立標準訊息應用程式延伸模組，透過訊息架構從程式碼提供貼紙。

### <a name="creating-sticker-packs"></a>建立貼紙套件

從 Xcode 內的特殊範本建立的貼紙套件，只需提供一組靜態映射資產，即可用來作為貼紙。 如上所述，它們不需要任何程式碼，而是開發人員只需要將影像檔拖曳到貼紙資產目錄內的 [貼紙套件] 資料夾中。

對於要包含在貼紙套件中的影像，它必須符合下列需求：

- 影像必須是 PNG、APNG、GIF 或 JPEG 格式。 Apple 建議在提供貼紙資產時使用 PNG 和 APNG 格式。
- 動畫貼紙只支援 APNG 和 GIF 格式。
- 貼紙影像應提供透明背景，因為它們可以放在使用者交談的訊息中。
- 個別影像檔案必須小於500kb。
- 映射不能小於100x100 點，或較大的 206 x 206 點。

> [!IMPORTANT]
> 您應一律以 `@3x` 300 x 300 到 618 x 618 圖元範圍的解析度提供貼紙影像。 系統會 `@2x` `@1x` 視需要在執行時間自動產生和版本。

Apple 建議您針對各種不同的彩色背景 (（例如白色、黑色、紅色、黃色、彩色) 和相片）來測試貼紙影像資產，以確保它們在所有可能的情況下都能發揮最大效果。

貼紙套件可以提供三種可用大小的其中一種：

- **Small** -100 x 100 點。
- **中** -136 x 136 點。 這是預設大小。
- **大型** -206 x 206 點。

使用 Xcode 的 [屬性] 偵測器來設定整個貼紙套件的大小，並只提供符合所要求大小的影像資產，以取得在 [訊息] 應用程式內的不乾膠貼紙瀏覽器中的最佳結果。

如需詳細資訊，請參閱我們的 [霜淇淋 Builder](/samples/xamarin/ios-samples/ios10-icecreambuilder) 應用程式和 Apple 的 [訊息參考](https://developer.apple.com/reference/messages)。

## <a name="creating-a-custom-sticker-experience"></a>建立自訂的貼紙體驗

如果應用程式需要更多的控制或彈性，而不是由貼紙套件提供，則可以包含訊息應用程式延伸模組，並透過訊息架構提供貼紙，以提供自訂的貼紙體驗。

建立自訂貼紙體驗有哪些優點？

1. 允許應用程式自訂將貼紙顯示給應用程式使用者的方式。 例如，若要以標準方格配置以外的格式來呈現貼紙，或以不同的彩色背景呈現。
2. 允許從程式碼動態建立貼紙，而不是以靜態映射資產的形式來提供。
3. 允許從開發人員的 web 伺服器動態下載不乾膠影像資產，而不需要將新版本發行至 App Store。
4. 允許存取裝置的相機，以即時建立貼紙。
5. 允許應用程式內購買，讓使用者可以從應用程式內購買更多貼紙。

若要建立自訂的貼紙體驗，請執行下列動作：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 開始 Visual Studio for Mac。
2. 開啟方案，將訊息應用程式延伸模組新增至。
3. 選取 [ **iOS**  >  **延伸**模組  >  **iMessage 擴充**功能，然後按 [**下一步]** 按鈕：

    [![選取 iMessage 延伸模組](intro-to-message-app-extensions-images/message01.png)](intro-to-message-app-extensions-images/message01.png#lightbox)
4. 輸入 **延伸模組名稱** ，然後按 [ **下一步]** 按鈕：

    [![輸入延伸模組名稱](intro-to-message-app-extensions-images/message02.png)](intro-to-message-app-extensions-images/message02.png#lightbox)
5. 按一下 [ **建立** ] 按鈕以建立延伸模組：

    [![按一下 [建立] 按鈕](intro-to-message-app-extensions-images/message03.png)](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 啟動 Visual Studio。
2. 開啟方案以新增訊息應用程式延伸模組。
3. 選取 ios **延伸模組 > IMessage 延伸模組 (ios) ** 然後按 [ **下一步]** 按鈕：

    [![選取 (iOS) 的 iMessage 延伸模組 ](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. 輸入 **名稱** ，然後按一下 [ **確定]** 按鈕

-----

依預設，會將檔案 `MessagesViewController.cs` 新增至方案。 這是延伸模組中的主要進入點，並繼承自 `MSMessageAppViewController` 類別。

Messages framework 提供用來向使用者呈現可用貼紙的類別：

- `MSStickerBrowserViewController` -控制將呈現貼紙的視圖。 它也會符合 `IMSStickerBrowserViewDataSource` 介面，以傳回所指定瀏覽器索引的貼紙計數和貼紙。
- `MSStickerBrowserView` -這是可用的貼紙將顯示在其中的視圖。
- `MSStickerSize` -決定瀏覽器視圖中顯示的貼紙方格的個別資料格大小。

### <a name="creating-a-custom-sticker-browser"></a>建立自訂的貼紙瀏覽器

開發人員可以 `MSMessageAppBrowserViewController` 在訊息應用程式延伸中提供自訂的不乾膠 () ，以進一步自訂使用者的貼紙體驗。 自訂的貼紙瀏覽器會在選取要包含在訊息串流中的貼紙時，變更將貼紙呈現給使用者的方式。

執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 [ **Solution Pad**中，以滑鼠右鍵按一下擴充功能的專案名稱，然後選取 [**加入**  >  **新**  >  檔案]。**iOS |Apple Watch**  >  **介面控制器**。
2. 輸入 `StickerBrowserViewController` **名稱** ，然後按一下 [ **新增** ] 按鈕：

    [![輸入 StickerBrowserViewController 作為名稱](intro-to-message-app-extensions-images/browser01.png)](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. 開啟 `StickerBrowserViewController.cs` 檔案進行編輯。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 [**方案總管**中，以滑鼠右鍵按一下擴充功能的專案名稱，然後選取 [**加入**  >  **新**  >  檔案]。**iOS |Apple Watch**  >  **介面控制器**。
2. 輸入 `StickerBrowserViewController` **名稱** ，然後按一下 [ **新增** ] 按鈕：

    [![輸入 StickerBrowserViewController 作為名稱](intro-to-message-app-extensions-images/browser01.w157-sml.png)](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
3. 開啟 `StickerBrowserViewController.cs` 檔案進行編輯。

-----

讓它 `StickerBrowserViewController.cs` 看起來像下面這樣：

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

請詳細查看上述程式碼。 它會針對延伸模組所提供的貼紙建立儲存體：

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

和會覆寫類別的兩個方法 `MSStickerBrowserViewController` ，以從這個資料存放區提供瀏覽器的資料：

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

`CreateSticker`方法會從擴充功能的組合中取得影像資產的路徑，並使用它從此資產建立的新實例 `MSSticker` ，並將其新增至集合：

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

`LoadSticker`從呼叫方法時，會從 `ViewDidLoad` 應用程式套件組合) 包含的命名映射資產 (建立貼紙，並將其新增到貼紙的集合中。

若要執行自訂不乾膠的貼上，請編輯檔案， `MessagesViewController.cs` 使其看起來如下所示：

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

仔細看一下此程式碼，它會建立自訂瀏覽器的儲存體：

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

在方法中 `ViewDidLoad` ，它會具現化並設定新的瀏覽器：

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

然後，它會將瀏覽器新增至視圖以顯示它：

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>進一步的貼紙自訂

您可以只在訊息應用程式延伸中包含兩個類別，藉此進一步自訂貼紙：

- `MSStickerView`
- `MSSticker`

使用上述方法時，擴充功能可以支援不依賴標準不乾膠的貼紙瀏覽器方法的貼上選取專案。 此外，您可以在兩種不同的視圖模式之間切換不乾膠顯示器：

- **Compact** -這是預設模式，其中的貼紙視圖會佔用訊息查看的底部25%。
- **展開** -[不乾膠] 視圖會填滿整個訊息查看。

您可以透過程式設計方式或手動方式，在這些模式之間切換此貼紙視圖。

請參閱下列在兩個不同視圖模式之間處理切換的範例。 每個狀態都需要兩個不同的 View 控制器。 會 `StickerBrowserViewController` 處理 **Compact** 視圖，看起來如下所示：

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

`AddStickerViewController`將會處理**擴充**的貼紙視圖，如下所示：

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

會 `MessageViewController` 執行這些視圖控制器來推動要求的狀態：

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

當使用者要求將新的貼紙新增至其可用的集合時，會將新的貼上 `AddStickerViewController` 成為可見的控制器，而 [不乾膠] 視圖會進入 **展開** 的視圖：

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

當使用者選擇要加入的貼紙時，會將其新增至其可用的集合，並要求 **Compact** view：

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
}
```

覆 `DidTransition` 寫方法以處理兩個模式之間的切換：

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

## <a name="summary"></a>摘要

本文涵蓋的檔應用程式延伸模組包含在與 **訊息** 應用程式整合的 Xamarin iOS 解決方案中，並向使用者呈現新功能。 它涵蓋了使用延伸模組來傳送文字、貼紙、媒體檔案和互動式訊息。

## <a name="related-links"></a>相關連結

- [霜淇淋 Builder (範例) ](/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [訊息參考](https://developer.apple.com/reference/messages)
- [應用程式延伸模組程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)