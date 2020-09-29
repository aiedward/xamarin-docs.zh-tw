---
title: 在 Xamarin 中要求應用程式評論
description: 本文說明 Apple 新增至 iOS 10 的 RequestReview 方法，並討論如何在 Xamarin 中加以執行。
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: b51eeb3594f1b41f8b8f7fdaedc7577138c4f5ba
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435668"
---
# <a name="request-app-review-in-xamarinios"></a>在 Xamarin 中要求應用程式評論

_本文涵蓋 Apple 新增至 iOS 10 的 RequestReview 方法，以及如何在 Xamarin 中加以執行。_

IOS 10.3 的新功能，此 `RequestReview()` 方法可讓 ios 應用程式要求使用者對其進行評分或複習。 當使用者從 App Store 安裝的貨運應用程式中呼叫這個方法時，iOS 10 將會處理開發人員的整個評等和審核程式。 由於此程式是由 App Store 原則所控制，因此可能會出現或可能不會顯示警示。

![範例審核要求警示](request-app-review-images/review01.png)

## <a name="requesting-a-rating-or-review"></a>要求評等或評論

雖然 `RequestReview()` 類別的靜態方法 `SKStoreReviewController` 可以在使用者體驗中有意義的任何時間點呼叫，但是審核程式是由 App Store 原則來控管和處理。 如此一來，此方法可能會或可能不會顯示警示，而且永遠不會呼叫以回應使用者動作，例如點擊按鈕。

例如，應用程式可能會在已啟動指定次數之後要求審核，或在播放程式完成層級後，讓遊戲要求審查。

若要在 Xamarin iOS 應用程式完成啟動時立即要求審查，請對檔案進行下列變更 `AppDelegate.cs` ：

```csharp
using Foundation;
using StoreKit;
using UIKit;

namespace iOSTenThree
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        ...

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Request a review from the user
            SKStoreReviewController.RequestReview ();

            return true;
        }

        ...

    }
}
```

> [!NOTE]
> `RequestReview()`在開發中的應用程式中呼叫一律會顯示評等和審核對話方塊，以便進行測試。 這並不適用于透過 TestFlight 散發的應用程式，方法呼叫將會被忽略。

在 `RequestReview()` 使用者從 App Store 安裝的出貨應用程式中呼叫方法時，iOS 10 將會處理開發人員的整個評等和審核程式。 同樣地，由於此程式是由 App Store 原則所控制，因此可能會有警示或可能不會顯示。

## <a name="linking-to-an-app-store-product-page"></a>連結至 App Store 產品頁面 

除了新的方法之外 `RequestReview` ，開發人員還可以在應用程式內，從應用程式內的 App Store 中，提供應用程式產品頁面的深層連結。 藉由附加 `action=write-review` 至產品頁面 URL 的結尾，將會開啟一個頁面，讓使用者可以自動撰寫應用程式的評論。 

## <a name="summary"></a>摘要

本文涵蓋了 Apple 新增至 iOS 10 的 RequestReview 方法，以及如何在 Xamarin 中加以執行。

## <a name="related-links"></a>相關連結

- [iOSTenThree 範例](/samples/xamarin/ios-samples/ios10-iostenthree/)