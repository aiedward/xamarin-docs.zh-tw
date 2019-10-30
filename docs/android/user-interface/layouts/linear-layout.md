---
title: Xamarin. Android LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/07/2018
ms.openlocfilehash: b1cff01c66ae2581a68286e62bd8c8c5fb7f9d72
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028950"
---
# <a name="xamarinandroid-linearlayout"></a>Xamarin. Android LinearLayout

[`LinearLayout`](xref:Android.Widget.LinearLayout)是一個[`ViewGroup`](xref:Android.Views.ViewGroup)
，顯示子[`View`](xref:Android.Views.View)
線性方向的元素（垂直或水準）。

您應該謹慎使用[`LinearLayout`](xref:Android.Widget.LinearLayout)。
如果您開始嵌套多個[`LinearLayout`](xref:Android.Widget.LinearLayout)，您可能會想要考慮使用[`RelativeLayout`](xref:Android.Widget.RelativeLayout)
只.

啟動名為**HelloLinearLayout**的新專案。

開啟**Resources/Layout/axml** ，並插入下列內容：

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

請仔細檢查此 XML。 有根[`LinearLayout`](xref:Android.Widget.LinearLayout)
將其方向定義為垂直 &ndash; 所有子[`View`](xref:Android.Views.View)s （其中有兩個）會垂直堆疊。 第一個子系是另一個[`LinearLayout`](xref:Android.Widget.LinearLayout)
這會使用水準方向，而第二個子系是[`LinearLayout`](xref:Android.Widget.LinearLayout)
，其使用垂直方向。 這些嵌套[`LinearLayout`](xref:Android.Widget.LinearLayout)中的每一個都包含數個[`TextView`](xref:Android.Widget.TextView)
元素，以其父系[`LinearLayout`](xref:Android.Widget.LinearLayout)所定義的方式彼此導向。

現在開啟**HelloLinearLayout.cs** ，並確定它會在[`OnCreate()`](xref:Android.App.Activity.OnCreate*)中載入**Resources/Layout/axml**版面配置。
方法

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)）方法會載入[`Activity`](xref:Android.App.Activity)的配置檔案，該檔案是由資源識別碼所指定 &ndash; `Resources.Layout.Main` 會參考**Resources/layout/axml**設定檔案。

執行應用程式。 您應該會看到下列內容：

[![應用程式的第一個 LinearLayout 螢幕擷取畫面，以水準方式排列，第二垂直](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

請注意 XML 屬性如何定義每個視圖的行為。 請嘗試使用不同的 `android:layout_weight` 值進行實驗，以查看如何根據每個元素的權數來散發螢幕房地產。 如需如何[`LinearLayout`](xref:Android.Widget.LinearLayout)的詳細資訊，請參閱[常見的版面設定物件](https://developer.android.com/guide/topics/ui/declaring-layout.html)檔
處理 `android:layout_weight` 屬性。

## <a name="references"></a>reference

- [`LinearLayout`](xref:Android.Widget.LinearLayout)
- [`TextView`](xref:Android.Widget.TextView)

_此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改，並根據[創意 Commons 2.5 屬性授權](https://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。_
