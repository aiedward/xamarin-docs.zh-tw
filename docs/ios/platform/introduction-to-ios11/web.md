---
title: WebKit 和 Safari iOS 11 中的變更
description: 本文件討論 WebKit 和 iOS 11 中的 Safari 服務架構所做的變更。 它說明如何使用 SFSafariViewController 中的更新和新功能 WKWebView 設定樣式。
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/12/2017
ms.openlocfilehash: 5ced73b1f3f5b8207ae1258dcb01a78c94df217d
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268926"
---
# <a name="webkit-and-safari-changes-in-ios-11"></a>WebKit 和 Safari iOS 11 中的變更

iOS 11 推出新版本的 Safari 網頁瀏覽器 – Safari 11.0 – 包括 WebKit 和 SafariServices 的變更。 本指南將探索這些變更。

## <a name="safariservices"></a>SafariServices

`SFSafariViewController` 我們在 iOS 9 中引進作為顯示 web 內容或驗證使用者，從您的應用程式的選項。 其功能的詳細資訊可在[Web 檢視](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller)指南。

iOS 11 推出樣式更新到 Safari 檢視控制器，為使用者提供應用程式與 web 之間更順暢的體驗。 例如，在網址列提供 Safari 檢視控制器中的應用程式瀏覽器，而不是小型瀏覽器的風格的移除。 您也可以自訂色彩配置以符合您的應用程式的色彩配置設定`preferredBarTintColor`和`PreferredControlTintColor`屬性：

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

下列程式碼片段會轉譯在紫色和白色橫條，如下圖所示：

![SFSafariViewController 橫條以紫色和白色轉譯](web-images/image1.png)

Safari 檢視控制器中的 [解除] 按鈕也可以藉由設定來變更`DismissButtonStyle`屬性設為`Done`， `Close`，或`Cancel`:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![關閉按鈕文字已變更](web-images/image2.png)

您可以變更此值時`SFSafariViewController`呈現。


根據 Safari 檢視控制器內的顯示內容，可能必須確保功能表列不摺疊為使用者捲動。 啟用此選項設定的新`BarCollapsedEnabled`屬性設`false`:

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![列摺疊 停用](web-images/image3.png)

Apple iOS 11 中的 [Safari] 檢視控制器的隱私權也將更新。 現在，瀏覽資料，例如 cookie 和本機存放區只存在於每個應用程式為基礎，而不是 Safari 檢視控制器的所有執行個體。 如此可讓使用者瀏覽活動的應用程式內的私用。

其他功能這類拖放支援 Url 並支援`window.open()`也已新增至`SFSafariViewController`iOS 11 中。 您可以找到關於這些新功能的詳細資訊[Apple SFSafariViewController 文件](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor)。


## <a name="webkit"></a>WebKit

`WKWebView` 引進了 iOS 8 做為顯示 web 內容至您的使用者中 WebKit 的一部分。 它是更多自訂性比`SFSafariViewController`，讓您能夠建立您自己的巡覽和使用者介面。

Apple 已引進三個主要改進`WKWebView`ios 11: 

- 能夠管理 cookie
- 內容篩選
- 自訂資源載入。 

Cookie 管理工作是透過新[ `WKHttpCookieStore` ](https://developer.apple.com/documentation/webkit/wkhttpcookiestore)類別，可讓您新增及刪除 cookie，以取得儲存在 WKWebView 的所有 cookie，並觀察變更儲存在 cookie。

內容篩選可讓您管理您的使用者所見的內容類型可讓您確定它是安全、 系列的用途，並視需要只可選取的使用者群組。 這透過新實作[ `WKContentRuleList` ](https://developer.apple.com/documentation/webkit/wkcontentrulelist)類別，藉由提供觸發程序和動作，在 JSON 中的組。 這些觸發程序和動作的更多有關可在 Apple[內容的封鎖規則](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html)指南。

iOS 11 現在可讓您自訂`WKWebView`與載入您的網站內容的自訂資源。 這透過實作`IWKUrlSchemeHandler`介面，可讓您處理不是 Web 組件的原生的 URL 配置。 這個介面具有啟動和停止的方法必須實作：

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

一旦已實作的處理常式，使用它來設定`SetUrlSchemeHandler`屬性上的`WKWebViewConfiguration`。 然後，載入的項目，使用自訂配置的 URL:

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```

