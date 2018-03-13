---
title: "格線"
ms.topic: article
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: a8a9735845139da700959caf3639defa6594f307
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="gridlayout"></a>格線

`GridLayout`新`ViewGroup`支援 2D 方格，類似於 HTML 表格，在檢視的版面配置如下所示的子類別：

 [![裁剪顯示四個資料格的格線](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` 適用於一般檢視階層架構，其中子檢視設定方格中所指定的資料列和資料行應該在其位置。 如此一來，*格線*能夠放在方格中檢視，而不需要任何中繼檢視提供資料表的結構，例如 TableLayout 中使用的資料表資料列所示。 藉由維護一般階層，*格線*能夠更迅速地配置它的子檢視。 讓我們看一下範例來說明此概念實際的意義在程式碼中。


## <a name="creating-a-grid-layout"></a>建立格線版面配置

下列 XML 程式碼加入了許多`TextView`控制項*格線*。

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

版面配置將會調整的資料列和資料行的大小，讓資料格可以容納其內容，如下列圖表所示：

 [![在左側小於右邊顯示兩個資料格的版面配置的圖表](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

這會導致下列的使用者介面的應用程式中執行時：

 [![顯示四個資料格的螢幕擷取畫面的 GridLayoutDemo 應用程式](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)



## <a name="specifying-orientation"></a>指定方向

請注意，在 XML 上述項目，每個`TextView`未指定資料列或資料行。 當未指定這些項目，則`GridLayout`指派每個順序，根據方向的子檢視。 比方說，讓我們來變更格線的方向從預設值，也就是水平、 為垂直像這樣：

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

現在，`GridLayout`將定位下每個資料行中，而不是從左到右，從上的資料格，如下所示：

 [![說明如何儲存格都放置在垂直方向的圖表](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

這會導致下列的使用者介面，在執行階段：

 [![螢幕擷取畫面的 GridLayoutDemo 放置在垂直方向的資料格](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)



### <a name="specifying-explicit-position"></a>指定明確的位置

如果我們想要明確控制的位置中的子檢視`GridLayout`，我們可以設定其`layout_row`和`layout_column`屬性。 例如，下列 XML 會導致第一個螢幕擷取畫面 （如上所示），不論方向中顯示的配置。

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



### <a name="specifying-spacing"></a>指定的間距

我們有幾個選項，以提供的檢視子系之間的間距`GridLayout`。 我們可以使用`layout_margin`邊界各子檢視上直接設定，如下所示的屬性

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

此外，在 Android 4 中，新的一般用途的間距檢視呼叫`Space`現已提供。 若要使用它，只要將它加入做為子檢視。
例如，下列 XML 加入額外的資料列來`GridLayout`藉由設定其`rowcount`為 3，並將`Space`提供之間間距的檢視`TextViews`。

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

這段 XML 會建立在間距`GridLayout`如下所示：

 [![螢幕擷取畫面的 GridLayoutDemo，示範較大的儲存格間距與](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

使用新的好處`Space`檢視是它允許間距，而不需要我們設定的每個子檢視的屬性。



### <a name="spanning-columns-and-rows"></a>跨越資料行和資料列

`GridLayout`也支援跨越多個資料行和資料列的資料格。 例如，假設我們將加入另一個資料列包含按鈕`GridLayout`如下所示：

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

這會導致第一個資料行`GridLayout`我們在這裡看到延展適應按鈕的大小：

[![螢幕擷取畫面的 GridLayoutDemo 跨越第一個資料行的按鈕](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

為了避免自動縮放的第一個資料行，我們可以設定成跨越兩個資料行藉由設定其 columnspan 像這樣的按鈕：

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

這樣會導致的版面配置`TextViews`我們稍早，先前加入到底部的按鈕具有版面配置類似`GridLayout`如下所示：

 [![螢幕擷取畫面的 GridLayoutDemo 跨越兩個資料行的按鈕](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)


## <a name="related-links"></a>相關連結

- [GridLayoutDemo (sample)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/GridLayoutDemo/)
- [介紹的冰淇淋三明治](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
