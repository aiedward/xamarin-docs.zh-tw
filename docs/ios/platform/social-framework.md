---
title: "社交架構"
description: "社交 Framework 會提供統一的 API，來與社交網路包括 Twitter 與 Facebook、 以及 SinaWeibo 中國中的使用者互動。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 9892577d7e0ed3d3f622f881cc51db09eb44a8fd
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="social-framework"></a>社交架構

_社交 Framework 會提供統一的 API，來與社交網路包括 Twitter 與 Facebook、 以及 SinaWeibo 中國中的使用者互動。_


使用社交架構可讓應用程式與社交網路從單一的應用程式開發介面互動，而不需要管理驗證。 其中包括為撰寫文章，以及可讓取用每一個社交網路應用程式開發介面透過 HTTP 的抽象提供檢視控制器的系統。

> [!IMPORTANT]
> **注意：**連線到不同的社交網路的跨平台 API，請參閱[Xamarin.Social](http://components.xamarin.com/view/xamarin.social/) Xamarin 元件存放區中的元件。

## <a name="connecting-to-twitter"></a>連線到 Twitter

### <a name="twitter-account-settings"></a>Twitter 帳戶設定

若要連線到 Twitter 使用社交架構，帳戶必須設定裝置設定中，如下所示：

 [![](social-framework-images/twitter01.png "Twitter 帳戶設定")](social-framework-images/twitter01.png#lightbox)

一旦已輸入並確認與 Twitter 帳戶，用以存取 Twitter 社交架構類別的裝置上的任何應用程式會使用此帳戶。

### <a name="sending-tweets"></a>傳送推文

社交 Framework 包含多個呼叫的控制站`SLComposeViewController`呈現來編輯及傳送推文中提供的系統檢視表。 下列螢幕擷取畫面顯示此檢視的範例：

 [![](social-framework-images/twitter02.png "這個螢幕擷取畫面顯示 SLComposeViewController 範例")](social-framework-images/twitter02.png#lightbox)

若要使用`SLComposeViewController`與 Twitter，必須建立控制器執行個體藉由呼叫`FromService`方法`SLServiceType.Twitter`如下所示：

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

之後`SLComposeViewController`會傳回執行個體，它可以用來呈現張貼到 Twitter 的 UI。 不過，進行第一件事是藉由呼叫在此情況下，檢查的社交網路 Twitter 可用性`IsAvailable`:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController` 絕對不會傳送推文中的直接沒有使用者互動。 不過，它可以使用下列方法初始化：

-   `SetInitialText` – 加入推文中顯示的初始文字。 
-  `AddUrl` -將 Url 加入推文。
-  `AddImage` – 將影像新增到推文。


初始化之後呼叫`PresentVIewController`所建立的檢視會顯示`SLComposeViewController`。 使用者可以再選擇性地編輯及傳送推文中，或取消傳送它。 在任一情況下，應關閉控制器中`CompletionHandler`，其中也會檢查結果以查看 tweet 已傳送或已取消，如下所示：

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>推文範例

下列程式碼示範如何使用`SLComposeViewController`來呈現檢視，用來傳送推文：

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

社交 Framework 也支援以社交網路提出 HTTP 要求。 會將封裝中的要求`SLRequest`用於目標特定的社交網路的應用程式開發介面的類別。

例如，下列程式碼就會要求 Twitter 取得公用時間表 （方法是展開上述的程式碼上）：

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

讓我們看看這個程式碼的詳細資料。 首先，它會取得存取權的帳戶存放區，並取得 Twitter 帳戶類型：

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

接下來，它會要求使用者是否您的應用程式可以存取其 Twitter 帳戶和帳戶授與存取權時，如果載入記憶體和更新的 UI:

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

當使用者要求的時間軸資料 （藉由點選 UI 中的按鈕） 時，應用程式第一次 form 從 Twitter 存取資料的要求：

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```
這個範例會藉由限制傳回的結果，最後十個項目`?count=10`在 URL 中。 最後，它將要求附加的 Twitter 帳戶 （已載入上面），並執行 Twitter 呼叫以擷取資料：

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

如果成功載入的資料，就會顯示未經處理的 JSON 資料 （如同在以下的範例輸出）：

[![](social-framework-images/twitter03.png "未經處理的 JSON 資料顯示的範例")](social-framework-images/twitter03.png#lightbox)

實際的應用程式，在 JSON 結果會無法剖析做為正常並向使用者顯示的結果。 請參閱[簡介 Web 服務](~/cross-platform/data-cloud/web-services/index.md)如需有關如何將 JSON 剖析資訊。

## <a name="connecting-to-facebook"></a>連接到 Facebook

### <a name="facebook-account-settings"></a>Facebook 帳戶設定

連接到 Facebook 社交架構是 Twitter 如上所示的處理序幾乎完全相同的。 Facebook 使用者帳戶必須設定裝置設定中，如下所示：

[![](social-framework-images/facebook01.png "Facebook 帳戶設定")](social-framework-images/facebook01.png#lightbox)

一旦設定之後，會使用社交架構，在裝置上的任何應用程式會使用此帳戶連接至 Facebook。

### <a name="posting-to-facebook"></a>張貼到 Facebook

因為共享架構是設計來存取多個的社交網路這個統一的 API，程式碼保持幾乎完全相同，不論所使用的社交網路。

例如，`SLComposeViewController`可用 Twitter 先前範例所示，只有不同切換 Facebook 專屬的設定和選項的完全相同。 例如: 

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

當搭配 Facebook、`SLComposeViewController`顯示看起來幾乎完全相同 Twitter 範例中，顯示的檢視**Facebook**為標題在此情況下：

[![](social-framework-images/facebook02.png "SLComposeViewController 顯示")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>呼叫 Facebook 圖形 API

類似於 Twitter 範例，共享架構的`SLRequest`物件可以搭配 Facebook 的 graph API。 例如，下列程式碼從傳回資訊 Xamarin 帳戶有關在 graph API （展開上述的程式碼上）：

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

此程式碼，上述的 Twitter 版本之間的唯一實際差異為 Facebook 的需求，若要取得開發人員/應用程式特定識別碼 （您可以從 Facebook 的開發人員入口網站中產生） 提出要求時必須設定為一個選項：

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

若要設定此選項 （或使用無效的索引鍵） 的失敗會導致錯誤或傳回任何資料。

## <a name="summary"></a>總結

本文示範了如何使用社交的架構來與 Twitter 與 Facebook 進行互動。 它示範如何設定裝置設定中的每個社交網路的帳戶。 它也將討論如何使用`SLComposeViewController`呈現公佈到社交網路的統一的檢視。 此外，它會檢查`SLRequest`用來呼叫每個社交網路應用程式開發介面的類別。


## <a name="related-links"></a>相關連結

- [SocialFrameworkDemo (sample)](https://developer.xamarin.com/samples/SocialFrameworkDemo/)
- [Web 服務簡介](~/cross-platform/data-cloud/web-services/index.md)
