---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 4b6fcfc3120c3ef09556f3466295f4bee35fad0c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029000"
---
# <a name="xamarinandroid-gridlayout"></a>Xamarin. Android GridLayout

`GridLayout` 是新的 `ViewGroup` 子類別，支援在2D 方格中配置視圖，類似于 HTML 表格，如下所示：

 [顯示四個數據格的![裁剪 GridLayout](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` 適用于一般視圖階層，其中子視圖會藉由指定應該位於其中的資料列和資料行，在方格中設定其位置。 如此一來， *GridLayout*就能夠在方格中定位視圖，而不需要任何中繼視圖都提供資料表結構，例如在 TableLayout 中使用的資料表資料列中顯示。 藉由維護平面階層， *GridLayout*可以更快速地配置其子視圖。 讓我們來看一個範例，以說明這個概念在程式碼中實際代表的意義。

## <a name="creating-a-grid-layout"></a>建立方格版面配置

下列 XML 會將數個 `TextView` 控制項加入至*GridLayout*。

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip" />
</GridLayout>
```

版面配置會調整資料列和資料行的大小，讓資料格可以符合其內容，如下圖所示：

 [版面配置的![圖，顯示左邊小於右邊的兩個儲存格](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

這會在應用程式中執行時產生下列使用者介面：

 [顯示四個數據格的 GridLayoutDemo 應用程式![螢幕擷取畫面](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)

## <a name="specifying-orientation"></a>指定方向

請注意，在上述 XML 中，每個 `TextView` 都不會指定資料列或資料行。 當未指定這些時，`GridLayout` 會根據方向，依序指派每個子視圖。 例如，讓我們將 GridLayout 的方向從預設的（水準）變更為垂直，如下所示：

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

現在，`GridLayout` 會將每個資料行中的儲存格（而不是由左至右）放在上方，如下所示：

 [說明如何以垂直方向放置儲存格的![圖表](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

這會在執行時間產生下列使用者介面：

 [![螢幕擷取畫面，其中的儲存格位於垂直方向](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)

### <a name="specifying-explicit-position"></a>指定明確位置

如果我們想要在 `GridLayout`中明確控制子視圖的位置，可以設定其 `layout_row` 和 `layout_column` 屬性。 例如，下列 XML 會導致第一個螢幕擷取畫面（如上所示）中顯示的版面配置，不論方向為何。

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="1" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="1"  />
</GridLayout>
```

### <a name="specifying-spacing"></a>指定間距

我們有幾個選項可提供 `GridLayout` 的子視圖之間的間距。 我們可以使用 `layout_margin` 屬性來直接設定每個子視圖的邊界，如下所示

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

此外，在 Android 4 中，現在可以使用新的一般用途間距視圖，稱為 `Space`。 若要使用它，請直接將它新增為子視圖。
例如，下列 XML 會將一個額外的資料列新增至 `GridLayout`，方法是將其 `rowcount` 設定為3，並加入 `Space` 視圖，以提供 `TextViews` 之間的間距。

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="3"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"         
            android:layout_height="50dp" />    
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="1" />
</GridLayout>
```

此 XML 會在 `GridLayout` 中建立間距，如下所示：

 [GridLayoutDemo 的螢幕擷取畫面，其中會以間距說明較大的資料格![](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

使用新的 `Space` view 的優點是，它允許間距，而且不需要我們在每個子視圖上設定屬性。

### <a name="spanning-columns-and-rows"></a>跨越資料行和資料列

`GridLayout` 也支援跨越多個資料行和資料列的資料格。 例如，假設我們將包含按鈕的另一個資料列新增至 `GridLayout`，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="4"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"        
            android:layout_height="50dp" />   
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"        
            android:layout_row="2"        
            android:layout_column="1" />
     <Button
            android:id="@+id/myButton"
            android:text="@string/hello"        
            android:layout_row="3"
            android:layout_column="0" />
</GridLayout>
```

這會導致 `GridLayout` 的第一個資料行伸展，以容納按鈕的大小，如以下所示：

[![螢幕擷取畫面，其中僅包含第一個資料行的按鈕 GridLayoutDemo](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

若要保留第一個資料行的延展，我們可以設定其 columnspan，使其橫跨兩個數據行，如下所示：

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

這麼做會產生 `TextViews` 的配置，類似于我們先前的配置，並將按鈕新增至 `GridLayout` 底部，如下所示：

 [![螢幕擷取畫面，其中包含兩個數據行的按鈕 GridLayoutDemo](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)

## <a name="related-links"></a>相關連結

- [GridLayoutDemo （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/gridlayoutdemo)
- [霜淇淋三明治簡介](https://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平臺](https://developer.android.com/sdk/android-4.0.html)
