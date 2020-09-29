---
title: 在 Xamarin 中交付
description: 本文說明如何在 Xamarin iOS 應用程式中使用遞交，在使用者的其他裝置上執行的應用程式之間傳輸使用者活動。
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 955fa049e88b74796d5949518f6149f1e2a9527c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435198"
---
# <a name="handoff-in-xamarinios"></a>在 Xamarin 中交付

_本文說明如何在 Xamarin iOS 應用程式中使用遞交，在使用者的其他裝置上執行的應用程式之間傳輸使用者活動。_

Apple 推出了 iOS 8 和 OS X Yosemite (10.10) 中提供一種通用機制，可讓使用者將其裝置上所啟動的活動轉移到另一個執行相同應用程式的裝置，或另一個支援相同活動的應用程式。

[![執行遞交操作的範例](handoff-images/handoff02.png)](handoff-images/handoff02.png#lightbox)

本文將快速瞭解如何在 Xamarin iOS 應用程式中啟用活動共用，並詳細討論交付架構：

## <a name="about-handoff"></a>關於遞交

IOS 8 和 OS X Yosemite 中的 Apple 引進的 (也稱為持續性)  (10.10) 以使用者的身分在其中一個裝置上啟動活動 (iOS 或 Mac) ，並在其裝置上繼續相同的活動 (如使用者的 iCloud 帳戶) 所識別。

IOS 9 中的交付已擴充，也支援新的增強式搜尋功能。 如需詳細資訊，請參閱我們的 [搜尋增強功能](~/ios/platform/search/index.md) 檔。

例如，使用者可以在其 iPhone 上啟動電子郵件，並在其 Mac 上順暢地繼續使用電子郵件，其中所有相同的訊息資訊都會填入，資料指標位於 iOS 中的相同位置。

任何共用相同 _小組識別碼_ 的應用程式都可以在應用程式中使用轉型來繼續使用者活動，只要這些應用程式是透過 iTunes App Store，或是由 Mac、Enterprise 或臨機操作應用程式) 的註冊開發人員 (所簽署。

任何 `NSDocument` 或任何 `UIDocument` 應用程式都會自動提供內建的交付支援，而且需要進行少量變更以支援提交。

### <a name="continuing-user-activities"></a>繼續使用者活動

`NSUserActivity`類別 (，以及的一些小變更 `UIKit` 和 `AppKit`) 可支援定義使用者的活動，該活動可能會在使用者的其他裝置上繼續。

若要將活動傳遞給另一個使用者的裝置，它必須封裝在實例中 `NSUserActivity` （標示為 _目前的活動_）、將其承載設定 (用來執行接續) 的資料，然後活動必須傳送至該裝置。

提交會傳遞最少的資訊，以定義要繼續的活動，並透過 iCloud 同步處理較大的資料封包。

在接收的裝置上，使用者會收到通知，指出有活動可供接續。 如果使用者選擇繼續新裝置上的活動，則會啟動指定的應用程式 (如果尚未執行) ，並使用的承載 `NSUserActivity` 重新開機活動。

[![繼續進行使用者活動的總覽](handoff-images/handoffinteractions.png)](handoff-images/handoffinteractions.png#lightbox)

只有共用相同開發人員小組識別碼和回應特定 _活動類型_ 的應用程式，才有資格繼續進行。 應用程式會在 `NSUserActivityTypes` 其 **資訊 plist** 檔案的索引鍵下定義其所支援的活動類型。 在此情況下，繼續裝置會根據小組識別碼、活動類型和選擇性 _活動標題_，選擇要執行接續的應用程式。

接收應用程式會使用來自字典的資訊 `NSUserActivity` `UserInfo` 來設定其使用者介面，並還原指定活動的狀態，讓使用者能夠順暢地進行轉換。

如果接續需要的資訊超出可透過 a 有效率地傳送的資訊 `NSUserActivity` ，繼續的應用程式可以傳送呼叫至原始應用程式，並建立一或多個串流以傳輸所需的資料。 例如，如果活動編輯了具有多個影像的大型文字檔，則需要串流處理在接收裝置上繼續活動所需的資訊。 如需詳細資訊，請參閱下面的 [支援接續資料流程](#supporting-continuation-streams) 一節。

如上所述， `NSDocument` 或 `UIDocument` 應用程式會自動提供內建的交付支援。 如需詳細資訊，請參閱下面的「 [以檔為基礎的應用程式中的支援交付](#supporting-handoff-in-document-based-apps) 」一節。

### <a name="the-nsuseractivity-class"></a>NSUserActivity 類別

`NSUserActivity`類別是在遞交交換中的主要物件，用來封裝可供接續的使用者活動狀態。 應用程式將會 `NSUserActivity` 為其支援的任何活動具現化複本，並希望在其他裝置上繼續進行。 例如，檔編輯器會針對目前開啟的每份檔建立活動。 不過，在最前面的視窗或索引標籤中，只會顯示最前面的檔 () 是 _目前的活動_ ，而且因此可用於接續。

的實例 `NSUserActivity` 是由其 `ActivityType` 和屬性所識別 `Title` 。 `UserInfo`Dictionary 屬性是用來攜帶活動狀態的相關資訊。 `NeedsSave` `true` 如果您想要透過的委派延遲載入狀態資訊，請將屬性設定為 `NSUserActivity` 。 您 `AddUserInfoEntries` 可以使用方法，視需要將其他用戶端的新資料合併到 `UserInfo` 字典中，以保留活動的狀態。

### <a name="the-nsuseractivitydelegate-class"></a>NSUserActivityDelegate 類別

`NSUserActivityDelegate`會用來將資訊放入字典中的 `NSUserActivity` 最新 `UserInfo` 狀態，並與活動的目前狀態同步。 當系統需要更新活動中的資訊時 (例如在另一個裝置) 的接續之前，它會呼叫委派的 `UserActivityWillSave` 方法。

您將需要執行方法， `UserActivityWillSave` 並對 (（例如 ) 、等）進行任何變更， `NSUserActivity` `UserInfo` `Title` 以確保它仍會反映目前活動的狀態。 當系統呼叫方法時 `UserActivityWillSave` ，將會 `NeedsSave` 清除旗標。 如果您修改活動的任何資料屬性，則必須 `NeedsSave` 再次設定為 `true` 。

`UserActivityWillSave`您可以選擇性地 `UIKit` 自動擁有或管理使用者活動，而不是使用上面所提供的方法 `AppKit` 。 若要這樣做，請設定回應程式物件的 `UserActivity` 屬性，並執行 `UpdateUserActivityState` 方法。 如需詳細資訊，請參閱下面的「 [回應中的支援遞交](#supporting-handoff-in-responders) 」一節。

### <a name="app-framework-support"></a>應用程式架構支援

`UIKit` (iOS) 和 `AppKit` (OS X) 都提供內建支援，可在 `NSDocument` 、回應程式 (`UIResponder` / `NSResponder`) 和 `AppDelegate` 類別中進行交付。 雖然每個作業系統都會以稍微不同的方式來執行切換，但基本機制和 Api 相同。

#### <a name="user-activities-in-document-based-apps"></a>以檔為基礎的應用程式中的使用者活動

以檔為基礎的 iOS 和 OS X 應用程式會自動提供內建的交付支援。 若要啟用這項支援，您必須 `NSUbiquitousDocumentUserActivityType` `CFBundleDocumentTypes` 在應用程式的 **Info. plist** 檔案中，為每個專案新增索引鍵和值。

如果有此索引鍵，則 `NSDocument` `UIDocument` 會自動 `NSUserActivity` 為指定類型的 iCloud 型檔建立實例。 您將需要為應用程式支援的每一種檔案類型提供活動類型，而多個檔案類型可以使用相同的活動類型。 `NSDocument`和 `UIDocument` 都會 `UserInfo` `NSUserActivity` 以其屬性的值自動填入的屬性 `FileURL` 。

在 OS X 上， `NSUserActivity` `AppKit` 當檔的視窗變成主視窗時，與回應者相關聯的會自動變成目前的活動。 在 iOS 上，針對所 `NSUserActivity` 管理的物件 `UIKit` ，您必須 `BecomeCurrent` 明確地呼叫方法，或在 `UserActivity` `UIViewController` 應用程式進入前景時，將檔的屬性設定在上。

`AppKit` 將會自動還原在 `UserActivity` OS X 上以這種方式建立的任何屬性。如果方法傳回， `ContinueUserActivity` 或如果未執行，就會發生這種情況 `false` 。 在這種情況下，檔會以的 `OpenDocument` 方法開啟， `NSDocumentController` 然後會收到 `RestoreUserActivityState` 方法呼叫。

如需詳細資訊，請參閱下面的「 [以檔為基礎的應用程式支援遞交](#supporting-handoff-in-document-based-apps) 」一節。

#### <a name="user-activities-and-responders"></a>使用者活動和回應

`UIKit`和都 `AppKit` 可以自動管理使用者活動（如果您將它設定為回應者物件的 `UserActivity` 屬性）。 如果狀態已經過修改，您必須將回應程式的 `NeedsSave` 屬性設定 `UserActivity` 為 `true` 。 系統將會 `UserActivity` 在需要時，自動儲存，然後藉由呼叫其方法，讓回應者的時間更新狀態 `UpdateUserActivityState` 。

如果有多個回應程式共用單一 `NSUserActivity` 實例，則會在 `UpdateUserActivityState` 系統更新使用者活動物件時接收回呼。 回應程式必須呼叫 `AddUserInfoEntries` 方法來更新的 `NSUserActivity` `UserInfo` 字典，以反映目前的活動狀態。 在 `UserInfo` 每次呼叫之前都會清除字典 `UpdateUserActivityState` 。

為了將本身與活動解除關聯，回應者可以將其 `UserActivity` 屬性設定為 `null` 。 當應用程式架構受控 `NSUserActivity` 實例沒有關聯的回應或檔時，它會自動失效。

如需詳細資訊，請參閱下面的「 [回應中的支援遞交](#supporting-handoff-in-responders) 」一節。

#### <a name="user-activities-and-the-appdelegate"></a>使用者活動和 AppDelegate

當您的應用程式在 `AppDelegate` 處理交付接續時，是它的主要進入點。 當使用者回應提交通知時，會啟動適當的應用程式 (如果尚未執行) ，而且 `WillContinueUserActivityWithType` `AppDelegate` 會呼叫的方法。 此時，應用程式應該會通知使用者正在啟動接續。

`NSUserActivity`呼叫的方法時，就會傳遞此實例 `AppDelegate` `ContinueUserActivity` 。 此時，您應該設定應用程式的使用者介面，並繼續指定的活動。

如需詳細資訊，請參閱下面的「執行中的 [交付](#implementing-handoff) 」部分。

## <a name="enabling-handoff-in-a-xamarin-app"></a>在 Xamarin 應用程式中啟用交付

由於提交所加諸的安全性需求，因此必須在 Apple 開發人員入口網站和 Xamarin. iOS 專案檔中正確設定使用交付架構的 Xamarin iOS 應用程式。

執行下列動作：

1. 登入 [Apple Developer 入口網站](https://developer.apple.com)。
2. 按一下 [ **憑證]、[識別碼] & 設定檔**。
3. 如果您尚未這麼做，請按一下 [ **識別碼** ] 並建立應用程式的識別碼 (例如 `com.company.appname`) ，否則請編輯現有的識別碼。
4. 確定已檢查 **iCloud** 服務是否有指定的識別碼：

    [![啟用指定識別碼的 iCloud 服務](handoff-images/provision01.png)](handoff-images/provision01.png#lightbox)
5. 儲存變更。
6. 按一下 [布建**設定檔**  >  **開發**]，並為您的應用程式建立新的開發布建設定檔：

    [![為應用程式建立新的開發提供設定檔](handoff-images/provision02.png)](handoff-images/provision02.png#lightbox)
7. 請下載並安裝新的布建設定檔，或使用 Xcode 下載並安裝設定檔。
8. 編輯您的 Xamarin iOS 專案選項，並確定您使用的是您剛才建立的布建設定檔：

    [![選取剛剛建立的布建設定檔](handoff-images/provision03.png)](handoff-images/provision03.png#lightbox)
9. 接下來，請編輯您的 **plist** 檔案，並確定您使用的是用來建立布建設定檔的應用程式識別碼：

    [![設定應用程式識別碼](handoff-images/provision04.png)](handoff-images/provision04.png#lightbox)
10. 滾動至 [ **背景模式** ] 區段，並檢查下列專案：

    [![啟用所需的背景模式](handoff-images/provision05.png)](handoff-images/provision05.png#lightbox)
11. 將變更儲存至所有檔案。

設定好這些設定之後，應用程式就可以開始存取「提交架構」 Api。 如需布建的詳細資訊，請參閱我們的 [裝置](~/ios/get-started/installation/device-provisioning/index.md) 布建和布建 [應用程式](~/ios/get-started/installation/device-provisioning/index.md) 指南。

## <a name="implementing-handoff"></a>實施交付

使用者活動可以在使用相同開發人員小組識別碼簽署的應用程式之間繼續，並支援相同的活動類型。 在 Xamarin 應用程式中執行交付時，您必須在或) 中建立使用者活動物件 `UIKit` (`AppKit` 、更新物件的狀態以追蹤活動，以及繼續接收裝置上的活動。

### <a name="identifying-user-activities"></a>識別使用者活動

實施交付的第一個步驟是識別您應用程式支援的使用者活動類型，並查看哪些活動適合在另一個裝置上接續。 例如： ToDo 應用程式可能支援將專案編輯為一種 _使用者活動類型_，並支援以另一種方式流覽可用的專案清單。

應用程式可以視需要建立多個使用者活動類型，一個用於應用程式提供的任何功能。 針對每個使用者活動類型，應用程式必須追蹤類型的活動開始和結束的時間，而且需要維護最新的狀態資訊，才能在另一部裝置上繼續該工作。

使用者活動可以在任何使用相同小組識別碼簽署的應用程式上繼續，而不需要在傳送和接收應用程式之間進行一對一的對應。 例如，給定的應用程式可以建立四種不同類型的活動，這些活動會由另一部裝置上的不同個別應用程式所使用。 這是 Mac 版應用程式之間常見的情況， (可能有許多功能和函式) 和 iOS 應用程式，其中每個應用程式都較小，且著重于特定的工作。

### <a name="creating-activity-type-identifiers"></a>建立活動類型識別碼

_活動類型識別碼_是加入至應用程式 plist 檔案陣列的簡短字串， `NSUserActivityTypes` 用**Info.plist**來唯一識別指定的使用者活動類型。 應用程式支援的每個活動在陣列中都會有一個專案。 Apple 建議對活動類型識別碼使用反向 DNS 樣式表示法，以避免發生衝突。 例如： `com.company-name.appname.activity` 適用于特定應用程式的活動 `com.company-name.activity` ，或可跨多個應用程式執行的活動。

建立 `NSUserActivity` 實例以識別活動類型時，會使用活動類型識別碼。 當另一部裝置上的活動繼續時，活動類型 (以及應用程式的小組識別碼) 會判斷要啟動哪個應用程式才能繼續活動。

例如，我們會在這裡建立稱為 **MonkeyBrowser** 的範例應用程式 ([下載](/samples/xamarin/ios-samples/ios8-monkeybrowser)) 。 此應用程式會顯示四個索引標籤，每個都有不同的 URL 在網頁瀏覽器視圖中開啟。 使用者將能夠繼續執行應用程式的不同 iOS 裝置上的任何索引標籤。

若要建立必要的活動類型識別碼以支援此行為，請編輯 **plist** 檔案，並切換至 **來源** 視圖。 新增 `NSUserActivityTypes` 金鑰並建立下列識別碼：

[![Plist 編輯器中的 NSUserActivityTypes 索引鍵和必要的識別碼](handoff-images/type01.png)](handoff-images/type01.png#lightbox)

我們為範例 **MonkeyBrowser** 應用程式中的每個索引標籤建立了四個新的活動類型識別碼。 建立您自己的應用程式時，請將陣列的內容取代為 `NSUserActivityTypes` 應用程式支援的活動專屬的活動類型識別碼。

### <a name="tracking-user-activity-changes"></a>追蹤使用者活動變更

當我們建立類別的新實例時 `NSUserActivity` ，我們會指定 `NSUserActivityDelegate` 實例來追蹤活動狀態的變更。 例如，下列程式碼可以用來追蹤狀態變更：

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;

namespace MonkeyBrowse
{
    public class UserActivityDelegate : NSUserActivityDelegate
    {
        #region Constructors
        public UserActivityDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void UserActivityReceivedData (NSUserActivity userActivity, NSInputStream inputStream, NSOutputStream outputStream)
        {
            // Log
            Console.WriteLine ("User Activity Received Data: {0}", userActivity.Title);
        }

        public override void UserActivityWasContinued (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity Was Continued: {0}", userActivity.Title);
        }

        public override void UserActivityWillSave (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity will be Saved: {0}", userActivity.Title);
        }
        #endregion
    }
}
```

`UserActivityReceivedData`當接續資料流程從傳送裝置接收到資料時，就會呼叫方法。 如需詳細資訊，請參閱下面的 [支援接續資料流程](#supporting-continuation-streams) 一節。

`UserActivityWasContinued`當其他裝置已從目前裝置接管活動時，就會呼叫方法。 視活動的類型而定（例如將新專案新增至待辦事項清單），應用程式可能需要中止傳送裝置上的活動。

在 `UserActivityWillSave` 將活動的任何變更儲存並同步至本機可用裝置之前，會呼叫方法。 您可以使用這個方法，在 `UserInfo` 傳送實例之前，對該實例的屬性進行最後一分鐘的變更 `NSUserActivity` 。

### <a name="creating-a-nsuseractivity-instance"></a>建立 NSUserActivity 實例

您的應用程式希望能夠在另一個裝置上繼續的每個活動都必須封裝在 `NSUserActivity` 實例中。 應用程式可以視需要建立多個活動，這些活動的本質取決於有問題的應用程式功能和功能。 例如，電子郵件應用程式可能會建立一個用來建立新訊息的活動，另一個則是用來讀取訊息。

在我們的範例應用程式 `NSUserActivity` 中，每次使用者在其中一個索引標籤式 web 瀏覽器視圖中輸入新的 URL 時，就會建立新的。 下列程式碼會儲存指定索引標籤的狀態：

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSUserActivity UserActivity { get; set; }
...

UserActivity = new NSUserActivity (UserActivityTab1);
UserActivity.Title = "Weather Tab";
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

它會 `NSUserActivity` 使用上面建立的其中一個使用者活動類型來建立新的，並為活動提供人們看得懂的標題。 它會附加至 `NSUserActivityDelegate` 上面建立的實例以監看狀態變更，並通知 iOS 此使用者活動為目前的活動。

### <a name="populating-the-userinfo-dictionary"></a>填入使用者資訊字典

如上所示， `UserInfo` 類別的屬性 `NSUserActivity` 是 `NSDictionary` 用來定義指定活動狀態的索引鍵/值組的。 中儲存的值 `UserInfo` 必須是下列其中一種類型： `NSArray` 、 `NSData` 、 `NSDate` 、 `NSDictionary` 、 `NSNull` 、 `NSNumber` 、 `NSSet` 、 `NSString` 或 `NSURL` 。 `NSURL` 指向 iCloud 檔的資料值會自動調整，使其指向接收裝置上的相同檔。

在上述範例中，我們建立了一個 `NSMutableDictionary`  物件，並以單一索引鍵來填入，提供使用者目前在指定索引標籤上所看到的 URL。 `AddUserInfoEntries` 使用者活動的方法是用來以將用來在接收裝置上還原活動的資料來更新活動：

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple 建議您保留最少傳送的資訊給極度匱乏，以確保活動會及時傳送給接收的裝置。 如果需要較大的資訊，例如必須傳送附加至檔的影像，您應該使用接續資料流程。 如需詳細資訊，請參閱下面的 [支援接續資料流程](#supporting-continuation-streams) 一節。

### <a name="continuing-an-activity"></a>繼續活動

交付會自動通知本機 iOS 和 OS X 裝置，這些裝置與原始裝置的實體相近，並登入相同的 iCloud 帳戶，以提供持續性使用者活動的可用性。 如果使用者選擇繼續新裝置上的活動，系統會根據小組識別碼和活動類型來啟動適當的應用程式 () 以及其 `AppDelegate` 接續需要發生的資訊。

首先， `WillContinueUserActivityWithType` 會呼叫方法，讓應用程式可以通知使用者，接續即將開始。 我們會在範例應用程式的 **AppDelegate.cs** 檔案中使用下列程式碼，以處理開始的接續：

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSString UserActivityTab2 = new NSString ("com.xamarin.monkeybrowser.tab2");
public NSString UserActivityTab3 = new NSString ("com.xamarin.monkeybrowser.tab3");
public NSString UserActivityTab4 = new NSString ("com.xamarin.monkeybrowser.tab4");
...

public FirstViewController Tab1 { get; set; }
public SecondViewController Tab2 { get; set;}
public ThirdViewController Tab3 { get; set; }
public FourthViewController Tab4 { get; set; }
...

public override bool WillContinueUserActivity (UIApplication application, string userActivityType)
{
    // Report Activity
    Console.WriteLine ("Will Continue Activity: {0}", userActivityType);

    // Take action based on the user activity type
    switch (userActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Inform view that it's going to be modified
        Tab1.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Inform view that it's going to be modified
        Tab2.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Inform view that it's going to be modified
        Tab3.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Inform view that it's going to be modified
        Tab4.PreparingToHandoff ();
        break;
    }

    // Inform system we handled this
    return true;
}
```

在上述範例中，每個 View 控制器都會向註冊， `AppDelegate` 且具有公用 `PreparingToHandoff` 方法，可顯示活動指標和訊息，讓使用者知道活動即將移交給目前的裝置。 範例：

```csharp
private void ShowBusy(string reason) {

    // Display reason
    BusyText.Text = reason;

    //Define Animation
    UIView.BeginAnimations("Show");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0.5f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PreparingToHandoff() {
    // Inform caller
    ShowBusy ("Continuing Activity...");
}
```

`ContinueUserActivity` `AppDelegate` 將呼叫的，以實際繼續指定的活動。 同樣地，從我們的範例應用程式：

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

`PerformHandoff`每個 View 控制器的公用方法實際上會執行交付，並還原目前裝置上的活動。 在此範例中，它會在使用者在不同裝置上流覽的指定索引標籤中顯示相同的 URL。 範例：

```csharp
private void HideBusy() {

    //Define Animation
    UIView.BeginAnimations("Hide");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PerformHandoff(NSUserActivity activity) {

    // Hide busy indicator
    HideBusy ();

    // Extract URL from dictionary
    var url = activity.UserInfo ["Url"].ToString ();

    // Display value
    URL.Text = url;

    // Display the give webpage
    WebView.LoadRequest(new NSUrlRequest(NSUrl.FromString(url)));

    // Save activity
    UserActivity = activity;
    UserActivity.BecomeCurrent ();

}
```

此 `ContinueUserActivity` 方法包含可 `UIApplicationRestorationHandler` 供您針對以檔或回應者為基礎的活動繼續呼叫的。 呼叫時，您必須將 `NSArray` 或可還原的物件傳遞給還原處理程式。 例如：

```csharp
completionHandler (new NSObject[]{Tab4});
```

針對每個傳遞的物件， `RestoreUserActivityState` 將會呼叫其方法。 然後，每個物件都可以使用字典中的資料 `UserInfo` 來還原其本身的狀態。 例如：

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

若是以檔為基礎的應用程式，如果您未執行 `ContinueUserActivity` 方法或它會傳回 `false` ， `UIKit` 或 `AppKit` 可自動繼續活動。 如需詳細資訊，請參閱下面的「 [以檔為基礎的應用程式支援遞交](#supporting-handoff-in-document-based-apps) 」一節。

### <a name="failing-handoff-gracefully"></a>正常交付失敗

由於移交程式依賴收集鬆散連線的 iOS 和 OS X 裝置之間的資訊傳輸，因此傳輸程式有時可能會失敗。 您應該設計您的應用程式以正常地處理這些失敗，並通知使用者發生任何情況。

如果發生失敗，將會呼叫的 `DidFailToContinueUserActivitiy` 方法 `AppDelegate` 。 例如：

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

您應該使用提供的 `NSError` 來提供有關失敗的資訊給使用者。

## <a name="native-app-to-web-browser-handoff"></a>原生應用程式至網頁瀏覽器的交付

使用者可能會想要繼續進行活動，而不需要在所需的裝置上安裝適當的原生應用程式。 在某些情況下，web 介面可能會提供所需的功能，而且活動仍可繼續。 例如，使用者的電子郵件帳戶可能會提供 web 基底 UI 來撰寫和讀取訊息。

如果原始的原生應用程式知道 web 介面的 URL (以及用來識別要繼續) 之指定專案的必要語法，則可以在實例的屬性中將此資訊編碼 `WebpageURL` `NSUserActivity` 。 如果接收的裝置未安裝適當的原生應用程式來處理接續，則可以呼叫提供的 web 介面。

## <a name="web-browser-to-native-app-handoff"></a>流覽至原生應用程式的網頁瀏覽器

如果使用者在原始裝置上使用 web 型介面，且接收裝置上的原生應用程式宣告屬性的網域部分 `WebpageURL` ，則系統會使用該應用程式來處理接續。 新的裝置將會收到將 `NSUserActivity` 活動類型標示為的實例， `BrowsingWeb` 而且 `WebpageURL` 會包含使用者造訪的 URL，因此 `UserInfo` 字典會是空的。

若要讓應用程式參與這種類型的交付，必須以 (格式的權利宣告網域， `com.apple.developer.associated-domains` `<service>:<fully qualified domain name>` 例如： `activity continuation:company.com`) 。

如果指定的定義域符合 `WebpageURL` 屬性的值，則會從該網域的網站下載已核准的應用程式識別碼清單。 網站必須在名為 **apple-應用** 程式-關聯 (的已簽署 JSON 檔案中提供已核准識別碼的清單，例如 `https://company.com/apple-app-site-association`) 。

此 JSON 檔案包含的字典會指定表單中的應用程式識別碼清單 `<team identifier>.<bundle identifier>` 。 例如：

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

若要將 JSON 檔案簽署 (，使其具有正確 `Content-Type` 的 `application/pkcs7-mime`) ，請使用 **終端** 機應用程式和 `openssl` 命令搭配 iOS 所信任的憑證授權單位單位所發行的憑證和金鑰 (請參閱 [https://support.apple.com/kb/ht5012](https://support.apple.com/kb/ht5012)) 清單。 例如：

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

此 `openssl` 命令會將您放在網站上的已簽署 JSON 檔案，輸出在 **apple-應用程式網站的關聯** URL 上。 例如：

```csharp
https://example.com/apple-app-site-association.
```

應用程式將會收到其 `WebpageURL` 網域具有其權利的任何活動 `com.apple.developer.associated-domains` 。 只有 `http` 和 `https` 通訊協定支援，任何其他通訊協定都會引發例外狀況。

## <a name="supporting-handoff-in-document-based-apps"></a>支援以檔為基礎的應用程式中的交付

如上所述，在 iOS 和 OS X 上，如果應用程式的 **Info. plist** 檔案包含的金鑰，檔型應用程式將會自動支援以 iCloud 為基礎的檔遞交 `CFBundleDocumentTypes` `NSUbiquitousDocumentUserActivityType` 。 例如：

```xml
<key>CFBundleDocumentTypes</key>
<array>
    <dict>
        <key>CFBundleTypeName</key>
        <string>NSRTFDPboardType</string>
        . . .
        <key>LSItemContentTypes</key>
        <array>
        <string>com.myCompany.rtfd</string>
        </array>
        . . .
        <key>NSUbiquitousDocumentUserActivityType</key>
        <string>com.myCompany.myEditor.editing</string>
    </dict>
</array>
```

在此範例中，字串是反向 DNS 應用程式指示項，其中包含附加的活動名稱。 如果以這種方式輸入，活動類型專案不需要在 plist 檔案的陣列中重複 `NSUserActivityTypes` 。 **Info.plist**

自動建立的使用者活動物件 (可透過檔的屬性使用 `UserActivity`) 可供應用程式中的其他物件參考，並且用來在接續時還原狀態。 例如，追蹤專案選取和檔位置。 `NeedsSave` `true` 每當狀態變更，並更新 `UserInfo` 方法中的字典時，您都必須將此活動屬性設定為 `UpdateUserActivityState` 。

您 `UserActivity` 可以從任何執行緒使用此屬性，並符合 (KVO) 通訊協定的索引鍵/值，以便在檔移入和移出 iCloud 時，用來讓檔保持同步。 `UserActivity`當檔關閉時，屬性會失效。

如需詳細資訊，請參閱 Apple [在以檔為基礎的應用程式中的使用者活動支援](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) 檔。

## <a name="supporting-handoff-in-responders"></a>支援回應中的交付

您可以藉由設定其屬性，將回應 (繼承自 `UIResponder` iOS 上的或 `NSResponder` OS X) 繼承至活動 `UserActivity` 。 系統會自動將 `UserActivity` 屬性儲存在適當的時間，呼叫回應程式的 `UpdateUserActivityState` 方法，使用方法將目前的資料加入至使用者活動物件  `AddUserInfoEntriesFromDictionary` 。

## <a name="supporting-continuation-streams"></a>支援接續資料流程

這可能是因為初始的交付承載無法有效率地傳輸繼續活動所需的資訊量。 在這些情況下，接收應用程式可以在其本身與原始應用程式之間建立一或多個串流，以傳輸資料。

原始應用程式會將 `SupportsContinuationStreams` 實例的屬性設定 `NSUserActivity` 為 `true` 。 例如：

```csharp
// Create a new user Activity to support this tab
UserActivity = new NSUserActivity (ThisApp.UserActivityTab1){
    Title = "Weather Tab",
    SupportsContinuationStreams = true
};
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

然後，接收的應用程式就可以 `GetContinuationStreams` `NSUserActivity` 在其中呼叫的方法 `AppDelegate` ，以建立資料流程。 例如：

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

在原始的裝置上，使用者活動委派會藉由呼叫其 `DidReceiveInputStream` 方法來接收串流，以提供要求的資料，以繼續執行繼續裝置上的使用者活動。

您將使用 `NSInputStream` 來提供資料流程資料的唯讀存取權，以及 `NSOutputStream` 提供僅限寫入存取權。 資料流程應該在要求和回應的方式中使用，而接收應用程式會要求更多資料，而原始應用程式會提供它。 因此，寫入來源裝置上輸出資料流程的資料會從繼續裝置上的輸入資料流程讀取，反之亦然。

即使在需要接續資料流程的情況下，這兩個應用程式之間應該會有最少量的來回通訊。

如需詳細資訊，請參閱 Apple 的 [使用接續資料流程](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) 檔。

## <a name="handoff-best-practices"></a>交付最佳作法

透過交付順利完成使用者活動的成功執行，需要謹慎的設計，因為所有相關的元件都有。 Apple 建議採用適用于已啟用交付的應用程式的下列最佳做法：

- 將您的使用者活動設計為需要最小的承載，才能讓活動的狀態與繼續。 承載愈大，接續開始所花費的時間就愈長。
- 如果您必須傳輸大量資料以成功接續，請考慮設定和網路額外負荷的相關成本。
- 大型的 Mac 應用程式通常會在 iOS 裝置上建立由數個較小的工作專屬應用程式所處理的使用者活動。 不同的應用程式和作業系統版本應設計成可妥善運作或正常失敗。
- 指定您的活動類型時，請使用反向 DNS 標記法來避免發生衝突。 如果活動是特定應用程式特有的，其名稱應該包含在類型定義中 (例如 `com.myCompany.myEditor.editing`) 。 如果活動可以跨多個應用程式運作，請從定義中卸載應用程式名稱 (例如 `com.myCompany.editing`) 。
- 如果您的應用程式需要更新使用者活動的狀態 (`NSUserActivity`) 將屬性設 `NeedsSave` 為 `true` 。 在適當的時間，遞交將會呼叫委派的 `UserActivityWillSave` 方法，因此您可以 `UserInfo` 視需要更新字典。
- 因為交付程式可能無法在接收裝置上立即初始化，所以您應該執行， `AppDelegate` `WillContinueUserActivity` 並通知使用者即將開始接續。

## <a name="example-handoff-app"></a>範例提交應用程式

如需在 Xamarin iOS 應用程式中使用遞交的範例，我們已在本指南中包含 [**MonkeyBrowser**](/samples/xamarin/ios-samples/ios8-monkeybrowser) 範例應用程式。 應用程式有四個索引標籤，可供使用者用來流覽網路，每個都有指定的活動類型：氣象、我的最愛、咖啡斷路和工作。

在任何索引標籤上，當使用者輸入新的 URL 並按下 [ **移至** ] 按鈕時，會為該索引標籤建立新的， `NSUserActivity` 其中包含使用者目前流覽的 URL：

[![範例提交應用程式](handoff-images/handoff01.png)](handoff-images/handoff01.png#lightbox)

如果使用者的另一個裝置已安裝 **MonkeyBrowser** 應用程式，則會使用相同的使用者帳戶登入 iCloud、位於相同的網路上，而且在接近上述裝置的位置，接移活動會顯示在左下角) 的主畫面 (：

[![顯示在左下角的主畫面上的交付活動](handoff-images/handoff02.png)](handoff-images/handoff02.png#lightbox)

如果使用者向上拖曳遞交圖示，將會啟動應用程式，並在 `NSUserActivity` 新的裝置上繼續指定的使用者活動：

[![新裝置上的使用者活動繼續](handoff-images/handoff03.png)](handoff-images/handoff03.png#lightbox)

當使用者活動成功傳送到另一個 Apple 裝置時，傳送裝置的 `NSUserActivity` 會在其上收到對方法的呼叫， `UserActivityWasContinued` `NSUserActivityDelegate` 讓它知道使用者活動已成功轉移到另一個裝置。

## <a name="summary"></a>摘要

本文提供了在使用者的多個 Apple 裝置之間，用來繼續使用者活動的交付架構簡介。 接下來，它會示範如何在 Xamarin iOS 應用程式中啟用和執行遞交。 最後，它會討論可用的不同類型的交付延續，以及交付的最佳作法。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [MonkeyBrowser 範例](/samples/xamarin/ios-samples/ios8-monkeybrowser)
- [適用于開發人員的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 的新功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 消費者介面指導方針](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit 架構參考](https://developer.apple.com/library/ios/home_kit_framework_ref)