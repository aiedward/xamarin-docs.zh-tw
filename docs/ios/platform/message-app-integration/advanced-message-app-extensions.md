---
title: 在 Xamarin.iOS 中的進階的訊息應用程式擴充功能
description: 本文說明使用訊息應用程式擴充功能，與在 Messages 應用程式整合，並向使用者呈現的新功能的 Xamarin.iOS 方案中的進階的技術。
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: baceb59116dd907918b34eca4f44293051190954
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120517"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>在 Xamarin.iOS 中的進階的訊息應用程式擴充功能

_本文說明使用訊息應用程式擴充功能，與在 Messages 應用程式整合，並向使用者呈現的新功能的 Xamarin.iOS 方案中的進階的技術。_


新增至 iOS 10，訊息應用程式擴充功能則是與整合**訊息**給使用者的應用程式並為您介紹新功能。 延伸模組可以傳送文字、 貼紙、 媒體檔案和互動式訊息。

## <a name="about-message-app-extensions"></a>有關訊息的應用程式延伸模組

如上所述，訊息應用程式擴充功能與整合**訊息**給使用者的應用程式並為您介紹新功能。 延伸模組可以傳送文字、 貼紙、 媒體檔案和互動式訊息。 有兩種類型的訊息應用程式擴充功能：

- **貼紙套件**-包含使用者可以新增至訊息的貼紙的集合。 您可以建立貼紙的組件，而不需要撰寫任何程式碼。
- **iMessage 應用程式**-可以呈現自訂使用者介面中選取貼紙、 輸入文字，包括 （具有選擇性型別轉換） 的媒體檔案與建立、 編輯和互動傳送的訊息應用程式。

訊息應用程式擴充功能提供三種主要的內容類型：

- **互動式訊息**-是一種應用程式所產生的自訂訊息內容中，當使用者點選在訊息中，應用程式會在前景中啟動。
- **貼紙**-可以包含在使用者之間傳送的訊息應用程式所產生的映像。 請參閱我們[冰淇淋產生器](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)貼紙的組件應用程式的範例實作的範例應用程式。
- **其他支援的內容**-應用程式可以提供內容，例如相片、 影片、 文字或向來都在 Messages 應用程式所支援類型的連結。

新增至 iOS 10，訊息應用程式現在包含自己專用的內建的應用程式存放區。 將顯示包含訊息的應用程式擴充功能的任何應用程式，並將它升級這個存放區中。 新的 「 訊息 」 應用程式下拉式清單會顯示任何訊息的應用程式市集，以提供快速的存取權的使用者從已下載的應用程式。

也新增在 iOS 10 中，Apple 已新增可讓使用者輕鬆地找出應用程式內嵌應用程式屬性。 比方說，如果一位使用者將內容傳送至另一個從第 2 個使用者不需要的應用程式安裝 （例如貼紙的範例），傳送應用程式名稱被列在底下的訊息記錄中的內容。 如果在使用者點選應用程式的名稱，我們開啟訊息應用程式存放區並選取存放區中的應用程式。

訊息應用程式延伸模組是類似於現有的 iOS 應用程式開發人員是很熟悉建立，而且會有存取所有標準的架構和標準的 iOS 應用程式的功能。 例如: 

- 他們可以存取應用程式內購買。
- 他們可以到 Apple Pay 的存取。
- 他們可以存取，例如相機的裝置硬體。

IOS 10 只支援訊息的應用程式擴充功能，不過，這些擴充功能所傳送的內容可在 watchOS 和 macOS 裝置上檢視。 新_最近項目頁面_加入 watchOS 3 中，會顯示最新的貼紙已傳送從電話，包括訊息應用程式擴充功能，並允許使用者從監看式傳送這些貼紙。

## <a name="about-interactive-messages"></a>有關互動式訊息

互動式訊息顯示自訂訊息泡泡，並提供的訊息應用程式擴充功能。 它們可讓使用者建立互動式訊息內容，將其插入訊息的 [輸入] 欄位中，並將它傳送。

