---
title: 在 Xamarin 中 EventKit
description: 本檔說明 EventKit 以及如何在 Xamarin 中使用它。 它會討論行事曆、行事曆事件和提醒、查看使用 EventKit 進行程式設計時經常使用的類別等等。
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 2e52fea7975094eb19d5247ea7747b40fda92630
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433160"
---
# <a name="eventkit-in-xamarinios"></a>在 Xamarin 中 EventKit

iOS 有兩個內建的行事曆相關應用程式：行事曆應用程式和提醒應用程式。 您可以直接瞭解行事曆應用程式管理行事歷數據的方式，但提醒應用程式比較不明顯。 提醒在到期時、完成時間等方面，實際上可以有相關的日期。因此，iOS 會將所有行事歷數據（不論是行事曆活動或提醒）儲存在一個位置（稱為行事 *曆資料庫*）中。

EventKit 架構提供一種方式來存取行事 *曆資料庫*儲存的行事曆、行事 *曆事件*和 *提醒* 資料。 從 iOS 4 開始，可以存取行事曆和行事曆事件，但在 iOS 6 中有新的提醒存取權。

在本指南中，我們將討論：

- **EventKit 基本概念** -這會透過主要類別來介紹 EventKit 的基本部分，並提供其使用方式的瞭解。 在處理檔的下一個部分之前，需要閱讀此區段。 
- **一般** 工作：「一般工作」一節旨在快速參考如何進行一般作業，例如：列舉行事曆、建立、儲存和取出行事曆事件和提醒，以及使用內建控制器來建立和修改行事曆事件。 此區段不需要從前端讀取，因為這是特定工作的參考。 

