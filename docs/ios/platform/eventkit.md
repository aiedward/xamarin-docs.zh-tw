---
title: 在 Xamarin.iOS 中 EventKit
description: 本文件說明 EventKit，以及如何在 Xamarin.iOS 中使用它。 它討論行事曆、 行事曆事件和提醒，其中探討了常用 EventKit，和其他服務進行程式設計時的類別。
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: ea03c8b382e2de29bd20ab1d696d7abb7733e182
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369224"
---
# <a name="eventkit-in-xamarinios"></a>在 Xamarin.iOS 中 EventKit

iOS 有兩個行事曆相關應用程式內建： 行事曆應用程式，並提醒應用程式。 夠簡單了解如何行事曆應用程式管理行事曆的資料，但提醒應用程式是較不明顯。 提醒實際上可以有與其相關聯的原因，完成之時在時的日期等。因此，iOS 會儲存所有行事曆資料，無論行事曆事件或提醒，在單一位置，稱為*行事曆資料庫*。

EventKit framework 可用來存取*行事曆*，*行事曆事件*，並*提醒*行事曆資料庫所儲存的資料。 存取行事曆和行事曆事件便已提供 iOS 4，但權限提醒的新 iOS 6。

本指南中，我們要討論：

-   **EventKit 基本概念**– 這會介紹的基本部分 EventKit 透過主要的類別，並提供其使用方式的了解。 需要先處理文件的下一個部分讀取這一節。 
-   **一般工作**– 常見的 [工作] 區段刻意要如何進行這類的常見項目上的快速參考; 列舉的行事曆、 建立、 儲存和擷取行事曆事件和提醒，以及使用的內建的控制站建立和修改行事曆事件。 本節需要讀取前-後，因為它已經是用來針對特定工作的參考。 


本指南中的所有工作都可在隨附的範例應用程式：

 [![](eventkit-images/01.png "隨附的範例應用程式畫面")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>需求

IOS 4.0 中引進了 EventKit 但 iOS 6.0 中引進了提醒資料的存取權。 因此，若要執行一般 EventKit 開發，您必須至少為目標版本 4.0 和 6.0 的提醒。

此外，提醒應用程式並不適用於模擬器中，這表示，提醒資料會也無法使用，除非您將其新增第一次。 此外，存取要求只會顯示實際的裝置上的使用者。 此情況下，EventKit 開發最佳測試方式是在裝置上。

## <a name="event-kit-basics"></a>事件套件基本概念

當使用 EventKit，務必掌握到一些常見的類別和其使用方式。 所有這些類別可在`EventKit`並`EventKitUI`(如`EKEventEditController`)。

### <a name="eventstore"></a>EventStore

*EventStore*類別是 EventKit 中最重要的類別，因為它需要在 EventKit 中執行任何作業。 它可以視為永續性儲存體或資料庫引擎、 EventKit 的所有資料。 從`EventStore`您可以存取的行事曆和行事曆事件的行事曆應用程式，以及提醒應用程式中的提醒。

因為`EventStore`是例如資料庫引擎，它應該是長時間執行，這表示，它應該是建立及終結盡可能應用程式執行個體的存留期間。 事實上，建議您建立一個執行個體之後`EventStore`應用程式中，您保留該參考周圍的整個存留期間，應用程式，除非您確定您不需要再次。 此外，所有的呼叫應該移至單一`EventStore`執行個體。 基於這個理由，單一子句模式建議保持可用的單一執行個體。

#### <a name="creating-an-event-store"></a>建立事件存放區

下列程式碼說明有效的方式建立的單一執行個體`EventStore`類別，並將它以靜態方式從應用程式內提供：

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

上述程式碼來具現化的執行個體使用單一子句模式`EventStore`應用程式載入時。 `EventStore`可供存取全域從應用程式內，如下所示：

```csharp
App.Current.EventStore;
```

請注意，在這裡的所有範例都使用此模式中，因此它們會參考`EventStore`透過`App.Current.EventStore`。

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>要求存取行事曆和提醒資料

然後才允許存取 EventStore 透過任何資料，應用程式必須先要求存取行事曆事件資料或提醒資料，您需要何者而定。 若要達到此目的，`EventStore`公開 （expose) 呼叫的方法`RequestAccess`這 — 呼叫時，會顯示給使用者，告知應用程式正在要求存取行事曆資料或提醒資料，根據哪個的警示檢視`EKEntityType`傳遞給它。 便會產生警示的檢視，因為呼叫是非同步的並會呼叫完成處理常式，傳遞做為`NSAction`（或 Lambda），這將會收到兩個參數; 是否已授與存取權的布林值和`NSError`，它會如果非 null包含在要求中的任何錯誤資訊。 比方說，下列程式碼會要求行事曆事件資料和顯示警示檢視，如果要求未授與存取權。

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

一旦已授與要求，它會記住只要應用程式安裝在裝置上，而且不會快顯警示給使用者。
不過，存取僅提供給資源行事曆事件或提醒授與的類型。 如果應用程式需要兩者的存取權，則應該要求兩者。

權限會記住，因為它是相對較低廉，提出要求，每次，因此它是個不錯的主意，執行作業之前，永遠要求存取。

