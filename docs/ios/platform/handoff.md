---
title: Xamarin.iOS 中遞交
description: 本文章涵蓋在要傳送的 Xamarin.iOS 應用程式中使用的遞移式使用者上所執行的應用程式之間的使用者活動的其他裝置。
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ec324e8fb8327b622424311b89567608311a6a19
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787516"
---
# <a name="handoff-in-xamarinios"></a>Xamarin.iOS 中遞交

_本文章涵蓋在要傳送的 Xamarin.iOS 應用程式中使用的遞移式使用者上所執行的應用程式之間的使用者活動的其他裝置。_

Apple 在 iOS 8 和 OS X Yosemite (10.10) 提供通用的機制，使用者要傳送活動啟動他們的裝置，其中引進遞交到執行相同的應用程式或支援相同活動的另一個應用程式的另一個裝置。

[![](handoff-images/handoff02.png "執行遞移式運算的一個範例")](handoff-images/handoff02.png#lightbox)

這篇文章會採用快速查看啟用共用 Xamarin.iOS 應用程式中的活動，並涵蓋遞移式中的架構詳細資料：

## <a name="about-handoff"></a>關於遞交

遞交 （也稱為持續性） 是由引入，Apple 在 iOS 8 和 OS X Yosemite (10.10)，讓使用者針對他們的裝置 （iOS 或 Mac） 的其中一個開始活動，並繼續在其裝置 （如使用者 iClou 所識別的另一台相同的活動d 帳戶)。

遞交已展開在 iOS 9，也支援新的、 增強的搜尋功能。 如需詳細資訊，請參閱我們[搜尋的增強功能](~/ios/platform/search/index.md)文件。

例如，使用者可以啟動其 iPhone 上的電子郵件，並順暢地繼續其在 Mac 上，所有相同訊息的資訊填入和它們在 iOS 中所保留的相同位置中的資料指標的電子郵件。

您共用相同的應用程式的任何_小組 ID_ （適用於 Mac，企業會有資格使用遞交，以跨應用程式繼續執行使用者活動，只要這些應用程式都是透過 iTunes App Store 中傳遞的或已註冊的開發人員簽署或臨機操作的應用程式）。

任何`NSDocument`或`UIDocument`的基礎應用程式自動擁有遞交支援內建，而且需要最少的變更，以便支援遞交。

### <a name="continuing-user-activities"></a>繼續使用者活動

`NSUserActivity`類別 (以及某些些微變更，才能`UIKit`和`AppKit`) 提供支援，可能可以繼續進行使用者的活動定義的其他使用者的裝置。

要傳遞給其他使用者的裝置的活動，它必須封裝在執行個體`NSUserActivity`、 標示為_目前活動_，將它的內容設定 （用來執行接續的資料） 和活動必須再傳送到該裝置。

遞交傳遞最低限度的定義來繼續進行，以較大的資料封包透過 iCloud 正在同步處理活動的資訊。

在接收裝置上，使用者會收到通知提供接續的活動。 如果使用者選擇繼續該活動執行新的裝置上，指定的應用程式啟動 （如果尚未執行），從裝載`NSUserActivity`用來重新啟動活動。

