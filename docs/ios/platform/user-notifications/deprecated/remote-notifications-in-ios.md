---
title: 在 iOS 中的推播通知
description: 本文件說明如何使用 iOS 9 及更早版本中的推播通知。 它討論的憑證，向 Apple 推播通知閘道服務 (APNS)，和更多功能。
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 8ad742607e506df436a5526d31621ac7636ac29b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61086986"
---
# <a name="push-notifications-in-ios"></a>在 iOS 中的推播通知

> [!IMPORTANT]
> 在本節中的資訊適用於 iOS 9 和之前，它已保留這裡以支援較舊 iOS 版本。 適用於 iOS 10 及更新版本，請參閱[使用者通知架構指南](~/ios/platform/user-notifications/index.md)支援 iOS 裝置上的本機和遠端的通知。

推播通知應該保持簡短，並且只會包含足夠的資料來通知它應該與伺服器應用程式取得更新的行動應用程式。 比方說，當新的電子郵件送達時，伺服器應用程式只會通知到達新的電子郵件的行動應用程式。 通知不會包含新的電子郵件本身。 行動應用程式會接著新的電子郵件從伺服器擷取適當時

推播的中心是在 iOS 中的通知*Apple 推播通知閘道服務 (APNS)*。 這是負責從應用程式伺服器的路由通知到 iOS 裝置的 Apple 提供的服務。
下圖說明適用於 iOS 的推播通知拓撲：![](remote-notifications-in-ios-images/image4.png "此圖說明適用於 iOS 的推播通知拓樸")

