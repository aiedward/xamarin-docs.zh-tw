---
title: Xamarin 中的動態通知動作按鈕
description: 使用 iOS 12，通知內容延伸模組可以新增、移除和更新在通知旁顯示的動作按鈕。 本檔說明如何搭配使用動態通知動作按鈕與 Xamarin。
ms.prod: xamarin
ms.assetid: 6B34AD78-5117-42D0-B6E7-C8B4B453EAFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: cb38d222cecd1a6c5bb65b0fb376888770dd0e49
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031964"
---
# <a name="dynamic-notification-action-buttons-in-xamarinios"></a>Xamarin 中的動態通知動作按鈕

在 iOS 12 中，通知可以動態地新增、移除和更新其相關聯的動作按鈕。 這類自訂可以提供使用者與通知內容直接相關的動作，以及使用者與它的互動。

## <a name="sample-app-redgreennotifications"></a>範例應用程式： RedGreenNotifications

本指南中的程式碼片段來自[RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)範例應用程式，示範如何使用 Xamarin 來處理 ios 12 中的通知動作按鈕。

這個範例應用程式會傳送兩種類型的本機通知：紅色和綠色。
應用程式傳送通知之後，請使用 3D Touch 來查看其自訂使用者介面。 然後，使用通知的 [動作] 按鈕來旋轉它所顯示的影像。 隨著影像旋轉，會出現 [**重設旋轉**] 按鈕，並視需要消失。

本指南中的程式碼片段來自此範例應用程式。

## <a name="default-action-buttons"></a>預設動作按鈕

通知的類別會決定其預設動作按鈕。

在應用程式啟動時建立並註冊通知類別。
例如，在[範例應用程式](#sample-app-redgreennotifications)中，`AppDelegate` 的 `FinishedLaunching` 方法會執行下列動作：

- 定義一個紅色通知類別，另一個用於綠色通知
- 藉由呼叫[`SetNotificationCategories`](xref:UserNotifications.UNUserNotificationCenter.SetNotificationCategories*)來註冊這些類別
`UNUserNotificationCenter` 的方法
- 附加單一[`UNNotificationAction`](xref:UserNotifications.UNNotificationAction)
對每個類別

下列範例程式碼說明其運作方式：

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

根據此程式碼，任何[`Content.CategoryIdentifier`](xref:UserNotifications.UNNotificationContent.CategoryIdentifier)的通知
為「紅色類別」或「綠色類別」，預設會顯示 [**旋轉20°** ] 動作按鈕。

## <a name="in-app-handling-of-notification-action-buttons"></a>通知動作按鈕的應用程式內處理

`UNUserNotificationCenter` 具有類型[`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate)的 `Delegate` 屬性。

在範例應用程式中，`AppDelegate` 會在 `FinishedLaunching`中將自己設定為使用者通知中心的委派：

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

然後，`AppDelegate` 會執行[`DidReceiveNotificationResponse`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.DidReceiveNotificationResponse*)
若要處理動作按鈕點擊：

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

這項 `DidReceiveNotificationResponse` 的執行並不會處理通知的 [**旋轉20°** ] 動作按鈕。 相反地，通知的內容延伸會處理此按鈕的點擊。 下一節會進一步討論通知動作按鈕的處理。

## <a name="action-buttons-in-the-notification-content-extension"></a>通知內容延伸中的動作按鈕

通知內容延伸包含定義通知自訂介面的視圖控制器。

此視圖控制器可以在其[`ExtensionContext`](xref:UIKit.UIViewController.ExtensionContext)上使用 `GetNotificationActions` 和 `SetNotificationActions` 方法
用來存取和修改通知之動作按鈕的屬性。

在範例應用程式中，通知內容延伸模組的 view controller 只會在回應已存在的動作按鈕時，修改動作按鈕。

> [!NOTE]
> 通知內容延伸模組可以回應動作按鈕，在其 view controller 的[`DidReceiveNotificationResponse`](xref:UserNotificationsUI.UNNotificationContentExtension_Extensions.DidReceiveNotificationResponse*)方法中，宣告為[IUNNotificationContentExtension](xref:UserNotificationsUI.IUNNotificationContentExtension)的一部分。
>
> 雖然它會與[上面所述](#in-app-handling-of-notification-action-buttons)的 `DidReceiveNotificationResponse` 方法共用名稱稱，但這是不同的方法。
>
> 在通知內容延伸完成處理按鈕點後，可以選擇是否要告訴主應用程式處理該相同的按鈕。 若要這樣做，它必須將適當的[UNNotificationContentExtensionResponseOption](xref:UserNotificationsUI.UNNotificationContentExtensionResponseOption)值傳遞給其完成處理常式：
>
> - `Dismiss` 表示應關閉通知介面，而且主要應用程式不需要處理按鈕點按。
> - `DismissAndForwardAction` 表示應關閉通知介面，而且主應用程式也應該處理按鈕點按。
> - `DoNotDismiss` 表示不應關閉通知介面，而且主要應用程式不需要處理按鈕點按。

內容延伸的 `DidReceiveNotificationResponse` 方法會決定已按下的動作按鈕、在通知的介面中旋轉影像，以及顯示或隱藏 [**重設**動作] 按鈕：

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

在此情況下，方法會將 `UNNotificationContentExtensionResponseOption.DoNotDismiss` 傳遞至其完成處理常式。 這表示通知的介面會保持開啟狀態。

## <a name="related-links"></a>相關連結

- [範例應用程式-RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Xamarin 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [宣告可採取動作的通知類型](https://developer.apple.com/documentation/usernotifications/declaring_your_actionable_notification_types?language=objc)
- [UserNotifications （Apple）](https://developer.apple.com/documentation/usernotifications?language=objc)
- [使用者通知的新功能（WWDC 2018）](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知的新功能（WWDC 2017）](https://developer.apple.com/videos/play/wwdc2017/708/)
- [產生遠端通知（Apple）](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
