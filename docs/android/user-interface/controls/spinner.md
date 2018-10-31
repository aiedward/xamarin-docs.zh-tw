---
title: Spinner
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 90b4755cdb4b8248c2b731d070d720076d4dda40
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102993"
---
# <a name="spinner"></a>Spinner

[`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) 是一種 widget，顯示的下拉式清單選取項目。 本指南說明如何建立簡單的應用程式中的微調，後面接著顯示與選取的選項相關聯的其他值的修改顯示選項清單。

## <a name="basic-spinner"></a>基本微調按鈕

在本教學課程的第一個部分中，您將建立一種簡單的微調 widget，顯示一份行星。 選取全球時，快顯通知訊息會顯示選取的項目：

[![HelloSpinner 應用程式的範例螢幕擷取畫面](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

開始新的專案，名為**HelloSpinner**。

開啟**Resources/Layout/Main.axml**並插入下列 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:padding="10dip"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dip"
        android:text="@string/planet_prompt"
    />
    <Spinner
        android:id="@+id/spinner"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:prompt="@string/planet_prompt"
    />
</LinearLayout>
```

請注意， [ `TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)的`android:text`屬性和[ `Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)的`android:prompt`屬性，這兩個參考相同的字串資源。 這段文字行為會如同在小工具的標題。 當套用至[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)，標題文字會出現在選取小工具時出現的 [選取] 對話方塊。

編輯**Resources/Values/Strings.xml**和修改檔案，看起來像這樣：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloSpinner</string>
  <string name="planet_prompt">Choose a planet</string>
  <string-array name="planets_array">
    <item>Mercury</item>
    <item>Venus</item>
    <item>Earth</item>
    <item>Mars</item>
    <item>Jupiter</item>
    <item>Saturn</item>
    <item>Uranus</item>
    <item>Neptune</item>
  </string-array>
</resources>
```

第二個`<string>`項目會定義所參考的標題字串[ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/)並[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)上述版面配置中。
`<string-array>`項目會定義將會顯示為清單中的字串清單[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)小工具。

現在，請開啟**MainActivity.cs**並新增下列`using`陳述式：

```csharp
using System;
```

接下來，插入下列程式碼 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "Main" layout resource
    SetContentView (Resource.Layout.Main);

    Spinner spinner = FindViewById<Spinner> (Resource.Id.spinner);

    spinner.ItemSelected += new EventHandler<AdapterView.ItemSelectedEventArgs> (spinner_ItemSelected);
    var adapter = ArrayAdapter.CreateFromResource (
            this, Resource.Array.planets_array, Android.Resource.Layout.SimpleSpinnerItem);

    adapter.SetDropDownViewResource (Android.Resource.Layout.SimpleSpinnerDropDownItem);
    spinner.Adapter = adapter;
}
```

在後`Main.axml`版面配置設定為 [內容] 檢視中， [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)小工具擷取自與版面配置[ `FindViewById<>(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/)。
的 [`CreateFromResource()`](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.CreateFromResource/p/Android.Content.Context/System.Int32/System.Int32/)
方法會建立新[ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)，它會繫結每個項目的字串陣列中的初始外觀[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) （此為每個項目出現在選取時，微調按鈕的方式）. `Resource.Array.planets_array`識別碼參考`string-array`上面所定義和`Android.Resource.Layout.SimpleSpinnerItem`識別碼參考標準微調按鈕的外觀，平台所定義的配置。
[`SetDropDownViewResource`](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.SetDropDownViewResource/p/System.Int32/)
呼叫以開啟小工具時，定義每個項目的外觀。 最後， [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)設定為將所有具有其項目建立關聯[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)藉由設定[ `Adapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter)屬性。

現在提供 notifys 應用程式，從已選取項目時的回呼方法[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)。 這個方法應該看起來如下：

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

選取的項目時，寄件者會轉換成[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)以便可以存取項目。 使用`Position`上的屬性`ItemEventArgs`，了解文字的選取的物件，並用它來顯示[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)。

執行應用程式;它看起來應該像這樣：

[![選取為全球的 mars 微調按鈕的螢幕擷取畫面範例](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>使用索引鍵/值組的微調按鈕

通常就必須使用`Spinner`顯示某種類型的應用程式所使用的資料與相關聯的索引鍵值。 因為`Spinner`無法直接與索引鍵/值組，您必須個別存放區索引鍵/值組中，填入`Spinner`索引鍵值，然後使用微調按鈕中選取的索引鍵的位置來查閱相關聯的資料值。 

在下列步驟中， **HelloSpinner**會修改應用程式，以顯示所選的全球級的平均溫度：

新增下列`using`陳述式來**MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

將下列執行個體變數加入`MainActivity`類別。
此清單中會包含在行星和其平均溫度的索引鍵/值組：

```csharp
private List<KeyValuePair<string, string>> planets;
```

在 `OnCreate`方法中，新增下列程式碼，再`adapter`宣告：

```csharp
planets = new List<KeyValuePair<string, string>>
{
    new KeyValuePair<string, string>("Mercury", "167 degrees C"),
    new KeyValuePair<string, string>("Venus", "464 degrees C"),
    new KeyValuePair<string, string>("Earth", "15 degrees C"),
    new KeyValuePair<string, string>("Mars", "-65 degrees C"),
    new KeyValuePair<string, string>("Jupiter" , "-110 degrees C"),
    new KeyValuePair<string, string>("Saturn", "-140 degrees C"),
    new KeyValuePair<string, string>("Uranus", "-195 degrees C"),
    new KeyValuePair<string, string>("Neptune", "-200 degrees C")
};
```

此程式碼會建立簡單的存放區的星球和其相關聯的平均溫度。 （在真實世界應用程式中，資料庫是通常用來儲存金鑰和其相關聯的資料。）

上述程式碼之後立即, 新增下列行以擷取索引鍵，並將它們放入清單 （依順序）：

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

傳遞至這份清單`ArrayAdapter`建構函式 (而不是`planets_array`資源):

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

修改`spinner_ItemSelected`使選取的位置用來查閱所選的全球級相關聯的值 （溫度）：

```csharp
private void spinner_ItemSelected(object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format("The mean temperature for planet {0} is {1}",
        spinner.GetItemAtPosition(e.Position), planets[e.Position].Value);
    Toast.MakeText(this, toast, ToastLength.Long).Show();
}
```

執行應用程式;快顯通知看起來應該像這樣：

[![顯示溫度的全球選取範圍的範例](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)
   
  

## <a name="resources"></a>資源

-   [`Resource.Layout`](https://developer.xamarin.com/api/type/Android.Resource+Layout/) 
-   [`ArrayAdapter`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) 
-   [`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) 

*此頁面上的部分是根據工作建立及 Android 的開放原始碼專案所共用，並依據所述的條款來使用修改*
[*Creative Commons 2.5 Attribution License*](http://creativecommons.org/licenses/by/2.5/).
