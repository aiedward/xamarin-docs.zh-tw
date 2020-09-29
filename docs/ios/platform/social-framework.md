---
title: Xamarin 中的社交架構
description: 社交架構提供整合的 API，可與社交網路（包括 Twitter 和 Facebook）互動，也提供適用于中國使用者的 SinaWeibo。
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 0c5f2a5c6a3274b298d3de216a2a0f42ed590611
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436480"
---
# <a name="social-framework-in-xamarinios"></a>Xamarin 中的社交架構

_社交架構提供整合的 API，可與社交網路（包括 Twitter 和 Facebook）互動，也提供適用于中國使用者的 SinaWeibo。_

使用社交架構可讓應用程式從單一 API 與社交網路互動，而不需要管理驗證。 它包含系統提供的 view controller 來撰寫貼文，以及允許透過 HTTP 取用每個社交網路 API 的抽象概念。

## <a name="connecting-to-twitter"></a>連接到 Twitter

### <a name="twitter-account-settings"></a>Twitter 帳戶設定

若要使用社交架構連接到 Twitter，必須在裝置設定中設定帳戶，如下所示：

 [![Twitter 帳戶設定](social-framework-images/twitter01.png)](social-framework-images/twitter01.png#lightbox)

一旦將帳戶輸入並使用 Twitter 驗證之後，使用社交架構類別來存取 Twitter 的裝置上的任何應用程式都會使用此帳戶。

### <a name="sending-tweets"></a>傳送推文

社交架構包含一個稱為的控制器 `SLComposeViewController` ，其會提供系統提供的視圖來編輯和傳送推文。 下列螢幕擷取畫面顯示此視圖的範例：

 [![此螢幕擷取畫面顯示 SLComposeViewController 的範例](social-framework-images/twitter02.png)](social-framework-images/twitter02.png#lightbox)

若要搭配 `SLComposeViewController` Twitter 使用，必須藉由呼叫方法來建立控制器的實例，如下 `FromService` `SLServiceType.Twitter` 所示：

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

`SLComposeViewController`傳回實例之後，就可以使用它來呈現要張貼到 Twitter 的 UI。 不過，第一件事就是檢查社交網路的可用性（在此案例中為 Twitter），方法是呼叫 `IsAvailable` ：

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController` 永遠不會直接傳送推文，而不需要使用者互動。 不過，您可以使用下列方法來初始化它：

- `SetInitialText` –新增要在推文中顯示的初始文字。
- `AddUrl` –將 Url 加入至推文。
- `AddImage` –將影像加入至推文。

初始化之後，呼叫會 `PresentVIewController` 顯示所建立的視圖 `SLComposeViewController` 。 然後，使用者可以選擇性地編輯和傳送推文，或取消傳送。 無論是哪一種情況，都應該在中關閉控制器 `CompletionHandler` ，也可以檢查結果以查看推文是否已傳送或取消，如下所示：

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>推文範例

下列程式碼示範如何使用 `SLComposeViewController` 來呈現用來傳送推文的視圖：

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

社交架構也包含對社交網路提出 HTTP 要求的支援。 它會將要求封裝在 `SLRequest` 用來鎖定特定社交網路 API 的類別中。

例如，下列程式碼會藉由擴充上述) 的程式碼，對 Twitter 提出要求以取得公用時間軸 (：

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

讓我們詳細看看此程式碼。 首先，它會取得帳戶存放區的存取權，並取得 Twitter 帳戶的類型：

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

接下來，如果您的應用程式可以存取 Twitter 帳戶，它會詢問使用者，如果授與存取權，則會將帳戶載入至記憶體，並更新 UI：

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

當使用者透過) UI 中的按鈕來要求時間軸資料 (時，應用程式會先形成存取 Twitter 資料的要求：

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```

此範例會藉由在 URL 中包含，將傳回的結果限制為最後十個專案 `?count=10` 。 最後，它會將要求附加至) 上載入的 Twitter 帳戶 (，並執行 Twitter 的呼叫來提取資料：

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

如果成功載入資料，將會顯示未經處理的 JSON 資料， (如下列範例輸出所示) ：

[![原始 JSON 資料顯示範例](social-framework-images/twitter03.png)](social-framework-images/twitter03.png#lightbox)

在實際的應用程式中，可以將 JSON 結果剖析為正常，並向使用者顯示結果。 如需如何剖析 JSON 的詳細資訊，請參閱 [簡介 Web 服務](~/cross-platform/data-cloud/web-services/index.md) 。

## <a name="connecting-to-facebook"></a>連接至 Facebook

### <a name="facebook-account-settings"></a>Facebook 帳戶設定

使用社交架構連接到 Facebook 幾乎與上述 Twitter 所用的程式相同。 您必須在裝置設定中設定 Facebook 使用者帳戶，如下所示：

[![Facebook 帳戶設定](social-framework-images/facebook01.png)](social-framework-images/facebook01.png#lightbox)

一旦設定之後，使用社交架構之裝置上的任何應用程式將會使用此帳戶來連線至 Facebook。

### <a name="posting-to-facebook"></a>張貼至 Facebook

由於社交架構是設計用來存取多個社交網路的統一 API，不論使用的社交網路為何，程式碼都會保持不變。

例如， `SLComposeViewController` 可以完全如同稍早所示的 Twitter 範例中使用，唯一的不同是切換至 Facebook 專屬的設定和選項。 例如：

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

與 Facebook 搭配使用時， `SLComposeViewController` 會顯示看起來幾乎與 Twitter 範例完全相同的視圖，在此案例中，將 **Facebook** 顯示為標題：

[![SLComposeViewController 顯示](social-framework-images/facebook02.png)](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>呼叫 Facebook 圖形 API

與 Twitter 範例類似，社交架構的 `SLRequest` 物件可與 Facebook 的圖形 API 搭配使用。 例如，下列程式碼會藉由擴充上述) 的程式碼，從圖形 API 傳回關於 Xamarin 帳戶 (的資訊：

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

這段程式碼與上述 Twitter 版本之間唯一的差異，是為了取得開發人員/應用程式專屬的識別碼 (您可以從 Facebook 的開發人員入口) 網站產生，而您也必須在提出要求時將其設定為選項：

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

無法設定此選項 (或使用不正確索引鍵) 會導致錯誤或未傳回任何資料。

## <a name="summary"></a>摘要

本文說明如何使用社交架構與 Twitter 和 Facebook 互動。 它會顯示在裝置設定中為每個社交網路設定帳戶的位置。 此外，也會討論如何使用 `SLComposeViewController` 來呈現將發佈到社交網路的統一觀點。 此外，它也會檢查 `SLRequest` 用來呼叫每個社交網路 API 的類別。

## <a name="related-links"></a>相關連結

- [SocialFrameworkDemo (範例) ](/samples/xamarin/ios-samples/socialframeworkdemo)
- [Web 服務簡介](~/cross-platform/data-cloud/web-services/index.md)