---
title: 在 Xamarin 中使用 ListView
description: ListView 是 Android 應用程式的重要 UI 元件;其使用方式是從功能表選項的簡短清單, 到長清單的連絡人或網際網路我的最愛。 它提供了一種簡單的方式來呈現資料列的滾動清單, 您可以使用內建樣式或廣泛自訂來進行格式化。
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 653b6e3d41f9acb4ee3e9ee3626e72220687ccb8
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510080"
---
# <a name="xamarinandroid-listview"></a>Xamarin. Android ListView

_ListView 是 Android 應用程式的重要 UI 元件;其使用方式是從功能表選項的簡短清單, 到長清單的連絡人或網際網路我的最愛。它提供了一種簡單的方式來呈現資料列的滾動清單, 您可以使用內建樣式或廣泛自訂來進行格式化。_

## <a name="overview"></a>總覽

清單視圖和介面卡包含在 Android 應用程式的最基本組建區塊中。 `ListView`類別提供彈性的方式來呈現資料, 不論是快顯功能表或長時間滾動清單。 它提供快速滾動、索引和單一或多重選取等可用性功能, 可協助您為應用程式建立方便使用的使用者介面。 `ListView` 執行個體需要 *Adapter* 提供資料列檢視中所含的資料給它。

本指南說明如何在 Xamarin `ListView`中執行和`Adapter`各種類別。 它也會示範如何自訂的外觀`ListView`, 並討論資料列重複使用來減少記憶體耗用量的重要性。 此外, 也會討論活動生命週期的影響`ListView`和`Adapter`使用方式。 如果您在使用 Xamarin 的跨平臺應用程式, 則控制項`ListView`的結構類似于 ios `UITableView` (而`UITableViewSource`Android `Adapter`與類似)。

首先, 簡短的教學課程會`ListView`介紹具有基本程式碼範例的。 接下來, 提供更多的主題連結, 協助您在`ListView`真實世界的應用程式中使用。

> [!NOTE]
> Widget 是更先進且彈性的`ListView`版本。 `RecyclerView` 因為`RecyclerView`設計為`ListView` (和`GridView`) 的後續版本`ListView` , 所以我們建議您使用`RecyclerView` , 而不是針對新的應用程式開發。 如需詳細資訊, 請參閱[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)。

## <a name="listview-tutorial"></a>ListView 教學課程

[`ListView`](xref:Android.Widget.ListView)是[`ViewGroup`](xref:Android.Views.ViewGroup)
這會建立可滾動專案的清單。 清單專案會使用[`IListAdapter`](xref:Android.Widget.IListAdapter)自動插入清單中。

在本教學課程中, 您將建立從字串陣列讀取之國家/地區名稱的可滾動清單。 選取清單專案時, 快顯訊息會顯示清單中專案的位置。

啟動名為**HelloListView**的新專案。

