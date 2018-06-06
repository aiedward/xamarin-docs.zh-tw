---
title: 具有 Web 標記 Xamarin.iOS 中搜尋
description: 本文件說明如何建立連結至 Xamarin.iOS 應用程式的 web 架構的搜尋結果。 它討論如何啟用編製索引，可讓您的應用程式網站執行可探索，並使用智慧應用程式橫幅、 通用的連結，以及更多的 web 內容。
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 438a65de3eb78f849493e3478bce5522a325d0cd
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787990"
---
# <a name="search-with-web-markup-in-xamarinios"></a>具有 Web 標記 Xamarin.iOS 中搜尋

提供網站上透過其內容的存取權的應用程式 (不只會從應用程式內)，web 內容可以標記具有特殊的連結，對於將編目 Apple，並提供深層連結至您的應用程式使用者的 iOS 9 裝置上。

如果您的 iOS 應用程式已經可以支援行動裝置的深層連結，以及您的網站提出深層連結應用程式內，Apple 的內容_Applebot_ web 編目程式會建立此內容索引並自動將它加入至其雲端索引：

[![](web-markup-images/webmarkup01.png "雲端索引概觀")](web-markup-images/webmarkup01.png#lightbox)

Apple 就會出現在 Spotlight 搜尋和 Safari 搜尋結果中的這些結果。
如果使用者在點選下列其中一種結果 （和它們已安裝的應用程式） 則會前往應用程式中的內容：

[![](web-markup-images/webmarkup02.png "深層連結從搜尋結果中的網站")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>啟用 Web 內容索引

有四個要設為您應用程式的內容可使用網頁的標記所需的步驟：

1. 請確認可以探索 Apple，並藉由定義做為索引您的應用程式網站**支援**或**行銷**在 iTunes Connect 中的網站。
2. 請確認您的應用程式的網站包含必要的標記，以實作行動深層連結。 請參閱下列各節，如需詳細資訊。
3. 啟用深層連結，在您的 iOS 應用程式中處理。
4. 加入標記由應用程式的網站提供豐富且更吸引人的結果給終端使用者顯示的結構化資料。 雖然這個步驟並非絕對必要，強烈建議 apple。

下列各節將介紹這些步驟詳細資料。

## <a name="make-your-apps-website-discoverable"></a>讓您的應用程式網站成為可搜尋

有 Apple 尋找您的應用程式網站的最簡單方式是使用它，可能是**支援**或**行銷**網站，當您將應用程式提交至 Apple iTunes Connect 透過。

## <a name="using-smart-app-banners"></a>使用智慧應用程式橫幅

在您的網站來呈現清除連結到您的應用程式提供智慧的應用程式內橫幅。 如果尚未安裝應用程式，Safari 會自動提示使用者安裝應用程式。 否則使用可以點選**檢視**啟動您的應用程式從網站的連結。 例如，若要建立智慧型的應用程式內橫幅，您可以使用下列程式碼：

```xml
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

如需詳細資訊，請參閱 Apple[升級應用程式與智慧應用程式橫幅](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html)文件。

## <a name="using-universal-links"></a>使用通用的連結

新增到 iOS 9、 通用的連結，請提供藉由提供下列的智慧型應用程式的橫幅或現有的自訂 URL 配置較佳替代方式：

- **唯一**– 相同的 URL 不能宣告多個網站。
- **安全**– 簽署的憑證是必要的可確保您和有效地擁有網站的網站連結至您的應用程式。
- **彈性**– 使用者可以控制 URL 啟動的網站或應用程式。
- **通用**– 相同的 URL 可以用來定義您的網站和應用程式的內容。

## <a name="using-twitter-cards"></a>使用 Twitter 卡

您可以提供深層連結應用程式的內容使用 Twitter 卡。 例如: 

```xml
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

如需詳細資訊，請參閱 Twitter 的[Twitter 卡通訊協定](http://dev.twitter.com/cards/mobile)文件。

## <a name="using-facebook-app-links"></a>使用 Facebook 應用程式的連結

您可以提供深層連結應用程式的內容使用 Facebook 應用程式連結。 例如: 

```xml
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

如需詳細資訊，請參閱 Facebook 的[應用程式連結](http://applinks.org)文件。

## <a name="opening-deep-links"></a>開啟深層連結

您要加入支援開啟及 Xamarin.iOS 應用程式中顯示的深層連結。 編輯**d**檔案，並覆寫`OpenURL`方法以處理自訂的 URL 格式。 例如: 

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

在上述程式碼中，我們會查看 URL 包含`/appname`和傳遞的值`query`(`123`在此範例中) 自訂檢視中的控制站我們的應用程式向使用者顯示要求的內容。

## <a name="providing-rich-results-with-structured-data"></a>提供豐富的結構化資料的結果

包含結構化資料標記中，您可以提供豐富的搜尋結果超出只是標題和描述的使用者。 包含影像、 應用程式特定資料 （例如評等） 和動作的結果與使用結構化資料標記。

豐富的結果更多參與，而且有助於提升您雲端中的排名依搜尋索引引誘更多使用者互動。

提供結構化資料標記的其中一個選項是使用 Open Graph。 例如: 

```xml
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

如需詳細資訊，請參閱[Open Graph](http://ogp.me)網站。

結構化資料標記的另一個常見的格式是 schema.org 的 Responsive 格式。 例如: 

```xml
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
    <span itemprop="ratingValue">4** stars -
    <span itemprop="reviewCount">255** reviews


```

相同的資訊可以表示 schema.org 的 JSON LD 格式：

```xml
<script type="application/ld+json">
    "@content":"http://schema.org",
    "@type":"AggregateRating",
    "ratingValue":"4",
    "reviewCount":"255"
</script>
```

下列顯示您的網站提供豐富的搜尋結果給終端使用者從中繼資料的範例：

[![](web-markup-images/deeplink01.png "許多搜尋結果，透過結構化資料標記")](web-markup-images/deeplink01.png#lightbox)

Apple 目前支援下列結構描述類型從 schema.org:

 - AggregateRating
 - ImageObject
 - InteractionCount
 - 提供項目
 - 組織
 - PriceRange
 - 配方
 - SearchAction

如需有關這些配置類型的詳細資訊，請參閱[schema.org](http://schema.org)。

## <a name="providing-actions-with-structured-data"></a>提供的結構化資料的動作

特定類型的結構化的資料可讓搜尋結果是由一般使用者可採取動作。 目前支援下列動作：

 - 撥號的電話號碼。
 - 取得指定的位址對應方向。
 - 播放音訊或視訊檔案。

例如，定義要撥打的電話號碼的動作可能如下所示：

```xml
<div itemscope itemtype="http://schema.org/Organization">
    <span itemprop="telephone">(408) 555-1212**


```

這個搜尋結果呈現給使用者，小型電話圖示將會在結果中。 如果在使用者點選圖示，就會呼叫指定的數字。

下列 HTML 可以加入要播放的音訊檔案，從搜尋結果的動作：

```xml
<div itemscope itemtype="http://schema.org/AudioObject">
    <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**


```

最後，下列 HTML 可以加入動作，以從搜尋結果中取得指示：

```xml
<div itemscope itemtype="http://schema.org/PostalAddress">
    <span itemprop="streetAddress">1 Infinite Loop**
    <span itemprop="addressLocality">Cupertino**
    <span itemprop="addressRegion">CA**
    <span itemprop="postalCode">95014**


```

如需詳細資訊，請參閱 Apple[應用程式搜尋開發人員網站](http://developer.apple.com/ios/search/)。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式內搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
