---
title: Xamarin. Android 行事曆
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: d0cd17f424426d326a3e53f0c289fc72068bb3ef
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457272"
---
# <a name="xamarinandroid-calendar"></a>Xamarin. Android 行事曆

## <a name="calendar-api"></a>行事曆 API

Android 4 中引進的一組新行事曆 Api 支援設計用來讀取或寫入資料給行事曆提供者的應用程式。 這些 Api 支援豐富的行事歷數據互動選項，包括讀取和寫入活動、出席者和提醒的能力。 藉由在應用程式中使用行事曆提供者，您透過 API 新增的資料將會出現在隨附于 Android 4 的內建行事曆應用程式中。

## <a name="adding-permissions"></a>新增許可權

在您的應用程式中使用新的行事曆 Api 時，您必須做的第一件事是將適當的許可權新增至 Android 資訊清單。 您需要新增的許可權是 `android.permisson.READ_CALENDAR` 和 `android.permission.WRITE_CALENDAR` ，取決於您是否正在讀取和/或寫入行事歷數據。

## <a name="using-the-calendar-contract"></a>使用行事曆合約

設定許可權之後，您可以使用類別與行事歷數據互動 `CalendarContract` 。 此類別提供應用程式與行事曆提供者互動時可使用的資料模型。 `CalendarContract`可讓應用程式將 uri 解析為行事曆實體，例如行事曆和事件。 它也提供一種方式，讓您能夠與每個實體中的各種欄位互動，例如行事曆的名稱和識別碼，或是事件的開始和結束日期。

讓我們看看使用行事曆 API 的範例。 在此範例中，我們將探討如何列舉行事曆和其事件，以及如何將新事件加入行事曆。

## <a name="listing-calendars"></a>列出行事曆

首先，讓我們來看看如何列舉行事曆應用程式中已註冊的行事曆。 若要這樣做，我們可以具現化 `CursorLoader` 。 在 Android 3.0 (API 11) 中引進， `CursorLoader` 是使用的慣用方式 `ContentProvider` 。 我們至少需要為行事曆和我們想要傳回的資料行指定內容 Uri。此資料行規格稱為 _投射_。

呼叫 `CursorLoader.LoadInBackground` 方法可讓我們查詢內容提供者的資料，例如行事曆提供者。
`LoadInBackground` 執行實際的載入作業，並傳回 `Cursor` 具有查詢結果的。

可 `CalendarContract` 協助我們同時指定內容 `Uri` 和投射。 若要取得查詢行事曆的內容 `Uri` ，我們可以直接使用 `CalendarContract.Calendars.ContentUri` 屬性，如下所示：

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

使用 `CalendarContract` 來指定我們想要的行事歷數據行一樣簡單。 我們只是將類別中的欄位加入 `CalendarContract.Calendars.InterfaceConsts` 至陣列。 例如，下列程式碼包含行事曆的識別碼、顯示名稱和帳戶名稱：

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

`Id`如果您要使用將資料系結 `SimpleCursorAdapter` 至 UI，請務必要包含這些資料，如我們稍後所見。 在內容 Uri 和投射就緒的情況下，我們會具現化， `CursorLoader` 並呼叫 `CursorLoader.LoadInBackground` 方法，以傳回具有行事歷數據的資料指標，如下所示：

```csharp
var loader = new CursorLoader(this, calendarsUri, calendarsProjection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();

```

此範例的 UI 包含 `ListView` ，其中清單中的每個專案代表一個行事曆。 下列 XML 會顯示包含下列各項的標記 `ListView` ：

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

此外，我們還需要指定每個清單專案的 UI，我們會將其放在個別的 XML 檔案中，如下所示：

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

從此時開始，它就是將資料從資料指標系結至 UI 的一般 Android 程式碼。 我們將使用 `SimpleCursorAdapter` ，如下所示：

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

在上述程式碼中，介面卡會採用陣列中所指定的資料行 `sourceColumns` ，並將其寫入至資料 `targetResources` 指標中每個行事曆專案陣列中的使用者介面元素。 此處使用的活動是的子類別， `ListActivity` 它包含 `ListAdapter` 我們設定介面卡的屬性。

以下是顯示最終結果的螢幕擷取畫面，其中顯示行事曆資訊 `ListView` ：

