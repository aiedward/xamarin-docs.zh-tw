---
title: 擴充 RecyclerView 範例
description: 將專案 click 事件處理常式新增至 RecyclerView 範例應用程式。
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 80b7dc5554e0db525de7fcea274e82cdf3e8c7f4
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457122"
---
# <a name="extending-the-recyclerview-example"></a>擴充 RecyclerView 範例

[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)中所述的基本應用程式實際上並不會這麼做 &ndash; ，只要滾動並顯示固定的相片專案清單，即可方便流覽。 在真實世界的應用程式中，使用者預期能夠透過點擊顯示中的專案來與應用程式互動。 此外，基礎資料來源也可以 (變更，或由應用程式) 變更，而顯示的內容必須與這些變更保持一致。 在下列各節中，您將瞭解如何 `RecyclerView` 在基礎資料來源變更時處理專案點按事件和更新。

### <a name="handling-item-click-events"></a>處理專案 Click 事件

當使用者接觸中的專案時 `RecyclerView` ，就會產生專案按一下事件，以通知應用程式有哪些專案被觸及。 此事件不會改為產生 `RecyclerView` &ndash; ，而是在「視圖」預留位置中包裝的專案視圖 () 偵測到觸控，然後將這些觸控視為 click 事件。

為了說明如何處理專案點按事件，下列步驟說明如何修改基本的相片觀賞應用程式，以報告使用者已接觸的相片。 當範例應用程式中發生專案按一下事件時，會發生下列順序：

1. 相片會 `CardView` 偵測到專案 click 事件，並通知介面卡。

2. 介面卡會將專案位置資訊) 的事件 (轉送到活動的專案點擊處理常式。

3. 活動的專案 click 處理常式會回應專案按一下事件。

首先，會將名為的事件處理常式成員 `ItemClick` 加入至 `PhotoAlbumAdapter` 類別定義：

```csharp
public event EventHandler<int> ItemClick;
```

接下來，會將專案 click 事件處理常式方法加入至 `MainActivity` 。
這個處理常式會短暫顯示一個快顯通知，指出觸及了哪些相片專案：

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

接下來，需要一行程式碼來註冊 `OnItemClick` 處理常式 `PhotoAlbumAdapter` 。 在建立之後，就可以立即執行這項工作 `PhotoAlbumAdapter` ： 

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

在此基本範例中，處理常式註冊會在主要活動的 `OnCreate` 方法中進行，但生產環境應用程式可能會在中註冊處理常式，並在中將 `OnResume` 其取消註冊， `OnPause` &ndash; 請參閱 [活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md) 以取得詳細資訊。

`PhotoAlbumAdapter` 現在將會 `OnItemClick` 在收到專案按一下事件時呼叫。 下一步是在介面卡中建立會引發此事件的處理常式 `ItemClick` 。 下列方法 `OnClick` 會緊接在介面卡的方法之後新增 `ItemCount` ：

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

這個 `OnClick` 方法是專案查看中*listener*專案點擊事件的介面卡接聽程式。 您必須先修改此函式 `PhotoViewHolder` ，以接受此方法做為額外的引數，並向 `OnClick` 專案 view 事件註冊，才能透過專案視圖來註冊此接聽程式)  (`Click` 。
以下是修改過的函式 `PhotoViewHolder` ：

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

`itemView`參數包含使用者所觸及之的參考 `CardView` 。 請注意，view 刀柄基類知道專案的版面配置位置 (`CardView`) ，它代表 (透過 `LayoutPosition` 屬性) ，而且當專案按一下事件發生時，此位置會傳遞至介面卡的 `OnClick` 方法。 介面卡的 `OnCreateViewHolder` 方法會修改，以將介面卡的 `OnClick` 方法傳遞給查看者的函式：

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

現在，當您建立並執行範例相片觀看應用程式時，在顯示器中使用相片將會出現快顯通知，報告已觸及的相片：

[![按相片卡時出現的範例快顯](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

這個範例只會示範一個用來執行事件處理常式的方法 `RecyclerView` 。 另一種可在此使用的方法是將事件放在視圖持有者上，並讓介面卡訂閱這些事件。 如果範例相片應用程式提供了相片編輯功能，則需要個別的事件， `ImageView` 而且在 `TextView` 每個中 `CardView` ：在上執行時， `TextView` 會啟動一個 `EditView` 對話方塊讓使用者編輯標題，而在上，則會啟動可讓使用者 `ImageView` 裁剪或旋轉相片的照片 touchup 工具。 視應用程式的需求而定，您必須設計最佳的方法來處理和回應觸控事件。

為了示範如何在 `RecyclerView` 資料集變更時進行更新，您可以修改相片觀看應用程式範例，以隨機挑選資料來源中的相片，並將其與第一張相片交換。 首先，會將 **隨機挑選** 按鈕新增至範例相片應用程式的 **主要 .axml** 版面配置：

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

接著，程式碼會新增至主要活動方法的結尾， `OnCreate` 以找出配置 `Random Pick` 中的按鈕，並將處理常式附加至它：

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

`RandomSwap`當按下**隨機挑選**按鈕時，此處理程式會呼叫相片專輯的方法。 `RandomSwap`方法會使用資料來源中的第一張相片來隨機交換相片，然後傳回隨機交換之相片的索引。 當您使用此程式碼編譯及執行範例應用程式時，按 **隨機挑選** 按鈕不會導致顯示變更，因為不 `RecyclerView` 知道資料來源的變更。

若要在 `RecyclerView` 資料來源變更之後持續更新，必須修改 **隨機挑選** click 處理常式，以 `NotifyItemChanged` 針對已變更的集合中的每個專案呼叫介面卡的方法， (在此案例中，兩個專案已變更：第一個相片和交換的相片) 。 這會導致 `RecyclerView` 更新其顯示，使其與資料來源的新狀態一致：

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

現在，當您 **選擇隨機挑選** 按鈕時，會 `RecyclerView` 更新顯示畫面，以顯示集合中的相片已與集合中的第一張相片交換：

[![交換前的第一個螢幕擷取畫面，交換後的第二個螢幕擷取畫面](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

當然， `NotifyDataSetChanged` 可以呼叫，而不是對進行這兩個呼叫 `NotifyItemChanged` ，但是這樣做會強制重新整理 `RecyclerView` 整個集合，即使集合中只有兩個專案已經變更。 呼叫 `NotifyItemChanged` 的效率明顯比呼叫更有效率 `NotifyDataSetChanged` 。

## <a name="related-links"></a>相關連結

- [RecyclerViewer (範例) ](/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 元件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)