---
title: 在 Xamarin 中使用 RelativeLayout
description: 如何在 Xamarin Android 應用程式中使用 RelativeLayout
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: a53baed9d5f291628d7d1a8da05739e43412d473
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509721"
---
# <a name="xamarinandroid-relativelayout"></a>Xamarin. Android RelativeLayout

[`RelativeLayout`](xref:Android.Widget.RelativeLayout)是顯示子系的。 [`ViewGroup`](xref:Android.Views.ViewGroup)[`View`](xref:Android.Views.View)
相對位置中的元素。 的位置[`View`](xref:Android.Views.View)可以指定為相對於兄弟元素 (例如, 到指定專案的左邊或下方), 或相對於的位置。[`RelativeLayout`](xref:Android.Widget.RelativeLayout)
區域 (例如對齊底端、中央的左邊)。

是一個非常強大的公用程式, 可用於設計使用者介面, 因為它[`ViewGroup`](xref:Android.Views.ViewGroup)可以排除嵌套的。 [`RelativeLayout`](xref:Android.Widget.RelativeLayout) 如果您發現自己使用數個嵌套的[`LinearLayout`](xref:Android.Widget.LinearLayout)
群組, 您可以使用單一[`RelativeLayout`](xref:Android.Widget.RelativeLayout)來取代它們。

啟動名為**HelloRelativeLayout**的新專案。

開啟**Resources/Layout/axml**檔案, 並插入下列內容:

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

請注意每個`android:layout_*`屬性, `layout_below`例如、 `layout_alignParentRight`和`layout_toLeftOf`。
使用[`RelativeLayout`](xref:Android.Widget.RelativeLayout)時, 您可以使用這些屬性來描述您要如何定位每個[`View`](xref:Android.Views.View)。 這些屬性中的每一個都定義了不同種類的相對位置。 某些屬性會使用兄弟[`View`](xref:Android.Views.View)的資源識別碼來定義自己的相對位置。 例如, [`Button`](xref:Android.Widget.Button)最後一個是定義為, 它是由識別碼`ok`所識別的[`View`](xref:Android.Views.View) (其為上一個[`Button`](xref:Android.Widget.Button)) 所識別的左邊和靠上對齊。

所有可用的版面配置屬性都定義于[`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)中。

請確定您已在中載入此版面配置[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
方法

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[`Activity`](xref:Android.App.Activity) &mdash; `Resource.Layout.Main` 方法會載入設定檔案, 而資源識別碼所指定的則是指 Resources/layout/axml 設定檔案。 [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)

執行應用程式。 您應該會看到下列版面配置:

[![具有 TextView、EditText 和兩個按鈕的相對版面配置螢幕擷取畫面](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)

## <a name="resources"></a>資源

- [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
- [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)
- [`TextView`](xref:Android.Widget.TextView)
- [`EditText`](xref:Android.Widget.EditText)
- [`Button`](xref:Android.Widget.Button)

*此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改, 並根據*
[*創意 Commons 2.5 屬性授權*](http://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。
