---
title: 在 Xamarin 中要求應用程式審查
description: 本文說明 Apple 新增至 iOS 10 的 RequestReview 方法，並討論如何在 Xamarin 中執行。
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: f8dc533d1428d622d9b01b8b8dd879653f78fd56
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769524"
---
# <a name="request-app-review-in-xamarinios"></a>在 Xamarin 中要求應用程式審查

_本文涵蓋 Apple 新增至 iOS 10 的 RequestReview 方法，以及如何在 Xamarin 中加以執行。_

Ios 10.3 的`RequestReview()`新功能，方法可讓 ios 應用程式要求使用者對其進行評分或檢查。 當使用者已從 App Store 安裝的出貨應用程式中呼叫此方法時，iOS 10 將會處理開發人員的整個評等和審查流程。 由於此程式是由 App Store 原則所控管，因此不一定會顯示警示。

![](request-app-review-images/review01.png "範例審查要求警示")

## <a name="requesting-a-rating-or-review"></a>要求評等或評論

雖然可以在使用者體驗中`SKStoreReviewController`合理地呼叫類別的靜態方法，但審核程式是由AppStore原則進行控管和處理。`RequestReview()` 因此，此方法不一定會顯示警示，而且永遠不會呼叫以回應使用者動作，例如點擊按鈕。

例如，應用程式可能會在啟動指定的次數之後要求審查，或在玩家完成一層樓之後，遊戲可能會要求審查。

若要在 Xamarin iOS 應用程式完成啟動時立即要求審查，請對檔案進行下列變更`AppDelegate.cs` ：

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
> 在`RequestReview()`開發中的應用程式中呼叫，一律會顯示 [評等] 和 [審查] 對話方塊，讓它可以進行測試。 這不適用於透過 TestFlight 散發的應用程式，其中會忽略方法呼叫。

當使用者已從 app Store 安裝的出貨應用程式中呼叫方法時，iOS10將會處理開發人員的整個評等和審查流程。`RequestReview()` 同樣地，因為此程式是由 App Store 原則所控管，所以不一定會顯示警示。

## <a name="linking-to-an-app-store-product-page"></a>連結至 App Store 產品頁面 

除了新`RequestReview`的方法之外，開發人員仍然可以從應用程式內提供應用程式商店中應用程式產品頁面的深層連結。 藉由`action=write-review`將附加至產品頁面 URL 的結尾，頁面將會開啟，讓使用者能夠自動撰寫應用程式的審核。 

## <a name="summary"></a>總結

本文涵蓋 Apple 新增至 iOS 10 的 RequestReview 方法，以及如何在 Xamarin 中執行。

## <a name="related-links"></a>相關連結

- [iOSTenThree 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
