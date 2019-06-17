---
title: 在 Xamarin.iOS 中的 web 檢視
description: 本文件說明 Xamarin.iOS 應用程式可以顯示的網頁內容的各種方式。 它討論 UIWebView、 WKWebView、 SFSafariViewController、 Safari 和應用程式的傳輸安全性。
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 9adf514e4fc510617e3f4d801569935ee4a03f25
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093766"
---
# <a name="web-views-in-xamarinios"></a>在 Xamarin.iOS 中的 web 檢視

IOS 的存留期 Apple 發行了數種方式來併入其應用程式中的 web 檢視功能的應用程式開發人員。 大部分的使用者利用他們的 iOS 裝置的內建 Safari 網頁瀏覽器，並因此預期有來自其他應用程式的網頁檢視功能是與這項體驗一致。 他們預期運作，必須在 par 和功能相同的效能相同的動作。

在本文中，我們將探討每個 Apple 提供的三個 Web 檢視： `UIWebView`， `WKWebview`，和`SFSafariViewController`、 其相似之處和差異，以及如何使用。 

iOS 11 推出新的變更，同時`WKWebView`和`SFSafariViewController`。 如需這些的詳細資訊，請參閱[Web iOS 11 指南中的變更](~/ios/platform/introduction-to-ios11/web.md)指南。

## <a name="uiwebview"></a>UIWebView

`UIWebView` 是 Apple 的傳統方式，提供您的應用程式中的 web 內容。 它 iOS 2.0 中，在發行之後淘汰 8.0。

如果您打算支援 iOS 版本早於 8.0，您必須使用`UIWebView`。 因為，`UIWebView`小於最適合用於效能比其他選擇，建議您應該檢查使用者的 iOS 版本。 如果它 8.0 或更新版本使用其中一個選項說明以下會建立較佳使用者體驗。
 
若要將 UIWebView 新增至 Xamarin.iOS 應用程式中，使用下列程式碼：
 
```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

這會產生下列 web 檢視：

[![](uiwebview-images/webview.png "ScalesPagesToFit 的效果")](uiwebview-images/webview.png#lightbox)

如需有關使用`UIWebView`，請參閱下列的訣竅：


- [載入網頁](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_a_web_page)
- [載入本機內容](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_local_content)
- [載入非 Web 文件](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_non-web_documents)

## <a name="wkwebview"></a>WKWebView

`WKWebView` iOS 8 好讓應用程式開發人員實作一個 web 瀏覽介面類似於行動裝置的 Safari 中引進。 這是因為，部分的事實，`WKWebView`使用 Nitro Javascript 引擎，相同的引擎供行動裝置的 Safari。 `WKWebView` 應該一律使用 UIWebView 是由於 over[增加效能](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview)，內建的使用者易記的筆勢，而且更便於網頁與應用程式之間的互動。
  
`WKWebView` 可以新增至您的應用程式 UIWebView，幾乎完全相同的方式但開發人員為您的 UI/UX 和功能的更多控制權。 建立和顯示的 web 檢視物件將會顯示要求的頁面上，不過您可以控制呈現檢視的方式、 使用者如何瀏覽，以及使用者檢視的結束方式。  

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

請務必注意`WKWebView`是 WebKit 命名空間中，因此您必須將此新增 using 指示詞類別的頂端。

`WKWebView` 也可用於 Xamarin.Mac 應用程式，並因此建議您考慮使用它，如果您要建立跨平台的 Mac/iOS 應用程式。

[處理 JavaScript 警示](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts)配方也提供有關使用使用 Javascript 的 WKWebView

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController
 
 `SFSafariViewController` 是提供從您的應用程式的 web 內容的最新方式，可在 iOS 9 及更新版本。 不同於`UIWebView`或是`WKWebView`，`SFSafariViewController`是檢視控制器，所以無法用來與其他檢視。 您應該呈現`SFSafariViewController`為新的檢視控制器，，您也會在相同的方式呈現任何檢視控制器。
 
 `SFSafariViewController` 是基本上 '迷你 safari'，您可以內嵌到您的應用程式。 例如 WKWebView 會使用相同的 Nitro Javascript 引擎，但也會提供一組額外的 Safari 功能，例如自動填滿、 讀取器和行動的 Safari 中分享 cookie 和資料的能力。 使用者之間的互動和`SFSafariViewController`不能存取您的應用程式。 您的應用程式不會有任何預設的 Safari 功能的存取權。
 
它也，根據預設，會實作**完成**按鈕，讓使用者輕鬆地返回您的應用程式，並轉送，且後 導覽按鈕，可讓您的使用者瀏覽一堆網頁。 此外，它也提供使用者的網址列讓它們不在預期的網頁上無憂無慮。 在網址列中不允許使用者變更 url。 

這些實作無法變更，因此`SFSafariViewController`非常適合用來作為預設瀏覽器，如果您的應用程式想要呈現的網頁，而不需要任何自訂。

下列程式碼可以用來啟動`SFSafariViewController`Xamarin.iOS 應用程式中：

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

這會產生下列 web 檢視：

[![](uiwebview-images/sfsafariviewcontroller.png "SFSafariViewController 範例 web 檢視")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

此外，也可以開啟 Safari 中行動應用程式在您的應用程式中，使用下列程式碼：

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

這會產生下列 web 檢視：

[![](uiwebview-images/safari.png "在 Safari 中顯示網頁")](uiwebview-images/safari.png#lightbox)

瀏覽使用者遠離您的應用程式，到 Safari 應該通常不見得能夠避免。 大部分的使用者不希望瀏覽您的應用程式之外，如果您離開您的應用程式時，使用者可能永遠不會傳回它，基本上終止 engagement。

iOS 9 的增強功能可讓使用者輕鬆地返回您的應用程式，透過所提供的 [Safari] 頁面的左上角的返回按鈕。

## <a name="app-transport-security"></a>應用程式傳輸安全性

應用程式的傳輸安全性，或是*ATS*中引進了 apple iOS 9，以確保所有的網際網路通訊符合最佳做法的安全連線。

如需有關 ATS 的詳細資訊，包括如何實作在您的應用程式，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)指南。

## <a name="related-links"></a>相關連結

- [Web 檢視 （範例）](https://developer.xamarin.com/samples/monotouch/WebView/)
