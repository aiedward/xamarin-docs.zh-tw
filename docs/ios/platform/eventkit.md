---
title: 在 Xamarin 中 EventKit
description: 本檔描述 EventKit，以及如何在 Xamarin 中使用它。 其中討論行事曆、行事曆事件和提醒、查看使用 EventKit 進行程式設計時常用的類別等等。
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: a797fc654c7bdbbdb621c9d18dc7f1a82676778b
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930230"
---
# <a name="eventkit-in-xamarinios"></a>在 Xamarin 中 EventKit

iOS 有兩個內建的行事曆相關應用程式：行事曆應用程式和提醒應用程式。 這很簡單，足以瞭解行事曆應用程式管理行事歷數據的方式，但提醒應用程式比較不明顯。 提醒實際上在到期時、何時完成等方面，都有與其相關聯的日期。因此，iOS 會將所有行事歷數據（不論是行事曆事件或提醒）儲存在一個位置，稱為行事*曆資料庫*。

EventKit 架構可讓您存取*行事曆資料庫*儲存的行事曆、行事*曆事件*和*提醒*資料。 從 iOS 4 開始就可以存取行事曆和行事曆事件，但在 iOS 6 中，提醒的存取是新的。

在本指南中，我們將討論：

- **EventKit 基本概念**–這會透過主要類別介紹 EventKit 的基本部分，並提供其使用方式的瞭解。 在處理檔的下一個部分之前，必須先閱讀這一節。 
- **一般**工作–一般工作一節是關於如何執行一般事項的快速參考，例如：列舉行事曆、建立、儲存和抓取行事曆事件和提醒，以及使用內建的控制器來建立和修改行事曆事件。 這一節不需要由 front 重新讀取，因為它是特定工作的參考。 

