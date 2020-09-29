---
title: 使用資料填入 Xamarin. Android ListView
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2017
ms.openlocfilehash: 6c22905e576b37d892d83a7b22799e4afdc2262f
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457155"
---
# <a name="populating-a-xamarinandroid-listview-with-data"></a>使用資料填入 Xamarin. Android ListView

若要將資料列加入至 `ListView` ，您必須將資料列加入至您的版面配置，並 `IListAdapter` 使用 `ListView` 呼叫來填入本身的方法來執行。 Android 包含內建 `ListActivity` 和 `ArrayAdapter` 類別，您可以使用這些類別，而不需要定義任何自訂版面配置 XML 或程式碼。 `ListActivity`類別會自動建立 `ListView` 並公開 `ListAdapter` 屬性，以提供透過介面卡顯示的資料列視圖。

內建的介面卡會採用 view 資源識別碼作為每個資料列所用的參數。 您可以使用內建的資源，例如中的資源， `Android.Resource.Layout` 因此您不需要自行撰寫。

## <a name="using-listactivity-and-arrayadapterltstringgt"></a>使用 ListActivity 和 >arrayadapter &lt; 字串&gt;

**BasicTable/HomeScreen**範例示範如何使用這些類別， `ListView` 只顯示幾行程式碼：

```csharp
[Activity(Label = "BasicTable", MainLauncher = true, Icon = "@drawable/icon")]
public class HomeScreen : ListActivity {
   string[] items;
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       items = new string[] { "Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers" };
       ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItem1, items);
   }
}
```

### <a name="handling-row-clicks"></a>處理資料列點擊

通常 `ListView` 也會允許使用者觸控某個資料列來執行一些動作 (例如播放歌曲、呼叫連絡人，或顯示其他螢幕) 。 若要回應使用者的潤色，必須在中使用另一個方法來執行，如下所示 `ListActivity` &ndash; `OnListItemClick` &ndash; ：

