---
title: 自訂 ListView 的外觀
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: 41b4bb25aa170abeb58a92058f4bfb8b78af35ad
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457233"
---
# <a name="customizing-a-listviews-appearance-with-xamarinandroid"></a>使用 Xamarin 自訂 ListView 的外觀

ListView 的外觀是由所顯示之資料列的版面配置所決定。 若要變更的外觀 `ListView` ，請使用不同的資料列版面配置。

## <a name="built-in-row-views"></a>內建的資料列視圖

有十二個內建的視圖可使用 Android 進行參考 **。版面**配置：

- **TestListItem** &ndash; 具有基本格式的單行文字。

- **SimpleListItem1** &ndash; 一行文字。

- **SimpleListItem2** &ndash; 兩行文字。

- **SimpleSelectableListItem** &ndash; 支援單一或多個專案選取 (新增至 API 層級 11) 的單行文字。

- **SimpleListItemActivated1** &ndash; 類似于 SimpleListItem1，但背景色彩表示選取了資料列 (在 API 層級 11) 中新增。

- **SimpleListItemActivated2** &ndash; 類似于 SimpleListItem2，但背景色彩表示選取了資料列 (在 API 層級 11) 中新增。

- **SimpleListItemChecked** &ndash; 顯示核取記號表示選取專案。

- **SimpleListItemMultipleChoice** &ndash; 顯示覆選框以表示多重選取選項。

- **SimpleListItemSingleChoice** &ndash; 顯示選項按鈕以指出互斥的選取範圍。

- **TwoLineListItem** &ndash; 兩行文字。

- **ActivityListItem** &ndash; 具有影像的單行文字。

- **SimpleExpandableListItem** &ndash; 依分類分組資料列，而且每個群組都可以展開或折迭。

每個內建的資料列視圖都有與其相關聯的內建樣式。 這些螢幕擷取畫面顯示每個視圖的顯示方式：

