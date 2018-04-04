---
title: RelativeLayout
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 51b58c02ca2768481ffd8bf0e508e94fd408f465
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) 是[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)顯示子[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)相對位置中的項目。 位置[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)可以指定相對於同層級項目 （例如對於左方的或低於指定的項目），或在放置相對於[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)區域 （例如由上至下，由的中央對齊）。

A [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)是非常強大的公用程式，設計使用者介面，因為它可以消除巢狀[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)s。 如果您發現自己使用數個巢狀[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)群組，您可以將它們取代單一[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)。

啟動新的專案，名為**HelloRelativeLayout**。

開啟**Resources/Layout/Main.axml**檔案，並插入下列：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="@android:drawable/editbox_background"
        android:layout_below="@id/label"/>
    <Button
        android:id="@+id/ok"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/entry"
        android:layout_alignParentRight="true"
        android:layout_marginLeft="10dip"
        android:text="OK" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toLeftOf="@id/ok"
        android:layout_alignTop="@id/ok"
        android:text="Cancel" />
</RelativeLayout>
```

請注意每個`android:layout_*`等屬性`layout_below`， `layout_alignParentRight`，和`layout_toLeftOf`。
當使用[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)，您可以使用這些屬性來描述如何在您想要將每個[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)。 這些屬性的每個定義不同類型的相對位置。 某些屬性會使用同層級的資源識別碼[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)來定義自己的相對位置。 比方說，最後一個[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)定義為至左的和對齊-與--頂端[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)識別碼所識別`ok`(也就是前一個[`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)).

中的所有可用的配置屬性定義[ `RelativeLayout.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)。

請確定您載入此配置中的[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)方法：

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/p/System.Int32/)方法會將載入的版面配置檔案[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)、 資源識別碼所指定&mdash;`Resource.Layout.Main`指**配置資源 / /Main.axml**配置檔案。

執行應用程式。 您應該會看到下列配置：

[![相對的版面配置 TextView、 EditText，與兩個按鈕的螢幕擷取畫面](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)


## <a name="resources"></a>資源

-   [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
-   [`RelativeLayout.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
-   [`EditText`](https://developer.xamarin.com/api/type/Android.Widget.EditText/)
-   [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)


*此頁面上的部分是修改依據工作建立及 Android 的開放原始碼專案所共用，並依據條款中所述來使用*
[*Creative Commons 2.5 Attribution 授權*](http://creativecommons.org/licenses/by/2.5/).
