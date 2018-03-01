---
title: "行事曆"
ms.topic: article
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 8075473464472c5a830f62ebfc91c00ad54d1b98
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="calendar"></a>行事曆

<a name="Calendar_API" />

## <a name="calendar-api"></a>行事曆應用程式開發介面

一組新的行事曆 Android 4 中導入的應用程式開發介面支援設計用來讀取或寫入資料到行事曆提供者的應用程式。 這些應用程式開發介面支援豐富的互動選項與行事曆資料，包括讀取和寫入事件、 出席者，以及提醒的能力。 使用行事曆提供者應用程式中，您將透過 API 的資料會出現在 Android 4 隨附內建的行事曆應用程式。

<a name="Adding_Permissions" />

## <a name="adding-permissions"></a>新增權限

使用新的行事曆應用程式開發介面應用程式中，的首先您必須進行時加入 Android 資訊清單中的適當的權限。 您要新增的權限`android.permisson.READ_CALENDAR`和`android.permission.WRITE_CALENDAR`，視是否您讀取和/或寫入行事曆資料而定。

<a name="Using_the_Calendar_Contract" />

## <a name="using-the-calendar-contract"></a>使用行事曆合約

一旦您設定權限，您可以使用與互動行事曆資料`CalendarContract`類別。 這個類別會提供應用程式可以使用行事曆提供者互動時的資料模型。 `CalendarContract`允許將行事曆的實體，例如行事曆與活動中解析 Uri 的應用程式。 它也會提供不同欄位的每個實體，例如行事曆的名稱和識別碼或事件的開始和結束日期與互動的方式。

讓我們看看使用行事曆 API 的範例。 在此範例中，我們將檢驗如何列舉行事曆和它們的事件，以及如何將新的事件新增至行事曆。

<a name="Listing_Calendars" />

## <a name="listing-calendars"></a>列出行事曆

首先，我們來討論如何列舉已註冊的行事曆應用程式中的日曆。 若要這樣做，我們可以具現化`CursorLoader`。 在 Android 3.0 (11 應用程式開發介面)，引進`CursorLoader`是慣用的方式取用`ContentProvider`。 最少，我們需要行事曆和我們想要傳回; 的資料行中指定的內容 Uri此資料行規格稱為_投影_。

呼叫`CursorLoader.LoadInBackground`方法可讓我們來查詢資料，例如行事曆提供者的內容提供者。
`LoadInBackground` 執行實際的載入作業，並傳回`Cursor`與查詢的結果。

`CalendarContract`協助我們指定這兩個內容`Uri`和投射。 若要取得內容`Uri`查詢行事曆，我們可以直接使用`CalendarContract.Calendars.ContentUri`屬性如下：

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

使用`CalendarContract`來指定哪個行事曆，我們想要的資料行也一樣簡單。 我們只要加入欄位`CalendarContract.Calendars.InterfaceConsts`陣列的類別。 例如，下列程式碼包括行事曆的 ID、 顯示名稱和帳戶名稱：

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

`Id`重要如果您使用包含`SimpleCursorAdapter`將資料繫結至 UI 中，我們將會很快看到。 使用內容的 Uri 和就地投影，我們具現化`CursorLoader`呼叫`CursorLoader.LoadInBackground`方法以傳回行事曆資料的資料指標，如下所示：

```csharp
var loader = new CursorLoader(this, calendarsUri, calendarsProjection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();

```

此範例中的使用者介面包含`ListView`，每個項目代表單一行事曆清單中。 下列 XML 顯示包含標記`ListView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="fill_parent">
  <ListView
    android:id="@android:id/android:list"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content" />
</LinearLayout>
```

此外，我們需要指定的 UI，針對每個清單項目，我們將放在個別的 XML 檔案，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="wrap_content">
  <TextView android:id="@+id/calDisplayName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="16dip" />
  <TextView android:id="@+id/calAccountName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="12dip" />
</LinearLayout>
```

從這裡開始，它是從資料指標將資料繫結至 UI 只標準 Android 程式碼。 我們將使用`SimpleCursorAdapter`，如下所示：

```csharp
string[] sourceColumns = {
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName };

