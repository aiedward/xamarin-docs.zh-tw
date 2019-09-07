---
title: 自訂 ListView 的外觀
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: 2787e814d330bf8262ba05e38c7827211e07fd72
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764251"
---
# <a name="customizing-a-listviews-appearance-with-xamarinandroid"></a>使用 Xamarin 自訂 ListView 的外觀

ListView 的外觀取決於所顯示的資料列配置。 若要變更的外觀`ListView`，請使用不同的資料列版面配置。

## <a name="built-in-row-views"></a>內建資料列視圖

有12個內建的視圖，可以使用**Android. Layout**來參考：

- **TestListItem**&ndash;具有最少格式的一行文字。

- **SimpleListItem1**&ndash;一行文字。

- **SimpleListItem2**&ndash;兩行文字。

- **SimpleSelectableListItem**&ndash;支援單一或多個專案選擇（在 API 層級11中新增）的單行文字。

- **SimpleListItemActivated1**&ndash;類似于 SimpleListItem1，但背景色彩會指出何時選取資料列（在 API 層級11中新增）。

- **SimpleListItemActivated2**&ndash;類似于 SimpleListItem2，但背景色彩會指出何時選取資料列（在 API 層級11中新增）。

- **SimpleListItemChecked**&ndash;顯示核取記號以指出選取範圍。

- **SimpleListItemMultipleChoice**&ndash;顯示覆選框以指出多重選取選擇。

- **SimpleListItemSingleChoice**&ndash;顯示選項按鈕，表示互斥的選取範圍。

- **TwoLineListItem**&ndash;兩行文字。

- **ActivityListItem**&ndash;含有影像的一行文字。

- **SimpleExpandableListItem**&ndash;依分類群組資料列，而且每個群組都可以展開或折迭。

每個內建的資料列視圖都有與其相關聯的內建樣式。 這些螢幕擷取畫面顯示每個視圖的顯示方式：

