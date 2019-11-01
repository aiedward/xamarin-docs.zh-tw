---
title: IOS 中的推播通知
description: 本檔說明如何在 iOS 9 和更早版本中使用推播通知。 其中討論憑證、向 Apple 推播通知閘道服務（APNS）註冊等等。
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 468d0e16a3bd5745a243b2d7c09e642e3aeffd1d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031372"
---
# <a name="push-notifications-in-ios"></a>IOS 中的推播通知

> [!IMPORTANT]
> 本節中的資訊適用于 iOS 9 和之前的版本，它已留在這裡以支援舊版 iOS。 針對 iOS 10 和更新版本，請參閱[使用者通知架構指南](~/ios/platform/user-notifications/index.md)，以在 ios 裝置上同時支援本機和遠端通知。

推播通知應該保持簡短，而且只包含足夠的資料來通知行動應用程式，它應該與伺服器應用程式連線以進行更新。 例如，當新的電子郵件送達時，伺服器應用程式只會通知行動應用程式新的電子郵件已抵達。 通知不會包含新的電子郵件本身。 然後，行動應用程式會從伺服器取得適當的新電子郵件

IOS 中推播通知的中心是*Apple 推播通知閘道服務（APNS）* 。 這是 Apple 提供的一項服務，負責將來自應用程式伺服器的通知路由傳送至 iOS 裝置。
下圖說明適用于 iOS 的推播通知拓撲：![](remote-notifications-in-ios-images/image4.png "此圖說明適用于 iOS 的推播通知拓撲")

