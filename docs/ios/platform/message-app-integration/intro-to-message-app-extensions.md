---
title: Xamarin 中的訊息應用程式延伸模組基本概念
description: 本文說明如何在與訊息應用程式整合的 Xamarin iOS 解決方案中加入訊息應用程式延伸模組，並為使用者提供新功能。
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/02/2017
ms.openlocfilehash: 37f2942c97f7604fbd72a6dd38de518d3668ee9e
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250127"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>Xamarin 中的訊息應用程式延伸模組基本概念

_本文說明如何在與訊息應用程式整合的 Xamarin iOS 解決方案中加入訊息應用程式延伸模組，並為使用者提供新功能。_

IOS 10 的新功能，訊息應用程式延伸模組會與「**訊息**」應用程式整合，並為使用者提供新功能。 延伸模組可以傳送文字、貼紙、媒體檔案和互動式訊息。

## <a name="about-message-app-extensions"></a>關於訊息應用程式延伸模組

如上所述，訊息應用程式延伸模組會與**訊息**應用程式整合，並為使用者提供新功能。 延伸模組可以傳送文字、貼紙、媒體檔案和互動式訊息。 有兩種類型的訊息應用程式延伸模組可供使用：

- **不乾膠套件**-包含使用者可以新增至訊息的貼紙集合。 您不需要撰寫任何程式碼，即可建立貼紙套件。
- **IMessage 應用程式**-可以在 [訊息] 應用程式中顯示自訂使用者介面，以選取貼紙、輸入文字，包括媒體檔案（具有選擇性的類型轉換），以及建立、編輯和傳送互動訊息。

訊息應用程式延伸模組提供三種主要的內容類型：

- **互動式訊息**-這是應用程式所產生的一種自訂訊息內容，當使用者按下該訊息時，應用程式就會在前景中啟動。
- **貼紙**-這是應用程式所產生的映射，可以包含在使用者之間傳送的訊息中。
- **其他支援的內容**-應用程式可以將相片、影片、文字或連結等內容提供給訊息應用程式一律支援的任何其他內容類型。

IOS 10 的新功能，訊息應用程式現在包含自己專屬的內建 App Store。 包含訊息應用程式延伸模組的任何應用程式都會在此存放區中顯示並升級。 新的訊息應用程式下拉式清單會顯示已從 [訊息] 應用程式存放區下載的任何應用程式，以提供使用者快速存取。

Apple 也已新增 iOS 10 中的新功能，可讓使用者輕鬆探索應用程式。 例如，如果某個使用者從第二個使用者未安裝的應用程式（例如貼紙）傳送內容至另一個，則傳送應用程式的名稱會列在訊息歷程記錄中的內容底下。 如果使用者按下應用程式的名稱，則會開啟訊息應用程式存放區，並在存放區中選取應用程式。

訊息應用程式延伸模組與開發人員熟悉建立的現有 iOS 應用程式類似，而且可以存取標準 iOS 應用程式的所有標準架構和功能。 例如：

- 他們可以存取應用程式內購買。
- 他們可以存取 Apple Pay。
- 他們可以存取相機之類的裝置硬體。

只有 iOS 10 支援訊息應用程式延伸模組，但在 watchOS 和 macOS 裝置上可看到這些延伸模組所傳送的內容。 新增至 watchOS 3 的新 [_最近專案] 頁面_將會顯示最近從電話傳送的貼紙，包括來自「訊息應用程式」延伸模組的不乾膠，並允許使用者從「監看」傳送那些貼紙。

## <a name="about-the-messages-framework"></a>關於訊息架構

在 iOS 10 的新功能中，訊息架構會在使用者的 iOS 裝置上提供訊息應用程式延伸模組與訊息應用程式之間的介面。 當使用者從 [訊息] 應用程式內啟動應用程式時，此架構可讓您探索應用程式，並提供配置其 UI 所需的資料和內容。

啟動應用程式之後，使用者會與其互動，以建立要透過訊息共用的新內容。 應用程式接著會使用訊息架構，將新建立的內容傳送至訊息應用程式進行處理。