本指南中的所有工作都可在隨附的範例應用程式中取得：

 [![附屬範例應用程式畫面](eventkit-images/01.png)](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>需求

EventKit 是在 iOS 4.0 中引進，但在 iOS 6.0 中引進了對提醒資料的存取。 因此，若要進行一般 EventKit 開發，您必須將目標設為至少4.0 版，並針對提醒輸入6.0。

此外，模擬器上無法使用提醒應用程式，這表示提醒資料也將無法使用，除非您先新增這些資料。 此外，使用者只會在實際裝置上向使用者顯示存取要求。 因此，EventKit 的開發最好是在裝置上進行測試。

## <a name="event-kit-basics"></a>事件套件基本概念

使用 EventKit 時，請務必先理解通用類別和其使用方式。 所有這些類別都可以在的 `EventKit` 和 (中找到 `EventKitUI` `EKEventEditController`) 。

### <a name="eventstore"></a>EventStore

*EventStore*類別是 EventKit 中最重要的類別，因為它必須在 EventKit 中執行任何作業。 您可以將它視為所有 EventKit 資料的持續性儲存體或資料庫引擎。 `EventStore`您可以從存取行事曆應用程式中的行事曆和行事曆事件，以及提醒應用程式中的提醒。

由於 `EventStore` 就像資料庫引擎一樣，它應該是長期存在的，這表示在應用程式實例的存留期內，應該盡可能少地建立和終結。 事實上，建議您在應用程式中建立一個的實例之後，在 `EventStore` 應用程式的整個存留期內保留該參考，除非您確定不再需要它。 此外，所有呼叫都應該移至單一 `EventStore` 實例。 基於這個理由，建議使用單一模式來保存單一實例。

#### <a name="creating-an-event-store"></a>建立事件存放區

下列程式碼說明如何建立類別的單一實例 `EventStore` ，並讓它從應用程式內以靜態方式提供使用的有效方法：

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

當應用程式載入時，上述程式碼會使用 Singleton 模式來具現化的實例 `EventStore` 。 `EventStore`接著可以從應用程式內全域存取，如下所示：

```csharp
App.Current.EventStore;
```

請注意，此處的所有範例都會使用此模式，因此它們會參考 `EventStore` via `App.Current.EventStore` 。

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>要求存取行事曆和提醒資料

在允許透過 EventStore 存取任何資料之前，應用程式必須先要求存取行事曆事件資料或提醒資料，視您需要的資料而定。 為了方便這項操作， `EventStore` 會公開呼叫的方法， `RequestAccess` 在呼叫此方法時，會向使用者顯示警示視圖，告知他們應用程式要求存取行事歷數據或提醒資料，視傳遞給它的資料而定 `EKEntityType` 。 因為它會引發警示視圖，所以呼叫是非同步，而且會將完成的處理常式呼叫當做 `NSAction` (或 Lambda) 傳送給它，以接收兩個參數; 是否授與存取權的布林值，以及 `NSError` ，如果不是 null，則會在要求中包含任何錯誤資訊。 例如，下列程式碼會要求存取行事曆事件資料，如果未授與要求，則會顯示警示查看。

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

一旦授與要求之後，只要應用程式安裝在裝置上，系統就會記住它，而不會向使用者顯示警示。
不過，只會提供資源類型的存取權，也就是已授與行事曆事件或提醒。 如果應用程式需要存取兩者，則應同時要求兩者。

由於會記住許可權，因此每次提出要求都相當便宜，因此最好在執行作業之前要求存取。

此外，由於會在個別 (非 UI) 執行緒上呼叫完成處理常式，因此應該透過呼叫完成處理常式中 UI 的任何更新，否則會擲回 `InvokeOnMainThread` 例外狀況，如果未攔截，應用程式將會損毀。

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` 這是描述 `EventKit` 專案或資料類型的列舉。 它有兩個值： `Event` 和提醒。 它是用在許多方法中，包括用來 `EventStore.RequestAccess` 分辨要 `EventKit` 存取或抓取的資料類型。

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar* 代表包含行事曆事件群組的行事曆。 行事曆可以儲存在許多不同的地方，例如在本機的 *iCloud*中，在協力廠商提供者位置（例如 *Exchange Server* 或 *Google*等等）中。很多時候 `EKCalendar` 都是用來告訴您 `EventKit` 要在哪裡尋找事件，或是要將它們儲存在哪裡。

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController* 可在 `EventKitUI` 命名空間中找到，而且是可用於編輯或建立行事曆事件的內建控制器。 就像內建的相機控制器一樣， `EKEventEditController` 在顯示 UI 和處理儲存方面，會有很大的努力。

### <a name="ekevent"></a>EKEvent

 *EKEvent* 代表行事曆事件。 `EKEvent`和都 `EKReminder` 是繼承自 `EKCalendarItem` ，而且都有欄位 `Title` ，例如、 `Notes` 等等。

### <a name="ekreminder"></a>EKReminder

 *EKReminder* 代表提醒專案。

### <a name="ekspan"></a>EKSpan

*EKSpan* 是一種列舉，描述修改可重複的事件時的事件範圍，而且有兩個值： *此事件* 和 *FutureEvents*。 `ThisEvent` 表示任何變更只會發生于所參考之數列中的特定事件，而 `FutureEvents` 會影響該事件和未來所有的週期。

## <a name="tasks"></a>工作

為了方便使用，EventKit 使用方式已細分為一般工作，如下列各節所述。

### <a name="enumerate-calendars"></a>列舉行事曆

若要列舉使用者在裝置上設定的行事曆，請 `GetCalendars` 在上呼叫 `EventStore` ，並傳遞行事曆的類型 (您想要接收的提醒或事件) ：

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>使用內建控制器來新增或修改事件

如果您想要以在使用行事曆應用程式時向使用者顯示的相同 UI 來建立或編輯事件， *EKEventEditViewController* 會為您執行許多繁重的工作：

 [![使用行事曆應用程式時向使用者顯示的 UI](eventkit-images/02.png)](eventkit-images/02.png#lightbox)

若要使用它，您會想要將它宣告為類別層級變數，以便在方法中宣告它時，不會被垃圾收集：

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

然後，若要啟動它：將它具現化、為它指定參考 `EventStore` 、連接 *EKEventEditViewDelegate* 委派，然後使用下列內容來顯示它 `PresentViewController` ：

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

（選擇性）如果您想要預先填入事件，您可以建立全新事件 (（如下所示）) ，也可以取出儲存的事件：

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

若要使用現有的事件，請參閱稍後的 *依識別碼抓取事件* 一節。

委派應覆寫 `Completed` 方法，當使用者完成對話方塊時，控制器會呼叫此方法：

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

（選擇性）在委派中，您可以檢查方法中的 *動作* `Completed` 來修改事件和重新儲存，或執行其他作業（如果已取消）等等：

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

若要在程式碼中建立事件，請在類別上使用 *FromStore* factory 方法 `EKEvent` ，並在其上設定任何資料：

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

您必須設定要儲存事件的行事曆，但如果您沒有喜好設定，則可以使用預設值：

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

若要儲存事件，請在上呼叫 *SaveEvent* 方法 `EventStore` ：

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

儲存之後， *EventIdentifier* 屬性會以可稍後用來取得事件的唯一識別碼進行更新：

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` 是字串格式的 GUID。

### <a name="create-a-reminder-programmatically"></a>以程式設計方式建立提醒

在程式碼中建立提醒與建立行事曆事件的方式大致相同：

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

若要儲存，請在上呼叫 *SaveReminder* 方法 `EventStore` ：

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>依識別碼抓取事件

若要依識別碼取得事件，請在上使用 *EventFromIdentifier* 方法， `EventStore` 並傳遞 `EventIdentifier` 從事件提取的：

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

針對事件，有兩個其他識別碼屬性，但 `EventIdentifier` 這是唯一適用的識別碼屬性。

### <a name="retrieving-a-reminder-by-id"></a>依識別碼抓取提醒

若要取得提醒，請在上使用 *GetCalendarItem* 方法， `EventStore` 並將 *CalendarItemIdentifier*傳遞給它：

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

因為會傳回 `GetCalendarItem` `EKCalendarItem` ，所以 `EKReminder` 如果您需要存取提醒資料或稍後使用實例，就必須將它轉換成 `EKReminder` 。

請勿使用行事 `GetCalendarItem` 曆事件，因為在撰寫本文時，它無法運作。

### <a name="deleting-an-event"></a>刪除事件

若要刪除行事曆事件，請在上呼叫 *RemoveEvent* ， `EventStore` 並傳遞事件的參考和適當的 `EKSpan` ：

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

不過請注意，刪除事件之後，事件參考將會是 `null` 。

### <a name="deleting-a-reminder"></a>刪除提醒

若要刪除提醒，請在上呼叫 *RemoveReminder* `EventStore` ，並傳遞提醒的參考：

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

請注意，在上述程式碼中，會將轉換為 `EKReminder` ，因為 `GetCalendarItem` 它是用來取出它

### <a name="searching-for-events"></a>搜尋事件

若要搜尋行事曆事件，您必須在上透過*PredicateForEvents*方法建立*NSPredicate*物件 `EventStore` 。 `NSPredicate`是 iOS 用來尋找相符專案的查詢資料物件：

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

建立之後 `NSPredicate` ，請在上使用 *EventsMatching* 方法 `EventStore` ：

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

請注意，查詢是同步 (封鎖) ，而且可能需要很長的時間（視查詢而定），因此您可能會想要啟動新的執行緒或工作來執行此作業。

### <a name="searching-for-reminders"></a>搜尋提醒

搜尋提醒與事件類似;它需要述詞，但呼叫已經是非同步，因此您不需要擔心封鎖執行緒：

```csharp
// create our NSPredicate which we'll use for the query
NSPredicate query = App.Current.EventStore.PredicateForReminders ( null );

// execute the query
App.Current.EventStore.FetchReminders (
        query, ( EKReminder[] items ) => {
                // do someting with the items
        } );
```

## <a name="summary"></a>摘要

本檔概述 EventKit 架構的重要部分，以及一些最常見的工作。 不過，EventKit 架構非常龐大且功能強大，而且還包含尚未引進的功能，例如：批次更新、設定警示、設定事件的週期、註冊及接聽行事曆資料庫的變更、設定地理柵欄等等。  如需詳細資訊，請參閱 Apple 的行事 [曆和提醒程式設計指南](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)。

## <a name="related-links"></a>相關連結

- [行事曆 (範例) ](/samples/xamarin/ios-samples/calendars)
- [iOS 6 簡介](~/ios/platform/introduction-to-ios6/index.md)
- [行事曆和提醒簡介](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)