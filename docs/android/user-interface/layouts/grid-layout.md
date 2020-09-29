---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 3b2b6a3f1aa575553964e71be3a64cec16bc616f
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457324"
---
# <a name="xamarinandroid-gridlayout"></a>Xamarin. Android GridLayout

`GridLayout`是新的子 `ViewGroup` 類別，支援在2d 方格中配置視圖，類似于 HTML 表格，如下所示：

 [![裁剪的 GridLayout 顯示四個數據格](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` 適用于平面視圖階層，其中的子視圖會藉由指定應該位於格線的資料列和資料行，在方格中設定其位置。 如此一來， *GridLayout* 就能夠在方格中放置視圖，而不需要任何中繼視圖都提供資料表結構，例如在 TableLayout 中使用的資料表資料列中所見。 藉由維護平面階層， *GridLayout* 能夠更快速地配置其子視圖。 讓我們看看一個範例，以說明此概念在程式碼中的實際意義。

## <a name="creating-a-grid-layout"></a>建立格線版面配置

下列 XML 會將數個 `TextView` 控制項新增至 *GridLayout*。

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

版面配置將會調整資料列和資料行的大小，讓儲存格可以容納其內容，如下圖所示：

 [![版面配置圖，顯示左邊的兩個數據格小於右邊](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

在應用程式中執行時，會產生下列使用者介面：

 [![顯示四個儲存格的 GridLayoutDemo 應用程式螢幕擷取畫面](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)

## <a name="specifying-orientation"></a>指定方向

請注意，在上述 XML 中，每個都不 `TextView` 會指定一個資料列或資料行。 如果未指定這些，則會 `GridLayout` 根據方向指派每個子視圖的順序。 例如，讓我們將 GridLayout 的方向從預設的水準變更為垂直，如下所示：

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

現在， `GridLayout` 會將每個資料行中的資料格從上到下，而不是由左至右放置，如下所示：

 [![說明儲存格如何以垂直方向定位的圖表](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

這會在執行時間產生下列使用者介面：

 [![以垂直方向定位資料格的 GridLayoutDemo 螢幕擷取畫面](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)

### <a name="specifying-explicit-position"></a>指定明確位置

如果我們想要明確地控制中子視圖的位置 `GridLayout` ，我們可以設定其 `layout_row` 和 `layout_column` 屬性。 例如，下列 XML 將會導致顯示于上述第一個螢幕擷取畫面中的版面配置 () ，不論方向為何。

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

有幾個選項可以提供的子視圖之間的間距 `GridLayout` 。 我們可以使用 `layout_margin` 屬性直接設定每個子視圖的邊界，如下所示

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

此外，在 Android 4 中， `Space` 現在已可使用新的一般用途間距查看。 若要使用它，只要將它新增為子視圖即可。
例如，下列 XML 會將另一個資料列加入至， `GridLayout` 方法是將其設定 `rowcount` 為3，然後加入 `Space` 提供之間間距的視圖 `TextViews` 。

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

這個 XML 會在中建立間距， `GridLayout` 如下所示：

 [![GridLayoutDemo 的螢幕擷取畫面，說明具有間距的大型儲存格](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

使用新視圖的優點 `Space` 是它允許間距，而且不需要我們在每個子視圖上設定屬性。

### <a name="spanning-columns-and-rows"></a>跨越資料行和資料列

`GridLayout`也支援跨越多個資料行和資料列的資料格。 例如，假設我們將包含按鈕的另一個資料列新增至， `GridLayout` 如下所示：

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

這會導致要延展的第一個資料行 `GridLayout` 來容納按鈕的大小，如下所示：

[![GridLayoutDemo 的螢幕擷取畫面，其中只會跨越第一個資料行](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

若要保留第一個資料行的延展，可以設定其 columnspan 來跨越兩個數據行，如下所示：

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

這樣做會產生類似先前配置的版面配置 `TextViews` ，並將按鈕新增至底部，如下 `GridLayout` 所示：

 [![GridLayoutDemo 具有橫跨兩個數據行之按鈕的螢幕擷取畫面](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)

## <a name="related-links"></a>相關連結

- [GridLayoutDemo (範例) ](/samples/xamarin/monodroid-samples/gridlayoutdemo)