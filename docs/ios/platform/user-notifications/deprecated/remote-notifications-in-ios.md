---
title: IOS 中的推播通知
description: 本檔說明如何在 iOS 9 和更早版本中使用推播通知。 它會討論憑證、向 Apple 推播通知閘道服務註冊 (APNS) 等等。
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: f9ef6e833fe48bb2b124f02d741a39e4141e56e6
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436362"
---
# <a name="push-notifications-in-ios"></a>IOS 中的推播通知

> [!IMPORTANT]
> 本章節中的資訊適用于 iOS 9 和更早版本，其為支援較舊的 iOS 版本。 若為 iOS 10 和更新版本，請參閱 [使用者通知架構指南](~/ios/platform/user-notifications/index.md) ，以在 ios 裝置上支援本機和遠端通知。

推播通知應該保持簡潔，而且只會包含足夠的資料，以通知行動應用程式它應該與伺服器應用程式聯繫以進行更新。 例如，當新的電子郵件送達時，伺服器應用程式只會通知行動應用程式新的電子郵件已到達。 通知不會包含新的電子郵件。 然後，行動應用程式會在適當時從伺服器中取出新的電子郵件

IOS 推播通知的中心是 *Apple 推播通知閘道服務 (APNS) *。 這是 Apple 所提供的一項服務，負責將應用程式伺服器的通知路由傳送至 iOS 裝置。
下圖說明適用于 iOS 的推播通知拓撲： ![ 此圖說明適用于 ios 的推播通知拓撲](remote-notifications-in-ios-images/image4.png)