遠端通知本身是 JSON 格式化字串符合格式和通訊協定中指定[通知承載](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1)一節[本機和推播通知程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)中[iOS 開發人員文件](https://developer.apple.com/devcenter/ios/index.action)。

Apple 會維護兩個環境的 APNS:*沙箱*並*生產*環境。 沙箱環境為了在開發階段期間進行測試，並且可在`gateway.sandbox.push.apple.com`TCP 連接埠上 2195年。 生產環境旨在用於應用程式已部署，請參閱`gateway.push.apple.com`TCP 連接埠上 2195年。

## <a name="requirements"></a>需求

推播通知，必須遵守下列規則，會依照 APNS 的架構：

-  **256 位元組訊息限制**-通知的整個訊息大小不得超過 256 個位元組。
-  **沒有回條確認**-APNS 不會提供訊息收件者對所做的任何通知寄件者。 如果裝置已無法連線，且會傳送多個連續的通知，除了最新的所有通知將都會遺失。 最新的通知會傳遞至裝置。
-  **每個應用程式需要的安全憑證**-與 APNS 通訊必須透過 SSL。


## <a name="creating-and-using-certificates"></a>建立及使用憑證

每個前一節中所述的環境需要他們自己的憑證。 本節將討論如何建立憑證、 佈建設定檔中，將它產生關聯，然後取得個人資訊交換憑證與 PushSharp 搭配使用。

1.  若要建立憑證移至 iOS 佈建入口網站 Apple 網站上以下的螢幕擷取畫面 （請注意應用程式識別碼 功能表項目，在左側） 中所示：

    [![](remote-notifications-in-ios-images/image5new.png "IOS Apple 網站上的佈建入口網站")](remote-notifications-in-ios-images/image5new.png#lightbox)

2.  接下來，瀏覽至 [應用程式識別碼] 區段，並建立新的應用程式識別碼，如下列螢幕擷取畫面所示：

    [![](remote-notifications-in-ios-images/image6new.png "瀏覽至 [應用程式識別碼] 區段，並建立新的應用程式識別碼")](remote-notifications-in-ios-images/image6new.png#lightbox)

3.  當您按一下 **+**  按鈕，您將能夠應用程式識別碼中，輸入描述和套件組合識別碼，在下一步 的螢幕擷取畫面所示：

    [![](remote-notifications-in-ios-images/image7new.png "輸入應用程式識別碼描述和套件組合識別碼")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. 請務必選取**明確的應用程式識別碼**和 套件組合識別碼結尾不是， `*` 。 這會建立適用於多個應用程式的識別項和推播通知憑證必須是單一應用程式。

1. 在 [應用程式服務] 下選取**推播通知**:

    [![](remote-notifications-in-ios-images/image8new.png "選取 推播通知")](remote-notifications-in-ios-images/image8new.png#lightbox)

2. 按下**送出**以確認新的應用程式識別碼的註冊：

    [![](remote-notifications-in-ios-images/image9new.png "確認註冊新的應用程式識別碼")](remote-notifications-in-ios-images/image9new.png#lightbox)

3.  接下來，您必須建立憑證，做為應用程式識別碼。 在左側導覽中，瀏覽至**憑證 > 所有**，然後選取`+`按鈕，如下列螢幕擷取畫面所示：

    [![](remote-notifications-in-ios-images/image10new.png "建立應用程式識別碼的憑證")](remote-notifications-in-ios-images/image8.png#lightbox)

4.  選取您想要使用的開發或生產環境憑證：

    [![](remote-notifications-in-ios-images/image11new.png "選取開發或生產環境的憑證")](remote-notifications-in-ios-images/image11new.png#lightbox)

5. 然後選取 我們剛剛建立的新應用程式識別碼：

    [![](remote-notifications-in-ios-images/image12new.png "選取剛才建立的新應用程式識別碼")](remote-notifications-in-ios-images/image12new.png#lightbox)

6.  這會顯示將帶領您建立的程序的指示*憑證簽署要求*使用**鑰匙圈存取**您 mac 上的應用程式

7.  既然已建立的憑證，它必須做的建置程序的一部分，簽署應用程式，以便它可以向 APNs 註冊。 這需要建立及安裝會使用憑證佈建設定檔。

8.  若要建立佈建設定檔的開發，瀏覽至**佈建設定檔**區段，然後遵循步驟來建立，使用我們剛才建立的應用程式識別碼。

9.  一旦您已建立佈建設定檔，開啟**Xcode Organizer**並加以重新整理。 如果您建立的佈建設定檔未出現您可能必須從 iOS 佈建入口網站下載的設定檔，然後手動將它匯入。 下列螢幕擷取畫面顯示召集人的範例，以佈建的設定檔加入：  
    [![](remote-notifications-in-ios-images/image13new.png "此螢幕擷取畫面顯示召集人的範例，以佈建的設定檔新增")](remote-notifications-in-ios-images/image13new.png#lightbox)

10.  此時，我們需要設定 Xamarin.iOS 專案以使用這個新建立的佈建設定檔。 這是從**專案選項** 對話方塊底下**iOS 套件組合簽署** 索引標籤，如下列螢幕擷取畫面：  
    [![](remote-notifications-in-ios-images/image11.png "Xamarin.iOS 專案設定為使用這個新建立的佈建設定檔")](remote-notifications-in-ios-images/image11.png#lightbox)

此時應用程式設定為使用推播通知。 不過，仍有幾個步驟，所需的憑證。 此憑證是 DER PushSharp，需要個人資訊交換 (PKCS12) 憑證與不相容的格式。 若要將憑證轉換，如此就可供 PushSharp，執行最後一個步驟執行：

1.  **下載憑證檔案**-登入 iOS 佈建入口網站中，選擇 [憑證] 索引標籤中，選取正確的佈建設定檔，然後選擇相關聯的憑證**下載**。
1.  **開啟 金鑰鏈存取**-這是應用程式是在 OS X 中的密碼管理系統的 GUI 介面。
1.  **匯入憑證**-如果憑證尚未安裝，**檔案...匯入項目**從 [金鑰鏈存取] 功能表。 瀏覽至上面，匯出的憑證，並加以選取。
1.  **將憑證匯出**-展開憑證，讓相關聯的私密金鑰為可見，機碼上按一下滑鼠右鍵，然後選擇 匯出。 將提示您將檔案名稱的匯出檔案的密碼。

現在我們已完成的憑證。 我們已建立的憑證，將用來簽署 iOS 應用程式，並轉換成格式，可以搭配 PushSharp 伺服器應用程式中的該憑證。 下一步 讓我們看看 APNS 與 iOS 應用程式互動的方式。

## <a name="registering-with-apns"></a>使用 APNS 註冊

之前的 iOS 應用程式可以接收遠端通知，它必須向 APNS 註冊。 APNS 會產生唯一的裝置權杖，並可返回 iOS 應用程式。 IOS 應用程式將會採取的裝置權杖，然後再將本身登錄與應用程式伺服器。 一旦發生所有這種情況，註冊便已完成，並在應用程式伺服器可能會推播通知給行動裝置。

理論上，但實際上並未發生，通常的裝置權杖時，可能會變更 iOS 應用程式註冊其本身的 apns 來說，每一次。 做為最佳化應用程式可能會快取的最新的裝置權杖，並只更新應用程式伺服器，但是會變更時。 下圖說明註冊和取得裝置權杖的程序：

 ![](remote-notifications-in-ios-images/image12.png "此圖說明如何註冊和取得裝置權杖的程序")

向 APNS 註冊會在處理`FinishedLaunching`方法的應用程式委派類別，藉由呼叫`RegisterForRemoteNotificationTypes`目前`UIApplication`物件。 當 iOS 應用程式註冊與 APNS 搭配時，它也必須指定何種類型的遠端通知它想要接收。 列舉型別中宣告這些遠端的通知類型`UIRemoteNotificationType`。 下列程式碼片段是如何接收遠端通知及徽章通知註冊的 iOS 應用程式的範例：

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

APNS 註冊要求會在幕後-收到回應時，iOS 會呼叫方法`RegisteredForRemoteNotifications`在`AppDelegate`類別，並將已註冊的裝置權杖傳遞。 權杖會包含在`NSData`物件。 下列程式碼片段顯示如何擷取裝置權杖提供該 APNS:

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

如果註冊失敗原因 （例如裝置未連線到網際網路） 時，會呼叫 iOS`FailedToRegisterForRemoteNotifications`應用程式委派類別。 下列程式碼片段示範如何顯示警示，登錄失敗通知使用者：

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>裝置權杖內部管理

裝置權杖會過期，或經過一段時間變更。 因此，伺服器應用程式必須進行一些房屋清除，並清除這些已過期或已變更的語彙基元。 當應用程式傳送推播通知的裝置已過期的權杖，以作為時，APNS 會記錄，並儲存該過期的權杖。 伺服器可能就會查詢以找出哪些權杖已過期的 APNS。

用來提供的 APNS*意見反應服務*-透過已建立用來傳送推播通知並傳送後的得知哪些權杖已過期的憑證驗證的 HTTPS 端點。 這已由 Apple 已被取代並移除。

相反地，還有新的 HTTP 狀態碼的情況下，先前的意見反應服務所報告的：

> 410-裝置權杖已不再使用中的主題。

此外，新`timestamp`JSON 資料的金鑰會在回應主體：

> 如果中的值： 狀態標頭為 410，此機碼值為 APNs 確認裝置權杖已不再適用於主題的最後一次。
>
> 停止將推送通知，直到裝置會向您的提供者的更新時間戳記中的語彙基元。

## <a name="summary"></a>總結

本節介紹周圍的推播通知，在 iOS 中的重要概念。 它說明角色的 Apple 推播通知閘道服務 (APNS)。 然後會說明建立和使用安全性憑證給 APNS 不可或缺。 最後這份文件完成如何使用應用程式伺服器的討論*意見反應服務*停止追蹤到期的裝置權杖。


## <a name="related-links"></a>相關連結

- [通知-示範本機及遠端通知 （範例）](https://developer.xamarin.com/samples/monotouch/Notifications/)
- [本機和推播通知適用於開發人員](https://developer.apple.com/notifications/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
