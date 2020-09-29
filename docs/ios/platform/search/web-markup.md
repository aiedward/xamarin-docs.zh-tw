---
title: 在 Xamarin 中使用 Web 標記搜尋
description: 本檔說明如何建立可連結回到 Xamarin iOS 應用程式的 web 型搜尋結果。 它會討論如何啟用 web 內容編制索引，讓您的應用程式網站可供探索、使用智慧型應用程式橫幅、通用連結等。
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: b578d1d171c6b8e91e76758f4c979fbc8a1b6eaa
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437003"
---
# <a name="search-with-web-markup-in-xamarinios"></a>在 Xamarin 中使用 Web 標記搜尋

針對透過網站提供其內容存取權的應用程式， (不只是從應用程式) ，您可以使用 Apple 編目的特殊連結來標記 web 內容，並在使用者的 iOS 9 裝置上提供您應用程式的深層連結。

如果您的 iOS 應用程式已經支援行動深層連結，而您的網站呈現了應用程式內內容的深層連結，Apple 的 _Applebot_ web 編目程式將會為此內容編制索引，並自動將其新增至其雲端索引：

[![雲端索引總覽](web-markup-images/webmarkup01.png)](web-markup-images/webmarkup01.png#lightbox)

Apple 會在焦點搜尋和 Safari 搜尋結果中呈現這些結果。
如果使用者按 (其中一個結果，且已安裝您的應用程式) 則會將它們移至應用程式中的內容：

[![從搜尋結果中的網站進行深層連結](web-markup-images/webmarkup02.png)](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>啟用 Web 內容編制索引

使用 Web 標記來讓應用程式的內容可供搜尋時，需要四個步驟：

1. 將您的應用程式網站定義為 iTunes Connect 中的 **支援** 或 **行銷** 網站，以確保 Apple 可以探索並編制其索引。
2. 確定您的應用程式網站包含必要的標記，以執行行動深層連結。 如需詳細資訊，請參閱下列各節。
3. 在您的 iOS 應用程式中啟用深層連結處理。
4. 為您的應用程式網站所呈現的結構化資料新增標記，以提供豐富且吸引人的結果給終端使用者。 雖然此步驟並非絕對必要，但強烈建議使用 Apple。

下列各節將詳細說明這些步驟。

## <a name="make-your-apps-website-discoverable"></a>讓您的應用程式網站可供探索

Apple 尋找應用程式網站的最簡單方式，就是在您透過 iTunes Connect 將應用程式提交至 Apple 時，將其當作 **支援** 或 **行銷** 網站使用。

## <a name="using-smart-app-banners"></a>使用智慧型應用程式橫幅

在您的網站上提供智慧型應用程式橫幅，以在您的應用程式中呈現清楚的連結。 如果尚未安裝應用程式，Safari 會自動提示使用者安裝您的應用程式。 否則，使用可點擊 [ **view** ] 連結，從網站啟動您的應用程式。 例如，您可以使用下列程式碼來建立智慧型應用程式橫幅：

```html
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

如需詳細資訊，請參閱 Apple [使用智慧型應用程式橫幅升級應用程式](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) 檔。

## <a name="using-universal-links"></a>使用通用連結

通用連結是 iOS 9 的新手，可提供下列各項，以提供更好的智慧應用程式橫幅或現有自訂 URL 配置的替代方案：

- **唯一** -相同的 URL 不能由一個以上的網站宣告。
- **安全** –網站需要有簽署的憑證，以確保網站是由您所擁有，而且會與您的應用程式有效地連結。
- **彈性** -使用者可以控制 URL 是否會啟動網站或應用程式。
- **通用** -相同的 URL 可以用來定義您的網站和應用程式的內容。

## <a name="using-twitter-cards"></a>使用 Twitter 卡片

您可以使用 Twitter 卡來提供應用程式內容的深層連結。 例如：

```html
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

如需詳細資訊，請參閱 Twitter 的 [Twitter 卡通訊協定](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/abouts-cards) 檔。

## <a name="using-facebook-app-links"></a>使用 Facebook 應用程式連結

您可以使用 Facebook 應用程式連結提供應用程式內容的深層連結。 例如：

```html
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

如需詳細資訊，請參閱 Facebook 的 [應用程式連結](https://developers.facebook.com/docs/applinks) 檔。

## <a name="opening-deep-links"></a>開啟深層連結

您必須在您的 Xamarin iOS 應用程式中新增開啟和顯示深層連結的支援。 編輯 **AppDelegate.cs** 檔案，並覆寫 `OpenURL` 方法以處理自訂 URL 格式。 例如：

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

在上述程式碼中，我們要尋找包含 `/appname` 和傳遞 `query` 此範例中 (值的 URL `123`) 至應用程式中的自訂視圖控制器，以向使用者顯示要求的內容。

## <a name="providing-rich-results-with-structured-data"></a>以結構化資料提供豐富的結果

藉由包含結構化資料標記，您可以提供豐富的搜尋結果給終端使用者，而不只是標題和描述。 包含影像、應用程式特定資料 (例如評等) 和使用結構化資料標記的結果動作。

豐富的結果更吸引人，而且可讓更多使用者與他們互動，以協助改善您在雲端式搜尋索引中的排名。

提供結構化資料標記的一個選項是使用 [開啟圖形]。 例如：

```html
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

如需詳細資訊，請參閱 [Open Graph](https://ogp.me) 網站。

結構化資料標記的另一個常見格式為 schema. org 的微資料格式。 例如：

```html
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
  <span itemprop="ratingValue">4** stars -
  <span itemprop="reviewCount">255** reviews
```

相同的資訊可以 schema 的 JSON-LD 格式表示：

```html
<script type="application/ld+json">
  "@content":"http://schema.org",
  "@type":"AggregateRating",
  "ratingValue":"4",
  "reviewCount":"255"
</script>
```

以下顯示您網站提供豐富搜尋結果給終端使用者的中繼資料範例：

[![透過結構化資料標記的豐富搜尋結果](web-markup-images/deeplink01.png)](web-markup-images/deeplink01.png#lightbox)

Apple 目前支援下列來自 schema.org 的架構類型：

- AggregateRating
- ImageObject
- InteractionCount
- 供應項目
- 組織
- PriceRange
- 配方
- SearchAction

如需這些配置類型的詳細資訊，請參閱 [schema.org](https://schema.org)。

## <a name="providing-actions-with-structured-data"></a>提供結構化資料的動作

特定類型的結構化資料可讓使用者使用搜尋結果。 目前支援下列動作：

- 撥電話號碼。
- 取得地圖方向至指定的位址。
- 播放音訊或影片檔案。

例如，定義撥打電話號碼的動作看起來可能如下所示：

```html
<div itemscope itemtype="http://schema.org/Organization">
  <span itemprop="telephone">(408) 555-1212**
```

當此搜尋結果顯示給終端使用者時，結果中會顯示一個小的電話圖示。 如果使用者按下圖示，則會呼叫指定的號碼。

下列 HTML 會新增動作來播放搜尋結果中的音訊檔案：

```html
<div itemscope itemtype="http://schema.org/AudioObject">
  <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**
```

最後，下列 HTML 會新增動作以從搜尋結果中取得指示：

```html
<div itemscope itemtype="http://schema.org/PostalAddress">
  <span itemprop="streetAddress">1 Infinite Loop**
  <span itemprop="addressLocality">Cupertino**
  <span itemprop="addressRegion">CA**
  <span itemprop="postalCode">95014**
```

如需詳細資訊，請參閱 Apple 的 [應用程式搜尋開發人員網站](https://developer.apple.com/ios/search/)。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [適用于開發人員的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)