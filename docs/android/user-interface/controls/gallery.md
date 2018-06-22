---
title: 組件庫
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/15/2018
ms.openlocfilehash: cb05dfb79c00e9c6f9fa1a8d80627abdb911c36e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765645"
---
# <a name="gallery"></a>組件庫

[`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/) 是用來水平捲動清單來顯示項目配置 widget，並將目前的選取範圍放在檢視的中心。

> [!IMPORTANT]
> 這個小工具已被取代，於 Android 4.1 (API level 16)。 

在本教學課程中，將建立的相片圖庫，並再選取主機庫項目每次顯示快顯通知訊息。

之後`Main.axml`版面配置 設定的內容檢視中，`Gallery`擷取自版面配置與[ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/)。
[ `Adapter` ](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/)屬性然後用來設定自訂的配接器 ( `ImageAdapter`) 做為來源，會顯示在 dallery 的所有項目。 `ImageAdapter`會在下一個步驟中建立。

若要在按下組件庫中的項目時，請執行一些動作，在匿名委派訂閱[ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)事件。 它會顯示[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)顯示 theselected 項目 （以零為起始） 的索引位置 （在真實世界案例中，位置無法用來取得完整大小的映像的其他工作）。

首先，有一些成員變數，包括參考 drawable 資源目錄中儲存的映像的識別碼陣列 (**資源/drawable**)。

接下來是類別建構函式，其中[ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/)如`ImageAdapter`定義及儲存到本機的欄位執行個體。
接下來，這會實作一些需要的方法繼承自[ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)。
建構函式和[ `Count` ](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/)屬性都一目了然。 一般來說， [ `GetItem(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/p/System.Int32/)應該會傳回實際物件的介面卡，指定的位置，但此範例中會忽略它。 同樣地， [ `GetItemId(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/p/System.Int32/)應該會傳回資料列識別碼的項目，但這裡不需要。

方法會套用至映像工作[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)會內嵌在[ `Gallery` ](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)在此方法中，成員[ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/)是用來建立新[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)。
[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)做好套用映像從 drawable 資源，設定本機陣列[ `Gallery.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.Gallery+LayoutParams/)高度和寬度映像，並設定小數位數以配合[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)維度和最後設定背景使用 styleable 取得建構函式中的屬性。

請參閱[ `ImageView.ScaleType` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView+ScaleType/)其他擴充選項的映像。

## <a name="walkthrough"></a>逐步解說

啟動新的專案，名為*HelloGallery*。

[![新的 Android 專案中新方案對話方塊的螢幕擷取畫面](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

尋找您想要使用時，某些相片或[下載這些範例影像](http://developer.android.com/shareables/sample_images.zip)。
將影像檔加入至專案的**資源/Drawable**目錄。 在**屬性**視窗中，針對每個要設定建置動作**AndroidResource**。

開啟**Resources/Layout/Main.axml**並插入下列：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

開啟`MainActivity.cs`插入下列程式碼和[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)方法：

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

建立新的類別稱為`ImageAdapter`該子類別[ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

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

![螢幕擷取畫面的 HelloGallery，顯示範例影像](gallery-images/hellogallery3.png)



## <a name="references"></a>參考

-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
-   [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)

*此頁面上的部分是修改依據工作建立及 Android 的開放原始碼專案所共用，並依據條款中所述來使用*
[*Creative Commons 2.5 Attribution 授權*](http://creativecommons.org/licenses/by/2.5/).


