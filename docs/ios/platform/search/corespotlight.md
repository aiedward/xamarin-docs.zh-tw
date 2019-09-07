---
title: 在 Xamarin 中使用核心焦點進行搜尋
description: 本檔說明如何在 Xamarin iOS 應用程式中使用核心焦點，以提供應用程式內內容的連結。 它討論如何建立、還原、更新和刪除可搜尋的專案。
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/20/2017
ms.openlocfilehash: 00a973e670ff5100a44ba158fe50f134781a97e2
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769499"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>在 Xamarin 中使用核心焦點進行搜尋

核心焦點是適用于 iOS 9 的新架構，呈現類似資料庫的 API，可新增、編輯或刪除應用程式內內容的連結。 使用核心焦點新增的專案，將會在 iOS 裝置上的焦點搜尋中提供。

如需可使用核心焦點編制索引之內容類型的範例，請參閱 Apple 的訊息、郵件、行事曆和便箋應用程式。 他們目前使用核心焦點來提供搜尋結果。

## <a name="creating-an-item"></a>建立專案

以下範例會建立專案，並使用核心焦點將其編制索引：

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

這項資訊會在搜尋結果中顯示如下：

[![](corespotlight-images/corespotlight01.png "核心焦點搜尋結果總覽")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>還原專案

當使用者透過應用程式的核心焦點來點擊新增至搜尋結果的專案時， `AppDelegate`會呼叫方法`ContinueUserActivity` （此`NSUserActivity`方法也會用於）。 例如：

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

請注意，這次我們要檢查的活動是否具有`ActivityType`的`CSSearchableItem.ActionType`。

## <a name="updating-an-item"></a>更新專案

在某些情況下，必須修改以核心焦點建立的索引項目，例如，需要變更標題或縮圖影像。 若要進行這種變更，我們使用與最初建立索引時所使用的相同方法。
我們會使用與`CSSearchableItem`建立專案時所用的相同識別碼來建立新的，並附加`CSSearchableItemAttributeSet`包含修改屬性的新：

[![](corespotlight-images/corespotlight02.png "更新專案總覽")](corespotlight-images/corespotlight02.png#lightbox)

當此專案寫入至可搜尋的索引時，會以新的資訊更新現有的專案。

## <a name="deleting-an-item"></a>刪除專案

當不再需要索引項目目時，核心焦點會提供多種方法來加以刪除。

首先，您可以依識別碼刪除專案，例如：

```csharp
// Delete Items by ID
CSSearchableIndex.DefaultSearchableIndex.Delete(new string[]{"1","16"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

接下來，您可以依功能變數名稱刪除索引項目的群組。 例如：

```csharp
// Delete by Domain Name
CSSearchableIndex.DefaultSearchableIndex.DeleteWithDomain(new string[]{"domain-name"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

最後，您可以使用下列程式碼刪除所有索引項目：

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

核心焦點具有下列功能，可協助您保持索引的精確度和最新狀態：

- **批次更新支援**-如果您的應用程式需要同時建立或修改大型索引群組，則可以在單一呼叫中，將整個批次`Index`傳送至`CSSearchableIndex`類別的方法。
- **回應索引變更**–使用您的`CSSearchableIndexDelegate`應用程式可以回應可搜尋索引中的變更和通知。
- 套用**資料保護**–使用資料保護類別，您可以在使用核心焦點新增至可搜尋索引的專案上，執行安全性。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
