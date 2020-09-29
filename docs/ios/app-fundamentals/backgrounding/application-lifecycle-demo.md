---
title: 適用于 Xamarin 的應用程式生命週期示範
description: 本檔會探討應用程式委派在 iOS 應用程式中處理的各種生命週期事件，並示範這些事件的處理時機和方式。
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/17/2018
ms.openlocfilehash: 08ef81f55ac1705224f2953197bfe582718b9cf7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432511"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>適用于 Xamarin 的應用程式生命週期示範

此文章和 [範例程式碼](/samples/xamarin/ios-samples/lifecycledemo) 示範 iOS 中的四個應用程式狀態，以及 `AppDelegate` 在狀態變更時通知應用程式之方法的角色。 應用程式會在應用程式變更狀態時，將更新列印到主控台：

[![範例應用程式](application-lifecycle-demo-images/image3-sml.png)](application-lifecycle-demo-images/image3.png#lightbox)

[![應用程式會在應用程式變更狀態時列印主控台的更新](application-lifecycle-demo-images/image4.png)](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>逐步解說

1. 在**LifecycleDemo**方案中開啟**生命週期**專案。
1. 開啟 `AppDelegate` 類別。 記錄已新增至生命週期方法，以指出應用程式何時變更狀態：

    ```csharp
    public override void OnActivated(UIApplication application)
    {
        Console.WriteLine("OnActivated called, App is active.");
    }
    public override void WillEnterForeground(UIApplication application)
    {
        Console.WriteLine("App will enter foreground");
    }
    public override void OnResignActivation(UIApplication application)
    {
        Console.WriteLine("OnResignActivation called, App moving to inactive state.");
    }
    public override void DidEnterBackground(UIApplication application)
    {
        Console.WriteLine("App entering background state.");
    }
    // not guaranteed that this will run
    public override void WillTerminate(UIApplication application)
    {
        Console.WriteLine("App is terminating.");
    }
    ```

1. 在模擬器或裝置上啟動應用程式。 `OnActivated` 將在應用程式啟動時呼叫。 應用程式_現在處於作用中狀態。_
1. 點擊模擬器或裝置上的 [首頁] 按鈕，讓應用程式進入背景。 `OnResignActivation` 而且 `DidEnterBackground` 將會在應用程式從轉換 `Active` 為 `Inactive` 和進入狀態時呼叫 `Backgrounded` 。 因為沒有任何應用程式程式碼會設定為在背景中執行，所以應用程式在記憶體中會被視為已 _暫_ 止。
1. 流覽回應用程式，使其回到前景。 `WillEnterForeground` 而且 `OnActivated` 都會被呼叫：

    ![將狀態變更列印到主控台](application-lifecycle-demo-images/image4.png)

    當應用程式從背景進入前景並變更畫面上顯示的文字時，就會執行 view controller 中的下列程式程式碼：

    ```csharp
    UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
        label.Text = "Welcome back!";
    });
    ```

1. 按 [ **首頁** ] 按鈕，將應用程式放到背景中。 然後，按兩下 [ **首頁** ] 按鈕，以顯示應用程式切換器。 在 iPhone X 上，從畫面底部向上滑動：

    [![應用程式切換器](application-lifecycle-demo-images/app-switcher-sml.png "應用程式切換器")](application-lifecycle-demo-images/app-switcher.png#lightbox)
  
1. 在應用程式切換器中找出應用程式，然後向上滑動以移除 iOS 11 上的 (，長按直到在角落) 出現紅色圖示為止：

    [![向上滑動以移除正在執行的應用程式](application-lifecycle-demo-images/app-switcher-swipe-sml.png "向上滑動以移除正在執行的應用程式")](application-lifecycle-demo-images/app-switcher-swipe.png#lightbox)

iOS 會終止應用程式。 請注意， `WillTerminate` 不會呼叫，因為應用程式已經在背景中 _暫停_ 。

## <a name="related-links"></a>相關連結

- [LifecycleDemo (範例) ](/samples/xamarin/ios-samples/lifecycledemo)