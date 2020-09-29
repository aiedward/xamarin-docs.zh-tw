---
title: Xamarin 中的動態通知動作按鈕
description: 使用 iOS 12 時，通知內容延伸可以新增、移除及更新通知旁邊顯示的動作按鈕。 本檔說明如何搭配使用動態通知動作按鈕與 Xamarin. iOS。
ms.prod: xamarin
ms.assetid: 6B34AD78-5117-42D0-B6E7-C8B4B453EAFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 88eb3dec0c72acd6984b226eae5acee4feb3eaac
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433825"
---
# <a name="dynamic-notification-action-buttons-in-xamarinios"></a>Xamarin 中的動態通知動作按鈕

在 iOS 12 中，通知可以動態新增、移除及更新其相關聯的動作按鈕。 這類自訂可讓使用者提供與通知內容直接相關的動作，以及與使用者的互動。

## <a name="sample-app-redgreennotifications"></a>範例應用程式： RedGreenNotifications

本指南中的程式碼片段來自 [RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications) 範例應用程式，示範如何使用 Xamarin 來處理 ios 12 中的通知動作按鈕。

此範例應用程式會傳送兩種類型的本機通知：紅色和綠色。
應用程式傳送通知之後，請使用 3D Touch 來查看其自訂使用者介面。 然後，使用通知的動作按鈕來旋轉它所顯示的影像。 當影像旋轉時，會出現 [ **重設旋轉** ] 按鈕，並視需要消失。

本指南中的程式碼片段來自此範例應用程式。

## <a name="default-action-buttons"></a>預設動作按鈕

通知的類別會決定其預設動作按鈕。

在應用程式啟動時建立並註冊通知類別。
例如，在 [範例應用程式](#sample-app-redgreennotifications)中，的 `FinishedLaunching` 方法 `AppDelegate` 會執行下列動作：

- 為紅色通知定義一個類別，並為綠色通知定義另一個類別
- 藉由呼叫，來註冊這些類別 [`SetNotificationCategories`](xref:UserNotifications.UNUserNotificationCenter.SetNotificationCategories*)
的方法 `UNUserNotificationCenter`
- 附加單一 [`UNNotificationAction`](xref:UserNotifications.UNNotificationAction)
至每個類別

下列範例程式碼顯示其運作方式：

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional | UNAuthorizationOptions.ProvidesAppNotificationSettings;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
        var rotateTwentyDegreesAction = UNNotificationAction.FromIdentifier("rotate-twenty-degrees-action", "Rotate 20°", UNNotificationActionOptions.None);

        var redCategory = UNNotificationCategory.FromIdentifier(
            "red-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var greenCategory = UNNotificationCategory.FromIdentifier(
            "green-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var set = new NSSet<UNNotificationCategory>(redCategory, greenCategory);
        center.SetNotificationCategories(set);
    });
    // ...
}
```

根據此程式碼，任何的通知，其 [`Content.CategoryIdentifier`](xref:UserNotifications.UNNotificationContent.CategoryIdentifier)
依預設，「紅色類別」或「綠色類別」會顯示 **旋轉的20°** 動作按鈕。

## <a name="in-app-handling-of-notification-action-buttons"></a>通知動作按鈕的應用程式內處理

`UNUserNotificationCenter` 具有 `Delegate` 類型的屬性 [`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate) 。

在範例應用程式中， `AppDelegate` 將本身設定為使用者通知中心在中的委派 `FinishedLaunching` ：

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = // ...
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        center.Delegate = this;
        // ...
