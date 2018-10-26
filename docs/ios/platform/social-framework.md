---
title: 在 Xamarin.iOS 中的社交架構
description: 社交架構會提供統一的 API 來與 Twitter 和 Facebook，以及 SinaWeibo 包括中國地區使用者的社交網路互動。
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 08ccd5b5ac78e82bf745764d70e59d2db9ec6776
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115701"
---
# <a name="social-framework-in-xamarinios"></a>在 Xamarin.iOS 中的社交架構

_社交架構會提供統一的 API 來與 Twitter 和 Facebook，以及 SinaWeibo 包括中國地區使用者的社交網路互動。_

使用社交架構可讓應用程式與社交網路單一 API 互動，而不需要管理驗證。 其中包括為撰寫文章，以及抽象層，可讓您透過 HTTP 使用每個社交網路的 API 提供檢視控制器的系統。

> [!IMPORTANT]
> 連接到各種社交網路的跨平台 API，請參閱 < [Xamarin.Social](http://components.xamarin.com/view/xamarin.social/) Xamarin 元件存放區中的元件。

## <a name="connecting-to-twitter"></a>連線到 Twitter

### <a name="twitter-account-settings"></a>Twitter 帳戶設定

若要連接到使用社交架構的 Twitter，帳戶必須設定裝置設定中，如下所示：

 [![](social-framework-images/twitter01.png "Twitter 帳戶設定")](social-framework-images/twitter01.png#lightbox)

一旦已輸入並確認與 Twitter 帳戶，用以存取 Twitter 的社交架構類別的裝置上的任何應用程式將使用此帳戶。

### <a name="sending-tweets"></a>傳送推文

社交架構包含呼叫的控制站`SLComposeViewController`其中會提供所提供的系統檢視表來編輯和傳送推文。 下列螢幕擷取畫面顯示此檢視的範例：

 [![](social-framework-images/twitter02.png "此螢幕擷取畫面顯示 SLComposeViewController 範例")](social-framework-images/twitter02.png#lightbox)

若要使用`SLComposeViewController`利用 Twitter 的控制器執行個體必須建立藉由呼叫`FromService`方法`SLServiceType.Twitter`，如下所示：

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

之後`SLComposeViewController`會傳回執行個體，它可以用來顯示 UI，張貼到 Twitter。 不過，進行第一件事是藉由呼叫在此情況下，檢查是否有社交網路，也就是 Twitter `IsAvailable`:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController` 永遠不會傳送推文直接而不需要使用者互動。 不過，它可以使用下列方法初始化：

-   `SetInitialText` -新增推文中顯示的初始文字。 
-  `AddUrl` -將 Url 新增至 推文。
-  `AddImage` -將映像將推文。


初始化之後，呼叫`PresentVIewController`會顯示所建立的檢視`SLComposeViewController`。 使用者可以再選擇性地編輯和傳送推文，或取消傳送它。 在任一情況下，應該關閉控制站在`CompletionHandler`，其中也會檢查結果以查看推文已傳送或已取消，如下所示：

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>推文範例

下列程式碼示範如何使用`SLComposeViewController`呈現檢視，用來傳送推文：

```csharp
using System;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _twitterComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
        #endregion

        #region Computed Properties
        public bool isTwitterAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Twitter); }
        }

        public SLComposeViewController TwitterComposer {
            get { return _twitterComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            SendTweet.Enabled = isTwitterAvailable;
        }
        #endregion

        #region Actions
        partial void SendTweet_TouchUpInside (UIButton sender)
        {
            // Set initial message
            TwitterComposer.SetInitialText ("Hello Twitter!");
            TwitterComposer.AddImage (UIImage.FromFile ("Icon.png"));
            TwitterComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (TwitterComposer, true, null);
        }
        #endregion
    }
}
```

### <a name="calling-twitter-api"></a>呼叫 Twitter API

社交架構也包含用於提出 HTTP 要求到社交網路的支援。 它會封裝中的要求`SLRequest`用來以特定的社交網路的 API 為目標的類別。

例如，下列程式碼會提出要求到 Twitter 即可取得公用時間軸 （藉由擴充上面提供的程式碼）：

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _twitterAccount;
#endregion

#region Computed Properties
public ACAccount TwitterAccount {
    get { return _twitterAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    SendTweet.Enabled = isTwitterAvailable;
    RequestTwitterTimeline.Enabled = false;

    // Initialize Twitter Account access 
    var accountStore = new ACAccountStore ();
    var accountType = accountStore.FindAccountType (ACAccountType.Twitter);

    // Request access to Twitter account
    accountStore.RequestAccess (accountType, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestTwitterTimeline.Enabled = true;
            });
        }
    });
}
#endregion

#region Actions
partial void RequestTwitterTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
    var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = TwitterAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

讓我們看看這個程式碼的詳細資料。 首先，它會取得存取權的帳戶存放區，並取得 Twitter 帳戶的類型：

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

接下來，它會要求使用者是否您的應用程式可以有自己的 Twitter 帳戶的存取權和帳戶授與存取權時，如果載入記憶體和更新的 UI:

```csharp
// Request access to Twitter account
accountStore.RequestAccess (accountType, (granted, error) => {
    // Allowed by user?
    if (granted) {
        // Get account
        _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
        InvokeOnMainThread (() => {
            // Update UI
            RequestTwitterTimeline.Enabled = true;
        });
    }
});
```

當使用者要求的時間軸資料 （藉由點選按鈕，以在 UI 中的） 時，應用程式會先 form 從 Twitter 存取資料的要求：

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```
此範例中會限制傳回的結果，最後十個項目包含`?count=10`在 URL 中。 最後，它將要求附加至 Twitter 帳戶 （也就載入上方），並執行呼叫 Twitter 提取資料：