[![TestListItem、SimpleSelectableListItem、SimpleListitem1 和 SimpleListItem2 的螢幕擷取畫面](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![SimpleListItemActivated1、SimpleListItemActivated2、SimpleListItemChecked 和 SimpleListItemMultipleChecked 的螢幕擷取畫面](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![SimpleListItemSingleChoice、TwoLineListItem、ActivityListItem 和 SimpleExpandableListItem 的螢幕擷取畫面](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

**BuiltInViews/HomeScreenAdapter**範例檔案（在**BuiltInViews**方案中）包含的程式碼可產生無法展開的清單專案畫面。 此視圖會在`GetView`方法中設定，如下所示：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

然後，您可以`Text1`參考標準控制項識別碼來設定視圖的屬性， `Text2`並`Icon`在`Android.Resource.Id` （不設定視圖不包含或將擲回例外狀況的屬性）底下：

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

**BuiltInExpandableViews/ExpandableScreenAdapter .cs**範例檔案（在**BuiltInViews**方案中）包含產生 SimpleExpandableListItem 畫面的程式碼。 群組視圖會在`GetGroupView`方法中設定，如下所示：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

子視圖會在`GetChildView`方法中設定，如下所示：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

接著，您可以參考標準`Text1`和`Text2`控制項識別碼來設定群組視圖和子視圖的屬性，如上所示。 SimpleExpandableListItem 螢幕擷取畫面（如上所示）提供單行群組視圖（SimpleExpandableListItem1）和兩行子視圖（SimpleExpandableListItem2）的範例。 或者，您可以針對兩行（SimpleExpandableListItem2）設定 [群組] 視圖，而子視圖可以設定為一行（SimpleExpandableListItem1），或者 [群組視圖] 和 [子視圖] 可以有相同的行數。 

## <a name="accessories"></a>附屬

資料列可以在此視圖的右方新增附屬物，以指示選取狀態：

- **SimpleListItemChecked**&ndash;建立具有核取記號的單一選取清單。

- **SimpleListItemSingleChoice**&ndash;建立選項按鈕類型清單，其中只能有一個選項。

- **SimpleListItemMultipleChoice**&ndash;建立核取方塊類型清單，其中可能會有多個選項可供選擇。

下列畫面以其各自的順序說明上述的配件：

[![SimpleListItemChecked、SimpleListItemSingleChoice 和 SimpleListItemMultipleChoice 與配件的螢幕擷取畫面](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

若要顯示這些配件的其中一個，請將所需的配置資源識別碼傳遞至介面卡，然後手動設定所需資料列的選取狀態。 這行程式`Adapter`代碼會示範如何使用下列其中一個版面配置來建立和指派：

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

`ListView`本身支援不同的選取模式，而不論所顯示的配件為何。 為避免混淆，請`Single`搭配使用 [ `SingleChoice`選擇模式] `Checked`與 [配件] `MultipleChoice` ，並將或`Multiple`模式用於樣式。 選取模式是由`ChoiceMode`的屬性`ListView`所控制。

### <a name="handling-api-level"></a>處理 API 層級

舊版的 Xamarin 會將列舉實作為整數屬性。 最新版本已引進適當的 .NET 列舉類型，可讓您更輕鬆地探索可能的選項。

根據您的目標 API 層級而定`ChoiceMode` ，可能是整數或列舉。 如果您想要以 Gingerbread API 為目標，範例檔案**AccessoryViews/HomeScreen**會將區塊標記為批註：

```csharp
// For targeting Gingerbread the ChoiceMode is an int, otherwise it is an
// enumeration.

lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // 1
//lv.ChoiceMode = Android.Widget.ChoiceMode.Multiple; // 2
//lv.ChoiceMode = Android.Widget.ChoiceMode.None; // 0

// Use this block if targeting Gingerbread or lower
/*
lv.ChoiceMode = 1; // Single
//lv.ChoiceMode = 0; // none
//lv.ChoiceMode = 2; // Multiple
//lv.ChoiceMode = 3; // MultipleModal
*/
```

### <a name="selecting-items-programmatically"></a>以程式設計方式選取專案

使用`SetItemChecked`方法來手動設定要「選取」的專案（可針對多重選取多次呼叫）：

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

程式碼也必須以不同于多個選取專案的方式來偵測單一選取專案。 若要判斷已在模式中`Single`選取的資料列， `CheckedItemPosition`請使用整數屬性：

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

若要判斷已在模式中選取`Multiple`的資料列，您需要在`CheckedItemPositions`中`SparseBooleanArray`執行迴圈。 稀疏陣列就像是只包含值已變更之專案的字典，因此您必須流覽整個陣列以尋找`true`值，以瞭解清單中所選取的內容，如下列程式碼片段所示：

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```

## <a name="creating-custom-row-layouts"></a>建立自訂的資料列版面配置

四個內建的資料列視圖非常簡單。 若要顯示更複雜的版面配置（例如電子郵件清單、推文或連絡人資訊），則需要自訂視圖。 自訂視圖通常會在**Resources/Layout**目錄中宣告為 AXML 檔案，然後使用自訂介面卡來載入其資源識別碼。 此視圖可以包含任意數目的顯示類別（例如 Textview、ImageViews 和其他控制項），其具有自訂色彩、字型和版面配置。

這個範例與先前的範例有幾種不同：

- 繼承自`Activity` ，而`ListActivity`不是。 您可以自訂任何`ListView`專案的資料列，但其他控制項也可以包含`Activity`在配置中（例如標題、按鈕或其他使用者介面元素）。 這個範例會在`ListView`上方加入一個標題來說明。

- 需要畫面的 AXML 版面配置檔案;在先前的範例中`ListActivity` ，不需要版面配置檔案。 這個 AXML 包含`ListView`控制項宣告。

- 需要 AXML 版面配置檔案才能轉譯每個資料列。 這個 AXML 檔包含具有自訂字型和色彩設定的文字和影像控制項。

- 使用選擇性的自訂選取器 XML 檔案，在選取資料列時設定其外觀。

- 此`Adapter`實作為`GetView`從覆寫傳回自訂的版面配置。

- `ItemClick`必須以不同的方式宣告（事件處理常式已`ListView.ItemClick`附加至，而`OnListItemClick`不`ListActivity`是中的覆寫）。

這些變更詳述如下，從建立活動的視圖和自訂資料列視圖開始，然後涵蓋介面卡和活動的修改來呈現它們。

### <a name="adding-a-listview-to-an-activity-layout"></a>將 ListView 加入至活動版面配置

因為`HomeScreen`不再從`ListActivity`它繼承，所以不會有預設的視圖，因此必須為 HomeScreen 的視圖建立版面配置 AXML 檔。 在此範例中，此視圖會有一個標題（使用`TextView`） `ListView`和以顯示資料。 版面配置定義于**Resources/layout/HomeScreen. axml**檔案中，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent">
    <TextView android:id="@+id/Heading"
        android:text="Vegetable Groups"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="#00000000"
        android:textSize="30dp"
        android:textColor="#FF267F00"
        android:textStyle="bold"
        android:padding="5dp"
    />
    <ListView android:id="@+id/List"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:cacheColorHint="#FFDAFF7F"
    />
</LinearLayout>
```

使用`Activity`搭配自訂配置（而不是`ListActivity`）的優點是能夠在螢幕上新增其他控制項，例如此範例中的標題`TextView` 。

### <a name="creating-a-custom-row-layout"></a>建立自訂的資料列版面配置

另一個 AXML 配置檔案必須包含將出現在清單視圖中的每個資料列的自訂配置。 在此範例中，資料列會有綠色的背景、棕色文字和靠右對齊的影像。 用來宣告此配置的 Android XML 標記會在**Resources/layout/CustomView. axml**中說明：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout  xmlns:android="http://schemas.android.com/apk/res/android"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
   android:background="#FFDAFF7F"
   android:padding="8dp">
    <LinearLayout android:id="@+id/Text"
       android:orientation="vertical"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:paddingLeft="10dip">
        <TextView
         android:id="@+id/Text1"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textColor="#FF7F3300"
         android:textSize="20dip"
         android:textStyle="italic"
         />
        <TextView
         android:id="@+id/Text2"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textSize="14dip"
         android:textColor="#FF267F00"
         android:paddingLeft="100dip"
         />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout >
```

雖然自訂資料列配置可以包含許多不同的控制項，但滾動效能可能會受到複雜設計和使用影像的影響（尤其是必須透過網路載入）。 如需解決滾動效能問題的詳細資訊，請參閱 Google 的文章。

### <a name="referencing-a-custom-row-view"></a>參考自訂資料列視圖

自訂介面卡範例的執行是在`HomeScreenAdapter.cs`中。 主要方法是`GetView`使用資源識別碼`Resource.Layout.CustomView`載入自訂 AXML 的位置，然後在傳回視圖中的每個控制項上設定屬性，然後再將其傳回。 會顯示完整的介面卡類別：

```csharp
public class HomeScreenAdapter : BaseAdapter<TableItem> {
   List<TableItem> items;
   Activity context;
   public HomeScreenAdapter(Activity context, List<TableItem> items)
       : base()
   {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
   {
       return position;
   }
   public override TableItem this[int position]
   {
       get { return items[position]; }
   }
   public override int Count
   {
       get { return items.Count; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       var item = items[position];
       View view = convertView;
       if (view == null) // no view to re-use, create new
           view = context.LayoutInflater.Inflate(Resource.Layout.CustomView, null);
       view.FindViewById<TextView>(Resource.Id.Text1).Text = item.Heading;
       view.FindViewById<TextView>(Resource.Id.Text2).Text = item.SubHeading;
       view.FindViewById<ImageView>(Resource.Id.Image).SetImageResource(item.ImageResourceId);
       return view;
   }
}
```

### <a name="referencing-the-custom-listview-in-the-activity"></a>參考活動中的自訂 ListView

因為類別現在會繼承自`Activity`， `ListView`所以會在類別中宣告欄位，以保存 AXML 中所宣告之控制項的參考： `HomeScreen`

```csharp
ListView listView;
```

然後，類別就必須使用`SetContentView`方法來載入活動的自訂版面配置 AXML。 然後，它可以在`ListView`版面配置中尋找控制項，然後建立並指派介面卡，然後指派 click 處理常式。 OnCreate 方法的程式碼如下所示：

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

最後， `ItemClick`必須定義處理常式; 在此情況下，它只`Toast`會顯示訊息：

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

產生的畫面看起來像這樣：

[![產生之 CustomRowView 的螢幕擷取畫面](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png#lightbox)

### <a name="customizing-the-row-selector-color"></a>自訂資料列選取器色彩

觸及資料列時，應該將其反白顯示給使用者意見反應。 當自訂視圖將**CustomView**指定為背景色彩時，它也會覆寫選取專案醒目提示。 **CustomView**中的這行程式碼會將背景設定為淺綠色，但這也表示觸及資料列時，沒有任何視覺指標：

```xml
android:background="#FFDAFF7F"
```

若要重新啟用反白顯示行為，並自訂所使用的色彩，請改為將背景屬性設定為自訂選取器。 選取器會同時宣告預設背景色彩和反白顯示色彩。 File **Resources/CustomSelector/xml**包含下列宣告：

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
<item android:state_pressed="false"
  android:state_selected="false"
  android:drawable="@color/cellback" />
<item android:state_pressed="true" >
  <shape>
     <gradient
      android:startColor="#E77A26"
        android:endColor="#E77A26"
        android:angle="270" />
  </shape>
</item>
<item android:state_selected="true"
  android:state_pressed="false"
  android:drawable="@color/cellback" />
</selector>
```

若要參考自訂選取器，請將**CustomView. axml**中的 background 屬性變更為：

```xml
android:background="@drawable/CustomSelector"
```

選取的資料列和對應`Toast`的訊息現在如下所示：

[![以橙色顯示的選取資料列，其中包含所選資料列名稱的快顯訊息](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)

### <a name="preventing-flickering-on-custom-layouts"></a>防止自訂版面配置閃爍

Android 會嘗試藉由快取`ListView`版面配置資訊來改善滾動的效能。 如果您有長時間的資料滾動清單，您也應該`android:cacheColorHint`在活動的`ListView` AXML 定義中設定宣告的屬性（與自訂資料列配置的背景相同的色彩值）。 如果無法包含此提示，可能會在使用者以自訂資料列背景色彩來滾動清單時，產生「閃爍」。

## <a name="related-links"></a>相關連結

- [BuiltInViews （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/builtinviews)
- [AccessoryViews （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/accessoryviews)
- [CustomRowView （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/customrowview)
