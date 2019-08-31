---
title: Xamarin 中的 Advanced Message 應用程式延伸模組
description: 本文說明在與訊息應用程式整合的 Xamarin iOS 解決方案中, 使用訊息應用程式延伸模組的先進技術, 並為使用者提供新功能。
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: e55e6d908bbeb9b4b42ccbcad8121a1b410b79af
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200140"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>Xamarin 中的 Advanced Message 應用程式延伸模組

_本文說明在與訊息應用程式整合的 Xamarin iOS 解決方案中, 使用訊息應用程式延伸模組的先進技術, 並為使用者提供新功能。_


IOS 10 的新功能, 訊息應用程式延伸模組會與「**訊息**」應用程式整合, 並為使用者提供新功能。 延伸模組可以傳送文字、貼紙、媒體檔案和互動式訊息。

## <a name="about-message-app-extensions"></a>關於訊息應用程式延伸模組

如上所述, 訊息應用程式延伸模組會與**訊息**應用程式整合, 並為使用者提供新功能。 延伸模組可以傳送文字、貼紙、媒體檔案和互動式訊息。 有兩種類型的訊息應用程式延伸模組可供使用:

- **不乾膠套件**-包含使用者可以新增至訊息的貼紙集合。 您不需要撰寫任何程式碼, 即可建立貼紙套件。
- **IMessage 應用程式**-可以在 [訊息] 應用程式中顯示自訂使用者介面, 以選取貼紙、輸入文字, 包括媒體檔案 (具有選擇性的類型轉換), 以及建立、編輯和傳送互動訊息。

訊息應用程式延伸模組提供三種主要的內容類型:

- **互動式訊息**-這是應用程式所產生的一種自訂訊息內容, 當使用者按下該訊息時, 應用程式就會在前景中啟動。
- **貼紙**-這是應用程式所產生的映射, 可以包含在使用者之間傳送的訊息中。 請參閱我們的[霜淇淋 Builder](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder)範例應用程式, 以取得適用于貼紙套件應用程式的範例。
- **其他支援的內容**-應用程式可以提供內容, 例如「訊息」應用程式一律支援的相片、影片、文字或連結類型。

IOS 10 的新功能, 訊息應用程式現在包含自己專屬的內建 App Store。 包含訊息應用程式延伸模組的任何應用程式都會在此存放區中顯示並升級。 新的訊息應用程式下拉式清單會顯示已從 [訊息] 應用程式存放區下載的任何應用程式, 以提供使用者快速存取。

Apple 也已新增 iOS 10 中的新功能, 可讓使用者輕鬆探索應用程式。 例如, 如果某個使用者從第二個使用者未安裝的應用程式 (例如貼紙) 傳送內容至另一個, 則傳送應用程式的名稱會列在訊息歷程記錄中的內容底下。 如果使用者按下應用程式的名稱, 則會開啟訊息應用程式存放區, 並在存放區中選取應用程式。

訊息應用程式延伸模組與開發人員熟悉建立的現有 iOS 應用程式類似, 而且可以存取標準 iOS 應用程式的所有標準架構和功能。 例如：

- 他們可以存取應用程式內購買。
- 他們可以存取 Apple Pay。
- 他們可以存取相機之類的裝置硬體。

只有 iOS 10 支援訊息應用程式延伸模組, 但在 watchOS 和 macOS 裝置上可看到這些延伸模組所傳送的內容。 新增至 watchOS 3 的新 [_最近專案] 頁面_將會顯示最近從電話傳送的貼紙, 包括來自「訊息應用程式」延伸模組的不乾膠, 並允許使用者從「監看」傳送那些貼紙。

## <a name="about-interactive-messages"></a>關於互動式訊息

互動式訊息會呈現自訂訊息的反升, 並由訊息應用程式延伸模組提供。 他們可讓使用者建立互動式郵件內容、將它插入訊息輸入欄位, 然後傳送它。

