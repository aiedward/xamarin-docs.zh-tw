---
title: Xamarin 中的 Advanced Message 應用程式延伸模組
description: 本文說明在與訊息應用程式整合的 Xamarin iOS 解決方案中使用訊息應用程式延伸模組，並為使用者提供新功能的先進技術。
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 8a1f386209ccc1f2cb33348930f29bf5ac65ce4f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435624"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>Xamarin 中的 Advanced Message 應用程式延伸模組

_本文說明在與訊息應用程式整合的 Xamarin iOS 解決方案中使用訊息應用程式延伸模組，並為使用者提供新功能的先進技術。_

IOS 10 的新功能，訊息應用程式延伸模組會與 **訊息** 應用程式整合，並為使用者提供新功能。 延伸模組可以傳送文字、貼紙、媒體檔案和互動式訊息。

## <a name="about-message-app-extensions"></a>關於訊息應用程式延伸模組

如上所述，訊息應用程式延伸模組會與 **訊息** 應用程式整合，並向使用者呈現新功能。 延伸模組可以傳送文字、貼紙、媒體檔案和互動式訊息。 有兩種類型的訊息應用程式延伸模組可供使用：

- **貼紙套件** ：包含使用者可新增至訊息的貼紙集合。 您無須撰寫任何程式碼，即可建立不乾膠貼紙套件。
- **IMessage 應用程式** -可以在訊息應用程式內顯示自訂消費者介面，以選取貼紙、輸入文字，包括具有選擇性類型轉換 (的媒體檔案) 以及建立、編輯和傳送互動訊息。

訊息應用程式延伸模組提供三種主要內容類型：

- **互動式訊息** -這是應用程式所產生的一種自訂訊息內容，當使用者按下訊息時，就會在前景中啟動應用程式。
- **貼紙** ：是應用程式所產生的影像，可包含在使用者之間傳送的訊息中。 請參閱我們的 [霜淇淋 Builder](/samples/xamarin/ios-samples/ios10-icecreambuilder) 範例應用程式，以取得適用于不乾膠的不乾膠套件應用程式範例。
- **其他支援的內容** -應用程式可以提供內容，例如相片、影片、文字或訊息應用程式一律支援之類型的連結。

新的 iOS 10，訊息應用程式現在包含自己專屬的內建 App Store。 包含訊息應用程式延伸模組的任何應用程式都會顯示並在此存放區中升級。 新的訊息應用程式隱藏式選單會顯示已從訊息 App Store 下載的任何應用程式，以提供使用者快速存取權。

此外，Apple 在 iOS 10 中新增了內嵌應用程式屬性，可讓使用者輕鬆地探索應用程式。 例如，如果某位使用者從第2位使用者未安裝的應用程式將內容傳送至另一個使用者 (例如) 的貼紙，則傳送應用程式的名稱會列在訊息歷程記錄的內容底下。 如果使用者按下應用程式的名稱，則會開啟 App Store 的訊息，並在存放區中選取應用程式。

Message Apps 擴充功能與開發人員熟悉建立的現有 iOS 應用程式類似，而且他們將可存取標準 iOS 應用程式的所有標準架構和功能。 例如：

- 他們可以存取應用程式內購買。
- 他們有 Apple Pay 的存取權。
- 他們可以存取裝置硬體，例如相機。

只有 iOS 10 支援訊息應用程式延伸模組，不過，這些延伸模組所傳送的內容可在 watchOS 和 macOS 裝置上看到。 新增至 watchOS 3 的新 _最近專案頁面_ 將會顯示從電話傳送的最新貼紙，包括來自訊息應用程式延伸模組的貼紙，並允許使用者從 watch 傳送這些貼紙。

## <a name="about-interactive-messages"></a>關於互動式訊息

互動式訊息會顯示自訂訊息的反升，並由訊息應用程式延伸模組提供。 它們可讓使用者建立互動式訊息內容、將它插入訊息輸入欄位，然後傳送它。