int[] targetResources = {
    Resource.Id.calDisplayName, Resource.Id.calAccountName };      

SimpleCursorAdapter adapter = new SimpleCursorAdapter (this,
    Resource.Layout.CalListItem, cursor, sourceColumns, targetResources);

ListAdapter = adapter;
```

在上述程式碼中，配接器會在指定的資料行`sourceColumns`陣列，並將它們寫入中的使用者介面項目`targetResources`資料指標中每個行事曆項目陣列。 這裡使用的活動是子類別的`ListActivity`; 它包含`ListAdapter`我們設定的配接器的屬性。

以下是螢幕擷取畫面的最終結果，以顯示在行事曆資訊`ListView`:

[![在模擬器中，顯示兩個行事曆項目執行 CalendarDemo](calendar-images/11-calendar.png)](calendar-images/11-calendar.png)


<a name="Listing_Calendar_Events" />

## <a name="listing-calendar-events"></a>列出行事曆事件

下一步 讓我們看看如何列舉給定的行事曆事件。
建置在上述範例時，我們會提供當使用者選取其中一個行事曆事件的清單。 因此，我們必須處理在先前的程式碼中的項目選取項目：

```csharp
ListView.ItemClick += (sender, e) => {
    int i = (e as ItemEventArgs).Position;

    cursor.MoveToPosition(i);
    int calId =
        cursor.GetInt (cursor.GetColumnIndex (calendarsProjection [0]));

    var showEvents = new Intent(this, typeof(EventListActivity));
    showEvents.PutExtra("calId", calId);
    StartActivity(showEvents);
};
```

在此程式碼中，我們建立試圖開啟活動的型別`EventListActivity`，用意在傳遞行事曆的識別碼。 我們需要知道哪個行事曆要查詢事件識別碼。 在`EventListActivity`的`OnCreate`方法中，我們可以擷取來自識別碼`Intent`如下所示：

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

現在讓我們來查詢事件，此行事曆識別碼。 要查詢事件之處理序的方式很類似我們查詢的一份行事曆更早版本，不過此時我們將使用`CalendarContract.Events`類別。 下列程式碼會建立查詢以擷取的事件：

```csharp
var eventsUri = CalendarContract.Events.ContentUri;

string[] eventsProjection = {
    CalendarContract.Events.InterfaceConsts.Id,
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart
};

var loader = new CursorLoader(this, eventsUri, eventsProjection,
                   String.Format ("calendar_id={0}", _calId), null, "dtstart ASC");
var cursor = (ICursor)loader.LoadInBackground();
```

在此程式碼中，我們先取得內容`Uri`來自事件`CalendarContract.Events.ContentUri`屬性。 接著指定我們想要擷取 eventsProjection 陣列中的事件資料行。
最後，我們具現化`CursorLoader`呼叫載入器的這個資訊與`LoadInBackground`方法以傳回`Cursor`與事件資料。

若要在 UI 中顯示的事件資料，我們可以使用標記和程式碼，就像我們之前要顯示的曆法清單。 同樣地，我們使用`SimpleCursorAdapter`資料繫結至`ListView`如下列程式碼所示：

```csharp
string[] sourceColumns = {
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart };

int[] targetResources = {
    Resource.Id.eventTitle,
    Resource.Id.eventStartDate };

var adapter = new SimpleCursorAdapter (this, Resource.Layout.EventListItem,
    cursor, sourceColumns, targetResources);

