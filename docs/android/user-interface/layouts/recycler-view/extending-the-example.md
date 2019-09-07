---
title: 擴充 RecyclerView 範例
description: 將專案按一下事件處理常式新增至 RecyclerView 範例應用程式。
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: 2fc2eabb26a9635b569cc6d3a51195fb554ca950
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758604"
---
# <a name="extending-the-recyclerview-example"></a>擴充 RecyclerView 範例

[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)中所述的基本應用程式實際上並不&ndash;會這麼做，只是滾動並顯示固定的相片專案清單，以方便流覽。 在真實世界的應用程式中，使用者希望能夠藉由點擊顯示中的專案來與應用程式互動。 此外，基礎資料來源也可以變更（或由應用程式變更），而且顯示的內容必須與這些變更保持一致。 在下列各節中，您將學習如何在基礎資料來源變更時處理`RecyclerView`專案點擊事件和更新。

### <a name="handling-item-click-events"></a>處理專案點擊事件

當使用者觸及中`RecyclerView`的專案時，會產生專案按下事件，以通知應用程式已觸及哪一個專案。 這個事件不是由所`RecyclerView`產生，而是由&ndash;專案視圖（包裝在視圖預留位置中）偵測到觸控，並將這些觸控視為 click 事件。

為了說明如何處理專案點擊事件，下列步驟將說明如何修改基本相片觀賞應用程式，以報告使用者已觸及哪些相片。 當範例應用程式中發生專案按下事件時，會發生下列順序：

1. 相片`CardView`會偵測到專案點擊事件並通知介面卡。

2. 介面卡會將事件（含有專案位置資訊）轉送到活動的專案按一下處理常式。

3. 活動的專案按一下處理常式會回應專案點擊事件。

首先，會將名`ItemClick`為的事件處理常式成員加入`PhotoAlbumAdapter`至類別定義：

```csharp
public event EventHandler<int> ItemClick;
```

接下來，會將專案按一下事件處理常式方法加入`MainActivity`至。
此處理程式會短暫顯示一個快顯通知，指出觸及了哪個相片專案：

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

接下來，需要`OnItemClick` `PhotoAlbumAdapter`程式程式碼以向註冊處理常式。 建立之後`PhotoAlbumAdapter` ，您可以立即完成此動作： 

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

在此基本範例中，處理常式註冊會在主要活動的`OnCreate`方法中進行，但生產應用程式可能會在`OnResume`中註冊處理常式`OnPause` ，並在中&ndash;將其取消註冊，以查看更多的[活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)更多資訊.

`PhotoAlbumAdapter`現在會在`OnItemClick`收到專案點擊事件時呼叫。 下一個步驟是在介面卡中建立可引發此`ItemClick`事件的處理常式。 下列方法`OnClick`會緊接在介面卡的`ItemCount`方法之後加入：

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

這個`OnClick`方法是來自專案視圖之專案按一下事件的介面卡*接聽程式。* 在`PhotoViewHolder`此接聽程式可以向專案視圖註冊（透過專案視圖的視圖持有者）之前，必須修改此函式，以接受此方法做為其他引數， `OnClick`並向專案視圖`Click`事件註冊。
以下是修改過`PhotoViewHolder`的構造函式：

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

參數包含使用者觸及之的`CardView`參考。 `itemView` 請注意，「視圖預留位置」基類知道它所代表`CardView` `LayoutPosition`之專案（）的版面配置位置（透過屬性），而這個位置會在專案按下事件`OnClick`發生時傳遞至介面卡的方法。 介面卡`OnCreateViewHolder`的方法經過修改後，會將`OnClick`介面卡的方法傳遞至視圖持有者的函式：

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

現在，當您建立並執行相片觀看應用程式範例時，在顯示畫面中的相片將會出現快顯通知，報告已觸及的相片：

[![點擊相片卡時顯示的範例快顯](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

這個範例只示範使用`RecyclerView`來執行事件處理常式的一種方法。 這裡可使用的另一種方法是將事件放在視圖持有者上，並讓介面卡訂閱這些事件。 如果範例相片應用程式提供相片編輯功能，則會需要個別`ImageView`的事件，而且`TextView`每個`TextView` `CardView`中的都是：上的觸控會`EditView`啟動可讓使用者編輯的對話方塊標題和上`ImageView`的觸控會啟動相片 touchup 工具，讓使用者裁剪或旋轉相片。 視您應用程式的需求而定，您必須設計處理和回應觸控事件的最佳方法。

為了示範如何`RecyclerView`在資料集變更時進行更新，可以修改相片觀看應用程式範例，以隨機挑選資料來源中的相片，並將它與第一張相片交換。 首先，會將**隨機挑選**按鈕新增至範例相片應用程式的**axml**版面配置：

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

接下來，在主要活動的`OnCreate`方法結尾加入程式碼，以找出配置中的`Random Pick`按鈕，並將處理常式附加至它：

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

這個處理常式會在按下`RandomSwap` [**隨機挑選**] 按鈕時，呼叫相片專輯的方法。 `RandomSwap`方法會隨機交換相片與資料來源中的第一張相片，然後傳回隨機交換相片的索引。 當您使用此程式碼編譯並執行範例應用程式時，點擊 [**隨機播放**] 按鈕並不會造成顯示變更`RecyclerView` ，因為不知道資料來源的變更。

若要`RecyclerView`在資料來源變更之後繼續更新，您必須修改**隨機挑選**的 click 處理常式，以針對`NotifyItemChanged`集合中已變更的每個專案呼叫介面卡的方法（在此案例中，兩個專案已變更：第一張相片和已交換的相片）。 這會`RecyclerView`導致更新其顯示，使其與資料來源的新狀態一致：

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

現在，當您按下 [**隨機挑選**] `RecyclerView`按鈕時，會更新顯示畫面，以顯示集合中進一步的相片已與集合中的第一張相片交換：

[![交換前的第一個螢幕擷取畫面，交換後的第二個螢幕擷取畫面](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

當然， `NotifyDataSetChanged`您可能已呼叫，而不是對`NotifyItemChanged`進行兩次呼叫，但是這麼做會`RecyclerView`強制重新整理整個集合，即使集合中只有兩個專案已變更也一樣。 呼叫`NotifyItemChanged`遠比呼叫`NotifyDataSetChanged`更有效率。

## <a name="related-links"></a>相關連結

- [RecyclerViewer （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 元件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
