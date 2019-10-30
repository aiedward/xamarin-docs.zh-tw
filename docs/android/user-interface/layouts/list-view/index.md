---
title: 在 Xamarin 中使用 ListView
description: ListView 是 Android 應用程式的重要 UI 元件;其使用方式是從功能表選項的簡短清單，到長清單的連絡人或網際網路我的最愛。 它提供了一種簡單的方式來呈現資料列的滾動清單，您可以使用內建樣式或廣泛自訂來進行格式化。
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: f6579e3b70e3788046916db12e201550e7fd5f16
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028883"
---
# <a name="xamarinandroid-listview"></a>Xamarin. Android ListView

_ListView 是 Android 應用程式的重要 UI 元件;其使用方式是從功能表選項的簡短清單，到長清單的連絡人或網際網路我的最愛。它提供了一種簡單的方式來呈現資料列的滾動清單，您可以使用內建樣式或廣泛自訂來進行格式化。_

## <a name="overview"></a>總覽

清單視圖和介面卡包含在 Android 應用程式的最基本組建區塊中。 `ListView` 類別提供一種彈性的方式來呈現資料，不論是簡短的功能表或長的滾動清單。 它提供快速滾動、索引和單一或多重選取等可用性功能，可協助您為應用程式建立方便使用的使用者介面。 `ListView` 執行個體需要 *Adapter* 提供資料列檢視中所含的資料給它。

本指南說明如何在 Xamarin 中執行 `ListView` 和各種 `Adapter` 類別。 它也會示範如何自訂 `ListView`的外觀，並討論資料列重複使用來減少記憶體耗用量的重要性。 此外，也會討論活動生命週期如何影響 `ListView` 和 `Adapter` 使用。 如果您在使用 Xamarin 的跨平臺應用程式，則 `ListView` 控制項的結構類似于 iOS `UITableView` （而 Android `Adapter` 與 `UITableViewSource`類似）。

首先，簡短的教學課程會介紹使用基本程式碼範例的 `ListView`。 接下來，提供更多的主題連結，協助您在真實世界的應用程式中使用 `ListView`。

> [!NOTE]
> `RecyclerView` widget 是更先進且彈性的 `ListView`版本。 由於 `RecyclerView` 是設計成 `ListView` （和 `GridView`）的後續版本，因此建議您使用 `RecyclerView`，而不要使用 `ListView` 來進行新的應用程式開發。 如需詳細資訊，請參閱[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)。

## <a name="listview-tutorial"></a>ListView 教學課程

[`ListView`](xref:Android.Widget.ListView)是一個[`ViewGroup`](xref:Android.Views.ViewGroup)
這會建立可滾動專案的清單。 系統會使用[`IListAdapter`](xref:Android.Widget.IListAdapter)，將清單專案自動插入清單中。

在本教學課程中，您將建立從字串陣列讀取之國家/地區名稱的可滾動清單。 選取清單專案時，快顯訊息會顯示清單中專案的位置。

啟動名為**HelloListView**的新專案。