此外，因為個別 (非 UI) 執行緒上呼叫完成處理常式，在 ui 中完成處理常式的任何更新，應該呼叫透過`InvokeOnMainThread`，否則會擲回例外狀況，如果未攔截到，應用程式將會損毀。

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` 是一種列舉，描述的型別`EventKit`項目或資料。 它有兩個值：`Event`與提醒。 它會在幾個方法，包括`EventStore.RequestAccess`告知`EventKit`何種來取得存取權，或擷取的資料。

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar*代表行事曆，其中包含一組的行事曆事件。 行事曆可以儲存在許多不同的地方，例如在本機， *iCloud*，請在第 3 方服務提供者位置這類*Exchange Server*或是*Google*，依此類推。多次`EKCalendar`用來告訴`EventKit`哪裡可找到事件或將其儲存的位置。

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController*篇`EventKitUI`命名空間和可用來編輯或建立行事曆事件是內建控制站。 就像是相機控制器中的 內建`EKEventEditController`會為您顯示 UI，以及處理儲存在完成困難的工作。

### <a name="ekevent"></a>EKEvent

 *EKEvent*代表行事曆事件。 兩者`EKEvent`並`EKReminder`繼承自`EKCalendarItem`並具有欄位，例如`Title`， `Notes`，依此類推。

### <a name="ekreminder"></a>EKReminder

 *EKReminder*表示提醒項目。

### <a name="ekspan"></a>EKSpan

*EKSpan*是一種列舉，描述事件時修改事件，可以循環，並有兩個值的範圍：*此事件*並*FutureEvents*。 `ThisEvent` 表示的任何變更時才會發生特定事件系列所參考，而`FutureEvents`會影響該事件和所有未來的週期。

## <a name="tasks"></a>工作

為了方便使用，EventKit 使用量有已分成下列各節所述的一般工作。

### <a name="enumerate-calendars"></a>列舉的行事曆

若要列舉在裝置上設定使用者的行事曆，請呼叫`GetCalendars`上`EventStore`並傳遞您想要接收的日曆 （提醒或事件） 的類型：

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>新增或修改使用內建的控制站的事件

*EKEventEditViewController*為您會執行許多繁重的工作，如果您想要建立或編輯相同的 UI，使用行事曆應用程式時，會向使用者的事件：

 [![](eventkit-images/02.png "使用行事曆應用程式時，會向使用者 UI")](eventkit-images/02.png#lightbox)

若要使用它，您會想要將它宣告為類別層級變數，使其不會回收如果它在方法宣告：

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

然後，若要啟動它： 具現化，並提供它的參考`EventStore`，接通*EKEventEditViewDelegate*委派給它，並且顯示使用`PresentViewController`:

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

（選擇性） 如果您想要預先填入事件，您可以建立全新的事件 （如下所示），或您可以擷取已儲存的事件：

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

若要使用現有的事件，請參閱*擷取的事件識別碼*稍後一節。

委派應該覆寫`Completed`使用者完成對話方塊時，會將控制器所呼叫的方法：

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

（選擇性） 在委派中，您可以檢查*動作*在`Completed`方法來修改事件並重新儲存，或執行其他項目，如果它已取消、 烈焰：

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

若要在程式碼中建立事件，請使用*FromStore*上的 factory 方法`EKEvent`類別，並在其上設定的任何資料：

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

您必須設定您想要儲存在中，事件的行事曆，但如果您有無喜好設定，您可以使用預設值：

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

若要儲存事件，請呼叫*SaveEvent*方法`EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

儲存之後， *EventIdentifier*可以稍後用來擷取事件的唯一識別碼將會更新屬性：

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` 是字串格式的 GUID。

### <a name="create-a-reminder-programmatically"></a>以程式設計方式建立提醒

建立程式碼中的提醒會就如同建立行事曆事件：

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

事件，則兩個其他的識別項屬性，但`EventIdentifier`是唯一適用於此。

### <a name="retrieving-a-reminder-by-id"></a>擷取識別碼提醒

若要擷取提醒，請使用*GetCalendarItem*方法`EventStore`並將它傳遞*CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

因為`GetCalendarItem`會傳回`EKCalendarItem`，它必須轉換成`EKReminder`如果您需要存取提醒資料，或使用與執行個體`EKReminder`更新版本。

請勿使用`GetCalendarItem`行事曆事件，因為在撰寫時，時間沒有作用。

### <a name="deleting-an-event"></a>刪除事件

若要刪除的行事曆事件，請呼叫*RemoveEvent*在您`EventStore`和事件，以及適當的參考傳遞`EKSpan`:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

請注意不過，在刪除事件之後，將會將事件參考`null`。

### <a name="deleting-a-reminder"></a>刪除提醒

若要刪除提醒，請呼叫*RemoveReminder*上`EventStore`和傳遞，提醒您參考：

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

請注意，在上述程式碼有轉型`EKReminder`，因為`GetCalendarItem`用來擷取它

### <a name="searching-for-events"></a>搜尋事件

若要搜尋的行事曆事件，您必須建立*NSPredicate*物件透過*PredicateForEvents*方法`EventStore`。 `NSPredicate`是查詢資料物件，iOS 會使用來找出相符項目：

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

當您建立後`NSPredicate`，使用*EventsMatching*方法`EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

請注意查詢已同步 （封鎖），而且可能需要很長的時間，依查詢，因此您可能想要啟動新的執行緒或工作才能這麼做。

### <a name="searching-for-reminders"></a>搜尋提醒

搜尋提醒是類似事件;它需要的述詞，但呼叫已非同步的因此您不需要擔心封鎖的執行緒：

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

本文件概要 EventKit framework 中，這兩個重要片段，並將一些最常見的工作。 不過，EventKit framework 很大，而且功能強大，而且包含尚未推出，這類的功能： 批次更新，設定警示，在註冊及接聽變更行事曆的資料庫上的事件上設定循環設定地理柵欄和更多功能。  如需詳細資訊，請參閱 Apple[行事曆和提醒程式設計指南](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)。


## <a name="related-links"></a>相關連結

- [行事曆 （範例）](https://developer.xamarin.com/samples/monotouch/Calendars/)
- [iOS 6 簡介](~/ios/platform/introduction-to-ios6/index.md)
- [行事曆和提醒簡介](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
