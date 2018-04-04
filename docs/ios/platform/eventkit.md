---
title: EventKit
description: 本指南提供有關如何存取和使用行事曆、 CalendarEvents，與提醒儲存的資料在行事曆資料庫中，公開透過 EventKit 的概觀。 它涵蓋的主要類別和其 EventKit 程式設計，以及數 EventKit 架構與相關聯的常見工作的角色。
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a8439586ac92f8139cf9341611125352c85706e5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="eventkit"></a>EventKit

_本指南提供有關如何存取和使用行事曆、 CalendarEvents，與提醒儲存的資料在行事曆資料庫中，公開透過 EventKit 的概觀。它涵蓋的主要類別和其 EventKit 程式設計，以及數 EventKit 架構與相關聯的常見工作的角色。_

iOS 會有兩個相關的行事曆應用程式內建： 行事曆應用程式，並提醒應用程式。 若要了解如何行事曆應用程式管理行事曆資料夠簡單但提醒應用程式是較不明顯。 提醒實際上可以有與其相關聯的通常是因為，要完成之時的日期等等。因此，iOS 會儲存所有行事曆資料，無論行事曆事件或在單一位置，稱為備忘提醒*行事曆資料庫*。

EventKit framework 提供方法來存取*行事曆*，*行事曆事件*，和*提醒*行事曆資料庫儲存的資料。 存取行事曆與行事曆事件已經提供自 iOS 4，但提醒存取的新 iOS 6。

本指南中，我們要涵蓋：

-   **EventKit 基本概念**– 這會造成 EventKit 透過主要類別的基本部分，而且提供了解其使用方式。 需要先處理文件的下一個部分讀取這一節。 
-   **一般工作**– 一般工作 > 一節就是要如何執行常見的動作，例如，快速參考; 列舉行事曆、 建立、 儲存和擷取行事曆事件和提醒，以及使用的內建的控制站建立和修改行事曆事件。 本節需要讀取前-後，因為它已經是用來針對特定工作的參考。 