建立名為**list_item**的 xml 檔案，並將它儲存在**Resources/Layout/** 資料夾內。 插入下列內容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

這個檔案會定義將放在[`ListView`](xref:Android.Widget.ListView)中的每個專案的配置。

開啟 `MainActivity.cs` 並修改類別以擴充[`ListActivity`](xref:Android.App.ListActivity) （而不是[`Activity`](xref:Android.App.Activity)）：

```csharp
public class MainActivity : ListActivity
{
```

為[`OnCreate()`](xref:Android.App.Activity.OnCreate*)）方法插入下列程式碼：

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

請注意，這並不會載入活動的配置檔案（您通常會使用[`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)））。
相反地，設定[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
屬性會自動新增[`ListView`](xref:Android.Widget.ListView)
以填滿[`ListActivity`](xref:Android.App.ListActivity)的整個畫面。
這個方法會使用[`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1)，它會管理要放入[`ListView`](xref:Android.Widget.ListView)的清單專案陣列。
[`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1)
「處理常式」會將應用程式[`Context`](xref:Android.Content.Context)、每個清單專案（在上一個步驟中建立）的配置描述，以及 `T[]` 或[`Java.Util.IList<T>`](xref:Java.Util.IList)
要插入[`ListView`](xref:Android.Widget.ListView)中的物件陣列
（定義下一個）。

[`TextFilterEnabled`](xref:Android.Widget.AbsListView.TextFilterEnabled)
屬性會開啟[`ListView`](xref:Android.Widget.ListView)的文字篩選，因此當使用者開始輸入時，將會篩選清單。

[`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
事件可以用來訂閱處理常式以進行點擊。 當[`ListView`](xref:Android.Widget.ListView)中的專案
按一下時，會呼叫處理常式和[`Toast`](xref:Android.Widget.Toast)
訊息隨即顯示，並使用按下專案中的文字。

您可以使用平臺所提供的清單專案設計，而不是為[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)定義您自己的設定檔。
例如，請嘗試使用 `Android.Resource.Layout.SimpleListItem1`，而不是 `Resource.Layout.list_item`。

新增下列 `using` 語句：

```csharp
using System;
```

接下來，將下列字串陣列新增為 `MainActivity`的成員：

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

這是將放入[`ListView`](xref:Android.Widget.ListView)中的字串陣列。

執行應用程式。 您可以滾動清單或輸入以進行篩選，然後按一下專案以查看訊息。 您應該會看到類似下面的內容：

[具有國家/地區名稱之 ListView 的![範例螢幕擷取畫面](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

請注意，使用硬式編碼的字串陣列並不是最佳的設計作法。 為了簡單起見，本教學課程中會使用一個，以示範[`ListView`](xref:Android.Widget.ListView)
機械. 較好的作法是參考外部資源所定義的字串陣列，例如專案**資源/值/字串 .xml**檔案中的 `string-array` 資源。 例如:

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

若要將這些資源字串用於[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter`1)，請取代原始的[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
使用下列程式程式碼：

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```

執行應用程式。 您應該會看到類似下面的內容：

[含有較少名稱清單之 ListView 的![範例螢幕擷取畫面](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)

## <a name="going-further-with-listview"></a>使用 ListView 進一步發展

其餘主題（連結于下方）會全面探討如何使用 `ListView` 類別，以及您可以用來搭配它的不同類型介面卡類型。 結構如下所示：

- **視覺外觀**&ndash; `ListView` 控制項的各個部分，以及它們的使用方式。

- [**類別**] &ndash; 用來顯示 `ListView`的類別。

- **在 ListView 中顯示資料**&ndash; 如何顯示簡單的資料清單;如何實行 `ListView's` 可用性功能;如何使用不同的內建資料列版面配置;以及介面卡如何藉由重新使用資料列視圖來節省記憶體。

- **自訂外觀**&ndash; 使用自訂版面配置、字型和色彩來變更 `ListView` 的樣式。

- **使用 SQLite** &ndash; 如何顯示具有 `CursorAdapter`之 SQLite 資料庫的資料。

- **活動生命週期**&ndash; 在執行 `ListView` 活動時的設計考慮，包括生命週期中您應填入資料的位置，以及釋放資源的時機。

討論（分成六個部分）先從 `ListView` 類別本身的總覽開始，再引進更複雜的使用方式範例。

- [ListView 組件和功能](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
- [使用資料填入 ListView](~/android/user-interface/layouts/list-view/populating.md)
- [自訂 ListView 的外觀](~/android/user-interface/layouts/list-view/customizing-appearance.md)
- [使用 CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
- [使用 ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
- [ListView 和活動生命週期](~/android/user-interface/layouts/list-view/activity-lifecycle.md)

## <a name="summary"></a>總結

這組主題 `ListView` 引進，並提供一些如何使用 `ListActivity`內建功能的範例。 它討論了 `ListView` 的自訂執行，可供彩色版面配置及使用 SQLite 資料庫，並在您的 `ListView` 的實作為活動生命週期的相關性時，很快就能接觸到。

## <a name="related-links"></a>相關連結

- [AccessoryViews （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/accessoryviews)
- [BasicTableAndroid （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableadapter)
- [BuiltInViews （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/builtinviews)
- [CustomRowView （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/customrowview)
- [FastScroll （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fastscroll)
- [SectionIndex （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sectionindex)
- [SimpleCursorTableAdapter （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/cursortableadapter)
- [活動生命週期教學課程](~/android/app-fundamentals/activity-lifecycle/index.md)
- [使用資料表和資料格（在 Xamarin. iOS 中）](~/ios/user-interface/controls/tables/index.md)
- [ListView 類別參考](xref:Android.Widget.ListView)
- [ListActivity 類別參考](xref:Android.App.ListActivity)
- [BaseAdapter 類別參考](xref:Android.Widget.BaseAdapter)
- [ArrayAdapter 類別參考](xref:Android.Widget.ArrayAdapter)
- [CursorAdapter 類別參考](xref:Android.Widget.CursorAdapter)
