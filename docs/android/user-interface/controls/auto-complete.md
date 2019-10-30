---
title: 自動完成 Xamarin. Android
ms.prod: xamarin
ms.assetid: D4C8CA49-8369-35B7-798D-B147FDC24185
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/31/2018
ms.openlocfilehash: 5a11ab06321b890d8f1f5a35a8456b06a900fbcc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029371"
---
# <a name="auto-complete-for-xamarinandroid"></a>自動完成 Xamarin. Android

`AutoCompleteTextView` 是可編輯的文字 view 元素，會在使用者輸入時自動顯示完成建議。 建議清單會顯示在下拉式功能表中，使用者可以從中選擇專案，以取代編輯方塊的內容。

![自動完成的範例](images/auto-complete.png)

## <a name="overview"></a>總覽

若要建立可提供自動完成建議的文字輸入 widget，請使用[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
機械. 系統會透過[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)，從與 widget 相關聯的字串集合收到建議。

在本教學課程中，您將建立[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
提供國家（地區）名稱建議的 widget。

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

[`TextView`](xref:Android.Widget.TextView)是介紹[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)的標籤
機械.

## <a name="tutorial"></a>教學課程

啟動名為*HelloAutoComplete*的新專案。

建立名為 `list_item.xml` 的 XML 檔案，並將它儲存在**Resources/Layout**資料夾內。 將此檔案的 [建立] 動作設定為 [`AndroidResource`]。 編輯檔案，如下所示：

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

這個檔案會定義一個簡單的[`TextView`](xref:Android.Widget.TextView) ，以供出現在建議清單中的每個專案使用。

開啟**Resources/Layout/axml** ，並插入下列內容：

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

開啟**MainActivity.cs** ，並將下列程式碼插入[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
方法

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

內容視圖設定為 `main.xml` 配置之後， [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget 是從具有[`FindViewById`](xref:Android.App.Activity.FindViewById*)的版面配置中捕捉而來。 接著會初始化新的[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) ，以將 `list_item.xml` 配置系結至 `COUNTRIES` 字串陣列中的每個清單專案（在下一個步驟中定義）。 最後，會呼叫 `SetAdapter()`，將[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)與[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget，讓字串陣列填入建議清單。

在 `MainActivity` 類別中，新增字串陣列：

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

這是當使用者輸入[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)時，下拉式清單中將提供的建議清單。
機械.

執行應用程式。 當您輸入時，您應該會看到類似下面的內容：

[![範例自動完成的螢幕擷取畫面，列出包含「ca」的名稱](auto-complete-images/helloautocomplete.png)](auto-complete-images/helloautocomplete.png#lightbox)

## <a name="more-information"></a>更多資訊

請注意，使用硬式編碼的字串陣列並不是建議的設計做法，因為您的應用程式程式碼應專注于行為，而不是內容。 應用程式內容（例如字串）應從程式碼外部化，以更輕鬆地修改內容，並加速內容的當地語系化。 在本教學課程中使用硬式編碼的字串，只是為了簡單且專注于[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
機械. 相反地，您的應用程式應該在 XML 檔案中宣告這類字串陣列。 您可以使用專案 `res/values/strings.xml` 檔案中的 `<string-array>` 資源來完成這項作業。 例如:

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

若要將這些資源字串用於[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)，請取代原始的[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
具有下列各項的函式程式程式碼：

```csharp
string[] countries = Resources.GetStringArray (Resource.array.countries_array);
var adapter = new ArrayAdapter<String> (this, Resource.layout.list_item, countries);
```

### <a name="references"></a>reference

- &ndash; 適用于 `AutoCompleteTextView` 的 Xamarin Android 範例專案的[AutoCompleteTextView 配方](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/autocomplete_text_view/add_an_autocomplete_text_input)
- [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
- [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)

_此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改，並根據[創意 Commons 2.5 屬性授權](https://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。本教學課程是以[Android 自動完成教學課程 *](https://developer.android.com/resources/tutorials/views/hello-autocomplete.html)為基礎。_