adapter.ViewBinder = new ViewBinder ();       
ListAdapter = adapter;
```

此程式碼與我們先前用來顯示行事曆清單的程式碼之間的主要差異就是使用`ViewBinder`，方向會設在行：

```csharp
adapter.ViewBinder = new ViewBinder ();
```

`ViewBinder`類別可讓我們來進一步控制如何我們值繫結至檢視。 在此情況下，我們並用來將事件開始時間 （毫秒） 從轉換成日期字串，如下列的實作中所示：

```csharp
class ViewBinder : Java.Lang.Object, SimpleCursorAdapter.IViewBinder
{    
    public bool SetViewValue (View view, Android.Database.ICursor cursor,
        int columnIndex)
    {
        if (columnIndex == 2) {
            long ms = cursor.GetLong (columnIndex);

            DateTime date = new DateTime (1970, 1, 1, 0, 0, 0,
                DateTimeKind.Utc).AddMilliseconds (ms).ToLocalTime ();

            TextView textView = (TextView)view;
            textView.Text = date.ToLongDateString ();

            return true;
        }
        return false;
    }    
}
```

這會顯示事件的清單，如下所示：

[![顯示三個行事曆事件的範例應用程式的螢幕擷取畫面](calendar-images/12-events.png)](calendar-images/12-events.png)


<a name="Adding_a_Calendar_Event" />

## <a name="adding-a-calendar-event"></a>新增行事曆事件

我們已看到如何讀取行事曆資料。 現在我們來看看如何將事件新增至行事曆。 針對此目的，請務必包含`android.permission.WRITE_CALENDAR`我們先前所述的權限。 若要新增行事曆事件，我們將：

1.  建立`ContentValues`執行個體。
1.  使用中的索引鍵`CalendarContract.Events.InterfaceConsts`類別，以填入`ContentValues`執行個體。
1.  設定時區，為事件的開始和結束時間。
1.  使用`ContentResolver`插入行事曆事件資料。


下列程式碼將說明這些步驟：

```csharp
ContentValues eventValues = new ContentValues ();

eventValues.Put (CalendarContract.Events.InterfaceConsts.CalendarId,
    _calId);
eventValues.Put (CalendarContract.Events.InterfaceConsts.Title,
    "Test Event from M4A");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Description,
    "This is an event created from Xamarin.Android");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtstart,
    GetDateTimeMS (2011, 12, 15, 10, 0));
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtend,
    GetDateTimeMS (2011, 12, 15, 11, 0));

eventValues.Put(CalendarContract.Events.InterfaceConsts.EventTimezone,
    "UTC");
eventValues.Put(CalendarContract.Events.InterfaceConsts.EventEndTimezone,
    "UTC");

var uri = ContentResolver.Insert (CalendarContract.Events.ContentUri,
    eventValues);
```

請注意，如果我們未設定時區類型的例外狀況`Java.Lang.IllegalArgumentException`就會擲回。 事件時間的值必須以新紀元以來的毫秒為單位，因為我們建立`GetDateTimeMS`方法 (在`EventListActivity`) 來轉換毫秒格式我們日期規格：

```csharp
long GetDateTimeMS (int yr, int month, int day, int hr, int min)
{
    Calendar c = Calendar.GetInstance (Java.Util.TimeZone.Default);

    c.Set (Java.Util.CalendarField.DayOfMonth, 15);
    c.Set (Java.Util.CalendarField.HourOfDay, hr);
    c.Set (Java.Util.CalendarField.Minute, min);
    c.Set (Java.Util.CalendarField.Month, Calendar.December);
    c.Set (Java.Util.CalendarField.Year, 2011);

    return c.TimeInMillis;
}
```

如果我們將按鈕加入至 UI 的 [事件] 清單並執行上述程式碼中按鈕的 click 事件處理常式，加入行事曆事件，並更新清單中，如下所示：

[![使用行事曆事件後面加入範例事件按鈕的範例應用程式的螢幕擷取畫面](calendar-images/13.png)](calendar-images/13.png)

如果我們開啟行事曆應用程式時，我們會看到此事件會寫入那里以及：

[![顯示選取的行事曆事件的行事曆應用程式的螢幕擷取畫面](calendar-images/14.png)](calendar-images/14.png)

如您所見，Android 讓來擷取，並保存行事曆資料的功能強大且容易存取行事曆功能緊密整合的應用程式。


## <a name="related-links"></a>相關連結

- [行事曆示範 （範例）](https://developer.xamarin.com/samples/CalendarDemo/)
- [介紹的冰淇淋三明治](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