[![](handoff-images/handoffinteractions.png "繼續使用者活動的概觀")](handoff-images/handoffinteractions.png#lightbox)

只有應用程式可共用相同的開發人員小組 ID 及回應特定_活動型別_適合接續。 應用程式定義活動支援的類型，它在`NSUserActivityTypes`索引鍵及其**Info.plist**檔案。 根據這點，持續的裝置選擇要執行接續小組 ID，活動型別為基礎的應用程式並選擇性地_活動標題_。

接收應用程式會使用來自資訊`NSUserActivity`的`UserInfo`設定其使用者介面，並還原指定的活動的狀態，使轉換不會顯示給使用者的無縫式的字典。

如果接續需要詳細資訊，而非可以有效率地透過傳送`NSUserActivity`，繼續應用程式可以傳送呼叫給原始的應用程式，並建立一或多個資料流傳輸所需的資料。 例如，如果活動已編輯多個映像的大型文字文件，串流處理被需要傳送接收裝置上繼續活動所需的資訊。 如需詳細資訊，請參閱[支援接續的資料流](#Supporting-Continuation-Streams)下一節。

如上所述，`NSDocument`或`UIDocument`的基礎應用程式自動擁有支援內建遞交。 如需詳細資訊，請參閱[支援遞移式文件為基礎的應用程式中](#Supporting-Handoff-in-Document-Based-Apps)下一節。

### <a name="the-nsuseractivity-class"></a>NSUserActivity 類別

`NSUserActivity`類別是遞移式交換中的主要物件，而且用來封裝適用於接續的使用者活動的狀態。 應用程式會具現化一份`NSUserActivity`任何活動，它支援，而且想要繼續在其他裝置。 例如，文件編輯器會建立每個文件的活動目前開啟。 不過，只有最上層文件 （最上層視窗或索引標籤中顯示） 是_目前活動_且因此供接續。

執行個體`NSUserActivity`兩者都由其`ActivityType`和`Title`屬性。 `UserInfo`字典屬性用來執行活動的狀態資訊。 設定`NeedsSave`屬性`true`如果您想為延遲載入的狀態資訊透過`NSUserActivity`的委派。 使用`AddUserInfoEntries`方法，將合併到其他用戶端的新資料`UserInfo`字典所需保留活動的狀態。

### <a name="the-nsuseractivitydelegate-class"></a>NSUserActivityDelegate 類別

`NSUserActivityDelegate`用來將資訊保存在`NSUserActivity`的`UserInfo`字典最新狀態並與活動的目前狀態同步。 當系統需要更新活動中的資訊 (例如另一個裝置上的接續之前)，它會呼叫`UserActivityWillSave`委派方法。

您必須實作`UserActivityWillSave`方法，並變更`NSUserActivity`(例如`UserInfo`，`Title`等等) 以確保它仍然會反映目前活動的狀態。 當系統呼叫`UserActivityWillSave`方法，`NeedsSave`旗標將會被清除。 如果您修改任何活動的資料屬性，您必須設定`NeedsSave`至`true`一次。

而不是使用`UserActivityWillSave`以上所顯示的方法，您可以選擇性地`UIKit`或`AppKit`自動管理使用者活動。 若要這樣做，請將設定回應物件的`UserActivity`屬性和實作`UpdateUserActivityState`方法。 請參閱[支援遞交，以回應](#Supporting-Handoff-in-Responders)下面章節，如需詳細資訊。

### <a name="app-framework-support"></a>應用程式架構支援

同時`UIKit`(iOS) 和`AppKit`(OS X) 提供內建的支援，以在遞交`NSDocument`，回應者 (`UIResponder`/`NSResponder`)，和`AppDelegate`類別。 雖然每個作業系統會實作遞移式而有稍微不同，基本的機制和 Api 都相同。

#### <a name="user-activities-in-document-based-apps"></a>在文件為基礎的應用程式中的使用者活動

文件為基礎的 iOS 和 OS X 應用程式自動擁有內建的遞移式支援。 若要啟用這項支援，您必須新增`NSUbiquitousDocumentUserActivityType`索引鍵和值，每個`CFBundleDocumentTypes`的應用程式中的項目**Info.plist**檔案。

如果此機碼存在，兩者`NSDocument`和`UIDocument`自動建立`NSUserActivity`icloud 的功能為基礎的文件的指定之類型的執行個體。 您必須提供應用程式支援的文件的每個類型的活動型別，可以使用相同的活動類型的多個文件類型。 同時`NSDocument`和`UIDocument`自動填入`UserInfo`屬性`NSUserActivity`與他們`FileURL`屬性的值。

OS X 上`NSUserActivity`受`AppKit`自動與回應相關聯，主視窗的文件視窗時，會變成目前的活動。 在 iOS 上，針對`NSUserActivity`物件管理`UIKit`，必須呼叫`BecomeCurrent`方法明確或具有文件的`UserActivity`屬性上設定`UIViewController`應用程式移至前景。

`AppKit` 會自動還原任何`UserActivity`以這種方式在 OS X 上建立屬性。會發生這個錯誤`ContinueUserActivity`方法會傳回`false`或者它是未實作。 在此情況下，以開啟文件`OpenDocument`方法`NSDocumentController`，將會接到`RestoreUserActivityState`方法呼叫。

請參閱[支援遞移式文件為基礎的應用程式中](#Supporting-Handoff-in-Document-Based-Apps)下面章節，如需詳細資訊。

#### <a name="user-activities-and-responders"></a>使用者活動和回應

同時`UIKit`和`AppKit`自動管理使用者活動，如果您將它設定為回應物件`UserActivity`屬性。 如果已修改狀態，您必須設定`NeedsSave`回應程式的屬性`UserActivity`至`true`。 系統會自動儲存`UserActivity`必要時，呼叫來更新狀態的回應時間，提供給其`UpdateUserActivityState`方法。

如果多個回應共用單一`NSUserActivity`接收執行個體，`UpdateUserActivityState`回呼時系統會更新使用者活動物件。 回應程式需要呼叫`AddUserInfoEntries`方法，以更新`NSUserActivity`的`UserInfo`此時反映目前的活動狀態的字典。 `UserInfo`字典會在每個清除`UpdateUserActivityState`呼叫。

若要解除本身的活動，回應者可以設定其`UserActivity`屬性`null`。 當受管理應用程式架構`NSUserActivity`執行個體沒有更多相關聯的回應或文件，它會自動失效。

請參閱[支援遞交，以回應](#Supporting-Handoff-in-Responders)下面章節，如需詳細資訊。

#### <a name="user-activities-and-the-appdelegate"></a>使用者活動和 AppDelegate

您的應用程式`AppDelegate`處理遞交接續時，是它的主要進入點。 當使用者對回應傳遞通知，適當的應用程式啟動 （如果不是已在執行中） 和`WillContinueUserActivityWithType`方法`AppDelegate`將呼叫。 此時，應用程式應該通知使用者正在啟動的接續。

`NSUserActivity`時傳遞執行個體`AppDelegate`的`ContinueUserActivity`方法呼叫。 此時，您應該設定應用程式的使用者介面，並繼續指定的活動。

請參閱[實作遞交](#Implementing-Handoff)下面章節，如需詳細資訊。

## <a name="enabling-handoff-in-a-xamarin-app"></a>啟用 Xamarin 應用程式中遞交

遞交所加諸的安全性需求，因為使用遞交 framework Xamarin.iOS 應用程式必須正確設定在這兩個 Apple 開發人員入口網站和 Xamarin.iOS 專案檔中。

請執行下列動作：

1. 登入[Apple 開發人員入口網站](http://developer.apple.com)。
2. 按一下**憑證、 識別項與設定檔**。
3. 如果您尚未這樣做，請按一下 上**識別碼**並建立您的應用程式的識別碼 (例如`com.company.appname`)，否則請編輯您現有的識別碼。
4. 請確認**iCloud**已核取指定之識別碼的服務： 

    [![](handoff-images/provision01.png "啟用指定之識別碼的 iCloud 服務")](handoff-images/provision01.png#lightbox)
5. 儲存您的變更。
4. 按一下**佈建的設定檔** > **開發**並建立新的開發工作，讓您佈建設定檔的應用程式： 

    [![](handoff-images/provision02.png "建立新的開發佈建設定檔的應用程式")](handoff-images/provision02.png#lightbox)
5. 請下載並安裝新的佈建設定檔或使用 Xcode 下載及安裝設定檔。
6. 編輯您 Xamarin.iOS 專案選項，並確保您使用您剛才建立的佈建設定檔： 

    [![](handoff-images/provision03.png "選取剛才建立的佈建設定檔")](handoff-images/provision03.png#lightbox)
7. 接著，編輯您**Info.plist**檔案，並確定您使用的用來建立佈建設定檔的應用程式識別碼： 

    [![](handoff-images/provision04.png "設定應用程式識別碼")](handoff-images/provision04.png#lightbox)
8. 捲動到**背景模式**區段，並檢查下列項目： 

    [![](handoff-images/provision05.png "啟用所需的背景模式")](handoff-images/provision05.png#lightbox)
9. 儲存所有檔案的變更。

這些設定，應用程式現在已準備好存取遞移式架構應用程式開發介面。 如需佈建的詳細資訊，請參閱我們[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)和[佈建您的應用程式](~/ios/get-started/installation/device-provisioning/index.md)輔助線。

## <a name="implementing-handoff"></a>實作遞交

使用者的活動可以繼續在相同的開發人員小組 ID 進行簽署，且支援相同的活動類型的應用程式之間。 Xamarin.iOS 應用程式中實作遞交需要您建立使用者活動物件 (在`UIKit`或`AppKit`)，更新物件的狀態來追蹤活動，並接收裝置上繼續活動。

### <a name="identifying-user-activities"></a>用來識別使用者的活動

實作遞移式的第一個步驟是識別應用程式支援的使用者活動的類型，查看其中一個這些活動都適合進行其他裝置上的接續。 例如： 待辦事項應用程式可能支援編輯的項目，做為其中_使用者活動型別_，並支援瀏覽與另一個可用的項目清單。

應用程式可以建立一樣多使用者活動類型是必要的其中一個應用程式提供的函式。 每個使用者的活動類型，應用程式必須追蹤時之型別的活動開始和結束，且必須維護另一個裝置上繼續該工作的最新狀態資訊。

使用者的活動可以繼續使用相同的小組 ID 沒有任何在傳送和接收的應用程式之間的一對一對應簽署任何應用程式上。 例如，給定的應用程式可以建立四種不同的活動，所使用的另一個裝置上的不同，則個別應用程式。 這是常見的應用程式 （可能有許多功能和函式） 的 Mac 版本和 iOS 應用程式之間其中每個應用程式是較小，並著重於特定的工作。

### <a name="creating-activity-type-identifiers"></a>建立活動型別識別項

_活動型別識別項_短字串加入至`NSUserActivityTypes`陣列的應用程式的**Info.plist**檔案用來唯一識別指定的使用者活動型別。 應用程式支援的每個活動在陣列中會有一個項目。 Apple 建議使用的活動類型識別項的反向 dns 標記法，避免發生衝突。 例如：`com.company-name.appname.activity`以特定的應用程式為基礎的活動或`com.company-name.activity`跨多個應用程式可以執行的活動。

活動類型識別項建立時會使用`NSUserActivity`識別的活動類型的執行個體。 活動會繼續其他裝置上，當活動類型 （以及應用程式的小組識別碼） 會決定繼續活動啟動哪些應用程式。

例如，我們將建立範例應用程式呼叫**MonkeyBrowser** ([此處下載](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/))。 此應用程式將會顯示四個索引標籤，各有不同的 URL 開啟 web 瀏覽器檢視中。 使用者可以繼續執行應用程式在不同的 iOS 裝置上的任何索引標籤。

若要建立必要的活動型別識別碼，以支援這個行為，請編輯**Info.plist**檔案，並切換至**來源**檢視。 新增`NSUserActivityTypes`鍵，然後建立下列識別碼：

[![](handoff-images/type01.png "NSUserActivityTypes 索引鍵和 plist 編輯器中所需的識別項")](handoff-images/type01.png#lightbox)

我們建立四個新活動類型識別項，各供一個範例中的索引標籤**MonkeyBrowser**應用程式。 當建立您自己的應用程式，來取代原始的內容`NSUserActivityTypes`陣列活動類型識別碼的活動特定應用程式支援。

### <a name="tracking-user-activity-changes"></a>追蹤使用者活動的變更

當我們建立的新執行個體`NSUserActivity`類別中，我們將在此指定`NSUserActivityDelegate`來追蹤活動的狀態變更的執行個體。 例如，下列程式碼可以用於追蹤狀態變更：

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

`UserActivityReceivedData`接續的資料流傳送的裝置從收到資料時，呼叫方法。 如需詳細資訊，請參閱[支援接續的資料流](#Supporting-Continuation-Streams)下一節。

`UserActivityWasContinued`呼叫方法時，另一部裝置已花費目前裝置中的活動。 根據類型的活動，例如將新項目加入至 ToDo 清單中，應用程式可能需要中止傳送裝置上的活動。

`UserActivityWillSave`方法之前呼叫活動的任何變更會儲存並在可用的本機裝置上進行同步處理。 您可以使用這個方法來變更任何過去一分鐘`UserInfo`屬性`NSUserActivity`傳送之前，執行個體。

### <a name="creating-a-nsuseractivity-instance"></a>建立 NSUserActivity 執行個體

您的應用程式想要提供其他裝置上，可能會繼續執行每個活動都必須封裝在`NSUserActivity`執行個體。 應用程式可以建立所需的許多活動，而且這些活動性質相依的功能和有問題的應用程式的功能。 例如，電子郵件應用程式可能會建立一個活動，以便建立新的訊息，和另一個用於讀取訊息。

我們的範例應用程式的新`NSUserActivity`每次使用者在其中一個索引標籤式的網頁瀏覽器檢視中輸入新的 URL。 下列程式碼會儲存狀態的指定索引標籤：

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

它會建立新`NSUserActivity`使用其中一種使用者活動型別前面所建立，並提供活動人類看得懂的標題。 它會將附加至執行個體的`NSUserActivityDelegate`上方建立監看的狀態變更，並通知 iOS 這個使用者的活動是目前的活動。

### <a name="populating-the-userinfo-dictionary"></a>填入使用者資訊字典

如我們所見，上述`UserInfo`屬性`NSUserActivity`類別是`NSDictionary`索引鍵-值組，用來定義特定活動的狀態。 中所儲存值`UserInfo`必須是下列類型之一： `NSArray`， `NSData`， `NSDate`， `NSDictionary`， `NSNull`， `NSNumber`， `NSSet`， `NSString`，或`NSURL`。 `NSURL` 指向 iCloud 的文件的資料值會自動調整，使其指向相同的文件接收裝置上。

在上述範例中，我們建立`NSMutableDictionary`物件，並填入單一索引鍵，提供使用者目前已在指定的索引標籤檢視的 URL。`AddUserInfoEntries`使用者活動的方法用來更新活動的資料，將會用來還原接收裝置上的活動：

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple 建議傳送匱乏最小值，以確保活動會傳送到接收裝置及時將資訊保存。 如果較大的資訊為必要項，例如編輯映像附加至文件需要傳送，您應該使用接續的資料流。 請參閱[支援接續的資料流](#Supporting-Continuation-Streams)下面章節以取得詳細資料。

### <a name="continuing-an-activity"></a>繼續進行活動

本機 iOS 和 OS X 裝置處於實際接近原始裝置，然後登入相同的 iCloud 帳戶中的持續性的使用者活動的可用性，會自動通知遞交。 如果使用者選擇新的裝置上繼續活動時，系統將會啟動適當的應用程式 （根據活動型別與小組 ID） 和資訊其`AppDelegate`接續需要發生。

首先，`WillContinueUserActivityWithType`應用程式可以通知使用者即將開始接續，因此，呼叫方法。 我們使用下列程式碼中的**d**我們的範例應用程式，以處理接續啟動的檔案：

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

在上述範例中，每個檢視控制站會向註冊`AppDelegate`而且具有公用`PreparingToHandoff`顯示活動指示器和訊息，讓使用者知道交給目前的裝置活動的方法。 範例：

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

`ContinueUserActivity`的`AppDelegate`將被呼叫來實際繼續指定的活動。 同樣地，從我們的範例應用程式：

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

公用`PerformHandoff`方法的每個檢視控制器實際 preforms 遞移式並還原目前的裝置上的活動。 在此範例中，它會在使用者已瀏覽不同的裝置的指定索引標籤中顯示相同的 URL。 範例：

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

`ContinueUserActivity`方法包含`UIApplicationRestorationHandler`活動繼續您的文件或回應者可以呼叫基礎。 您必須傳遞`NSArray`或還原處理常式呼叫時可還原的物件。 例如: 

```csharp
completionHandler (new NSObject[]{Tab4});
```

每個物件傳遞，其`RestoreUserActivityState`會呼叫的方法。 每個物件可以再使用中的資料`UserInfo`字典，以便還原其本身的狀態。 例如: 

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

針對文件為基礎應用程式，如果您沒有實作`ContinueUserActivity`方法或其傳回`false`，`UIKit`或`AppKit`可以自動繼續活動。 請參閱[支援遞移式文件為基礎的應用程式中](#Supporting-Handoff-in-Document-Based-Apps)下面章節，如需詳細資訊。

### <a name="failing-handoff-gracefully"></a>依正常程序失敗遞交

由於遞交依賴鬆散連接集合 iOS 和 OS X 裝置之間資訊的傳輸，傳輸程序有時可能會失敗。 您應該設計您的應用程式正常地處理這些失敗，並通知使用者的任何發生的情況。

如果發生故障，`DidFailToContinueUserActivitiy`方法`AppDelegate`將呼叫。 例如: 

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

您應該使用提供`NSError`提供使用者失敗有關的資訊。

## <a name="native-app-to-web-browser-handoff"></a>Web 瀏覽器傳遞至原生應用程式

使用者可能想要繼續而不需要安裝所需的裝置上的適當原生應用程式的活動。 在某些情況下，web 型介面可能會提供必要的功能，仍然可以繼續活動。 例如，使用者的電子郵件帳戶可能會提供撰寫及讀取訊息 web 基底 UI。

如果原始、 原生應用程式知道 URL 的 web 介面 （以及必要的語法，來識別正在繼續執行給定的項目），它可以編碼資訊儲存在這個`WebpageURL`屬性`NSUserActivity`執行個體。 如果接收的裝置沒有合適的原生應用程式，以處理接續所安裝，就可以呼叫提供的 web 介面。

## <a name="web-browser-to-native-app-handoff"></a>原生應用程式遞交的網頁瀏覽器

如果使用者已在原始的裝置上使用的 web 型介面，並在接收裝置上的原生應用程式宣告的網域部分`WebpageURL`屬性，則系統會使用該應用程式的控制代碼的接續。 新的裝置將會收到`NSUserActivity`標記做為活動類型的執行個體`BrowsingWeb`和`WebpageURL`將包含使用者已瀏覽，URL`UserInfo`字典將會是空白。

參與這種類型的遞移式應用程式，它必須宣告中的網域`com.apple.developer.associated-domains`權利與格式`<service>:<fully qualified domain name>`(例如： `activity continuation:company.com`)。

如果指定的網域符合`WebpageURL`屬性的值，遞移式會從該網域上的網站下載核准的應用程式識別碼的清單。 網站必須提供一份名為帶正負號的 JSON 檔案中的核准識別碼**apple 應用程式的站台關聯**(例如， `https://company.com/apple-app-site-association`)。

此 JSON 檔案包含指定表單中的應用程式識別碼清單的字典`<team identifier>.<bundle identifier>`。 例如: 

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

若要簽署 JSON 檔案 (使其具有正確`Content-Type`的`application/pkcs7-mime`)，使用**終端機**應用程式和`openssl`使用憑證與金鑰 iOS 信任之憑證授權單位所發出的命令 (請參閱[http://support.apple.com/kb/ht5012 ](http://support.apple.com/kb/ht5012)清單)。 例如: 

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

`openssl`命令會輸出帶正負號的 JSON 檔案，您可以在網站上放置**apple 應用程式的站台關聯**URL。 例如: 

```csharp
https://example.com/apple-app-site-association.
```

應用程式將會收到任何活動其`WebpageURL`網域是在其`com.apple.developer.associated-domains`權利。 只有`http`和`https`通訊協定支援，任何其他通訊協定將會引發例外狀況。

## <a name="supporting-handoff-in-document-based-apps"></a>支援文件為基礎的應用程式中遞交

如上所述，在 iOS 和 OS X 上的文件為基礎的應用程式會自動支援 icloud 的功能為基礎的文件的遞移式如果應用程式的**Info.plist**檔案包含`CFBundleDocumentTypes`索引鍵`NSUbiquitousDocumentUserActivityType`。 例如: 

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

在此範例中的字串為附加的活動名稱與反向 DNS 應用程式指示項。 如果輸入如此一來，不需要中重複的活動型別項目`NSUserActivityTypes`陣列**Info.plist**檔案。

自動建立使用者活動物件 (可透過文件的`UserActivity`屬性) 可以用來還原接續的狀態與應用程式中的其他物件所參考。 例如，若要追蹤項目選取項目和文件位置。 您需要設定此活動`NeedsSave`屬性`true`時的狀態變更和更新`UserInfo`字典中的`UpdateUserActivityState`方法。

`UserActivity`屬性可從任何執行緒，而且符合索引鍵-值 observing (KVO) 通訊協定，因此它可以用來保留文件同步處理，因為移入和登出的 icloud 的功能。 `UserActivity`屬性在文件關閉時將會失效。

如需詳細資訊，請參閱 Apple[文件為基礎的應用程式中的使用者活動支援](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5)文件。

## <a name="supporting-handoff-in-responders"></a>支援在回應中遞交

您可以將回應產生關聯 (繼承自`UIResponder`在 iOS 上或`NSResponder`OS X 上) 設定的活動其`UserActivity`屬性。 系統會自動儲存`UserActivity`屬性在適當時間呼叫的回應`UpdateUserActivityState`方法，以將目前的資料加入至使用者活動物件使用`AddUserInfoEntriesFromDictionary`方法。

## <a name="supporting-continuation-streams"></a>支援接續的資料流

可能是其中繼續活動所需的資訊數量無法進行有效率地傳送初始遞交裝載的情況。 在這些情況下，接收應用程式可以建立其本身之間原始的應用程式傳輸資料的一或多個資料流。

原始的應用程式將會設定`SupportsContinuationStreams`屬性`NSUserActivity`執行個體`true`。 例如: 

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

接收應用程式可以接著呼叫`GetContinuationStreams`方法`NSUserActivity`中其`AppDelegate`來建立資料流。 例如: 

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

在原始裝置上，使用者的活動委派會接收串流藉由呼叫其`DidReceiveInputStream`方法，以便提供的資料要求繼續裝置上的使用者活動。

您將使用`NSInputStream`提供唯讀資料流資料存取和`NSOutputStream`提供唯寫存取。 資料流應該用於要求和回應的方式，其中接收應用程式要求更多資料，而原始的應用程式提供它。 使原始裝置上寫入輸出資料流的資料時持續在裝置上，輸入資料流讀取，反之亦然。

即使在接續的資料流需進行的情況下，應該有最小的後和等兩個應用程式之間的通訊。

如需詳細資訊，請參閱 Apple[使用接續的資料流](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13)文件。

## <a name="handoff-best-practices"></a>遞交最佳作法

順暢的使用者活動透過遞交接續成功實作需要謹慎設計，因為所有的各種元件。 Apple 建議採用遞交啟用應用程式的下列最佳作法：

- 設計要求可以繼續執行活動的狀態與相關聯的最小裝載您使用者的活動。 較大承載、 所需的時間花費啟動接續。
- 如果您必須傳送大量的資料成功的接續，考量成本參與組態和網路負擔。
- 通常會針對大型的 Mac 應用程式建立都由數個較小，iOS 裝置上的特定工作的應用程式的使用者活動。 不同的應用程式和作業系統版本，都應該設計成一起順利運作，或依正常程序失敗。
- 在指定活動類型，使用反向 DNS 標記法，避免發生衝突。 類型定義中，如果給定的應用程式的特定活動，會包含其名稱 (例如`com.myCompany.myEditor.editing`)。 如果活動可以使用多個應用程式，從卸除的應用程式名稱的定義 (例如`com.myCompany.editing`)。
- 如果您的應用程式需要更新的使用者活動的狀態 (`NSUserActivity`) 設定`NeedsSave`屬性`true`。 在適當的時間，遞移式會呼叫委派的`UserActivityWillSave`方法，使您可以更新`UserInfo`所需的字典。
- 因為遞移式程序可能會立即初始化接收裝置上，您應該實作`AppDelegate`的`WillContinueUserActivity`，並通知使用者即將啟動的接續。

## <a name="example-handoff-app"></a>範例遞移式應用程式

為使用遞交 Xamarin.iOS 應用程式中的範例，我們已包含[ **MonkeyBrowser** ](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)本指南使用範例應用程式。 應用程式有四個索引標籤，使用者可以使用瀏覽網頁時，每個都有指定的活動型別： 天氣、 我的最愛、 咖啡中斷和工作。

任何索引標籤上，當使用者輸入新的 URL 和點選**移**按鈕，新`NSUserActivity`建立該索引標籤，其中包含使用者目前瀏覽的 URL:

[![](handoff-images/handoff01.png "範例遞移式應用程式")](handoff-images/handoff01.png#lightbox)

如果其他使用者的裝置有**MonkeyBrowser**安裝，應用程式登入 icloud 的功能使用相同的使用者帳戶，是在同一個網路，而且非常接近上述的裝置，遞移式活動將會顯示在首頁（在較低的左下角） 畫面中：

[![](handoff-images/handoff02.png "在左下角的 [首頁] 螢幕上顯示的遞移式活動")](handoff-images/handoff02.png#lightbox)

如果使用者向上拖曳遞交圖示上，而且會啟動應用程式中指定的使用者活動`NSUserActivity`繼續在新的裝置上：

[![](handoff-images/handoff03.png "繼續新的裝置上的使用者活動")](handoff-images/handoff03.png#lightbox)

當使用者活動成功傳送到其他 Apple 裝置、 傳送裝置的`NSUserActivity`會接收呼叫`UserActivityWasContinued`方法上的其`NSUserActivityDelegate`，讓它知道使用者活動具有已成功地轉移到另一個裝置。

## <a name="summary"></a>總結

這篇文章已授與用來繼續使用者活動之間的使用者的 Apple 裝置的多個遞移式架構的簡介。 接下來，它會示範如何啟用及 Xamarin.iOS 應用程式中實作遞交。 最後，它將討論不同類型的可遞移式接續和遞移式最佳作法。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [MonkeyBrowser 範例](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [新功能 iOS 9.0 以上版本](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 使用者介面指導方針](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit Framework 參考](https://developer.apple.com/library/ios/home_kit_framework_ref)
