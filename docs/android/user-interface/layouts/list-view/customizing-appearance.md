---
title: "自訂 ListView 的外觀"
ms.topic: article
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 18c53ed6428eff911420c696d45b341d8e0fa5c1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="customizing-a-listviews-appearance"></a>自訂 ListView 的外觀

<a name="overview" />

## <a name="overview"></a>總覽

ListView 的外觀取決於所顯示的資料列的配置。 若要變更的外觀`ListView`，使用不同的資料列配置。

<a name="Built-in_Row_Views" />

## <a name="built-in-row-views"></a>內建的資料列檢視

有 12 個內建的檢視，可以使用參考**Android.Resource.Layout**:

- **TestListItem** &ndash;單行文字，最少的格式。

- **SimpleListItem1** &ndash;單行文字。

- **SimpleListItem2** &ndash;兩行文字。

- **SimpleSelectableListItem** &ndash;單行支援 （新增應用程式開發介面層級 11） 的單一或多個項目選取的文字。

- **SimpleListItemActivated1** &ndash; SimpleListItem1，與類似，但是背景色彩表示當選取資料列 （新增應用程式開發介面層級 11）。

- **SimpleListItemActivated2** &ndash; SimpleListItem2，與類似，但是背景色彩表示當選取資料列 （新增應用程式開發介面層級 11）。

- **SimpleListItemChecked** &ndash;顯示核取記號，表示選取範圍。

- **SimpleListItemMultipleChoice** &ndash;顯示核取方塊，以指出多重選取項目。

- **SimpleListItemSingleChoice** &ndash;顯示選項按鈕來顯示互斥選項。

- **TwoLineListItem** &ndash;兩行文字。

- **ActivityListItem** &ndash;單行文字與映像。

- **SimpleExpandableListItem** &ndash;可以展開或摺疊類別目錄，而每個群組的群組資料列。

每個內建的資料列檢視都有與其相關聯的內建的樣式。 這些螢幕擷取畫面會顯示每個檢視的顯示方式：

