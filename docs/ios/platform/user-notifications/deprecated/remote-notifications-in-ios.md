---
title: 在 iOS 中的推播通知
description: 本文件說明如何使用 iOS 9 及更早版本中的推播通知。 它會討論向 Apple 推播通知閘道 Service (APNS)，以及其他更多的憑證。
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 7bb2a250b9d3cc0c8df02f432330f9fe1dc58f94
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788663"
---
# <a name="push-notifications-in-ios"></a>在 iOS 中的推播通知

> [!IMPORTANT]
> 本節中的資訊屬於 iOS 9 及之前，它已保留這裡以支援較舊 iOS 版本。 適用於 iOS 10 和更新版本，請參閱[使用者通知架構指南](~/ios/platform/user-notifications/index.md)支援 iOS 裝置上的本機和遠端的通知。

推播通知應該保持簡短，而且只可包含足夠的資料來告知它應該與伺服器應用程式取得更新的行動應用程式。 例如，當新的電子郵件送達時，伺服器應用程式只會通知到達新電子郵件的行動應用程式。 通知不會包含新的電子郵件本身。 行動應用程式會擷取新的電子郵件伺服器從適當時

中間的推播通知，在 iOS 中的是*Apple 推播通知閘道 Service (APNS)*。 這是由負責傳閱通知從應用程式伺服器到 iOS 裝置的 Apple 提供的服務。
下圖顯示適用於 iOS 的推播通知拓樸： ![ ](remote-notifications-in-ios-images/image4.png "此映像說明適用於 iOS 的推播通知拓樸")

