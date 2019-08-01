---
title: Xamarin Android 微調按鈕
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 2c7f0de2347e614b8c24de32bf3f88362a212a94
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510415"
---
# <a name="xamarinandroid-spinner"></a>Xamarin Android 微調按鈕

[`Spinner`](xref:Android.Widget.Spinner)是一個小工具, 會顯示選取專案的下拉式清單。 本指南說明如何建立簡單的應用程式, 以顯示微調框中的選項清單, 然後再進行修改, 以顯示與所選選擇相關聯的其他值。

## <a name="basic-spinner"></a>基本微調

在本教學課程的第一個部分中, 您將建立簡單的微調工具 widget, 以顯示行星清單。 選取地球之後, 快顯訊息會顯示選取的專案:

[![HelloSpinner 應用程式的範例螢幕擷取畫面](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

啟動名為**HelloSpinner**的新專案。

開啟**Resources/Layout/axml** , 並插入下列 XML:

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

請注意, `android:text` [`Spinner`](xref:Android.Widget.Spinner)的屬性和`android:prompt`的屬性都參考相同的字串資源。 [`TextView`](xref:Android.Widget.TextView) 此文字的行為會當做 widget 的標題。 當套用至[`Spinner`](xref:Android.Widget.Spinner)，標題文字會出現在選取小工具時出現的 [選取] 對話方塊。

編輯**Resources/Values/string .xml**並修改檔案, 如下所示:

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

第二`<string>`個元素會定義[`TextView`](xref:Android.Widget.TextView)和[`Spinner`](xref:Android.Widget.Spinner)在上述版面配置中所參考的標題字串。
元素會定義要在[`Spinner`](xref:Android.Widget.Spinner) widget 中顯示為清單的字串清單。 `<string-array>`

現在開啟**MainActivity.cs**並新增下列`using`語句:

```csharp
using System;
```

接下來, 針對[`OnCreate()`](xref:Android.App.Activity.OnCreate*)) 方法插入下列程式碼:

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

在後`Main.axml`版面配置設定為 [內容] 檢視中， [`Spinner`](xref:Android.Widget.Spinner)小工具擷取自與版面配置[`FindViewById<>(int)`](xref:Android.App.Activity.FindViewById*)。
該[`CreateFromResource()`](xref:Android.Widget.ArrayAdapter.CreateFromResource*)
方法接著會建立新[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)的, 它會將字串陣列中的每個專案系結至[`Spinner`](xref:Android.Widget.Spinner)的初始外觀 (這是在選取時, 每個專案在微調框中的顯示方式)。 識別碼會參考上述`string-array`定義的, 而`Android.Resource.Layout.SimpleSpinnerItem`識別碼會參考平臺所定義的標準微調外觀版面配置。 `Resource.Array.planets_array`
[`SetDropDownViewResource`](xref:Android.Widget.ArrayAdapter.SetDropDownViewResource*)
呼叫以定義開啟小工具時, 每個專案的外觀。 最後, [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)設定[`Adapter`](xref:Android.Widget.ArrayAdapter)屬性以將其所有專案[`Spinner`](xref:Android.Widget.Spinner)與產生關聯。

現在提供回呼方法, 以便在從[`Spinner`](xref:Android.Widget.Spinner)選取專案時 notifys 應用程式。 此方法看起來應該像這樣:

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

選取專案時, 傳送者會轉換成[`Spinner`](xref:Android.Widget.Spinner) , 以便存取專案。 使用`Position` [`Toast`](xref:Android.Widget.Toast)上的屬性,您可以找出所選取物件的文字,並使用它來顯示。`ItemEventArgs`

執行應用程式;看起來應該像這樣:

[![已選取 Mars 做為地球的微調按鈕的螢幕擷取畫面範例](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>使用索引鍵/值配對的微調

通常必須使用`Spinner`來顯示與您的應用程式所使用的某種資料相關聯的索引鍵值。 因為`Spinner`無法直接使用索引鍵/值組, 所以您必須分別儲存索引鍵/值組、在`Spinner`中填入索引鍵值, 然後使用微調框中所選取索引鍵的位置來查詢相關聯的資料值。 

在下列步驟中, 會修改**HelloSpinner**應用程式, 以顯示所選地球的平均溫度:

將下列`using`語句新增至**MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

將下列執行個體變數新增至`MainActivity`類別。
這份清單會保存行星的索引鍵/值組和其平均溫度:

```csharp
private List<KeyValuePair<string, string>> planets;
```

在方法中, 在宣告之前`adapter`加入下列程式碼: `OnCreate`

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

此程式碼會為行星和其相關聯的平均溫度建立簡單的存放區。 (在真實世界的應用程式中, 通常會使用資料庫來儲存金鑰和其相關聯的資料)。

緊接在上述程式碼之後, 新增下列幾行以將金鑰解壓縮, 並將其放入清單中 (依序):

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

將此清單傳遞給`ArrayAdapter`此函式 (而`planets_array`不是資源):

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

修改`spinner_ItemSelected` , 讓選取的位置用來查閱與所選地球相關聯的值 (溫度):

```csharp
private void spinner_ItemSelected(object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format("The mean temperature for planet {0} is {1}",
        spinner.GetItemAtPosition(e.Position), planets[e.Position].Value);
    Toast.MakeText(this, toast, ToastLength.Long).Show();
}
```

執行應用程式;快顯看起來應該像這樣:

[![顯示溫度的地球選取範例](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)

## <a name="resources"></a>資源

- [`Resource.Layout`](xref:Android.Resource.Layout)
- [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
- [`Spinner`](xref:Android.Widget.Spinner)

*此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改, 並根據*
[*創意 Commons 2.5 屬性授權*](http://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。
