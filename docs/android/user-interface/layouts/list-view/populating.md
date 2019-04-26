---
title: 將資料填入 ListView
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 57c69223a01074ed15714026b7e9ec4e995808e0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61171183"
---
# <a name="populating-a-listview-with-data"></a>將資料填入 ListView


## <a name="overview"></a>總覽

若要將資料列加入`ListView`您需要將它新增至您的版面配置和實作`IListAdapter`方法，`ListView`填入本身的呼叫。 Android 包含內建`ListActivity`和`ArrayAdapter`類別，您可以使用未定義任何自訂的版面配置 XML 或程式碼。 `ListActivity`類別會自動建立`ListView`，並公開`ListAdapter`屬性，來提供資料列檢視，以顯示透過配接器。

內建配接器需要檢視資源識別碼做為取得每個資料列所用的參數。 您可以使用內建的資源，例如中`Android.Resource.Layout`因此您不需要撰寫您自己。


## <a name="using-listactivity-and-arrayadapterltstringgt"></a>使用 ListActivity 和 ArrayAdapter&lt;字串&gt;

此範例**BasicTable/HomeScreen.cs**示範如何使用這些類別來顯示`ListView`只需幾行程式碼中：

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


### <a name="handling-row-clicks"></a>按一下 處理資料列

通常`ListView`也可讓使用者接觸的資料列來執行某些動作 （例如播放歌曲，或呼叫連絡人，或顯示另一個畫面）。 回應使用者修飾，需要有一個方法中實作`ListActivity` &ndash; `OnListItemClick` &ndash;如下所示：

[![SimpleListItem 的螢幕擷取畫面](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

現在使用者可以接觸的資料列和`Toast`警示會出現：

[![螢幕擷取畫面的快顯出現時所觸及的資料列](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>實作 ListAdapter

`ArrayAdapter<string>` 很棒，因為其單純性，但非常有限。 不過，通常您會有商務實體，而不是只是您想要繫結的字串的集合。
比方說，如果您的資料是由員工類別的集合所組成，您可能想要只顯示每一位員工的名稱的清單。 若要自訂的行為`ListView`來控制哪些資料會顯示您必須實作的子類別`BaseAdapter`覆寫下列四個項目：

-   **計數**&ndash;告訴控制項在資料中有多少資料列。

-   **GetView** &ndash;傳回一份檢視的每個資料列，填入資料。
    這個方法有參數`ListView`以重複使用現有、 未使用的資料列中傳遞。

-   **GetItemId** &ndash;傳回資料列識別碼 （通常是資料列數字，但也可以是任何您喜歡的長數值）。

-   **此 [int]** indexer&ndash;傳回特定的資料列號碼相關聯的資料。

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


### <a name="using-a-custom-adapter"></a>使用自訂的配接器

使用自訂的配接器是內建類似`ArrayAdapter`，並傳入`context`而`string[]`顯示的值：

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

由於此範例使用相同的資料列配置 (`SimpleListItem1`) 產生的應用程式看起來與上一個範例相同。


### <a name="row-view-re-use"></a>資料列檢視重複使用

在此範例中，有只有六個項目。 因為畫面可以容納八個沒有資料列重複使用所需。 顯示時數百或數千個資料列，不過，它會浪費記憶體來建立數百或數千個`View`物件時只有八個符合螢幕大小一次。 若要從其檢視放置在佇列中以供重複使用的畫面消失的一個資料列時，請避免此情況下，。 當使用者捲動時，`ListView`呼叫`GetView`來要求新的檢視，以顯示&ndash;如果可以使用它會將未使用的檢視中`convertView`參數。 如果這個值是 null，則您的程式碼應該建立新的檢視執行個體，否則您可以重新設定該物件的屬性並重複使用它。

`GetView`方法重複使用資料列檢視應遵循此模式：

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

自訂配接器實作應該*一律*重複使用`convertView`之前建立新的檢視，以確保它們未執行記憶體不足時顯示較長清單物件。

某些配接器實作 (例如`CursorAdapter`) 沒有`GetView`方法，而不是它們需要兩個不同的方法`NewView`並`BindView`的重複使用的資料列，藉以強制執行區隔的責任`GetView`分成兩個方法。 沒有`CursorAdapter`本文件稍後的範例。


## <a name="enabling-fast-scrolling"></a>啟用快速捲動

快速捲動會協助使用者提供的額外 '控制代碼' 做為直接存取清單的一部分的捲軸來捲動很長的清單。 此螢幕擷取畫面顯示快速捲動控制代碼：

[![快速捲動捲軸的控制代碼的螢幕擷取畫面](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

造成快速捲動的控制代碼，才會出現很簡單，只要設定`FastScrollEnabled`屬性設`true`:

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>新增區段的索引

區段索引時，提供其他意見反應使用者是透過一長串快速捲動&ndash;它會顯示它們有捲動到哪一個 'section'。 若要導致顯示配接器的子類別必須實作的區段索引`ISectionIndexer`介面，以提供根據所顯示的資料列索引文字：

[![螢幕擷取畫面的 H，區段上方出現的開始小時](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

若要實作`ISectionIndexer`您需要將三種方法新增至配接器：

-   **GetSections** &ndash;提供區段的完整清單可以顯示的索引項目。 此方法需要 Java 物件的陣列，讓程式碼需要建立`Java.Lang.Object[]`從.NET 集合。 在本例中它會傳回一份清單中的初始字元`Java.Lang.String`。

-   **GetPositionForSection** &ndash;傳回第一個資料列位置指定的區段索引。

-   **GetSectionForPosition** &ndash;傳回給定資料列所顯示的區段索引。


此範例`SectionIndex/HomeScreenAdapter.cs`檔案會在建構函式中實作這些方法和一些額外的程式碼。 建構函式建置循環使用每個資料列，並擷取的標題 （項目必須已經排序，才能順利運作） 的第一個字元的區段索引。

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

您的區段索引項目不需要將 1 對 1 對應到實際的區段。 這就是為什麼`GetPositionForSection`方法存在。
`GetPositionForSection` 可讓您得以將任何索引是在您的索引清單的清單檢視中的任何區段中的對應。 例如，您可能會在您的索引，"z"，但您可能沒有每個字母的資料表區段，因此而不是"z"對應到 26，它可能會對應到 25 或月 24 日，或任何區段索引"z"應該對應至。



## <a name="related-links"></a>相關連結

- [BasicTableAndroid (sample)](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter （範例）](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [FastScroll （範例）](https://developer.xamarin.com/samples/FastScroll/)
