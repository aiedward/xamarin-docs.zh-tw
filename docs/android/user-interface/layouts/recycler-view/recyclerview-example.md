---
title: "基本的 RecyclerView 範例"
ms.topic: article
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 44ebc8098250da26762538cddf5a89ffac709d8e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="a-basic-recyclerview-example"></a>基本的 RecyclerView 範例


若要了解如何`RecyclerView`一般應用程式的運作方式，本主題探討[RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/)範例應用程式、 使用的簡單程式碼範例`RecyclerView`顯示相片的大型集合： 

[![用來顯示相片的 CardViews RecyclerView 應用程式的兩個螢幕擷取畫面](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer**使用[CardView](~/android/user-interface/controls/card-view.md)實作中的每個相片項目`RecyclerView`版面配置。 因為`RecyclerView`的效能優點，此範例應用程式是可以順暢且不會明顯造成延遲，快速地捲動大量相片的集合。


### <a name="an-example-data-source"></a>範例資料來源

在此範例應用程式中的"相簿 「 資料來源 (由`PhotoAlbum`類別) 提供`RecyclerView`與項目內容。
`PhotoAlbum` 是具有字幕; 相片的集合當您具現化它時，您會取得 32 相片的現成集合：

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

在每個相片執行個體`PhotoAlbum`公開的屬性，可讓您讀取其影像資源識別碼`PhotoID`，和它的標題字串`Caption`。 如此可依索引子存取每一張相片組織相片的集合。 例如，下列程式碼存取的影像資源識別碼和集合中的第十個相片的標題：

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` 也提供`RandomSwap`交換隨機選擇相片的集合中的其他集合中的第一個相片，您可以呼叫的方法：

```csharp
mPhotoAlbum.RandomSwap ();
```

因為的實作詳細資料`PhotoAlbum`不了解相關`RecyclerView`、`PhotoAlbum`原始程式碼未提及。 原始程式碼`PhotoAlbum`位於[PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs)中[RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/)範例應用程式。


### <a name="layout-and-initialization"></a>版面配置和初始化

配置檔， **Main.axml**，包含單一`RecyclerView`內`LinearLayout`:

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

請注意，您必須使用完整限定名稱**android.support.v7.widget.RecyclerView**因為`RecyclerView`封裝在支援程式庫中。 `OnCreate`方法`MainActivity`初始化這個版面配置、 具現化配接器，並準備基礎資料來源：

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

此程式碼會執行下列動作：

1. 具現化`PhotoAlbum`資料來源。

2. 將相片專輯資料來源傳遞至建構函式的配接器， `PhotoAlbumAdapter` （定義稍後在本指南）。 
   請注意，它會被視為最佳做法是將資料來源做為參數傳遞給配接器的建構函式。 

3. 取得`RecyclerView`與配置。

4. 外掛到配接器`RecyclerView`藉由呼叫的執行個體`RecyclerView``SetAdapter`方法如上所示。

### <a name="layout-manager"></a>配置管理員

每個項目`RecyclerView`組成`CardView`，其中包含相片的映像和相片的標題 (詳細資訊則包含在[檢視持有者](#view-holder)下一節)。 預先定義`LinearLayoutManager`用於配置每個`CardView`垂直捲動排列：

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

此程式碼位於主要活動`OnCreate`方法。 配置管理員建構函式需要*內容*，因此`MainActivity`使用傳遞`this`如以上所示。

而不是使用 predefind `LinearLayoutManager`，您可以插入兩個顯示的自訂版面配置管理員`CardView`項目並存，實作翻頁的動畫效果來瀏覽相片的集合。 稍後在本指南中，您會看到如何藉由交換不同的版面配置管理員中修改配置的範例。

<a name="view-holder" />

### <a name="view-holder"></a>檢視持有者

檢視持有者類別稱為`PhotoViewHolder`。 每個`PhotoViewHolder`執行個體會保留參考`ImageView`和`TextView`相關聯的資料列的項目中已配置版面的`CardView`這裡訂位如下：

[![包含 ImageView 和 TextView CardView 的圖表](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` 衍生自`RecyclerView.ViewHolder`，並包含參考屬性`ImageView`和`TextView`上述配置所示。
`PhotoViewHolder` 兩個屬性和一個建構函式組成：

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
在此程式碼範例中，`PhotoViewHolder`父項目檢視的參考傳遞給建構函式 ( `CardView`)，`PhotoViewHolder`包裝。 請注意，您永遠轉送給父項目 」 檢視的基底建構函式。 `PhotoViewHolder`建構函式呼叫`FindViewById`上找出其子檢視的參考，每個父項目檢視`ImageView`和`TextView`，儲存在結果`Image`和`Caption`屬性，分別。 配接器稍後檢視的參考從擷取這些屬性時就更新此`CardView`的子檢視，以新的資料。

如需有關`RecyclerView.ViewHolder`，請參閱[RecyclerView.ViewHolder 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。


### <a name="adapter"></a>配接器

配接器會載入每個`RecyclerView`與特定的臉正面相片的資料列。 針對給定資料列位置相片*P*，比方說，配接器會找出相關聯的資料位置*P*內的資料來源和複製這項資料給資料列項目位置*P*中`RecyclerView`集合。 配接器使用查閱的參考，檢視持有者`ImageView`和`TextView`在該位置，因此不需要重複呼叫`FindViewById`的這些檢視當使用者捲動相片的集合，並重複使用的檢視。

在**RecyclerViewer**，配接器類別衍生自`RecyclerView.Adapter`建立`PhotoAlbumAdapter`:

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

`mPhotoAlbum`成員包含資料來源 （相簿） 傳遞到建構函式，則建構函式會複製到此成員變數的相簿。 下列必要`RecyclerView.Adapter`方法實作：

-   **`OnCreateViewHolder`** &ndash; 具現化項目配置檔案及 檢視擁有者。

-   **`OnBindViewHolder`** &ndash; 將位於指定位置的資料載入到其參考會儲存在指定的檢視擁有者的檢視。

-   **`ItemCount`** &ndash; 資料來源中傳回的項目數。

配置管理員呼叫這些方法，而它會定位項目內`RecyclerView`。 這些方法的實作會檢查下列各節。


#### <a name="oncreateviewholder"></a>OnCreateViewHolder

版面配置管理員呼叫`OnCreateViewHolder`時`RecyclerView`需要新的檢視預留位置，表示項目。 `OnCreateViewHolder` 「 充氣 」 檢視的配置檔案中的項目檢視，並將包裝在新檢視`PhotoViewHolder`執行個體。 `PhotoViewHolder`建構函式會找出並配置中儲存子檢視的參考，如先前所述[檢視持有者](#view-holder)。

每個資料列項目由`CardView`包含`ImageView`（適用於相片） 和`TextView`（適用於標題）。 此配置命名為位於檔案**PhotoCardView.axml**:

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

這個配置表示的單一資料列項目`RecyclerView`。 `OnBindViewHolder`方法 （如下所述） 將資料複製到資料來源`ImageView`和`TextView`的版面配置。
`OnCreateViewHolder` 「 充氣 」 這個配置的給定的相片位置`RecyclerView`和具現化新`PhotoViewHolder`執行個體 (其會找出並快取的參考`ImageView`和`TextView`中相關聯的子檢視`CardView`版面配置):

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

產生的檢視擁有者執行個體， `vh`，傳回至呼叫端 （配置管理員）。


#### <a name="onbindviewholder"></a>OnBindViewHolder

當配置管理員已準備好顯示中的特定檢視`RecyclerView`的可見螢幕區，它會呼叫配接器的`OnBindViewHolder`方法來填滿指定的資料列與資料來源的內容位置的項目。 `OnBindViewHolder` 取得指定的資料列位置 （相片影像資源和相片的標題的字串） 的相片資訊，並將此資料複製至相關聯的檢視。 透過儲存在檢視持有者物件的參考位於檢視 (這透過傳入`holder`參數):

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

傳入的檢視擁有者物件必須先轉換成衍生的檢視擁有者類型 (在此情況下， `PhotoViewHolder`) 之前，它會使用。
配接器會載入檢視持有人所參考的檢視中的影像資源`Image`屬性，而且它會將標題文字複製到檢視持有人所參考的檢視`Caption`屬性。 這*繫結*它的資料相關聯的檢視。

請注意，`OnBindViewHolder`是直接處理的資料結構的程式碼。 在此情況下，`OnBindViewHolder`了解如何將對應`RecyclerView`項目至其相關聯的資料項目中的資料來源的位置。 對應是直接在此情況下因為位置可以做為陣列索引的相簿。不過，更複雜的資料來源可能需要額外的程式碼來建立這類對應。


#### <a name="itemcount"></a>ItemCount

`ItemCount`方法傳回資料集合中的項目數。 在範例相片檢視器應用程式，將項目計數會是相簿的相片數目：

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

如需有關`RecyclerView.Adapter`，請參閱[RecyclerView.Adapter 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)。


### <a name="putting-it-all-together"></a>加以整合

產生`RecyclerView`範例相片應用程式的實作包含`MainActivity`建立資料來源、 配置管理員和配接器的程式碼。 `MainActivity` 建立`mRecyclerView`執行個體，會具現化的資料來源和配接器，並插入配置管理員和配接器：

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

`PhotoViewHolder` 找出並檢視的參考會快取：

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

`PhotoAlbumAdapter` 會實作三個所需的方法覆寫：

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

當此程式碼會編譯並執行時，它會建立基本的相片，檢視應用程式，如下列螢幕擷取畫面所示：

[![檢視應用程式與垂直捲動相片卡張相片的兩個螢幕擷取畫面](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

此基本應用程式只支援瀏覽的相簿。 它不會回應項目觸控事件，也不會處理基礎資料中的變更。 這項功能加入在[延伸 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)。


### <a name="changing-the-layoutmanager"></a>變更 LayoutManager

因為`RecyclerView`的彈性，所以可以輕鬆地修改應用程式使用不同的版面配置管理員。 在下列範例中，它會修改成顯示水平捲動的格線版面配置，而非垂直線性版面配置的相簿。 若要這樣做，請配置管理員具現化會修改成使用`GridLayoutManager`，如下所示：

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

此程式碼變更會取代垂直`LinearLayoutManager`與`GridLayoutManager`呈現的水平方向捲動的兩個資料列所組成的方格。 當您編譯，然後再次執行應用程式時，您會看到所拍攝的相片會顯示在方格中，並捲動水平，而不是垂直：

[![具有水平捲動方格中的相片應用程式的範例螢幕擷取畫面](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

藉由變更只有一行的程式碼，是可以修改相片檢視應用程式使用不同的版面配置與不同的行為。
請注意，配接器程式碼都配置 XML 必須修改，以變更配置樣式。 

在下一個主題中，[延伸 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)，這個基本範例應用程式將會擴充以處理項目按一下事件，並更新`RecyclerView`當基礎資料來源變更。



## <a name="related-links"></a>相關連結

- [RecyclerViewer （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 組件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [擴充 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
