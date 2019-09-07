---
title: 在 Xamarin 中使用 Web 標記進行搜尋
description: 本檔說明如何建立連結回到 Xamarin iOS 應用程式的 web 型搜尋結果。 其中討論如何啟用 web 內容編制索引，讓應用程式的網站可供探索、使用智慧型應用程式橫幅、通用連結等等。
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/20/2017
ms.openlocfilehash: 52da0cfcab56c0acd339f4f0a0f2456a66d002a8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769484"
---
# <a name="search-with-web-markup-in-xamarinios"></a>在 Xamarin 中使用 Web 標記進行搜尋

對於透過網站（而不只是從應用程式）存取其內容的應用程式，web 內容可以使用 Apple 進行編目的特殊連結加以標記，並在使用者的 iOS 9 裝置上提供應用程式的深層連結。

如果您的 iOS 應用程式已支援 mobile 深層連結，而您的網站呈現您應用程式中內容的深層連結，Apple 的_Applebot_ web 編目程式會為此內容編制索引，並自動將其新增至其雲端索引：

[![](web-markup-images/webmarkup01.png "雲端索引總覽")](web-markup-images/webmarkup01.png#lightbox)

Apple 會在焦點搜尋和 Safari 搜尋結果中呈現這些結果。
如果使用者按其中一個結果（並已安裝您的應用程式），則會將其移至應用程式中的內容：

[![](web-markup-images/webmarkup02.png "在搜尋結果中從網站深層連結")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>啟用 Web 內容編制索引

使用 Web 標記，讓您的應用程式內容可供搜尋，需要四個步驟：

1. 藉由在 iTunes Connect 中將其定義為**支援**或**行銷**網站，確保 Apple 能夠探索並編制應用程式網站的索引。
2. 請確定您應用程式的網站包含執行行動深層連結所需的標記。 如需詳細資訊，請參閱下列各節。
3. 在您的 iOS 應用程式中啟用深層連結處理。
4. 為應用程式網站呈現的結構化資料新增標記，以提供豐富且吸引人的結果給終端使用者。 雖然此步驟不是絕對必要，但強烈建議使用 Apple。

下列各節將詳細說明這些步驟。

## <a name="make-your-apps-website-discoverable"></a>讓應用程式的網站可供探索

讓 Apple 尋找應用程式網站的最簡單方式，就是在您透過 iTunes Connect 將應用程式提交至 Apple 時，使用它做為**支援**或**行銷**網站。

## <a name="using-smart-app-banners"></a>使用智慧型應用程式橫幅

在您的網站上提供智慧型應用程式橫幅，以在應用程式中呈現清楚的連結。 如果尚未安裝應用程式，Safari 會自動提示使用者安裝您的應用程式。 否則，您可以使用 [ **view** ] 連結，從網站啟動您的應用程式。 例如，若要建立智慧型應用程式橫幅，您可以使用下列程式碼：

```html
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

如需詳細資訊，請參閱 Apple[使用智慧型應用程式橫幅推廣應用程式](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html)檔。

## <a name="using-universal-links"></a>使用通用連結

適用于 iOS 9 的新連結：提供下列各項，為智慧型應用程式橫幅或現有的自訂 URL 配置提供更好的替代方案。

- **唯一**-不能有多個網站宣告相同的 URL。
- **安全**–網站需要已簽署的憑證，確保網站擁有，且有效地連結至您的應用程式。
- **彈性**–終端使用者可以控制 URL 是否啟動網站或應用程式。
- **通用**–相同的 URL 可以用來定義您的網站和應用程式的內容。

## <a name="using-twitter-cards"></a>使用 Twitter 卡

您可以使用 Twitter 卡來提供應用程式內容的深層連結。 例如：

```html
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

如需詳細資訊，請參閱 Twitter 的[Twitter 卡通訊協定](http://dev.twitter.com/cards/mobile)檔。

## <a name="using-facebook-app-links"></a>使用 Facebook 應用程式連結

您可以使用 Facebook 應用程式連結，提供應用程式內容的深層連結。 例如：

```html
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

如需詳細資訊，請參閱 Facebook 的[應用程式連結](http://applinks.org)檔。

## <a name="opening-deep-links"></a>開啟深層連結

您需要新增在您的 Xamarin iOS 應用程式中開啟和顯示深層連結的支援。 編輯**AppDelegate.cs**檔案，並覆寫`OpenURL`方法以處理自訂 URL 格式。 例如：

```csharp
public override bool OpenUrl (UIApplication application, NSUrl url, string sourceApplication, NSObject annotation)
{

  // Handling a URL in the form http://company.com/appname/?123
  try {
    var components = new NSUrlComponents(url,true);
    var path = components.Path;
    var query = components.Query;

    // Is this a known format?
    if (path == "/appname") {
      // Display the view controller for the content
      // specified in query (123)
      return ContentViewController.LoadContent(query);
    }
  } catch {
    // Ignore issue for now
  }

  return false;
}
```

在上述程式碼中，我們要尋找包含`/appname`的 URL，並將的`query`值（`123`在此範例中為）傳遞給應用程式中的自訂視圖控制器，以向使用者顯示要求的內容。

## <a name="providing-rich-results-with-structured-data"></a>以結構化資料提供豐富的結果

藉由包含結構化資料標記，您可以為使用者提供豐富的搜尋結果，而不只是標題和描述。 使用結構化資料標記，將影像、應用程式特定資料（例如評等）和動作包含在結果中。

豐富的結果會更具吸引力，並藉由吸引更多使用者與他們互動，協助改善您在雲端式搜尋索引中的排名。

提供結構化資料標記的其中一個選項是使用 [開啟圖形]。 例如：

```html
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

如需詳細資訊，請參閱[Open Graph](http://ogp.me)網站。

結構化資料標記的另一個常見格式是架構. 組織的微資料格式。 例如：

```html
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
  <span itemprop="ratingValue">4** stars -
  <span itemprop="reviewCount">255** reviews
```

相同的資訊可以用架構. 組織的 JSON-LD 格式來表示：

```html
<script type="application/ld+json">
  "@content":"http://schema.org",
  "@type":"AggregateRating",
  "ratingValue":"4",
  "reviewCount":"255"
</script>
```

以下顯示網站的中繼資料範例，提供豐富的搜尋結果給使用者：

[![](web-markup-images/deeplink01.png "透過結構化資料標記的豐富搜尋結果")](web-markup-images/deeplink01.png#lightbox)

Apple 目前支援 schema.org 中的下列架構類型：

- AggregateRating
- ImageObject
- InteractionCount
- 提高
- 結構
- PriceRange
- 配方
- SearchAction

如需這些配置類型的詳細資訊，請參閱[schema.org](http://schema.org)。

## <a name="providing-actions-with-structured-data"></a>以結構化資料提供動作

特定類型的結構化資料可讓使用者進行搜尋結果。 目前支援下列動作：

- 撥打電話號碼。
- 取得給定位址的地圖方向。
- 播放音訊或影片檔案。

例如，定義撥號電話號碼的動作可能如下所示：

```html
<div itemscope itemtype="http://schema.org/Organization">
  <span itemprop="telephone">(408) 555-1212**
```

當此搜尋結果呈現給使用者時，結果中會顯示一個小電話圖示。 如果使用者按下圖示，將會呼叫指定的數位。

下列 HTML 會加入動作來播放搜尋結果中的音訊檔案：

```html
<div itemscope itemtype="http://schema.org/AudioObject">
  <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**
```

最後，下列 HTML 會加入動作以從搜尋結果取得指示：

```html
<div itemscope itemtype="http://schema.org/PostalAddress">
  <span itemprop="streetAddress">1 Infinite Loop**
  <span itemprop="addressLocality">Cupertino**
  <span itemprop="addressRegion">CA**
  <span itemprop="postalCode">95014**
```

如需詳細資訊，請參閱 Apple 的[應用程式搜尋開發人員網站](https://developer.apple.com/ios/search/)。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
