---
title: 基本 RecyclerView 範例
description: 示範如何使用 RecyclerView 的範例應用程式。
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/30/2018
ms.openlocfilehash: 6093c983a80c53b4900bb26c3a7020724a59c06d
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571280"
---
# <a name="a-basic-recyclerview-example"></a>基本 RecyclerView 範例

為了瞭解 `RecyclerView` 在一般應用程式中的運作方式，本主題會探索[RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)範例應用程式，這是使用 `RecyclerView` 來顯示大量相片集合的簡單程式碼範例： 

[![RecyclerView 應用程式的兩個螢幕擷取畫面，使用 CardViews 來顯示相片](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer**會使用[CardView](~/android/user-interface/controls/card-view.md)來執行配置中的每個相片專案 `RecyclerView` 。 基於效能方面的 `RecyclerView` 優勢，此範例應用程式能夠順暢地快速流覽大量的相片，而不會有明顯的延遲。

### <a name="an-example-data-source"></a>範例資料來源

在此範例應用程式中，「相片專輯」資料來源（由 `PhotoAlbum` 類別表示）會提供 `RecyclerView` 專案內容。
`PhotoAlbum`是包含標題的相片集合;當您將它具現化時，您會得到現成的32相片集合：

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

中的每個相片實例 `PhotoAlbum` 都會公開屬性，可讓您讀取其影像資源識別碼 `PhotoID` 和其標題字串 `Caption` 。 相片的集合會進行組織，讓索引子可以存取每一張相片。 例如，下列幾行程式碼會存取集合中第十張相片的映射資源識別碼和標題：

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum`也提供一種 `RandomSwap` 方法，可讓您呼叫以將集合中的第一個相片與集合中其他位置的隨機播放相片交換：

```csharp
mPhotoAlbum.RandomSwap ();
```

因為的執行詳細資料與 `PhotoAlbum` 瞭解無關，所以 `RecyclerView` `PhotoAlbum` 此處不會顯示原始程式碼。 您可以在 `PhotoAlbum` [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)範例應用程式的[PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs)中取得的原始程式碼。

### <a name="layout-and-initialization"></a>版面配置和初始化

版面配置檔案**axml**是由內的單一組成 `RecyclerView` `LinearLayout` ：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

請注意，您必須使用完整名稱**v7。 RecyclerView** ，因為 `RecyclerView` 已封裝在支援程式庫中。 的 `OnCreate` 方法會 `MainActivity` 初始化此配置、將介面卡具現化，並準備基礎資料來源：

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Prepare the data source:
        mPhotoAlbum = new PhotoAlbum ();

        // Instantiate the adapter and pass in its data source:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);

        // Set our view from the "main" layout resource:
        SetContentView (Resource.Layout.Main);

        // Get our RecyclerView layout:
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug the adapter into the RecyclerView:
        mRecyclerView.SetAdapter (mAdapter);
```

此程式碼會執行以下動作：

1. 具現化 `PhotoAlbum` 資料來源。

2. 將相片專輯資料來源傳遞至介面卡的函式 `PhotoAlbumAdapter` （本指南稍後定義）。 
   請注意，將資料來源當做參數傳遞至介面卡的函式是最佳做法。 

3. 從配置 `RecyclerView` 中取得。

4. 藉 `RecyclerView` 由呼叫 `RecyclerView` `SetAdapter` 方法（如上所示），將介面卡插入實例。

### <a name="layout-manager"></a>版面建構管理員

中的每個專案都由組成 `RecyclerView` `CardView` ，其中包含相片影像和相片標題（詳細資料會在下面的「[視圖預留位置](#view-holder)」一節中討論）。 預先定義的 `LinearLayoutManager` 是用來以 `CardView` 垂直捲動相片順序配置每一個：

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

此程式碼位於主要活動的 `OnCreate` 方法中。 建構管理員的程式設計人員需要*內容*，因此 `MainActivity` 會使用來傳遞，如上所 `this` 示。

您可以不使用預先定義的，而是 `LinearLayoutManager` 插入會並排顯示兩個專案的自訂版面建構管理員 `CardView` ，以執行翻頁動畫效果來跨越相片集合。 稍後在本指南中，您將會看到如何藉由在不同的版面建構管理員中交換來修改版面配置的範例。

<a name="view-holder"></a>

### <a name="view-holder"></a>視圖持有者

會呼叫 view 預留位置類別 `PhotoViewHolder` 。 每個 `PhotoViewHolder` 實例 `ImageView` 都有 `TextView` 相關聯之資料列專案的和參考，其配置在中， `CardView` 如下所示圖表：

[![包含 ImageView 和 TextView 的 CardView 圖](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder`衍生自 `RecyclerView.ViewHolder` ，並且包含屬性，以儲存對的參考 `ImageView` ，並 `TextView` 顯示在上述版面配置中。
`PhotoViewHolder`包含兩個屬性和一個函式：

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

在此程式碼範例中， `PhotoViewHolder` 會將參考傳遞至包裝的父專案視圖（ `CardView` ） `PhotoViewHolder` 。 請注意，您一律會將父專案視圖轉送至基底函式。 此函式會 `PhotoViewHolder` `FindViewById` 在父專案視圖上呼叫，以找出它的每一個子視圖參考， `ImageView` 並 `TextView` 分別將結果儲存在 `Image` 和 `Caption` 屬性中。 之後，介面卡會在以新資料更新此子視圖時，從這些屬性中抓取 view 參考 `CardView` 。

如需的詳細資訊 `RecyclerView.ViewHolder` ，請參閱[RecyclerView. ViewHolder 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。

### <a name="adapter"></a>配接器

介面卡會 `RecyclerView` 使用特定相片的資料來載入每一列。 例如，針對位於資料列位置*p*的指定相片，介面卡會在資料來源中的位置*p*處尋找相關聯的資料，並將此資料複製到集合中位置*p*的資料列專案 `RecyclerView` 。 介面卡會使用視圖持有者來查閱 `ImageView` 和該位置的參考， `TextView` 因此 `FindViewById` 當使用者滾動相片集合並重複使用 views 時，不需要重複呼叫這些視圖。

在**RecyclerViewer**中，介面卡類別衍生自 `RecyclerView.Adapter` 以建立 `PhotoAlbumAdapter` ：

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;

    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }
    ...
}
```

`mPhotoAlbum`成員包含傳遞至此函式的資料來源（相片專輯）; 此函式會將相片專輯複製到這個成員變數中。 會實作為下列必要 `RecyclerView.Adapter` 方法：

- **`OnCreateViewHolder`** 具現 &ndash; 化專案配置檔案和視圖預留位置。

- **`OnBindViewHolder`** 將 &ndash; 位於指定位置的資料載入至其參考儲存在指定之視圖持有者的視圖中。

- **`ItemCount`** 傳回 &ndash; 資料來源中的專案數。

當版面建構管理員在中定位專案時，會呼叫這些方法 `RecyclerView` 。 下列各節會檢查這些方法的執行。

#### <a name="oncreateviewholder"></a>OnCreateViewHolder

版面建構管理員會在 `OnCreateViewHolder` `RecyclerView` 需要新的視圖持有者代表專案時呼叫。 `OnCreateViewHolder`從視圖的版面配置檔案擴大專案視圖，並在新的實例中包裝該視圖 `PhotoViewHolder` 。 此函式會 `PhotoViewHolder` 在配置中尋找並儲存子視圖的參考，如先前在[視圖預留位置](#view-holder)中所述。

每個資料列專案都會以 `CardView` 包含 `ImageView` （適用于相片）和 `TextView` （適用于標題）的來表示。 此配置位於 PhotoCardView 檔案中 **。 axml**：

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:card_view="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <android.support.v7.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        card_view:cardElevation="4dp"
        card_view:cardUseCompatPadding="true"
        card_view:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Caption"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="4dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</FrameLayout>
```

此版面配置代表中的單一資料列專案 `RecyclerView` 。 `OnBindViewHolder`方法（如下所述）會將資料來源中的資料複製到此配置的 `ImageView` 和中 `TextView` 。
`OnCreateViewHolder`在中擴大指定相片位置的此配置 `RecyclerView` ，並具現化新的 `PhotoViewHolder` 實例（這會在相關聯的配置中尋找和快取 `ImageView` 和子視圖的參考 `TextView` `CardView` ）：

```csharp
public override RecyclerView.ViewHolder
    OnCreateViewHolder (ViewGroup parent, int viewType)
{
    // Inflate the CardView for the photo:
    View itemView = LayoutInflater.From (parent.Context).
                Inflate (Resource.Layout.PhotoCardView, parent, false);

    // Create a ViewHolder to hold view references inside the CardView:
    PhotoViewHolder vh = new PhotoViewHolder (itemView);
    return vh;
}

```

產生的「視圖預留位置」實例 `vh` 會傳回給呼叫者（版面建構管理員）。

#### <a name="onbindviewholder"></a>OnBindViewHolder

當版面建構管理員準備好要在的可見螢幕區域中顯示特定的視圖時 `RecyclerView` ，它會呼叫介面卡的方法，在 `OnBindViewHolder` 指定的資料列位置填入專案，並提供來自資料來源的內容。 `OnBindViewHolder`取得指定的資料列位置（相片的影像資源和相片標題的字串）的相片資訊，並將此資料複製到相關聯的視圖。 Views 是透過存放在 view 預留位置物件中的參考來尋找（會透過參數傳入 `holder` ）：

```csharp
public override void
    OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
{
    PhotoViewHolder vh = holder as PhotoViewHolder;

    // Load the photo image resource from the photo album:
    vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);

    // Load the photo caption from the photo album:
    vh.Caption.Text = mPhotoAlbum[position].Caption;
}
```

傳入的 view 預留位置物件必須先轉換成衍生的視圖預留位置類型（在此案例中為）， `PhotoViewHolder` 才能使用它。
介面卡會將影像資源載入至 view 持有人的屬性所參考的視圖 `Image` 中，並將標題文字複製到 view 持有人的屬性所參考的視圖中 `Caption` 。 這*會*將相關聯的視圖與其資料系結。

請注意， `OnBindViewHolder` 是直接處理資料結構的程式碼。 在此情況下， `OnBindViewHolder` 瞭解如何將 `RecyclerView` 專案位置對應至資料來源中的相關資料項目。 在此情況下，此對應很簡單，因為此位置可當做相片專輯的陣列索引使用;不過，更複雜的資料來源可能需要額外的程式碼，才能建立這種對應。

#### <a name="itemcount"></a>ItemCount

方法會傳回 `ItemCount` 資料集合中的專案數。 在範例相片檢視器應用程式中，專案計數是相片專輯中的相片數目：

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

如需的詳細資訊 `RecyclerView.Adapter` ，請參閱[RecyclerView. Adapter 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)。

### <a name="putting-it-all-together"></a>將它全部放在一起

範例相片應用程式所產生的結果是 `RecyclerView` 由 `MainActivity` 建立資料來源、版面建構管理員和介面卡的程式碼所組成。 `MainActivity`建立 `mRecyclerView` 實例、具現化資料來源和介面卡，並插入版面建構管理員和介面卡：

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        mPhotoAlbum = new PhotoAlbum();
        SetContentView (Resource.Layout.Main);
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug in the linear layout manager:
        mLayoutManager = new LinearLayoutManager (this);
        mRecyclerView.SetLayoutManager (mLayoutManager);

        // Plug in my adapter:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
        mRecyclerView.SetAdapter (mAdapter);
    }
}

```

`PhotoViewHolder`尋找並快取視圖參考：

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

`PhotoAlbumAdapter`會執行三個必要的方法覆寫：

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;
    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }

    public override RecyclerView.ViewHolder
        OnCreateViewHolder (ViewGroup parent, int viewType)
    {
        View itemView = LayoutInflater.From (parent.Context).
                    Inflate (Resource.Layout.PhotoCardView, parent, false);
        PhotoViewHolder vh = new PhotoViewHolder (itemView);
        return vh;
    }

    public override void
        OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
    {
        PhotoViewHolder vh = holder as PhotoViewHolder;
        vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);
        vh.Caption.Text = mPhotoAlbum[position].Caption;
    }

    public override int ItemCount
    {
        get { return mPhotoAlbum.NumPhotos; }
    }
}
```

當此程式碼經過編譯和執行時，它會建立基本的相片觀賞應用程式，如下列螢幕擷取畫面所示：

[![相片觀賞應用程式的兩個螢幕擷取畫面與垂直捲動相片卡片](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

如果沒有繪製陰影（如上述螢幕擷取畫面所示），請編輯**Properties/androidmanifest.xml** ，並將下列屬性設定新增至 `<application>` 元素：

```xml
android:hardwareAccelerated="true"
```

此基本應用程式僅支援流覽相片專輯。 它不會回應專案觸控事件，也不會處理基礎資料中的變更。 這項功能是在[擴充 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)中新增的。

### <a name="changing-the-layoutmanager"></a>變更 LayoutManager

因為 `RecyclerView` 有彈性，所以可以輕鬆地將應用程式修改為使用不同的版面建構管理員。 在下列範例中，它已修改為顯示具有格線版面配置的相片專輯，而不是垂直線性版面配置。 若要這麼做，建構管理員具現化會修改為使用，如下所示 `GridLayoutManager` ：

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

這段程式碼變更會將垂直替換 `LinearLayoutManager` 成 `GridLayoutManager` ，其呈現由以水準方向滾動的兩個數據列組成的方格。 當您再次編譯並執行應用程式時，您會看到相片顯示在方格中，而且捲軸是水準的，而不是垂直的：

[![在方格中使用水準滾動相片之應用程式的範例螢幕擷取畫面](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

只要變更一行程式碼，就可以修改相片觀賞應用程式，以使用不同的版面配置，並具有不同的行為。
請注意，介面卡程式碼和版面配置 XML 都不需要修改來變更版面配置樣式。 

在下一個主題中，[延伸 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)時，這個基本範例應用程式會擴充以處理專案點擊事件，並 `RecyclerView` 在基礎資料來源變更時進行更新。

## <a name="related-links"></a>相關連結

- [RecyclerViewer （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 元件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [擴充 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
