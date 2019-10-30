---
title: 在 Xamarin 中進行遞交
description: 本文涵蓋在 Xamarin iOS 應用程式中使用遞交功能，在使用者的其他裝置上執行的應用程式之間傳輸使用者活動。
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 97a4a90b66e2c205ef8e9081e6989bf0f3650b16
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032400"
---
# <a name="handoff-in-xamarinios"></a>在 Xamarin 中進行遞交

_本文涵蓋在 Xamarin iOS 應用程式中使用遞交功能，在使用者的其他裝置上執行的應用程式之間傳輸使用者活動。_

Apple 在 iOS 8 和 OS X Yosemite （10.10）中引進了移交，以提供一般機制，讓使用者將其中一個裝置上啟動的活動傳送至另一個執行相同應用程式的裝置，或另一個支援相同活動的應用程式。

[![](handoff-images/handoff02.png "An example of performing a Handoff operation")](handoff-images/handoff02.png#lightbox)

本文將快速瞭解如何在 Xamarin iOS 應用程式中啟用活動共用，並詳細涵蓋交付架構：

## <a name="about-handoff"></a>關於遞交

IOS 8 和 OS X Yosemite （10.10）中的 Apple 引進了遞交（也稱為持續性），這種方式可讓使用者在其中一個裝置（iOS 或 Mac）上啟動活動，並在其裝置上繼續該相同的活動（如使用者的 iClou 所識別d 帳戶）。

IOS 9 中的遞交已擴大，也支援新的增強型搜尋功能。 如需詳細資訊，請參閱我們的[搜尋增強功能](~/ios/platform/search/index.md)檔。

例如，使用者可以在 iPhone 上啟動電子郵件，並順暢地繼續 Mac 上的電子郵件，並將所有相同的訊息資訊填入，並將游標放在 iOS 中的相同位置。

只要應用程式是透過 iTunes App Store 提供，或由已註冊的開發人員（適用于 Mac、企業或臨機操作的應用程式）所簽署，任何共用相同_小組識別碼_的應用程式都有資格使用遞交來繼續跨應用程式進行使用者活動。

任何 `NSDocument` 或 `UIDocument` 為基礎的應用程式都會自動提供內建的遞交支援，而且需要最少的變更才能支援遞交。

### <a name="continuing-user-activities"></a>繼續使用者活動

`NSUserActivity` 類別（以及 `UIKit` 和 `AppKit`的一些小變更）提供了定義使用者活動的支援，可能會在使用者的其他裝置上繼續。

若要將活動傳遞至另一個使用者裝置，則必須將它封裝在實例 `NSUserActivity`中，並標示為_目前的活動_、將其裝載設定（用來執行接續的資料），然後必須將活動傳送至該裝置。

遞交會傳遞最少的資訊來定義要繼續的活動，並透過 iCloud 同步處理較大的資料封包。

在接收的裝置上，使用者會收到一則通知，指出有可用來接續的活動。 如果使用者選擇繼續新裝置上的活動，則會啟動指定的應用程式（如果尚未執行），而且會使用來自 `NSUserActivity` 的承載來重新開機活動。

[![](handoff-images/handoffinteractions.png "An overview of Continuing User Activities")](handoff-images/handoffinteractions.png#lightbox)

只有共用相同開發人員小組識別碼並回應指定_活動類型_的應用程式，才符合接續資格。 應用程式會在**plist**檔案的 `NSUserActivityTypes` 金鑰底下，定義其支援的活動類型。 在此情況下，繼續的裝置會根據小組識別碼、活動類型和選擇性的_活動標題_，選擇要執行接續的應用程式。

接收端應用程式會使用 `NSUserActivity`的 `UserInfo` 字典中的資訊來設定其使用者介面，並還原指定活動的狀態，讓使用者可以順暢地進行轉換。

如果接續需要的資訊超過可透過 `NSUserActivity`有效率地傳送的資訊，則繼續執行的應用程式可以傳送呼叫給原始應用程式，並建立一或多個資料流程來傳輸所需的資料。 例如，如果活動正在編輯具有多個影像的大型文字檔，則需要進行串流以傳輸在接收的裝置上繼續活動所需的資訊。 如需詳細資訊，請參閱下面的[支援接續資料流程](#supporting-continuation-streams)一節。

如上所述，`NSDocument` 或 `UIDocument` 為基礎的應用程式會自動提供內建的遞交支援。 如需詳細資訊，請參閱下面的[以檔為基礎的應用程式支援遞交](#supporting-handoff-in-document-based-apps)一節。

### <a name="the-nsuseractivity-class"></a>NSUserActivity 類別

`NSUserActivity` 類別是遞交交換中的主要物件，可用來封裝可用於接續的使用者活動狀態。 應用程式會為其支援的任何活動具現化 `NSUserActivity` 複本，並希望在另一部裝置上繼續。 例如，檔編輯器會針對目前開啟的每個檔建立活動。 不過，只有最前面的檔（顯示在最前面的視窗或索引標籤中）是_目前的活動_，而且因此可用於接續。

`NSUserActivity` 的實例是由它的 `ActivityType` 和 `Title` 屬性所識別。 `UserInfo` dictionary 屬性是用來攜帶活動狀態的相關資訊。 如果您想要透過 `NSUserActivity`的委派延遲載入狀態資訊，請將 `NeedsSave` 屬性設定為 [`true`]。 您可以使用 `AddUserInfoEntries` 方法，視需要將其他用戶端的新資料合併到 `UserInfo` 字典中，以保留活動的狀態。

### <a name="the-nsuseractivitydelegate-class"></a>NSUserActivityDelegate 類別

`NSUserActivityDelegate` 可用來將 `NSUserActivity`的 `UserInfo` 字典中的資訊保持在最新狀態，並與活動的目前狀態同步。 當系統需要更新活動中的資訊時（例如，在另一個裝置上接續之前），它會呼叫委派的 `UserActivityWillSave` 方法。

您必須執行 `UserActivityWillSave` 方法，並對 `NSUserActivity` （例如 `UserInfo`、`Title`等）進行任何變更，以確保它仍會反映目前活動的狀態。 當系統呼叫 `UserActivityWillSave` 方法時，將會清除 `NeedsSave` 旗標。 如果您修改活動的任何資料屬性，就必須將 `NeedsSave` 設定為 [`true`]。

您可以選擇性地讓 `UIKit` 或 `AppKit` 自動管理使用者活動，而不是使用上述的 `UserActivityWillSave` 方法。 若要這麼做，請設定回應者物件的 `UserActivity` 屬性，並執行 `UpdateUserActivityState` 方法。 如需詳細資訊，請參閱下面的「[回應中的支援遞交](#supporting-handoff-in-responders)」一節。

### <a name="app-framework-support"></a>應用程式架構支援

`UIKit` （iOS）和 `AppKit` （OS X）都會在 `NSDocument`、回應者（`UIResponder`/`NSResponder`）和 `AppDelegate` 類別中提供內建支援。 雖然每個 OS 會以稍有不同的方式來執行交付，但基本機制和 Api 都相同。

#### <a name="user-activities-in-document-based-apps"></a>以檔為基礎的應用程式中的使用者活動

以檔為基礎的 iOS 和 OS X 應用程式會自動提供內建的遞交支援。 若要啟用這項支援，您必須在應用程式的**plist**檔案中新增每個 `CFBundleDocumentTypes` 專案的 `NSUbiquitousDocumentUserActivityType` 金鑰和值。

如果此機碼存在，`NSDocument` 和 `UIDocument` 會自動為指定類型的 iCloud 檔建立 `NSUserActivity` 實例。 您必須為應用程式支援的每一種檔案類型提供活動類型，而且多個檔案類型可以使用相同的活動類型。 `NSDocument` 和 `UIDocument` 都會使用其 `FileURL` 屬性的值，自動填入 `NSUserActivity` 的 `UserInfo` 屬性。

在 OS X 上，`AppKit` 所管理且與回應程式相關聯的 `NSUserActivity`，會在檔的視窗變成主要視窗時，自動成為目前的活動。 在 iOS 上，針對 `UIKit`所管理的 `NSUserActivity` 物件，您必須在應用程式進入前景時，明確地呼叫 `BecomeCurrent` 方法，或在 `UIViewController` 上設定檔的 `UserActivity` 屬性。

`AppKit` 會自動還原在 OS X 上以這種方式建立的任何 `UserActivity` 屬性。如果 `ContinueUserActivity` 方法傳回 `false` 或未執行，就會發生這種情況。 在此情況下，檔會以 `NSDocumentController` 的 `OpenDocument` 方法開啟，然後它會收到 `RestoreUserActivityState` 方法呼叫。

如需詳細資訊，請參閱下面的以[檔為基礎的應用程式支援遞交](#supporting-handoff-in-document-based-apps)一節。

#### <a name="user-activities-and-responders"></a>使用者活動和回應程式

`UIKit` 和 `AppKit` 都可以自動管理使用者活動，如果您將它設定為回應者物件的 `UserActivity` 屬性。 如果狀態已經過修改，您就必須將回應者 `UserActivity` 的 `NeedsSave` 屬性設定為 [`true`]。 系統會在必要時自動儲存 `UserActivity`，並藉由呼叫其 `UpdateUserActivityState` 方法，讓回應者時間更新狀態。

如果多個回應程式共用一個 `NSUserActivity` 實例，當系統更新使用者活動物件時，就會收到 `UpdateUserActivityState` 回呼。 回應程式必須呼叫 `AddUserInfoEntries` 方法，以在此時更新 `NSUserActivity`的 `UserInfo` 字典，以反映目前的活動狀態。 在每個 `UpdateUserActivityState` 呼叫之前，會清除 `UserInfo` 字典。

若要將本身與活動解除關聯，回應者可以將其 `UserActivity` 屬性設為 `null`。 當應用程式架構 managed `NSUserActivity` 實例沒有更多相關聯的回應或檔時，它會自動失效。

如需詳細資訊，請參閱下面的「[回應中的支援遞交](#supporting-handoff-in-responders)」一節。

#### <a name="user-activities-and-the-appdelegate"></a>使用者活動和 AppDelegate

應用程式的 `AppDelegate` 是處理遞交接續時的主要進入點。 當使用者回應遞交通知時，會啟動適當的應用程式（如果尚未執行），並呼叫 `AppDelegate` 的 `WillContinueUserActivityWithType` 方法。 此時，應用程式應通知使用者正在啟動接續。

呼叫 `AppDelegate`的 `ContinueUserActivity` 方法時，會傳遞 `NSUserActivity` 實例。 此時，您應該設定應用程式的使用者介面，並繼續指定的活動。

如需詳細資訊，請參閱下面的

## <a name="enabling-handoff-in-a-xamarin-app"></a>在 Xamarin 應用程式中啟用遞交

基於遞交的安全性需求，使用遞交架構的 Xamarin iOS 應用程式必須在 Apple 開發人員入口網站和 Xamarin 專案檔中正確設定。

請執行下列動作：

1. 登入[Apple Developer 入口網站](https://developer.apple.com)。
2. 按一下 [**憑證]、[識別碼] & 設定檔**。
3. 如果您尚未這麼做，請按一下 [**識別碼**]，然後為您的應用程式建立識別碼（例如 `com.company.appname`），或者編輯現有的識別碼。
4. 確定已檢查指定識別碼的**iCloud**服務：

    [![](handoff-images/provision01.png "Enable the iCloud service for the given ID")](handoff-images/provision01.png#lightbox)
5. 儲存您的變更。
6. 按一下 [布建**設定檔** > **開發**]，然後為您的應用程式建立新的開發布建設定檔：

    [![](handoff-images/provision02.png "Create a new development provisioning profile for the app")](handoff-images/provision02.png#lightbox)
7. 請下載並安裝新的布建設定檔，或使用 Xcode 來下載並安裝設定檔。
8. 編輯您的 Xamarin iOS 專案選項，並確定您使用的是您剛才建立的布建設定檔：

    [![](handoff-images/provision03.png "Select the provisioning profile just created")](handoff-images/provision03.png#lightbox)
9. 接下來，編輯您的**plist**檔案，並確定您使用的是用來建立布建設定檔的應用程式識別碼：

    [![](handoff-images/provision04.png "Set App ID")](handoff-images/provision04.png#lightbox)
10. 流覽至 [**背景模式**] 區段，並檢查下列專案：

    [![](handoff-images/provision05.png "Enable the required background modes")](handoff-images/provision05.png#lightbox)
11. 將變更儲存至所有檔案。

這些設定都備妥之後，應用程式就可以開始存取「遞交架構 Api」。 如需布建的詳細資訊，請參閱我們的[裝置](~/ios/get-started/installation/device-provisioning/index.md)布建和布建[您的應用程式](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="implementing-handoff"></a>執行遞交

使用者活動可以在使用相同開發人員小組識別碼簽署的應用程式之間繼續進行，並支援相同的活動類型。 在 Xamarin iOS 應用程式中執行交付時，您必須建立使用者活動物件（在 `UIKit` 或 `AppKit`）、更新物件的狀態以追蹤活動，以及繼續接收裝置上的活動。

### <a name="identifying-user-activities"></a>識別使用者活動

執行遞交的第一個步驟是識別您的應用程式支援的使用者活動類型，並查看哪些活動適合在另一個裝置上接續。 例如： ToDo 應用程式可能支援將專案編輯為一種_使用者活動類型_，並支援以另一種方式流覽可用的專案清單。

應用程式可以視需要建立多個使用者活動類型，一個用於應用程式提供的任何功能。 針對每個使用者活動類型，應用程式必須追蹤類型的活動開始和結束的時間，而且需要維護最新狀態資訊，才能在另一部裝置上繼續該工作。

使用者活動可以在使用相同小組識別碼簽署的任何應用程式上繼續進行，而不需要在傳送和接收應用程式之間進行任何一對一的對應。 例如，指定的應用程式可以建立四種不同類型的活動，由不同的個別應用程式在另一個裝置上使用。 這是應用程式 Mac 版本（可能有許多功能和功能）和 iOS 應用程式之間的常見情況，其中每個應用程式較小且著重于特定的工作。

### <a name="creating-activity-type-identifiers"></a>建立活動類型識別碼

_活動類型識別碼_是一個簡短字串，已新增至應用程式**plist**檔案的 `NSUserActivityTypes` 陣列，用來唯一識別指定的使用者活動類型。 在陣列中，應用程式支援的每個活動都會有一個專案。 Apple 建議針對活動類型識別碼使用反向 DNS 樣式的標記法，以避免發生衝突。 例如： `com.company-name.appname.activity` 特定應用程式的活動，或可跨多個應用程式執行之活動的 `com.company-name.activity`。

建立 `NSUserActivity` 實例以識別活動類型時，會使用活動類型識別碼。 當另一個裝置上的活動繼續時，活動類型（以及應用程式的小組識別碼）會決定要啟動哪一個應用程式，以繼續進行活動。

例如，我們將建立名為**MonkeyBrowser**的範例應用程式（[這裡下載](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)）。 此應用程式會顯示四個索引標籤，每個索引標籤在網頁瀏覽器視圖中開啟不同的 URL。 使用者將能夠繼續執行應用程式之不同 iOS 裝置上的任何索引標籤。

若要建立必要的活動類型識別碼以支援此行為，請編輯**plist**檔案，並切換至**來源**視圖。 新增 `NSUserActivityTypes` 金鑰，並建立下列識別碼：

[![](handoff-images/type01.png "The NSUserActivityTypes key and required identifiers in the plist editor")](handoff-images/type01.png#lightbox)

我們已建立四個新的活動類型識別碼，一個用於範例**MonkeyBrowser**應用程式中的每個索引標籤。 建立您自己的應用程式時，請將 `NSUserActivityTypes` 陣列的內容取代為您的應用程式支援的活動特有的活動類型識別碼。

### <a name="tracking-user-activity-changes"></a>追蹤使用者活動變更

當我們建立 `NSUserActivity` 類別的新實例時，我們會指定 `NSUserActivityDelegate` 實例，以追蹤活動狀態的變更。 例如，您可以使用下列程式碼來追蹤狀態變更：

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

當接續資料流程已從傳送裝置接收資料時，會呼叫 `UserActivityReceivedData` 方法。 如需詳細資訊，請參閱下面的[支援接續資料流程](#supporting-continuation-streams)一節。

當另一個裝置已從目前裝置接管活動時，會呼叫 `UserActivityWasContinued` 方法。 視活動的類型而定（例如，將新專案新增至待辦事項清單），應用程式可能需要中止傳送裝置上的活動。

系統會先呼叫 `UserActivityWillSave` 方法，才會在本機可用的裝置上儲存及同步處理活動的任何變更。 在傳送之前，您可以使用這個方法，對 `NSUserActivity` 實例的 `UserInfo` 屬性進行最後一分鐘的變更。

### <a name="creating-a-nsuseractivity-instance"></a>建立 NSUserActivity 實例

您的應用程式希望能夠在另一部裝置上繼續執行的每個活動，都必須封裝在 `NSUserActivity` 實例中。 應用程式可以視需要建立多個活動，而這些活動的本質取決於有問題之應用程式的功能和功能。 例如，電子郵件應用程式可能會建立一個活動來建立新的訊息，另一個則用於讀取訊息。

針對我們的範例應用程式，每次使用者在其中一個索引標籤式 web 瀏覽器視圖中輸入新的 URL 時，就會建立新的 `NSUserActivity`。 下列程式碼會儲存指定索引標籤的狀態：

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

它會使用上述所建立的其中一個使用者活動類型建立新的 `NSUserActivity`，並為活動提供人們看得懂的標題。 它會附加至上面所建立的 `NSUserActivityDelegate` 實例，以監看狀態變更，並通知 iOS 此使用者活動為目前的活動。

### <a name="populating-the-userinfo-dictionary"></a>填入使用者資訊字典

如先前所見，`NSUserActivity` 類別的 `UserInfo` 屬性是用來定義指定活動狀態的索引鍵/值組 `NSDictionary`。 儲存在 `UserInfo` 中的值必須是下列其中一種類型： `NSArray`、`NSData`、`NSDate`、`NSDictionary`、`NSNull`、`NSNumber`、`NSSet`、`NSString`或 `NSURL`。 指向 iCloud 檔的 `NSURL` 資料值會自動調整，使其指向接收裝置上的相同檔。

在上述範例中，我們建立了一個 `NSMutableDictionary` 物件，並以提供使用者目前在指定索引標籤上所看到之 URL 的單一索引鍵來填入它。使用者活動的 `AddUserInfoEntries` 方法是用來更新活動，以及將用來在接收裝置上還原活動的資料：

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple 建議將資訊傳送至極度匱乏最小值，以確保活動會及時傳送到接收的裝置。 如果需要較大的資訊（例如附加至檔的影像需要進行編輯），您應該使用接續資料流程。 如需詳細資訊，請參閱下面的[支援接續資料流程](#supporting-continuation-streams)一節。

### <a name="continuing-an-activity"></a>繼續活動

遞交作業會自動通知與原始裝置實體鄰近的本機 iOS 和 OS X 裝置，並登入相同的 iCloud 帳戶，以提供持續性使用者活動的可用性。 如果使用者選擇繼續新裝置上的活動，系統會啟動適當的應用程式（根據小組識別碼和活動類型），並提供其 `AppDelegate` 接續必須發生的資訊。

首先，會呼叫 `WillContinueUserActivityWithType` 方法，讓應用程式可以通知使用者，接續即將開始。 我們會在範例應用程式的**AppDelegate.cs**檔案中使用下列程式碼，以處理接續的開始：

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

在上述範例中，每個 View Controller 都會向 `AppDelegate` 註冊，並具有公開的 `PreparingToHandoff` 方法，可顯示活動指標和訊息，讓使用者知道活動即將交給目前的裝置。 範例：

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

將呼叫 `AppDelegate` 的 `ContinueUserActivity`，以實際繼續指定的活動。 同樣地，從我們的範例應用程式：

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

每個 View Controller 的公用 `PerformHandoff` 方法實際上會執行遞交，並在目前的裝置上還原活動。 在範例中，它會在指定的索引標籤中，顯示使用者在不同裝置上流覽的相同 URL。 範例：

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

`ContinueUserActivity` 方法包含的 `UIApplicationRestorationHandler`，可讓您針對以檔或回應者為基礎的活動繼續進行呼叫。 呼叫時，您必須將 `NSArray` 或可還原的物件傳遞至還原處理程式。 例如:

```csharp
completionHandler (new NSObject[]{Tab4});
```

針對每個傳遞的物件，將會呼叫其 `RestoreUserActivityState` 方法。 然後，每個物件都可以使用 `UserInfo` 字典中的資料來還原其本身的狀態。 例如:

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

針對以檔為基礎的應用程式，如果您未執行 `ContinueUserActivity` 方法，或它會傳回 `false`，`UIKit` 或 `AppKit` 可以自動繼續活動。 如需詳細資訊，請參閱下面的以[檔為基礎的應用程式支援遞交](#supporting-handoff-in-document-based-apps)一節。

### <a name="failing-handoff-gracefully"></a>無法正常地遞交

由於遞交會依賴集合鬆散連線的 iOS 和 OS X 裝置之間的資訊傳輸，因此傳輸程式有時可能會失敗。 您應該設計您的應用程式來適當地處理這些失敗，並告知使用者發生的任何狀況。

如果發生失敗，將會呼叫 `AppDelegate` 的 `DidFailToContinueUserActivitiy` 方法。 例如:

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

您應該使用提供的 `NSError`，將有關失敗的資訊提供給使用者。

## <a name="native-app-to-web-browser-handoff"></a>原生應用程式到網頁瀏覽器的交付

使用者可能會想要繼續執行活動，而不需要在所需的裝置上安裝適當的原生應用程式。 在某些情況下，web 型介面可能會提供所需的功能，而活動仍然可以繼續。 例如，使用者的電子郵件帳戶可能會提供用來撰寫和讀取訊息的 web 基底 UI。

如果原始的原生應用程式知道 web 介面的 URL （以及用來識別要繼續之指定專案的必要語法），則可以在 `NSUserActivity` 實例的 `WebpageURL` 屬性中編碼這項資訊。 如果接收的裝置未安裝適當的原生應用程式來處理接續，則可以呼叫提供的 web 介面。

## <a name="web-browser-to-native-app-handoff"></a>Web 瀏覽器至原生應用程式交付

如果使用者在原始裝置上使用 web 型介面，而接收端裝置上的原生應用程式宣告 `WebpageURL` 屬性的網域部分，則系統會使用該應用程式來處理接續。 新裝置會收到將活動類型標示為 `BrowsingWeb` 的 `NSUserActivity` 實例，而 `WebpageURL` 將包含使用者所造訪的 URL，`UserInfo` 字典將會是空的。

若要讓應用程式參與這種類型的交付，它必須以 `<service>:<fully qualified domain name>` 格式的 `com.apple.developer.associated-domains` 權利宣告網域（例如： `activity continuation:company.com`）。

如果指定的網域符合 `WebpageURL` 屬性的值，則會從該網域的網站下載已核准的應用程式識別碼清單。 網站必須提供已簽署 JSON 檔案中的已核准識別碼清單，名為**apple-應用程式-網站關聯**（例如 `https://company.com/apple-app-site-association`）。

此 JSON 檔案包含一個字典，其中指定 `<team identifier>.<bundle identifier>`格式的應用程式識別碼清單。 例如:

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

若要簽署 JSON 檔案（使其具有正確的 `application/pkcs7-mime``Content-Type`），請使用**終端**機應用程式和 `openssl` 命令，以及由 iOS 信任的憑證授權單位單位所發行的憑證和金鑰（請參閱[https://support.apple.com/kb/ht5012](https://support.apple.com/kb/ht5012)以取得清單）。 例如:

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

`openssl` 命令會將您放在網站上的已簽署 JSON 檔案輸出至 apple--------- **site-關聯**URL。 例如:

```csharp
https://example.com/apple-app-site-association.
```

應用程式將會收到其 `WebpageURL` 網域 `com.apple.developer.associated-domains` 權利的任何活動。 只有 `http` 和 `https` 通訊協定支援，任何其他通訊協定都會引發例外狀況。

## <a name="supporting-handoff-in-document-based-apps"></a>支援以檔為基礎的應用程式中的遞交

如上所述，在 iOS 和 OS X 上，如果應用程式的**plist**檔案包含 `NSUbiquitousDocumentUserActivityType`的 `CFBundleDocumentTypes` 金鑰，則以檔為基礎的應用程式會自動支援以 iCloud 為基礎的檔遞交。 例如:

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

在此範例中，字串是使用附加活動名稱的反向 DNS 應用程式指示項。 如果以這種方式輸入，則不需要在**plist**檔案的 `NSUserActivityTypes` 陣列中重複活動類型專案。

自動建立的使用者活動物件（可透過檔的 `UserActivity` 屬性取得）可以由應用程式中的其他物件參考，並在接續時用來還原狀態。 例如，用來追蹤專案選取範圍和檔位置。 當狀態變更時，您必須將此活動 `NeedsSave` 屬性設定為 `true`，並在 `UpdateUserActivityState` 方法中更新 `UserInfo` 的字典。

`UserActivity` 屬性可以從任何執行緒使用，並符合索引鍵-值觀察（KVO）通訊協定，因此可以用來在檔移入和移出 iCloud 時保持同步。 當檔關閉時，`UserActivity` 屬性將會失效。

如需詳細資訊，請參閱 Apple[在以檔為基礎的應用程式中的使用者活動支援](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5)檔。

## <a name="supporting-handoff-in-responders"></a>在回應程式中支援遞交

您可以藉由設定其 `UserActivity` 屬性，將回應程式（繼承自 iOS 上的 `UIResponder` 或 OS X 上的 `NSResponder`）關聯至活動。 系統會在適當的時間自動儲存 `UserActivity` 屬性，呼叫回應者的 `UpdateUserActivityState` 方法，使用 `AddUserInfoEntriesFromDictionary` 方法，將目前的資料加入至使用者活動物件。

## <a name="supporting-continuation-streams"></a>支援接續資料流程

可能是因為初始遞交裝載無法有效率地傳輸繼續活動所需的資訊量。 在這些情況下，接收應用程式可以在其本身與原始應用程式之間建立一或多個串流，以傳輸資料。

原始應用程式會將 `NSUserActivity` 實例的 `SupportsContinuationStreams` 屬性設定為 [`true`]。 例如:

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

然後，接收應用程式就可以在其 `AppDelegate` 中呼叫 `NSUserActivity` 的 `GetContinuationStreams` 方法，以建立資料流程。 例如:

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

在原始裝置上，使用者活動委派會藉由呼叫其 `DidReceiveInputStream` 方法來接收串流，以提供所要求的資料，以繼續執行裝置上的使用者活動。

您將使用 `NSInputStream` 來提供串流資料的唯讀存取權，而 `NSOutputStream` 提供僅限寫入的存取權。 串流應用於要求和回應的方式，其中接收的應用程式會要求更多資料，而原始應用程式會提供它。 如此一來，在來源裝置上寫入輸出資料流程的資料，就會從繼續裝置上的輸入資料流程讀取，反之亦然。

即使在需要接續資料流程的情況下，這兩個應用程式之間應該會有最少的來回通訊。

如需詳細資訊，請參閱 Apple 的[使用接續資料流程](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13)檔。

## <a name="handoff-best-practices"></a>遞交最佳做法

透過遞交順利完成使用者活動的成功，需要謹慎設計，因為涉及所有的各種元件。 Apple 建議採用下列適用于您的已啟用應用程式的最佳作法：

- 將您的使用者活動設計成需要最小的承載，才能讓活動的狀態與持續性產生關聯。 承載越大，接續開始的時間就越長。
- 如果您必須傳輸大量資料以成功接續，請考慮設定和網路額外負荷所牽涉到的成本。
- 大型 Mac 應用程式通常會在 iOS 裝置上建立由數個較小的工作特定應用程式所處理的使用者活動。 不同的應用程式和作業系統版本應設計成妥善運作或正常失敗。
- 指定活動類型時，請使用反向 DNS 標記法來避免衝突。 如果活動專屬於特定的應用程式，其名稱應該包含在型別定義中（例如 `com.myCompany.myEditor.editing`）。 如果活動可以跨多個應用程式工作，請從定義中卸載應用程式名稱（例如 `com.myCompany.editing`）。
- 如果您的應用程式需要更新使用者活動的狀態（`NSUserActivity`），請將 `NeedsSave` 屬性設定為 [`true`]。 在適當的時間，遞交會呼叫委派的 `UserActivityWillSave` 方法，以便您可以視需要更新 `UserInfo` 字典。
- 由於遞交程式可能無法在接收的裝置上立即初始化，因此您應該執行 `AppDelegate`的 `WillContinueUserActivity`，並告知使用者即將啟動的接續。

## <a name="example-handoff-app"></a>範例遞交應用程式

如需在 Xamarin iOS 應用程式中使用遞交的範例，我們已在本指南中包含[**MonkeyBrowser**](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)範例應用程式。 應用程式有四個索引標籤，可供使用者用來流覽 web，每個索引標籤都有指定的活動類型：天氣、我的最愛、咖啡中斷和工作。

在任何索引標籤上，當使用者輸入新的 URL 並**點擊 [執行**] 按鈕時，就會為該索引標籤建立新的 `NSUserActivity`，其中包含使用者目前流覽的 url：

[![](handoff-images/handoff01.png "Example Handoff App")](handoff-images/handoff01.png#lightbox)

如果使用者的其他裝置已安裝**MonkeyBrowser**應用程式、使用相同的使用者帳戶登入 iCloud、位於相同的網路上，且接近上述裝置，則會在主畫面上顯示 [遞交] 活動（位於較低的左手邊）：

[![](handoff-images/handoff02.png "The Handoff Activity displayed on the home screen in the lower left hand corner")](handoff-images/handoff02.png#lightbox)

如果使用者向上拖曳 [遞交] 圖示，應用程式將會啟動，而在 `NSUserActivity` 中指定的使用者活動會在新的裝置上繼續進行：

[![](handoff-images/handoff03.png "The User Activity continued on the new device")](handoff-images/handoff03.png#lightbox)

使用者活動成功傳送至另一個 Apple 裝置時，傳送裝置的 `NSUserActivity` 將會在其 `NSUserActivityDelegate` 上收到 `UserActivityWasContinued` 方法的呼叫，讓它知道使用者活動已成功轉移至另一個裝置。

## <a name="summary"></a>總結

本文已介紹用來在多個使用者的 Apple 裝置之間繼續使用者活動的遞交架構簡介。 接下來，它會示範如何在 Xamarin iOS 應用程式中啟用和執行遞交。 最後，它討論了各種不同類型的交付接續和交付的最佳作法。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [MonkeyBrowser 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 中的新功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 使用者介面指導方針](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit 架構參考](https://developer.apple.com/library/ios/home_kit_framework_ref)
