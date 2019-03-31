---
title: 在 Xamarin.iOS 中遞交
description: 這篇文章，涵蓋了使用中的 Xamarin.iOS 應用程式，將傳送的遞移式使用者上執行的應用程式之間的使用者活動的其他裝置。
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 1a5cc9f06fdca5944a9a3201ac15d63ca7f15453
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677959"
---
# <a name="handoff-in-xamarinios"></a>在 Xamarin.iOS 中遞交

_這篇文章，涵蓋了使用中的 Xamarin.iOS 應用程式，將傳送的遞移式使用者上執行的應用程式之間的使用者活動的其他裝置。_

Apple 已引進遞移式 iOS 8 和 OS X Yosemite (10.10) 提供通用的機制，讓使用者傳輸已啟動他們的裝置，其中的活動中執行相同的應用程式或支援相同的活動的另一個應用程式的另一個裝置。

[![](handoff-images/handoff02.png "執行遞移式作業的範例")](handoff-images/handoff02.png#lightbox)

本文將快速看一下啟用共用的 Xamarin.iOS 應用程式中的活動，並涵蓋詳細的遞移式架構：

## <a name="about-handoff"></a>關於遞交

遞交 （也就是持續性） 是由 Apple 在 iOS 8 和 OS X Yosemite (10.10) 引入做為針對其裝置 （iOS 或 Mac） 的其中一個開始活動，並繼續相同的活動，在他們的裝置 （如使用者的 iClou 所識別的另一個使用者的方式d 帳戶)。

增強的搜尋功能時，已在 iOS 9，也支援新的、 擴充遞交。 如需詳細資訊，請參閱我們[搜尋增強功能](~/ios/platform/search/index.md)文件。

比方說，使用者可以啟動其 iPhone 上的電子郵件，並順暢地繼續其 Mac 的所有相同的訊息資訊填入和他們離開時的狀態在 iOS 中的相同位置中的資料指標中的 電子郵件。

您會共用相同的應用程式的任何_小組識別碼_是合格的使用者活動繼續跨應用程式，只要這些應用程式都是使用遞移式傳遞透過 iTunes App Store 或帶正負號的已註冊的開發人員 （適用於 Mac、 Enterprise或臨機操作的應用程式）。

任何`NSDocument`或`UIDocument`基礎的應用程式會自動擁有遞移式支援內建，而且需要最少的變更，以便支援遞交。

### <a name="continuing-user-activities"></a>繼續使用者活動

`NSUserActivity`類別 (一些小變更，以及`UIKit`和`AppKit`) 來定義使用者的活動，可能可以繼續在其他使用者的裝置提供支援。

活動透過傳遞至另一個使用者的裝置，它必須封裝在執行個體`NSUserActivity`、 標示為_目前活動_，有其裝載組 （用來執行接續的資料），然後，活動必須傳輸到該裝置。

遞移式傳遞的資訊來定義活動，以繼續執行，具有較大的資料封包透過 iCloud 要同步的最低限度。

在接收端的裝置，使用者會收到通知，活動是適用於接續。 （如果尚未執行），如果使用者選擇新的裝置上繼續活動時，會啟動指定的應用程式和從裝載`NSUserActivity`用來重新啟動活動。

