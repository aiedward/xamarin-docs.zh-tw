---
title: 延伸 RecyclerView 範例
description: RecyclerView 範例應用程式中加入項目 click 事件處理常式。
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: eca0f58a470228ce8e6331defe88c1ef727cef57
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105177"
---
# <a name="extending-the-recyclerview-example"></a>延伸 RecyclerView 範例


基本的應用程式中所述[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)實際上還沒有其他&ndash;只是捲動，並顯示相片項目，以便瀏覽的固定的清單。 在真實世界應用程式，使用者應該能夠藉由點選中顯示的項目互動的應用程式。 此外，基礎資料來源可以變更 （或變更應用程式），而且顯示的內容必須保持一致與這些變更的功能。 在下列章節中，您將了解如何處理項目按一下事件，並更新`RecyclerView`當基礎資料來源變更。


### <a name="handling-item-click-events"></a>處理項目按一下事件

當使用者碰觸的項目中`RecyclerView`，產生的項目按一下事件通知哪一種項目已接觸到的應用程式。 此事件不會產生`RecyclerView`&ndash;相反地，項目檢視 （也就包裝在檢視持有者） 偵測到的修飾，並會回報這些修飾，按一下 [事件]。

為了說明如何處理項目按一下事件，下列步驟會說明基本的相片檢視應用程式如何修改的報表的照片有被接觸到的使用者。 範例應用程式中的項目按一下事件時，下列順序發生：

1.  相片的`CardView`偵測到的項目 click 事件，並通知配接器。

2.  配接器會將 （具有項目位置的資訊） 的事件轉送至活動的項目-click 處理常式。

3.  活動的項目-click 處理常式會回應項目的 click 事件。

首先，事件處理常式成員呼叫`ItemClick`新增至`PhotoAlbumAdapter`類別定義：

```csharp
public event EventHandler<int> ItemClick;
```

接下來，將項目按一下 事件處理常式方法加入至`MainActivity`。
這個處理常式會短暫顯示快顯通知，指出已觸及的相片項目：

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

接下來，若要註冊所需一行程式碼`OnItemClick`處理常式和`PhotoAlbumAdapter`。 若要這樣做的好地方是緊接`PhotoAlbumAdapter`建立： 

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

在此基本範例中，處理常式註冊進行中的主要活動`OnCreate`方法，但生產應用程式可能會註冊中的處理常式`OnResume`和取消註冊在`OnPause`&ndash;看到[活動開發週期](~/android/app-fundamentals/activity-lifecycle/index.md)如需詳細資訊。

`PhotoAlbumAdapter` 現在會呼叫`OnItemClick`當它收到的項目按一下事件。 下一個步驟是建立處理常式中的介面卡，會將此引發`ItemClick`事件。 下列方法`OnClick`，緊接在後面的介面卡新增`ItemCount`方法：

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

這`OnClick`方法是配接器的*接聽程式*的項目檢視的項目按一下事件。 與項目檢視 （透過項目檢視的檢視持有者），可以註冊此接聽程式之前`PhotoViewHolder`建構函式必須修改才能接受做為額外的引數，這個方法，並註冊`OnClick`項目檢視`Click`事件。
以下是 已修改`PhotoViewHolder`建構函式：

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

`itemView`參數包含參考`CardView`，已處理的使用者。 請注意檢視持有者的基底類別知道項目配置位置 (`CardView`)，它表示 (透過`LayoutPosition`屬性)，而這個位置會傳遞至配接器的`OnClick`方法在項目按一下事件發生時。 配接器的`OnCreateViewHolder`方法會將配接器的修改`OnClick`檢視預留位置的建構函式的方法：

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

現在當您建置並執行範例相片檢視應用程式時，點選在顯示的相片會導致報告已觸及的相片會出現快顯通知：

[![在點選時相片卡片所顯示的範例快顯通知](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

此範例示範只是其中一種方法實作事件處理常式與`RecyclerView`。 無法在這裡使用的另一種方法是將事件放在檢視持有者，並有訂閱這些事件的介面卡。 如果範例相片應用程式提供相片編輯功能，會需要不同的事件`ImageView`而`TextView`每個內`CardView`： 會牽涉到`TextView`會啟動`EditView`對話方塊，讓使用者編輯標題和修飾上的`ImageView`會啟動相片 touchup 工具可讓使用者裁剪或旋轉相片。 根據您的應用程式的需求，您必須設計最適合處理和回應觸控事件的方法。

若要示範如何`RecyclerView`可以更新時可以修改的資料集的變更，範例相片檢視應用程式，以隨機挑選資料來源中的相片，並交換與第一張相片。 首先，**隨機挑選** 按鈕新增至範例相片應用程式**Main.axml**版面配置：

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

接下來，程式碼會加入主活動的結尾處`OnCreate`方法來找出`Random Pick`按鈕在配置中，並在附加的處理常式：

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

此處理常式會呼叫相簿`RandomSwap`方法時**隨機挑選**點選按鈕時。 `RandomSwap`方法隨機交換第一張相片，資料來源中之相片，則會傳回隨機交換相片的索引。 當您編譯和執行範例應用程式，以下列程式碼時，點選**隨機挑選** 按鈕不會導致顯示變更因為`RecyclerView`並不知道資料來源的變更。

要保留`RecyclerView`之後的資料來源的變更，更新**隨機挑選**按一下 處理常式必須呼叫配接器的修改`NotifyItemChanged`方法已變更之集合中的每個項目 （在此情況下，兩個項目有變更： 第一張相片並已交換的相片)。 這會導致`RecyclerView`來更新它的顯示畫面，使其與新資料來源的狀態一致：

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

現在，當**隨機挑選**點選按鈕時，`RecyclerView`更新以顯示該相片進一步向下集合中已與交換集合中第一張相片的顯示：

[![交換，交換後的第二個螢幕擷取畫面之前的第一個螢幕擷取畫面](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

當然`NotifyDataSetChanged`可能呼叫而不是將兩個呼叫`NotifyItemChanged`，因此會強制這麼做，但`RecyclerView`重新整理整個集合，即使只有兩個集合中的項目已變更。 呼叫`NotifyItemChanged`會比呼叫更具效率`NotifyDataSetChanged`。


## <a name="related-links"></a>相關連結

- [RecyclerViewer （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 組件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [基本的 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
