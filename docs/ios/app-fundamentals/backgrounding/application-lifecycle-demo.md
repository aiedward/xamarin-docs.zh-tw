---
title: Xamarin.iOS 的應用程式生命週期示範
description: 這份文件會檢查各種生命週期事件處理應用程式中的委派 iOS 應用程式，來示範這些事件處理的時機和方式。
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 64c695065012e4bf796c219c260324d9b6278ca5
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783580"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>Xamarin.iOS 的應用程式生命週期示範

在本節中，我們會檢查應用程式，示範四個應用程式狀態，以及所扮演的角色`AppDelegate`通知時變更狀態的應用程式中的方法。 每當應用程式會將狀態變更時，應用程式會列印到主控台的更新：

 [![](application-lifecycle-demo-images/image3.png "範例應用程式")](application-lifecycle-demo-images/image3.png#lightbox)

 [![](application-lifecycle-demo-images/image4.png "每當應用程式會將狀態變更時，應用程式會列印到主控台的更新")](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>逐步解說


  1. 開啟_生命週期_專案中_LifecycleDemo_方案。
  1. 開啟 `AppDelegate`類別。 請注意，我們已加入記錄存留週期方法，讓我們知道應用程式變更狀態時：

            ```chsarp
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

  1. 啟動應用程式在模擬器或裝置上。 `OnActivated` 將應用程式啟動時呼叫。 應用程式目前處於_Active_狀態。
  1. 叫用模擬器或裝置重新啟動的背景應用程式的 [首頁] 按鈕。 `OnResignActivation` 和`DidEnterBackground`將呼叫從應用程式轉換為`Active`至`Inactive`到`Backgrounded`狀態。 因為我們沒有提供我們的應用程式要在背景中執行的任何程式碼，應用程式會被視為_Suspended_在記憶體中。
  1. 瀏覽回到應用程式，讓它回到前景。 `WillEnterForeground` 和`OnActivated`會同時呼叫：

        ![](application-lifecycle-demo-images/image4.png "列印到主控台的狀態變更")

    請注意，我們加入下列程式碼行通知我們，應用程式已進入前景與背景我們檢視控制器：

        ```csharp
            UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
                    label.Text = "Welcome back!";
                });
        ```

1. 按**首頁** 按鈕，將在背景應用程式。 然後，點選**首頁**按鈕即可啟動應用程式切換器：
    
    ![](application-lifecycle-demo-images/app-switcher-.png "應用程式切換器")
  
1. 應用程式中找到應用程式切換器，並將它移除向上撥動：
    
    ![](application-lifecycle-demo-images/app-switcher-swipe-.png "若要移除執行中應用程式中向上撥動") 
    
iOS 會終止該應用程式。 請注意，`WillTerminate`因為我們會終止的應用程式不會呼叫_Suspended_在背景中。

既然我們了解 iOS 應用程式狀態和轉換，我們將探討可用 backgrounding 在 iOS 中的不同選項。



## <a name="related-links"></a>相關連結

- [LifecycleDemo(Part2) （範例）](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