[![SimpleListItem 的螢幕擷取畫面](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

現在使用者可以觸及某個資料列， `Toast` 將會出現警示：

[![當觸及資料列時出現的快顯螢幕擷取畫面](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)

## <a name="implementing-a-listadapter"></a>執行 Adapterview

`ArrayAdapter<string>` 是很好的，因為它的簡易性很簡單，但非常有限。 不過，您通常會有商務實體的集合，而不只是您想要系結的字串。
例如，如果您的資料包含員工類別的集合，則您可能會想要讓清單只顯示每個員工的名稱。 若要自訂的行為 `ListView` 來控制要顯示的資料，您必須執行覆 `BaseAdapter` 寫下列四個專案的子類別：

- **計數** &ndash; 告訴控制項資料中有多少資料列。

- **GetView** &ndash; 若要傳回每個資料列的視圖，請填入資料。
    這個方法具有的參數， `ListView` 可傳入現有、未使用的資料列以供重複使用。

- **GetItemId** &ndash; 傳回資料列識別碼， (通常是資料列號碼，雖然它可以是您想要) 的任何 long 值。

- **這個 [int]** 索引子會傳回 &ndash; 與特定資料列編號相關聯的資料。

**BasicTableAdapter/HomeScreenAdapter**中的範例程式碼示範如何子類別 `BaseAdapter` ：

```csharp
public class HomeScreenAdapter : BaseAdapter<string> {
   string[] items;
   Activity context;
   public HomeScreenAdapter(Activity context, string[] items) : base() {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
  {
       return position;
   }
   public override string this[int position] {  
       get { return items[position]; }
   }
   public override int Count {
       get { return items.Length; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       View view = convertView; // re-use an existing view, if one is available
      if (view == null) // otherwise create a new one
           view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
       view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
       return view;
   }
}
```

### <a name="using-a-custom-adapter"></a>使用自訂介面卡

使用自訂介面卡類似于內建的 `ArrayAdapter` ，會傳入 `context` 和 `string[]` 值以顯示：

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

因為此範例使用相同的資料列配置 (`SimpleListItem1`) 產生的應用程式看起來會與上一個範例相同。

### <a name="row-view-re-use"></a>資料列視圖重複使用

在此範例中，只有六個專案。 因為畫面可以容納八個，所以不需要重複使用資料列。 不過，當顯示數百或數千個數據列時，可能會浪費記憶體來建立數百個或數千個 `View` 物件，且一次只能有八個符合的畫面。 為了避免這種情況，當資料列從畫面中消失時，其視圖會放置在佇列中以供重複使用。 當使用者滾動時， `ListView` 會呼叫 `GetView` 以要求新的視圖顯示（ &ndash; 如果有的話），以在參數中傳遞未使用的視圖 `convertView` 。 如果這個值為 null，則您的程式碼應該建立新的 view 實例，否則您可以重新設定該物件的屬性，並重複使用它。

`GetView`方法應該遵循此模式來重複使用資料列視圖：

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

自訂介面卡的執行應該 *一律* 在 `convertView` 建立新的視圖之前重複使用物件，以確保它們在顯示較長的清單時不會用盡記憶體。

某些介面卡的執行 (例如 `CursorAdapter`) 沒有 `GetView` 方法，而是需要兩個不同的方法， `NewView` 並藉 `BindView` 由將的責任分隔 `GetView` 為兩個方法來強制執行資料列重複使用。 `CursorAdapter`本檔稍後會有一個範例。

## <a name="enabling-fast-scrolling"></a>啟用快速滾動

快速滾動可協助使用者藉由提供額外的「控點」做為捲軸來直接存取清單的一部分，藉此滾動長清單。 這個螢幕擷取畫面顯示快速滾動控制碼：

[![使用捲軸控點進行快速滾動的螢幕擷取畫面](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

只要將屬性設為，就可以顯示快速滾動控點 `FastScrollEnabled` `true` ：

```csharp
ListView.FastScrollEnabled = true;
```

### <a name="adding-a-section-index"></a>加入區段索引

區段索引會在使用者透過長清單迅速滾動時提供其他意見反應，以顯示其所 &ndash; 滾動的「區段」。 為了讓區段索引出現，Adapter 子類別必須 `ISectionIndexer` 根據所顯示的資料列來執行介面，以提供索引文字：

[![以 H 開頭區段中顯示的 H 螢幕擷取畫面](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

若要執行 `ISectionIndexer` ，您必須將三個方法新增至介面卡：

- **GetSections** &ndash; 提供可顯示之區段索引標題的完整清單。 這個方法需要 JAVA 物件的陣列，因此程式碼需要 `Java.Lang.Object[]` 從 .net 集合建立。 在我們的範例中，它會傳回清單中的初始字元清單做為 `Java.Lang.String` 。

- **GetPositionForSection** &ndash; 傳回指定區段索引的第一個資料列位置。

- **GetSectionForPosition** &ndash; 傳回要為指定資料列顯示的區段索引。

範例檔案會在函式 `SectionIndex/HomeScreenAdapter.cs` 中執行這些方法和一些其他程式碼。 此函式會藉由逐一查看每個資料列並解壓縮標題的第一個字元來建立區段索引 (專案必須已經排序，才能讓此作業) 。

```csharp
alphaIndex = new Dictionary<string, int>();
for (int i = 0; i < items.Length; i++) { // loop through items
   var key = items[i][0].ToString();
   if (!alphaIndex.ContainsKey(key))
       alphaIndex.Add(key, i); // add each 'new' letter to the index
}
sections = new string[alphaIndex.Keys.Count];
alphaIndex.Keys.CopyTo(sections, 0); // convert letters list to string[]

// Interface requires a Java.Lang.Object[], so we create one here
sectionsObjects = new Java.Lang.Object[sections.Length];
for (int i = 0; i < sections.Length; i++) {
   sectionsObjects[i] = new Java.Lang.String(sections[i]);
}
```

建立資料結構之後， `ISectionIndexer` 方法非常簡單：

```csharp
public Java.Lang.Object[] GetSections()
{
   return sectionsObjects;
}
public int GetPositionForSection(int section)
{
   return alphaIndexer[sections[section]];
}
public int GetSectionForPosition(int position)
{   // this method isn't called in this example, but code is provided for completeness
    int prevSection = 0;
    for (int i = 0; i < sections.Length; i++)
    {
        if (GetPositionForSection(i) > position)
        {
            break;
        }
        prevSection = i;
    }
    return prevSection;
}
```

您的區段索引標題不需要將1:1 對應到您的實際區段。 這就是 `GetPositionForSection` 方法存在的原因。
`GetPositionForSection` 讓您有機會將索引清單中的任何索引對應到清單視圖中的任何區段。 例如，您的索引中可能會有 "z"，但每個字母可能都沒有資料表區段，因此，它可能會對應至25或24，或任何區段索引 "z" 應對應至。

## <a name="related-links"></a>相關連結

- [BasicTableAndroid (範例) ](/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter (範例) ](/samples/xamarin/monodroid-samples/basictableadapter)
- [FastScroll (範例) ](/samples/xamarin/monodroid-samples/fastscroll)