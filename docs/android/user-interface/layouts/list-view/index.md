---
title: 在 Xamarin.Android 中使用的 ListView
description: ListView 是重要的 UI 元件的 Android 應用程式;它用於所有位置的功能表選項的簡短清單中較長的連絡人或網際網路我的最愛清單。 它提供簡單的方式來呈現的資料列，可以使用內建樣式格式化或廣泛地自訂捲動清單。
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: a30256722647bbea482970d0c4a751954810d99e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122643"
---
# <a name="listview"></a>ListView

_ListView 是重要的 UI 元件的 Android 應用程式;它用於所有位置的功能表選項的簡短清單中較長的連絡人或網際網路我的最愛清單。它提供簡單的方式來呈現的資料列，可以使用內建樣式格式化或廣泛地自訂捲動清單。_


## <a name="overview"></a>總覽

清單檢視和配接器包括在 Android 應用程式的最基本建置組塊。 `ListView`類別提供一種彈性的方式來呈現資料，無論是簡短的功能表或長時間捲動清單。 它提供快速捲動、 索引等可協助您建置應用程式的行動設備友善的使用者介面的單一或多個選取的可用性功能。 `ListView` 執行個體需要 *Adapter* 提供資料列檢視中所含的資料給它。

本指南說明如何實作`ListView`和各種`Adapter`在 Xamarin.Android 中的類別。 它也會示範如何自訂外觀`ListView`，以及重新使用中的資料列，以降低記憶體耗用量的重要性。 另外還有一些討論活動開發週期會如何影響`ListView`和`Adapter`使用。 如果您正在使用 Xamarin.iOS，跨平台應用程式`ListView`控制項是結構上類似於 iOS `UITableView` (和 Android`Adapter`類似於`UITableViewSource`)。

首先，簡短的教學課程介紹`ListView`基本程式碼範例。 接下來，會提供更進階的主題連結，協助您使用`ListView`真實世界應用程式中。


> [!NOTE]
> `RecyclerView`小工具是更進階且彈性的版本`ListView`。 因為`RecyclerView`設計為以後續`ListView`(並`GridView`)，我們建議您改用`RecyclerView`而非`ListView`開發新應用程式。 如需詳細資訊，請參閱 < [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)。



## <a name="listview-tutorial"></a>ListView 教學課程

[`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) 是 [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
這樣將會產生一份可捲動的項目。 清單項目會自動插入至清單中使用[ `IListAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.IListAdapter/)。

在本教學課程中，您將建立可捲動所讀取的字串陣列的國家/地區名稱清單。 選取清單項目時，快顯通知訊息會在清單中顯示項目的位置。


開始新的專案，名為**HelloListView**。

