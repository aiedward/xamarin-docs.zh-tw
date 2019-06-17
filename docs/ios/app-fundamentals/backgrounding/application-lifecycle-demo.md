---
title: 適用於 Xamarin.iOS 應用程式生命週期示範
description: 這份文件會檢查應用程式委派的 iOS 應用程式，示範何時及如何處理這些事件所處理的各種生命週期事件。
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/17/2018
ms.openlocfilehash: 3beb511c03b328ecea824bf89355d056df003f3e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946160"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>適用於 Xamarin.iOS 應用程式生命週期示範

這篇文章並[程式碼範例](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)示範如何在 iOS 中，四個應用程式狀態和角色`AppDelegate`中通知的狀態變更當應用程式的方法。 每當應用程式會將狀態變更時，應用程式會列印到主控台的更新：

[![](application-lifecycle-demo-images/image3-sml.png "範例應用程式")](application-lifecycle-demo-images/image3.png#lightbox)

[![](application-lifecycle-demo-images/image4.png "每當應用程式會將狀態變更時，應用程式會列印到主控台的更新")](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>逐步解說

1. 開啟**生命週期**專案中**LifecycleDemo**解決方案。
1. 開啟`AppDelegate`類別。 記錄已新增至生命週期方法，以指出應用程式已變更狀態時：

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

1. 啟動應用程式在模擬器中或在裝置上。 `OnActivated` 將應用程式啟動時呼叫。 應用程式目前處於_Active_狀態。
1. 叫用模擬器或裝置重新啟動應用程式到背景工作上的 [首頁] 按鈕。 `OnResignActivation` 和`DidEnterBackground`將呼叫從應用程式轉換成`Active`要`Inactive`出入`Backgrounded`狀態。 因為若要在背景執行設定任何應用程式程式碼時，應用程式會被視為_暫止_在記憶體中。
1. 瀏覽回到應用程式，以讓它能夠重新放置到前景。 `WillEnterForeground` 和`OnActivated`會同時呼叫：

    ![](application-lifecycle-demo-images/image4.png "列印到主控台的狀態變更")

    當應用程式已進入前景與背景，並將螢幕上顯示的文字變更時，會執行下列檢視控制器中的程式碼行：

    ```csharp
    UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
        label.Text = "Welcome back!";
    });
    ```

1. 按下**首頁** 按鈕，讓應用程式在背景。 然後，點選**首頁**按鈕，即可開啟應用程式切換器。 在 iphone X，從螢幕底部向上撥動中：

    [![應用程式切換器](application-lifecycle-demo-images/app-switcher-sml.png "應用程式切換器")](application-lifecycle-demo-images/app-switcher.png#lightbox)
  
1. 在應用程式切換器中尋找應用程式，並將它移除 （在 iOS 11 日長按之前的紅色圖示會出現在角落） 向上撥動：

    [![最多移除執行中應用程式的撥動](application-lifecycle-demo-images/app-switcher-swipe-sml.png "撥動，最多移除執行中應用程式")](application-lifecycle-demo-images/app-switcher-swipe.png#lightbox)

iOS 會終止應用程式。 請注意，`WillTerminate`不會呼叫，因為應用程式已經_暫止_在背景中。

## <a name="related-links"></a>相關連結

- [LifecycleDemo （範例）](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