[![TestListItem、SimpleSelectableListItem、SimpleListitem1 和 SimpleListItem2 的螢幕擷取畫面](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![SimpleListItemActivated1、SimpleListItemActivated2、SimpleListItemChecked 和 SimpleListItemMultipleChecked 的螢幕擷取畫面](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![SimpleListItemSingleChoice、TwoLineListItem、ActivityListItem 和 SimpleExpandableListItem 的螢幕擷取畫面](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

**BuiltInViews**方案中的**BuiltInViews/HomeScreenAdapter .cs**範例檔 () 包含產生不可展開清單專案螢幕的程式碼。 此視圖會在方法中設定，如下所示 `GetView` ：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

然後，您可以藉由參考標準控制項識別碼來設定該視圖的屬性 `Text1` ， `Text2` 並 `Icon` 在 [ (未 `Android.Resource.Id` 設定該視圖不包含的屬性，或將擲回例外狀況) ：

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

**BuiltInViews**方案中的**BuiltInExpandableViews/ExpandableScreenAdapter .cs**範例檔 () 包含產生 SimpleExpandableListItem 畫面的程式碼。 群組視圖是在方法中設定，如下所示 `GetGroupView` ：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

子視圖是在方法中設定，如下所示 `GetChildView` ：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

接著，您可以參考標準和控制項識別碼來設定群組視圖和子視圖的屬性，如上 `Text1` `Text2` 所示。 上方顯示的 SimpleExpandableListItem 螢幕擷取畫面 () 提供單行群組視圖的範例 (SimpleExpandableListItem1) 以及 (SimpleExpandableListItem2) 的雙線子視圖。 或者，您可以將群組視圖設定為兩行 (SimpleExpandableListItem2) ，而子視圖可設定為一行 (SimpleExpandableListItem1) ，或是群組視圖和子視圖可以有相同的行數。 

## <a name="accessories"></a>Accessories

資料列可以將附屬項加入至 view 右邊，以指出選取狀態：

- **SimpleListItemChecked** &ndash; 使用核取記號來建立單一選取清單。

- **SimpleListItemSingleChoice** &ndash; 建立只能有一個選擇的選項按鈕類型清單。

- **SimpleListItemMultipleChoice** &ndash; 建立核取方塊類型清單，其中可能有多個選項可供選擇。

下列畫面將依各自的順序來說明上述的配件：

[![SimpleListItemChecked、SimpleListItemSingleChoice 和 SimpleListItemMultipleChoice 與配件的螢幕擷取畫面](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

若要顯示其中一個配件，請將所需的版面配置資源識別碼傳遞至介面卡，然後手動設定所需資料列的選取狀態。 這行程式碼會示範如何 `Adapter` 使用下列其中一個版面配置來建立和指派：

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

`ListView`它本身支援不同的選取模式，不論顯示的配件為何。 為了避免混淆，請使用 `Single` 選取模式搭配 `SingleChoice` 配件和 `Checked` 或 `Multiple` 模式搭配 `MultipleChoice` 樣式。 選取模式是由的屬性所控制 `ChoiceMode` `ListView` 。

### <a name="handling-api-level"></a>處理 API 層級

舊版 Xamarin. Android 將列舉實作為整數屬性。 最新版本引進了適當的 .NET 列舉型別，讓您更容易找到可能的選項。

視您的目標 API 層級而定， `ChoiceMode` 它可能是整數或列舉。 如果您想要將目標設為 Gingerbread API， **AccessoryViews/HomeScreen** 範例檔案會將區塊標記為批註：

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

手動設定 [已選取] 使用方法完成的專案 `SetItemChecked` (可以針對多重選取) 多次呼叫：

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

程式碼也必須偵測單一選取專案的方式不同于多重選取專案。 若要判斷在模式中選取的資料列， `Single` 請使用 `CheckedItemPosition` 整數屬性：

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

若要判斷哪些資料列已在 `Multiple` 模式中選取，您需要在執行迴圈 `CheckedItemPositions` `SparseBooleanArray` 。 稀疏陣列就像只包含值已變更之專案的字典，因此您必須在尋找值的整個陣列中取得值， `true` 以瞭解已在清單中選取的專案，如下列程式碼片段所示：

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```

## <a name="creating-custom-row-layouts"></a>建立自訂資料列版面配置

四個內建的資料列視圖非常簡單。 若要顯示更複雜的版面配置 (例如電子郵件清單、推文或連絡人資訊) 需要自訂視圖。 自訂視圖通常會在 **Resources/Layout** 目錄中宣告為 .axml 檔案，然後使用自訂介面卡以其資源識別碼載入。 此視圖可包含任意數目的顯示類別 (例如 Textview、ImageViews，以及自訂色彩、字型和版面配置) 的其他控制項。

此範例與先前的範例不同，有許多方法：

- 繼承自 `Activity` ，而不是 `ListActivity` 。 您可以自訂任何資料列 `ListView` ，不過，其他控制項也可以包含在 `Activity` 版面配置 (例如標題、按鈕或其他使用者介面元素) 。 這個範例會在的上方加入標題 `ListView` ，以說明。

- 需要畫面的 .AXML 版面配置檔案;在先前的範例中，不 `ListActivity` 需要版面配置檔案。 這個 .AXML 包含 `ListView` 控制項宣告。

- 需要 .AXML 版面配置檔案才能轉譯每個資料列。 這個 .AXML 檔包含具有自訂字型和色彩設定的文字和影像控制項。

- 使用選擇性的自訂選取器 XML 檔案，在選取資料列時設定其外觀。

- `Adapter`執行會從覆寫傳回自訂版面配置 `GetView` 。

- `ItemClick` 必須以不同的方式宣告 (將事件處理常式附加至， `ListView.ItemClick` 而不是) 中的覆寫  `OnListItemClick` `ListActivity` 。

這些變更的詳細說明，從建立活動的觀點和自訂資料列視圖，然後涵蓋對介面卡和活動的修改來呈現它們。

### <a name="adding-a-listview-to-an-activity-layout"></a>將 ListView 新增至活動配置

因為 `HomeScreen` 不再繼承自 `ListActivity` 它沒有預設視圖，所以必須為 HomeScreen 的視圖建立版面配置 .axml 檔。 在此範例中，視圖會有標題 (使用 `TextView`) 和 `ListView` 來顯示資料。 版面配置定義于 **Resources/layout/HomeScreen .axml** 檔案中，如下所示：

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

使用 `Activity` 與自訂配置 (而不是) 的優點 `ListActivity` 在於能夠將其他控制項新增至畫面，例如 `TextView` 此範例中的標題。

### <a name="creating-a-custom-row-layout"></a>建立自訂資料列版面配置

另一個 .AXML 版面配置檔案必須包含將出現在清單視圖中的每個資料列的自訂版面配置。 在此範例中，資料列會有綠色背景、棕色文字和靠右對齊的影像。 要宣告此配置的 Android XML 標記會在 **Resources/layout/CustomView 中說明。 .axml**：

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

雖然自訂資料列版面配置可以包含許多不同的控制項，但是滾動效能可能會受到複雜設計和使用映射的影響 (特別是必須透過網路) 載入。 如需解決滾動效能問題的詳細資訊，請參閱 Google 的文章。

### <a name="referencing-a-custom-row-view"></a>參考自訂資料列視圖

自訂介面卡範例的實作為 `HomeScreenAdapter.cs` 。 主要方法是 `GetView` 使用資源識別碼載入自訂 .axml，然後在其傳回 `Resource.Layout.CustomView` 之前，先在 view 中的每個控制項上設定屬性。 完整的介面卡類別如下所示：

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

因為 `HomeScreen` 類別現在繼承自 `Activity` ，所以 `ListView` 會在類別中宣告一個欄位，以保存在 .axml 中宣告之控制項的參考：

```csharp
ListView listView;
```

然後，類別必須使用方法，載入活動的自訂版面配置 .AXML `SetContentView` 。 然後，它可以 `ListView` 在配置中尋找控制項，然後建立並指派介面卡，並指派 click 處理常式。 >oncreate 方法的程式碼如下所示：

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

最後 `ItemClick` 必須定義處理常式，在此情況下，它只會顯示一則 `Toast` 訊息：

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

當觸及某個資料列時，應該會反白顯示使用者意見反應。 當自訂視圖指定做為 **CustomView .axml** 的背景色彩時，它也會覆寫選取專案反白顯示。 **CustomView. .axml**中的這一行程式碼會將背景設定為淺綠色，但這也表示當觸及資料列時，沒有視覺指標：

```xml
android:background="#FFDAFF7F"
```

若要重新啟用醒目提示行為，也要自訂所使用的色彩，請改為將背景屬性設定為自訂選取器。 選取器會宣告預設的背景色彩以及反白顯示色彩。 檔案 **資源/繪製/CustomSelector.xml** 包含下列宣告：

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

若要參考自訂選取器，請將 **CustomView. .axml** 中的 background 屬性變更為：

```xml
android:background="@drawable/CustomSelector"
```

選取的資料列和對應的 `Toast` 訊息現在看起來像這樣：

[![在橙色中選取的資料列，其中有快顯訊息顯示選取的資料列名稱](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)

### <a name="preventing-flickering-on-custom-layouts"></a>防止自訂版面配置閃爍

Android 會嘗試透過快取配置資訊來改善滾動的效能 `ListView` 。 如果您有長時間的資料滾動清單，您也應該將 `android:cacheColorHint` 活動 .axml 定義中宣告的屬性 (設定為與 `ListView` 您的自訂資料列配置的背景) 相同的色彩值。 當使用者以自訂資料列背景色彩滾動清單時，無法包含此提示可能會導致「閃爍」。

## <a name="related-links"></a>相關連結

- [BuiltInViews (範例) ](/samples/xamarin/monodroid-samples/builtinviews)
- [AccessoryViews (範例) ](/samples/xamarin/monodroid-samples/accessoryviews)
- [CustomRowView (範例) ](/samples/xamarin/monodroid-samples/customrowview)