建立名為 XML 檔案**list_item.xml**並將其內部儲存**資源/配置/** 資料夾。 插入下列：

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

這個檔案會定義每個項目會放在版面配置[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)。

開啟`MainActivity.cs`並修改類別，以擴充[ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/) (而非[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)):

```csharp
public class MainActivity : ListActivity
{
```

插入下列程式碼 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);

    ListView.TextFilterEnabled = true;

    ListView.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args)
    {
        Toast.MakeText(Application, ((TextView)args.View).Text, ToastLength.Short).Show();
    };
}
```

請注意這不會載入版面配置檔活動 (這通常是處理[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)))。
相反地，設定 [`ListAdapter`](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)
屬性會自動新增 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
以填滿整個畫面[ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/)。
這個方法會採用[ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)，其可管理的清單項目會放入陣列[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)。
的 [`ArrayAdapter<T>`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)
建構函式應用程式[ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/)，每個清單項目 （在上一個步驟中建立） 的配置描述和`T[]`或 [`Java.Util.IList<T>`](https://developer.xamarin.com/api/type/Java.Util.IList/)
要插入的物件陣列 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
（下一步定義）。

的 [`TextFilterEnabled`](https://developer.xamarin.com/api/property/Android.Widget.AbsListView.TextFilterEnabled/)
屬性會開啟文字篩選[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)，如此一來，當使用者開始輸入時，會篩選清單。

的 [`ItemClick`](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)
事件可以用來訂閱點擊處理常式中。 中的項目 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
已按下，會呼叫處理常式， [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
會顯示訊息，使用已按下的項目中的文字。

您可以使用而不是定義您自己的版面配置檔的平台所提供的清單項目設計[ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)。
例如，請嘗試使用`Android.Resource.Layout.SimpleListItem1`而不是`Resource.Layout.list_item`。

新增下列`using`陳述式：

```csharp
using System;
```
接下來，新增下列字串陣列，成員的身分`MainActivity`:

```csharp
static readonly string[] countries = new String[] {
    "Afghanistan","Albania","Algeria","American Samoa","Andorra",
    "Angola","Anguilla","Antarctica","Antigua and Barbuda","Argentina",
    "Armenia","Aruba","Australia","Austria","Azerbaijan",
    "Bahrain","Bangladesh","Barbados","Belarus","Belgium",
    "Belize","Benin","Bermuda","Bhutan","Bolivia",
    "Bosnia and Herzegovina","Botswana","Bouvet Island","Brazil","British Indian Ocean Territory",
    "British Virgin Islands","Brunei","Bulgaria","Burkina Faso","Burundi",
    "Cote d'Ivoire","Cambodia","Cameroon","Canada","Cape Verde",
    "Cayman Islands","Central African Republic","Chad","Chile","China",
    "Christmas Island","Cocos (Keeling) Islands","Colombia","Comoros","Congo",
    "Cook Islands","Costa Rica","Croatia","Cuba","Cyprus","Czech Republic",
    "Democratic Republic of the Congo","Denmark","Djibouti","Dominica","Dominican Republic",
    "East Timor","Ecuador","Egypt","El Salvador","Equatorial Guinea","Eritrea",
    "Estonia","Ethiopia","Faeroe Islands","Falkland Islands","Fiji","Finland",
    "Former Yugoslav Republic of Macedonia","France","French Guiana","French Polynesia",
    "French Southern Territories","Gabon","Georgia","Germany","Ghana","Gibraltar",
    "Greece","Greenland","Grenada","Guadeloupe","Guam","Guatemala","Guinea","Guinea-Bissau",
    "Guyana","Haiti","Heard Island and McDonald Islands","Honduras","Hong Kong","Hungary",
    "Iceland","India","Indonesia","Iran","Iraq","Ireland","Israel","Italy","Jamaica",
    "Japan","Jordan","Kazakhstan","Kenya","Kiribati","Kuwait","Kyrgyzstan","Laos",
    "Latvia","Lebanon","Lesotho","Liberia","Libya","Liechtenstein","Lithuania","Luxembourg",
    "Macau","Madagascar","Malawi","Malaysia","Maldives","Mali","Malta","Marshall Islands",
    "Martinique","Mauritania","Mauritius","Mayotte","Mexico","Micronesia","Moldova",
    "Monaco","Mongolia","Montserrat","Morocco","Mozambique","Myanmar","Namibia",
    "Nauru","Nepal","Netherlands","Netherlands Antilles","New Caledonia","New Zealand",
    "Nicaragua","Niger","Nigeria","Niue","Norfolk Island","North Korea","Northern Marianas",
    "Norway","Oman","Pakistan","Palau","Panama","Papua New Guinea","Paraguay","Peru",
    "Philippines","Pitcairn Islands","Poland","Portugal","Puerto Rico","Qatar",
    "Reunion","Romania","Russia","Rwanda","Sqo Tome and Principe","Saint Helena",
    "Saint Kitts and Nevis","Saint Lucia","Saint Pierre and Miquelon",
    "Saint Vincent and the Grenadines","Samoa","San Marino","Saudi Arabia","Senegal",
    "Seychelles","Sierra Leone","Singapore","Slovakia","Slovenia","Solomon Islands",
    "Somalia","South Africa","South Georgia and the South Sandwich Islands","South Korea",
    "Spain","Sri Lanka","Sudan","Suriname","Svalbard and Jan Mayen","Swaziland","Sweden",
    "Switzerland","Syria","Taiwan","Tajikistan","Tanzania","Thailand","The Bahamas",
    "The Gambia","Togo","Tokelau","Tonga","Trinidad and Tobago","Tunisia","Turkey",
    "Turkmenistan","Turks and Caicos Islands","Tuvalu","Virgin Islands","Uganda",
    "Ukraine","United Arab Emirates","United Kingdom",
    "United States","United States Minor Outlying Islands","Uruguay","Uzbekistan",
    "Vanuatu","Vatican City","Venezuela","Vietnam","Wallis and Futuna","Western Sahara",
    "Yemen","Yugoslavia","Zambia","Zimbabwe"
  };
```

這是字串陣列，將會置於[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)。

執行應用程式。 您可以捲動清單，或鍵入以篩選，然後按一下的項目，就會看見訊息。 您應該會看到類似下面的內容：

[![範例螢幕擷取畫面的 ListView 國家/地區名稱](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

請注意，使用硬式編碼的字串陣列，不是最佳設計做法。 其中一個用來示範在為了簡單起見，本教學課程 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
小工具。 比較好的作法是參考所定義的外部資源，例如字串陣列`string-array`專案中的資源**Resources/Values/Strings.xml**檔案。 例如: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloListView</string>
  <string-array name="countries_array">
    <item>Bahrain</item>
    <item>Bangladesh</item>
    <item>Barbados</item>
    <item>Belarus</item>
    <item>Belgium</item>
    <item>Belize</item>
    <item>Benin</item>
  </string-array>
</resources>
```

若要使用這些資源字串[ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)，取代原始 [`ListAdapter`](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)
取代為下列一行：

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```
執行應用程式。 您應該會看到類似下面的內容：

[![範例的螢幕擷取畫面 ListView 較小的名稱清單](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)


## <a name="going-further-with-listview"></a>繼續進行與 ListView

（連結） 的其他主題需要使用的完整介紹`ListView`類別和不同類型的配接器類型，您可以使用它。 結構如下所示：

-   **視覺外觀**&ndash;部份`ListView`控制項和其運作方式。

-   **類別**&ndash;用來顯示類別的概觀`ListView`。

-   **在 ListView 中顯示資料**&ndash;如何顯示資料的簡單清單; 如何實作`ListView's`可用性功能; 如何使用不同的內建資料列配置; 以及配接器重複使用資料列檢視所節省的記憶體。

-   **自訂外觀**&ndash;變更的樣式`ListView`自訂版面配置、 字型與色彩。

-   **使用 SQLite** &ndash;如何顯示與 SQLite 資料庫中的資料`CursorAdapter`。

-   **活動生命週期**&ndash;時實作的設計考量`ListView`活動，包括其中生命週期中您應填入您的資料，以及何時釋放資源。

討論 （分為六個的部分） 開始的概觀`ListView`類別本身之前越來越複雜範例，示範如何使用它。

-   [ListView 組件和功能](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [填入資料的 ListView](~/android/user-interface/layouts/list-view/populating.md)
-   [自訂 ListView 的外觀](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [使用 CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [使用 ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView 和活動生命週期](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>總結

這組主題引進`ListView`並提供如何使用內建功能的一些範例`ListActivity`。 它討論的自訂實作`ListView`允許彩色的配置，並且使用 SQLite 資料庫，並簡要觸及上活動開發週期的相關性您`ListView`實作。


## <a name="related-links"></a>相關連結

- [AccessoryViews （範例）](https://developer.xamarin.com/samples/AccessoryViews/)
- [BasicTableAndroid （範例）](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter （範例）](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [BuiltInViews （範例）](https://developer.xamarin.com/samples/BuiltInViews/)
- [CustomRowView （範例）](https://developer.xamarin.com/samples/CustomRowView/)
- [FastScroll （範例）](https://developer.xamarin.com/samples/FastScroll/)
- [SectionIndex （範例）](https://developer.xamarin.com/samples/SectionIndex/)
- [SimpleCursorTableAdapter （範例）](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter （範例）](https://developer.xamarin.com/samples/CursorTableAdapter/)
- [活動生命週期教學課程](~/android/app-fundamentals/activity-lifecycle/index.md)
- [使用資料表和資料格中 （Xamarin.iOS)](~/ios/user-interface/controls/tables/index.md)
- [ListView 類別參考](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [ListActivity 類別參考](https://developer.xamarin.com/api/type/Android.App.ListActivity/)
- [BaseAdapter 類別參考](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
- [ArrayAdapter 類別參考](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
- [CursorAdapter 類別參考](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
