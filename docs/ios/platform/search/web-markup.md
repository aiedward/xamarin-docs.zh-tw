---
title: Web 標記在 Xamarin.iOS 中使用的搜尋
description: 本文件說明如何建立連結至 Xamarin.iOS 應用程式的網頁搜尋結果。 它討論如何啟用 web 內容編製索引，讓您的應用程式網站可供探索，使用智慧型應用程式內橫幅、 通用連結，和更多功能。
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 243408ce6e2236b75ea35dfd17633a9a24493c1b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102798"
---
# <a name="search-with-web-markup-in-xamarinios"></a>Web 標記在 Xamarin.iOS 中使用的搜尋

提供其內容，透過網站的存取權的應用程式 (不只是從應用程式內)，web 內容可以來標記的特殊連結，將由 Apple 編目，並提供您裝置上應用程式使用者的 iOS 9 的深層連結。

如果您的 iOS 應用程式已經可以支援行動裝置的深層連結，而且您的網站所呈現的深層連結應用程式內，Apple 的內容_Applebot_ web 編目程式將此內容的索引，並自動將它新增至其雲端索引：

[![](web-markup-images/webmarkup01.png "雲端索引概觀")](web-markup-images/webmarkup01.png#lightbox)

Apple 會呈現在 Spotlight 搜尋和 Safari 搜尋結果中的這些結果。
如果使用者在點選其中一種結果 （而且它們已安裝的應用程式） 則會將他們引導至您的應用程式中的內容：

[![](web-markup-images/webmarkup02.png "從搜尋結果中的網站連結的深度")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>啟用 Web 內容編製索引

有四個要設為您應用程式的內容可使用 Web 標記所需的步驟：

1. 確認可以探索 Apple，並藉由定義為索引您的應用程式網站**支援**或是**行銷**在 iTunes Connect 中的網站。
2. 請確定您的應用程式網站包含必要的標記，以實作行動裝置的深層連結。 請參閱下列各節，如需詳細資訊。
3. 啟用 iOS 應用程式中處理的深層連結。
4. 將您的應用程式網站，為使用者提供豐富且吸引人的結果呈現的結構化資料的標記。 雖然這個步驟不是絕對必要，強烈建議由 Apple。

下列各節將介紹這些詳細資料中的步驟。

## <a name="make-your-apps-website-discoverable"></a>製作您的應用程式網站探索

有 Apple 尋找您的應用程式網站的最簡單方式是使用做**支援**或是**行銷**網站，當您將應用程式提交給 Apple 透過 iTunes Connect。

## <a name="using-smart-app-banners"></a>使用智慧型應用程式內橫幅

在您的網站，來呈現的明確連結至您的應用程式提供智慧型應用程式內橫幅。 如果尚未安裝應用程式，Safari 將會自動提示使用者安裝您的應用程式。 否則使用可以點選**檢視**連結來啟動您的應用程式從網站。 例如，若要建立智慧型應用程式內橫幅，您可以使用下列程式碼：

```xml
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

如需詳細資訊，請參閱 Apple[升級應用程式與智慧的應用程式內橫幅](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html)文件。

## <a name="using-universal-links"></a>使用通用的連結

新增至 iOS 9、 通用連結提供較佳替代的智慧型應用程式內橫幅或現有的自訂 URL 配置藉由提供下列：

- **唯一**-相同的 URL 不能宣告多個網站。
- **安全**– 的簽署的憑證時，需要可確保該網站擁有您和有效的網站連結至您的應用程式。
- **彈性**– 使用者可以控制在網站或應用程式的 URL 是否會啟動。
- **通用**-相同的 URL 可以用來定義您的網站和您的應用程式的內容。

## <a name="using-twitter-cards"></a>使用 Twitter 卡

您可以提供您的應用程式使用 Twitter 卡的內容的深層連結。 例如: 

```xml
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

如需詳細資訊，請參閱 Twitter 的[Twitter 卡片通訊協定](http://dev.twitter.com/cards/mobile)文件。

## <a name="using-facebook-app-links"></a>使用 Facebook 應用程式連結

您可以提供使用 Facebook 應用程式連結您的應用程式內容的深層連結。 例如: 

```xml
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

如需詳細資訊，請參閱 Facebook 的[應用程式連結](http://applinks.org)文件。

## <a name="opening-deep-links"></a>開啟深層連結

您要新增支援開啟和顯示您的 Xamarin.iOS 應用程式深層連結。 編輯**AppDelegate.cs**檔案，並覆寫`OpenURL`方法以處理自訂的 URL 格式。 例如: 

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

在上述程式碼中，我們正在尋找一個 URL，其中包含`/appname`並傳遞的值`query`(`123`在此範例中) 以在我們的應用程式，以向使用者顯示要求的內容中的自訂檢視控制器。

## <a name="providing-rich-results-with-structured-data"></a>提供豐富結構化資料的結果

包含結構化資料標記中，您可以提供豐富的搜尋結果超過只是提供標題與描述的使用者。 包括影像、 應用程式 （例如評等） 的特定資料和動作來使用結構化資料標記的結果。

豐富的結果會更豐富，而且可以協助改善您在雲端中的排名基礎搜尋服務索引，方法是它提供更多使用者與它們互動。

提供結構化資料標記的其中一個選項是使用 Open Graph。 例如: 

```xml
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

如需詳細資訊，請參閱[Open Graph](http://ogp.me)網站。

結構化資料標記的另一種常見格式是 schema.org 的微資料格式。 例如: 

```xml
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
    <span itemprop="ratingValue">4** stars -
    <span itemprop="reviewCount">255** reviews


```

可以表示相同的資訊，JSON-LD schema.org 的格式：

```xml
<script type="application/ld+json">
    "@content":"http://schema.org",
    "@type":"AggregateRating",
    "ratingValue":"4",
    "reviewCount":"255"
</script>
```

以下顯示從您的網站為使用者提供豐富的搜尋結果中繼資料的範例：

[![](web-markup-images/deeplink01.png "豐富的搜尋結果，透過結構化資料標記")](web-markup-images/deeplink01.png#lightbox)

Apple 目前支援下列的結構描述型別，從 schema.org:

 - AggregateRating
 - ImageObject
 - InteractionCount
 - 供應項目
 - 組織
 - PriceRange
 - 配方
 - SearchAction

如需有關這些配置類型的詳細資訊，請參閱[schema.org](http://schema.org)。

## <a name="providing-actions-with-structured-data"></a>提供結構化資料的動作

特定類型的結構化資料可讓搜尋結果可由使用者執行動作。 目前支援下列動作：

 - 撥號的電話號碼。
 - 取得對應至指定的地址的方向。
 - 播放音訊或視訊檔案。

例如，定義要撥打的電話號碼的動作看起來可能如下所示：

```xml
<div itemscope itemtype="http://schema.org/Organization">
    <span itemprop="telephone">(408) 555-1212**


```

使用者看到此搜尋結果中時，小型手機圖示將顯示在結果中。 如果在使用者點選圖示，就會呼叫指定的數字。

下列 HTML 可以加入要播放的音訊檔案，從搜尋結果的動作：

```xml
<div itemscope itemtype="http://schema.org/AudioObject">
    <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**


```

最後，下列 HTML 會新增動作以從搜尋結果中取得指示：

```xml
<div itemscope itemtype="http://schema.org/PostalAddress">
    <span itemprop="streetAddress">1 Infinite Loop**
    <span itemprop="addressLocality">Cupertino**
    <span itemprop="addressRegion">CA**
    <span itemprop="postalCode">95014**


```

如需詳細資訊，請參閱 Apple[應用程式搜尋服務開發人員網站](http://developer.apple.com/ios/search/)。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
