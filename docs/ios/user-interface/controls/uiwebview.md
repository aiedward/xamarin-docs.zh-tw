---
title: "網頁檢視"
description: "釐清 iOS web 檢視選項"
ms.topic: article
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 70703797792a2f667a2eb20cbc45d1736e5e6b9d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="web-views"></a>網頁檢視

IOS 的存留期 Apple 已發行數種方式併入 web 應用程式中的檢視功能的應用程式開發人員。 大部分的使用者利用內建的 Safari 網頁瀏覽器在 iOS 裝置上，所以預期會從其他應用程式的網頁檢視功能是與這項體驗一致。 他們期望應用程式相同的動作，才能執行，要在長條上方，以及的功能相同的效能。

在本文中，我們將探討每個由 Apple 提供的三個 Web 檢視： `UIWebView`， `WKWebview`，和`SFSafariViewController`、 其相似性和差異，以及如何使用。 

iOS 11 引進新的變更同時`WKWebView`和`SFSafariViewController`。 如需這些的詳細資訊，請參閱[Web iOS 11 指南中的變更](~/ios/platform/introduction-to-ios11/web.md)指南。

## <a name="uiwebview"></a>UIWebView

`UIWebView` 是提供 web 內容在您的應用程式中的 Apple 的傳統方式。 它 iOS 2.0 中，在發行並已被取代為準，8.0。

如果您打算支援 iOS 版本早於 8.0，您必須使用`UIWebView`。 因為用`UIWebView`小於最適合用於效能比其他選擇，建議您應該檢查使用者的 iOS 版本。 如果它 8.0 或更新版本使用的選項之一說明下方將會建立較佳使用者體驗。
 
若要新增 UIWebView Xamarin.iOS 應用程式，請使用下列程式碼：
 
```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

這會產生下列 web 檢視：

[![](uiwebview-images/webview.png "ScalesPagesToFit 效果")](uiwebview-images/webview.png#lightbox)

如需有關使用`UIWebView`，請參閱下列的配方：


- [載入 Web 網頁](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_a_web_page/)
- [本機內容載入](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_local_content/)
- [載入非 Web 文件](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_non-web_documents/)

## <a name="wkwebview"></a>WKWebView

`WKWebView` iOS 8 允許應用程式開發人員實作一個 web 瀏覽介面類似於行動 Safari 中引進了。 這是因為，部分的事實，`WKWebView`使用 Nitro Javascript 引擎，相同的引擎供行動 Safari。 `WKWebView` 您應該永遠使用的 over UIWebView 有可能因為將[的效能提高](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview)，內建的使用者易記的筆勢及網頁和您的應用程式之間互動的簡化。
  
`WKWebView` 可以加入您的應用程式幾乎完全相同方式來 UIWebView，但開發人員為您的 UI/UX 和功能的更多控制權。 不過，您可以將檢視呈現的方式、 使用者如何巡覽，以及如何在使用者結束檢視控制建立和顯示 web 檢視物件中會顯示要求的頁面。  

下列程式碼可以用來啟動`WKWebView`Xamarin.iOS 應用程式中：

```csharp
    WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
    View.AddSubview(webView);

    var url = new NSUrl("https://xamarin.com");
    var request = new NSUrlRequest(url);
    webView.LoadRequest(request);
```

這會產生下列 web 檢視：

[![](uiwebview-images/wkwebview.png "未提供 ScalesPagesToFit 範例 web 檢視")](uiwebview-images/wkwebview.png#lightbox)

請務必注意`WKWebView`是 WebKit 命名空間中，因此您必須加入下列 using 指示詞加入您的類別的頂端。

`WKWebView` 也可用於 Xamarin.Mac 應用程式，並因此可能會想要考慮使用它，如果您要建立跨平台的 Mac/iOS 應用程式。

[處理 JavaScript 警示](https://developer.xamarin.com/recipes/ios/content_controls/web_view/handle_javascript_alerts/)配方也會提供資訊在 Javascript 中使用 WKWebView

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController
 
 `SFSafariViewController` 是提供從您的應用程式的 web 內容的最新的方式，可在 iOS 9 及更新版本。 不同於`UIWebView`或`WKWebView`，`SFSafariViewController`檢視控制站，所以無法用來與其他檢視。 您應該呈現`SFSafariViewController`做為新檢視控制站，您也會在相同的方式呈現檢視的任何控制站。
 
 `SFSafariViewController` 是基本上 '迷你 safari'，您可以內嵌到應用程式。 像 WKWebView 其相同的 Nitro Javascript 引擎，但也提供了各式各樣的其他 Safari 功能，例如自動填滿、 讀取器和行動 Safari 的共用 cookie 和資料的能力。 使用者之間的互動和`SFSafariViewController`都無法存取您的應用程式。 您的應用程式不會有任何預設 Safari 功能的存取權。
 
它也，根據預設，會實作**完成**按鈕，讓使用者輕鬆地返回您的應用程式，而且轉寄然後再瀏覽按鈕，可讓您的使用者瀏覽網頁的堆疊。 此外，它也提供使用者使用網址列給予他們安心上預期的網頁。 網址列中不允許使用者變更 url。 

這些實作無法變更，因此`SFSafariViewController`非常適合用來作為預設瀏覽器，如果您的應用程式想要呈現沒有任何自訂的網頁。

下列程式碼可以用來啟動`SFSafariViewController`Xamarin.iOS 應用程式中：

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

這會產生下列 web 檢視：

[![](uiwebview-images/sfsafariviewcontroller.png "SFSafariViewController 範例 web 檢視")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

此外，也可以開啟行動 Safari 應用程式從應用程式中的使用下列程式碼：

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

這會產生下列 web 檢視：

[![](uiwebview-images/safari.png "在 Safari 中顯示網頁")](uiwebview-images/safari.png#lightbox)

瀏覽使用者遠離您的應用程式到 Safari 通常應該一律避免。 大部分的使用者將不預期瀏覽您的應用程式之外，如果您離開您的應用程式時，使用者可能永遠不會傳回，基本上終止 engagement。

iOS 9 的增強功能讓使用者輕鬆地返回您的應用程式，透過所提供的左上角的 [Safari] 頁面的上一頁按鈕。

## <a name="app-transport-security"></a>應用程式的傳輸安全性

應用程式的傳輸安全性，或*AT*引進 apple 在 iOS 9，請確定所有的網際網路通訊符合最佳做法的安全連線。

如需有關 AT 的詳細資訊，包括如何實作在應用程式，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)指南。

## <a name="related-links"></a>相關連結

- [網頁檢視 （範例）](https://developer.xamarin.com/samples/monotouch/WebView/)
