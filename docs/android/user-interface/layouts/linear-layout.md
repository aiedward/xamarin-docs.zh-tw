---
title: LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/07/2018
ms.openlocfilehash: f3d0394f6b2388918f728bd5a25e9e809a832ca6
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670970"
---
# <a name="linearlayout"></a>LinearLayout

[`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 是 [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
顯示子系 [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
以線性的方向的項目水平或垂直。

您應謹慎過度使用[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)。
如果您開始巢狀多重[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s，您可能要考慮使用 [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
改為。

開始新的專案，名為**HelloLinearLayout**。

開啟**Resources/Layout/Main.axml**並插入下列：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"    >

  <LinearLayout
      android:orientation=    "horizontal"
      android:layout_width=    "match_parent"
      android:layout_height=    "match_parent"
      android:layout_weight=    "1"    >
      <TextView
          android:text=    "red"
          android:gravity=    "center_horizontal"
          android:background=    "#aa0000"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "green"
          android:gravity=    "center_horizontal"
          android:background=    "#00aa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "blue"
          android:gravity=    "center_horizontal"
          android:background=    "#0000aa"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "yellow"
          android:gravity=    "center_horizontal"
          android:background=    "#aaaa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
  </LinearLayout>
        
  <LinearLayout
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"
    android:layout_weight=    "1"    >
    <TextView
        android:text=    "row one"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row two"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row three"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row four"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
       android:layout_weight=    "1"    />
  </LinearLayout>

</LinearLayout>
```

請仔細檢查這段 XML。 沒有根 [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
定義其方向是垂直&ndash;所有的子系[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)s （它的有兩個） 以垂直方式會是堆疊。 第一個子系是另一個 [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
使用水平方向和第二個的子系 [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
使用垂直方向。 每一種巢狀[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s 包含數個 [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
項目，為其父代所定義的方式彼此導向[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)。

現在，請開啟**HelloLinearLayout.cs** ，並確定它會載入**Resources/Layout/Main.axml**中的版面配置 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
方法：

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32))方法會載入的配置檔案[ `Activity`](https://developer.xamarin.com/api/type/Android.App.Activity/)資源識別碼所指定&ndash;`Resources.Layout.Main`是指**資源/配置 /Main.axml**版面配置檔。

執行應用程式。 您應該會看到下列：

[![螢幕擷取畫面的第一個 LinearLayout 水平排列應用程式，第二個垂直](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

請注意，XML 屬性定義每個檢視的行為的方式。 嘗試實驗不同的值，如`android:layout_weight`若要查看如何分散螢幕面積根據每個項目的權數。 請參閱[常見的配置物件](https://developer.android.com/guide/topics/ui/declaring-layout.html)文件，如需詳細資訊的方式 [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
控制代碼`android:layout_weight`屬性。


## <a name="references"></a>參考

-   [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*此頁面上的部分是根據工作建立及 Android 的開放原始碼專案所共用，並依據所述的條款來使用修改*
[*Creative Commons 2.5 Attribution License*](http://creativecommons.org/licenses/by/2.5/).

