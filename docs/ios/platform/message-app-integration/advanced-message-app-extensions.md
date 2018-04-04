---
title: 進階的訊息應用程式擴充功能
description: 本文將說明進階的技巧，在與郵件應用程式整合，並對使用者顯示的新功能的 Xamarin.iOS 方案中使用的訊息應用程式擴充功能。
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: cd2cabf98c83bba7502e8533e482713a9c43f67a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="advanced-message-app-extensions"></a>進階的訊息應用程式擴充功能

_本文將說明進階的技巧，在與郵件應用程式整合，並對使用者顯示的新功能的 Xamarin.iOS 方案中使用的訊息應用程式擴充功能。_


新訊息應用程式擴充功能整合到 iOS 10、**訊息**應用程式並呈現給使用者的新功能。 擴充功能可以傳送文字、 貼紙、 媒體檔案和互動式的訊息。

## <a name="about-message-app-extensions"></a>訊息應用程式擴充功能

如前所述，訊息應用程式擴充功能與整合**訊息**應用程式並呈現給使用者的新功能。 擴充功能可以傳送文字、 貼紙、 媒體檔案和互動式的訊息。 有兩種類型的訊息應用程式擴充功能：

- **貼紙套件**-包含使用者可以新增至訊息的貼紙的集合。 可以建立不需要撰寫任何程式碼的貼紙的組件。
- **應用程式 iMessage** -可以呈現自訂使用者介面選取貼紙、 輸入文字，包括媒體檔案 （具有選擇性型別轉換） 以及建立、 編輯和傳送互動訊息的訊息應用程式內。

訊息應用程式擴充功能，提供三種主要的內容類型：

- **互動式訊息**-一種應用程式產生的自訂訊息內容，當使用者點選在訊息中，應用程式會啟動在前景。
- **貼紙**層可以包含在使用者之間傳送的訊息的應用程式所產生的映像。 請參閱我們[冰淇淋產生器](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)貼紙的組件的應用程式的範例實作的範例應用程式。
- **支援的其他內容**-應用程式可以提供內容，例如相片、 影片、 文字或一律訊息應用程式所支援類型的連結。

新增 ios 10，訊息應用程式現在包含自己專用的內建的應用程式存放區。 將顯示任何包括訊息應用程式擴充功能的應用程式，並將其升級此存放區中。 新的郵件應用程式服務將會顯示已從來為使用者提供快速存取訊息應用程式市集下載任何應用程式。

也新增在 iOS 10、 Apple 已加入可讓使用者輕易地找到應用程式內嵌應用程式屬性。 比方說，如果一位使用者將內容傳送至另一個在第 2 個使用者不需要的應用程式安裝 （例如貼紙的範例），傳送應用程式名稱被列在訊息記錄中的內容。 如果在使用者點選應用程式的名稱，我們會開啟訊息應用程式存放區和存放區中選取的應用程式。

訊息應用程式擴充功能都與現有的 iOS 應用程式開發人員會熟悉建立，而且可以存取所有標準的架構和標準的 iOS 應用程式的功能。 例如: 

- 他們可以存取應用程式內購買。
- 他們擁有存取權 Apple Pay。
- 他們可以存取裝置的硬體，例如網路攝影機。

IOS 10 只支援訊息應用程式擴充功能，不過，這些擴充功能所傳送的內容在 watchOS 及 macOS 裝置上檢視。 新_最近頁面_加入 watchOS 3，會顯示最近使用的貼紙的電話，包括訊息應用程式延伸模組，從已傳送，並且允許使用者從監看式傳送這些貼紙。

## <a name="about-interactive-messages"></a>有關互動式訊息

互動式訊息顯示自訂訊息泡泡，也所提供的訊息應用程式擴充功能。 它們可讓使用者建立互動式訊息內容，將其插入訊息的 [輸入] 欄位中並將它送出。

