---
title: 行事曆
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 115b5dbd0ec7093bf386e569aedfd0bafe5dc906
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827657"
---
# <a name="calendar"></a>行事曆


## <a name="calendar-api"></a>行事曆 API

一組新的行事曆 Android 4 中引入的 Api 支援設計用來讀取或寫入資料至行事曆提供者的應用程式。 這些 Api 支援豐富的行事曆資料，包括能夠讀取和寫入事件、 出席者，以及提醒互動選項。 在您的應用程式中使用的行事曆提供者，您將透過 API 的資料會出現在 Android 4 隨附內建的行事曆應用程式。


## <a name="adding-permissions"></a>新增權限

使用新的行事曆 Api 應用程式中，您只需要第一件事時，將適當的權限新增至 Android 資訊清單。 您要新增的權限`android.permisson.READ_CALENDAR`和`android.permission.WRITE_CALENDAR`，取決於是否您讀取和/或寫入的行事曆資料。


## <a name="using-the-calendar-contract"></a>使用行事曆合約

一旦您設定的權限，您可以使用互動使用行事曆資料`CalendarContract`類別。 這個類別提供的資料模型的互動與行事曆提供者時，可以使用應用程式。 `CalendarContract`允許將行事曆的實體，例如行事曆與活動中解析 Uri 的應用程式。 它也會提供不同欄位的每個實體，例如行事曆的名稱和識別碼或事件的開始和結束日期與互動的方式。

讓我們看看使用行事曆 API 的範例。 在此範例中，我們將檢驗如何列舉行事曆和它們的事件，以及如何將新事件新增至行事曆。


## <a name="listing-calendars"></a>列出行事曆

首先，讓我們檢查如何列舉已註冊的行事曆應用程式中的行事曆。 若要這樣做，我們可以具現化`CursorLoader`。 在 Android 3.0 (API 11) 中引進`CursorLoader`是慣用的方式取用`ContentProvider`。 在最少，我們必須指定行事曆和我們想要傳回; 的資料行的內容 Uri此資料行規格稱為_投影_。

呼叫`CursorLoader.LoadInBackground`方法可讓我們來查詢資料，例如行事曆提供者的內容提供者。
`LoadInBackground` 執行實際的載入作業，並傳回`Cursor`查詢的結果。

`CalendarContract`可協助我們指定兩個內容`Uri`和投影。 若要取得內容`Uri`查詢行事曆，我們可以直接使用`CalendarContract.Calendars.ContentUri`屬性如下：

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

使用`CalendarContract`的行事曆的指定我們想要的資料行也一樣簡單。 我們只要在增加中的欄位`CalendarContract.Calendars.InterfaceConsts`陣列的類別。 例如，下列程式碼包含行事曆的識別碼、 顯示名稱和帳戶名稱：

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

`Id`如果您使用包含重要`SimpleCursorAdapter`資料繫結到 UI 中，我們會在短時間內看到。 使用內容的 Uri 和投影就地，我們將執行個體化`CursorLoader`並呼叫`CursorLoader.LoadInBackground`方法來傳回行事曆資料的資料指標，如下所示：

```csharp
var loader = new CursorLoader(this, calendarsUri, calendarsProjection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();

```

此範例中的 UI 包含`ListView`，每個項目代表單一行事曆清單中。 下列 XML 會顯示包含標記`ListView`:

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

此外，我們需要指定每個清單項目，我們將放在個別的 XML 檔案，如下所示的 UI:

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

從這裡開始，就只是一般的 Android 程式碼，從資料指標將資料繫結至 UI。 我們將使用`SimpleCursorAdapter`，如下所示：

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

在上述程式碼中，配接器會在指定的資料行`sourceColumns`陣列，並將它們寫入中的使用者介面項目`targetResources`資料指標中每個行事曆項目陣列。 此處所使用的活動是子類別的`ListActivity`; 它包含`ListAdapter`我們將設定配接器的屬性。

以下是螢幕擷取畫面顯示最後的結果，使用中顯示的行事曆資訊`ListView`:

