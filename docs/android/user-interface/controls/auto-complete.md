---
title: 自動完成
ms.prod: xamarin
ms.assetid: D4C8CA49-8369-35B7-798D-B147FDC24185
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/31/2018
ms.openlocfilehash: cf2221380e5ddbd8278cc2d387c6eb185d990c1a
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671894"
---
# <a name="auto-complete"></a>自動完成

`AutoCompleteTextView` 會自動顯示完成建議，而使用者輸入的可編輯的文字檢視項目。 建議清單會顯示在下拉式功能表，使用者可以從中選擇一個項目來取代使用 [編輯] 方塊的內容。

![自動完成的範例](images/auto-complete.png)

## <a name="overview"></a>總覽

若要建立文字項目小工具提供自動完成建議，請使用 [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
小工具。 透過小工具相關聯的字串集合從接收建議[ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)。

在本教學課程中，您將建立 [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
提供建議的國家/地區名稱的小工具。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="5dp">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Country" />
    <AutoCompleteTextView android:id="@+id/autocomplete_country"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"/>
</LinearLayout>
```

[ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/)是導入的標籤 [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
小工具。


## <a name="tutorial"></a>教學課程

開始新的專案，名為*HelloAutoComplete*。

建立名為 XML 檔案`list_item.xml`並將其內部儲存**資源/配置**資料夾。 將這個檔案的建置動作設定`AndroidResource`。 編輯檔案以看起來像這樣：

```xml
<?xml version="1.0" encoding="utf-8"?>

<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp"
    android:textColor="#000">
</TextView>
```

這個檔案會定義簡單[ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/)將用於每個項目會出現在建議清單。

開啟**Resources/Layout/Main.axml**並插入下列：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="5dp">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Country" />
    <AutoCompleteTextView android:id="@+id/autocomplete_country"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"/>
</LinearLayout>
```

開啟**MainActivity.cs**並插入下列程式碼 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "Main" layout resource
    SetContentView (Resource.Layout.Main);

    AutoCompleteTextView textView = FindViewById<AutoCompleteTextView> (Resource.Id.autocomplete_country);
    var adapter = new ArrayAdapter<String> (this, Resource.Layout.list_item, COUNTRIES);

    textView.Adapter = adapter;
}
```

設定內容的檢視之後`main.xml`版面配置， [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
小工具擷取自與版面配置[ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/)。 新[ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)則會初始化為繫結`list_item.xml`版面配置中的每個清單項目`COUNTRIES`（定義於下一個步驟） 的字串陣列。 最後，`SetAdapter()`呼叫來建立關聯[ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)與 [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
小工具，讓字串陣列將填入建議清單。

內`MainActivity`類別中，新增的字串陣列：

```csharp
static string[] COUNTRIES = new string[] {
  "Afghanistan", "Albania", "Algeria", "American Samoa", "Andorra",
  "Angola", "Anguilla", "Antarctica", "Antigua and Barbuda", "Argentina",
  "Armenia", "Aruba", "Australia", "Austria", "Azerbaijan",
  "Bahrain", "Bangladesh", "Barbados", "Belarus", "Belgium",
  "Belize", "Benin", "Bermuda", "Bhutan", "Bolivia",
  "Bosnia and Herzegovina", "Botswana", "Bouvet Island", "Brazil", "British Indian Ocean Territory",
  "British Virgin Islands", "Brunei", "Bulgaria", "Burkina Faso", "Burundi",
  "Cote d'Ivoire", "Cambodia", "Cameroon", "Canada", "Cape Verde",
  "Cayman Islands", "Central African Republic", "Chad", "Chile", "China",
  "Christmas Island", "Cocos (Keeling) Islands", "Colombia", "Comoros", "Congo",
  "Cook Islands", "Costa Rica", "Croatia", "Cuba", "Cyprus", "Czech Republic",
  "Democratic Republic of the Congo", "Denmark", "Djibouti", "Dominica", "Dominican Republic",
  "East Timor", "Ecuador", "Egypt", "El Salvador", "Equatorial Guinea", "Eritrea",
  "Estonia", "Ethiopia", "Faeroe Islands", "Falkland Islands", "Fiji", "Finland",
  "Former Yugoslav Republic of Macedonia", "France", "French Guiana", "French Polynesia",
  "French Southern Territories", "Gabon", "Georgia", "Germany", "Ghana", "Gibraltar",
  "Greece", "Greenland", "Grenada", "Guadeloupe", "Guam", "Guatemala", "Guinea", "Guinea-Bissau",
  "Guyana", "Haiti", "Heard Island and McDonald Islands", "Honduras", "Hong Kong", "Hungary",
  "Iceland", "India", "Indonesia", "Iran", "Iraq", "Ireland", "Israel", "Italy", "Jamaica",
  "Japan", "Jordan", "Kazakhstan", "Kenya", "Kiribati", "Kuwait", "Kyrgyzstan", "Laos",
  "Latvia", "Lebanon", "Lesotho", "Liberia", "Libya", "Liechtenstein", "Lithuania", "Luxembourg",
  "Macau", "Madagascar", "Malawi", "Malaysia", "Maldives", "Mali", "Malta", "Marshall Islands",
  "Martinique", "Mauritania", "Mauritius", "Mayotte", "Mexico", "Micronesia", "Moldova",
  "Monaco", "Mongolia", "Montserrat", "Morocco", "Mozambique", "Myanmar", "Namibia",
  "Nauru", "Nepal", "Netherlands", "Netherlands Antilles", "New Caledonia", "New Zealand",
  "Nicaragua", "Niger", "Nigeria", "Niue", "Norfolk Island", "North Korea", "Northern Marianas",
  "Norway", "Oman", "Pakistan", "Palau", "Panama", "Papua New Guinea", "Paraguay", "Peru",
  "Philippines", "Pitcairn Islands", "Poland", "Portugal", "Puerto Rico", "Qatar",
  "Reunion", "Romania", "Russia", "Rwanda", "Sqo Tome and Principe", "Saint Helena",
  "Saint Kitts and Nevis", "Saint Lucia", "Saint Pierre and Miquelon",
  "Saint Vincent and the Grenadines", "Samoa", "San Marino", "Saudi Arabia", "Senegal",
  "Seychelles", "Sierra Leone", "Singapore", "Slovakia", "Slovenia", "Solomon Islands",
  "Somalia", "South Africa", "South Georgia and the South Sandwich Islands", "South Korea",
  "Spain", "Sri Lanka", "Sudan", "Suriname", "Svalbard and Jan Mayen", "Swaziland", "Sweden",
  "Switzerland", "Syria", "Taiwan", "Tajikistan", "Tanzania", "Thailand", "The Bahamas",
  "The Gambia", "Togo", "Tokelau", "Tonga", "Trinidad and Tobago", "Tunisia", "Turkey",
  "Turkmenistan", "Turks and Caicos Islands", "Tuvalu", "Virgin Islands", "Uganda",
  "Ukraine", "United Arab Emirates", "United Kingdom",
  "United States", "United States Minor Outlying Islands", "Uruguay", "Uzbekistan",
  "Vanuatu", "Vatican City", "Venezuela", "Vietnam", "Wallis and Futuna", "Western Sahara",
  "Yemen", "Yugoslavia", "Zambia", "Zimbabwe"
};
```

這是使用者輸入時，會提供下拉式清單中的建議清單 [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
小工具。

執行應用程式。 當您輸入時，您應該會看到類似如下：

[![自動完成螢幕擷取畫面範例列出包含"ca"的名稱](auto-complete-images/helloautocomplete.png)](auto-complete-images/helloautocomplete.png#lightbox)



## <a name="more-information"></a>更多資訊

請注意，使用硬式編碼的字串陣列，不是建議的設計作法，因為您的應用程式程式碼應該專注於行為，不是內容。 應用程式內容，例如字串應該以輕鬆修改的內容，並促進當地語系化內容的程式碼，才能外部化。 硬式編碼的字串會在本教學課程只用於更為簡單，並專注於 [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
小工具。 相反地，您的應用程式應該宣告這類 XML 檔案中的字串陣列。 這可以透過完成`<string-array>`專案中的資源`res/values/strings.xml`檔案。 例如: 

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

若要使用這些資源字串[ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)，取代原始 [`ArrayAdapter`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
使用下列建構函式行：

```csharp
string[] countries = Resources.GetStringArray (Resource.array.countries_array);
var adapter = new ArrayAdapter<String> (this, Resource.layout.list_item, countries);
```


### <a name="references"></a>參考

-   [AutoCompleteTextView 配方](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/autocomplete_text_view/add_an_autocomplete_text_input) &ndash; Xamarin.Android 的範例專案`AutoCompleteTextView`。
-   [`ArrayAdapter`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
-   [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)

*此頁面上的部分是根據工作建立及 Android 的開放原始碼專案所共用，並依據所述的條款來使用修改*
 [ *Creative Commons 2.5 Attribution License*](http://creativecommons.org/licenses/by/2.5/) *.本教學課程根據*
 [ *Android 自動完成的教學課程*](https://developer.android.com/resources/tutorials/views/hello-autocomplete.html)
 *。*
