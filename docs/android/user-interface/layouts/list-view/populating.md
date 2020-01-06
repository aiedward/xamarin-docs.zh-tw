---
title: 以資料填入 Xamarin. Android ListView
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2017
ms.openlocfilehash: 7ec5537345536884e2dc3da02ab54a3ca00f760e
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607967"
---
# <a name="populating-a-xamarinandroid-listview-with-data"></a>以資料填入 Xamarin. Android ListView

若要將資料列加入 `ListView` 您需要將它新增至您的配置，並使用 `ListView` 呼叫來填入其本身的方法來執行 `IListAdapter`。 Android 包含內建的 `ListActivity` 和 `ArrayAdapter` 類別，您可以在不定義任何自訂版面配置 XML 或程式碼的情況下使用。 `ListActivity` 類別會自動建立 `ListView` 並公開 `ListAdapter` 屬性，以提供資料列視圖以透過介面卡顯示。

內建介面卡會採用 view resource ID 做為每個資料列所使用的參數。 您可以使用內建資源（例如 `Android.Resource.Layout` 中的資源），而不需要自行撰寫。

## <a name="using-listactivity-and-arrayadapterltstringgt"></a>使用 ListActivity 和 ArrayAdapter&lt;字串&gt;

**BasicTable/HomeScreen**範例示範如何使用這些類別，只在幾行程式碼中顯示 `ListView`：

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

通常 `ListView` 也可以讓使用者觸及某個資料列來執行某個動作（例如，播放歌曲、呼叫連絡人，或顯示另一個畫面）。 若要回應使用者的接觸，必須在 `ListActivity` &ndash; `OnListItemClick` 中執行一種以上的方法 &ndash; 如下所示：

[![SimpleListItem 的螢幕擷取畫面](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

現在使用者可以碰觸資料列，隨即會出現 `Toast` 警示：

[![觸及資料列時顯示的快顯通知螢幕擷取畫面](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)

## <a name="implementing-a-listadapter"></a>執行 ListAdapter

`ArrayAdapter<string>` 很簡單，因為它的簡單性非常有限，但卻非常受限。 不過，通常您會有商業實體的集合，而不只是您想要系結的字串。
例如，如果您的資料包含 Employee 類別的集合，則您可能會想要讓清單只顯示每個員工的名稱。 若要自訂 `ListView` 的行為，以控制要顯示的資料，您必須實作用 `BaseAdapter` 的子類別，覆寫下列四個專案：

- &ndash;**計數**，以告知控制資料中有多少資料列。

- **GetView** &ndash; 以傳回每個資料列的視圖，並填入資料。
    這個方法有一個參數，可讓 `ListView` 傳入現有、未使用的資料列以供重複使用。

- **GetItemId** &ndash; 會傳回資料列識別碼（通常是資料列編號，雖然它可以是任何您想要的長數值）。

- **這個 [int]** 索引子 &ndash; 來傳回與特定資料列編號相關聯的資料。

**BasicTableAdapter/HomeScreenAdapter**中的範例程式碼示範如何 `BaseAdapter`子類別：

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

使用自訂介面卡類似于內建的 `ArrayAdapter`，傳入 `context` 和要顯示的值 `string[]`：

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

因為此範例使用相同的資料列配置（`SimpleListItem1`），所以產生的應用程式看起來會與前一個範例相同。

### <a name="row-view-re-use"></a>資料列視圖重複使用

在此範例中，只有六個專案。 因為畫面可以符合八個，所以不需要重複使用任何資料列。 不過，在顯示數百或數千個數據列時，如果一次只有8個符合畫面，就會浪費記憶體來建立上百個或數千個 `View` 物件。 若要避免這種情況，當資料列從畫面中消失時，其 view 會放在佇列中以供重複使用。 當使用者滾動時，`ListView` 會呼叫 `GetView` 以要求新的視圖顯示 &ndash; 如果有的話，會在 `convertView` 參數中傳遞未使用的 view。 如果這個值是 null，則您的程式碼應該會建立新的 view 實例，否則您可以重新設定該物件的屬性並重複使用它。

`GetView` 方法應遵循此模式來重新使用資料列視圖：

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

自訂介面卡的執行應該*一律*在建立新的視圖之前，先重複使用 `convertView` 物件，以確保它們在顯示長清單時不會用盡記憶體。

某些介面卡的執行（例如 `CursorAdapter`）沒有 `GetView` 的方法，而是需要兩個不同的方法 `NewView` 和 `BindView` 將 `GetView` 的責任分隔成兩個方法，以強制執行資料列重複使用。 本檔稍後會有 `CursorAdapter` 的範例。

## <a name="enabling-fast-scrolling"></a>啟用快速滾動

快速滾動可協助使用者在長清單中滾動，方法是提供額外的「控點」做為捲軸，以直接存取清單的一部分。 這個螢幕擷取畫面顯示快速的捲軸控點：

[![具有捲軸控點之快速滾動的螢幕擷取畫面](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

使快速滾動控點出現，就像將 `FastScrollEnabled` 屬性設為 `true`一樣簡單：

```csharp
ListView.FastScrollEnabled = true;
```

### <a name="adding-a-section-index"></a>新增區段索引

當使用者透過長清單快速滾動時，區段索引會提供額外的意見反應，&ndash; 它會顯示他們所滾動至的「區段」。 若要讓區段索引出現，介面卡子類別必須執行 `ISectionIndexer` 介面，以根據顯示的資料列提供索引文字：

[H 的 ![螢幕擷取畫面，顯示在開頭為 H 的一節上方](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

若要執行 `ISectionIndexer` 您需要將三種方法新增到介面卡：

- **GetSections** &ndash; 提供可顯示之區段索引標題的完整清單。 這個方法需要 JAVA 物件的陣列，因此程式碼必須從 .NET 集合建立 `Java.Lang.Object[]`。 在我們的範例中，它會傳回清單中的初始字元清單，做為 `Java.Lang.String`。

- **GetPositionForSection** &ndash; 會傳回給定區段索引的第一個資料列位置。

- **GetSectionForPosition** &ndash; 會傳回要針對指定資料列顯示的區段索引。

範例 `SectionIndex/HomeScreenAdapter.cs` 檔案會在函式中執行這些方法，以及一些額外的程式碼。 此函式會建立區段索引，方法是逐一查看每個資料列，並將標題的第一個字元解壓縮（這些專案必須已經過排序才能正常執行）。

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

建立資料結構之後，`ISectionIndexer` 方法非常簡單：

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

您的區段索引標題不需要將1:1 對應到您的實際區段。 這就是為什麼 `GetPositionForSection` 方法存在的原因。
`GetPositionForSection` 可讓您有機會將索引清單中的任何索引對應到清單視圖中的任何區段。 例如，您在索引中可能會有 "z"，但您可能不會有每個字母的資料表區段，因此，它可能會對應到25或24，或任何區段索引 "z" 應對應至。

## <a name="related-links"></a>相關連結

- [BasicTableAndroid （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableadapter)
- [FastScroll （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fastscroll)