[![在模擬器中執行的 CalendarDemo，顯示兩個行事曆專案](calendar-images/11-calendar.png)](calendar-images/11-calendar.png#lightbox)

## <a name="listing-calendar-events"></a>列出行事曆事件

接下來讓我們看看如何列舉特定行事曆的事件。
根據上述範例建立時，我們會在使用者選取其中一個行事歷時，顯示事件的清單。 因此，我們必須處理先前程式碼中的專案選取專案：

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

在此程式碼中，我們會建立一個意圖來開啟類型的活動 `EventListActivity` ，並在意圖中傳遞行事曆的識別碼。 我們需要識別碼才能知道要查詢事件的行事曆。 在的 `EventListActivity` `OnCreate` 方法中，我們可以取出的識別碼， `Intent` 如下所示：

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

現在讓我們來查詢此行事曆識別碼的事件。 查詢事件的程式與我們稍早查詢行事曆清單的方式類似，只有這次我們會使用 `CalendarContract.Events` 類別。 下列程式碼會建立查詢來取出事件：

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

在此程式碼中，我們會先 `Uri` 從屬性取得事件的內容 `CalendarContract.Events.ContentUri` 。 接著，我們會在 eventsProjection 陣列中指定要取出的事件資料行。
最後，我們會 `CursorLoader` 使用此資訊來具現化，並呼叫載入器的 `LoadInBackground` 方法，以傳回 `Cursor` 具有事件資料的。

若要在 UI 中顯示事件資料，我們可以使用標記和程式碼，就像我們之前所做的一樣，以顯示行事曆清單。 同樣地，我們會使用 `SimpleCursorAdapter` 將資料系結至， `ListView` 如下列程式碼所示：

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

這段程式碼與先前用來顯示行事曆清單的程式碼之間的主要差異，是使用在 `ViewBinder` 該行上設定的：

```csharp
adapter.ViewBinder = new ViewBinder ();
```

`ViewBinder`類別可讓我們進一步控制將值系結至視圖的方式。 在此情況下，我們會使用它來將事件開始時間從毫秒轉換為日期字串，如下列實作為所示：

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

這會顯示事件清單，如下所示：

[![顯示三個行事曆事件的範例應用程式螢幕擷取畫面](calendar-images/12-events.png)](calendar-images/12-events.png#lightbox)

## <a name="adding-a-calendar-event"></a>新增行事曆事件

我們已瞭解如何讀取行事歷數據。 現在讓我們來看看如何將事件加入行事曆。 若要這樣做，請務必包含我們稍 `android.permission.WRITE_CALENDAR` 早所述的許可權。 若要將事件加入行事曆，我們將：

1. 建立  `ContentValues` 實例。
1. 使用類別中的索引鍵  `CalendarContract.Events.InterfaceConsts` 來填入  `ContentValues` 實例。
1. 設定事件開始和結束時間的時區。
1. 使用將  `ContentResolver` 事件資料插入行事曆。

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

請注意，如果我們沒有設定時區，則會擲回類型的例外狀況 `Java.Lang.IllegalArgumentException` 。 由於事件時間值必須以毫秒為單位來表示，因為 epoch 之後，我們會 `GetDateTimeMS` 在) 中建立方法 (，將 `EventListActivity` 我們的日期規格轉換成毫秒格式：

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

如果我們將按鈕新增至事件清單 UI，然後在按鈕的 click 事件處理常式中執行上述程式碼，則會將該事件新增至行事曆，並在清單中更新，如下所示：

[![範例應用程式的螢幕擷取畫面，其中包含行事曆事件，後面接著 [新增範例事件] 按鈕](calendar-images/13.png)](calendar-images/13.png#lightbox)

如果我們開啟行事曆應用程式，則會看到事件也寫在那裡：

[![顯示所選行事曆事件的行事曆應用程式螢幕擷取畫面](calendar-images/14.png)](calendar-images/14.png#lightbox)

如您所見，Android 可讓您以強大且輕鬆的方式取得和保存行事歷數據，讓應用程式能夠順暢地整合行事曆功能。

## <a name="related-links"></a>相關連結

- [行事曆示範 (範例) ](/samples/xamarin/monodroid-samples/calendardemo)