[![在模擬器中執行的 CalendarDemo，顯示兩個行事曆項目](calendar-images/11-calendar.png)](calendar-images/11-calendar.png#lightbox)



## <a name="listing-calendar-events"></a>列出行事曆事件

下一步 讓我們看看如何列舉指定的行事曆的事件。
建置在上述範例中，我們將會呈現事件，當使用者選取其中一個行事曆的清單。 因此，我們需要處理先前的程式碼中的項目選取項目：

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

在此程式碼中，我們要建立之意圖開啟類型的活動`EventListActivity`，用意在傳遞行事曆的識別碼。 我們必須要知道的行事曆進行事件查詢的識別碼。 在 `EventListActivity`的`OnCreate`方法中，我們可以擷取來自識別碼`Intent`，如下所示：

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

現在讓我們為此查詢事件行事曆識別碼。 查詢事件的程序是類似於我們稍早的行事曆的清單中查詢的方式只是我們將使用這次`CalendarContract.Events`類別。 下列程式碼會建立查詢來擷取事件：

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

在此程式碼中，我們先取得內容`Uri`從事件`CalendarContract.Events.ContentUri`屬性。 然後，我們會指定我們想要擷取 eventsProjection 陣列中的事件資料行。
最後，我們將執行個體化`CursorLoader`呼叫的載入器的此資訊與`LoadInBackground`方法來傳回`Cursor`與事件資料。

若要在 UI 中顯示的事件資料，我們可以使用標記和程式碼，就像我們之前顯示行事曆的清單。 同樣地，我們使用`SimpleCursorAdapter`若要將資料繫結`ListView`如下列程式碼所示：

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

此程式碼以及我們稍早用來顯示行事曆清單的程式碼的主要差異是使用`ViewBinder`，這在該行設定：

```csharp
adapter.ViewBinder = new ViewBinder ();
```

`ViewBinder`類別可讓我們來進一步控制如何我們將值繫結至檢視時。 在此情況下，我們使用它從毫秒為單位的事件開始時間轉換為日期字串，如下列實作中所示：

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

[![顯示三個行事曆事件的範例應用程式的螢幕擷取畫面](calendar-images/12-events.png)](calendar-images/12-events.png#lightbox)



## <a name="adding-a-calendar-event"></a>加入行事曆事件

我們已了解如何讀取行事曆資料。 現在我們來看看如何將事件新增至行事曆。 針對此目的，請務必包含`android.permission.WRITE_CALENDAR`我們先前所述的權限。 若要加入行事曆事件，我們將會：

1.  建立`ContentValues`執行個體。
1.  從使用金鑰`CalendarContract.Events.InterfaceConsts`類別，以填入`ContentValues`執行個體。
1.  設定時區，為事件的開始和結束時間。
1.  使用`ContentResolver`插入行事曆中的事件資料。


下列程式碼說明這些步驟：

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

請注意，如果我們不會設定時區，類型的例外狀況`Java.Lang.IllegalArgumentException`就會擲回。 因為事件的時間值必須以毫秒為單位，以新紀元以來，我們會建立`GetDateTimeMS`方法 (在`EventListActivity`) 來轉換我們的日期規格毫秒格式：

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

如果我們將按鈕加入至 UI 的 [事件] 清單，並執行上述程式碼中按鈕的 click 事件處理常式，加入行事曆事件，並更新我們的清單中，如下所示：

[![使用行事曆事件後面加入範例事件按鈕的範例應用程式的螢幕擷取畫面](calendar-images/13.png)](calendar-images/13.png#lightbox)

如果我們開啟行事曆應用程式時，我們會看到該事件會寫入那里以及：

[![顯示所選行事曆事件的行事曆應用程式的螢幕擷取畫面](calendar-images/14.png)](calendar-images/14.png#lightbox)

如您所見，Android 可讓為了擷取和保存行事曆資料的功能強大且容易存取應用程式順暢地整合 行事曆功能。


## <a name="related-links"></a>相關連結

- [行事曆示範 （範例）](https://developer.xamarin.com/samples/monodroid/CalendarDemo/)
- [簡介 Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](https://developer.android.com/sdk/android-4.0.html)