建立名為**list_item**的 xml 檔案, 並將它儲存在**Resources/Layout/** 資料夾內。 插入下列內容:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

這個檔案會定義將放在中[`ListView`](xref:Android.Widget.ListView)之每個專案的版面配置。

開啟`MainActivity.cs`並修改類別以擴充[`ListActivity`](xref:Android.App.ListActivity) (而不是[`Activity`](xref:Android.App.Activity)):

```csharp
public class MainActivity : ListActivity
{
```

為[`OnCreate()`](xref:Android.App.Activity.OnCreate*)) 方法插入下列程式碼:

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

請注意, 這並不會載入活動的配置檔案 (通常是使用[`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)))。
相反地, 設定[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
屬性會自動新增[`ListView`](xref:Android.Widget.ListView)
以填滿的整個畫面[`ListActivity`](xref:Android.App.ListActivity)。
這個方法會採用[`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1), 它會管理要放[`ListView`](xref:Android.Widget.ListView)入中的清單專案陣列。
該[`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1)
「處理常式」 [`Context`](xref:Android.Content.Context)會採用應用程式、每個清單專案 (在上一個步驟中建立) 的`T[]`配置描述, 以及或[`Java.Util.IList<T>`](xref:Java.Util.IList)
要插入的物件陣列[`ListView`](xref:Android.Widget.ListView)
(定義下一個)。

該[`TextFilterEnabled`](xref:Android.Widget.AbsListView.TextFilterEnabled)
屬性會開啟的[`ListView`](xref:Android.Widget.ListView)文字篩選, 因此當使用者開始輸入時, 將會篩選清單。

該[`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
事件可以用來訂閱處理常式以進行點擊。 當中的專案[`ListView`](xref:Android.Widget.ListView)
按一下時, 會呼叫處理常式, 並使用[`Toast`](xref:Android.Widget.Toast)
訊息隨即顯示, 並使用按下專案中的文字。

您可以使用平臺所提供的清單專案設計, 而不是定義您自己的配置[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)檔。
例如, 請嘗試使用`Android.Resource.Layout.SimpleListItem1` , `Resource.Layout.list_item`而不是。

新增下列`using`語句:

```csharp
using System;
```
接下來, 將下列字串陣列新增為的成員`MainActivity`:

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

這是將放入中[`ListView`](xref:Android.Widget.ListView)的字串陣列。

執行應用程式。 您可以滾動清單或輸入以進行篩選, 然後按一下專案以查看訊息。 您應該會看到類似下面的內容：

[![ListView 的範例螢幕擷取畫面, 包含國家/地區名稱](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

請注意, 使用硬式編碼的字串陣列並不是最佳的設計作法。 為了簡單起見, 本教學課程中會使用一個, 以示範[`ListView`](xref:Android.Widget.ListView)
機械. 較好的作法是參考外部資源所定義的字串陣列, 例如`string-array`專案**資源/值/字串 .xml**檔案中的資源。 例如：

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

若要將這些資源字串[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter`1)用於, 請取代原始的[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
使用下列程式程式碼:

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```

執行應用程式。 您應該會看到類似下面的內容：

[![ListView 的範例螢幕擷取畫面, 其中包含較小的名稱清單](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)

## <a name="going-further-with-listview"></a>使用 ListView 進一步發展

其餘主題 (連結于`ListView`下方) 會全面探討如何使用類別, 以及您可以與它搭配使用的不同類型介面卡類型。 結構如下所示：

-   **視覺外觀**控制項的`ListView`各個部分, 以及它們的使用方式。 &ndash;

-   **類別**概述用來`ListView`顯示的類別。 &ndash;

-   **在 ListView 中顯示資料**如何顯示簡單的資料清單、如何執行`ListView's`可用性功能、如何使用不同的內建資料列配置, 以及介面卡如何藉由重新使用資料列視圖來節省記憶體。 &ndash;

-   **自訂外觀**使用自`ListView`定義版面配置、字型和色彩變更的樣式。 &ndash;

-   **使用 SQLite**如何使用顯示 SQLite 資料庫中的`CursorAdapter`資料。 &ndash;

-   **活動生命週期**&ndash;在執行`ListView`活動時的設計考慮, 包括生命週期中您應填入資料的位置, 以及釋放資源的時機。

討論 (分為六個部分) 從`ListView`類別本身的總覽開始, 再引進更複雜的使用方式範例。

-   [ListView 組件和功能](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [使用資料填入 ListView](~/android/user-interface/layouts/list-view/populating.md)
-   [自訂 ListView 的外觀](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [使用 CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [使用 ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView 和活動生命週期](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>總結

這組主題會引進`ListView`並提供一些範例, 說明如何使用的內建功能。 `ListActivity` 它討論了允許彩色`ListView`版面配置和使用 SQLite 資料庫的自訂實現, 而且它會簡短觸及您`ListView`的實作為活動生命週期的相關性。


## <a name="related-links"></a>相關連結

- [AccessoryViews (範例)](https://developer.xamarin.com/samples/monodroid/AccessoryViews/)
- [BasicTableAndroid (範例)](https://developer.xamarin.com/samples/monodroid/BasicTableAndroid/)
- [BasicTableAdapter (範例)](https://developer.xamarin.com/samples/monodroid/BasicTableAdapter/)
- [BuiltInViews (範例)](https://developer.xamarin.com/samples/monodroid/BuiltInViews/)
- [CustomRowView (範例)](https://developer.xamarin.com/samples/monodroid/CustomRowView/)
- [FastScroll (範例)](https://developer.xamarin.com/samples/monodroid/FastScroll/)
- [SectionIndex (範例)](https://developer.xamarin.com/samples/monodroid/SectionIndex/)
- [SimpleCursorTableAdapter (範例)](https://developer.xamarin.com/samples/monodroid/SimpleCursorTableAdapter/)
- [CursorTableAdapter (範例)](https://developer.xamarin.com/samples/monodroid/CursorTableAdapter/)
- [活動生命週期教學課程](~/android/app-fundamentals/activity-lifecycle/index.md)
- [使用資料表和資料格 (在 Xamarin. iOS 中)](~/ios/user-interface/controls/tables/index.md)
- [ListView 類別參考](xref:Android.Widget.ListView)
- [ListActivity 類別參考](xref:Android.App.ListActivity)
- [BaseAdapter 類別參考](xref:Android.Widget.BaseAdapter)
- [ArrayAdapter 類別參考](xref:Android.Widget.ArrayAdapter)
- [CursorAdapter 類別參考](xref:Android.Widget.CursorAdapter)
