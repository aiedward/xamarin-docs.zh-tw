---
title: 在 Xamarin 中使用 ListView
description: ListView 是 Android 應用程式的重要 UI 元件;您可以從功能表選項的簡短清單中使用它，到長清單的連絡人或網際網路我的最愛。 它提供簡單的方式來呈現資料列的滾動清單，這些資料列可以使用內建樣式進行格式化，也可以廣泛進行自訂。
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 50784844d35e2f04436b05d9491149a3e0282bdc
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457174"
---
# <a name="xamarinandroid-listview"></a>Xamarin. Android ListView

_ListView 是 Android 應用程式的重要 UI 元件;您可以從功能表選項的簡短清單中使用它，到長清單的連絡人或網際網路我的最愛。它提供簡單的方式來呈現資料列的滾動清單，這些資料列可以使用內建樣式進行格式化，也可以廣泛進行自訂。_

## <a name="overview"></a>概觀

列出視圖和介面卡會包含在 Android 應用程式的最基本組建區塊中。 `ListView`類別可提供彈性的方式來呈現資料，不論是簡短的功能表或長的滾動清單。 它提供快速滾動、索引和單一或多個選項等可用性功能，可協助您為應用程式建立便於使用行動裝置的使用者介面。 `ListView` 執行個體需要 *Adapter* 提供資料列檢視中所含的資料給它。

本指南說明如何 `ListView` 在 Xamarin 中執行和各種不同的 `Adapter` 類別。 它也會示範如何自訂的外觀 `ListView` ，並討論資料列重複使用的重要性，以降低記憶體耗用量。 此外，也會討論活動生命週期如何影響 `ListView` 和 `Adapter` 使用。 如果您使用的是使用 Xamarin 的跨平臺應用程式，則該 `ListView` 控制項的結構與 ios (類似， `UITableView` 且 Android `Adapter` 類似于 `UITableViewSource`) 。

首先，簡短的教學課程會介紹 `ListView` 具有基本程式碼範例的。 接下來，會提供更多 advanced 主題的連結，協助您 `ListView` 在真實世界的應用程式中使用。

> [!NOTE]
> `RecyclerView`Widget 是更先進且彈性的版本 `ListView` 。 因為 `RecyclerView` 是設計為 `ListView` (和) 的後續版本 `GridView` ，所以我們建議您使用， `RecyclerView` 而不是 `ListView` 針對新的應用程式開發。 如需詳細資訊，請參閱 [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)。

## <a name="listview-tutorial"></a>ListView 教學課程

[`ListView`](xref:Android.Widget.ListView) 是 [`ViewGroup`](xref:Android.Views.ViewGroup)
這會建立可滾動專案的清單。 清單專案會使用來自動插入清單中 [`IListAdapter`](xref:Android.Widget.IListAdapter) 。

在本教學課程中，您將會建立從字串陣列讀取之國家/地區名稱的可滾動清單。 選取清單專案時，快顯訊息會顯示專案在清單中的位置。

開始名為 **HelloListView**的新專案。