[![](advanced-message-app-extensions-images/interactive01.png "建立互動式訊息的內容")](advanced-message-app-extensions-images/interactive01.png#lightbox)

接收的使用者可以藉由點選來建立訊息應用程式擴充功能載入訊息歷程記錄中其訊息泡泡回覆互動式訊息。 擴充功能可啟動的全螢幕，而且允許使用者撰寫回覆並將它傳送回原始的使用者。

[![](advanced-message-app-extensions-images/interactive02.png "延伸模組啟動的全螢幕")](advanced-message-app-extensions-images/interactive02.png#lightbox)


下列主題將涵蓋詳細說明如下：

- 訊息 API 概觀
- 延伸模組的生命週期
- 撰寫電子郵件
- 傳送訊息

## <a name="messages-api-overview"></a>訊息 API 概觀

當使用者叫用，訊息應用程式擴充功能將會顯示底部的 精簡的檢視模式中的訊息記錄：

[![](advanced-message-app-extensions-images/interactive03.png "訊息 API 概觀")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. `MSMessageAppViewController`訊息應用程式擴充功能中的物件是擴充功能的檢視會顯示給使用者時，會呼叫的主要類別。
2. 交談會向使用者`MSConversation`物件執行個體。
3. `MSMessage`類別代表交談中指定的訊息泡泡。
4. `MSSession` 控制如何傳送訊息。
5. `MSMessageTemplateLayout` 控制訊息的顯示方式

## <a name="the-extension-lifecycle"></a>延伸模組的生命週期

看看訊息應用程式的擴充功能，成為作用中的程序：

[![](advanced-message-app-extensions-images/interactive04.png "訊息應用程式的擴充功能，成為作用中的程序")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. 當啟動擴充功能時 （例如從 [應用程式] 下拉式清單中） 時，訊息應用程式會啟動處理程序。
2. `DidBecomeActive`方法呼叫並傳遞`MSConversation`代表交談訊息應用程式延伸模組中執行。
3. 因為擴充功能關閉的基礎`UIViewController`兩者`ViewWillAppear`和`ViewDidAppear`稱為。

接下來，看看訊息應用程式的擴充功能，成為停用的程序：

[![](advanced-message-app-extensions-images/interactive05.png "訊息應用程式的延伸模組，變成停用的程序")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. 當訊息應用程式擴充功能已停用時、`ViewWillDisappear`會先呼叫方法。
2. 然後`ViewDidDisappear`會呼叫方法。
3. `WillResignActive`方法呼叫並傳遞`MSConversation`代表交談訊息應用程式延伸模組中執行。 此時，訊息 」 應用程式與擴充功能之間的連線，就是即將解除。
4. 稍後，此程序便會終止在 Messages 應用程式。

延伸模組是短暫的程序，因為它是以節省處理和電池電源系統積極地終止。 開發人員應該記住這設計和實作訊息應用程式擴充功能時。

## <a name="composing-a-message"></a>撰寫電子郵件

一旦訊息應用程式擴充功能執行的處理序中，並顯示其使用者介面，下列程式碼可以用來撰寫新郵件中：

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

此程式碼會建立新`MSMessage`，並設定數個屬性 (例如`Url`)。 雖然訊息只能建立在 iOS 上，可以是傳送至 iOS 和 macOS，要顯示。

如果使用者按一下訊息泡泡在 macOS 上的交談中，Mac 會嘗試開啟網頁瀏覽器中的 URL 中指定的位址。 如此一來，開發人員網站應該能夠顯示在 macOS 上的網頁瀏覽器中的訊息部分表示法為基礎的機器。

`AccessibilityLabel`屬性可由螢幕助讀程式來讀取使用者的交談文字記錄。 `Layout`屬性會指定訊息的顯示方式，目前只有`MSMessageTemplateLayout`支援，而且看起來如下：

[![](advanced-message-app-extensions-images/interactive06.png "MSMessageTemplateLayout 範本")](advanced-message-app-extensions-images/interactive06.png#lightbox)

`Image`屬性`MSMessageTemplateLayout`MessageBubble 螢幕上的主要內文中提供內容。 `MediaFileUrl`屬性也會提供訊息泡泡圖中，主體的內容，但不是支援的內容是用來`UIImage`（例如，會在背景循環的視訊檔案）。 如果兩個`Image`並`MediaFileUrl`會提供屬性，`Image`屬性將會優先。 `MediaFileUrl`支援 PNG、 JPEG、 GIF 和影片 （在可以播放 Media Player framework 的任何格式） 的媒體格式。

建議的媒體大小為 3 倍解析度的 300 x 300 像素。 也接受稍微較大和較小的資產，並測試幾個不同的大小，以取得最佳結果，Apple 建議。 訊息應用程式將向下取樣，並調整此所需的媒體。

當資產會傳送至接收者時，連接任何媒體將會自動透過網路傳輸的最佳化訊息應用程式的轉碼。 因為這個緣故，Apple，而且包括中的媒體，因為媒體會相應減少，並針對傳輸，壓縮會附加至訊息的文字因此可能呈現文字難以辨識。

`ImageTitle`和`ImageSubtitle`屬性提供的媒體，會顯示訊息泡泡圖中的描述。 這些屬性將會傳送為文字到接收端裝置它們將會在較低的左下角的 映像進行清晰地呈現。

`Caption`， `SubCaption`，`TrailingCaption`和`TrailingSubcaption`屬性進一步描述映像，並將在下圖下方區段中呈現。 設定所有這些屬性，以設定`null`會建立不含標題區域的訊息泡泡：

[![](advanced-message-app-extensions-images/interactive07.png "標題區域不在訊息泡泡圖")](advanced-message-app-extensions-images/interactive07.png#lightbox)

要注意的最後一件事是在 Messages 應用程式將會繪製訊息泡泡的左上角的訊息應用程式擴充功能圖示。

## <a name="sending-a-message"></a>傳送訊息

一次`MSMessage`已撰寫下列程式碼可以用來將它傳送：

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

`ActiveConversation`屬性`MSMessagesAppViewController`會保留目前啟動訊息的應用程式擴充功能的交談。

呼叫`InsertMessage`的`MSConversation`納入交談的訊息並處理任何可能發生的錯誤。 成功地包含訊息時，訊息泡泡圖將顯示在 [輸入] 欄位中。

此外，延伸模組可以將不同類型的資料傳送給這類的對話：

- **文字** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **附件** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **貼紙** -  `ActiveConversation.InsertSticker (sticker, (obj) => {...});`何處`sticker`是`MSSticker`。

在 輸入 欄位上新的內容之後，使用者可傳送訊息，依序點選 藍色**傳送**按鈕 （就如同任何一般的訊息）。 沒有內容，自動傳送的訊息 」 應用程式擴充功能的方法，此程序是完全控制的使用者。

## <a name="handling-the-compact-and-expanded-modes"></a>處理壓縮和展開的模式

訊息應用程式擴充功能可以顯示在兩個不同的檢視模式的其中一個：

[![](advanced-message-app-extensions-images/interactive08.png "在兩個不同的檢視模式中顯示的訊息應用程式擴充功能： 壓縮和展開")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -這是訊息的應用程式擴充功能會佔用最下方的 25%的 [訊息] 檢視的預設模式。 在精簡的模式下，應用程式並沒有鍵盤、 水平捲動或撥動筆勢辨識器的存取權。 應用程式能夠存取 [輸入] 欄位，並呼叫`InsertMessage`立即會那里對使用者顯示。
- **展開**-訊息應用程式擴充功能會填滿整個 [訊息] 檢視。 它並沒有存取權來輸入 欄位中，但並沒有鍵盤、 水平捲動和撥動筆勢辨識器的存取。

訊息應用程式擴充功能可以切換這些模式之間以程式設計方式或以手動方式由使用者在任何時間，而且應該立即回應任何變更檢視模式。

看看下列範例中處理兩個不同的檢視模式之間切換。 兩個不同的檢視控制器都必須針對每個狀態。 `StickerBrowserViewController`控制代碼**Compact**檢視和`AddStickerViewController`處理**展開**檢視：

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

或者，也可以使用應用程式`WillTransition`方法以處理檢視模式變更之前 （如同您在上述 Icecream 產生器範例），它會呈現給使用者。 如需詳細資訊，請參閱我們[進一步的貼紙自訂](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md)文件。

## <a name="replying-to-a-message"></a>回覆訊息

有兩個訊息的應用程式擴充功能必須處理的訊息回覆時的情況：

[![](advanced-message-app-extensions-images/interactive09.png "訊息應用程式擴充功能的非作用中與主動模式")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **延伸模組為非作用中**-有一個使用者可以點選以啟動延伸模組，並繼續互動式交談的訊息文字記錄中的訊息應用程式擴充功能的訊息 （泡泡）。
- **延伸模組是主動**-使用者可以點選中輸入的展開檢視模式，然後繼續進行互動的程序，從他們離開的地方將訊息文字記錄的訊息應用程式擴充功能的訊息泡泡。

### <a name="the-extension-is-inactive"></a>延伸模組為非使用中

當訊息泡泡的訊息文字記錄中的使用者點選時，訊息應用程式擴充功能為非使用中時，會發生下列處理序：

[![](advanced-message-app-extensions-images/interactive10.png "處理非使用中的訊息泡泡圖")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. 使用者可點選延伸模組的訊息泡泡。
2. 當啟動擴充功能時，訊息應用程式會啟動處理程序。
3. `DidBecomeActive`方法呼叫並傳遞`MSConversation`代表交談訊息應用程式延伸模組中執行。
4. 因為擴充功能關閉的基礎`UIViewController`兩者`ViewWillAppear`和`ViewDidAppear`稱為。

程序完成時，就會看到訊息應用程式擴充功能中的展開檢視模式。

### <a name="the-extension-is-active"></a>延伸模組為使用中狀態

當訊息泡泡的訊息文字記錄中的使用者點選時，並且訊息應用程式擴充功能在作用中時，會使用下列程序：

[![](advanced-message-app-extensions-images/interactive11.png "處理作用中的訊息泡泡圖")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. 使用者可點選延伸模組的訊息泡泡。
2. 訊息應用程式擴充功能已在使用，因為`WillTransition`方法的`MSMessagesAppViewController`呼叫以處理從精簡型切換成展開檢視模式。
3. `DidSelectMessage`方法`MSMessagesAppViewController`呼叫並傳遞`MSMessage`和`MSConversation`所屬訊息泡泡。
4. `DidTransition`方法的`MSMessagesAppViewController`呼叫以處理從精簡型切換成展開檢視模式。

同樣地，完成程序時，將會展開檢視模式中看到訊息應用程式擴充功能。

### <a name="accessing-the-selected-message"></a>存取選取的訊息

在任一情況下，當使用者點選訊息泡泡屬於訊息應用程式擴充功能，它必須取得的存取權`MSMessage`的點下的使用`SelectedMessage`屬性`MSConversation`。

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

正在傳送之不同步驟的互動式交談的兩個使用者之間的交談中，可以開始部分完成的訊息泡泡會擾亂訊息文字記錄：

[![](advanced-message-app-extensions-images/interactive12.png "部分完成的訊息 （泡泡） 可讓您可以想堆訊息文字記錄")](advanced-message-app-extensions-images/interactive12.png#lightbox)

相反地，訊息應用程式擴充功能應該到簡潔的註解訊息的文字記錄中摺疊前一訊息泡泡：

[![](advanced-message-app-extensions-images/interactive13.png "摺疊訊息文字記錄中先前的訊息 （泡泡）")](advanced-message-app-extensions-images/interactive13.png#lightbox)

這處理使用`MSSession`摺疊所有現有的步驟。 因此`DidSelectMessage`方法的`MSMessagesAppViewController`類別無法修改成如下所示：

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

如果選取的訊息已結束`MSSession`，否則使用新`MSSession`建立。 `SummaryText`屬性`MSMessage`用來將標題加入至摺疊的上一個步驟。 如果`SummaryText`屬性設定為`null`，交談中的上一個步驟會完全移除交談文字記錄。

## <a name="advanced-message-api-features"></a>進階訊息 API 功能

使用上述的詳細說明新的訊息 API 的基本功能，接下來檢查一些更進階 Apple 已內建於 framework 的功能。

首先，有數種其他覆寫方法中的`MSMessagesAppViewController`提供更深入存取交談的類別：

- `DidStartSendingMessage` -這是在使用者點選 [傳送] 按鈕時被呼叫的。 這不表示訊息實際上已傳遞至收件者，只要傳送程序已啟動。
- `DidCancelSendingMessage` -這會在使用者點選*X*交談文字記錄中的訊息泡泡的右上角的按鈕。
- `DidReceiveMessage` -使用中訊息的應用程式擴充功能時，會呼叫這個方法從一個交談參與者收到新的訊息。

### <a name="group-conversations"></a>群組交談

雖然使用者參與群組對話 （含 3 個或多個個人），這必須列入考量，設計和實作訊息應用程式擴充功能時，可用訊息的應用程式擴充功能。

使用三個使用者群組交談中看看看下列互動：

[![](advanced-message-app-extensions-images/interactive14.png "使用三個使用者群組交談中的互動")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. 使用者 1 傳送互動式訊息的群組要求使用者 2，以及使用者 3 選擇漢堡配料。
2. 使用者 2 選擇 tomatoes。
3. 使用者 3 選擇小。
4. 使用者 2 和 3 使用者的選擇會回到使用者 1 抵達幾乎相同的時間。 如此一來，使用者 2 選擇摺疊成摘要行，而且無法使用。 此情況下可能有也已翻轉、 使用者 2 的選擇顯示和使用者 3 的要摺疊起來。

在任一情況下，此行為是不想要為使用者 1 應該能夠存取使用者 2 和 3 使用者的選擇。 訊息應用程式擴充功能在雲端中儲存訊息狀態和使用的 URL 屬性，Apple 建議為了處理這種情況下， `MSMessage` （可取得使用者之間傳送的） 來存取此狀態。

當使用者傳送訊息時，工作階段權杖產生，並推送至雲端，與目前的訊息狀態。 當使用者點選訊息泡泡交談文字記錄中時，工作階段權杖用來從雲端擷取目前的工作階段狀態。

### <a name="sender-identifiers"></a>寄件者識別碼

若要存取訊息的寄件者的識別項的討論，採取群組交談，上述的範例：

[![](advanced-message-app-extensions-images/interactive15.png "傳送識別碼的群組交談")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. 同樣地，使用者 1 會傳送互動式訊息的群組要求使用者 2，以及使用者 3 選擇漢堡配料。
2. 使用者 3 會挑選小。
3. 選擇使用者 3 的送達回使用者 1 和使用者 2 已不尚未回覆。
4. 由於 Apple 是非常關心使用者隱私權，訊息應用程式擴充功能只會知道的唯一識別碼 (為`NSUUID`)，指派交談中的每個參與者。 在本機裝置上，即只有目前使用者的識別項。
5. `MSMessage`具有`SenderIdentifier`符合使用者的其中一個屬性的參與者清單中已知擴充功能。
6. 每個使用者裝置有它自己的複本，其中同樣地，只有本機使用者的識別碼為已知的參與者清單。
7. 當傳送訊息時，其`SenderIdentifier`也知道屬性是使用本機使用者。

寄件者識別碼可以用下列方式：

- 藉由在參與者清單中尋找擴充功能可以在交談中取得使用者的數目。
- 當使用者從延伸模組接收訊息時，它可以追蹤的傳送者識別項。 如果收到具有相同的傳送者識別項的另一個訊息，擴充功能就會知道它是來自相同的使用者。
- 它們可用來協助識別交談中的特定使用者。

傳送者識別項可以用於任何的文字欄位`MSMessageTemplateLayout`前置詞的方式以貨幣符號 (`$`)。 例如: 

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

若在 Messages 應用程式會顯示訊息泡泡，這種類型的格式，它將會取代`$uuid...`中傳送訊息之交談的參與者的連絡人名稱。

傳送者識別項是每個裝置上唯一的因此查看再次注意上, 圖，該使用者 1 的裝置和使用者 3 裝置交談中每個參與者都有不同的唯一寄件者識別碼。

寄件者識別項的訊息應用程式擴充功能的安裝範圍。 因此如果使用者解除安裝，並重新安裝訊息的應用程式擴充功能的新安裝將會產生新的寄件者識別項。

若要存取 寄件者識別碼，延伸模組可以使用下列程式碼：

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

訊息應用程式延伸模組所產生的互動式訊息會傳遞下列 Apple 平台上：

- watchOS 3
- macOS Sierra
- iOS 10

三個平台，iOS 10 可讓使用者產生的互動式訊息。 在 macOS Sierra，如果使用者按一下互動式的訊息泡泡圖中，URL 附加到`MSMessage`將會在 Safari 中開啟，而且應該那里顯示訊息的表示法。

按一下 watchOS 在 Messages 應用程式可以遞交至連接的 iOS 裝置使用者可以在其中撰寫回覆的互動式訊息。

如果較舊的 Apple 平台接收互動式訊息時，新的訊息 API 會有後援的支援：

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

它們會以兩個不同的訊息傳遞後援的格式：

- 其中一個會將映像所提供範本版面配置。
- 另將會是 URL 中提供`MSMessage`。

## <a name="summary"></a>總結

這篇文章說明在整合了 Xamarin.iOS 解決方案中使用的訊息應用程式擴充功能的進階的技巧**訊息**應用程式和使用者有新功能。


## <a name="related-links"></a>相關連結

- [冰淇淋產生器 （範例）](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [訊息參考](https://developer.apple.com/reference/messages)
- [應用程式擴充功能的程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