[![](handoff-images/handoffinteractions.png "繼續使用者活動的概觀")](handoff-images/handoffinteractions.png#lightbox)

只有應用程式，共用相同的開發人員小組識別碼，並對給定_活動型別_適合接續。 應用程式定義它支援在活動型別`NSUserActivityTypes`的索引鍵及其**Info.plist**檔案。 如此一來，持續的裝置選擇應用程式執行將 「 小組識別碼，活動類型為基礎的接續並選擇性地_活動標題_。

接收的應用程式會使用來自`NSUserActivity`的`UserInfo`設定其使用者介面，並還原指定活動的狀態，使轉換看起來不讓終端使用者的字典。

如果接續會要求資訊更多，可以有效率地透過傳送`NSUserActivity`，正在繼續應用程式可以傳送呼叫給原始應用程式，並建立一或多個資料流傳輸所需的資料。 例如，如果活動已編輯與多個映像的大型文字文件，串流需要傳輸接收的裝置上繼續活動所需的資訊。 如需詳細資訊，請參閱 <<c0> [ 支援接續資料流](#supporting-continuation-streams)下一節。

如上面所述`NSDocument`或`UIDocument`基礎的應用程式會自動擁有支援內建的遞交。 如需詳細資訊，請參閱 <<c0> [ 文件為基礎的應用程式中支援的遞交](#supporting-handoff-in-document-based-apps)下一節。

### <a name="the-nsuseractivity-class"></a>NSUserActivity 類別

`NSUserActivity`類別是遞移式交換中的主要物件，以及用來封裝適用於接續的使用者活動的狀態。 應用程式會具現化一份`NSUserActivity`它支援，而且想要繼續在其他裝置上的任何活動。 例如，文件編輯器會建立每個文件的活動目前開啟。 不過，只有最前面顯示的文件 （在最前面 視窗或索引標籤） 是_目前的活動_，因此適用於接續。

執行個體`NSUserActivity`兩者都由其`ActivityType`和`Title`屬性。 `UserInfo`字典屬性用來執行活動的狀態資訊。 設定`NeedsSave`屬性，以`true`如果您想要為消極式載入狀態資訊，透過`NSUserActivity`的委派。 使用`AddUserInfoEntries`方法，以從其他用戶端至新的資料合併`UserInfo`字典為必要項目以保留活動的狀態。

### <a name="the-nsuseractivitydelegate-class"></a>NSUserActivityDelegate 類別

`NSUserActivityDelegate`用來將資訊保存在`NSUserActivity`的`UserInfo`字典最新狀態和活動的目前狀態同步。 當系統需要更新的活動中的資訊 (例如另一個裝置上的接續之前)，它會呼叫`UserActivityWillSave`委派方法。

您必須實作`UserActivityWillSave`方法，並進行任何變更要`NSUserActivity`(例如`UserInfo`，`Title`等) 以確保它仍然會反映目前活動的狀態。 當系統呼叫`UserActivityWillSave`方法，`NeedsSave`旗標將會被清除。 如果您修改任何活動的資料屬性，您必須設定`NeedsSave`至`true`一次。

而不是使用`UserActivityWillSave`上述的方法，您可以選擇性地讓`UIKit`或`AppKit`自動管理的使用者活動。 若要這樣做，請將設定回應物件的`UserActivity`屬性並實作`UpdateUserActivityState`方法。 請參閱[在回應中支援的遞交](#supporting-handoff-in-responders)如需詳細資訊，如下一節。

### <a name="app-framework-support"></a>應用程式架構支援

兩者`UIKit`(iOS) 和`AppKit`(OS X) 提供內建的支援，以在遞交`NSDocument`，回應者 (`UIResponder`/`NSResponder`)，和`AppDelegate`類別。 雖然每個作業系統實作遞移式的方式稍有不同，基本的機制和 Api 都相同。

#### <a name="user-activities-in-document-based-apps"></a>在文件為基礎的應用程式中的使用者活動

文件為基礎的 iOS 和 OS X 應用程式自動擁有內建的遞移式支援。 若要啟用這項支援，您必須新增`NSUbiquitousDocumentUserActivityType`索引鍵和值都`CFBundleDocumentTypes`的應用程式中的項目**Info.plist**檔案。

如果此機碼存在，同時`NSDocument`並`UIDocument`自動建立`NSUserActivity`iCloud 為基礎的文件的指定之類型的執行個體。 您必須提供每種類型的應用程式支援的文件的活動類型和多個文件類型都可以使用相同的活動型別。 兩者`NSDocument`並`UIDocument`自動填入`UserInfo`屬性`NSUserActivity`具有其`FileURL`屬性的值。

在 OS X 上`NSUserActivity`受`AppKit`和自動與回應相關聯的文件視窗變成主視窗時，成為目前的活動。 在 iOS 上，針對`NSUserActivity`受管理的物件`UIKit`，您必須呼叫`BecomeCurrent`方法明確或有文件的`UserActivity`上設定屬性`UIViewController`當應用程式則來到前景。

`AppKit` 會自動還原任何`UserActivity`以此方式，在 OS X 上建立的屬性。會發生這個錯誤`ContinueUserActivity`方法會傳回`false`或者它是未實作。 在此情況下，以開啟文件`OpenDocument`方法`NSDocumentController`，將會接到`RestoreUserActivityState`方法呼叫。

請參閱[文件為基礎的應用程式中支援的遞交](#supporting-handoff-in-document-based-apps)如需詳細資訊，如下一節。

#### <a name="user-activities-and-responders"></a>使用者活動和回應

兩者`UIKit`並`AppKit`自動管理使用者活動，如果您將它設定為回應物件的`UserActivity`屬性。 如果已修改狀態，您必須設定`NeedsSave`回應程式的屬性`UserActivity`至`true`。 系統會自動儲存`UserActivity`時所需，在給予回應時間，以更新的狀態，藉由呼叫後其`UpdateUserActivityState`方法。

如果多個回應共用單一`NSUserActivity`執行個體，接到`UpdateUserActivityState`回呼時，系統會更新使用者的活動物件。 回應程式需要呼叫`AddUserInfoEntries`方法來更新`NSUserActivity`的`UserInfo`此時反映目前的活動狀態的字典。 `UserInfo`字典會清除每一個前面`UpdateUserActivityState`呼叫。

若要取消本身關聯的活動，回應者可以設定其`UserActivity`屬性設`null`。 應用程式架構的管理時`NSUserActivity`執行個體有沒有更多相關聯的回應或文件，就會自動失效。

請參閱[在回應中支援的遞交](#supporting-handoff-in-responders)如需詳細資訊，如下一節。

#### <a name="user-activities-and-the-appdelegate"></a>使用者活動和 AppDelegate

您的應用程式`AppDelegate`處理遞移式接續時，是它的主要進入點。 當使用者回應遞移式通知，適當的應用程式啟動 （如果沒有已在執行中） 和`WillContinueUserActivityWithType`方法的`AppDelegate`將呼叫。 此時，應用程式應該通知使用者正在啟動接續。

`NSUserActivity`執行個體時傳遞`AppDelegate`的`ContinueUserActivity`呼叫方法。 此時，您應該設定應用程式的使用者介面，並繼續指定的活動。

請參閱[實作遞移式](#implementing-handoff)如需詳細資訊，如下一節。

## <a name="enabling-handoff-in-a-xamarin-app"></a>啟用 Xamarin 應用程式遞交

遞移式所加諸的安全性需求，因為使用遞移式架構的 Xamarin.iOS 應用程式必須正確設定這兩個 Apple 開發人員入口網站中，與 Xamarin.iOS 專案檔中。

請執行下列動作：

1. 登入[Apple 開發人員入口網站](https://developer.apple.com)。
2. 按一下 **憑證、 識別碼與設定檔**。
3. 如果您尚未這麼做，按一下**識別碼**並建立您的應用程式的識別碼 (例如`com.company.appname`)，否則請編輯您現有的識別碼。
4. 請確認**iCloud**已核取指定之識別碼的服務：

    [![](handoff-images/provision01.png "啟用指定之識別碼的 iCloud 服務")](handoff-images/provision01.png#lightbox)
5. 儲存您的變更。
4. 按一下 **佈建設定檔** > **開發**和應用程式建立新的開發，為您佈建設定檔：

    [![](handoff-images/provision02.png "建立新的開發，佈建應用程式設定檔")](handoff-images/provision02.png#lightbox)
5. 下載並安裝新的佈建設定檔，或使用 Xcode 來下載並安裝設定檔。
6. 編輯您的 Xamarin.iOS 專案選項，並確定您使用您剛才建立的佈建設定檔：

    [![](handoff-images/provision03.png "選取剛才建立的佈建設定檔")](handoff-images/provision03.png#lightbox)
7. 接著，編輯您**Info.plist**檔案，並確定您使用的用來建立佈建設定檔的應用程式識別碼：

    [![](handoff-images/provision04.png "設定應用程式識別碼")](handoff-images/provision04.png#lightbox)
8. 若要捲動**背景模式**區段，並檢查下列項目：

    [![](handoff-images/provision05.png "啟用所需的背景模式")](handoff-images/provision05.png#lightbox)
9. 儲存所有檔案的變更。

使用這些設定之後，應用程式現在已準備好存取遞移式 Framework Api。 如需佈建的詳細資訊，請參閱我們[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)並[佈建您的應用程式](~/ios/get-started/installation/device-provisioning/index.md)輔助線。

## <a name="implementing-handoff"></a>實作遞交

使用者活動可以繼續在應用程式，以同一位開發人員小組識別碼來簽署，並支援相同的活動型別之間進行。 在 Xamarin.iOS 應用程式中實作遞移式必須要建立的使用者活動物件 (在`UIKit`或`AppKit`)、 更新物件的狀態來追蹤活動，並在接收端的裝置上繼續活動。

### <a name="identifying-user-activities"></a>用來識別使用者活動

實作遞移式的第一個步驟是識別應用程式支援，使用者活動的類型，並看到其中一個這些活動會接續另一個裝置上的良好候選項目。 例如： 待辦事項應用程式可能支援編輯的項目，做為其中一個_使用者的活動類型_，並支援瀏覽和另一個可用的項目清單。

應用程式可以建立多個使用者活動型別是必要的因為另一個用於任何應用程式提供的函式。 每個使用者的活動類型，應用程式必須追蹤類型的活動開始和結束，而且需要維護另一部裝置上繼續該工作的最新的狀態資訊。

使用者活動可以繼續使用相同的小組識別碼，而不需要任何在傳送和接收的應用程式之間的一對一對應簽署任何應用程式上進行。 例如，指定應用程式可以建立四種不同類型所使用的另一個裝置上的不同，請個別應用程式的活動。 這是經常會發生狀況之間的應用程式 （可能有許多功能和函式） 的 Mac 版本及 iOS 應用程式，其中每個應用程式是較小，並著重於特定的工作。

### <a name="creating-activity-type-identifiers"></a>建立活動型別識別項

_活動型別識別項_短字串加入至`NSUserActivityTypes`應用程式的各種**Info.plist**檔案用來唯一識別指定的使用者活動型別。 在每個應用程式支援的活動陣列中會有一個項目。 Apple 建議使用的活動型別識別項的反向 DNS 樣式標記法來避免發生衝突。 例如：`com.company-name.appname.activity`以特定的應用程式為基礎的活動或`com.company-name.activity`可以跨多個應用程式執行的活動。

建立時，會使用活動的型別識別項`NSUserActivity`識別的活動類型的執行個體。 當另一個裝置上接續活動時，活動類型 （以及應用程式的小組識別碼） 會決定繼續活動啟動哪一個應用程式。

例如，我們要建立範例應用程式呼叫**MonkeyBrowser** ([這裡下載](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/))。 此應用程式將會顯示四個索引標籤，各有不同的 URL 開啟網頁瀏覽器檢視中。 使用者將能夠繼續執行應用程式在不同的 iOS 裝置上的任何索引標籤。

若要建立必要的活動型別識別項，以支援此行為，請編輯**Info.plist**檔案，並切換至**來源**檢視。 新增`NSUserActivityTypes`索引鍵，並建立下列識別碼：

[![](handoff-images/type01.png "Plist 編輯器中所需的識別項與 NSUserActivityTypes 金鑰")](handoff-images/type01.png#lightbox)

我們會建立四個新的活動型別識別項，各供一個範例中的索引標籤**MonkeyBrowser**應用程式。 當建立您自己的應用程式，來取代原始的內容`NSUserActivityTypes`陣列與特定活動的活動 」 型別識別項。 您的應用程式支援。

### <a name="tracking-user-activity-changes"></a>追蹤使用者活動變更

當我們建立的新執行個體`NSUserActivity`類別中，我們會指定`NSUserActivityDelegate`追蹤活動的狀態變更的執行個體。 例如，下列程式碼可用來追蹤狀態變更：

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

`UserActivityReceivedData`接續 Stream 收到資料傳送的裝置時，會呼叫方法。 如需詳細資訊，請參閱 <<c0> [ 支援接續資料流](#supporting-continuation-streams)下一節。

`UserActivityWasContinued`另一部裝置已接管將活動從目前的裝置時，會呼叫方法。 根據類型的活動，例如將新的項目加入至 ToDo 清單中，應用程式可能需要中止傳送的裝置上的活動。

`UserActivityWillSave`之前活動的任何變更會儲存及同步處理在本機使用的裝置上呼叫方法。 您可以使用這個方法，讓任何過去一分鐘變更`UserInfo`屬性`NSUserActivity`執行個體，然後才傳送出去。

### <a name="creating-a-nsuseractivity-instance"></a>建立 NSUserActivity 執行個體

您的應用程式想要提供可能會繼續在其他裝置上的每個活動都必須封裝在`NSUserActivity`執行個體。 應用程式可以建立所需的任意多個活動，以及這些活動的性質是有問題的應用程式功能與功能而定。 例如，電子郵件應用程式可能會建立一個活動，以便建立新的訊息，另一個用於讀取訊息。

我們的範例應用程式，新`NSUserActivity`會建立每次使用者在其中一個索引標籤式的網頁瀏覽器檢視中輸入新的 URL。 下列程式碼會儲存在指定的索引標籤的狀態：

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

它會建立新`NSUserActivity`使用其中一個使用者的活動類型上面所建立，並且提供活動的人類看得懂的標題。 它會將附加至執行個體的`NSUserActivityDelegate`上面建立監看的狀態變更，並通知 iOS 這個使用者活動是目前的活動。

### <a name="populating-the-userinfo-dictionary"></a>填入 UserInfo 字典

如我們所見，上述`UserInfo`的屬性`NSUserActivity`類別是`NSDictionary`索引鍵-值組，用來定義指定活動的狀態。 中儲存的值`UserInfo`必須是下列一種類型： `NSArray`， `NSData`， `NSDate`， `NSDictionary`， `NSNull`， `NSNumber`， `NSSet`， `NSString`，或`NSURL`。 `NSURL` iCloud 文件所指向的資料值會自動調整，使其指向在接收端的裝置上相同的文件。

在上述範例中，我們建立`NSMutableDictionary`物件，並填入單一索引鍵提供使用者目前已在指定的索引標籤檢視的 URL。`AddUserInfoEntries`使用者活動的方法用來將用來還原活動接收的裝置上的資料更新的活動：

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple 建議保持傳送至匱乏的最低限度，以確保活動會傳送到接收端裝置及時的資訊。 如果較大的資訊是必要的例如編輯映像附加至文件都需要傳送，您應該使用接續的資料流。 請參閱[支援接續資料流](#supporting-continuation-streams)節以取得詳細資料。

### <a name="continuing-an-activity"></a>繼續執行活動

本機的 iOS 和 OS X 裝置的實際接近來源裝置中，並登入相同的 iCloud 帳戶中的具持續性的使用者活動的可用性，自動將告知遞交。 如果使用者選擇新的裝置上繼續活動時，系統將會啟動適當的應用程式 （根據活動類型與小組識別碼） 和資訊其`AppDelegate`接續需要進行。

首先，`WillContinueUserActivityWithType`方法呼叫，所以應用程式可以通知使用者，接續即將開始。 我們使用中的下列程式碼**AppDelegate.cs**我們的範例應用程式，以處理接續啟動檔案：

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

在上述範例中，每個檢視控制器會向`AppDelegate`且具有公用`PreparingToHandoff`方法，以顯示活動的指標，並讓使用者知道可以遞交給目前的裝置活動的訊息。 範例：

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

`ContinueUserActivity`的`AppDelegate`將呼叫實際繼續指定的活動。 同樣地，從我們的範例應用程式：

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

公用`PerformHandoff`的每個檢視控制器方法實際執行遞移式，並還原目前的裝置上的活動。 在範例中，它會在使用者已瀏覽不同的裝置的指定索引標籤中顯示相同的 URL。 範例：

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

`ContinueUserActivity`方法包含`UIApplicationRestorationHandler`活動繼續執行，您可以呼叫文件或回應的基礎。 您需要傳遞`NSArray`或還原處理常式呼叫時可還原的物件。 例如: 

```csharp
completionHandler (new NSObject[]{Tab4});
```

每個物件傳遞，其`RestoreUserActivityState`會呼叫方法。 每個物件可以接著使用中的資料`UserInfo`字典來還原它自己的狀態。 例如: 

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

文件的應用程式，如果您不會實作`ContinueUserActivity`方法，或者它會傳回`false`，`UIKit`或`AppKit`可以自動繼續活動。 請參閱[文件為基礎的應用程式中支援的遞交](#supporting-handoff-in-document-based-apps)如需詳細資訊，如下一節。

### <a name="failing-handoff-gracefully"></a>美麗的遞交

由於遞移式依賴之間鬆散連接的集合 iOS 和 OS X 裝置的資訊的傳輸，傳輸程序有時可能會失敗。 您應該設計您的應用程式正常處理這些失敗，並通知使用者發生任何情況下。

發生問題時，`DidFailToContinueUserActivitiy`方法的`AppDelegate`將呼叫。 例如: 

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

您應該使用提供`NSError`資訊提供給使用者失敗有關。

## <a name="native-app-to-web-browser-handoff"></a>Web 瀏覽器遞移式原生應用程式

使用者可能想要繼續而不需要適當的原生應用程式，以安裝在所需的裝置上的活動。 在某些情況下，具有 web 式介面可能會提供必要的功能，並仍可以繼續活動。 比方說，使用者的電子郵件帳戶可能會提供 web 型 UI 來撰寫和讀取訊息。

如果原始的原生應用程式知道 URL 的 web 介面 （以及用來識別正在繼續指定的項目所需的語法），它可以編碼資訊儲存在這個`WebpageURL`屬性`NSUserActivity`執行個體。 如果接收端裝置沒有合適的原生應用程式，以安裝處理接續，您就可以呼叫提供的 web 介面。

## <a name="web-browser-to-native-app-handoff"></a>原生應用程式遞交的網頁瀏覽器

如果使用者所使用的 web 型介面，在 來源裝置上，並在接收端的裝置上的原生應用程式宣告的網域部分`WebpageURL`屬性，則系統會使用該應用程式的控制代碼的接續。 新的裝置都會收到`NSUserActivity`標示為活動類型的執行個體`BrowsingWeb`和`WebpageURL`會包含的 URL，使用者已瀏覽，`UserInfo`字典將會是空白。

為了參與這種類型的遞移式應用程式，它必須宣告中的網域`com.apple.developer.associated-domains`格式的權利`<service>:<fully qualified domain name>`(例如： `activity continuation:company.com`)。

如果指定的網域符合`WebpageURL`屬性的值，遞移式會從該網域上的網站下載核准的應用程式識別碼的清單。 網站必須提供一份名為帶正負號的 JSON 檔案中的核准識別碼**apple 應用程式-站台關聯**(比方說， `https://company.com/apple-app-site-association`)。

此 JSON 檔案包含指定表單中的應用程式識別碼的清單字典`<team identifier>.<bundle identifier>`。 例如: 

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

要簽署的 JSON 檔案 (使其具有正確`Content-Type`的`application/pkcs7-mime`)，使用**終端機**應用程式和`openssl`憑證與金鑰 iOS 所信任之憑證授權單位所發出的命令 (請參閱[https://support.apple.com/kb/ht5012 ](https://support.apple.com/kb/ht5012)清單)。 例如: 

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

`openssl`命令會輸出您放在自己的網站的已簽署的 JSON 檔案**apple 應用程式-站台關聯**URL。 例如: 

```csharp
https://example.com/apple-app-site-association.
```

應用程式將會收到任何活動其`WebpageURL`的網域處於其`com.apple.developer.associated-domains`權利。 只有`http`和`https`通訊協定支援，其他通訊協定將會引發例外狀況。

## <a name="supporting-handoff-in-document-based-apps"></a>在文件為基礎的應用程式中支援遞交

如上所述，在 iOS 和 OS X 上的文件為基礎的應用程式將會自動支援遞交 iCloud 為基礎的文件如果應用程式的**Info.plist**檔案包含`CFBundleDocumentTypes`索引鍵`NSUbiquitousDocumentUserActivityType`。 例如: 

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

在此範例中的字串會是反向 DNS 應用程式的指示項的附加活動名稱。 如果輸入如此一來，活動型別項目不需要在重複`NSUserActivityTypes`一連串**Info.plist**檔案。

自動建立使用者活動物件 (可透過文件的`UserActivity`屬性) 可以用來還原上接續的狀態與應用程式中的其他物件所參考。 例如，若要追蹤項目選取項目和文件位置。 您需要設定此活動`NeedsSave`屬性，以`true`的狀態變更和更新的每當`UserInfo`字典中的`UpdateUserActivityState`方法。

`UserActivity`屬性可從任何執行緒，且符合的索引鍵-值觀察 (KVO) 通訊協定，因此它可以用來讓文件保持同步，因為它就會移入和登出的 iCloud。 `UserActivity`文件關閉時，屬性將會失效。

如需詳細資訊，請參閱 Apple[文件為基礎的應用程式中的使用者活動支援](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5)文件。

## <a name="supporting-handoff-in-responders"></a>在 回應中支援遞交

您可以建立回應的關聯 (繼承自`UIResponder`在 iOS 上或`NSResponder`OS X 上) 設定的活動其`UserActivity`屬性。 系統會自動儲存`UserActivity`屬性，在適當的時間，呼叫的回應`UpdateUserActivityState`方法將目前的資料加入至使用者的活動物件使用`AddUserInfoEntriesFromDictionary`方法。

## <a name="supporting-continuation-streams"></a>支援的接續資料流

某些狀況下，繼續活動所需的資訊數量無法進行有效率地傳送初始遞移式承載可能。 在這些情況下，接收應用程式可以建立一或多個本身與原始的應用程式傳輸資料之間的資料流。

原始的應用程式將會設定`SupportsContinuationStreams`的屬性`NSUserActivity`執行個體`true`。 例如: 

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

接收的應用程式接著可以呼叫`GetContinuationStreams`方法`NSUserActivity`在其`AppDelegate`來建立資料流。 例如: 

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

在 來源裝置上使用者的活動委派會接收資料流藉由呼叫其`DidReceiveInputStream`方法，以便提供的資料要求繼續恢復裝置的使用者活動。

您將使用`NSInputStream`提供唯讀存取資料流資料，以及`NSOutputStream`提供唯寫存取。 資料流應用於要求和回應的方式，其中接收應用程式要求更多資料，而原始的應用程式提供它。 以便繼續在裝置上，輸入資料流讀取資料寫入至輸出資料流來源裝置上，反之亦然。

即使在其中接續 Stream 所需的情況下，應該有最小的上一步，並詳述在兩個應用程式之間的通訊。

如需詳細資訊，請參閱 Apple[使用接續的資料流](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13)文件。

## <a name="handoff-best-practices"></a>遞移式最佳作法

無縫之接續的使用者活動透過遞移式成功實作必須謹慎設計，因為所有各種相關的元件。 Apple 建議採用遞移式啟用應用程式的下列最佳作法：

- 設計使用者活動，需要最小的承載可能要關聯到繼續執行活動的狀態。 愈大裝載，它會啟動接續。
- 如果您必須將大量資料成功接續，考慮成本參與組態和網路負擔。
- 它是適用於大型的 Mac 應用程式建立都由幾個選項，較小，在 iOS 裝置上的特定工作的應用程式的使用者活動。 不同的應用程式和 OS 版本應該設計為適合共同運作，或依正常程序失敗。
- 指定您的活動類型時，請使用反向 DNS 標記法來避免發生衝突。 型別定義中，如果活動是針對指定的應用程式，會包含其名稱 (例如`com.myCompany.myEditor.editing`)。 如果活動可以使用多個應用程式，從卸除的應用程式名稱的定義 (例如`com.myCompany.editing`)。
- 如果您的應用程式需要更新的使用者活動的狀態 (`NSUserActivity`) 設定`NeedsSave`屬性設`true`。 在適當的時間，遞移式會呼叫的委派`UserActivityWillSave`方法，讓您可以更新`UserInfo`所需的字典。
- 因為遞移式程序接收的裝置上可能未立即初始化，您應該實作`AppDelegate`的`WillContinueUserActivity`，並通知即將啟動接續的使用者。

## <a name="example-handoff-app"></a>範例遞移式應用程式

為 Xamarin.iOS 應用程式中使用遞移式的範例，我們都納入[ **MonkeyBrowser** ](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)本指南使用範例應用程式。 應用程式有四個索引標籤，使用者可以使用瀏覽網站，各有其指定的活動類型：天氣、 我的最愛、 休息時間和工作。

任何索引標籤上，當使用者輸入新的 URL 和點選**移**按鈕，新`NSUserActivity`建立該索引標籤，其中包含使用者目前瀏覽的 URL:

[![](handoff-images/handoff01.png "範例遞移式應用程式")](handoff-images/handoff01.png#lightbox)

如果另一個使用者的裝置有**MonkeyBrowser**應用程式安裝，請登入 iCloud 使用相同的使用者帳戶、 位於相同網路，然後在接近上述的裝置，遞移式活動將會顯示在首頁畫面 （在較低的左下角）：

[![](handoff-images/handoff02.png "在左下角的 [首頁] 螢幕上顯示的遞移式活動")](handoff-images/handoff02.png#lightbox)

如果使用者向上拖曳遞交圖示，將會啟動應用程式和使用者活動中指定`NSUserActivity`會繼續在新的裝置上：

[![](handoff-images/handoff03.png "繼續在新的裝置上的使用者活動")](handoff-images/handoff03.png#lightbox)

當使用者活動已成功傳送至其他 Apple 裝置傳送裝置的`NSUserActivity`會呼叫`UserActivityWasContinued`方法在其`NSUserActivityDelegate`讓它知道使用者活動已成功轉移給另一個裝置。

## <a name="summary"></a>總結

本文提供用來繼續使用者活動之間的使用者的 Apple 裝置的多個遞移式架構的簡介。 接下來，它會示範如何啟用和 Xamarin.iOS 應用程式中實作遞移式。 最後，它還會討論不同類型的可遞移式接續和遞移式最佳作法。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [MonkeyBrowser 範例](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [新功能 iOS 9.0 以上版本](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 使用者介面指導方針](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit 架構參考](https://developer.apple.com/library/ios/home_kit_framework_ref)
