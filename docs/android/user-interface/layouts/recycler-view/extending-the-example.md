---
title: 擴充 RecyclerView 範例
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 83147261a2d5458272f7e2bc105154da4308f4b0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769259"
---
# <a name="extending-the-recyclerview-example"></a>擴充 RecyclerView 範例


基本應用程式中所述[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)實際上不會執行大部分&ndash;只要捲動，然後顯示固定的臉正面相片項目，以便瀏覽清單。 在真實世界的應用程式，使用者應該能夠藉由點選中顯示的項目與應用程式互動。 此外，在基礎資料來源可以變更 （或變更應用程式），並顯示的內容必須維持一致，這些變更。 在下列章節中，您將學習如何處理項目按一下事件，並更新`RecyclerView`當基礎資料來源變更。


### <a name="handling-item-click-events"></a>處理項目按一下事件

當使用者碰觸的項目`RecyclerView`，通知就哪項目已接觸到的應用程式產生的項目按一下事件。 這個事件不由產生`RecyclerView` &ndash; （這會包裝在檢視持有者） 的項目檢視偵測到修飾而報告這些工作，當按一下事件。

為了說明如何處理項目按一下事件，下列步驟說明基本的相片檢視應用程式如何修改的報表的臉正面相片已被接觸到的使用者。 範例應用程式中的項目按一下事件時，下列順序發生：

1.  相片`CardView`偵測到項目的 click 事件，並通知配接器。

2.  配接器會將 （含有項目位置資訊） 的事件轉送至活動的項目 click 處理常式。

3.  活動的項目 click 處理常式會回應項目 click 事件。

首先，事件處理常式成員呼叫`ItemClick`加入至`PhotoAlbumAdapter`類別定義：

```csharp
public event EventHandler<int> ItemClick;
```

接著，將項目按一下 事件處理常式方法加入至`MainActivity`。
這個處理常式會短暫顯示快顯通知，指出已觸及的臉正面相片項目：

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

接下來，程式碼行需要註冊`OnItemClick`處理常式和`PhotoAlbumAdapter`。 若要這樣做最好之後立即`PhotoAlbumAdapter`建立 (主要活動中`OnCreate`方法):

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

`PhotoAlbumAdapter` 現在將會呼叫`OnItemClick`當它收到的項目按一下事件。 下一個步驟是建立在引發此配接器處理常式`ItemClick`事件。 下面的方法， `OnClick`，新增配接器的後立即`ItemCount`方法：

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

這`OnClick`方法是配接器的*接聽程式*從項目檢視項目按一下事件。 此接聽程式可以向 （透過項目檢視的檢視持有者），項目檢視之前`PhotoViewHolder`建構函式必須修改才能接受做為額外的引數，這個方法，並註冊`OnClick`與項目檢視`Click`事件。
以下是 修改`PhotoViewHolder`建構函式：

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

`itemView`參數包含參考`CardView`，已接觸到的使用者。 請注意檢視持有者的基底類別知道版面配置的位置的項目 (`CardView`)，它代表 (透過`LayoutPosition`屬性)，而且這個位置傳遞至配接器的`OnClick`方法時，會在項目按一下事件。 配接器的`OnCreateViewHolder`方法會將配接器的修改`OnClick`檢視持有人建構函式的方法：

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

現在當您建置及執行範例相片檢視應用程式，點選在顯示的相片會導致出現報表已觸及的臉正面相片的快顯通知：

[![點選當相片卡時所顯示的範例快顯通知](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

這個範例會示範一個方法，實作事件處理常式取代`RecyclerView`。 無法在這裡使用的另一種方法是將事件放在檢視持有者，並讓配接器，訂閱這些事件。 如果範例相片應用程式提供相片編輯功能，會需要不同的事件`ImageView`和`TextView`內每個`CardView`： 會牽涉到`TextView`會啟動`EditView`對話方塊可讓使用者編輯標題和在修飾`ImageView`會啟動相片 touchup 工具，可讓使用者裁剪或旋轉相片。 根據您的應用程式需求，您必須設計處理，而且回應觸控事件的最佳方法。

若要示範如何`RecyclerView`時修改資料集的範例相片檢視應用程式變更為隨機選取資料來源中的相片，並在交換的第一張相片可以更新。 首先，**隨機挑選**按鈕會加入至範例相片應用程式**Main.axml**版面配置：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/randPickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:text="Random Pick" />
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

接下來，在主要活動的結尾處加入程式碼`OnCreate`方法來找出`Random Pick`按鈕版面配置中，並附加至處理常式：

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        // Randomly swap a photo with the first photo:
        int idx = mPhotoAlbum.RandomSwap();
    }
};

```

這個處理常式呼叫的相簿`RandomSwap`方法時**隨機挑選**所點選按鈕。 `RandomSwap`方法隨機交換第一張相片，資料來源中之相片，則會傳回隨機交換相片的索引。 當您編譯和執行這個程式碼的範例應用程式時，點選**隨機挑選**按鈕不會導致顯示變更因為`RecyclerView`並不知道資料來源的變更。

要保留`RecyclerView`後的資料來源的變更，更新**隨機挑選**按一下處理常式必須呼叫配接器的修改`NotifyItemChanged`方法已變更之集合中每個項目 （在此情況下，兩個項目有變更： 第一張相片和交換的相片)。 這會導致`RecyclerView`更新它的顯示畫面，使其與新資料來源的狀態一致：

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        int idx = mPhotoAlbum.RandomSwap();

        // First photo has changed:
        mAdapter.NotifyItemChanged(0);

        // Swapped photo has changed:
        mAdapter.NotifyItemChanged(idx);
    }
};

```

現在，當**隨機挑選**點選按鈕，`RecyclerView`更新以顯示該相片進一步向下集合中已交換與集合中第一張相片的顯示：

[![第一個螢幕擷取畫面之前交換，交換之後的第二個螢幕擷取畫面](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

當然，`NotifyDataSetChanged`無法呼叫而不是兩個呼叫`NotifyItemChanged`，因此可能會強制這樣做，但`RecyclerView`重新整理整個集合，即使只有兩個集合中的項目已變更。 呼叫`NotifyItemChanged`是更具效率比呼叫`NotifyDataSetChanged`。


## <a name="related-links"></a>相關連結

- [RecyclerViewer （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 組件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [基本的 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
