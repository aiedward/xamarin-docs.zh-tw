---
title: Xamarin. Android TableLayout
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 4175b1fa62b2bc0e4209d13934c2bdbdd1e2a085
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028741"
---
# <a name="xamarinandroid-tablelayout"></a>Xamarin. Android TableLayout

[`TableLayout`](xref:Android.Widget.TableLayout)是一個[`ViewGroup`](xref:Android.Views.ViewGroup)
，顯示子[`View`](xref:Android.Views.View)
資料列和資料行中的元素。

啟動名為**HelloTableLayout**的新專案。

開啟**Resources/Layout/axml**檔案，並插入下列內容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:stretchColumns="1">

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Open..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-O"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save As..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-Shift-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Import..."
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Export..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-E"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Quit"
            android:padding="3dip"/>
    </TableRow>
</TableLayout>
```

請注意，這類似于 HTML 資料表的結構。 [`TableLayout`](xref:Android.Widget.TableLayout)
元素就像 HTML `<table>` 元素一樣。[`TableRow`](xref:Android.Widget.TableRow)
就像是 `<tr>` 元素;但是針對資料格，您可以使用任何種類的[`View`](xref:Android.Views.View)元素。 在此範例中， [`TextView`](xref:Android.Widget.TextView)
用於每個資料格。 在某些資料列之間，還有一個基本[`View`](xref:Android.Views.View)，用來繪製水平線。

請確定您的**HelloTableLayout**活動會在[`OnCreate()`](xref:Android.App.Activity.OnCreate*)中載入此版面配置
方法

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)）方法會載入[`Activity`](xref:Android.App.Activity)的配置檔案，該檔案是由資源識別碼所指定 &mdash; `Resource.Layout.Main` 會參考**Resources/layout/axml**設定檔案。

執行應用程式。 您應該會看到下列內容：

[顯示多個資料表資料列之 TableLayout 應用程式的![範例螢幕擷取畫面](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)

## <a name="references"></a>reference

- [`TableLayout`](xref:Android.Widget.TableLayout)
- [`TableRow`](xref:Android.Widget.TableRow)
- [`TextView`](xref:Android.Widget.TextView)

_此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改，並根據[創意 Commons 2.5 屬性授權](https://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。_
