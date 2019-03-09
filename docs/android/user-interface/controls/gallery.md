---
title: Gallery
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: f9b73428531deeacc7bdea271cdc0c2872038e99
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666955"
---
# <a name="gallery"></a>Gallery

[`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/) 是用來水平捲動的清單中顯示的項目配置 widget，並將目前的選取範圍定位在檢視的中央。

> [!IMPORTANT]
> Android 4.1 （API 層級 16） 中，此 widget 已被取代。 

在本教學課程中，您將建立的相片圖庫，然後選取資源庫項目每次顯示快顯通知訊息。

在後`Main.axml`版面配置 設定的內容檢視中，`Gallery`擷取自與版面配置[ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/)。
的 [`Adapter`](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/)
屬性則用來設定自訂的配接器 ( `ImageAdapter`) 做為來源，會顯示在 dallery 的所有項目。 `ImageAdapter`會在下一個步驟中建立。

按一下資源庫中的項目時，請執行一些作業，訂閱匿名委派 [`ItemClick`](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)
事件的引數。 它會顯示 [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
顯示 theselected 項目 （以零為起始） 的索引位置 （在真實世界案例中，位置無法用來取得其他某項工作的完整大小的影像）。

首先，有幾個成員變數，包括參考可繪製資源目錄中儲存的映像的識別碼陣列 (**資源/drawable**)。

接下來是類別建構函式，其中 [`Context`](https://developer.xamarin.com/api/type/Android.Content.Context/)
針對`ImageAdapter`定義執行個體並將其儲存至本機欄位。
接下來，這會實作一些必要的方法繼承自[ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)。
建構函式， [`Count`](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/)
屬性都簡單易懂。 一般來說， [`GetItem(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/p/System.Int32/)
會傳回實際的物件，在配接器中，指定的位置，但此範例中會忽略它。 同樣地， [`GetItemId(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/p/System.Int32/)
應該會傳回資料列識別碼的項目，但是這裡不需要。

方法會套用至映像的工作 [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
將內嵌在 [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
在此方法中，成員 [`Context`](https://developer.xamarin.com/api/type/Android.Content.Context/)
用來建立新[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)。
的 [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
準備好藉由套用映像從本機可繪製資源，設定的陣列 [`Gallery.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.Gallery+LayoutParams/)
高度和寬度的映像中，然後再設定調整以符合 [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
維度和最後設定使用建構函式中取得的可設定樣式屬性的背景。

請參閱[ `ImageView.ScaleType` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView+ScaleType/)調整選項的其他映像。

## <a name="walkthrough"></a>逐步解說

開始新的專案，名為*HelloGallery*。

[![新的 Android 專案中新增解決方案 對話方塊的螢幕擷取畫面](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

尋找您想要使用時，部分相片或[下載這些範例映像](https://developer.android.com/shareables/sample_images.zip)。
將影像檔新增至專案的**資源/Drawable**目錄。 在 **屬性**視窗中，將 建置動作各自**AndroidResource**。

開啟**Resources/Layout/Main.axml**並插入下列：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

開啟`MainActivity.cs`並插入下列程式碼 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    Gallery gallery = (Gallery) FindViewById<Gallery>(Resource.Id.gallery);

    gallery.Adapter = new ImageAdapter (this);

    gallery.ItemClick += delegate (object sender, Android.Widget.AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

建立新的類別，稱為`ImageAdapter`子類別化[ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
          context = c;
    }

    public override int Count { get { return thumbIds.Length; } }

    public override Java.Lang.Object GetItem (int position)
    {
          return null;
    }

    public override long GetItemId (int position)
    {
          return 0;
    }

    // create a new ImageView for each item referenced by the Adapter
    public override View GetView (int position, View convertView, ViewGroup parent)
    {
          ImageView i = new ImageView (context);

          i.SetImageResource (thumbIds[position]);
          i.LayoutParameters = new Gallery.LayoutParams (150, 100);
          i.SetScaleType (ImageView.ScaleType.FitXy);

          return i;
    }

    // references to our images
    int[] thumbIds = {
            Resource.Drawable.sample_1,
            Resource.Drawable.sample_2,
            Resource.Drawable.sample_3,
            Resource.Drawable.sample_4,
            Resource.Drawable.sample_5,
            Resource.Drawable.sample_6,
            Resource.Drawable.sample_7
     };
}

```

執行應用程式。 它看起來應該像下列螢幕擷取畫面：

![螢幕擷取畫面的 HelloGallery，顯示範例映像](gallery-images/hellogallery3.png)



## <a name="references"></a>參考

-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
-   [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)

*此頁面上的部分是根據工作建立及 Android 的開放原始碼專案所共用，並依據所述的條款來使用修改*
[*Creative Commons 2.5 Attribution License*](http://creativecommons.org/licenses/by/2.5/).