[![TestListItem、 SimpleSelectableListItem、 SimpleListitem1 和 SimpleListItem2 的螢幕擷取畫面](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png)

[![SimpleListItemActivated1、 SimpleListItemActivated2、 SimpleListItemChecked 和 SimpleListItemMultipleChecked 的螢幕擷取畫面](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png)

[![SimpleListItemSingleChoice、 TwoLineListItem、 ActivityListItem 和 SimpleExpandableListItem 的螢幕擷取畫面](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png)

**BuiltInViews/HomeScreenAdapter.cs**範例檔案 (在**BuiltInViews**方案) 包含要產生的非可展開清單項目畫面的程式碼。 在檢視中設定`GetView`方法如下：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

檢視的屬性可以設定所參考的標準控制項識別碼`Text1`，`Text2`和`Icon`下`Android.Resource.Id`（未設定屬性的檢視不包含或將會擲回例外狀況）：

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

**BuiltInExpandableViews/ExpandableScreenAdapter.cs**範例檔案 (在**BuiltInViews**方案) 包含要產生 SimpleExpandableListItem 螢幕的程式碼。 [群組] 檢視中設定`GetGroupView`方法如下：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

設定子檢視`GetChildView`方法如下：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

[群組] 檢視和子檢視的屬性可以設定參考標準`Text1`和`Text2`控制識別項，如上所示。 SimpleExpandableListItem 螢幕擷取畫面 （如上所示） 提供一列的群組檢視 (SimpleExpandableListItem1) 和兩行子檢視 (SimpleExpandableListItem2) 的範例。 或者，可以設定兩行程式碼 (SimpleExpandableListItem2) 的群組檢視和子檢視可以設定的一行 (SimpleExpandableListItem1)，或群組檢視和子檢視可以有相同的行數。 


<a name="Accessories" />

## <a name="accessories"></a>附屬應用程式

資料列可以具有附屬應用程式加入至檢視的權限，表示選取狀態：

- **SimpleListItemChecked** &ndash;建立單一選取清單進行檢查，以當做指標。

- **SimpleListItemSingleChoice** &ndash;建立其中只有一個選擇是可能的選項按鈕類型清單。

- **SimpleListItemMultipleChoice** &ndash;建立多個選項可能會核取方塊類型清單。

在下列畫面中，在其各自的順序說明上述的附屬應用程式：

[![螢幕擷取畫面的 SimpleListItemChecked、 SimpleListItemSingleChoice 和 SimpleListItemMultipleChoice 與附屬應用程式](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png)

若要顯示其中一個這些附屬應用程式傳遞至配接器的必要的配置資源識別碼然後手動設定需要的資料列的選取狀態。 這行程式碼示範如何建立並指派`Adapter`使用其中一種配置：

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

`ListView`本身支援不同的選取模式，不論所顯示的裝飾。 若要避免混淆，請使用`Single`具有選取範圍模式`Checked`和`SingleChoice`附屬應用程式和`Multiple`模式搭配`MultipleChoice`樣式。 選取模式由`ChoiceMode`屬性`ListView`。

<a name="Handling_API_Level" />

### <a name="handling-api-level"></a>處理應用程式開發介面層級

舊版的 Xamarin.Android 實作為整數屬性的列舉型別。 最新版本導入了適當的.NET 列舉型別讓它更容易找出可能的選項。

應用程式開發介面層級根據您的目標，`ChoiceMode`是整數或列舉型別。 範例檔案**AccessoryViews/HomeScreen.cs**有區塊標記為註解如果您想要以 Gingerbread API 為目標：

```csharp
// For targeting Gingerbread the ChoiceMode is an int, otherwise it is an
// enumeration.

lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // 1
//lv.ChoiceMode = Android.Widget.ChoiceMode.Multiple; // 2
//lv.ChoiceMode = Android.Widget.ChoiceMode.None; // 0

// Use this block if targeting Gingerbread or lower
/*
lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // Single
//lv.ChoiceMode = 0; // none
//lv.ChoiceMode = 2; // Multiple
//lv.ChoiceMode = 3; // MultipleModal
*/
```

<a name="Selecting_Items_Programmatically" />

### <a name="selecting-items-programmatically"></a>以程式設計方式選取項目

手動設定的項目 '選取' 透過`SetItemChecked`（它可以多次呼叫的多個選取項目） 的方法：

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

該程式碼也必須偵測單一選取不同的多個選取項目。 若要判斷哪一個資料列中已選取`Single`模式使用`CheckedItemPosition`整數屬性：

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

若要判斷哪些資料列中已選取`Multiple`您要循環模式`CheckedItemPositions` `SparseBooleanArray`。 疏鬆陣列就像是字典，其中只包含項目位置的值已變更，因此您必須周遊整個陣列以尋找`true`知道已選取的內容清單中的下列程式碼片段所示的值：

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```

<a name="Creating_Custom_Row_Layouts" />

## <a name="creating-custom-row-layouts"></a>建立自訂的資料列版面配置

四個內建的資料列檢視就會非常簡單。 若要顯示更複雜的配置 （例如電子郵件，或推文或連絡資訊的清單） 的自訂檢視。 自訂檢視通常會宣告為 AXML 檔案**資源/配置**目錄中，而且再載入使用其資源的自訂配接器的識別碼。 檢視表可以包含任意數目的顯示類別 （例如 TextViews、 ImageViews 和其他控制項），使用自訂色彩、 字型和配置。

此範例與前面的範例數種方式：

-  繼承自`Activity`，而非`ListActivity`。 您可以自訂的資料列的任何`ListView`，不過，其他控制項也會包含在`Activity`配置 （例如標題、 按鈕或其他使用者介面項目）。 這個範例會將上述標題`ListView`來說明。

-  需要 AXML 配置檔案的螢幕。在上述範例`ListActivity`不需要配置檔案。 包含此 AXML`ListView`控制宣告。

-  需要 AXML 配置檔案來呈現每個資料列。 這個 AXML 檔案包含自訂字型和色彩設定的文字和影像控制項。

-  設定資料列的外觀，當選取時，會使用選擇性的自訂選取器 XML 檔案。

-  `Adapter`實作會傳回從自訂版面配置`GetView`覆寫。

-  `ItemClick` 必須以不同的方式宣告 (事件處理常式附加至`ListView.ItemClick`而不是覆寫`OnListItemClick`中`ListActivity`)。


這些變更如下所述，開始建立活動的檢視和自訂資料列檢視，然後涵蓋修改配接器與活動加以轉譯。

<a name="Adding_a_ListView_to_an_Activity_Layout" />

### <a name="adding-a-listview-to-an-activity-layout"></a>加入活動配置的 ListView

因為`HomeScreen`不再繼承自`ListActivity`它沒有預設檢視中，因此必須建立配置 AXML 檔案 HomeScreen 的檢視。 針對此範例中，檢視會有標題 (使用`TextView`) 和`ListView`來顯示資料。 版面配置中定義**Resources/Layout/HomeScreen.axml**檔案如下所示：

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

使用的好處`Activity`與自訂版面配置 (而不是`ListActivity`) 在於能夠將其他控制項加入至畫面中，例如標題`TextView`在此範例中。

<a name="Creating_a_Custom_Row_Layout" />

### <a name="creating-a-custom-row-layout"></a>建立自訂的資料列版面配置

另一個 AXML 配置檔案，才能包含每個資料列會出現在清單檢視中的自訂配置。 在此範例中的資料列都有綠色背景、 棕色文字以及靠右對齊的映像。 Android 的 XML 標記宣告這個配置述**Resources/Layout/CustomView.axml**:

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

雖然自訂資料列配置可以包含許多不同的控制項，捲動效能可能會受到複雜的設計，並使用映像 （尤其是當它們有要載入透過網路）。 在處理捲動效能問題，請參閱 Google 的文件，如需詳細資訊。

<a name="Referencing_a_Custom_Row_View" />

### <a name="referencing-a-custom-row-view"></a>參考自訂資料列檢視

實作自訂配接器範例處於`HomeScreenAdapter.cs`。 主要方法是`GetView`它會載入使用的資源識別碼的自訂 AXML `Resource.Layout.CustomView`，然後在每個再加以傳回檢視的控制項上設定屬性。 顯示完整的配接器類別：

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

<a name="Referencing_the_Custom_ListView_in_the_Activity" />

### <a name="referencing-the-custom-listview-in-the-activity"></a>參考自訂活動中 ListView

因為`HomeScreen`類別會繼承自`Activity`、`ListView`來保存 AXML 中宣告控制項的參考類別中宣告欄位：

```csharp
ListView listView;
```

此類別必須再載入活動的自訂版面配置 AXML 使用`SetContentView`方法。 它可以找到`ListView`版面配置中的控制項接著會建立和指派配接器並指派 click 處理常式。 OnCreate 方法的程式碼如下所示：

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

最後`ItemClick`必須定義處理常式; 在此情況下它只顯示`Toast`訊息：

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

產生螢幕看起來像這樣：

[![產生 CustomRowView 的螢幕擷取畫面](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png)


<a name="Customizing_the_Row_Selector_Color" />

### <a name="customizing-the-row-selector-color"></a>自訂的資料列選取器的色彩

接觸到一個資料列是它應該會反白顯示的使用者意見反應。 自訂檢視時指定為與背景色彩**CustomView.axml** ，它也會覆寫反白顯示的選取項目。 這行程式碼中**CustomView.axml**背景以淺綠色，但它也表示沒有任何視覺化指標時已觸及的資料列集：

```xml
android:background="#FFDAFF7F"
```

若要重新啟用醒目提示的行為，以及來自訂用的色彩，將背景屬性設定為自訂的選取器改為。 預設背景色彩以及反白顯示色彩，會宣告選取器。 檔案**Resources/Drawable/CustomSelector.xml**包含下列宣告：

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

若要參考自訂選取器，變更中的背景屬性**CustomView.axml**至：

```xml
android:background="@drawable/CustomSelector"
```

選取的資料列和對應`Toast`訊息現在看起來像這樣：

[![選取的資料列，以橙色，並顯示所選資料列的名稱的快顯通知訊息](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png)


<a name="Preventing_Flickering_on_Custom_Layouts" />

### <a name="preventing-flickering-on-custom-layouts"></a>防止閃爍自訂版面配置上

改善的效能嘗試 android`ListView`捲動藉由快取配置資訊。 如果您有長時間捲動的資料清單您也應該設定`android:cacheColorHint`屬性`ListView`活動的 AXML 定義 （以相同的色彩值做為自訂的資料列版面配置的背景） 中的宣告。 無法包含這個提示可能導致 '重繪閃動' 捲動透過自訂的資料列的背景色彩的清單。



## <a name="related-links"></a>相關連結

- [BuiltInViews （範例）](https://developer.xamarin.com/samples/BuiltInViews/)
- [AccessoryViews （範例）](https://developer.xamarin.com/samples/AccessoryViews/)
- [CustomRowView （範例）](https://developer.xamarin.com/samples/CustomRowView/)
