---
title: 在 Xamarin.Android 中使用 RelativeLayout
description: 如何在 Xamarin.Android 應用程式中使用 RelativeLayout
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: af2972ecc92435836a75013e6203ba47c2c04627
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113135"
---
# <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) 已[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)顯示子系 [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
相對位置中的項目。 位置[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)可以指定相對於同層級項目 （例如對於左邊的或指定的項目底下），或在將放置相對於 [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
（例如對齊到底部，左側的中心） 的區域。

A [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)是一個非常強大的公用程式，設計使用者介面，因為它可免除巢狀[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)s。 如果您發現自己使用數個巢狀結構 [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
群組，您可以將其取代為單一[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)。

開始新的專案，名為**HelloRelativeLayout**。

開啟**Resources/Layout/Main.axml**檔案，並插入下列：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="match_parent"
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

請注意每個`android:layout_*`屬性，例如`layout_below`， `layout_alignParentRight`，和`layout_toLeftOf`。
使用時[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)，您可以使用這些屬性來描述要如何放置每個[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)。 這些屬性的每個定義不同類型的相對位置。 某些屬性會使用同層級的資源識別碼[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)來定義自己的相對位置。 比方說，過去[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)欺騙左邊的和對齊與--top 定義[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)識別碼所識別`ok`(也就是先前[`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)).

中的所有可用的版面配置屬性定義[ `RelativeLayout.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)。

確定您將在此版面配置 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
方法：

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/p/System.Int32/)方法會載入的配置檔案[ `Activity`](https://developer.xamarin.com/api/type/Android.App.Activity/)資源識別碼所指定&mdash;`Resource.Layout.Main`是指**資源/配置 /Main.axml**版面配置檔。

執行應用程式。 您應該會看到下列配置：

[![相對的版面配置 TextView、 EditText，與兩個按鈕的螢幕擷取畫面](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)


## <a name="resources"></a>資源

-   [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
-   [`RelativeLayout.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
-   [`EditText`](https://developer.xamarin.com/api/type/Android.Widget.EditText/)
-   [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)


*此頁面上的部分是根據工作建立及 Android 的開放原始碼專案所共用，並依據所述的條款來使用修改*
[*Creative Commons 2.5 Attribution License*](http://creativecommons.org/licenses/by/2.5/).