遠端通知本身是 JSON 格式的字串，其遵循[iOS 開發人員檔](https://developer.apple.com/devcenter/ios/index.action)中《[本機和推播通知程式設計指南》](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)的「[通知](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1)承載」一節中所指定的格式和通訊協定。

Apple 會維護兩個 APNS 環境： *沙箱* 和 *生產* 環境。 沙箱環境適用于開發階段中的測試，而且可以在 `gateway.sandbox.push.apple.com` TCP 通訊埠2195上找到。 生產環境適用于已部署的應用程式，而且可以在 `gateway.push.apple.com` TCP 埠2195上找到。

## <a name="requirements"></a>需求

推播通知必須觀察 APNS 架構所規定的下列規則：

- **256 位元組訊息限制** -通知的整個訊息大小不得超過256個位元組。
- **無回條確認** -APNS 不會提供寄件者任何訊息給預定收件者的通知。 如果無法連線到裝置，而且傳送了多個連續通知，則除了最新的所有通知都將遺失。 只有最新的通知會傳遞至裝置。
- **每個應用程式都需要安全的憑證** -必須透過 SSL 來與 APNS 進行通訊。

## <a name="creating-and-using-certificates"></a>建立和使用憑證

上一節所述的每個環境都需要自己的憑證。 本節將說明如何建立憑證、將憑證與布建設定檔產生關聯，然後取得要與 PushSharp 搭配使用的個人資訊交換憑證。

1. 若要建立憑證，請前往 Apple 網站上的 iOS 布建入口網站，如下列螢幕擷取畫面所示 (請注意左側) 上的 [應用程式識別碼] 功能表項目：

    [![蘋果網站上的 iOS 布建入口網站](remote-notifications-in-ios-images/image5new.png)](remote-notifications-in-ios-images/image5new.png#lightbox)

2. 接下來，流覽至 [應用程式識別碼] 區段，並建立新的應用程式識別碼，如下列螢幕擷取畫面所示：

    [![流覽至 [應用程式識別碼] 區段，並建立新的應用程式識別碼](remote-notifications-in-ios-images/image6new.png)](remote-notifications-in-ios-images/image6new.png#lightbox)

3. 當您按一下按鈕時 **+** ，您將能夠輸入應用程式識別碼的描述和套件組合識別碼，如下一個螢幕擷取畫面所示：

    [![輸入應用程式識別碼的描述和套件組合識別碼](remote-notifications-in-ios-images/image7new.png)](remote-notifications-in-ios-images/image7new.png#lightbox)

4. 請務必選取 **明確的應用程式** 識別碼，且套件組合識別碼的結尾不是  `*` 。 這將會建立適用于多個應用程式的識別碼，且必須針對單一應用程式使用推播通知憑證。

5. 在 [應用程式服務] 下，選取 [ **推播通知**]：

    [![選取推播通知](remote-notifications-in-ios-images/image8new.png)](remote-notifications-in-ios-images/image8new.png#lightbox)

6. 然後按下 [ **提交** ] 以確認註冊新的應用程式識別碼：

    [![確認註冊新的應用程式識別碼](remote-notifications-in-ios-images/image9new.png)](remote-notifications-in-ios-images/image9new.png#lightbox)

7. 接下來，您必須建立應用程式識別碼的憑證。 在左側導覽中，流覽至 [ **憑證] > [全部** ]，然後選取 `+` 按鈕，如下列螢幕擷取畫面所示：

    [![建立應用程式識別碼的憑證](remote-notifications-in-ios-images/image10new.png)](remote-notifications-in-ios-images/image8.png#lightbox)

8. 選取您是否要使用開發或生產憑證：

    [![選取開發或生產憑證](remote-notifications-in-ios-images/image11new.png)](remote-notifications-in-ios-images/image11new.png#lightbox)

9. 然後選取剛才建立的新應用程式識別碼：

    [![選取剛建立的新應用程式識別碼](remote-notifications-in-ios-images/image12new.png)](remote-notifications-in-ios-images/image12new.png#lightbox)

10. 這將會顯示指示，引導您完成使用 Mac 上的**Keychain Access**應用程式建立*憑證簽署要求*的程式。

11. 現在已建立憑證，您必須在組建程式中使用該憑證來簽署應用程式，使其可以向 APNs 註冊。 這需要建立和安裝使用憑證的布建設定檔。

12. 若要建立開發提供設定檔，請流覽至 [布建 **設定檔** ] 區段，並使用我們剛才建立的應用程式識別碼，依照步驟建立它。

13. 建立布建設定檔之後，請開啟  **Xcode 召集人** 並重新整理。 如果您建立的布建設定檔未出現，則可能需要從 iOS 布建入口網站下載設定檔，並手動將其匯入。 下列螢幕擷取畫面顯示已新增布建設定檔的召集人範例：  
    [![這個螢幕擷取畫面顯示已新增布建設定檔的召集人範例](remote-notifications-in-ios-images/image13new.png)](remote-notifications-in-ios-images/image13new.png#lightbox)

14. 此時，我們需要將 Xamarin 專案設定為使用這個新建立的布建設定檔。 這是在 [IOS 套件組合**簽署**] 索引標籤下的 [**專案選項**] 對話方塊中完成，如下列螢幕擷取畫面所示：  
    [![將 [Xamarin] 專案設定為使用這個新建立的布建設定檔](remote-notifications-in-ios-images/image11.png)](remote-notifications-in-ios-images/image11.png#lightbox)

此時，應用程式會設定為使用推播通知。 但是，憑證還需要進行一些步驟。 此憑證的 DER 格式與 PushSharp 不相容，這需要 (PKCS12) 憑證的個人資訊交換。 若要轉換憑證使其可供 PushSharp 使用，請執行下列最後步驟：

1. **下載憑證** 檔案-登入 iOS 布建入口網站，選擇 [憑證] 索引標籤，選取與正確布建設定檔相關聯的憑證，然後選擇 [  **下載** ]。
1. **開啟 Keychain 存取** -這是應用程式是 OS X 中密碼管理系統的 GUI 介面。
1. 匯**入憑證**-如果尚未安裝憑證，請將檔案 **.。。** 從 [Keychain 存取] 功能表匯入專案。 流覽至上述匯出的憑證，然後選取它。
1. **匯出憑證** -展開憑證以便看見相關聯的私密金鑰，以滑鼠右鍵按一下機碼，然後選擇 [匯出]。 系統會提示您輸入所匯出檔案的檔案名和密碼。

至此，我們已完成憑證的相關步驟。 我們已建立將用來簽署 iOS 應用程式的憑證，並將該憑證轉換成可在伺服器應用程式中搭配 PushSharp 使用的格式。 接下來讓我們看看 iOS 應用程式如何與 APNS 互動。

## <a name="registering-with-apns"></a>註冊 APNS

在 iOS 應用程式收到遠端通知之前，必須先向 APNS 註冊。 APNS 將會產生唯一的裝置權杖，並將其傳回給 iOS 應用程式。 IOS 應用程式接著會取得裝置權杖，然後向應用程式伺服器註冊其本身。 一旦發生這種情況，註冊就會完成，而應用程式伺服器可能會將通知推播至行動裝置。

理論上，裝置權杖可能會在 iOS 應用程式每次向 APNS 註冊本身時變更，但實際上並不會發生這種情況。 優化應用程式可能會快取最新的裝置權杖，而且只會在應用程式伺服器確實變更時進行更新。 下圖說明註冊和取得裝置權杖的程式：

 ![下圖說明註冊和取得裝置權杖的程式](remote-notifications-in-ios-images/image12.png)

在目前的物件上呼叫時，會在 `FinishedLaunching` 應用程式委派類別的方法中處理 APNS 的註冊 `RegisterForRemoteNotificationTypes` `UIApplication` 。 當 iOS 應用程式向 APNS 註冊時，它也必須指定要接收的遠端通知類型。 這些遠端通知類型會宣告于列舉中 `UIRemoteNotificationType` 。 下列程式碼片段是 iOS 應用程式如何註冊以接收遠端警示和徽章通知的範例：

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

APNS 註冊要求會在背景中執行-收到回應時，iOS 會呼叫類別中的方法， `RegisteredForRemoteNotifications` `AppDelegate` 並傳遞已註冊的裝置權杖。 標記將會包含在物件中 `NSData` 。 下列程式碼片段示範如何取出 APNS 提供的裝置權杖：

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

如果註冊因某些原因而失敗 (例如裝置未連線到網際網路) ，則 iOS 會 `FailedToRegisterForRemoteNotifications` 在應用程式委派類別上呼叫。 下列程式碼片段顯示如何向使用者顯示警示，通知他們註冊失敗：

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>裝置權杖維護

裝置權杖將會在一段時間後過期或變更。 因為這種情況，伺服器應用程式必須進行一些清理，並清除這些過期或變更的權杖。 當應用程式將推播通知傳送至具有過期權杖的裝置時，APNS 會記錄並儲存該過期權杖。 然後，伺服器可能會查詢 APNS 以找出已過期的權杖。

用來提供 *意見服務* 的 APNS： HTTPS 端點，可透過為了傳送推播通知而建立的憑證來進行驗證，並傳回已過期之權杖的相關資料。 這已由 Apple 取代，並已移除。

相反地，有一個新的 HTTP 狀態碼適用于意見服務先前回報的案例：

> 410-主題已不再使用裝置權杖。

此外，新 `timestamp` 的 JSON 資料索引鍵將會出現在回應主體中：

> 如果： status 標頭中的值為410，則此索引鍵的值是上次確認裝置權杖不再適用于主題的時間。
>
> 停止推播通知，直到裝置向您的提供者註冊含有較晚時間戳記的權杖為止。

## <a name="summary"></a>摘要

本節介紹 iOS 中推播通知周圍的重要概念。 它說明 Apple Push Notification Gateway 服務 (APNS) 的角色。 接著，它會說明如何建立及使用 APNS 所不可或缺的安全性憑證。 最後，本檔將討論應用程式伺服器如何使用 *意見服務* 來停止追蹤過期的裝置權杖。

## <a name="related-links"></a>相關連結

- [通知-示範本機和遠端通知 (範例) ](/samples/xamarin/ios-samples/notifications)
- [開發人員的本機和推播通知](https://developer.apple.com/notifications/)
- [UIApplication](/dotnet/api/uikit.uiapplication)
- [UIRemoteNotificationType](/dotnet/api/uikit.UIRemoteNotificationType)