---
title: 在 Xamarin.iOS 中的核心 Spotlight 搜尋
description: 本文件說明如何在 Xamarin.iOS 應用程式中使用核心焦點，以提供應用程式內容的連結。 它討論如何建立、 還原、 更新和刪除可搜尋的項目。
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: fb9ddcc39bd33199dc370897250cd0d74597612f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248469"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>在 Xamarin.iOS 中的核心 Spotlight 搜尋

核心焦點是新的架構，適用於 iOS 9 所呈現的類似資料庫 API，以新增、 編輯或刪除應用程式內的內容連結。 已使用核心焦點的項目可在 iOS 裝置上的 Spotlight 搜尋中。

如需內容類型的範例，可以使用核心 Spotlight 索引，請參閱 Apple 的郵件、 郵件、 行事曆和附註的應用程式。 目前，它們都會使用核心焦點提供搜尋結果。

## <a name="creating-an-item"></a>建立項目

建立項目，並加以檢索，使用核心焦點的範例如下：

```csharp
using CoreSpotlight;
...

// Create attributes to describe an item
var attributes = new CSSearchableItemAttributeSet();
attributes.Title = "App Center Test";
attributes.ContentDescription = "Automatically test your app on 1,000 devices in the cloud.";

// Create item
var item = new CSSearchableItem ("1", "products", attributes);

// Index item
CSSearchableIndex.DefaultSearchableIndex.Index (new CSSearchableItem[]{ item }, (error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

這項資訊會出現在搜尋結果如下：

[![](corespotlight-images/corespotlight01.png "Core Spotlight 搜尋結果概觀")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>還原項目

當使用者點選加入至您的應用程式，透過核心 Spotlight 搜尋結果的項目`AppDelegate`方法`ContinueUserActivity`稱為 (這個方法也用於`NSUserActivity`)。 例如: 

```csharp
public override bool ContinueUserActivity (UIApplication application,
   NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchableItem.ActionType.ToString ()) {
            // Display content for searchable item...
        }
        break;
    }

    return true;
}
```

請注意，這次我們檢查活動 having`ActivityType`的`CSSearchableItem.ActionType`。

## <a name="updating-an-item"></a>更新項目

有時候可能會當我們建立透過核心焦點的索引項目需要加以修改，例如在 標題 或 縮圖影像的變更是必要項。 若要讓這項變更，我們會使用相同的方法，用來一開始建立索引。
我們會建立新`CSSearchableItem`使用相同的識別碼，與用來建立項目，並附加新`CSSearchableItemAttributeSet`包含已修改的屬性：

[![](corespotlight-images/corespotlight02.png "更新項目概觀")](corespotlight-images/corespotlight02.png#lightbox)

當這個項目會寫入可搜尋的索引時，以新的資訊更新現有的項目。

## <a name="deleting-an-item"></a>刪除項目

核心焦點提供多種方法來刪除索引的項目，當不再需要。

首先，您可以刪除項目依其識別項，例如：

```csharp
// Delete Items by ID
CSSearchableIndex.DefaultSearchableIndex.Delete(new string[]{"1","16"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

接下來，您可以刪除其網域名稱的索引項目群組。 例如: 

```csharp
// Delete by Domain Name
CSSearchableIndex.DefaultSearchableIndex.DeleteWithDomain(new string[]{"domain-name"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

最後，您可以刪除所有的索引項目，為下列程式碼：

```csharp
// Delete all index items
CSSearchableIndex.DefaultSearchableIndex.DeleteAll((error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```
## <a name="additional-core-spotlight-features"></a>其他核心焦點功能

核心焦點具有下列功能，有助於維持準確且最新的索引：

- **批次更新支援**– 如果您的應用程式需要建立或修改索引的大型群組，在此同時，整個批次可以傳送給`Index`方法`CSSearchableIndex`一次呼叫中的類別。
- **索引變更回應**–`CSSearchableIndexDelegate`您的應用程式可以變更與通知回應從可搜尋的索引。
- **套用資料保護**– 使用資料保護類別，您可以實作安全性上加入可搜尋的索引，使用核心焦點的項目。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
