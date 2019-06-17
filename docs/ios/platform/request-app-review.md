---
title: 要求在 Xamarin.iOS 中的應用程式檢閱
description: 這篇文章描述 RequestReview 方法新增至 iOS 10，該 Apple，並討論如何在 Xamarin.iOS 中加以實作。
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: f72aaa781b0712e206cf02725cfc434594287f41
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61365787"
---
# <a name="request-app-review-in-xamarinios"></a>要求在 Xamarin.iOS 中的應用程式檢閱

_本文章涵蓋 RequestReview 方法新增至 iOS 10，以及如何在 Xamarin.iOS 中實作該 Apple。_

剛接觸 iOS 10.3，`RequestReview()`方法可讓 iOS 應用程式以要求使用者評等或檢閱它。 使用者從 App Store 安裝的傳送應用程式中呼叫這個方法時，iOS 10 會處理整個的評等，和開發人員適用的檢閱程序。 此程序應用程式存放區原則所控管，因為警示可能會或可能不會顯示。

![](request-app-review-images/review01.png "檢閱要求警示範例")

## <a name="requesting-a-rating-or-review"></a>要求評分或評論

雖然`RequestReview()`靜態方法`SKStoreReviewController`類別可以呼叫隨時派得上用場的使用者體驗中，檢閱程序會控管，而且由應用程式存放區原則。 如此一來，這個方法可能會或可能不會顯示警示，並永遠不可叫用以回應使用者動作，例如點選按鈕，即可。

例如，應用程式可能會要求的檢閱之後，啟動以指定重試次數，或遊戲在播放程式完成層級之後，可能會要求檢閱。

要求檢閱的 Xamarin.iOS 應用程式可讓您完成啟動，因為會進行下列變更以`AppDelegate.cs`檔案：

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
> 呼叫`RequestReview()`少量的程式開發中應用程式將一律顯示評等和檢閱對話方塊中，因此可進行測試。 這不適用於透過 TestFlight，方法呼叫將會忽略已散發的應用程式。

當`RequestReview()`使用者從 App Store 安裝的傳送應用程式中呼叫方法，iOS 10 開發人員處理整個的評等和檢閱程序。 同樣地，因為此程序應用程式存放區原則所控管，警示可能會或可能不會顯示。

## <a name="linking-to-an-app-store-product-page"></a>連結至 App Store 的產品頁面 

除了新`RequestReview`方法，開發人員仍可提供應用程式中從 App Store 中的應用程式的產品頁面的深層連結。 藉由附加`action=write-review`至產品頁面 URL 的結尾，頁面將會開啟使用者可以撰寫自動檢閱應用程式。 

## <a name="summary"></a>總結

這篇文章已涵蓋 RequestReview 方法新增至 iOS 10，以及如何在 Xamarin.iOS 中實作該 Apple。



## <a name="related-links"></a>相關連結

- [iOSTenThree 範例](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
