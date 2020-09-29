---
title: 在 Xamarin 中使用 Core 焦點搜尋
description: 本檔說明如何在 Xamarin iOS 應用程式中使用核心焦點，以提供應用程式內內容的連結。 它會討論如何建立、還原、更新和刪除可搜尋的專案。
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 684e2b8ad5098dd9cecb14eb9c5e265e3ea53035
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436133"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>在 Xamarin 中使用 Core 焦點搜尋

核心焦點是適用于 iOS 9 的新架構，它提供了類似資料庫的 API，可新增、編輯或刪除您應用程式中內容的連結。 使用 Core 焦點新增的專案將可在 iOS 裝置上的焦點搜尋中使用。

如需可使用核心焦點來編制索引之內容類型的範例，請查看 Apple 的訊息、郵件、行事曆和 Notes 應用程式。 它們目前都使用核心焦點來提供搜尋結果。

## <a name="creating-an-item"></a>建立專案

下列範例會建立專案，並使用核心焦點將它編制索引：

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

[![核心焦點搜尋結果總覽](corespotlight-images/corespotlight01.png)](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>還原專案

當使用者透過您應用程式的核心焦點，透過核心焦點將專案新增至搜尋結果時 `AppDelegate` ， `ContinueUserActivity` 會呼叫方法 (此方法也會用於 `NSUserActivity`) 。 例如：

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

請注意，這次我們會檢查是否有的活動 `ActivityType` `CSSearchableItem.ActionType` 。

## <a name="updating-an-item"></a>更新專案

有時候我們使用 Core 焦點建立的索引項目目，可能需要修改，例如，需要變更標題或縮圖影像。 為了進行這種變更，我們會使用與最初建立索引時所用的相同方法。
我們會使用與 `CSSearchableItem` 用來建立專案的相同識別碼建立新的，並附加 `CSSearchableItemAttributeSet` 包含已修改屬性的新專案：

[![更新專案總覽](corespotlight-images/corespotlight02.png)](corespotlight-images/corespotlight02.png#lightbox)

當這個專案寫入至可搜尋的索引時，現有的專案會以新的資訊更新。

## <a name="deleting-an-item"></a>刪除專案

當不再需要索引項目時，核心焦點會提供多種方法來刪除索引項目目。

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

接下來，您可以依功能變數名稱刪除索引項目目的群組。 例如：

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

核心焦點有下列功能，可協助保持索引的精確度和最新狀態：

- **批次更新支援** –如果您的應用程式需要同時建立或修改大型索引群組，則可以 `Index` `CSSearchableIndex` 在單一呼叫中將整個批次傳送至類別的方法。
- **回應索引變更** –使用您的 `CSSearchableIndexDelegate` 應用程式可以從可搜尋的索引回應變更和通知。
- 套用**資料保護**：使用資料保護類別，您可以在使用核心焦點新增至可搜尋索引的專案上，執行安全性。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [適用于開發人員的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)