---
title: 基本的 RecyclerView 範例
description: 示範如何使用 RecyclerView 範例應用程式。
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/30/2018
ms.openlocfilehash: d48796b3c62fc342bd86f2d58e74c5f1710174bb
ms.sourcegitcommit: 0a1c392829454468dbe92f81d975e124a22b7014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2018
ms.locfileid: "39360834"
---
# <a name="a-basic-recyclerview-example"></a>基本的 RecyclerView 範例

若要了解如何`RecyclerView`一般應用程式中的運作方式，本主題探討[RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/)範例應用程式、 使用簡單的程式碼範例`RecyclerView`顯示相片的大型集合： 

[![用以顯示相片 CardViews RecyclerView 應用程式的兩個螢幕擷取畫面](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer**會使用[CardView](~/android/user-interface/controls/card-view.md)實作中的每個相片項目`RecyclerView`版面配置。 因為`RecyclerView`的效能優點，此範例應用程式是能夠快速地捲動大量相片的集合，順暢且沒有明顯的延遲。


### <a name="an-example-data-source"></a>範例資料來源

在此範例應用程式，「 把相簿沖印 」 資料來源 (由`PhotoAlbum`類別) 提供`RecyclerView`與項目內容。
`PhotoAlbum` 是與標題; 相片集合當您具現化，您會收到現成 32 相片集合：

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

在每個相片執行個體`PhotoAlbum`公開屬性，可讓您讀取其映像資源識別碼`PhotoID`，和它的標題字串`Caption`。 相片集合經過組織，使得索引子可以存取每一張相片。 例如，下列程式碼存取的映像資源識別碼和集合中的第十個相片的標題：

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` 也提供`RandomSwap`交換集合中隨機選擇相片集合中的其他地方使用第一張相片，您可以呼叫的方法：

```csharp
mPhotoAlbum.RandomSwap ();
```

因為的實作詳細資料`PhotoAlbum`了解無關`RecyclerView`，則`PhotoAlbum`原始程式碼未提及。 原始程式碼`PhotoAlbum`將會位於[PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs)中[RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/)範例應用程式。


### <a name="layout-and-initialization"></a>版面配置和初始化

版面配置檔案中， **Main.axml**，包含單一`RecyclerView`內`LinearLayout`:

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

請注意，您必須使用的完整限定名稱**android.support.v7.widget.RecyclerView**因為`RecyclerView`封裝在支援程式庫。 `OnCreate`方法的`MainActivity`初始化這個版面配置、 具現化配接器，並做準備基礎資料來源：

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

2. 將相片專輯資料來源傳遞至建構函式的配接器， `PhotoAlbumAdapter` （這本指南稍後的定義）。 
   請注意，它會被視為最佳做法是傳遞做為參數的資料來源，配接器的建構函式。 

3. 取得`RecyclerView`與配置。

4. 外掛到配接器`RecyclerView`藉由呼叫的執行個體`RecyclerView``SetAdapter`方法如上所示。

### <a name="layout-manager"></a>配置管理員

在每個項目`RecyclerView`組成`CardView`，其中包含相片的映像和相片的標題 (詳細資料所述[檢視的持有者](#view-holder)下一節)。 預先定義`LinearLayoutManager`用來配置每個`CardView`垂直捲動排列：

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

此程式碼位於主要活動`OnCreate`方法。 配置管理員建構函式需要*內容*，因此`MainActivity`傳遞使用`this`如上所示。

而不是使用 predefind `LinearLayoutManager`，您可以插入會顯示兩個自訂的版面配置管理員`CardView`-並存，實作來周遊集合的相片的翻頁動畫效果的項目。 稍後在本指南中，您會看到如何藉由交換在不同的版面配置管理員中修改版面配置的範例。

<a name="view-holder" />

### <a name="view-holder"></a>檢視持有者

檢視持有者類別稱為`PhotoViewHolder`。 每個`PhotoViewHolder`執行個體所保有的參考`ImageView`並`TextView`項目的內容相關聯的資料列，其中的版面配置`CardView`這裡如：

[![包含 ImageView 和 TextView CardView 的圖表](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` 衍生自`RecyclerView.ViewHolder`並包含要儲存的參考屬性`ImageView`和`TextView`上述的版面配置 所示。
`PhotoViewHolder` 包含兩個屬性和一個建構函式：

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
在此範例中，`PhotoViewHolder`父項目檢視的參考傳遞給建構函式 ( `CardView`)，`PhotoViewHolder`包裝。 請注意，您一律轉送父項目 」 檢視的基底建構函式。 `PhotoViewHolder`建構函式呼叫`FindViewById`上父項目檢視，以找出其子檢視的參考，每個`ImageView`並`TextView`，儲存在結果`Image`和`Caption`屬性，分別。 配接器稍後擷取檢視參考這些屬性時它會更新這`CardView`的子檢視，以新的資料。

如需詳細資訊`RecyclerView.ViewHolder`，請參閱 < [RecyclerView.ViewHolder 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。


### <a name="adapter"></a>配接器

配接器載入每一個`RecyclerView`與特定的照片的資料列。 針對給定資料列位置照片*P*，比方說，配接器找出相關聯的資料位置*P*內的資料來源和複製這項資料給資料列項目位置*P*在`RecyclerView`集合。 配接器使用的檢視持有者查閱的參考`ImageView`並`TextView`在該位置，因此不需要重複呼叫`FindViewById`為當使用者捲動相片集合，並重複使用檢視，這些檢視。

在  **RecyclerViewer**，配接器類別衍生自`RecyclerView.Adapter`建立`PhotoAlbumAdapter`:

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

`mPhotoAlbum`成員包含資料來源 （相簿） 傳遞至建構函式; 建構函式會將把相簿沖印複製到此成員變數。 下列必要`RecyclerView.Adapter`方法實作：

-   **`OnCreateViewHolder`** &ndash; 具現化項目配置檔案並檢視持有者。

-   **`OnBindViewHolder`** &ndash; 載入其參考儲存在指定的檢視持有者檢視中位於指定位置的資料。

-   **`ItemCount`** &ndash; 傳回資料來源中的項目數目。

配置管理員呼叫這些方法，而它定位內的項目`RecyclerView`。 這些方法的實作會檢查下列各節。


#### <a name="oncreateviewholder"></a>OnCreateViewHolder

版面配置管理員呼叫`OnCreateViewHolder`當`RecyclerView`需要新的檢視持有者，來代表項目。 `OnCreateViewHolder` 擴大項目檢視，從檢視版面配置檔案，並將包裝在新檢視`PhotoViewHolder`執行個體。 `PhotoViewHolder`建構函式會找出並將子檢視的參考儲存在版面配置，如先前所述[檢視的持有者](#view-holder)。

每個資料列項目由`CardView`，其中包含`ImageView`（適用於相片） 和`TextView`（適用於標題）。 此配置位於檔案**PhotoCardView.axml**:

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

這個配置表示的單一資料列項目`RecyclerView`。 `OnBindViewHolder` （如下所述） 的方法會將資料複製到資料來源`ImageView`和`TextView`的這個版面配置。
`OnCreateViewHolder` 擴大此配置指定的相片中的位置`RecyclerView`並具現化新`PhotoViewHolder`執行個體 (這會找出並快取的參考`ImageView`並`TextView`在相關聯的子檢視`CardView`版面配置):

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

產生的檢視持有者執行個體， `vh`，傳回到呼叫端 （版面配置管理員）。


#### <a name="onbindviewholder"></a>OnBindViewHolder

配置管理員時準備好顯示檢視中的特定`RecyclerView`的可見螢幕區，它會呼叫配接器的`OnBindViewHolder`方法來填滿資料來源的內容與指定的資料列位置的項目。 `OnBindViewHolder` 取得指定的資料列位置 （相片影像資源和相片的標題的字串） 的相片資訊，並將此資料複製到相關聯的檢視。 檢視位於透過儲存在檢視的持有者物件的參考 (這透過傳入`holder`參數):

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

傳入檢視的持有者物件必須先轉換成衍生的檢視擁有者型別 (在此情況下， `PhotoViewHolder`) 使用它之前。
配接器會將映像資源載入的檢視持有者所參考的檢視`Image`屬性，然後它會將標題文字複製到檢視持有人所參考的檢視`Caption`屬性。 這*繫結*它的資料相關聯的檢視。

請注意，`OnBindViewHolder`是直接處理資料結構的程式碼。 在此情況下，`OnBindViewHolder`了解如何將對應`RecyclerView`項目至其相關聯的資料項目中的資料來源的位置。 對應是直接在此情況下因為位置可用來當做陣列索引到相簿中;不過，更複雜的資料來源可能需要額外的程式碼來建立這類的對應。


#### <a name="itemcount"></a>ItemCount

`ItemCount`方法會傳回資料集合中的項目數目。 在範例相片檢視器應用程式中，將項目計數會是中把相簿沖印相片數目：

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

如需詳細資訊`RecyclerView.Adapter`，請參閱 < [RecyclerView.Adapter 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)。


### <a name="putting-it-all-together"></a>加以整合

產生`RecyclerView`範例相片應用程式的實作方法包括`MainActivity`會建立資料來源、 配置管理員和配接器的程式碼。 `MainActivity` 建立`mRecyclerView`執行個體，會具現化的資料來源和配接器，以及插入配置管理員和配接器：

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

`PhotoViewHolder` 找出並快取檢視參考：

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

當此程式碼編譯並執行時，它會建立基本的相片檢視應用程式，如下列螢幕擷取畫面所示：

[![檢視應用程式，使用垂直捲動相片卡片的相片的兩個螢幕擷取畫面](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

如果繪製陰影時未被 （如上述螢幕擷取畫面所示），編輯**properties/Androidmanifest.xml** ，並新增下列屬性設定加入`<application>`項目：

```xml
android:hardwareAccelerated="true"
```

此基本應用程式只支援把相簿沖印瀏覽。 它不會回應項目的觸控事件，也不會處理基礎資料中的變更。 這項功能會新增[延伸 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)。




### <a name="changing-the-layoutmanager"></a>變更 LayoutManager

因為`RecyclerView`的彈性，就可以輕鬆地修改應用程式使用不同的版面配置管理員。 在下列範例中，會修改它以顯示與水平捲動格線版面配置而不是以垂直的線性配置的相簿。 若要這樣做，版面配置管理員具現化會修改成使用`GridLayoutManager`，如下所示：

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

此程式碼變更會取代垂直`LinearLayoutManager`與`GridLayoutManager`其中會提供兩個的水平方向捲動的資料列所組成的方格。 當您編譯和重新執行應用程式時，您會看到的相片會顯示在方格中，並捲動的是水平，而不是垂直：

[![使用中格線的水平捲動相片的應用程式的範例螢幕擷取畫面](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

藉由變更只有一行程式碼，是可以修改相片檢視應用程式使用不同的版面配置使用不同的行為。
請注意，配接器程式碼和 XML 的配置都不需要修改，以變更配置樣式。 

在下一個主題中，[延伸 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)，此基本範例應用程式將會擴充以處理項目按一下事件，並更新`RecyclerView`當基礎資料來源變更。



## <a name="related-links"></a>相關連結

- [RecyclerViewer （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 組件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [延伸 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