建立名為 **list_item.xml** 的 XML 檔案，並將它儲存在 **資源/配置/** 資料夾內。 插入下列內容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

這個檔案會定義將放置在中的每個專案的版面配置 [`ListView`](xref:Android.Widget.ListView) 。

開啟 `MainActivity.cs` 並修改類別，以擴充 [`ListActivity`](xref:Android.App.ListActivity) (而不是 [`Activity`](xref:Android.App.Activity)) ：

```csharp
public class MainActivity : ListActivity
{
```

針對) 方法插入下列程式碼 [`OnCreate()`](xref:Android.App.Activity.OnCreate*) ：

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

請注意，這不會載入活動 (的版面配置檔案，您通常會使用 [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)) # A2 來執行此動作。
相反地，設定 [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
屬性會自動新增 [`ListView`](xref:Android.Widget.ListView)
填滿的整個畫面 [`ListActivity`](xref:Android.App.ListActivity) 。
這個方法會採用 [`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1) ，它會管理要放入中的清單專案陣列 [`ListView`](xref:Android.Widget.ListView) 。
[`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1)
「函式」會採用應用程式 [`Context`](xref:Android.Content.Context) 、 (在上一個步驟中建立的每個清單專案的版面配置描述) ，以及 `T[]` 或 [`Java.Util.IList<T>`](xref:Java.Util.IList)
要插入的物件陣列 [`ListView`](xref:Android.Widget.ListView)
 (定義下一個) 。

[`TextFilterEnabled`](xref:Android.Widget.AbsListView.TextFilterEnabled)
屬性會開啟的文字篩選 [`ListView`](xref:Android.Widget.ListView) ，如此一來，當使用者開始鍵入時，就會篩選清單。

[`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
事件可以用來訂閱處理常式以進行點擊。 當專案在 [`ListView`](xref:Android.Widget.ListView)
按一下時會呼叫處理常式，並呼叫 [`Toast`](xref:Android.Widget.Toast)
使用按一下專案中的文字來顯示訊息。

您可以使用平臺所提供的清單專案設計，而不是定義您自己的版面配置檔案 [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter) 。
例如，請嘗試使用 `Android.Resource.Layout.SimpleListItem1` 而不是 `Resource.Layout.list_item` 。

加入下列 `using` 陳述式：

```csharp
using System;
```

接下來，新增下列字串陣列做為的成員 `MainActivity` ：

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

這是將放入中的字串陣列 [`ListView`](xref:Android.Widget.ListView) 。

執行應用程式。 您可以滾動清單或輸入以篩選清單，然後按一下專案以查看訊息。 您應該會看到如下內容：

[![包含國家/地區名稱的 ListView 範例螢幕擷取畫面](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

請注意，使用硬式編碼的字串陣列不是最佳的設計作法。 為了簡單起見，本教學課程中會使用一個，以示範 [`ListView`](xref:Android.Widget.ListView)
部件。 更好的作法是參考外部資源所定義的字串陣列，例如，使用 `string-array` 專案 **資源/值/Strings.xml** 檔案中的資源。 例如：

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

若要針對使用這些資源字串 [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter`1) ，請取代原始的 [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
以下列程式程式碼：

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```

執行應用程式。 您應該會看到如下內容：

[![具有較小名稱清單的 ListView 範例螢幕擷取畫面](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)

## <a name="going-further-with-listview"></a>使用 ListView 繼續進行

以下 (連結的其餘主題) 全面瞭解如何使用 `ListView` 類別，以及您可以搭配使用的不同類型介面卡類型。 其結構如下所示：

- **視覺外觀** &ndash; 控制項的各部分 `ListView` 以及其運作方式。

- **類別** &ndash; 用來顯示的類別總覽   `ListView` 。

- **在 ListView** &ndash; 中顯示資料如何顯示簡單的資料清單;如何實行 `ListView's` 可用性功能、如何使用不同的內建資料列配置，以及如何使用資料列視圖來節省記憶體。

- **自訂外觀** &ndash;`ListView`使用自訂版面配置、字型和色彩來變更的樣式。

- **使用 SQLite** &ndash; 如何使用來顯示 SQLite 資料庫的資料 `CursorAdapter` 。

- **活動生命週期** &ndash; 在實施活動時的設計考慮 `ListView` ，包括您應在生命週期中填入資料的位置，以及釋放資源的時機。

討論 (分成六個部分) 一開始 `ListView` 先概述類別本身，然後再引進更複雜的範例，說明如何使用它。

- [ListView 組件和功能](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
- [使用資料填入 ListView](~/android/user-interface/layouts/list-view/populating.md)
- [自訂 ListView 的外觀](~/android/user-interface/layouts/list-view/customizing-appearance.md)
- [使用 CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
- [使用 ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
- [ListView 和活動生命週期](~/android/user-interface/layouts/list-view/activity-lifecycle.md)

## <a name="summary"></a>摘要

這組主題的推出 `ListView` ，並提供一些範例，說明如何使用的內建功能 `ListActivity` 。 它討論了自訂的 `ListView` 實作為配置，並且可以使用 SQLite 資料庫，並在您的實作為活動生命週期的關聯性時，短暫接觸到 `ListView` 。

## <a name="related-links"></a>相關連結

- [AccessoryViews (範例) ](/samples/xamarin/monodroid-samples/accessoryviews)
- [BasicTableAndroid (範例) ](/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter (範例) ](/samples/xamarin/monodroid-samples/basictableadapter)
- [BuiltInViews (範例) ](/samples/xamarin/monodroid-samples/builtinviews)
- [CustomRowView (範例) ](/samples/xamarin/monodroid-samples/customrowview)
- [FastScroll (範例) ](/samples/xamarin/monodroid-samples/fastscroll)
- [SectionIndex (範例) ](/samples/xamarin/monodroid-samples/sectionindex)
- [SimpleCursorTableAdapter (範例) ](/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter (範例) ](/samples/xamarin/monodroid-samples/cursortableadapter)
- [活動生命週期教學課程](~/android/app-fundamentals/activity-lifecycle/index.md)
- [在 Xamarin 中使用資料表和資料格 () ](~/ios/user-interface/controls/tables/index.md)
- [ListView 類別參考](xref:Android.Widget.ListView)
- [ListActivity 類別參考](xref:Android.App.ListActivity)
- [BaseAdapter 類別參考](xref:Android.Widget.BaseAdapter)
- [>arrayadapter 類別參考](xref:Android.Widget.ArrayAdapter)
- [CursorAdapter 類別參考](xref:Android.Widget.CursorAdapter)