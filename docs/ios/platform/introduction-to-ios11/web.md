---
title: IOS 11 中的 WebKit 和 Safari 變更
description: 本檔討論在 iOS 11 中對 WebKit 和 Safari 服務架構所做的變更。 本文說明如何使用 SFSafariViewController 中的樣式更新和 WKWebView 中的新功能。
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/12/2017
ms.openlocfilehash: ef9577aad756ae67ac9fed685d7e40faea33c316
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032057"
---
# <a name="webkit-and-safari-changes-in-ios-11"></a>IOS 11 中的 WebKit 和 Safari 變更

iOS 11 引進了新版本的 Safari web 瀏覽器– Safari 11.0 –其中包括對 WebKit 和 SafariServices 的變更。 本指南會探索這些變更。

## <a name="safariservices"></a>SafariServices

`SFSafariViewController` 是在 iOS 9 中引進，做為顯示 web 內容或從您的應用程式驗證使用者的選項。 如需其功能的詳細資訊，請參閱[Web Views](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller) guide。

iOS 11 引進了 Safari View Controller 的樣式更新，讓您的使用者在應用程式與 web 之間獲得更順暢的體驗。 例如，移除網址列現在會讓 Safari 視圖控制器感覺應用程式內瀏覽器，而不是迷你瀏覽器。 您也可以藉由設定 [`preferredBarTintColor`] 和 [`PreferredControlTintColor`] 屬性，自訂色彩配置以配合應用程式的色彩配置：

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

下列程式碼片段會以紫色和白色呈現橫條，如下圖所示：

![以紫色和白色呈現的 SFSafariViewController 橫條](web-images/image1.png)

您也可以將 [`DismissButtonStyle`] 屬性設定為 [`Done`]、[`Close`] 或 [`Cancel`]，以變更 Safari View Controller 中顯示的 [關閉] 按鈕：

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![關閉按鈕文字已變更](web-images/image2.png)

當 `SFSafariViewController` 呈現時，可以變更這個值。

視 Safari 視圖控制器內顯示的內容而定，可能需要確保功能表列不會在使用者滾動時折迭。 若要啟用此功能，請將新的 `BarCollapsedEnabled` 屬性設定為 `false`：

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![已停用橫條折迭](web-images/image3.png)

Apple 也已在 iOS 11 的 Safari 視圖控制器中，對隱私權進行了更新。 現在，流覽 cookie 和本機儲存體之類的資料只會以個別應用程式為基礎，而不是在 Safari view controller 的所有實例上。 這會讓使用者流覽活動在您的應用程式中保持私用。

其他功能（例如，Url 的拖放支援和 `window.open()` 的支援）也已新增至 iOS 11 中的 `SFSafariViewController`。 您可以在[Apple 的 SFSafariViewController 檔](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor)中找到這些新功能的詳細資訊。

## <a name="webkit"></a>WebKit

`WKWebView` 是在 iOS 8 中引進為 WebKit 的一部分，做為將 web 內容顯示給使用者的方法。 它比 `SFSafariViewController`更容易自訂，可讓您建立自己的導覽和使用者介面。

Apple 在 iOS 11 中引進了三項 `WKWebView` 的主要改良功能： 

- 管理 cookie 的能力
- 內容篩選
- 自訂資源載入。 

Cookie 管理是透過新的[`WKHttpCookieStore`](https://developer.apple.com/documentation/webkit/wkhttpcookiestore)類別來完成，它可讓您新增和刪除 cookie、取得儲存在 WKWebView 中的所有 cookie，以及觀察 cookie 存放區的變更。

內容篩選可讓您管理使用者會看到的內容類型，讓您可以確定它是安全的、可供家庭使用，而且如有必要，只會提供給選取的使用者群組。 這會透過新的[`WKContentRuleList`](https://developer.apple.com/documentation/webkit/wkcontentrulelist)類別來執行，方法是在 JSON 中提供配對的觸發程式和動作。 如需這些觸發程式和動作的詳細資訊，請參閱 Apple 的[內容封鎖規則](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html)指南。

iOS 11 現在可讓您使用 web 內容的自訂資源載入，自訂 `WKWebView`。 這會透過 `IWKUrlSchemeHandler` 介面來執行，這可讓您處理不是 Web 套件原生的 URL 配置。 此介面具有必須執行的啟動和停止方法：

```csharp
public class MyHandler : NSObject, IWKUrlSchemeHandler {

    [Export("webView:startURLSchemeTask:")]
    public void StartUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        
        // Implement a IWKUrlSchemeTask here
        var response = new NSUrlResponse(urlSchemeTask.Request.Url, "text/html", ContentLength, null);
        urlSchemeTask.DidReceiveResponse(response);
        urlSchemeTask.DidReceiveData(someData);
        urlSchemeTask.DidFinish();
    }

    [Export("webView:stopURLSchemeTask:")]
    public void StopUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        throw new NotImplementedException();
    }

}
``` 

一旦處理常式完成之後，請使用它來設定 `WKWebViewConfiguration`上的 `SetUrlSchemeHandler` 屬性。 然後，載入使用自訂配置之內容的 URL：

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```