本指南中的所有工作都可在隨附的範例應用程式：

 [![](eventkit-images/01.png "隨附的範例應用程式畫面")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>需求

EventKit 首見於 iOS 4.0，但 iOS 6.0 中導入提醒資料的存取權。 因此，若要執行一般 EventKit 開發，您必須至少為目標版本 4.0 和 6.0 提醒。

此外，提醒應用程式並不適用於模擬器，這表示，提醒資料將也無法使用，除非您將其新增第一次。 此外，存取要求只會顯示在實際裝置上的使用者。 因此，EventKit 開發最佳測試是在裝置上。

## <a name="event-kit-basics"></a>事件套件的基本概念

當使用 EventKit，務必的通用類別和其使用方式。 所有這些類別可以位於`EventKit`和`EventKitUI`(如`EKEventEditController`)。

### <a name="eventstore"></a>EventStore

*EventStore*類別是 EventKit 中的最重要類別，因為它需要 EventKit 中執行任何作業。 它可以視為永續性儲存體或資料庫引擎，為所有 EventKit 資料。 從`EventStore`您具有存取權的行事曆和行事曆應用程式中的行事曆事件，以及提醒應用程式中的提醒。

因為`EventStore`會像是資料庫引擎，它應該是長時間執行，也就是說，它應該會在建立及終結盡可能應用程式執行個體的存留期間。 事實上，則建議，一旦您建立一個執行個體`EventStore`應用程式中，您該參考保留周圍的整個存留期應用程式，除非您確定您不需要使用一次。 此外，所有呼叫必須都移至單一`EventStore`執行個體。 基於這個理由，單一子句模式建議保持可用的單一執行個體。

#### <a name="creating-an-event-store"></a>建立事件存放區

下列程式碼將說明有效的方式建立的單一執行個體`EventStore`類別，並將其以靜態方式從應用程式中提供：

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

上述程式碼來具現化的執行個體使用單一子句模式`EventStore`應用程式載入時。 `EventStore`可以再全域從存取應用程式中，如下所示：

```csharp
App.Current.EventStore;
```

請注意，所有在此範例使用此模式中，因此其參考`EventStore`透過`App.Current.EventStore`。

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>要求存取行事曆和提醒資料

然後才允許存取透過 EventStore 任何資料，應用程式必須先要求存取行事曆事件資料或提醒資料，您需要根據哪一個。 為了幫助，`EventStore`公開的方法呼叫`RequestAccess`其中 — 呼叫時，會告訴他們的應用程式所要求的存取權的行事曆資料 」 或 「 提醒資料，根據哪個對使用者顯示警示的檢視`EKEntityType`傳遞給它。 便會產生警示的檢視，因為呼叫是非同步的並將呼叫做為傳遞的完成處理常式`NSAction`（或 Lambda） 它將會接收兩個參數，則為布林值的存取是否已授與，和`NSError`，而如果將非 null包含在要求中的所有錯誤資訊。 比方說，下列程式碼將會要求存取行事曆事件資料並顯示警示檢視，如果未授與要求。

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

一旦已授與要求，會記住，只要應用程式安裝在裝置上並不會快顯通知給使用者。
不過，存取只會提供行事曆事件或提醒授與的資源類型。 如果應用程式需要存取，則應該要求兩者。

權限會記住，因為它是相對低廉來提出要求，每次，所以最好先執行運算之前，永遠要求存取。

此外，個別 (非 UI) 執行緒上呼叫完成處理常式，因為任何更新以完成處理常式中的 UI 應呼叫透過`InvokeOnMainThread`，否則將擲回例外狀況，並不會遭到攔截，如果應用程式將會損毀。

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` 是一種列舉，描述的型別`EventKit`項目或資料。 它有兩個值：`Event`和提示。 用於多種方法，包括`EventStore.RequestAccess`告訴`EventKit`何種取得存取權，或擷取的資料。

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar*代表行事曆，其中包含一組的行事曆事件。 行事曆可以儲存在許多不同的位置，例如在本機中*iCloud*，請在第 3 這類協力廠商服務提供者位置*Exchange Server*或*Google*等等。多次`EKCalendar`可用來告知`EventKit`哪裡可找到事件或將它們儲存的位置。

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController*位於`EventKitUI`命名空間和一個內建的控制站，可用來編輯或建立行事曆事件。 就像是相機控制器中的 內建`EKEventEditController`會為您顯示 UI 和處理儲存中的困難。

### <a name="ekevent"></a>EKEvent

 *EKEvent*代表行事曆事件。 同時`EKEvent`和`EKReminder`繼承自`EKCalendarItem`和具有欄位，例如`Title`， `Notes`，依此類推。

### <a name="ekreminder"></a>EKReminder

 *EKReminder*表示提醒項目。

### <a name="ekspan"></a>EKSpan

*EKSpan*是一種列舉，描述的事件時修改的事件可以循環，並有兩個值的範圍：*擔不多*和*FutureEvents*。 `ThisEvent` 表示的任何變更只會繼續，所參考，數列中的特定事件而`FutureEvents`會影響該事件，以及所有未來的日數。

## <a name="tasks"></a>工作

為了方便使用，EventKit 使用量具有已分成下列各節中所述的一般工作。

### <a name="enumerate-calendars"></a>列舉的行事曆

若要列舉的裝置設定使用者的行事曆，請呼叫`GetCalendars`上`EventStore`，並傳遞您想要接收的日曆 （提醒或事件）：

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>加入或修改使用內建的控制站的事件

*EKEventEditViewController*為您執行許多繁重的工作，如果您想要建立或編輯具有相同的 UI 呈現給使用者時使用的行事曆應用程式的事件：

 [![](eventkit-images/02.png "使用行事曆應用程式時，向使用者顯示的 UI")](eventkit-images/02.png#lightbox)

若要使用它，您會想要將它宣告為類別層級變數，讓它不會取得回收如果它在方法中宣告：

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

然後，若要啟動它： 具現化，將參考`EventStore`，裝設*EKEventEditViewDelegate*委派給它，並且顯示使用`PresentViewController`:

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

（選擇性） 如果您想要預先填入事件中，您可以建立全新的事件 （如下所示），或您可以擷取已儲存的事件：

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

如果您想要預先填入 UI，請務必在控制器上設定的事件屬性：

```csharp
eventController.Event = newEvent;
```

若要使用現有的事件，請參閱*擷取的事件識別碼*稍後區段。

應該覆寫委派`Completed`使用者完成對話方塊時，會將控制器所呼叫的方法：

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

（選擇性） 在委派中，您可以檢查*動作*中`Completed`方法來修改事件並重新儲存，或執行其他方面，如果它已取消、 烈焰：

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

若要建立程式碼中的事件，請使用*FromStore*上的 factory 方法`EKEvent`類別，並在其上設定的任何資料：

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

您必須設定要儲存在中，事件的行事曆，但如果您不有任何喜好設定，您可以使用預設值：

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

若要儲存的事件，請呼叫*SaveEvent*方法`EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

儲存之後， *EventIdentifier*屬性會以更新可以稍後用來擷取事件的唯一識別項：

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` 是字串格式的 GUID。

### <a name="create-a-reminder-programmatically"></a>以程式設計方式建立提醒

建立好提醒您程式碼中，是與建立行事曆事件大致相同：

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

若要儲存，請呼叫*SaveReminder*方法`EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>擷取的事件識別碼

它所擷取的事件識別碼，請使用*EventFromIdentifier*方法`EventStore`並將它傳遞`EventIdentifier`，提取自事件：

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

對於事件，還有其他兩個識別項屬性，但`EventIdentifier`是唯一適用於此。

### <a name="retrieving-a-reminder-by-id"></a>擷取識別碼的提醒

若要擷取提醒，請使用*GetCalendarItem*方法`EventStore`並將它傳遞*CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

因為`GetCalendarItem`傳回`EKCalendarItem`，必須將它轉換成`EKReminder`如果您需要存取提醒資料，或使用執行個體當做`EKReminder`更新版本。

請勿使用`GetCalendarItem`行事曆事件，因為在撰寫時，它不能。

### <a name="deleting-an-event"></a>刪除事件

若要刪除的行事曆事件，請呼叫*RemoveEvent*上您`EventStore`和事件，並適當的參考傳遞`EKSpan`:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

請注意不過，事件已刪除之後，就會將事件參考`null`。

### <a name="deleting-a-reminder"></a>刪除提醒

若要刪除提醒，請呼叫*RemoveReminder*上`EventStore`並傳遞參考至提醒：

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

請注意，上述程式碼中有轉換成`EKReminder`，因為`GetCalendarItem`用來擷取它

### <a name="searching-for-events"></a>搜尋事件

若要搜尋的行事曆事件，您必須建立*NSPredicate*物件透過*PredicateForEvents*方法`EventStore`。 `NSPredicate`是查詢的資料物件，iOS 會使用來尋找相符項目的：

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

一旦您已建立`NSPredicate`，使用*EventsMatching*方法`EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

請注意，查詢是同步 （封鎖） 可能需要很長的時間，視查詢，而定，因此您可能想要加速新的執行緒或工作才能執行。

### <a name="searching-for-reminders"></a>搜尋提醒

搜尋提醒是類似的事件。它需要的述詞，但呼叫已經是非同步的因此您不需要擔心封鎖的執行緒：

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

本文件提供 EventKit 架構的重要項目和一些最常見工作的概觀。 不過，EventKit framework 非常大型的強大，而且包含功能並未已導入，例如： 批次更新，設定註冊和行事曆資料庫上的變更接聽的事件上設定循環的警示設定 GeoFences 等等。  如需詳細資訊，請參閱 Apple[行事曆和提醒程式設計指南](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)。


## <a name="related-links"></a>相關連結

- [行事曆 （範例）](https://developer.xamarin.com/samples/monotouch/Calendars/)
- [iOS 6 簡介](~/ios/platform/introduction-to-ios6/index.md)
- [行事曆和提醒簡介](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
