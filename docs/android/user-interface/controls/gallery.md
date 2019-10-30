---
title: Android 資源庫控制項
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 93eb7f98da6f3fe06f288eae5823f7173e58585f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029236"
---
# <a name="xamarinandroid-gallery-control"></a>Xamarin. Android 元件庫控制項

[`Gallery`](xref:Android.Widget.Gallery)是一個版面配置小工具，可用來在水準滾動清單中顯示專案，並將目前的選取範圍置於視圖的中央。

> [!IMPORTANT]
> 此 widget 在 Android 4.1 （API 層級16）中已被取代。 

在本教學課程中，您將建立相片圖庫，並在每次選取主機庫專案時顯示快顯通知訊息。

設定內容視圖的 `Main.axml` 配置之後，會從配置中使用[`FindViewById`](xref:Android.App.Activity.FindViewById*)來捕捉 `Gallery`。
[`Adapter`](xref:Android.Widget.AdapterView.RawAdapter)
接著，屬性會用來設定自訂介面卡（`ImageAdapter`），做為要在 dallery 中顯示之所有專案的來源。 `ImageAdapter` 會在下一個步驟中建立。

若要在按一下圖庫中的專案時執行某些動作，匿名委派會訂閱[`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
事件的引數。 它會顯示[`Toast`](xref:Android.Widget.Toast)
這會顯示 theselected 專案的索引位置（以零為基底）（在真實世界的案例中，位置可用來取得其他工作的完整大小影像）。

首先，有幾個成員變數，包括參考儲存在可繪製資原始目錄（**資源/可繪製**）中之影像的識別碼陣列。

[下一步] 是類別的 [函式]，其中[`Context`](xref:Android.Content.Context)
會定義 `ImageAdapter` 實例，並將其儲存至本機欄位。
接下來，這會執行繼承自[`BaseAdapter`](xref:Android.Widget.BaseAdapter)的一些必要方法。
函式和[`Count`](xref:Android.Widget.BaseAdapter.Count)
屬性一目了然。 一般來說， [`GetItem(int)`](xref:Android.Widget.BaseAdapter.GetItem*)
應該會傳回介面卡中指定位置的實際物件，但此範例會忽略它。 同樣地， [`GetItemId(int)`](xref:Android.Widget.BaseAdapter.GetItemId*)
應該會傳回專案的資料列識別碼，但此處不需要。

方法會執行工作，以將影像套用至[`ImageView`](xref:Android.Widget.ImageView)
將內嵌在[`Gallery`](xref:Android.Widget.Gallery)
在這個方法中，成員[`Context`](xref:Android.Content.Context)
是用來建立新的[`ImageView`](xref:Android.Widget.ImageView)。
[`ImageView`](xref:Android.Widget.ImageView)
會藉由從可繪製資源的本機陣列套用影像來準備，設定[`Gallery.LayoutParams`](xref:Android.Widget.Gallery.LayoutParams)
影像的高度和寬度，設定尺規以符合[`ImageView`](xref:Android.Widget.ImageView)
維度，最後將背景設定為使用在此函式中取得的 styleable 屬性。

如需其他影像縮放選項，請參閱[`ImageView.ScaleType`](xref:Android.Widget.ImageView.ScaleType) 。

## <a name="walkthrough"></a>逐步解說

啟動名為*HelloGallery*的新專案。

[在 [新增方案] 對話方塊中![新 Android 專案的螢幕擷取畫面](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

尋找您想要使用的一些相片，或[下載這些範例影像](https://developer.android.com/shareables/sample_images.zip)。
將影像檔案新增至專案的**資源/可繪製**目錄。 在 [**屬性**] 視窗中，將每個的 [建立] 動作設定為 [ **AndroidResource**]。

開啟**Resources/Layout/axml** ，並插入下列內容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

開啟 `MainActivity.cs`，並為[`OnCreate()`](xref:Android.App.Activity.OnCreate*)插入下列程式碼
方法

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

建立名為 `ImageAdapter` 子類別[`BaseAdapter`](xref:Android.Widget.BaseAdapter)的新類別：

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

執行應用程式。 看起來應該類似下列螢幕擷取畫面：

![顯示範例影像之 HelloGallery 的螢幕擷取畫面](gallery-images/hellogallery3.png)

## <a name="references"></a>reference

- [`BaseAdapter`](xref:Android.Widget.BaseAdapter)
- [`Gallery`](xref:Android.Widget.Gallery)
- [`ImageView`](xref:Android.Widget.ImageView)

_此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改，並根據[創意 Commons 2.5 屬性授權](https://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。_
