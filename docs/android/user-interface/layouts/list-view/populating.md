---
title: "填入資料的 ListView"
ms.topic: article
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: 74d8533d0a757a307d88125701a482dfefd5eec2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="populating-a-listview-with-data"></a>填入資料的 ListView

<a name="overview" />

## <a name="overview"></a>總覽

若要將資料列加入`ListView`您需要將它加入至您的版面配置和實作`IListAdapter`.remove，`ListView`填入本身呼叫。 Android 包含內建`ListActivity`和`ArrayAdapter`類別，您可以使用但未定義任何自訂版面配置 XML 或程式碼。 `ListActivity`類別會自動建立`ListView`公開`ListAdapter`提供的資料列檢視，以顯示透過配接器的屬性。

內建配接器需要檢視資源識別碼做為取得每個資料列所用的參數。 您可以使用內建的資源，例如中`Android.Resource.Layout`因此您不需要撰寫您自己。

<a name="Using_ListActivity_and_ArrayAdapterString" />

## <a name="using-listactivity-and-arrayadapterltstringgt"></a>使用 ListActivity 和 ArrayAdapter&lt;字串&gt;

此範例**BasicTable/HomeScreen.cs**示範如何使用這些類別來顯示`ListView`只需要幾行程式碼：

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

<a name="Handling_Row_Clicks" />

### <a name="handling-row-clicks"></a>按一下 處理資料列

通常`ListView`也可讓使用者修改資料列來執行某些動作 （例如播放歌曲，或呼叫連絡人，或顯示另一個螢幕）。 若要回應使用者工作需要有一個方法中實作`ListActivity` &ndash; `OnListItemClick` &ndash;如下所示：

[![SimpleListItem 的螢幕擷取畫面](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

現在使用者可以修改資料列和`Toast`會出現警示：

[![螢幕擷取畫面的快顯會接觸到一個資料列時顯示](populating-images/basictable2.png)](populating-images/basictable2.png)

<a name="Implementing_a_ListAdapter" />

## <a name="implementing-a-listadapter"></a>實作 ListAdapter

`ArrayAdapter<string>` 非常簡單，但因為非常有限。 不過，通常您會有商務實體，而非只是您想要繫結的字串的集合。
例如，如果您的資料組成的員工類別集合，那麼您可能需要清單只會顯示每位員工的名稱。 若要自訂的行為`ListView`來控制哪些資料會顯示您必須實作的子類別`BaseAdapter`覆寫下列四個項目：

-   **計數**&ndash;告訴控制項在資料中有多少資料列。

-   **GetView** &ndash;傳回檢視每個資料列，填入資料。
    這個方法具有參數來代表`ListView`傳遞現有、 未使用的資料列中重複使用。

-   **GetItemId** &ndash;傳回資料列識別碼 （通常是資料列數字，雖然它可以是任何您喜歡的長數值）。

-   **此 [int]**索引子&ndash;傳回特定資料列數目與相關聯的資料。

中的範例程式碼**BasicTableAdapter/HomeScreenAdapter.cs**示範如何以子類別`BaseAdapter`:

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

<a name="Using_a_Custom_Adapter" />

### <a name="using-a-custom-adapter"></a>使用自訂的配接器

使用自訂的配接器是內建類似`ArrayAdapter`，並傳入`context`和`string[]`顯示的值：

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

由於這個範例使用相同的資料列配置 (`SimpleListItem1`) 產生的應用程式看起來與上一個範例相同。

<a name="Row_View_Re-Use" />

### <a name="row-view-re-use"></a>資料列檢視重複使用

在此範例中有只有六個項目。 因為畫面能容納八個，沒有資料列重複使用所需。 當顯示數百或數千個資料列，不過，它會浪費記憶體來建立數百或數千個`View`物件時只有八個符合螢幕大小一次。 若要避免此情況下，，資料列從其檢視放入佇列，以供重複使用的畫面消失時。 當使用者捲動，`ListView`呼叫`GetView`來要求新的檢視，以顯示&ndash;如果使用通過未使用的檢視中`convertView`參數。 如果您的程式碼應該建立新的檢視執行個體，這個值是 null，否則您可以重新設定該物件的屬性並重複使用它。

`GetView`方法應該遵循這個模式，以重複使用資料列檢視：

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

自訂配接器實作應該*一律*重複使用`convertView`之前建立新的檢視，以確保它們不會執行記憶體不足時顯示長的清單物件。

某些配接器實作 (例如`CursorAdapter`) 沒有`GetView`方法，而是它們需要兩個不同方法`NewView`和`BindView`的重複使用資料列，藉以強制執行分隔責任`GetView`分成兩個方法。 沒有`CursorAdapter`文件中稍後的範例。

<a name="Enabling_Fast_Scrolling" />

## <a name="enabling-fast-scrolling"></a>啟用快速捲動

快速捲動可協助使用者捲動詳細清單，藉由提供其他 '控制代碼' 做為捲軸來直接存取清單的一部分。 這個螢幕擷取畫面顯示快速捲動控制代碼：

[![快速捲動捲軸的控制代碼的螢幕擷取畫面](populating-images/fastscroll.png)](populating-images/fastscroll.png)

導致快速捲動的控制代碼，才會出現很簡單，只設定`FastScrollEnabled`屬性`true`:

```csharp
ListView.FastScrollEnabled = true;
```

<a name="Adding_a_Section_Index" />

### <a name="adding-a-section-index"></a>新增區段索引

區段索引提供其他意見反應使用者何時可快速瀏覽一長串&ndash;它會顯示它們捲到哪些 'section'。 若要造成顯示配接器的子類別必須實作的區段索引`ISectionIndexer`介面，以提供根據所顯示的資料列索引文字：

[![螢幕擷取畫面 H H 為開頭的區段上方出現](populating-images/sectionindex.png)](populating-images/sectionindex.png)

若要實作`ISectionIndexer`您要新增到配接器的三種方法：

-   **GetSections** &ndash;提供區段的完整清單索引的項目，無法顯示。 此方法需要 Java 物件的陣列，因此該程式碼必須建立`Java.Lang.Object[]`從.NET 集合。 在我們的範例會傳回做為清單中的初始字元清單`Java.Lang.String`。

-   **GetPositionForSection** &ndash;傳回指定的截面索引的第一個資料列位置。

-   **GetSectionForPosition** &ndash;傳回給定資料列所顯示的區段索引。


此範例`SectionIndex/HomeScreenAdapter.cs`檔案會實作這些方法和某些其他程式碼中建構函式。 建構函式會根據區段索引的每個資料列中執行迴圈，並擷取 （項目必須已排序，此工作） 的標題的第一個字元。

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

建立資料結構`ISectionIndexer`是非常簡單的方法：

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

1:1 對應到您實際的區段不需要您的區段索引項目。 這就是為什麼`GetPositionForSection`方法的存在。
`GetPositionForSection` 會讓您將任何索引是在清單檢視中的任何小節為您的索引清單。 比方說，您可能在索引中，"z"，但是您可能沒有每個字母的資料表區段，因此而不是"z"對應到 26，它可能會對應到 25 24，或任何區段索引"z"應該對應至。



## <a name="related-links"></a>相關連結

- [BasicTableAndroid （範例）](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (sample)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [FastScroll （範例）](https://developer.xamarin.com/samples/FastScroll/)