[![](advanced-message-app-extensions-images/interactive01.png "建立互動式訊息內容")](advanced-message-app-extensions-images/interactive01.png#lightbox)

接收的使用者可以藉由點選訊息歷程，以建立訊息應用程式擴充功能載入其訊息泡泡回覆互動的訊息。 延伸模組將可啟動的全螢幕，並允許使用者撰寫回覆，並將它傳送回原始的使用者。

[![](advanced-message-app-extensions-images/interactive02.png "副檔名啟動全螢幕")](advanced-message-app-extensions-images/interactive02.png#lightbox)


將下面將詳細涵蓋下列主題：

- 訊息 API 概觀
- 延伸模組的生命週期
- 撰寫電子郵件
- 傳送訊息

## <a name="messages-api-overview"></a>訊息 API 概觀

當使用者叫用，訊息應用程式擴充功能將會顯示底部的精簡檢視模式中的訊息記錄：

[![](advanced-message-app-extensions-images/interactive03.png "訊息 API 概觀")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. `MSMessageAppViewController`訊息應用程式擴充功能中的物件是延伸模組檢視會顯示給使用者時所呼叫的主要類別。
2. 交談會呈現給使用者，作為`MSConversation`物件執行個體。
3. `MSMessage`類別代表給定的交談中訊息泡泡。
4. `MSSession` 控制如何傳送訊息。
5. `MSMessageTemplateLayout` 控制訊息的顯示方式

## <a name="the-extension-lifecycle"></a>延伸模組的生命週期

看看訊息應用程式的延伸模組，無法成為使用中的程序：

[![](advanced-message-app-extensions-images/interactive04.png "訊息應用程式的延伸模組，無法成為使用中的程序")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. 擴充功能 （例如，從應用程式服務） 啟動時，訊息應用程式將會啟動處理程序。
2. `DidBecomeActive`方法呼叫，並傳遞`MSConversation`代表交談訊息應用程式擴充功能中執行。
3. 因為擴充功能以登出`UIViewController`兩者`ViewWillAppear`和`ViewDidAppear`稱為。

接下來，看看變成停用的訊息應用程式擴充功能的程序：

[![](advanced-message-app-extensions-images/interactive05.png "變成停用的訊息應用程式擴充功能的程序")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. 正在停用訊息的應用程式擴充功能，當`ViewWillDisappear`會先呼叫的方法。
2. 然後在`ViewDidDisappear`會呼叫的方法。
3. `WillResignActive`方法呼叫，並傳遞`MSConversation`代表交談訊息應用程式擴充功能中執行。 此時，訊息應用程式和延伸模組之間的連線即將釋出。
4. 稍後的位置，此程序便會終止訊息 」 應用程式。

擴充功能就短期存在的處理程序，因為它會以節省處理和電池電源系統積極地終止。 開發人員應該記住這設計和實作訊息應用程式擴充功能時。

## <a name="composing-a-message"></a>撰寫電子郵件

一旦訊息應用程式擴充功能的程序中執行，並顯示其使用者介面，下列程式碼可用於撰寫新郵件：

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

此程式碼會建立新`MSMessage`並設定某些屬性 (例如`Url`)。 雖然訊息只能建立在 iOS 上，才能傳送到 iOS 及 macOS 顯示。

如果使用者按一下上 macOS 交談中訊息泡泡，Mac 會嘗試開啟網頁瀏覽器中的 URL 中指定的位址。 如此一來，開發人員網站應該能夠顯示某些表示法 macOS 的 web 瀏覽器中的訊息型機器。

`AccessibilityLabel`屬性用於螢幕助讀程式讀取文字記錄交談的使用者。 `Layout`屬性會指定訊息的顯示方式，目前只`MSMessageTemplateLayout`支援，而且看起來如下：

[![](advanced-message-app-extensions-images/interactive06.png "MSMessageTemplateLayout 範本")](advanced-message-app-extensions-images/interactive06.png#lightbox)

`Image`屬性`MSMessageTemplateLayout`MessageBubble 螢幕上的主要本文提供內容。 `MediaFileUrl`屬性也會提供訊息泡泡圖中，主體的內容，但不是支援的內容可讓`UIImage`（例如，會在背景中執行迴圈的視訊檔案）。 如果兩個`Image`和`MediaFileUrl`會提供屬性，`Image`屬性更高的優先順序。 `MediaFileUrl`支援 PNG、 JPEG、 GIF 和視訊 （在可以播放 Media Player framework 的任何格式） 的媒體格式。

建議的媒體大小是 300 x 300 像素 3 倍的解析度。 稍微較大和較小的資產也接受和 Apple 建議測試幾個不同的大小，以取得最佳的結果。 訊息應用程式會向下範例並調整此媒體依需要。

當資產會傳送至收件者時，連接任何媒體都會自動儲存轉碼由訊息 」 應用程式透過網路傳輸的最佳化工作。 因為這個緣故，Apple 阻止包括中的媒體，因為媒體會向下調整資源，並針對傳輸，壓縮會附加至訊息的文字可能會使得文字難以辨識。

`ImageTitle`和`ImageSubtitle`屬性會提供媒體訊息泡泡圖中顯示的描述。 這些屬性將會傳送以文字到接收裝置它們將會在左下角的映像進行清楚轉譯。

`Caption`， `SubCaption`，`TrailingCaption`和`TrailingSubcaption`屬性進一步描述映像，並將影像下方區段中轉譯。 設定所有這些屬性，以設定`null`會建立訊息泡泡沒有標題區域：

[![](advanced-message-app-extensions-images/interactive07.png "沒有標題區域的訊息泡泡圖")](advanced-message-app-extensions-images/interactive07.png#lightbox)

請注意最後一個項目是訊息 」 應用程式將會繪製訊息應用程式擴充功能圖示，訊息泡泡左上角。

## <a name="sending-a-message"></a>傳送訊息

一次`MSMessage`已撰寫下列程式碼可以用來將它送出：

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

`ActiveConversation`屬性`MSMessagesAppViewController`會保留目前的交談訊息應用程式擴充功能已啟動中。

呼叫`InsertMessage`的`MSConversation`来納入交談的訊息處理可能會發生任何錯誤。 已成功包含訊息時，訊息泡泡圖將顯示輸入欄位中。

此外，擴充功能可以將不同類型的資料傳送給交談，例如：

- **Text** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **附件** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **貼紙** -  `ActiveConversation.InsertSticker (sticker, (obj) => {...});`其中`sticker`是`MSSticker`。

輸入欄位上新的內容之後，使用者就可以傳送訊息，藉由點選藍色**傳送**按鈕 （就如同任何一般訊息）。 沒有任何內容，自動傳送的訊息應用程式擴充功能的方式，此程序是完全在使用者的控制之下。

## <a name="handling-the-compact-and-expanded-modes"></a>處理壓縮和展開的模式

訊息應用程式擴充功能可以顯示在兩個不同的檢視模式的其中一個：

[![](advanced-message-app-extensions-images/interactive08.png "兩個不同的檢視模式中顯示的訊息應用程式擴充功能： 壓縮和展開")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -這是訊息的應用程式擴充功能會佔用下 25%的 [訊息] 檢視的預設模式。 精簡模式，應用程式沒有存取鍵盤、 水平捲動或撥動筆勢辨識器。 應用程式沒有存取輸入欄位，並呼叫`InsertMessage`立即向使用者顯示。
- **展開**-訊息應用程式擴充功能會填滿整個 [訊息] 檢視。 它並沒有存取至輸入欄位，但並沒有鍵盤、 水平捲軸及撥動筆勢辨識器的存取。

訊息應用程式擴充功能可以切換這些模式之間以程式設計方式或以手動方式由使用者在任何時間，而且應該立即回應任何會變更檢視模式。

看看下列範例會處理兩個不同的檢視模式之間切換。 兩個不同的檢視控制站就必須針對每個狀態。 `StickerBrowserViewController`控點**Compact**檢視和`AddStickerViewController`處理**加寬**檢視：

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

（選擇性） 無法使用該應用程式`WillTransition`之前可以處理的檢視模式變更 （為 「 已完成上述 Icecream 產生器範例中），它會呈現給使用者的方法。 如需詳細資訊，請參閱我們[進一步貼紙自訂](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md)文件。

## <a name="replying-to-a-message"></a>回覆訊息

有兩個訊息的應用程式擴充功能需要處理回覆訊息時的情況：

[![](advanced-message-app-extensions-images/interactive09.png "訊息應用程式擴充功能的非作用中與現用模式")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **擴充功能為非作用中**-有一個使用者可以點選以啟用擴充功能，並繼續互動式交談的訊息文字記錄的訊息應用程式擴充功能的訊息 （泡泡）。
- **擴充功能為 「 作用中**-使用者可以點選輸入展開檢視模式，並繼續從而中斷的地方互動式程序的訊息文字記錄中的訊息應用程式擴充功能的訊息泡泡。

### <a name="the-extension-is-inactive"></a>擴充功能為非作用中

當使用者在訊息文字記錄中點選訊息泡泡，而非使用中訊息應用程式擴充功能時，會發生下列處理序：

[![](advanced-message-app-extensions-images/interactive10.png "處理非使用中的訊息泡泡圖")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. 在使用者點選擴充功能的訊息泡泡。
2. 當啟動擴充功能時，訊息應用程式會啟動處理程序。
3. `DidBecomeActive`方法呼叫，並傳遞`MSConversation`代表交談訊息應用程式擴充功能中執行。
4. 因為擴充功能以登出`UIViewController`兩者`ViewWillAppear`和`ViewDidAppear`稱為。

處理程序完成時，訊息應用程式擴充功能將會出現在展開檢視模式。

### <a name="the-extension-is-active"></a>擴充功能為 「 作用中

當使用者在訊息文字記錄中點選訊息泡泡並且作用中的訊息應用程式擴充功能時，將會發生下列處理序：

[![](advanced-message-app-extensions-images/interactive11.png "處理作用中的訊息泡泡圖")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. 在使用者點選擴充功能的訊息泡泡。
2. 訊息應用程式擴充功能已在使用中，因為`WillTransition`方法`MSMessagesAppViewController`呼叫以處理從精簡型切換至展開檢視模式。
3. `DidSelectMessage`方法`MSMessagesAppViewController`呼叫並傳遞`MSMessage`和`MSConversation`所屬訊息泡泡。
4. `DidTransition`方法`MSMessagesAppViewController`呼叫以處理從精簡型切換至展開檢視模式。

同樣地，處理程序完成時，訊息應用程式擴充功能將會出現在展開檢視模式。

### <a name="accessing-the-selected-message"></a>存取選取的訊息

在任一情況下，當使用者點選訊息泡泡屬於訊息應用程式擴充功能，它必須能夠存取`MSMessage`，已點選`SelectedMessage`屬性`MSConversation`。

例如: 

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

選取的訊息應該會顯示在訊息應用程式擴充功能的 UI，並允許使用者撰寫回應。

## <a name="removing-partially-completed-messages"></a>正在移除部分完成的訊息

正在傳送之不同步驟的互動式交談的兩個使用者之間的交談中，部分完成的訊息 （泡泡） 就可以開始弄亂訊息文字記錄：

[![](advanced-message-app-extensions-images/interactive12.png "部分完成的訊息 （泡泡） 可以佔用訊息文字記錄")](advanced-message-app-extensions-images/interactive12.png#lightbox)

相反地，訊息應用程式擴充功能應該成簡潔的註解訊息文字記錄中摺疊先前的訊息 （泡泡）：

[![](advanced-message-app-extensions-images/interactive13.png "摺疊訊息文字記錄中先前的訊息 （泡泡）")](advanced-message-app-extensions-images/interactive13.png#lightbox)

這可處理使用`MSSession`摺疊所有現有的步驟。 所以`DidSelectMessage`方法`MSMessagesAppViewController`類別可以修改成如下所示：

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

如果已選取的訊息已經結束`MSSession`，否則使用新`MSSession`建立。 `SummaryText`屬性`MSMessage`用來將標題加入至摺疊的上一個步驟。 如果`SummaryText`屬性設定為`null`，交談中的上一個步驟會完全移除交談的文字記錄。

## <a name="advanced-message-api-features"></a>進階訊息 API 功能

與上述的詳細說明新的訊息 API 的基本功能，接下來檢查部份 Apple 已建置到架構更進階功能。

首先，有數個其他覆寫方法中的`MSMessagesAppViewController`類別可提供更深入的存取權交談：

- `DidStartSendingMessage` -這是在使用者點選 [傳送] 按鈕時被呼叫的。 這不表示訊息實際上已傳遞至收件者，只是傳送程序已啟動。
- `DidCancelSendingMessage` -這會在使用者點選*X*交談文字記錄中的訊息泡泡的右上角的按鈕。
- `DidReceiveMessage` -此方法稱為作用中訊息的應用程式擴充功能時收到新訊息從一個交談的參與者。

### <a name="group-conversations"></a>群組交談

雖然涉及使用者群組交談 （具有 3 個以上的個人版） 中，而這必須納入考量，設計和實作訊息應用程式擴充功能時，可以使用訊息應用程式擴充功能。

查看下列互動與三個使用者群組交談中：

[![](advanced-message-app-extensions-images/interactive14.png "有三個使用者群組交談中的互動")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. 使用者 1 傳送群組互動訊息詢問使用者 2，以及使用者 3 選擇漢堡配料。
2. 使用者 2 選擇 tomatoes。
3. 使用者 3 選擇酸黃瓜。
4. 使用者 2 和 3 使用者的選擇會回到使用者 1 抵達幾乎相同的時間。 如此一來，使用者 2 的選擇會摺疊為摘要資料行，而且無法使用。 此情況下無法有也已翻轉、 與使用者 2 的選擇顯示 」 和 「 使用者 3 的要摺疊。

在任一情況下，此行為是不需要使用者 1 應該能夠存取使用者 2 和 3 使用者的選擇。 若要處理這種情況下，訊息應用程式擴充功能會將訊息狀態儲存在雲端中，可使用的 URL 屬性，建議 Apple `MSMessage` （可取得傳送使用者之間） 來存取這個狀態。

當使用者傳送訊息時，工作階段權杖產生並推送至雲端的目前訊息狀態。 當使用者點選訊息泡泡交談文字記錄中時，工作階段權杖用來擷取目前的工作階段狀態從雲端。

### <a name="sender-identifiers"></a>寄件者識別

若要討論存取訊息的寄件者的識別項、 採用群組交談，上述的範例：

[![](advanced-message-app-extensions-images/interactive15.png "傳送的識別項的群組交談")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. 同樣地，使用者 1 會傳送一組互動式訊息詢問使用者 2，以及使用者 3 選擇漢堡配料。
2. 「 使用者 3 會挑選酸黃瓜。
3. 使用者 3 的選擇送達回使用者 1 和使用者 2 不尚未回覆。
4. 由於 Apple 是非常注重使用者隱私權，訊息應用程式擴充功能只知道唯一識別碼 (為`NSUUID`)，指派交談中的每個參與者。 在本機裝置上，就只有目前使用者的識別項。
5. `MSMessage`具有`SenderIdentifier`符合使用者的其中一個屬性的參與者的清單中已知的延伸模組。
6. 每個使用者裝置有它自己的複本，其中同樣地，只有本機使用者的識別項為已知的參與者清單。
7. 當傳送訊息時，其`SenderIdentifier`也稱為屬性設為屬於本機使用者。

寄件者識別項可以用下列方式：

- 藉由查看參與者清單延伸模組可以取得交談中的使用者數目。
- 當擴充從使用者接收訊息時，它可以追蹤的寄件者識別項。 如果收到相同的傳送者識別項與另一則訊息，擴充功能就會知道它是來自相同的使用者。
- 它們可以用於協助識別特定交談中的使用者。

傳送者識別碼可用於任何文字欄位的`MSMessageTemplateLayout`前置詞以貨幣符號的方式 (`$`)。 例如: 

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

當訊息 」 應用程式會顯示訊息泡泡，這種類型的格式時，將會取代`$uuid...`中傳送訊息之交談的參與者的連絡人名稱。

寄件者識別項是上每個裝置，唯一的因此使用者 1 的裝置和裝置使用者的 3，查看再次注意，圖中交談的每個參與者都有不同的唯一寄件者識別碼。

寄件者識別的範圍為訊息的應用程式擴充功能的安裝。 因此如果使用者解除安裝並重新安裝訊息的應用程式擴充功能將會為新的安裝產生新的傳送者識別項。

若要存取 寄件者識別項，擴充功能可以使用下列程式碼：

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

訊息應用程式擴充功能所產生的互動式訊息會傳送下列 Apple 平台：

- watchOS 3
- macOS Sierra
- iOS 10

三個平台，iOS 10 可讓使用者以產生互動的訊息。 在上 macOS 利也，如果使用者在互動式訊息泡泡圖 URL 附加至`MSMessage`Safari 中將會開啟，而且應該那里顯示訊息的表示法。

WatchOS，訊息應用程式可以遞交互動式附加的 iOS 裝置使用者可以在其中撰寫回覆訊息。

新的訊息 API 可支援後援，如果互動的訊息會傳送舊 Apple 平台：

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

它們會以兩個不同的訊息傳遞後援格式：

- 其中一個會是映像，因為提供的範本版面配置。
- 其他將 URL 中提供`MSMessage`。

## <a name="summary"></a>總結

這篇文章提供了進階的技巧，在整合與 Xamarin.iOS 方案中使用的訊息應用程式擴充功能**訊息**應用程式和呈現給使用者的新功能。


## <a name="related-links"></a>相關連結

- [冰淇淋產生器 （範例）](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [訊息參考](https://developer.apple.com/reference/messages)
- [應用程式擴充功能的程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
