---
title: Xamarin 中的 Web Views
description: 本檔說明 Xamarin iOS 應用程式可以用來顯示 web 內容的各種方式。 其中討論 WKWebView、SFSafariViewController、Safari 和應用程式傳輸安全性。
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 8640800717a88e800503e93c339eeb080707374e
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915693"
---
# <a name="web-views-in-xamarinios"></a>Xamarin 中的 Web Views

在 iOS Apple 的存留期內，應用程式開發人員已發行數種方式，以在其應用程式中納入 web view 功能。 大部分的使用者在其 iOS 裝置上使用內建的 Safari web 瀏覽器，因此會預期來自其他應用程式的 web view 功能會與此體驗一致。 它們預期會有相同的手勢來運作、將效能視為同等，以及功能相同。

iOS 11 引進了 `WKWebView` 和 `SFSafariViewController`的新變更。 如需這些功能的詳細資訊，請參閱[iOS 11 中的 Web 變更指南](~/ios/platform/introduction-to-ios11/web.md)。

## <a name="wkwebview"></a>WKWebView

`WKWebView` 是在 iOS 8 中引進，可讓應用程式開發人員執行類似于行動 Safari 的 web 流覽介面。 這種情況的原因是，`WKWebView` 使用 Nitro JAVAscript 引擎，這是 mobile Safari 所使用的相同引擎。 `WKWebView` 應該一律在可能的情況下使用於 UIWebView，因為效能增加、內建的使用者易記手勢，以及網頁與應用程式之間的互動輕鬆。

`WKWebView` 可以使用幾乎完全相同的方式新增至您的應用程式，但身為開發人員，您對 UI/UX 和功能有更大的控制權。 建立和顯示 web view 物件將會顯示要求的頁面，不過您可以控制如何呈現視圖、使用者可以流覽的方式，以及使用者如何結束視圖。  

下列程式碼可用來啟動您的 Xamarin iOS 應用程式中的 `WKWebView`：

```csharp
WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
View.AddSubview(webView);

var url = new NSUrl("https://docs.microsoft.com");
var request = new NSUrlRequest(url);
webView.LoadRequest(request);
```

請務必注意，`WKWebView` 是在 `WebKit` 命名空間中，因此您必須將此 using 指示詞新增至類別的頂端。

`WKWebView` 也可以在 Xamarin. Mac 應用程式中使用，如果您要建立跨平臺的 Mac/iOS 應用程式，則應使用此功能。

「[處理 JAVAscript 警示](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts)」配方也會提供有關使用 WKWebView 搭配 JavaScript 的資訊。

## <a name="sfsafariviewcontroller"></a>SFSafariViewController

 `SFSafariViewController` 是從您的應用程式提供 web 內容，並可在 iOS 9 和更新版本中取得的最新方式。 不同于 `UIWebView` 或 `WKWebView`，`SFSafariViewController` 是視圖控制器，因此不能與其他視圖一起使用。 您應該以新的視圖控制器的方式呈現 `SFSafariViewController`，就像使用任何視圖控制器一樣。

 `SFSafariViewController` 基本上是「迷你 safari」，可以內嵌到您的應用程式中。 如同 WKWebView，它會使用相同的 Nitro JAVAscript 引擎，但也會提供一系列額外的 Safari 功能，例如自動填滿、讀者，以及與 mobile Safari 共用 cookie 和資料的能力。 使用者和 `SFSafariViewController` 之間的互動無法供您的應用程式存取。 您的應用程式將無法存取任何預設的 Safari 功能。

根據預設，它也會執行 [**完成**] 按鈕，讓使用者可以輕鬆地返回您的應用程式，以及向前和向後瀏覽按鈕，讓您的使用者可以流覽一堆網頁。 此外，它也會為使用者提供一個網址列，讓他們知道他們是在預期的網頁上。 網址列不允許使用者變更 url。

這些執行無法變更，因此，如果您的應用程式想要呈現不含任何自訂的網頁，則最好使用 `SFSafariViewController` 做為預設瀏覽器。

下列程式碼可用來啟動您的 Xamarin iOS 應用程式中的 `SFSafariViewController`：

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

這會產生下列 web 視圖：

[![使用 SFSafariViewController 的範例 web 視圖](webview-images/sfsafariviewcontroller.png)](webview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

您也可以使用下列程式碼，從您的應用程式內開啟 mobile Safari 應用程式：

```csharp
var url = new NSUrl("https://docs.microsoft.com");

UIApplication.SharedApplication.OpenUrl(url);
```

這會產生下列 web 視圖：

[![Safari 中顯示的網頁](webview-images/safari.png)](webview-images/safari.png#lightbox)

通常一律避免將使用者從您的應用程式移至 Safari。 大部分的使用者都不會預期流覽至您的應用程式之外，因此，如果您離開應用程式，使用者可能永遠不會傳回它，基本上是終止 engagement。

iOS 9 改良功能可讓使用者透過 Safari 頁面左上角提供的 [上一頁] 按鈕，輕鬆地返回您的應用程式。

## <a name="app-transport-security"></a>應用程式傳輸安全性

IOS 9 中的 Apple 引進了應用程式傳輸安全性或*ATS* ，以確保所有網際網路通訊都符合安全連線的最佳作法。

如需 ATS 的詳細資訊，包括如何在您的應用程式中加以執行，請參閱[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)指南。

## <a name="uiwebview-deprecated"></a>UIWebView （已被取代）

> [!IMPORTANT]
> `UIWebView` 已被取代。 在2020年4月之前，使用此控制項的應用程式將[不會被接受到 App Store，而現有的應用程式必須在2020年12月將其移除](https://developer.apple.com/news/?id=12232019b)。
>
> [Apple 的 `UIWebView` 檔](https://developer.apple.com/documentation/uikit/uiwebview)建議應用程式應該改用[`WKWebView`](#wkwebview) 。

> [!IMPORTANT]
> 如果您要在使用 Xamarin 時尋找 `UIWebView` 取代警告（ITMS-90809）的相關資源，請參閱[Xamarin web](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809)工作檔。

`UIWebView` 是 Apple 在您的應用程式中提供 web 內容的舊版方式。 它是在 iOS 2.0 中發行，並已淘汰8.0。

若要將 UIWebView 新增至您的 Xamarin iOS 應用程式，請使用下列程式碼：

```csharp
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://docs.microsoft.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

這會產生下列 web 視圖：

[![ScalesPagesToFit 的效果](webview-images/webview.png)](webview-images/webview.png#lightbox)

## <a name="related-links"></a>相關連結

- [網站（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)