本指南中的所有工作都可在隨附範例應用程式中取得：

 [![隨附的範例應用程式畫面](eventkit-images/01.png)](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>需求

EventKit 是在 iOS 4.0 中引進，但在 iOS 6.0 中引進了提醒資料的存取權。 因此，若要進行一般 EventKit 開發，您必須以最低版本4.0 和6.0 為目標，以取得提醒。

此外，模擬器上也無法使用 [提醒] 應用程式，這表示提醒資料也將無法使用，除非您先新增它們。 此外，存取要求只會向使用者顯示在實際裝置上。 因此，在裝置上進行 EventKit 開發是最佳的測試。

## <a name="event-kit-basics"></a>事件套件基本概念

使用 EventKit 時，請務必掌握通用類別及其使用方式。 所有這些類別都可以在 `EventKit` 和 `EventKitUI` （適用于）中找到 `EKEventEditController` 。

### <a name="eventstore"></a>EventStore

*EventStore*類別是 EventKit 中最重要的類別，因為它必須在 EventKit 中執行任何作業。 您可以將它視為所有 EventKit 資料的持續性儲存體（或資料庫引擎）。 從 `EventStore` 您可以存取行事曆應用程式中的行事曆和行事曆事件，以及提醒應用程式中的提醒。

由於 `EventStore` 與資料庫引擎相似，因此它應該是長期的，這表示應該在應用程式實例的存留期內盡可能少地建立和終結。 事實上，當您在應用程式中建立一個實例之後，建議 `EventStore` 您針對應用程式的整個存留期保留該參考，除非您確定不再需要它。 此外，所有呼叫都應該移至單一 `EventStore` 實例。 基於這個理由，建議使用單一模式來保留單一實例。

#### <a name="creating-an-event-store"></a>建立事件存放區

下列程式碼說明如何建立類別的單一實例 `EventStore` ，並讓它可在應用程式中以靜態方式使用的有效方法：

```csharp
public class App
{
    public static App Current {
            get { return current; }
    }
    private static App current;

    public EKEventStore EventStore {
            get { return eventStore; }
    }
    protected EKEventStore eventStore;

    static App ()
    {
            current = new App();
    }
    protected App () 
    {
            eventStore = new EKEventStore ( );
    }
}
```

上述程式碼會在 `EventStore` 應用程式載入時，使用 Singleton 模式來具現化的實例。 `EventStore`接著可以從應用程式內全域存取，如下所示：

```csharp
App.Current.EventStore;
```

請注意，這裡的所有範例都會使用此模式，因此它們會參考 `EventStore` via `App.Current.EventStore` 。

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>要求存取行事曆和提醒資料

應用程式必須先要求存取行事曆事件資料或提醒資料（視您的需求而定），才能允許透過 EventStore 存取任何資料。 為了方便這項 `EventStore` 操作，會公開名為的方法，呼叫時，會向 `RequestAccess` 使用者顯示一個警示視圖，告訴他們應用程式正在要求存取行事歷數據或提醒資料（視傳遞給它的時間而定） `EKEntityType` 。 因為它會引發警示視圖，所以呼叫是非同步，而且會呼叫傳遞為（或 Lambda）的完成處理常式， `NSAction` 它會接收兩個參數; 是否已授與存取權的布林值，以及 `NSError` （如果不是-null 會包含要求中的任何錯誤資訊）。 例如，下列程式碼會要求存取行事曆事件資料，並在未授與要求時顯示警示視圖。

```csharp
App.Current.EventStore.RequestAccess (EKEntityType.Event, 
    (bool granted, NSError e) => {
            if (granted)
                    //do something here
            else
                    new UIAlertView ( "Access Denied", 
"User Denied Access to Calendar Data", null,
"ok", null).Show ();
            } );
```

一旦授與要求之後，只要應用程式已安裝在裝置上，就會記住它，而且不會向使用者顯示警示。
不過，只會提供資源類型的存取權，也就是已授與行事曆事件或提醒。 如果應用程式需要兩者的存取權，它應該會要求兩者。

由於會記住許可權，因此每次提出要求時都是相當便宜的，因此在執行作業之前，一律要求存取是個不錯的主意。

此外，由於會在個別的（非 UI）執行緒上呼叫完成處理常式，因此應該透過呼叫完成處理常式中 UI 的任何更新，否則會擲回 `InvokeOnMainThread` 例外狀況，如果未攔截到，應用程式將會損毀。

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType`是描述專案或資料類型的列舉 `EventKit` 。 它有兩個值： `Event` 和提醒。 它用於許多方法，包括用 `EventStore.RequestAccess` 來分辨要 `EventKit` 存取或抓取的資料種類。

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar*代表行事曆，其中包含一組行事曆事件。 行事曆可以儲存在許多不同的位置，例如本機上的*iCloud*，在協力廠商提供者位置（例如*Exchange Server*或*Google*）等。許多時候 `EKCalendar` 都是用來告訴您 `EventKit` 要在何處尋找事件，或是在何處儲存。

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController*可以在 `EventKitUI` 命名空間中找到，而且是內建的控制器，可以用來編輯或建立行事曆事件。 就像內建的相機控制器一樣， `EKEventEditController` 會為您執行繁重的工作，以顯示 UI 和處理儲存。

### <a name="ekevent"></a>EKEvent

 *EKEvent*代表行事曆事件。 `EKEvent`和都 `EKReminder` 繼承自， `EKCalendarItem` 而且具有 `Title` 、等欄位 `Notes` 。

### <a name="ekreminder"></a>EKReminder

 *EKReminder*代表提醒專案。

### <a name="ekspan"></a>EKSpan

*EKSpan*是一種列舉，會在修改可重複發生的事件時，描述事件的範圍，而且有兩個值：*此事件*和*FutureEvents*。 `ThisEvent`表示任何變更只會發生在所參考數列中的特定事件，而 `FutureEvents` 會影響該事件和所有未來的週期。

## <a name="tasks"></a>工作

為了方便使用，EventKit 使用量已分為一般工作，如下節所述。

### <a name="enumerate-calendars"></a>列舉行事曆

若要列舉使用者在裝置上設定的行事曆，請 `GetCalendars` 在上呼叫， `EventStore` 並傳遞您想要接收的行事曆類型（提醒或事件）：

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>使用內建控制器來新增或修改事件

如果您想要建立或編輯具有在使用行事曆應用程式時呈現給使用者之相同 UI 的事件， *EKEventEditViewController*會為您執行許多繁重的工作：

 [![使用行事曆應用程式時，向使用者顯示的 UI](eventkit-images/02.png)](eventkit-images/02.png#lightbox)

若要使用它，您會想要將它宣告為類別層級變數，以便在方法中宣告時不會進行垃圾收集：

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

然後，若要啟動它，請將它具現化、為它提供參考 `EventStore` 、將*EKEventEditViewDelegate*委派連接到它，然後使用來顯示它 `PresentViewController` ：

```csharp
EventKitUI.EKEventEditViewController eventController = 
        new EventKitUI.EKEventEditViewController ();

// set the controller's event store - it needs to know where/how to save the event
eventController.EventStore = App.Current.EventStore;

// wire up a delegate to handle events from the controller
eventControllerDelegate = new CreateEventEditViewDelegate ( eventController );
eventController.EditViewDelegate = eventControllerDelegate;

// show the event controller
PresentViewController ( eventController, true, null );
```

或者，如果您想要預先填入事件，您可以建立全新的事件（如下所示），也可以取出已儲存的事件：

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and exercise!";
newEvent.Notes = "This is your reminder to go and exercise for 30 minutes.”;
```

如果您想要預先填入 UI，請務必在控制器上設定事件屬性：

```csharp
eventController.Event = newEvent;
```

若要使用現有的事件，請參閱稍後的*依識別碼取得事件*一節。

委派應該覆寫 `Completed` 方法，當使用者完成對話方塊時，控制器就會呼叫此方法：

```csharp
protected class CreateEventEditViewDelegate : EventKitUI.EKEventEditViewDelegate
{
        // we need to keep a reference to the controller so we can dismiss it
        protected EventKitUI.EKEventEditViewController eventController;

        public CreateEventEditViewDelegate (EventKitUI.EKEventEditViewController eventController)
        {
                // save our controller reference
                this.eventController = eventController;
        }

        // completed is called when a user eith
        public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
        {
                eventController.DismissViewController (true, null);
                }
        }
}
```

（選擇性）在委派中，您可以在方法中檢查*動作* `Completed` 來修改事件並重新儲存，或執行其他工作（如果已取消），等等：

```csharp
public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
{
        eventController.DismissViewController (true, null);

        switch ( action ) {

        case EKEventEditViewAction.Canceled:
                break;
        case EKEventEditViewAction.Deleted:
                break;
        case EKEventEditViewAction.Saved:
                // if you wanted to modify the event you could do so here,
// and then save:
                //App.Current.EventStore.SaveEvent ( controller.Event, )
                break;
        }
}
```

### <a name="creating-an-event-programmatically"></a>以程式設計方式建立事件

若要在程式碼中建立事件，請在類別上使用*FromStore* factory 方法 `EKEvent` ，並在其上設定任何資料：

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and do some exercise!";
newEvent.Notes = "This is your motivational event to go and do 30 minutes of exercise. Super important. Do this.";
```

您必須設定要儲存事件的行事曆，但如果您沒有任何喜好設定，可以使用預設值：

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

若要儲存事件，請在上呼叫*SaveEvent*方法 `EventStore` ：

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

儲存之後， *EventIdentifier*屬性將會更新為可供稍後用來捕獲事件的唯一識別碼：

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier`是字串格式化的 GUID。

### <a name="create-a-reminder-programmatically"></a>以程式設計方式建立提醒

在程式碼中建立提醒與建立行事曆事件大致相同：

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

若要儲存，請在上呼叫*SaveReminder*方法 `EventStore` ：

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>依識別碼抓取事件

若要依識別碼抓取事件，請在上使用*EventFromIdentifier*方法， `EventStore` 並將從事件提取的傳遞給它 `EventIdentifier` ：

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

對於事件，還有兩個其他的識別碼屬性，但 `EventIdentifier` 是唯一適用于這個的。

### <a name="retrieving-a-reminder-by-id"></a>依識別碼抓取提醒

若要取得提醒，請在上使用*GetCalendarItem*方法， `EventStore` 並將*CalendarItemIdentifier*傳遞給它：

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

因為會傳回 `GetCalendarItem` `EKCalendarItem` ，所以 `EKReminder` 如果您需要存取提醒資料或使用實例做為稍後的，則必須將它轉換成 `EKReminder` 。

請勿使用行事 `GetCalendarItem` 曆事件，因為在撰寫本文時，它沒有作用。

### <a name="deleting-an-event"></a>刪除事件

若要刪除行事曆事件，請在上呼叫*RemoveEvent* ， `EventStore` 並傳遞事件的參考和適當的 `EKSpan` ：

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

不過，請注意，在刪除事件之後，事件參考將會是 `null` 。

### <a name="deleting-a-reminder"></a>刪除提醒

若要刪除提醒，請在上呼叫*RemoveReminder* `EventStore` ，並將參考傳遞給提醒：

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

請注意，在上述程式碼中，會將轉換為 `EKReminder` ，因為 `GetCalendarItem` 已用來抓取它

### <a name="searching-for-events"></a>搜尋事件

若要搜尋行事曆事件，您必須透過上的*PredicateForEvents*方法來建立*NSPredicate*物件 `EventStore` 。 `NSPredicate`是 iOS 用來尋找相符專案的查詢資料物件：

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

建立之後 `NSPredicate` ，請在上使用*EventsMatching*方法 `EventStore` ：

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

請注意，查詢是同步（封鎖），而且可能需要很長的時間（視查詢而定），因此您可能會想要加速新的執行緒或工作來執行此作業。

### <a name="searching-for-reminders"></a>搜尋提醒

搜尋提醒與事件類似;它需要述詞，但呼叫已經是非同步，因此您不必擔心封鎖執行緒：

```csharp
// create our NSPredicate which we'll use for the query
NSPredicate query = App.Current.EventStore.PredicateForReminders ( null );

// execute the query
App.Current.EventStore.FetchReminders (
        query, ( EKReminder[] items ) => {
                // do someting with the items
        } );
```

## <a name="summary"></a>總結

本檔概述 EventKit 架構的重要部分，以及一些最常見的工作。 不過，EventKit 架構非常大且功能強大，而且包含未在此處引進的功能，例如：批次更新、設定警示、設定事件的週期、註冊及接聽行事曆資料庫的變更、設定地理柵欄等等。  如需詳細資訊，請參閱 Apple 的行事[曆與提醒程式設計指南](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)。

## <a name="related-links"></a>相關連結

- [行事曆（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/calendars)
- [iOS 6 簡介](~/ios/platform/introduction-to-ios6/index.md)
- [行事曆與提醒簡介](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