```

然後， `AppDelegate` 實施 [`DidReceiveNotificationResponse`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.DidReceiveNotificationResponse*)
若要處理動作按鈕，請點擊：

```csharp
[Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
{
    if (response.IsDefaultAction)
    {
        Console.WriteLine("ACTION: Default");
    }
    if (response.IsDismissAction)
    {
        Console.WriteLine("ACTION: Dismiss");
    }
    else
    {
        Console.WriteLine($"ACTION: {response.ActionIdentifier}");
    }

    completionHandler();
        }
```

的這個執行不 `DidReceiveNotificationResponse` 會處理通知的 **旋轉20°** 動作按鈕。 相反地，通知的內容延伸模組會處理此按鈕的點擊。 下一節將進一步討論通知動作按鈕的處理。

## <a name="action-buttons-in-the-notification-content-extension"></a>通知內容延伸中的動作按鈕

通知內容延伸包含定義通知自訂介面的查看控制器。

此 view controller 可 `GetNotificationActions` `SetNotificationActions` 在其上使用和方法 [`ExtensionContext`](xref:UIKit.UIViewController.ExtensionContext)
屬性，用來存取和修改通知的動作按鈕。

在範例應用程式中，通知內容延伸的查看控制器只會在回應已存在的動作按鈕時修改動作按鈕。

> [!NOTE]
> 通知內容延伸可以回應動作按鈕，在其 view 控制器的 [`DidReceiveNotificationResponse`](xref:UserNotificationsUI.UNNotificationContentExtension_Extensions.DidReceiveNotificationResponse*) 方法中，宣告為 [IUNNotificationContentExtension](xref:UserNotificationsUI.IUNNotificationContentExtension)的一部分。
>
> 雖然它會與 `DidReceiveNotificationResponse` [上面所述](#in-app-handling-of-notification-action-buttons)的方法共用名稱稱，但這是不同的方法。
>
> 通知內容延伸程式完成按鈕點的處理之後，就可以選擇是否要讓主應用程式處理相同的按鈕點。 若要這樣做，它必須傳遞適當的 [UNNotificationContentExtensionResponseOption](xref:UserNotificationsUI.UNNotificationContentExtensionResponseOption) 值給它的完成處理常式：
>
> - `Dismiss` 表示應關閉通知介面，且主要應用程式不需要處理按鈕的按鍵。
> - `DismissAndForwardAction` 表示應關閉通知介面，且主要應用程式也應該處理按鈕的點一下。
> - `DoNotDismiss` 指出不應該關閉通知介面，且主要應用程式不需要處理按鈕的按鍵。

內容延伸的 `DidReceiveNotificationResponse` 方法會決定所要點擊的動作按鈕、在通知的介面中旋轉影像，以及顯示或隱藏 **重設** 動作按鈕：

```csharp
[Export("didReceiveNotificationResponse:completionHandler:")]
public void DidReceiveNotificationResponse(UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
{
    var rotationAction = ExtensionContext.GetNotificationActions()[0];

    if (response.ActionIdentifier == "rotate-twenty-degrees-action")
    {
        rotationButtonTaps += 1;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        // 9 rotations * 20 degrees = 180 degrees. No reason to
        // show the reset rotation button when the image is half
        // or fully rotated.
        if (rotationButtonTaps % 9 == 0)
        {
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
        }
        else if (rotationButtonTaps % 9 == 1)
        {
            var resetRotationAction = UNNotificationAction.FromIdentifier("reset-rotation-action", "Reset rotation", UNNotificationActionOptions.None);
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction, resetRotationAction });
        }
    }

    if (response.ActionIdentifier == "reset-rotation-action")
    {
        rotationButtonTaps = 0;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
    }

    completionHandler(UNNotificationContentExtensionResponseOption.DoNotDismiss);
}
```

在此情況下，方法會傳遞 `UNNotificationContentExtensionResponseOption.DoNotDismiss` 至完成處理常式。 這表示通知的介面會保持開啟。

## <a name="related-links"></a>相關連結

- [範例應用程式-RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Xamarin 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [宣告可操作的通知類型](https://developer.apple.com/documentation/usernotifications/declaring_your_actionable_notification_types?language=objc)
- [UserNotifications (Apple) ](https://developer.apple.com/documentation/usernotifications?language=objc)
- [使用者通知的新功能 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知的新功能 (WWDC 2017) ](https://developer.apple.com/videos/play/wwdc2017/708/)
- [ (Apple) 產生遠端通知 ](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)