[![](advanced-message-app-extensions-images/interactive01.png "建立互動式訊息內容")](advanced-message-app-extensions-images/interactive01.png#lightbox)

接收使用者可以藉由在訊息歷程記錄中的訊息反升來載入建立它的訊息應用程式延伸模組, 來回複互動式訊息。 延伸模組將會以全螢幕的方式啟動, 並可讓使用者撰寫回復, 並將其傳回給原始使用者。

[![](advanced-message-app-extensions-images/interactive02.png "延伸模組已啟動全螢幕")](advanced-message-app-extensions-images/interactive02.png#lightbox)


下列主題將詳細說明如下:

- 訊息 API 總覽
- 延伸模組生命週期
- 撰寫訊息
- 傳送訊息

## <a name="messages-api-overview"></a>訊息 API 總覽

當使用者叫用時, 訊息應用程式延伸模組會顯示在 compact view 模式的訊息歷程記錄底部:

[![](advanced-message-app-extensions-images/interactive03.png "訊息 API 總覽")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. 訊息`MSMessageAppViewController`應用程式延伸模組中的物件是在對使用者顯示延伸模組的視圖時, 所呼叫的主要類別。
2. 交談會以`MSConversation`物件實例的形式呈現給使用者。
3. `MSMessage`類別代表交談中的指定訊息氣泡。
4. `MSSession`控制訊息的傳送方式。
5. `MSMessageTemplateLayout`控制訊息的顯示方式

## <a name="the-extension-lifecycle"></a>延伸模組生命週期

查看訊息應用程式延伸模組變成作用中的流程:

[![](advanced-message-app-extensions-images/interactive04.png "訊息應用程式延伸模組變成作用中的進程")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. 當擴充功能啟動時 (例如, 從應用程式選單), 訊息應用程式將會啟動進程。
2. 會呼叫`MSConversation`方法並傳遞, 代表訊息應用程式延伸模組執行所在的交談。 `DidBecomeActive`
3. 因為延伸模組是以`UIViewController` `ViewWillAppear`為基礎, 而且`ViewDidAppear`會呼叫。

接下來, 請查看訊息應用程式延伸模組已停用的流程:

[![](advanced-message-app-extensions-images/interactive05.png "訊息應用程式延伸模組已停用的進程")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. 當訊息應用程式延伸模組停用時, `ViewWillDisappear`會先呼叫方法。
2. 然後會呼叫方法。 `ViewDidDisappear`
3. 會呼叫`MSConversation`方法並傳遞, 代表訊息應用程式延伸模組執行所在的交談。 `WillResignActive` 此時, 訊息應用程式與延伸模組之間的連接即將發行。
4. 在稍後的時間點, 進程會由「訊息」應用程式終止。

由於延伸模組是短期的程式, 系統會積極地終止它來節省處理和電池電源。 開發人員在設計和執行訊息應用程式延伸模組時, 應該記住這一點。

## <a name="composing-a-message"></a>撰寫訊息

一旦訊息應用程式延伸模組在進程中執行, 並顯示其使用者介面, 就可以使用下列程式碼來撰寫新的訊息:

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

此程式碼會建立`MSMessage`新的, 並設定數個`Url`屬性 (例如)。 雖然訊息只能在 iOS 上建立, 但它可以傳送至 iOS 和 macOS 以顯示。

如果使用者在 macOS 上按一下交談中的訊息反升, Mac 會嘗試在網頁瀏覽器中開啟 URL 中所指定的位址。 因此, 開發人員的網站應該能夠在以 macOS 為基礎的電腦上的網頁瀏覽器中顯示訊息的某種表示。

螢幕閱讀程式會使用屬性來讀取與使用者交談的文字記錄。`AccessibilityLabel` 屬性會指定訊息的顯示方式, 目前`MSMessageTemplateLayout`只支援, 而且看起來如下: `Layout`

[![](advanced-message-app-extensions-images/interactive06.png "MSMessageTemplateLayout 範本")](advanced-message-app-extensions-images/interactive06.png#lightbox)

的`Image` 屬性`MSMessageTemplateLayout`會提供畫面上 MessageBubble 主要主體的內容。 屬性也會提供訊息內文的內容, 但允許不`UIImage`支援的內容 (例如在背景中迴圈的影片檔案)。 `MediaFileUrl` 如果同時`Image`提供和`MediaFileUrl`屬性, 將會優先`Image`使用屬性。 `MediaFileUrl`支援 PNG、JPEG、GIF 和 video (以任何可由媒體播放機 framework 播放的格式) 媒體格式。

建議的媒體大小為 300 x 300 圖元, 解析度為3倍。 也會接受稍微較大且較小的資產, 而且 Apple 會以幾個不同的大小來建議測試, 以獲得最佳結果。 訊息應用程式會向下取樣, 並視需要調整此媒體。

當資產傳送至接收者時, [訊息] 應用程式會自動轉碼附加的任何媒體, 以將其從網路傳輸優化。 因此, Apple 不鼓勵在附加至郵件的媒體中包含文字, 因為媒體會相應縮小並壓縮以進行傳輸, 因此可能會轉譯文字模糊。

`ImageTitle` 和`ImageSubtitle`屬性會提供訊息反升中顯示之媒體的描述。 這些屬性將會以文字形式傳送到接收裝置, 在此 crisply 中會將它們轉譯成影像的左下角。

`Caption` 、`SubCaption`和屬性會進一步描述影像,並會在影像下方的區段中呈現。`TrailingSubcaption` `TrailingCaption` 將所有這些屬性設定為`null` , 將會建立不含標題區域的訊息反升:

[![](advanced-message-app-extensions-images/interactive07.png "沒有標題區域的訊息反升")](advanced-message-app-extensions-images/interactive07.png#lightbox)

最後要注意的是, 訊息應用程式會在訊息反升的左上角繪製訊息應用程式延伸模組的圖示。

## <a name="sending-a-message"></a>傳送訊息

`MSMessage`一旦組成之後, 您就可以使用下列程式碼來傳送它:

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

的`ActiveConversation` 屬性`MSMessagesAppViewController`會保存訊息應用程式延伸模組啟動所在的目前交談。

`InsertMessage` 呼叫的以將訊息包含在交談中,並處理可能`MSConversation`發生的任何錯誤。 如果成功包含訊息, 則會在輸入欄位中顯示訊息反升。

此外, 延伸模組可以將不同類型的資料傳送至交談, 例如:

- **文字** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **附件** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **貼紙** -  ,`ActiveConversation.InsertSticker (sticker, (obj) => {...});`其中是`sticker` 。 `MSSticker`

當新內容位於輸入欄位之後, 使用者就能夠藉由使用藍色的 [**傳送**] 按鈕 (就如同任何一般訊息) 來傳送訊息。 訊息應用程式延伸模組無法自動傳送內容, 此進程完全在使用者的控制之下。

## <a name="handling-the-compact-and-expanded-modes"></a>處理 Compact 和展開模式

訊息應用程式延伸模組可以在兩種不同的視圖模式中顯示:

[![](advanced-message-app-extensions-images/interactive08.png "以兩種不同的視圖模式顯示的訊息應用程式延伸模組:已擴充 Compact &")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -這是預設模式, 其中訊息應用程式延伸模組佔用訊息視圖的底部 25%。 在 Compact 模式中, 應用程式沒有鍵盤、水準滾動或滑動手勢辨識器的存取權。 應用程式可以存取輸入欄位, 而且`InsertMessage`會立即向使用者顯示對的呼叫。
- 已**展開**-訊息應用程式延伸模組會填滿整個訊息視圖。 它沒有輸入欄位的存取權, 但可以存取鍵盤、水準滾動和滑動手勢辨識器。

訊息應用程式延伸模組可以隨時以程式設計或手動方式在這些模式之間切換, 而且應該立即回應視圖模式中的任何變更。

請參閱下列在兩個不同的視圖模式之間處理切換的範例。 每個狀態都需要兩個不同的視圖控制器。 會處理**Compact**視圖, 而且將會處理展開的視圖: `StickerBrowserViewController` `AddStickerViewController`

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

覆`DidTransition`寫方法以處理兩種模式之間的切換:

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

(選擇性) 應用程式可能已使用`WillTransition`方法來處理視圖模式變更, 然後才呈現給使用者 (如同上述的 Icecream 產生器範例中所做的)。 如需詳細資訊, 請參閱我們的[進一步的貼紙自訂](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md)檔。

## <a name="replying-to-a-message"></a>回復郵件

有兩種情況, 訊息應用程式延伸模組在回復訊息時必須處理:

[![](advanced-message-app-extensions-images/interactive09.png "處於非作用中和主動模式的訊息應用程式延伸模組")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **延伸模組為非**使用中-訊息文字記錄中有其中一個訊息應用程式延伸模組的訊息, 使用者可以點擊此功能來啟動擴充功能並繼續互動式交談。
- **延伸模組為**作用中-使用者可以在訊息文字記錄中, 按一下訊息應用程式延伸模組的訊息反升, 以進入展開的視圖模式, 並從停止的地方繼續互動進程。

### <a name="the-extension-is-inactive"></a>延伸模組為非使用中

當使用者在訊息文字記錄中對訊息進行反升, 而且訊息應用程式延伸模組處於非使用中狀態時, 將會發生下列進程:

[![](advanced-message-app-extensions-images/interactive10.png "處理非作用中訊息的反升")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. 使用者會點擊擴充功能的訊息反升。
2. 當擴充功能啟動時, 訊息應用程式將會啟動一個進程。
3. 會呼叫`MSConversation`方法並傳遞, 代表訊息應用程式延伸模組執行所在的交談。 `DidBecomeActive`
4. 因為延伸模組是以`UIViewController` `ViewWillAppear`為基礎, 而且`ViewDidAppear`會呼叫。

當程式完成時, 訊息應用程式延伸模組將會以展開的視圖模式呈現。

### <a name="the-extension-is-active"></a>延伸模組為作用中

當使用者在訊息文字記錄中對訊息進行反升, 且訊息應用程式延伸模組為作用中時, 將會進行下列程式:

[![](advanced-message-app-extensions-images/interactive11.png "處理作用中訊息反升")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. 使用者會點擊擴充功能的訊息反升。
2. 因為訊息應用程式延伸模組已在使用中`WillTransition` , 所以`MSMessagesAppViewController`會呼叫的方法來處理從 Compact 切換到展開的視圖模式。
3. 的`DidSelectMessage`方法`MSMessagesAppViewController` 會被呼叫,`MSConversation`並傳遞訊息,並將其設為冒泡所屬的。`MSMessage`
4. 呼叫的`MSMessagesAppViewController`方法, 以處理從 Compact 切換至展開的視圖模式。 `DidTransition`

同樣地, 當程式完成時, 訊息應用程式延伸模組將會以展開的視圖模式呈現。

### <a name="accessing-the-selected-message"></a>存取選取的訊息

不論是哪一種情況, 當使用者按下屬於訊息應用程式延伸模組的訊息反升者時, 都必須`MSMessage` `SelectedMessage`使用的屬性`MSConversation`來存取所繪製的。

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

選取的訊息應該會顯示在訊息應用程式延伸模組的 UI 中, 而且應該允許使用者撰寫回應。

## <a name="removing-partially-completed-messages"></a>移除部分完成的訊息

在交談的兩個使用者之間傳送互動式對話的不同步驟的程式中, 部分完成的訊息可能會開始雜亂訊息文字記錄:

[![](advanced-message-app-extensions-images/interactive12.png "部分完成的訊息氣泡可能會雜亂訊息文字記錄")](advanced-message-app-extensions-images/interactive12.png#lightbox)

相反地, 訊息應用程式延伸模組應折迭先前的訊息, 並在訊息文字記錄中折迭為簡潔的批註:

[![](advanced-message-app-extensions-images/interactive13.png "折迭訊息文字記錄中的上一個訊息氣泡")](advanced-message-app-extensions-images/interactive13.png#lightbox)

這會使用`MSSession`來處理, 以折迭所有現有的步驟。 因此, 可以修改`MSMessagesAppViewController`類別的方法,如下所示:`DidSelectMessage`

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

如果選取的訊息已經`MSSession`存在, 則會使用它, 否則會建立新`MSSession`的。 的`SummaryText` 屬性`MSMessage`是用來將標題加入折迭的先前步驟。 如果屬性設定為`null`, 則會從對話文字記錄中完全移除交談中的先前步驟。 `SummaryText`

## <a name="advanced-message-api-features"></a>Advanced Message API 功能

透過上述詳細說明的新訊息 API 的基本功能, 接下來請檢查 Apple 已內建在架構中的一些較先進功能。

首先, `MSMessagesAppViewController`類別中有數個其他的覆寫方法, 可讓您更深入地存取對話:

- `DidStartSendingMessage`-當使用者按下 [傳送] 按鈕時, 就會呼叫此。 這並不表示訊息實際上已傳遞給收件者, 只是已啟動傳送程式。
- `DidCancelSendingMessage`-當使用者在對話文字記錄中, 按下 [訊息] 右上角的 [ *X* ] 按鈕時, 就會發生這種情況。
- `DidReceiveMessage`-當訊息應用程式延伸模組為作用中時, 會呼叫此方法, 從交談中的其中一個參與者收到新的訊息。

### <a name="group-conversations"></a>群組交談

訊息應用程式延伸模組可在使用者參與群組交談時使用 (含3個或更多個人), 而且在設計和執行訊息應用程式延伸模組時, 必須考慮這一點。

請查看與三位使用者在群組交談中的下列互動:

[![](advanced-message-app-extensions-images/interactive14.png "與三位使用者進行群組交談的互動")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. 使用者1傳送一個群組互動式訊息, 要求使用者2和使用者3選擇漢堡一起享用配料。
2. 使用者2選擇 [tomatoes]。
3. 使用者3選擇 pickles。
4. 使用者2的和使用者3的選擇幾乎會在同一時間抵達使用者1。 因此, 使用者2的選擇會折迭為摘要行, 且無法使用。 此案例也可能已翻轉, 並顯示使用者2的選擇, 而使用者3則會折迭。

不論是哪一種情況, 都不會出現這種行為, 因為使用者1應該能夠同時存取使用者2的和使用者3的選擇。 為了處理這種情況, Apple 建議訊息應用程式延伸模組會將訊息狀態儲存在雲端, 並使用的 URL 屬性`MSMessage` (在使用者之間傳送) 來存取此狀態。

當使用者傳送訊息時, 會產生會話權杖, 並將目前的訊息狀態推送至雲端。 當使用者在對話文字記錄中按下訊息反升時, 會使用會話權杖從雲端抓取目前的會話狀態。

### <a name="sender-identifiers"></a>寄件者識別碼

若要討論存取訊息寄件者的識別碼, 請取得上述的群組交談範例:

[![](advanced-message-app-extensions-images/interactive15.png "群組交談傳送識別碼")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. 同樣地, User 1 會傳送一個群組互動式訊息, 要求使用者2和使用者3選擇漢堡一起享用配料。
2. 使用者3挑選 pickles。
3. 使用者3的選擇會回傳給使用者 1, 而使用者2尚未回復。
4. 因為 Apple 非常關心使用者隱私權, 所以訊息應用程式延伸模組只會知道在交談中指派給每`NSUUID`個參與者的唯一識別碼 (如)。 在本機裝置上, 只知道目前使用者的識別碼。
5. `MSMessage`具有一個`SenderIdentifier`屬性, 它會符合擴充功能已知的參與者清單中的其中一個使用者。
6. 每個使用者裝置都有自己的參與者清單複本, 而且只知道本機使用者的識別碼。
7. 當傳送訊息時, 它`SenderIdentifier`的屬性也稱為「本機使用者」。

傳送者識別碼可以透過下列方式使用:

- 藉由查看參與者清單, 延伸模組可以取得對話中的使用者數目。
- 當擴充功能收到來自使用者的訊息時, 它可以追蹤寄件者識別碼。 如果它收到另一個具有相同傳送者識別碼的訊息, 延伸模組就會知道它是來自相同的使用者。
- 它們可以用來協助識別交談中的特定使用者。

傳送者識別碼可以用於的`MSMessageTemplateLayout`任何文字欄位中, 其前面會加上貨幣符號 (`$`)。 例如：

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

當 [訊息] 應用程式顯示以這種格式設定的訊息反升時, `$uuid...`會將取代為傳送訊息之交談中的參與者連絡人名稱。

寄件者識別碼在每個裝置上都是唯一的, 因此請再次查看上面的圖表, 請注意, 使用者1的裝置和使用者3的裝置在交談中的每個參與者都有不同的唯一寄件者識別碼。

寄件者識別碼的範圍限於訊息應用程式延伸模組的安裝。 因此, 如果使用者卸載並重新安裝訊息應用程式延伸模組, 則會針對新的安裝產生新的傳送者識別碼。

若要存取寄件者識別碼, 延伸模組可以使用下列程式碼:

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

訊息應用程式延伸模組所產生的互動式訊息將會在下列 Apple 平臺上傳遞:

- watchOS 3
- macOS Sierra
- iOS 10

在這三種平臺中, 只有 iOS 10 會允許使用者產生互動式訊息。 在 macOS Sierra 上, 如果使用者按一下互動式訊息反升, 會在 Safari 中開啟附加`MSMessage`至的 URL, 並在該處顯示訊息的標記法。

在 watchOS 上, 「訊息」應用程式可以將互動式訊息遞交給附加的 iOS 裝置, 讓使用者可以在其中撰寫回復。

如果在較舊的 Apple 平臺上收到互動式訊息, 新的訊息 API 就會支援 fallback:

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

它們會以一種不同的訊息, 以回溯格式傳遞:

- 其中一個會是範本配置所提供的影像。
- 另一個則是中`MSMessage`所提供的 URL。

## <a name="summary"></a>總結

本文提供的先進技術, 可讓您在與**訊息**應用程式整合的 Xamarin iOS 解決方案中使用訊息應用程式延伸模組, 並向使用者呈現新功能。


## <a name="related-links"></a>相關連結

- [霜淇淋 Builder (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [訊息參考](https://developer.apple.com/reference/messages)
- [應用程式擴充程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