訊息架構和訊息應用程式延伸模組建置於預先存在的 iOS 應用程式擴充功能技術之上。 如需應用程式擴充功能的詳細資訊，請參閱 Apple 的[應用程式擴充程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

不同于 Apple 在整個系統中提供的其他擴充點，開發人員不需要為其訊息應用程式延伸模組提供主機應用程式，因為訊息應用程式本身會作為容器。 不過，開發人員可以選擇在新的或現有的 iOS 應用程式內包含訊息應用程式延伸模組，並連同配套一起傳送。

如果 iOS 應用程式的套件組合中包含訊息應用程式延伸模組，應用程式的圖示就會顯示在裝置的主畫面上，以及訊息應用程式中的訊息應用程式下拉式清單中。 如果未包含在應用程式套件組合中，訊息應用程式延伸模組只會顯示在訊息應用程式的下拉式清單中。

即使訊息應用程式延伸模組並未包含在主機應用程式套件組合中，開發人員也必須在訊息應用程式延伸模組的配套中提供應用程式圖示，因為這是將顯示在系統其他部分的圖示，例如 [訊息應用程式] 下拉式清單或設定，針對延伸模組。

## <a name="about-stickers"></a>關於貼紙

Apple 將貼紙作為 iMessage 使用者進行通訊的新方式，讓貼紙以內嵌形式傳送，以作為任何其他訊息內容，或附加至先前的訊息，並在交談內部進行反升。

什麼是貼紙？

- 這些是訊息應用程式延伸模組所提供的映射。
- 它們可以是動畫或靜態影像。
- 它們提供新的方式，可從應用程式內部共用影像內容。

有兩種方式可建立貼紙：

1. 您可以從 Xcode 內部建立貼紙套件訊息應用程式延伸模組，而不需要包含任何程式碼。 所有必要的都是貼紙和應用程式圖示的資產。
2. 藉由建立標準訊息應用程式延伸模組，透過訊息架構提供程式碼的貼紙。

### <a name="creating-sticker-packs"></a>建立貼紙套件

貼紙套件是從 Xcode 內的特殊範本建立，只是提供一組靜態的影像資產，可以用來做為貼紙。 如上所述，它們不需要任何程式碼，而開發人員只需將影像檔拖曳到貼紙資產類別目錄中的 [貼紙套件] 資料夾。

若要將影像包含在貼紙套件中，它必須符合下列需求：

- 影像必須是 PNG、APNG、GIF 或 JPEG 格式。 Apple 建議在提供貼紙資產時僅使用 PNG 和 APNG 格式。
- 動畫貼紙僅支援 APNG 和 GIF 格式。
- 貼紙影像應該提供透明背景，因為它們可以放在使用者交談中的訊息氣泡上。
- 個別的影像檔案必須小於500kb。
- 影像不能小於100x100 點，或大於 206 x 206 點。

> [!IMPORTANT]
> 應一律以 300 x 300 到 618 `@3x` x 618 圖元範圍的解析度提供貼紙影像。 系統會在執行時間自動`@2x`產生`@1x`和版本（如有需要）。

Apple 建議您針對各種不同的彩色背景（例如白色、黑色、紅色、黃色和多彩色）和相片來測試貼紙影像資產，以確保它們在所有可能的情況下都能發揮最大效果。

貼紙套件可以提供下列三種大小之一的貼紙：

- **小型**-100 x 100 點。
- **中**-136 x 136 點。 這是預設大小。
- **大型**-206 x 206 點。

使用 Xcode 的屬性偵測器來設定整個貼紙套件的大小，並只提供符合所要求大小的影像資產，以便在 [訊息] 應用程式內的 [貼紙瀏覽器] 中得到最佳結果。

如需詳細資訊，請參閱我們的[Ice Builder](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder)應用程式和 Apple 的[訊息參考](https://developer.apple.com/reference/messages)。

## <a name="creating-a-custom-sticker-experience"></a>建立自訂的不乾膠功能體驗

如果應用程式需要的控制或彈性比貼紙套件所提供的更多，它可以包含訊息應用程式延伸模組，並透過訊息架構提供貼紙，以供自訂的貼紙體驗使用。

建立自訂貼紙體驗有哪些優點？

1. 允許此應用程式自訂向應用程式使用者顯示貼紙的方式。 例如，表示以標準方格版面配置以外的格式或不同的彩色背景來呈現貼紙。
2. 允許從程式碼動態建立貼紙，而不是以靜態影像資產的形式包含。
3. 允許從開發人員的 web 伺服器動態下載貼紙影像資產，而不需要將新版本發行至 App Store。
4. 允許存取裝置的相機，以快速建立貼紙。
5. 允許應用程式內購買，讓使用者可以從應用程式內購買更多貼紙。

若要建立自訂的不乾膠功能體驗，請執行下列動作：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 啟動 Visual Studio for Mac。
2. 開啟解決方案，將訊息應用程式延伸模組新增至。
3. 選取 [ **iOS**  > **擴充** > 功能**iMessage 擴充**功能]，然後按 [**下一步]** 按鈕：

    [![](intro-to-message-app-extensions-images/message01.png "選取 iMessage 擴充功能")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. 輸入**擴充功能名稱**，然後按 [**下一步]** 按鈕：

    [![](intro-to-message-app-extensions-images/message02.png "輸入延伸模組名稱")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. 按一下 [**建立**] 按鈕以建立擴充功能：

    [![](intro-to-message-app-extensions-images/message03.png "按一下 [建立] 按鈕")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 啟動 Visual Studio。
2. 開啟方案以新增訊息應用程式延伸模組。
3. 選取 [ **Ios 擴充功能 > IMessage 延伸模組（ios）** ]，然後按 [**下一步]** 按鈕：

    [![選取 iMessage 延伸模組（iOS）](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. 輸入**名稱**，然後按一下 [**確定]** 按鈕

-----

根據預設， `MessagesViewController.cs`檔案會加入至方案。 這是延伸模組中的主要進入點，並繼承自`MSMessageAppViewController`類別。

Messages framework 提供的類別可向使用者呈現可用的貼紙：

- `MSStickerBrowserViewController`-控制要在其中呈現貼紙的視圖。 它也會符合`IMSStickerBrowserViewDataSource`介面，以傳回所指定瀏覽器索引的貼紙計數和貼紙。
- `MSStickerBrowserView`-這是可用的貼紙會顯示在其中的視圖。
- `MSStickerSize`-針對瀏覽器視圖中顯示的貼紙格線，決定個別的資料格大小。

### <a name="creating-a-custom-sticker-browser"></a>建立自訂的貼紙瀏覽器

開發人員可以在訊息應用程式延伸模組中提供自訂的貼紙瀏覽器`MSMessageAppBrowserViewController`（），以進一步自訂使用者的貼紙體驗。 自訂的貼紙瀏覽器會在選取要包含在訊息串流中的貼紙時，變更如何向使用者呈現貼紙。

請執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在  **Solution Pad**中，以滑鼠右鍵按一下擴充功能的專案名稱，**然後選取** > **新增檔案 ...**  >  **iOS |** AppleWatch > **介面控制器**。
2. 輸入`StickerBrowserViewController`作為**名稱**，然後按一下 [**新增**] 按鈕：

    [![](intro-to-message-app-extensions-images/browser01.png "在 [名稱] 中輸入 StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. `StickerBrowserViewController.cs`開啟檔案進行編輯。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管**中，以滑鼠右鍵按一下擴充功能的專案名稱，**然後選取** > **新增檔案 ...**  >  **iOS |** AppleWatch > **介面控制器**。
2. 輸入`StickerBrowserViewController`作為**名稱**，然後按一下 [**新增**] 按鈕：

    [![](intro-to-message-app-extensions-images/browser01.w157-sml.png "在 [名稱] 中輸入 StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
3. `StickerBrowserViewController.cs`開啟檔案進行編輯。

-----

`StickerBrowserViewController.cs`讓看起來如下所示：

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

如需詳細資訊，請參閱上述程式碼。 它會針對延伸模組所提供的貼紙建立存放裝置：

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

和會覆寫`MSStickerBrowserViewController`類別的兩個方法，以提供來自此資料存放區之瀏覽器的資料：

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

方法會從延伸模組的配套取得影像資產的路徑，並使用它從這個資產建立的新實例`MSSticker` ，並將其新增至集合： `CreateSticker`

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

會從`ViewDidLoad`呼叫方法，以從命名影像資產（包含在應用程式套件組合中）建立貼紙，並將其新增到貼紙的集合中。 `LoadSticker`

若要執行自訂的不乾膠式流覽`MessagesViewController.cs`器，請編輯檔案，使其看起來如下所示：

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

查看此程式碼的詳細資訊，它會建立自訂瀏覽器的儲存體：

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

而在`ViewDidLoad`方法中，它會具現化並設定新的瀏覽器：

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

然後，它會將瀏覽器新增至視圖，以顯示它：

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>進一步的貼紙自訂

藉由在訊息應用程式延伸模組中只包含兩個類別，即可進行進一步的不乾膠自訂：

- `MSStickerView`
- `MSSticker`

使用上述方法時，延伸模組可支援不依賴標準貼紙瀏覽器方法的不乾膠卷選。 此外，您可以在兩個不同的視圖模式之間切換貼紙顯示：

- **Compact** -這是預設模式，其中的貼紙視圖會佔用訊息視圖的底部 25%。
- 已**展開**-[貼紙] 視圖會填滿整個訊息視圖。

此貼紙視圖可以透過程式設計或手動方式在這些模式之間切換。

請參閱下列在兩個不同的視圖模式之間處理切換的範例。 每個狀態都需要兩個不同的視圖控制器。 會處理 Compact 視圖，看起來如下所示： `StickerBrowserViewController`

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

將會處理展開的貼紙視圖，並看起來如下所示： `AddStickerViewController`

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

`MessageViewController`會執行這些 View 控制器以驅動要求的狀態：

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

當使用者要求將新的貼紙新增至可用的集合時，會`AddStickerViewController`有一個新的 [顯示控制器]，而 [貼紙] 視圖會進入**展開**的視圖：

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

當使用者選擇要新增的貼紙時，會將它新增至其可用的集合，並要求**壓縮**視圖：

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
}
```

覆`DidTransition`寫方法以處理兩種模式之間的切換：

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

本文涵蓋了在與**訊息**應用程式整合的 Xamarin iOS 解決方案中包含訊息應用程式延伸模組，並為使用者提供新功能。 它涵蓋了使用延伸模組來傳送文字、貼紙、媒體檔案和互動式訊息。

## <a name="related-links"></a>相關連結

- [霜淇淋 Builder （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [訊息參考](https://developer.apple.com/reference/messages)
- [應用程式擴充程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