```csharp
// Request data
request.Account = TwitterAccount;
request.PerformRequest ((data, response, error) => {
    // Was there an error?
    if (error == null) {
        // Was the request successful?
        if (response.StatusCode == 200) {
            // Yes, display it
            InvokeOnMainThread (() => {
                Results.Text = data.ToString ();
            });
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", response.StatusCode);
            });
        }
    } else {
        // No, display error
        InvokeOnMainThread (() => {
            Results.Text = string.Format ("Error: {0}", error);
        });
    }
});
```

如果已成功載入資料，就會顯示未經處理的 JSON 資料 （如下列範例輸出）：

[![](social-framework-images/twitter03.png "未經處理的 JSON 資料顯示的範例")](social-framework-images/twitter03.png#lightbox)

在實際的應用程式中，JSON 結果然後無法剖析為標準] 和 [向使用者顯示的結果。 請參閱[介紹 Web Service](~/cross-platform/data-cloud/web-services/index.md)如需如何剖析 JSON。

## <a name="connecting-to-facebook"></a>連接到 Facebook

### <a name="facebook-account-settings"></a>Facebook 帳戶設定

連接到 Facebook 社交架構是用於 Twitter 如上所示的程序幾乎相同。 Facebook 使用者帳戶必須設定裝置設定中，如下所示：

[![](social-framework-images/facebook01.png "Facebook 帳戶設定")](social-framework-images/facebook01.png#lightbox)

設定之後，請使用社交架構裝置上的任何應用程式會使用此帳戶來連接至 Facebook。

### <a name="posting-to-facebook"></a>張貼到 Facebook

社交架構為統一的 API 來存取多個社交網路設計，程式碼保持幾乎完全相同，不論所使用的社交網路。

比方說，`SLComposeViewController`可以用完全相同 Twitter 先前範例所示，唯一差別切換到 Facebook 特有的設定和選項。 例如: 

```csharp
using System;
using Foundation;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _facebookComposer = SLComposeViewController.FromService (SLServiceType.Facebook);
        #endregion

        #region Computed Properties
        public bool isFacebookAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Facebook); }
        }

        public SLComposeViewController FacebookComposer {
            get { return _facebookComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            PostToFacebook.Enabled = isFacebookAvailable;
        }
        #endregion

        #region Actions
        partial void PostToFacebook_TouchUpInside (UIButton sender)
        {
            // Set initial message
            FacebookComposer.SetInitialText ("Hello Facebook!");
            FacebookComposer.AddImage (UIImage.FromFile ("Icon.png"));
            FacebookComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (FacebookComposer, true, null);
        }
        #endregion
    }
}
```

與 Facebook 搭配使用時`SLComposeViewController`會顯示檢視看起來幾乎完全相同 Twitter 範例中，顯示**Facebook**為此案例中的項目：

[![](social-framework-images/facebook02.png "SLComposeViewController 顯示")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>呼叫 Facebook 圖形 API

類似於 Twitter 的範例，社交架構的`SLRequest`物件可與 Facebook 的 graph API。 比方說，下列程式碼會傳回資訊從 graph API 的 Xamarin 帳戶 （藉由擴充上面提供的程式碼）：

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _facebookAccount;
#endregion

#region Computed Properties
public ACAccount FacebookAccount {
    get { return _facebookAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    PostToFacebook.Enabled = isFacebookAvailable;
    RequestFacebookTimeline.Enabled = false;

    // Initialize Facebook Account access 
    var accountStore = new ACAccountStore ();
    var options = new AccountStoreOptions ();
    var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
    accountType = accountStore.FindAccountType (ACAccountType.Facebook);

    // Request access to Facebook account
    accountStore.RequestAccess (accountType, options, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _facebookAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestFacebookTimeline.Enabled = true;
            });
        }
    });

}
#endregion

#region Actions
partial void RequestFacebookTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl ("https://graph.facebook.com/283148898401104");
    var request = SLRequest.Create (SLServiceKind.Facebook, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = FacebookAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

此程式碼，以上所顯示的 Twitter 版本之間唯一的真正差異是 Facebook 的需求，以取得開發人員/應用程式特定識別碼 （您可以從 Facebook 的開發人員入口網站中產生） 提出要求時必須設定的選項：

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

若未設定此選項 （或使用無效的金鑰） 會導致錯誤或傳回任何資料。

## <a name="summary"></a>總結

本文說明如何使用社交架構與 Twitter 和 Facebook 互動。 它示範了如何設定裝置設定中的每個社交網路的帳戶。 它也討論如何使用`SLComposeViewController`呈現張貼到社交網路的統一的檢視。 此外，它會檢查`SLRequest`類別，用來呼叫每個社交網路的 API。


## <a name="related-links"></a>相關連結

- [SocialFrameworkDemo （範例）](https://developer.xamarin.com/samples/SocialFrameworkDemo/)
- [Web 服務簡介](~/cross-platform/data-cloud/web-services/index.md)
