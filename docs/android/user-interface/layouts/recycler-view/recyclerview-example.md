---
title: 基本 RecyclerView 範例
description: 示範如何使用 RecyclerView 的範例應用程式。
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/30/2018
ms.openlocfilehash: d4a775d1e96fd6650623c2a151ae74b8b68ce0ac
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457077"
---
# <a name="a-basic-recyclerview-example"></a>基本 RecyclerView 範例

為了瞭解如何 `RecyclerView` 在一般應用程式中運作，本主題將探索 [RecyclerViewer](/samples/xamarin/monodroid-samples/android50-recyclerviewer) 範例應用程式，這是一個簡單的程式碼範例，用 `RecyclerView` 來顯示大型的相片集合： 

[![RecyclerView 應用程式的兩個螢幕擷取畫面，其使用 CardViews 來顯示相片](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** 會使用 [CardView](~/android/user-interface/controls/card-view.md) 來執行版面配置中的每個相片專案 `RecyclerView` 。 基於 `RecyclerView` 效能的優勢，此範例應用程式能夠快速地快速地滾動大量的相片，而不會有明顯的延遲。

### <a name="an-example-data-source"></a>範例資料來源

在此範例應用程式中，「相片專輯」資料來源 (由類別表示， `PhotoAlbum`) 提供 `RecyclerView` 專案內容。
`PhotoAlbum` 是具有標題的相片集合;當您將它具現化時，您會取得現成的32相片集合：

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

中的每個相片實例 `PhotoAlbum` 都會公開屬性，這些屬性可讓您讀取其影像資源識別碼、 `PhotoID` 和其標題字串 `Caption` 。 相片集合的組織方式是讓索引子可以存取每個相片。 例如，下列程式程式碼會存取集合中第十張相片的影像資源識別碼和標題：

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` 也提供一種 `RandomSwap` 方法，您可以呼叫此方法，將集合中的第一張相片與集合中的其他位置隨機播放的相片交換：

```csharp
mPhotoAlbum.RandomSwap ();
```

由於的執行詳細資料與 `PhotoAlbum` 理解無關，因此 `RecyclerView` `PhotoAlbum` 這裡不會顯示原始程式碼。 您可以在 `PhotoAlbum` [RecyclerViewer](/samples/xamarin/monodroid-samples/android50-recyclerviewer)範例應用程式的[PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs)取得原始程式碼。

### <a name="layout-and-initialization"></a>版面配置和初始化

版面配置檔案 **.axml**是由中的單一組成 `RecyclerView` `LinearLayout` ：

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

請注意，您必須使用完整名稱 **V7 RecyclerView** ，因為 `RecyclerView` 封裝在支援程式庫中。 的 `OnCreate` 方法會 `MainActivity` 初始化此版面配置、將介面卡具現化，並準備基礎資料來源：

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

2. 將相片專輯資料來源傳遞至介面卡的函式， `PhotoAlbumAdapter` (本指南稍後所定義) 。 
   請注意，將資料來源當作參數傳遞至介面卡的函式，是最佳做法。 

3. 從配置取得 `RecyclerView` 。

4. 藉由呼叫方法將介面卡插入實例中，如上 `RecyclerView` `RecyclerView` `SetAdapter` 所示。

### <a name="layout-manager"></a>版面建構管理員

中的每個專案 `RecyclerView` 都是由 `CardView` 包含相片影像和相片標題的所組成， (詳細資料會在下面) 的 [ [View 刀柄](#view-holder) ] 區段中討論。 預先定義的 `LinearLayoutManager` 可用來 `CardView` 在垂直捲動排列中配置每一個：

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

此程式碼位於主要的活動 `OnCreate` 方法中。 建構管理員的函式需要 *內容*，因此 `MainActivity` 會使用如上所示傳遞 `this` 。

您可以不使用預先定義的，而是 `LinearLayoutManager` 插入自訂的版面建構管理員，以並排顯示兩個 `CardView` 專案，並執行翻頁動畫效果，以跨越相片的集合。 稍後在本指南中，您將會看到如何藉由在不同的版面建構管理員中交換來修改版面配置的範例。

<a name="view-holder"></a>

### <a name="view-holder"></a>查看預留位置

呼叫 view 刀柄類別 `PhotoViewHolder` 。 每個 `PhotoViewHolder` 實例 `ImageView` 都會保留 `TextView` 相關聯之資料列專案的參考，而該專案會在中配置 `CardView` 為圖表，如下所示：

[![包含 >imageview 和 TextView 的 CardView 圖表](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` 衍生自 `RecyclerView.ViewHolder` 並且包含屬性，以儲存對的參考 `ImageView` ，並 `TextView` 顯示于上述配置中。
`PhotoViewHolder` 包含兩個屬性和一個函式：

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

在此程式碼範例中， `PhotoViewHolder` 會將父專案視圖的參考傳遞給函式， (`CardView` 包裝的) `PhotoViewHolder` 。 請注意，您一律會將父專案視圖轉寄至基底的函式。 函式會 `PhotoViewHolder` `FindViewById` 在父專案視圖上呼叫，以找出其子視圖參考， `ImageView` 以及 `TextView` 分別將結果儲存在 `Image` 和屬性中 `Caption` 。 介面卡會在使用新資料更新此子視圖時，從這些屬性抓取 view 參考 `CardView` 。

如需的詳細資訊 `RecyclerView.ViewHolder` ，請參閱 [RecyclerView. ViewHolder 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。

### <a name="adapter"></a>配接器

介面卡會載入每個 `RecyclerView` 資料列，其中包含特定相片的資料。 例如，針對位於資料列位置 *p*的指定相片，此介面卡會在資料來源內的位置 *p* 找出相關聯的資料，並將此資料複製到集合中位置 *p* 的資料列專案 `RecyclerView` 。 介面卡會使用視圖持有者來查閱和位置的參考， `ImageView` `TextView` 因此 `FindViewById` 當使用者滾動相片集合並重複使用視圖時，不需要重複呼叫這些視圖。

在 **RecyclerViewer**中，從衍生的介面卡類別 `RecyclerView.Adapter` 可建立 `PhotoAlbumAdapter` ：

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

`mPhotoAlbum`成員包含 (已傳遞至函式之相片專輯) 的資料來源; 此函式會將相片專輯複製到這個成員變數。 下列是實作為的必要 `RecyclerView.Adapter` 方法：

- **`OnCreateViewHolder`** 具現 &ndash; 化專案配置檔案和查看預留位置。

- **`OnBindViewHolder`** 將 &ndash; 位於指定位置的資料載入到其參考儲存在指定之視圖持有人的視圖中。

- **`ItemCount`** 傳回 &ndash; 資料來源中的專案數。

當版面建構管理員在中放置專案時，會呼叫這些方法 `RecyclerView` 。 下列各節會檢查這些方法的執行方式。

#### <a name="oncreateviewholder"></a>OnCreateViewHolder

版面建構管理員會在 `OnCreateViewHolder` `RecyclerView` 需要新的視圖持有者來代表專案時呼叫。 `OnCreateViewHolder` 從視圖的版面配置檔案中擴大專案視圖，並將視圖包裝在新的 `PhotoViewHolder` 實例中。 此函式會 `PhotoViewHolder` 在版面配置中尋找並儲存子視圖的參考，如先前在 [View 預留位置](#view-holder)中所述。

每個資料列專案都是以 `CardView` 包含 `ImageView` 相片)  (以及 `TextView` 標題)  (的表示。 此配置位於 **PhotoCardView. .axml**：

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

這個版面配置代表中的單一資料列專案 `RecyclerView` 。 `OnBindViewHolder`以下所述的方法 () 將資料來源中的資料複製到這個配置的 `ImageView` 和中 `TextView` 。
`OnCreateViewHolder` 在中為指定的相片位置擴大此配置 `RecyclerView` ，並將新的 (實例具現化，以在 `PhotoViewHolder` `ImageView` `TextView` 相關聯的配置) 中尋找和快取和子視圖的參考 `CardView` ：

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

產生的視圖預留位置實例 `vh` 會傳回給呼叫者 (建構管理員) 。

#### <a name="onbindviewholder"></a>OnBindViewHolder

當版面建構管理員準備好在可見的螢幕區域中顯示特定的視圖時 `RecyclerView` ，它會呼叫介面卡的 `OnBindViewHolder` 方法，以資料來源中的內容填滿指定的資料列位置上的專案。 `OnBindViewHolder` 取得指定之資料列位置的相片資訊 (相片的影像資源，以及相片標題) 的字串，並將此資料複製到相關聯的視圖。 您可以透過參數中所儲存的參考來尋找視圖， (透過 `holder` 參數) 傳入：

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

傳入的檢視器預留位置物件必須先轉換成衍生的視圖持有人類型 (在此案例中，在 `PhotoViewHolder` 使用之前) 。
介面卡會將影像資源載入至由視圖者的屬性所參考的視圖 `Image` ，並將標題文字複製到視圖持有者屬性所參考的視圖中 `Caption` 。 這會將相關聯的視圖系 *結到其* 資料。

請注意，這 `OnBindViewHolder` 是直接處理資料結構的程式碼。 在此情況下， `OnBindViewHolder` 瞭解如何將 `RecyclerView` 專案位置對應到資料來源中的相關資料項目。 在此情況下，對應很簡單，因為位置可以用來做為相片專輯的陣列索引;不過，更複雜的資料來源可能需要額外的程式碼來建立這類的對應。

#### <a name="itemcount"></a>ItemCount

方法會傳回 `ItemCount` 資料集合中的專案數。 在範例相片檢視器應用程式中，專案計數是照片專輯中的相片數目：

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

如需的詳細資訊 `RecyclerView.Adapter` ，請參閱 [RecyclerView Adapter 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)。

### <a name="putting-it-all-together"></a>把它全部放在一起

範例相片應用程式所產生的結果是 `RecyclerView` 由 `MainActivity` 可建立資料來源、版面建構管理員和介面卡的程式碼所組成。 `MainActivity` 建立 `mRecyclerView` 實例、具現化資料來源和介面卡，然後插入版面建構管理員和介面卡：

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

`PhotoViewHolder` 尋找並快取視圖參考：

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

`PhotoAlbumAdapter` 會執行三個必要的方法覆寫：

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

編譯並執行此程式碼時，會建立基本的相片觀賞應用程式，如下列螢幕擷取畫面所示：

[![相片觀看應用程式的兩個螢幕擷取畫面，其中包含垂直捲動照片卡](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

如果未繪製陰影 (如上面的螢幕擷取畫面所示) ，請編輯 **屬性/AndroidManifest.xml** ，然後將下列屬性設定加入至 `<application>` 元素：

```xml
android:hardwareAccelerated="true"
```

這個基本的應用程式只支援流覽相片專輯。 它不會回應專案觸控事件，也不會處理基礎資料中的變更。 這項功能已加入 [擴充 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)。

### <a name="changing-the-layoutmanager"></a>變更 LayoutManager

由於 `RecyclerView` 有彈性，因此很容易就能將應用程式修改為使用不同的版面建構管理員。 在下列範例中，它會經過修改，以顯示具有水準滾動之格線版面配置的相片專輯，而不是垂直線性版面配置。 若要這樣做，請將版面建構管理員具現化修改為使用，如下所示 `GridLayoutManager` ：

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

這段程式碼的變更 `LinearLayoutManager` 會以垂直 `GridLayoutManager` 方向的兩個數據列所組成的方格取代垂直。 當您再次編譯及執行應用程式時，您會看到相片顯示在方格中，而捲軸為水準而非垂直：

[![格線中具有水準滾動相片的應用程式範例螢幕擷取畫面](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

只要變更一行程式碼，就可以修改相片觀賞應用程式，以使用不同的版面配置，並使用不同的行為。
請注意，介面卡程式碼和版面配置 XML 都不需要修改來變更版面配置樣式。 

在下一個主題中，擴充 [RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)，這個基本的範例應用程式會擴充以處理專案點按事件，並在 `RecyclerView` 基礎資料來源變更時進行更新。

## <a name="related-links"></a>相關連結

- [RecyclerViewer (範例) ](/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 元件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [擴充 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)