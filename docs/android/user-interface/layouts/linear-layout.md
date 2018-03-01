---
title: LinearLayout
ms.topic: article
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 3cc5db39280c72f0de9dbdae07a49b56416c90a5
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="linearlayout"></a>LinearLayout

[`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 是[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)顯示子[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)線性方向的項目水平或垂直。

您應謹慎過度使用[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)。
如果您一開始是巢狀多重[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s，您可能要考慮使用[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)改為。

啟動新的專案，名為**HelloLinearLayout**。

開啟**Resources/Layout/Main.axml**並插入下列：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation=    "vertical"
    android:layout_width=    "fill_parent"
    android:layout_height=    "fill_parent"    >

  <LinearLayout
      android:orientation=    "horizontal"
      android:layout_width=    "fill_parent"
      android:layout_height=    "fill_parent"
      android:layout_weight=    "1"    >
      <TextView
          android:text=    "red"
          android:gravity=    "center_horizontal"
          android:background=    "#aa0000"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "green"
          android:gravity=    "center_horizontal"
          android:background=    "#00aa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "blue"
          android:gravity=    "center_horizontal"
          android:background=    "#0000aa"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "yellow"
          android:gravity=    "center_horizontal"
          android:background=    "#aaaa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
  </LinearLayout>
        
  <LinearLayout
    android:orientation=    "vertical"
    android:layout_width=    "fill_parent"
    android:layout_height=    "fill_parent"
    android:layout_weight=    "1"    >
    <TextView
        android:text=    "row one"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row two"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row three"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row four"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
       android:layout_weight=    "1"    />
  </LinearLayout>

</LinearLayout>
```

請仔細檢查此 XML。 沒有根[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) ，定義其是垂直方向&ndash;所有子[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)s （它的有兩個） 以垂直方式將會是堆疊。 第一個子系是另一個[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)使用水平方向的而且第二個子[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)使用垂直方向。 每個巢狀[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s 包含數個[ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/)項目，為其父代所定義的方式彼此導向[ `LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).

現在開啟**HelloLinearLayout.cs** ，並確定它會載入**Resources/Layout/Main.axml**中的配置[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)方法：

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32))方法會將載入的版面配置檔案[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)、 資源識別碼所指定&ndash;`Resources.Layout.Main`指**配置資源 / /Main.axml**配置檔案。

執行應用程式。 您應該會看到下列：

[ ![螢幕擷取畫面的第一個線性水平排列的應用程式，第二個垂直](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png)

請注意，XML 屬性如何定義每個檢視的行為。 嘗試使用不同的值`android:layout_weight`以查看實際螢幕面積散發方式根據每個項目的權數。 請參閱[一般的版面配置物件](http://developer.android.com/guide/topics/ui/declaring-layout.html)文件，如需有關如何[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)控點`android:layout_weight`屬性。

<a name="References" />

## <a name="references"></a>參考

-   [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*此頁面上的部分是修改依據工作建立及 Android 的開放原始碼專案所共用，並依據條款中所述來使用*
[*Creative Commons 2.5 Attribution 授權*](http://creativecommons.org/licenses/by/2.5/).

