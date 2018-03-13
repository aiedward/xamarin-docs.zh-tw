---
title: ListView
description: "ListView 是重要的 UI 元件的 Android 應用程式它用於所有位置的功能表選項的簡短清單中較長的連絡人或網際網路我的最愛清單。 它提供簡單的方式來呈現捲動的資料列，可以使用內建樣式格式化或自訂的廣泛清單。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 2d5a83b9f6278406e9b643277357df253f5fd524
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="listview"></a>ListView

_ListView 是重要的 UI 元件的 Android 應用程式它用於所有位置的功能表選項的簡短清單中較長的連絡人或網際網路我的最愛清單。它提供簡單的方式來呈現捲動的資料列，可以使用內建樣式格式化或自訂的廣泛清單。_


## <a name="overview"></a>總覽

清單檢視和配接器隨附的最基本的 Android 應用程式的建置組塊。 `ListView`類別會提供具彈性的方式呈現資料，不論是簡短的功能表或捲動清單。 它提供可用性功能，例如快速捲動、 索引以及可協助您建置您的應用程式的行動設備友善的使用者介面的單一或多個選取範圍。 `ListView` 執行個體需要 *Adapter* 提供資料列檢視中所含的資料給它。

本指南說明如何實作`ListView`各種`Adapter`Xamarin.Android 中的類別。 它也會示範如何自訂的外觀`ListView`，以及它討論的資料列重複用來降低記憶體耗用量的重要性。 另外還有一些討論的活動生命週期如何影響`ListView`和`Adapter`使用。 如果您正在 Xamarin.iOS，與跨平台應用程式上`ListView`控制項是結構上類似於 iOS `UITableView` (和 Android`Adapter`類似於`UITableViewSource`)。

首先，簡短的教學課程介紹`ListView`基本程式碼範例。 接下來，會提供更進階的主題連結，協助您使用`ListView`真實世界應用程式中。


> [!NOTE]
> `RecyclerView` Widget 是更進階且彈性的版本`ListView`。 因為`RecyclerView`設計為後續`ListView`(和`GridView`)，我們建議您改用`RecyclerView`而不是`ListView`開發新應用程式。 如需詳細資訊，請參閱[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)。



## <a name="listview-tutorial"></a>ListView 教學課程

[`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) 是[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)會建立可捲動的項目清單。 清單項目會自動插入清單使用[ `IListAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.IListAdapter/)。

在本教學課程中，您將建立可捲動的讀取自字串陣列的國家/地區名稱清單。 選取清單項目時，快顯通知訊息會在清單中顯示項目的位置。


啟動新的專案，名為**HelloListView**。