遠端通知本身是 JSON 格式字串，遵循 [IOS 開發人員檔](https://developer.apple.com/devcenter/ios/index.action)中[本機和推播通知程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)的[通知承載](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1)一節中所指定的格式和通訊協定。

Apple 會維護兩個 APNS 的環境：一個是*沙箱*和一個*生產*環境。 沙箱環境適用于在開發階段進行測試，而且可以在 TCP 通訊埠2195上的 `gateway.sandbox.push.apple.com` 找到。 生產環境是要在已部署的應用程式中使用，而且可以在 TCP 通訊埠2195上的 `gateway.push.apple.com` 中找到。

## <a name="requirements"></a>需求

推播通知必須觀察 APNS 的架構所規定的下列規則：

- **256 位元組訊息限制**-通知的整個訊息大小不得超過256個位元組。
- **沒有回條確認**-APNS 不會為傳送者提供訊息傳送給預定收件者的任何通知。 如果無法連線到裝置，且已傳送多個順序通知，則除了最近的所有通知都將遺失。 只有最新通知會傳遞至裝置。
- **每個應用程式都需要安全憑證**-與 APNS 的通訊必須透過 SSL 來完成。

## <a name="creating-and-using-certificates"></a>建立和使用憑證

上一節所提及的每個環境都需要自己的憑證。 本節將說明如何建立憑證、將其與布建設定檔產生關聯，然後取得個人資訊交換憑證以搭配 PushSharp 使用。

1. 若要建立憑證，請移至 Apple 網站上的 iOS 布建入口網站，如下列螢幕擷取畫面所示（請注意左側的 [應用程式識別碼] 功能表項目）：

    [![](remote-notifications-in-ios-images/image5new.png "The iOS Provisioning Portal on Apples website")](remote-notifications-in-ios-images/image5new.png#lightbox)

2. 接下來，流覽至 [應用程式識別碼] 區段，並建立新的應用程式識別碼，如下列螢幕擷取畫面所示：

    [![](remote-notifications-in-ios-images/image6new.png "Navigate to the App IDs section and create a new app ID")](remote-notifications-in-ios-images/image6new.png#lightbox)

3. 當您按一下 **+**  按鈕，您將能夠應用程式識別碼中，輸入描述和套件組合識別碼，在下一步 的螢幕擷取畫面所示：

    [![](remote-notifications-in-ios-images/image7new.png "Enter the description and a Bundle Identifier for the app ID")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. 請務必選取 [**明確的應用程式**識別碼]，而 [套件組合識別碼] 結尾不會是 `*`。 這會建立適用于多個應用程式的識別碼，而推播通知憑證必須適用于單一應用程式。

5. 在 [應用程式服務] 底下，選取 [**推播通知**]：

    [![](remote-notifications-in-ios-images/image8new.png "Select Push Notifications")](remote-notifications-in-ios-images/image8new.png#lightbox)

6. 然後按下 [**提交**] 來確認註冊新的應用程式識別碼：

    [![](remote-notifications-in-ios-images/image9new.png "Confirm registration of the new App ID")](remote-notifications-in-ios-images/image9new.png#lightbox)

7. 接下來，您必須建立應用程式識別碼的憑證。 在左側導覽中，流覽至 [**憑證] > [全部**]，然後選取 [`+`] 按鈕，如下列螢幕擷取畫面所示：

    [![](remote-notifications-in-ios-images/image10new.png "Create the certificate for the app ID")](remote-notifications-in-ios-images/image8.png#lightbox)

8. 選取您是否想要使用開發或實際執行憑證：

    [![](remote-notifications-in-ios-images/image11new.png "Select a Development or Production certificate")](remote-notifications-in-ios-images/image11new.png#lightbox)

9. 然後選取剛才建立的新應用程式識別碼：

    [![](remote-notifications-in-ios-images/image12new.png "Select the new App ID just created")](remote-notifications-in-ios-images/image12new.png#lightbox)

10. 這會顯示指示，引導您使用 Mac 上的**Keychain Access**應用程式來建立*憑證簽署要求*。

11. 既然已建立憑證，就必須在建立程式中使用它來簽署應用程式，讓它可以向 APNs 註冊。 這需要建立並安裝使用憑證的布建設定檔。

12. 若要建立開發布建設定檔，請流覽至 [布建**設定檔**] 區段，並遵循步驟來建立它，並使用我們剛才建立的應用程式識別碼。

13. 建立布建設定檔之後，請開啟**Xcode 召集人**並加以重新整理。 如果您建立的布建設定檔未出現，可能需要從 iOS 布建入口網站下載設定檔，並手動將它匯入。 下列螢幕擷取畫面顯示已新增布建設定檔的召集人範例：  
    [![](remote-notifications-in-ios-images/image13new.png "This screen shot shows an example of the Organizer with the provision profile added")](remote-notifications-in-ios-images/image13new.png#lightbox)

14. 此時，我們必須將 Xamarin iOS 專案設定為使用這個新建立的布建設定檔。 這是從 [IOS 套件組合**簽署**] 索引標籤下的 [**專案選項**] 對話方塊中完成，如下列螢幕擷取畫面所示：  
    [![](remote-notifications-in-ios-images/image11.png "Configure the Xamarin.iOS project to use this newly created provisioning profile")](remote-notifications-in-ios-images/image11.png#lightbox)

此時，應用程式會設定為使用推播通知。 不過，憑證仍然需要幾個步驟。 此憑證是與 PushSharp 不相容的 DER 格式，其需要個人資訊交換（PKCS12）憑證。 若要轉換憑證，使其可供 PushSharp 使用，請執行下列最後步驟：

1. **下載憑證**檔案-登入 iOS 布建入口網站，選擇 [憑證] 索引標籤，選取與正確的布建設定檔相關聯的憑證，然後選擇 [**下載**]。
1. **Open Keychain Access** -這是「應用程式」是 OS X 中密碼管理系統的 GUI 介面。
1. 匯**入憑證**-如果尚未安裝憑證，檔案 **.。。** 從 [Keychain 存取] 功能表匯入專案。 流覽至上面匯出的憑證，然後選取它。
1. **匯出憑證**-展開憑證，使相關聯的私密金鑰可見，並以滑鼠右鍵按一下金鑰，然後選擇 [匯出]。 系統會提示您輸入所匯出檔案的檔案名和密碼。

到目前為止，我們都是使用憑證完成。 我們已建立憑證，用來簽署 iOS 應用程式，並將該憑證轉換成可在伺服器應用程式中搭配 PushSharp 使用的格式。 接下來，我們將探討 iOS 應用程式如何與 APNS 互動。

## <a name="registering-with-apns"></a>向 APNS 註冊

在 iOS 應用程式可以接收遠端通知之前，它必須向 APNS 註冊。 APNS 會產生唯一的裝置權杖，並將其傳回至 iOS 應用程式。 然後 iOS 應用程式會採用裝置權杖，然後向應用程式伺服器註冊其本身。 一旦發生這種情況，註冊便已完成，應用程式伺服器可能會將通知推送至行動裝置。

理論上，每次 iOS 應用程式向 APNS 註冊本身時，裝置權杖可能會變更，但實際上不會發生這種情況。 做為優化，應用程式可能會快取最新的裝置權杖，而且只會在應用程式伺服器進行變更時進行更新。 下圖說明註冊和取得裝置權杖的程式：

 ![](remote-notifications-in-ios-images/image12.png "This diagram illustrates the process of registration and obtaining a device token")

藉由在目前的 `UIApplication` 物件上呼叫 `RegisterForRemoteNotificationTypes`，在應用程式委派類別的 `FinishedLaunching` 方法中處理 APNS 的註冊。 當 iOS 應用程式向 APNS 註冊時，它也必須指定要接收的遠端通知類型。 這些遠端通知類型會在列舉 `UIRemoteNotificationType`中宣告。 下列程式碼片段是 iOS 應用程式可以註冊以接收遠端警示和徽章通知的範例：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
    var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

    UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications ();
} else {
    UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
    UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
}
```

APNS 註冊要求會在背景中進行-收到回應時，iOS 會呼叫 `AppDelegate` 類別中 `RegisteredForRemoteNotifications` 的方法，並傳遞已註冊的裝置權杖。 權杖會包含在 `NSData` 物件中。 下列程式碼片段示範如何取得 APNS 提供的裝置權杖：

```csharp
public override void RegisteredForRemoteNotifications (
UIApplication application, NSData deviceToken)
{
    // Get current device token
    var DeviceToken = deviceToken.Description;
    if (!string.IsNullOrWhiteSpace(DeviceToken)) {
        DeviceToken = DeviceToken.Trim('<').Trim('>');
    }

    // Get previous device token
    var oldDeviceToken = NSUserDefaults.StandardUserDefaults.StringForKey("PushDeviceToken");

    // Has the token changed?
    if (string.IsNullOrEmpty(oldDeviceToken) || !oldDeviceToken.Equals(DeviceToken))
    {
        //TODO: Put your own logic here to notify your server that the device token has changed/been created!
    }

    // Save new device token
    NSUserDefaults.StandardUserDefaults.SetString(DeviceToken, "PushDeviceToken");
}
```

如果註冊因某種原因而失敗（例如裝置未連線到網際網路），iOS 將會在應用程式委派類別上呼叫 `FailedToRegisterForRemoteNotifications`。 下列程式碼片段顯示如何向使用者顯示警示，通知他們註冊失敗：

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>裝置權杖維護

裝置權杖會在一段時間後過期或變更。 因此，伺服器應用程式必須執行一些房屋清理，並清除這些已過期或已變更的權杖。 當應用程式以推播通知形式傳送至具有過期權杖的裝置時，APNS 會記錄並儲存該過期的權杖。 接著，伺服器可以查詢 APNS 以找出已過期的權杖。

用來提供意見反應*服務*的 APNS-HTTPS 端點，會透過建立來傳送推播通知的憑證進行驗證，並傳回有關哪些權杖已過期的資料。 Apple 已淘汰此程式並加以移除。

而是由意見服務先前回報的案例有新的 HTTP 狀態碼：

> 410-主題的裝置權杖已不再有效。

此外，回應主體中會有新的 `timestamp` JSON 資料金鑰：

> 如果： status 標頭中的值是410，則此索引鍵的值是上次 APNs 確認該主題的裝置權杖不再有效的時間。
>
> 停止推播通知，直到裝置向您的提供者註冊具有較新時間戳記的權杖。

## <a name="summary"></a>總結

本節介紹 iOS 中推播通知的重要概念。 它說明了 Apple Push Notification Gateway 服務（APNS）的角色。 接著涵蓋建立和使用 APNS 所不可或缺的安全性憑證。 最後，這份檔已完成討論應用程式伺服器如何使用意見反應*服務*來停止追蹤過期的裝置權杖。

## <a name="related-links"></a>相關連結

- [通知-示範本機和遠端通知（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/notifications)
- [開發人員的本機和推播通知](https://developer.apple.com/notifications/)
- [UIApplication](https://docs.microsoft.com/dotnet/api/uikit.uiapplication)
- [UIRemoteNotificationType](https://docs.microsoft.com/dotnet/api/uikit.UIRemoteNotificationType)