遠端通知會自行而 JSON 格式化遵守格式的字串中指定的通訊協定[通知裝載](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1)區段[本機和推播通知程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)中[iOS 開發人員文件](https://developer.apple.com/devcenter/ios/index.action)。

Apple 會維護兩種環境的 APNS:*沙箱*和*生產*環境。 沙箱環境適用於測試的開發階段，並且可以位於`gateway.sandbox.push.apple.com`tcp 連接埠 2195年。 生產環境適用於已部署，而且可以在找到的應用程式`gateway.push.apple.com`tcp 連接埠 2195年。

## <a name="requirements"></a>需求

推播通知，必須遵守下列規則，依照 APNS 的架構：

-  **256 位元組的訊息限制**-通知的整個訊息大小不得超過 256 個位元組。
-  **沒有回條確認**-APNS 不提供訊息至預定的收件者所做的任何通知寄件者。 如果裝置已無法連線到多個連續的通知會傳送，除了最新的所有通知將都會遺失。 只有最新的通知會傳送到裝置。
-  **每個應用程式需要的安全憑證**-APNS 的通訊必須透過 SSL。


## <a name="creating-and-using-certificates"></a>建立及使用憑證

上一節中所述的環境中都需要自己的憑證。 本節將討論如何建立憑證、 佈建設定檔，將它產生關聯，然後取得個人資訊交換憑證 PushSharp 搭配使用。

1.  若要建立憑證移至 iOS 佈建入口網站在 Apple 網站上下列螢幕擷取畫面 （請注意在左邊的應用程式識別碼功能表項目） 中所示：

    [![](remote-notifications-in-ios-images/image5new.png "IOS 蘋果網站上的佈建入口網站")](remote-notifications-in-ios-images/image5new.png#lightbox)

2.  接下來，瀏覽至 [應用程式識別碼] 區段，並建立新的應用程式識別碼，如下列螢幕擷取畫面所示：

    [![](remote-notifications-in-ios-images/image6new.png "瀏覽至 [應用程式識別碼] 區段，並建立新的應用程式識別碼")](remote-notifications-in-ios-images/image6new.png#lightbox)

3.  當您按一下 [ **+** ] 按鈕，您將能夠輸入的應用程式識別碼、 描述和配套識別碼中的下一個螢幕擷取畫面所示：

    [![](remote-notifications-in-ios-images/image7new.png "輸入描述和套件組合識別碼的應用程式識別碼")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. 請務必選取**明確的應用程式識別碼**和套件組合識別碼不是以結尾`*`。 這會建立適用於多個應用程式，識別項和推播通知憑證必須是單一應用程式。

1. 在 [應用程式服務] 下選取**推播通知**:

    [![](remote-notifications-in-ios-images/image8new.png "選取 推播通知")](remote-notifications-in-ios-images/image8new.png#lightbox)

2. 按**送出**以確認註冊新的應用程式識別碼：

    [![](remote-notifications-in-ios-images/image9new.png "確認註冊新的應用程式識別碼")](remote-notifications-in-ios-images/image9new.png#lightbox)

3.  接下來，您必須將憑證建立的應用程式識別碼。 在左側導覽中，瀏覽至**憑證 > 所有**選取`+`按鈕，如下列螢幕擷取畫面所示：

    [![](remote-notifications-in-ios-images/image10new.png "建立憑證之應用程式識別碼")](remote-notifications-in-ios-images/image8.png#lightbox)

4.  選取您想要使用的開發或實際執行憑證是否：

    [![](remote-notifications-in-ios-images/image11new.png "選取開發或實際執行憑證")](remote-notifications-in-ios-images/image11new.png#lightbox)

5. 然後選取 新我們剛才建立的應用程式識別碼：

    [![](remote-notifications-in-ios-images/image12new.png "選取剛才建立的新應用程式 ID")](remote-notifications-in-ios-images/image12new.png#lightbox)

6.  這將會顯示將引導您建立的程序的指示*憑證簽署要求*使用**金鑰鏈存取**mac 上的應用程式

7.  已建立憑證，它必須做的建置程序的一部分，簽署該應用程式，使其可以向 APNs 註冊。 這需要建立及安裝使用憑證佈建設定檔。

8.  若要建立佈建設定檔的開發，瀏覽至**佈建的設定檔**區段，然後遵循步驟以建立它，請使用我們剛才建立的應用程式識別碼。

9.  一旦您已建立佈建設定檔，開啟**Xcode 召集人**和重新整理。 如果您建立佈建設定檔不會顯示可能需要從 iOS 佈建入口網站下載的設定檔，並以手動方式將它匯入。 下列螢幕擷取畫面顯示召集人的範例，以佈建的設定檔加入：

    [![](remote-notifications-in-ios-images/image13new.png "這個螢幕擷取畫面顯示佈建設定檔加入範例的 [組合管理]")](remote-notifications-in-ios-images/image13new.png#lightbox)

10.  此時，我們需要設定 Xamarin.iOS 專案以使用這個新建立的佈建設定檔。 這是從**專案選項**對話方塊下方**iOS 套件組合簽署**索引標籤上，如下列螢幕擷取畫面：

    [![](remote-notifications-in-ios-images/image11.png "使用這個新建立的佈建設定檔將 Xamarin.iOS 專案設定")](remote-notifications-in-ios-images/image11.png#lightbox)



此時應用程式設定為使用推播通知。 不過，仍有幾個步驟，所需要的憑證。 此憑證處於 DER PushSharp，需要的個人資訊交換 (PKCS12) 憑證與不相容的格式。 若要轉換的憑證，使它供 PushSharp，執行最後一個步驟執行：

1.  **下載的憑證檔案**-登入的 iOS 佈建入口網站中，選擇 [憑證] 索引標籤，選取正確的佈建設定檔和選擇相關聯的憑證**下載**。
1.  **開啟 金鑰鏈存取**-這是應用程式是在 OS X 的密碼管理系統 GUI 介面。
1.  **將憑證匯入**-如果憑證未安裝，**檔案...匯入項目**從 [金鑰鏈存取] 功能表。 瀏覽至上面，匯出的憑證並加以選取。
1.  **匯出憑證**-讓相關聯的私密金鑰為可見，請展開 憑證、 金鑰上按一下滑鼠右鍵並選擇匯出。 將會提示您輸入檔案名稱和匯出檔案的密碼。

此時，我們都使用憑證。 我們已建立將用於簽署 iOS 應用程式的憑證，並轉譯成該憑證可以用於 PushSharp 中的伺服器應用程式的格式。 下一步 讓我們看看 APNS 與 iOS 應用程式互動的方式。

## <a name="registering-with-apns"></a>使用 APNS 註冊

之前的 iOS 應用程式可以接收遠端的通知，它必須使用 APNS 註冊。 APNS 會產生唯一的裝置權杖，並傳回的 iOS 應用程式。 IOS 應用程式會接著採取裝置權杖，然後再登錄本身與應用程式伺服器。 一旦發生這種情況，註冊已完成，而且應用程式伺服器可能會推播通知給行動裝置。

理論上，但實際上不是這樣，通常裝置權杖時，可能會變更 iOS 應用程式可註冊其本身 APNS，每次。 隨著應用程式的最佳化可能會快取的最新的裝置權杖，而且只會在變更更新應用程式伺服器。 下圖說明註冊並取得裝置權杖的程序：

 ![](remote-notifications-in-ios-images/image12.png "此圖說明註冊並取得裝置權杖的程序")

使用 APNS 註冊中，會處理`FinishedLaunching`藉由呼叫應用程式委派類別的方法`RegisterForRemoteNotificationTypes`上目前`UIApplication`物件。 當使用 APNS 註冊的 iOS 應用程式時，它也必須指定何種類型的遠端通知它要接收。 這些遠端通知類型會宣告列舉中`UIRemoteNotificationType`。 下列程式碼片段是如何 iOS 應用程式可登錄成為遠端徽章和警示通知的範例：

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

APNS 註冊要求會在背景中-收到回應時，iOS 會呼叫方法`RegisteredForRemoteNotifications`中`AppDelegate`類別，並將已註冊的裝置的語彙基元。 權杖會包含在`NSData`物件。 下列程式碼片段會示範如何擷取裝置權杖提供該 APNS:

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

如果註冊失敗原因 （例如裝置未連線到網際網路） 時，會呼叫 iOS`FailedToRegisterForRemoteNotifications`應用程式上委派類別。 下列程式碼片段示範如何顯示警示給使用者，通知他們註冊失敗：

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>裝置權杖內部管理

裝置權杖會到期，或是隨著時間而變更。 因此，伺服器應用程式將會需要某些馬上清除作業，並清除這些已過期或已變更的語彙基元。 當應用程式傳送為推播通知給具有過期的權杖的裝置時，APNS 會記錄，並儲存該過期的權杖。 伺服器可能再查詢以找出哪些權杖已過期的 APNS。

提供用於 APNS*意見反應服務*-驗證已建立用來傳送推播通知和傳送備份資料有關哪些權杖已過期的憑證透過 HTTPS 端點。 這沒有已由 Apple 已被取代，而且移除。

取而代之的是新的 HTTP 狀態碼先前報告的意見反應服務的情況：

> 410-無法再使用相關的主題中裝置權杖。

此外，新`timestamp`JSON 資料的金鑰會在回應主體：

> 如果中的值： 狀態標頭是 410，此索引鍵的值是 APNs 確認裝置權杖已不再適用於本主題的最後一次。
>
> 停止將推送通知，直到裝置註冊權杖與您的提供者的更新時間戳記。

## <a name="summary"></a>總結

本節介紹圍繞在 iOS 中的推播通知的重要概念。 它說明的 Apple 推播通知閘道服務 (APNS) 的角色。 然後會涵蓋建立和使用 APNS 或缺的安全性憑證。 最後這份文件完成並討論如何使用應用程式伺服器上*意見反應服務*停止追蹤到期的裝置權杖。


## <a name="related-links"></a>相關連結

- [通知-示範本機和遠端通知 （範例）](https://developer.xamarin.com/samples/monotouch/Notifications/)
- [本機和開發人員的推播通知](https://developer.apple.com/notifications/)
- [Uiapplication #](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
