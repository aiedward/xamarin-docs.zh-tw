---
title: Xamarin. Android TableLayout
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/08/2020
ms.openlocfilehash: e33dbae13dc9b7af7649777f16bf5d23e11209ad
ms.sourcegitcommit: 124d845f8d2768353e8b7fe1ab1d959a589367f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91872260"
---
# <a name="xamarinandroid-tablelayout"></a>Xamarin. Android TableLayout

[`TableLayout`](xref:Android.Widget.TableLayout) 是 [`ViewGroup`](xref:Android.Views.ViewGroup)
顯示子系 [`View`](xref:Android.Views.View)
資料列和資料行中的元素。

開始名為 **HelloTableLayout**的新專案。

開啟 **資源/配置/content_main.xml** 檔案，然後插入下列內容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:stretchColumns="1">

    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_column="1"
            android:text="Open..."
            android:padding="3dip"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Ctrl-O"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_column="1"
            android:text="Save..."
            android:padding="3dip"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Ctrl-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_column="1"
            android:text="Save As..."
            android:padding="3dip"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Ctrl-Shift-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_width="wrap_content"
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Import..."
            android:padding="3dip"/>
    </TableRow>

    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Export..."
            android:padding="3dip"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Ctrl-E"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_width="wrap_content"
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_column="1"
            android:text="Quit"
            android:padding="3dip"/>
    </TableRow>
</TableLayout>
```

請注意這與 HTML 資料表的結構有何相似之處。 [`TableLayout`](xref:Android.Widget.TableLayout)
元素就像 HTML `<table>` 元素; [`TableRow`](xref:Android.Widget.TableRow)
就像專案一樣 `<tr>` ，但是針對儲存格，您可以使用任何種類的 [`View`](xref:Android.Views.View) 元素。 在此範例中， [`TextView`](xref:Android.Widget.TextView)
用於每個資料格。 在某些資料列之間，也有一個基本的 [`View`](xref:Android.Views.View) ，用來繪製水平線。

請確定您的 **HelloTableLayout** 活動會將此配置載入 [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
方法：

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)) 方法會載入資源識別碼所指定之的配置檔案 [`Activity`](xref:Android.App.Activity) ，它 &mdash; `Resource.Layout.Main` 是指**Resources/layout/.axml**配置檔。

執行應用程式。 您應該會看見下列內容：

[![顯示多個資料表資料列之 TableLayout 應用程式的範例螢幕擷取畫面](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)

## <a name="references"></a>參考

- [`TableLayout`](xref:Android.Widget.TableLayout)
- [`TableRow`](xref:Android.Widget.TableRow)
- [`TextView`](xref:Android.Widget.TextView)

_此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改，並根據 [創意 Commons 2.5 屬性授權](https://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。_
