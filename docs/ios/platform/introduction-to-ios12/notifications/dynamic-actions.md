---
title: 在 Xamarin.iOS 中的動態通知動作按鈕
description: Ios 12、 notification content 延伸模組可以新增、 移除及更新與通知一起顯示的 [動作] 按鈕。 本文件說明如何使用 Xamarin.iOS 中的動態通知動作按鈕。
ms.prod: xamarin
ms.assetid: 6B34AD78-5117-42D0-B6E7-C8B4B453EAFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 5611d673ecc7af896fd3a9e566e184e408b6b367
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60876054"
---
# <a name="dynamic-notification-action-buttons-in-xamarinios"></a>在 Xamarin.iOS 中的動態通知動作按鈕

在 iOS 12 中，通知可以動態地新增、 移除及更新其相關聯的動作按鈕。 這種自訂可讓您能夠為使用者提供通知的內容與使用者互動直接相關的動作。

## <a name="sample-app-redgreennotifications"></a>範例應用程式：RedGreenNotifications

本指南中的程式碼片段來自[RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)範例應用程式，示範如何使用 Xamarin.iOS 搭配 iOS 12 中的通知動作按鈕。

此範例應用程式傳送本機通知的兩種： 紅色、 綠色。
應用程式傳送通知之後，請以檢視其自訂使用者介面中使用 3D 觸控。 然後，使用通知的 [動作] 按鈕以旋轉它所顯示的映像。 影像會旋轉，如**重設旋轉**按鈕隨即出現，並視就會消失。

本指南中的程式碼片段來自此範例應用程式。

## <a name="default-action-buttons"></a>預設動作按鈕

通知的類別會決定其預設動作按鈕。

建立並註冊通知的類別，而啟動應用程式。
例如，在[範例應用程式](#sample-app-redgreennotifications)，則`FinishedLaunching`方法`AppDelegate`會進行下列作業：

- 定義一個類別，紅色的通知，而另一個綠色的通知
- 藉由呼叫註冊這些類別 [`SetNotificationCategories`](xref:UserNotifications.UNUserNotificationCenter.SetNotificationCategories*)
方法 `UNUserNotificationCenter`
- 會附加單一 [`UNNotificationAction`](xref:UserNotifications.UNNotificationAction)
每個類別目錄

下列範例程式碼會示範其運作方式：

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

其依據此程式碼中，任何通知 [`Content.CategoryIdentifier`](xref:UserNotifications.UNNotificationContent.CategoryIdentifier)
「 紅色類別目錄 」 或 「 綠類別目錄 」 將根據預設，顯示**旋轉 20 °**動作按鈕。

## <a name="in-app-handling-of-notification-action-buttons"></a>通知動作按鈕的應用程式內處理

`UNUserNotificationCenter` 已`Delegate`型別的屬性[ `IUNUserNotificationCenterDelegate` ](xref:UserNotifications.IUNUserNotificationCenterDelegate)。

範例應用程式中`AppDelegate`本身設為 使用者通知中心中的委派`FinishedLaunching`:

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

然後，`AppDelegate`實作 [`DidReceiveNotificationResponse`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.DidReceiveNotificationResponse*)
若要處理的動作按鈕點選：

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

這個實作`DidReceiveNotificationResponse`不會處理通知**旋轉 20 °**動作按鈕。 相反地，notification content 延伸模組會處理這個按鈕上的點選。 下一節進一步討論通知動作按鈕處理。

## <a name="action-buttons-in-the-notification-content-extension"></a>Notification content 延伸模組中的 [動作] 按鈕

Notification content 延伸模組包含定義通知的自訂介面檢視控制器。

可以使用此檢視控制器`GetNotificationActions`和`SetNotificationActions`方法在其 [`ExtensionContext`](xref:UIKit.UIViewController.ExtensionContext)
若要存取和修改通知的動作按鈕的屬性。

範例應用程式中 notification content 延伸模組的檢視控制器上現有的動作按鈕點選回應時，才修改動作按鈕。

> [!NOTE]
> 通知內容延伸模組可以回應在其檢視控制器的動作按鈕點選[ `DidReceiveNotificationResponse` ](xref:UserNotificationsUI.UNNotificationContentExtension_Extensions.DidReceiveNotificationResponse*)方法，宣告為部分[IUNNotificationContentExtension](xref:UserNotificationsUI.IUNNotificationContentExtension)。
>
> 雖然它共用名稱與`DidReceiveNotificationResponse`方法[上述](#in-app-handling-of-notification-action-buttons)，這是不同的方法。
>
> Notification content 延伸模組可讓您完成處理只要點選按鈕之後，它可以選擇要告知主應用程式來處理該相同點選的按鈕。 若要這樣做，它必須通過了適當的值[UNNotificationContentExtensionResponseOption](xref:UserNotificationsUI.UNNotificationContentExtensionResponseOption)至其完成處理常式：
>
> - `Dismiss` 表示應關閉通知介面項目，而且主要應用程式不需要處理點選按鈕。
> - `DismissAndForwardAction` 表示應關閉通知介面項目，而且主要應用程式也應該處理點選按鈕。
> - `DoNotDismiss` 指出，應該不會關閉通知介面，以及主要應用程式不需要處理點選按鈕。

內容的延伸模組`DidReceiveNotificationResponse`方法會決定哪一個動作點選按鈕，在通知的介面，以及顯示或隱藏影像旋轉**重設**動作按鈕：

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

在此情況下，此方法會傳遞`UNNotificationContentExtensionResponseOption.DoNotDismiss`至其完成處理常式。 這表示，通知的介面會保持開啟狀態。

## <a name="related-links"></a>相關連結

- [範例應用程式 – RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [在 Xamarin.iOS 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [宣告可採取動作的通知類型](https://developer.apple.com/documentation/usernotifications/declaring_your_actionable_notification_types?language=objc)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [在 使用者通知 (WWDC 2018) 最新消息](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知 (WWDC 2017) 在最新消息](https://developer.apple.com/videos/play/wwdc2017/708/)
- [產生遠端通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
