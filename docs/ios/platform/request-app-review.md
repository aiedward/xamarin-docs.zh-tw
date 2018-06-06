---
title: 要求中 Xamarin.iOS 應用程式檢閱
description: 本文描述 RequestReview 方法加入至 iOS 10，該 Apple，並討論如何實作 Xamarin.iOS 中。
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 2b329ebad5faaa635d9a791f8760bd5f521de591
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788153"
---
# <a name="request-app-review-in-xamarinios"></a>要求中 Xamarin.iOS 應用程式檢閱

_本文涵蓋 RequestReview 方法加入至 iOS 10，以及如何實作在 Xamarin.iOS 該 Apple。_

Ios 10.3，新`RequestReview()`方法可讓要求使用者評等或檢閱 iOS 應用程式。 使用者安裝應用程式商店中的傳送應用程式中呼叫這個方法時，iOS 10 會處理整個分級，並檢閱程序的開發人員。 此程序由應用程式存放區原則，因為警示可能或可能不會顯示。

![](request-app-review-images/review01.png "範例要求檢閱警示")

## <a name="requesting-a-rating-or-review"></a>要求的評等或檢閱

雖然`RequestReview()`靜態方法`SKStoreReviewController`可以呼叫類別的任何一點，也可以在使用者經驗，檢閱程序是控管，而且由應用程式存放區原則處理。 如此一來，這個方法可能會或可能不會顯示警示，並永遠不會呼叫以回應使用者動作，例如點選按鈕。

例如，應用程式可能會要求的檢閱之後，它會啟動指定的次數或遊戲可能要求檢閱，播放程式完成層級之後。

要求檢閱 Xamarin.iOS 應用程式完成啟動，因為會進行下列變更以`AppDelegate.cs`檔案：

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
> 呼叫`RequestReview()`在置中的開發應用程式將一律顯示分級並檢閱對話方塊，以便進行測試。 這不適用於已散發到 TestFlight，將會忽略方法呼叫的應用程式。

當`RequestReview()`使用者安裝應用程式商店中的傳送應用程式中呼叫方法、 iOS 10 處理整個的評等，並檢閱程序的開發人員。 同樣地，此程序由應用程式存放區原則，因為警示可能或可能不會顯示。

## <a name="linking-to-an-app-store-product-page"></a>連結至 App Store 產品頁面 

除了新`RequestReview`方法，開發人員仍然可以提供深層連結應用程式的應用程式中從 App Store 中的產品頁面。 藉由附加`action=write-review`到產品網頁 URL 的結尾，頁面將會開啟使用者可以撰寫會自動將應用程式的檢閱。 

## <a name="summary"></a>總結

這篇文章已涵蓋 RequestReview 方法加入至 iOS 10，以及如何實作在 Xamarin.iOS 該 Apple。



## <a name="related-links"></a>相關連結

- [iOSTenThree 範例](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