建立名為 XML 檔案**list_item.xml**並將其內部儲存**資源/配置/**資料夾。 插入下列：

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

此檔案會定義每個項目會放在配置[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)。

開啟`HelloListView.cs`並使該類別成為擴充[ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/) (而不是[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)):

```csharp
public class HelloListView : ListActivity
{
```

插入下列程式碼[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);

    ListView.TextFilterEnabled = true;

    ListView.ItemClick += delegate (object sender, ItemEventArgs args) {
        // When clicked, show a toast with the TextView text
        Toast.MakeText (Application, ((TextView)args.View).Text, ToastLength.Short).Show ();
    };
}
```

請注意這不會載入配置檔案 」 活動 (這通常與[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)))。
相反地，設定[ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)屬性會自動新增[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)填滿整個螢幕的[ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/)。
這個方法會採用[ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)，它負責管理將放入的清單項目陣列[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)。
[ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)建構函式會採用應用程式[ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/)，每個清單項目 （在上一個步驟中建立） 的配置描述和`T[]`或[`Java.Util.IList<T>` ](https://developer.xamarin.com/api/type/Java.Util.IList/)要插入的物件陣列[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) （下一步定義）。

[ `TextFilterEnabled` ](https://developer.xamarin.com/api/property/Android.Widget.AbsListView.TextFilterEnabled/)屬性會開啟文字篩選[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)，如此一來，當使用者開始輸入時，會篩選清單。

[ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)事件可以用來訂閱按一下處理常式。 中的項目時[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)是按下，會呼叫處理常式和[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)會顯示訊息，使用按一下的項目中的文字。

您可以使用而不是定義您自己的配置檔案。 平台所提供的清單項目設計[ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)。
例如，請嘗試使用`Android.Resource.Layout.SimpleListItem1`而不是`Resource.Layout.list_item`。

之後[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))方法，將字串陣列：

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

這是字串陣列，將會放置到[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)。

執行應用程式。 您可以捲動清單，或輸入以篩選，然後按一下以查看訊息的項目。 您應該會看到類似下面的內容：

[![範例的螢幕擷取畫面 ListView 國家 （地區） 名稱](images/helloviews6.png)](images/helloviews6.png#lightbox)

請注意，使用硬式編碼的字串陣列不是最佳的設計作法。 使用其中一種是在為了簡單起見，本教學課程示範[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) widget。 更好的作法是為參考外部資源，例如由包含所定義的字串陣列`string-array`專案中的資源**Resources/Values/Strings.xml**檔案。 例如: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
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

若要使用這些資源字串[ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)，取代原始[ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)為下列行：

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```


## <a name="going-further-with-listview"></a>繼續進行的 ListView

（依以下的連結） 的其他主題查看全方位使用`ListView`類別和不同類型的配接器類型，您可以使用它。 結構如下所示：

-   **視覺外觀**&ndash;部分`ListView`控制項以及它們如何運作。

-   **類別**&ndash;用來顯示類別概觀`ListView`。

-   **在 ListView 中顯示資料**&ndash;如何顯示資料的簡單清單; 如何實作`ListView's`可用性功能; 若要使用不同的內建資料列配置; 以及配接器重新使用資料列檢視所節省的記憶體。

-   **自訂外觀**&ndash;變更的樣式`ListView`自訂版面配置、 字型與色彩。

-   **使用 SQLite** &ndash;如何顯示 SQLite 資料庫資料的`CursorAdapter`。

-   **活動的生命週期**&ndash;實作時，設計考量`ListView`活動，包括其中生命週期中您應填入您的資料，以及何時釋放資源。

討論 （分為六個部分） 開頭的概觀`ListView`類別本身之前將它變得更複雜的範例，說明如何使用它。

-   [ListView 組件和功能](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [填入資料的 ListView](~/android/user-interface/layouts/list-view/populating.md)
-   [自訂 ListView 的外觀](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [使用 CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [使用 ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView 和活動生命週期](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>總結

這組主題導入`ListView`並提供如何使用內建功能的一些範例`ListActivity`。 它所討論的自訂實作`ListView`所允許的色彩配置，並使用 SQLite 資料庫，並簡要接觸到的活動生命週期相關性您`ListView`實作。


## <a name="related-links"></a>相關連結

- [AccessoryViews （範例）](https://developer.xamarin.com/samples/AccessoryViews/)
- [BasicTableAndroid （範例）](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (sample)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [BuiltInViews （範例）](https://developer.xamarin.com/samples/BuiltInViews/)
- [CustomRowView （範例）](https://developer.xamarin.com/samples/CustomRowView/)
- [FastScroll （範例）](https://developer.xamarin.com/samples/FastScroll/)
- [SectionIndex （範例）](https://developer.xamarin.com/samples/SectionIndex/)
- [SimpleCursorTableAdapter （範例）](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter （範例）](https://developer.xamarin.com/samples/CursorTableAdapter/)
- [活動生命週期教學課程](~/android/app-fundamentals/activity-lifecycle/index.md)
- [處理資料表和資料格 （在 Xamarin.iOS)](~/ios/user-interface/controls/tables/index.md)
- [ListView 類別參考](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [ListActivity 類別參考](https://developer.xamarin.com/api/type/Android.App.ListActivity/)
- [BaseAdapter 類別參考](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
- [ArrayAdapter 類別參考](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
- [CursorAdapter 類別參考](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
