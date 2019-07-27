---
title: 以資料填入 Xamarin. Android ListView
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: dff2efe687fde16903df19fefad2e2589c888086
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510113"
---
# <a name="populating-a-xamarinandroid-listview-with-data"></a>以資料填入 Xamarin. Android ListView

若要將資料列`ListView`加入至, 您需要將它加入至您的`IListAdapter`配置, 並使用`ListView`呼叫來填入本身的方法來執行。 Android 包含內`ListActivity`建和`ArrayAdapter`類別, 您可以在不定義任何自訂版面配置 XML 或程式碼的情況下使用。 類別會自動`ListView`建立並公開`ListAdapter`屬性, 以提供透過介面卡顯示的資料列視圖。 `ListActivity`

內建介面卡會採用 view resource ID 做為每個資料列所使用的參數。 您可以使用內建資源 (例如中`Android.Resource.Layout`的資源), 而不需要自行撰寫。

## <a name="using-listactivity-and-arrayadapterltstringgt"></a>使用 ListActivity 和 ArrayAdapter&lt;字串&gt;

**BasicTable/HomeScreen**範例示範如何使用這些類別, 只`ListView`在幾行程式碼中顯示:

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
   protected override void OnListItemClick(ListView l, View v, int position, long id)
}
```


### <a name="handling-row-clicks"></a>處理資料列點擊

通常也`ListView`會允許使用者觸控某個資料列來執行某些動作 (例如播放歌曲、呼叫連絡人, 或顯示另一個畫面)。 若要回應使用者的觸控, 必須在中執行一個以上的方法`ListActivity` , 如下所&ndash; `OnListItemClick` &ndash;示:

[![SimpleListItem 的螢幕擷取畫面](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

現在使用者可以觸碰一列, `Toast`隨即會出現警示:

[![觸及資料列時所出現的快顯通知螢幕擷取畫面](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>執行 ListAdapter

`ArrayAdapter<string>`非常簡單, 但非常有限。 不過, 通常您會有商業實體的集合, 而不只是您想要系結的字串。
例如, 如果您的資料包含 Employee 類別的集合, 則您可能會想要讓清單只顯示每個員工的名稱。 若要自訂的行為`ListView`以控制要顯示哪些資料, 您必須實作用的`BaseAdapter`子類別, 以覆寫下列四個專案:

-   **計數**&ndash;以告知控制項資料中有多少資料列。

-   **GetView**&ndash;若要傳回每個資料列的視圖, 請填入資料。
    這個方法具有的`ListView`參數, 可用於傳入現有、未使用的資料列以供重複使用。

-   **GetItemId**&ndash;傳回資料列識別碼 (通常是資料列編號, 雖然它可以是任何您想要的長數值)。

-   **此 [int]** 索引&ndash;器可傳回與特定資料列編號相關聯的資料。

**BasicTableAdapter/HomeScreenAdapter**中的範例程式碼示範如何子類別`BaseAdapter`化:

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

使用自訂介面卡類似于內建`ArrayAdapter`的, 傳入`context`和`string[]`值以顯示:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

因為此範例使用相同的資料列配置`SimpleListItem1`(), 所以產生的應用程式看起來會與前一個範例相同。


### <a name="row-view-re-use"></a>資料列視圖重複使用

在此範例中, 只有六個專案。 因為畫面可以符合八個, 所以不需要重複使用任何資料列。 不過, 在顯示數百個或數千個數據列時, 如果一次只有8個符合螢幕, 就`View`會浪費記憶體來建立上百個或數千個物件。 若要避免這種情況, 當資料列從畫面中消失時, 其 view 會放在佇列中以供重複使用。 當使用者滾動時, 會`ListView`呼叫`GetView`來要求要顯示&ndash;的新視圖 (如果`convertView`有的話) 會在參數中傳遞未使用的 view。 如果這個值是 null, 則您的程式碼應該會建立新的 view 實例, 否則您可以重新設定該物件的屬性並重複使用它。

`GetView`方法應該遵循此模式來重新使用資料列視圖:

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

自訂介面卡的執行應該*一律*在`convertView`建立新的視圖之前重複使用物件, 以確保它們在顯示長清單時不會用盡記憶體。

某些介面卡`CursorAdapter`的執行`GetView` (例如) 沒有方法, 而是需要兩個不同的方法`NewView` , `BindView`並將的責任`GetView`分成兩個來強制執行資料列重複使用方法. 本檔稍後`CursorAdapter`會提供範例。


## <a name="enabling-fast-scrolling"></a>啟用快速滾動

快速滾動可協助使用者在長清單中滾動, 方法是提供額外的「控點」做為捲軸, 以直接存取清單的一部分。 這個螢幕擷取畫面顯示快速的捲軸控點:

[![具有捲軸控點之快速滾動的螢幕擷取畫面](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

使快速滾動手柄出現, 就像將`FastScrollEnabled`屬性設定為`true`一樣簡單:

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>新增區段索引

當使用者透過長清單&ndash;快速滾動時, 區段索引會顯示其所滾動至的「區段」, 以提供更多的意見反應。 若要讓區段索引出現, 介面卡子類別必須執行`ISectionIndexer`介面, 以根據顯示的資料列提供索引文字:

[![H 的螢幕擷取畫面, 顯示在以 H 開頭的上方區段](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

若要`ISectionIndexer`執行, 您必須將三個方法新增到介面卡:

-   **GetSections**&ndash;提供可顯示之區段索引標題的完整清單。 這個方法需要 JAVA 物件的陣列, 因此程式碼必須`Java.Lang.Object[]`從 .net 集合建立。 在我們的範例中, 它會以形式`Java.Lang.String`傳回清單中的初始字元清單。

-   **GetPositionForSection**&ndash;傳回指定之區段索引的第一個資料列位置。

-   **GetSectionForPosition**&ndash;傳回要針對指定資料列顯示的區段索引。


範例`SectionIndex/HomeScreenAdapter.cs`檔案會在函式中執行這些方法, 以及一些額外的程式碼。 此函式會建立區段索引, 方法是逐一查看每個資料列, 並將標題的第一個字元解壓縮 (這些專案必須已經過排序才能正常執行)。

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

建立資料結構之後, `ISectionIndexer`方法非常簡單:

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

您的區段索引標題不需要將1:1 對應到您的實際區段。 這就是方法`GetPositionForSection`存在的原因。
`GetPositionForSection`讓您有機會將索引清單中的任何索引對應到清單視圖中的任何區段。 例如, 您在索引中可能會有 "z", 但您可能不會有每個字母的資料表區段, 因此, 它可能會對應到25或 24, 或任何區段索引 "z" 應對應至。



## <a name="related-links"></a>相關連結

- [BasicTableAndroid (範例)](https://developer.xamarin.com/samples/monodroid/BasicTableAndroid/)
- [BasicTableAdapter (範例)](https://developer.xamarin.com/samples/monodroid/BasicTableAdapter/)
- [FastScroll (範例)](https://developer.xamarin.com/samples/monodroid/FastScroll/)