[![建立互動式訊息內容](advanced-message-app-extensions-images/interactive01.png)](advanced-message-app-extensions-images/interactive01.png#lightbox)

接收使用者可以藉由在訊息歷程記錄中使用訊息反升來載入建立它的訊息應用程式延伸模組，來回複互動式訊息。 延伸模組將會以全螢幕啟動，並可讓使用者撰寫回復，並將其傳回給來源使用者。

[![延伸模組已啟動全螢幕](advanced-message-app-extensions-images/interactive02.png)](advanced-message-app-extensions-images/interactive02.png#lightbox)

以下將詳細說明下列主題：

- 訊息 API 總覽
- 擴充功能生命週期
- 撰寫訊息
- 傳送訊息

## <a name="messages-api-overview"></a>訊息 API 總覽

當使用者叫用時，訊息應用程式延伸模組會以精簡模式顯示在訊息歷程記錄底部：

[![訊息 API 總覽](advanced-message-app-extensions-images/interactive03.png)](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. `MSMessageAppViewController`訊息應用程式延伸模組中的物件是向使用者顯示延伸模組的視圖時，所呼叫的主要類別。
2. 交談會以物件實例的形式呈現給使用者 `MSConversation` 。
3. `MSMessage`類別代表交談中的指定訊息反升。
4. `MSSession` 控制傳送訊息的方式。
5. `MSMessageTemplateLayout` 控制訊息的顯示方式

## <a name="the-extension-lifecycle"></a>擴充功能生命週期

請看看訊息應用程式延伸模組變成作用中的流程：

[![訊息應用程式延伸模組變成作用中的進程](advanced-message-app-extensions-images/interactive04.png)](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. 啟動擴充功能時 (例如，從應用程式隱藏式選單) ，訊息應用程式將會啟動處理常式。
2. `DidBecomeActive`呼叫方法並傳遞 `MSConversation` ，代表訊息應用程式延伸模組執行所在的交談。
3. 因為此延伸模組是以 `UIViewController` 和兩者為基礎 `ViewWillAppear` ，所以 `ViewDidAppear` 會呼叫。

接下來，看看訊息應用程式延伸模組變成停用的進程：

[![訊息應用程式延伸模組變成停用的進程](advanced-message-app-extensions-images/interactive05.png)](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. 停用訊息應用程式延伸模組時， `ViewWillDisappear` 會先呼叫方法。
2. 然後 `ViewDidDisappear` 會呼叫此方法。
3. `WillResignActive`呼叫方法並傳遞 `MSConversation` ，代表訊息應用程式延伸模組執行所在的交談。 此時，訊息應用程式與延伸模組之間的連接即將發行。
4. 在稍後的時間點，訊息應用程式會終止進程。

由於延伸模組是短暫的程式，因此系統會積極地終止，以節省處理和電池電力。 開發人員在設計和執行訊息應用程式延伸模組時，應牢記這一點。

## <a name="composing-a-message"></a>撰寫訊息

當訊息應用程式延伸模組在進程中執行，並顯示其消費者介面之後，就可以使用下列程式碼來撰寫新的訊息：

```csharp
MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
{
    var components = new NSUrlComponents {
        QueryItems = iceCream.QueryItems
    };

    var layout = new MSMessageTemplateLayout {
        Image = iceCream.RenderSticker (true),
        Caption = caption
    };

    var message = new MSMessage (session ?? new MSSession()) {
        Url = components.Url,
        Layout = layout
    };

    return message;
}
```

此程式碼會建立新的 `MSMessage` ，並設定數個屬性 (例如 `Url`) 。 雖然訊息只能在 iOS 上建立，但可以同時傳送至 iOS 和 macOS。

如果使用者在 macOS 上按一下對話中的訊息反升，Mac 會嘗試在 web 瀏覽器中開啟 URL 中指定的位址。 如此一來，開發人員的網站應該能夠在 macOS 型電腦上的網頁瀏覽器中顯示訊息的某些標記法。

`AccessibilityLabel`螢幕讀取器會使用此屬性來讀取與使用者交談的文字記錄。 `Layout`屬性會指定訊息的顯示方式，目前只 `MSMessageTemplateLayout` 支援，而且看起來像下面這樣：

[![MSMessageTemplateLayout 範本](advanced-message-app-extensions-images/interactive06.png)](advanced-message-app-extensions-images/interactive06.png#lightbox)

的 `Image` 屬性會 `MSMessageTemplateLayout` 提供 MessageBubble 在螢幕上的主體內容。 `MediaFileUrl`屬性也會提供訊息反升主體的內容，但不支援 (的內容， `UIImage` 例如會在背景) 中迴圈的影片檔案。 如果同時 `Image` 提供和 `MediaFileUrl` 屬性，將 `Image` 會優先使用屬性。 `MediaFileUrl`支援任何格式的 PNG、JPEG、GIF 和影片 (可由媒體播放機架構播放) 媒體格式。

建議的媒體大小為3倍解析度的 300 x 300 圖元。 也接受稍微放大且較小的資產，且 Apple 會使用幾個不同的大小來建議測試，以取得最佳結果。 訊息應用程式會向下取樣，並視需要調整此媒體。

當資產傳送至接收者時，任何附加的媒體都會自動由訊息應用程式轉碼，以將其從網路傳送到優化。 基於這個原因，Apple 不會在附加至郵件的媒體中包含文字，因為媒體將會相應縮小並壓縮以進行傳輸，因此可能會轉譯文字模糊。

`ImageTitle`和 `ImageSubtitle` 屬性會提供訊息反升中所呈現之媒體的描述。 這些屬性會以文字的形式傳送至接收裝置，並在影像的左下角將 crisply 轉譯出來。

`Caption`、 `SubCaption` `TrailingCaption` 和 `TrailingSubcaption` 屬性會進一步描述影像，並在影像下方的區段中呈現。 將所有這些屬性設定為， `null` 將會在沒有標題區域的情況下建立訊息反升：

[![沒有標題區域的訊息反升圖](advanced-message-app-extensions-images/interactive07.png)](advanced-message-app-extensions-images/interactive07.png#lightbox)

最後要注意的一點是，「訊息」應用程式會在訊息反升的左上角繪製訊息應用程式延伸模組的圖示。

## <a name="sending-a-message"></a>傳送訊息

一旦撰寫好之後 `MSMessage` ，您就可以使用下列程式碼來傳送它：

```csharp
public void SendMessage (MSMessage message)
{
    // Insert the message into the conversation
    ActiveConversation.InsertMessage (message, (error) => {
        // Did the message send successfully?
        if (error == null) {
            // Handle successful send
        } else {
            // Report Error
            Console.WriteLine ("Error: {0}", error);
        }
    };

}
```

的 `ActiveConversation` 屬性 `MSMessagesAppViewController` 會保存訊息應用程式延伸模組在其中啟動的目前交談。

呼叫的， `InsertMessage` `MSConversation` 以將訊息包含在對話中，並處理可能發生的任何錯誤。 如果成功包含訊息，訊息反升將會顯示在輸入欄位中。

此外，此延伸模組可將不同類型的資料傳送到交談，例如：

- **文本** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **附件** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **貼紙**  -  `ActiveConversation.InsertSticker (sticker, (obj) => {...});`其中 `sticker` 是 `MSSticker` 。

當新內容位於輸入欄位之後，使用者就可以藉由按下藍色的 [ **傳送** ] 按鈕來傳送訊息 (就像一般的訊息) 一樣。 沒有任何方法可讓訊息應用程式延伸模組自動傳送內容，此程式完全受限於使用者的控制權。

## <a name="handling-the-compact-and-expanded-modes"></a>處理 Compact 和展開模式

訊息應用程式延伸模組可以用兩種不同的視圖模式來顯示：

[![顯示在兩個不同視圖模式中的訊息應用程式延伸模組： Compact & 展開](advanced-message-app-extensions-images/interactive08.png)](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -這是預設模式，訊息應用程式延伸模組會佔用訊息查看的底部25%。 在精簡模式中，應用程式沒有鍵盤、水準滾動或滑動手勢辨識器的存取權。 應用程式確實可以存取輸入欄位，而 `InsertMessage` 的呼叫會立即向使用者顯示。
- **展開** -訊息應用程式延伸模組會填滿整個訊息查看。 它無法存取輸入欄位，但可以存取鍵盤、水準滾動和滑動手勢辨識器。

您可以隨時以程式設計方式或手動方式，在這些模式之間切換訊息應用程式延伸模組，而且應該立即回應視圖模式中的任何變更。

請參閱下列在兩個不同視圖模式之間處理切換的範例。 每個狀態都需要兩個不同的 View 控制器。 `StickerBrowserViewController`會處理**Compact**視圖，而且 `AddStickerViewController` 會處理**展開**的視圖：

```csharp
using System;

using Messages;
using Foundation;
using UIKit;

namespace MessagesExtension {
    public partial class MessagesViewController : MSMessagesAppViewController, IIceCreamsViewControllerDelegate, IBuildIceCreamViewControllerDelegate {
        public MessagesViewController (IntPtr handle) : base (handle)
        {
        }

        public override void WillBecomeActive (MSConversation conversation)
        {
            base.WillBecomeActive (conversation);

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, PresentationStyle);
        }

        public override void WillTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected an active converstation");

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, presentationStyle);
        }

        void PresentViewController (MSConversation conversation, MSMessagesAppPresentationStyle presentationStyle)
        {
            // Determine the controller to present.
            UIViewController controller;

            if (presentationStyle == MSMessagesAppPresentationStyle.Compact) {
                // Show a list of previously created ice creams.
                controller = InstantiateIceCreamsController ();
            } else {
                var iceCream = new IceCream (conversation.SelectedMessage);
                controller = iceCream.IsComplete ? InstantiateCompletedIceCreamController (iceCream) : InstantiateBuildIceCreamController (iceCream);
            }

            foreach (var child in ChildViewControllers) {
                child.WillMoveToParentViewController (null);
                child.View.RemoveFromSuperview ();
                child.RemoveFromParentViewController ();
            }

            AddChildViewController (controller);
            controller.View.Frame = View.Bounds;
            controller.View.TranslatesAutoresizingMaskIntoConstraints = false;
            View.AddSubview (controller.View);

            controller.View.LeftAnchor.ConstraintEqualTo (View.LeftAnchor).Active = true;
            controller.View.RightAnchor.ConstraintEqualTo (View.RightAnchor).Active = true;
            controller.View.TopAnchor.ConstraintEqualTo (View.TopAnchor).Active = true;
            controller.View.BottomAnchor.ConstraintEqualTo (View.BottomAnchor).Active = true;

            controller.DidMoveToParentViewController (this);
        }

        UIViewController InstantiateIceCreamsController ()
        {
            // Instantiate a `IceCreamsViewController` and present it.
            var controller = Storyboard.InstantiateViewController (IceCreamsViewController.StoryboardIdentifier) as IceCreamsViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an IceCreamsViewController from the storyboard");

            controller.Builder = this;
            return controller;
        }

        UIViewController InstantiateBuildIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (BuildIceCreamViewController.StoryboardIdentifier) as BuildIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an BuildIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            controller.Builder = this;

            return controller;
        }

        public UIViewController InstantiateCompletedIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (CompletedIceCreamViewController.StoryboardIdentifier) as CompletedIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an CompletedIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            return controller;
        }

        public void DidSelectAdd (IceCreamsViewController controller)
        {
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void Build (BuildIceCreamViewController controller, IceCreamPart iceCreamPart)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected a conversation");

            var iceCream = controller.IceCream;
            if (iceCream == null)
                throw new Exception ("Expected the controller to be displaying an ice cream");

            string messageCaption = string.Empty;
            var b = iceCreamPart as Base;
            var s = iceCreamPart as Scoops;
            var t = iceCreamPart as Topping;

            if (b != null) {
                iceCream.Base = b;
                messageCaption = "Let's build an ice cream";
            } else if (s != null) {
                iceCream.Scoops = s;
                messageCaption = "I added some scoops";
            } else if (t != null) {
                iceCream.Topping = t;
                messageCaption = "Our finished ice cream";
            } else {
                throw new Exception ("Unexpected type of ice cream part selected.");
            }

            // Create a new message with the same session as any currently selected message.
            var message = ComposeMessage (iceCream, messageCaption, conversation.SelectedMessage?.Session);

            // Add the message to the conversation.
            conversation.InsertMessage (message, null);

            // If the ice cream is complete, save it in the history.
            if (iceCream.IsComplete) {
                var history = IceCreamHistory.Load ();
                history.Append (iceCream);
                history.Save ();
            }

            Dismiss ();
        }

        MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
        {
            var components = new NSUrlComponents {
                QueryItems = iceCream.QueryItems
            };

            var layout = new MSMessageTemplateLayout {
                Image = iceCream.RenderSticker (true),
                Caption = caption
            };

            var message = new MSMessage (session ?? new MSSession()) {
                Url = components.Url,
                Layout = layout
            };

            return message;
        }
    }
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

（選擇性）應用程式可能會 `WillTransition` 先使用方法來處理視圖模式變更，然後才將它呈現給使用者 (，如同上述) 的 Icecream Builder 範例中所做的。 如需詳細資訊，請參閱我們的 [進一步貼紙自訂](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md) 檔。

## <a name="replying-to-a-message"></a>回復訊息

在回復訊息時，訊息應用程式延伸模組將需要處理兩個案例：

[![處於非作用中和作用中模式的訊息應用程式延伸模組](advanced-message-app-extensions-images/interactive09.png)](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **延伸模組為非** 使用中-訊息記錄中有一個訊息應用程式延伸模組的訊息，使用者可利用這些訊息來啟用擴充功能，並繼續進行互動式對話。
- **延伸模組為** 作用中-使用者可以在訊息記錄中，按一下訊息應用程式延伸模組的訊息反升，以進入展開的視圖模式，並從其離開的位置繼續互動式進程。

### <a name="the-extension-is-inactive"></a>延伸模組為非使用中

當使用者在訊息文字記錄中或在訊息應用程式延伸模組處於非使用中狀態時，會發生下列進程：

[![處理非使用中的訊息反升](advanced-message-app-extensions-images/interactive10.png)](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. 使用者會點擊延伸的訊息反升。
2. 當擴充功能啟動時，訊息應用程式將會啟動處理常式。
3. `DidBecomeActive`呼叫方法並傳遞 `MSConversation` ，代表訊息應用程式延伸模組執行所在的交談。
4. 因為此延伸模組是以 `UIViewController` 和兩者為基礎 `ViewWillAppear` ，所以 `ViewDidAppear` 會呼叫。

當程式完成時，訊息應用程式延伸模組將會顯示在展開的視圖模式中。

### <a name="the-extension-is-active"></a>延伸模組為作用中

當使用者在訊息文字記錄中對訊息進行反升顯示，且訊息應用程式延伸模組為作用中時，就會發生下列進程：

[![處理活動訊息反升](advanced-message-app-extensions-images/interactive11.png)](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. 使用者會點擊延伸的訊息反升。
2. 因為訊息應用程式延伸模組已在使用中，所以 `WillTransition` 會呼叫的方法， `MSMessagesAppViewController` 以處理從 Compact 切換至展開的視圖模式。
3. 的 `DidSelectMessage` 方法 `MSMessagesAppViewController` 會呼叫並傳遞，且訊息的反升會 `MSMessage` `MSConversation` 屬於。
4. 的 `DidTransition` 方法 `MSMessagesAppViewController` 會呼叫，以處理從 Compact 切換至展開的視圖模式。

同樣地，當程式完成時，訊息應用程式延伸模組將會顯示在展開的視圖模式中。

### <a name="accessing-the-selected-message"></a>存取選取的訊息

無論是哪一種情況，當使用者按下屬於訊息應用程式延伸模組的訊息反升時，都必須使用的屬性來存取 `MSMessage` `SelectedMessage` `MSConversation` 。

例如：

```csharp
using System;
using Foundation;
using Messages;
using UIKit;

namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        ...

        #region Override Methods
        public override void DidSelectMessage (MSMessage message, MSConversation conversation)
        {
            base.DidSelectMessage (message, conversation);

            // Get selected message
            var selected = conversation.SelectedMessage;

            // Present the user interface to continue editing
            // the conversation
            ...
        }
        #endregion
    }
}
```

選取的訊息應該會顯示在訊息應用程式延伸模組的 UI 中，而且應該允許使用者撰寫回應。

## <a name="removing-partially-completed-messages"></a>移除部分完成的訊息

在交談的兩個使用者之間傳送互動式交談的不同步驟時，部分完成的訊息會反升至訊息文字記錄：

[![部分完成的訊息反升可能會雜亂訊息文字記錄](advanced-message-app-extensions-images/interactive12.png)](advanced-message-app-extensions-images/interactive12.png#lightbox)

相反地，訊息應用程式延伸模組應該將先前的訊息反升至訊息文字記錄中的簡潔批註：

[![在訊息文字記錄中折迭先前的訊息](advanced-message-app-extensions-images/interactive13.png)](advanced-message-app-extensions-images/interactive13.png#lightbox)

這是使用來折迭 `MSSession` 所有現有步驟的處理。 因此，可以 `DidSelectMessage` `MSMessagesAppViewController` 修改類別的方法，如下所示：

```csharp
public override void DidSelectMessage (MSMessage message, MSConversation conversation)
{
    base.DidSelectMessage (message, conversation);

    MSSession session;
    var summary = "";

    // Get selected message
    var selected = conversation.SelectedMessage;

    // Does the selected message already have a session?
    if (selected.Session == null) {
        // No, create a new session
        session = new MSSession ();
        summary = "Let's build an ice cream";
    } else {
        // Yes, use the existing session
        session = selected.Session;
        summary = "I added some scoops";
    }

    // Create an instance of the message being composed
    var existingMessage = new MSMessage (session);
    message.SummaryText = summary;
    ...

    // Present the user interface to continue editing
    // the conversation
    ...
}
```

如果選取的訊息已經結束，則 `MSSession` 會使用它，否則會建立新的 `MSSession` 。 的 `SummaryText` 屬性 `MSMessage` 是用來將標題新增至折迭的先前步驟。 如果 `SummaryText` 屬性設定為 `null` ，就會將對話中的先前步驟從對話文字記錄中完全移除。

## <a name="advanced-message-api-features"></a>Advanced Message API 功能

透過上述詳細說明的新訊息 API 的基本功能，接下來請檢查 Apple 已內建在架構中的一些更先進功能。

首先，類別中有幾個其他覆寫方法可 `MSMessagesAppViewController` 提供更深入的交談存取權：

- `DidStartSendingMessage` -當使用者按下 [傳送] 按鈕時呼叫。 這並不表示訊息實際上已傳送給收件者，只是傳送進程已啟動。
- `DidCancelSendingMessage` -當使用者按一下對話文字記錄中訊息反升的右上角的 [ *X* ] 按鈕時，就會發生這種情況。
- `DidReceiveMessage` -當訊息應用程式延伸模組為作用中時，會呼叫這個方法。從交談中的其中一個參與者收到新的訊息。

### <a name="group-conversations"></a>群組交談

當使用者參與群組交談 (與3個或更多個人) 時，就可以使用訊息應用程式延伸模組，而且必須在設計和執行訊息應用程式延伸模組時考慮這一點。

請看看下列與三個使用者群組對話中的互動：

[![與三位使用者進行群組交談的互動](advanced-message-app-extensions-images/interactive14.png)](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. 使用者1傳送一組互動式訊息，要求使用者2和使用者3選擇漢堡一起享用配料。
2. 使用者2選擇 tomatoes。
3. 使用者3選擇 pickles。
4. 使用者2和使用者3的選擇幾乎都是在同一時間抵達使用者1。 因此，使用者2的選擇會折迭到摘要行中，而且無法使用。 這種情況也可能已翻轉，並顯示使用者2的選擇，而且使用者3是折迭狀態。

無論是哪一種情況，都不會有這種行為，因為使用者1應該要能夠存取使用者2和使用者3的選擇。 為了處理這種情況，Apple 建議訊息應用程式延伸模組會將訊息狀態儲存在雲端中，並使用在 `MSMessage` 使用者) 之間傳送 (的 URL 屬性來存取此狀態。

當使用者傳送訊息時，會產生會話權杖，並以目前的訊息狀態推送至雲端。 當使用者在對話文字記錄中按下訊息時，會使用會話權杖從雲端取出目前的會話狀態。

### <a name="sender-identifiers"></a>寄件者識別碼

若要討論存取訊息寄件者的識別碼，請採用上面所提供的群組交談範例：

[![群組交談傳送識別碼](advanced-message-app-extensions-images/interactive15.png)](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. 同樣地，使用者1會傳送一組互動式訊息，要求使用者2和使用者3選擇漢堡一起享用配料。
2. 使用者3挑選 pickles。
3. 使用者3的選擇會回到使用者1，使用者2尚未回復。
4. 因為 Apple 很在意使用者隱私權，所以訊息應用程式延伸模組只會瞭解唯一識別碼 (作為 `NSUUID`) ，將每個參與者指派給交談。 在本機裝置上，只有目前使用者的識別碼是已知的。
5. `MSMessage`具有 `SenderIdentifier` 屬性，該屬性會符合延伸模組已知之參與者清單中的其中一個使用者。
6. 每個使用者裝置都有自己的參與者清單複本，而在這種情況下，只有本機使用者的識別碼是已知的。
7. 傳送訊息時，它的 `SenderIdentifier` 屬性也稱為本機使用者。

傳送者識別碼可透過下列方式使用：

- 藉由查看參與者清單，擴充功能可以取得對話中的使用者數目。
- 當擴充功能收到來自使用者的訊息時，它可以追蹤寄件者識別碼。 如果接收到另一個具有相同寄件者識別碼的訊息，則延伸模組會知道它來自相同的使用者。
- 可以用來協助識別交談中的特定使用者。

傳送者識別碼可以用在的任何文字欄位中，方法是在 `MSMessageTemplateLayout` 其前面加上貨幣符號 (`$`) 。 例如：

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

當訊息應用程式顯示具有這種格式的訊息反升時，它會以 `$uuid...` 傳送訊息之交談中參與者的連絡人名稱取代。

每個裝置上的寄件者識別碼都是唯一的，因此請再次查看上圖，請注意，使用者1的裝置和使用者3的裝置對於對話中的每個參與者都有不同的唯一寄件者識別碼。

傳送者識別碼的範圍是訊息應用程式延伸模組的安裝。 因此，如果使用者卸載並重新安裝訊息應用程式延伸模組，則新的安裝將會產生新的傳送者識別碼。

若要存取寄件者識別碼，擴充功能可以使用下列程式碼：

```csharp
public override void DidStartSendingMessage (MSMessage message, MSConversation conversation)
{
    base.DidStartSendingMessage (message, conversation);

    // Get the sender's participant ID
    var senderID = message.SenderParticipantIdentifier;

    // Get the local participant ID
    var localID = conversation.LocalParticipantIdentifier;

    // Get the remote participant IDs
    var remoteIDs = conversation.RemoteParticipantIdentifiers;
}
```

## <a name="supported-platforms"></a>支援的平台

訊息應用程式延伸模組所產生的互動式訊息將會在下列 Apple 平臺上傳遞：

- watchOS 3
- macOS Sierra
- iOS 10

在三個平臺中，只有 iOS 10 可讓使用者產生互動式訊息。 在 macOS Sierra 上，如果使用者按一下互動式訊息反升，則附加至的 URL `MSMessage` 會在 Safari 中開啟，而且應該會在該處顯示訊息的標記法。

在 watchOS 上，訊息應用程式可以將互動式訊息提交至附加的 iOS 裝置，讓使用者可以在其中撰寫回復。

如果在較舊的 Apple 平臺上收到互動式訊息，則新的訊息 API 支援 fallback：

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

它們會以回溯格式傳遞，以作為兩個不同的訊息：

- 其中一個會是範本配置所提供的影像。
- 另一個則是中提供的 URL `MSMessage` 。

## <a name="summary"></a>摘要

本文提供先進的技術，可在與 **訊息** 應用程式整合的 Xamarin iOS 解決方案中使用訊息應用程式延伸模組，並為使用者提供新功能。

## <a name="related-links"></a>相關連結

- [霜淇淋 Builder (範例) ](/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [訊息參考](https://developer.apple.com/reference/messages)
- [應用程式延伸模組程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)