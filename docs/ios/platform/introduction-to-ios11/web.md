---
title: 在 iOS 11 中變更 web
description: 本文件討論 WebKit 及 iOS 11 的 Safari Services 架構所做的變更。 它說明如何使用樣式 SFSafariViewController 中的更新和 WKWebView 中的新功能。
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/12/2016
ms.openlocfilehash: f5876a9d201950ebac45e8b1f786b0e97452a7f1
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787444"
---
# <a name="web-changes-in-ios-11"></a>在 iOS 11 中變更 web

iOS 11 導入了新版本的 Safari 網頁瀏覽器 – Safari 11.0 – 包括 WebKit 和 SafariServices 的變更。 本指南會探討這些變更。

## <a name="safariservices"></a>SafariServices

`SFSafariViewController` 中引進了 iOS 9 來顯示 web 內容或驗證使用者，從您的應用程式的選項。 其功能的詳細資訊位於[Web 檢視](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller)指南。

iOS 11 介紹了樣式更新 Safari 檢視控制站，為使用者提供應用程式和網站之間更順暢的體驗。 例如，在網址列現在可讓應用程式內瀏覽器，而不是迷你瀏覽器的風格的 Safari 檢視控制器的移除。 您也可以自訂色彩配置以符合您的應用程式的色彩配置藉由設定`preferredBarTintColor`和`PreferredControlTintColor`屬性：

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

下圖所示，下列程式碼片段會轉譯紫色和白色中的長條：

![以紫色和白色轉譯 SFSafariViewController 列](web-images/image1.png)

Safari 檢視控制器中的 [解除] 按鈕也可以藉由設定變更`DismissButtonStyle`屬性設為`Done`， `Close`，或`Cancel`:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![關閉按鈕文字已變更](web-images/image2.png)

可以變更此值時`SFSafariViewController`呈現。


根據顯示在 Safari 檢視控制器內容，可能需要確保功能表列不摺疊捲動。 啟用此選項設定新`BarCollapsedEnabled`屬性`false`:

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![列摺疊停用](web-images/image3.png)

Apple 也已對更新在 iOS 11 Safari 檢視控制器中的隱私權。 現在，瀏覽資料，例如 cookie 和本機儲存體只存在於每個應用程式為基礎，而不是在 Safari 檢視控制站的所有執行個體。 如此可讓使用者瀏覽活動的私用應用程式內。

其他功能如拖曳和卸除支援 Url 並支援`window.open()`另加入`SFSafariViewController`iOS 11 中。 您可以找到這些新功能的詳細資訊[Apple SFSafariViewController 文件](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor)。


## <a name="webkit"></a>WebKit

`WKWebView` 引進的 WebKit iOS 8 做為方法的對使用者顯示 web 內容中的一部分。 它是比更可自訂`SFSafariViewController`，可讓您建立您自己的巡覽和使用者介面。

Apple 已導入三個主要改進`WKWebView`使用 iOS 11: 

- 管理 cookie 的能力
- 內容篩選
- 自訂資源載入。 

Cookie 管理都會透過新[ `WKHttpCookieStore` ](https://developer.apple.com/documentation/webkit/wkhttpcookiestore)類別，可讓您加入和刪除 cookie，取得儲存在 WKWebView 中的所有 cookie，並觀察變更儲存在 cookie。

內容篩選可讓您管理您的使用者會看到的內容類型可讓您進行確認該檔案是安全、 系列的方便，且如有需要，僅供選取的使用者群組。 這透過新實作[ `WKContentRuleList` ](https://developer.apple.com/documentation/webkit/wkcontentrulelist)類別，藉由提供成對的觸發程序和 JSON 中的動作。 這些觸發程序和動作的詳細資訊位於 Apple[內容封鎖規則](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html)指南。

iOS 11 現在可讓您自訂`WKWebView`與載入您的網站內容的自訂資源。 這透過實作`IWKUrlSchemeHandler`介面，可讓您處理不是原生 Web 組件的 URL 結構描述。 這個介面具有啟動和停止的方法必須實作：

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

一旦已實作的處理常式，將它用於設定`SetUrlSchemeHandler`屬性`WKWebViewConfiguration`。 然後，負載平衡器使用自訂配置的 URL:

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```

