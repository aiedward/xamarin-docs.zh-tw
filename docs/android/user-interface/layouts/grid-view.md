---
title: GridView
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: a1bcb83d6057cb7d4a43c510d7b5805b574812e6
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510055"
---
# <a name="xamarinandroid-gridview"></a>Xamarin. Android GridView

[`GridView`](xref:Android.Widget.GridView)是[`ViewGroup`](xref:Android.Views.ViewGroup)
以二維、可滾動方格顯示專案。 格線專案會使用[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)自動插入至版面配置。

在本教學課程中, 您將建立影像縮圖的方格。 選取專案時, 快顯訊息會顯示影像的位置。

啟動名為**HelloGridView**的新專案。

尋找您想要使用的一些相片, 或[下載這些範例影像](https://developer.android.com/shareables/sample_images.zip)。 將影像檔案新增至專案的**資源/可繪製**目錄。 在 [**屬性**] 視窗中, 將每個的 [建立] 動作設定為 [ **AndroidResource**]。

開啟**Resources/Layout/axml**檔案, 並插入下列內容:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gridview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:columnWidth="90dp"
    android:numColumns="auto_fit"
    android:verticalSpacing="10dp"
    android:horizontalSpacing="10dp"
    android:stretchMode="columnWidth"
    android:gravity="center"
/>
```

這[`GridView`](xref:Android.Widget.GridView)會填滿整個畫面。 屬性很容易理解。 如需有效屬性的詳細資訊, 請[`GridView`](xref:Android.Widget.GridView)參閱參考。

開啟`HelloGridView.cs`並插入下列程式碼[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
方法

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    var gridview = FindViewById<GridView> (Resource.Id.gridview);
    gridview.Adapter = new ImageAdapter (this);

    gridview.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

設定內容視圖的**axml**版面配置之後, [`GridView`](xref:Android.Widget.GridView)會從配置中使用[`FindViewById`](xref:Android.App.Activity.FindViewById*)來捕捉。 該[`Adapter`](xref:Android.Widget.AdapterView.RawAdapter)
接著, 屬性會用來設定自訂適配`ImageAdapter`卡 () 做為要在方格中顯示之所有專案的來源。 在下一個步驟中會建立。`ImageAdapter`

若要在按一下方格中的專案時執行某些動作, 會訂閱[`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)事件的匿名委派。
它會顯示[`Toast`](xref:Android.Widget.Toast) , 其中顯示所選取專案的索引位置 (以零為基底) (在真實世界的案例中, 位置可用來取得其他工作的完整大小影像)。 請注意, 您可以使用 JAVA 樣式接聽程式類別, 而不是 .NET 事件。

建立名`ImageAdapter`為子類別[`BaseAdapter`](xref:Android.Widget.BaseAdapter)的新類別:

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
        context = c;
    }

    public override int Count {
        get { return thumbIds.Length; }
    }

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
        ImageView imageView;

        if (convertView == null) {  // if it's not recycled, initialize some attributes
            imageView = new ImageView (context);
            imageView.LayoutParameters = new GridView.LayoutParams (85, 85);
            imageView.SetScaleType (ImageView.ScaleType.CenterCrop);
            imageView.SetPadding (8, 8, 8, 8);
        } else {
            imageView = (ImageView)convertView;
        }

        imageView.SetImageResource (thumbIds[position]);
        return imageView;
    }

    // references to our images
    int[] thumbIds = {
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7
    };
}
```

首先, 這會執行一些繼承自的[`BaseAdapter`](xref:Android.Widget.BaseAdapter)必要方法。 此函式和[`Count`](xref:Android.Widget.BaseAdapter.Count)屬性一目了然。 平常[`GetItem(int)`](xref:Android.Widget.BaseAdapter.GetItem*)
應該會傳回介面卡中指定位置的實際物件, 但此範例會忽略它。 同樣地[`GetItemId(int)`](xref:Android.Widget.BaseAdapter.GetItemId*)
應該會傳回專案的資料列識別碼, 但此處不需要。

第一個必要的方法[`GetView()`](xref:Android.Widget.BaseAdapter.GetView*)是。
這個方法會建立新的[`View`](xref:Android.Views.View)
針對加入至的`ImageAdapter`每個影像。 當這個呼叫時,[`View`](xref:Android.Views.View)
傳入, 這通常是回收的物件 (至少呼叫過這次之後), 因此會檢查物件是否為 null。 如果它*是*null, 則為[`ImageView`](xref:Android.Widget.ImageView)
會具現化並設定影像呈現所需的屬性:

- [`LayoutParams`](xref:Android.Views.View.LayoutParameters)設定視圖&mdash;的高度和寬度, 這可確保不論可以繪製的大小為何, 每個影像都會調整大小並裁剪以符合這些尺寸 (視情況而調整)。

- [`SetScaleType()`](xref:Android.Widget.ImageView.SetScaleType*)宣告影像應裁剪至中心 (如有必要)。

- [`SetPadding(int, int, int, int)`](xref:Android.Views.View.SetPadding*)定義所有邊的填補。 (請注意, 如果影像的外觀比例不同, 則如果影像不符合提供給 ImageView 的維度, 則較少的填補會造成更多的影像裁剪)。

如果傳遞[`View`](xref:Android.Views.View)至[`GetView()`](xref:Android.Widget.BaseAdapter.GetView*)的*不*是 null, 則為本機[`ImageView`](xref:Android.Widget.ImageView)
會使用回收[`View`](xref:Android.Views.View)的物件進行初始化。

結束于[`GetView()`](xref:Android.Widget.BaseAdapter.GetView*)
方法, 傳遞`position`至方法的整數會用來`thumbIds`從陣列中選取影像, 這會設定為的影像資源[`ImageView`](xref:Android.Widget.ImageView)。

剩下的就是定義`thumbIds`可繪製資源的陣列。

執行應用程式。 您的方格版面配置看起來應該像這樣:

[![GridView 顯示15個影像的範例螢幕擷取畫面](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

嘗試使用[`GridView`](xref:Android.Widget.GridView)和的行為進行實驗[`ImageView`](xref:Android.Widget.ImageView)
藉由調整其屬性來進行元素。 例如, 而不是使用[`LayoutParams`](xref:Android.Views.View.LayoutParameters) [嘗試[`SetAdjustViewBounds()`](xref:Android.Widget.ImageView.SetAdjustViewBounds*)使用]。

## <a name="references"></a>參考

- [`GridView`](xref:Android.Widget.GridView)
- [`ImageView`](xref:Android.Widget.ImageView)
- [`BaseAdapter`](xref:Android.Widget.BaseAdapter)

*此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改, 並根據*
[*創意 Commons 2.5 屬性授權*](http://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。
