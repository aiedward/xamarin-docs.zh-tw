---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 45e625b28bbdf0009b5cfcf661b00ce17638771d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667226"
---
# <a name="gridlayout"></a>GridLayout

`GridLayout`是一種新`ViewGroup`支援 2D 方格，類似於 HTML 表格中的檢視版面配置，如下所示的子類別：

 [![裁剪 GridLayout 顯示四個資料格](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` 適用於一般檢視階層，其中子檢視設定方格中所指定的資料列和資料行，它們應該在其位置。 如此一來， *GridLayout*能夠放在方格中的檢視，而不需要任何中繼檢視提供資料表的結構，例如 TableLayout 中所使用的資料表資料列所示。 藉由維護的平面階層*GridLayout*能夠更迅速地配置它的子檢視。 讓我們看看範例來說明此概念實際的意義在程式碼中。


## <a name="creating-a-grid-layout"></a>建立格線版面配置

下列 XML 程式碼加入了許多`TextView`控制項，成為*GridLayout*。

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

版面配置會調整的資料列和資料行大小，讓資料格可以配合其內容，如下列圖所示：

 [![在左側小於右邊顯示兩個資料格的版面配置的圖表](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

這會導致下列的使用者介面中的應用程式執行時：

 [![顯示四個資料格的螢幕擷取畫面的 GridLayoutDemo 應用程式](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)



## <a name="specifying-orientation"></a>指定的方向

請注意，在上述 XML 每個`TextView`未指定的資料列或資料行。 當未指定這些項目，則`GridLayout`指派每個順序，根據方向的子檢視。 比方說，讓我們變更的 GridLayout 方向從預設值，也就是水平、 為垂直像這樣：

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

現在，`GridLayout`將定位下，請在每個資料行，而不是從左到右，從上方的資料格，如下所示：

 [![說明如何將資料格放置在垂直方向的圖表](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

這會導致下列的使用者介面，在執行階段：

 [![螢幕擷取畫面的 GridLayoutDemo 放置在垂直方向的資料格](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)



### <a name="specifying-explicit-position"></a>指定明確的位置

如果我們想要明確控制中的子檢視的位置`GridLayout`，我們可以設定其`layout_row`和`layout_column`屬性。 例如，下列 XML 會導致第一個螢幕擷取畫面示 （如上所示），不論方向為何的版面配置。

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

我們有幾個選項，將提供的檢視之子系之間的間距`GridLayout`。 我們可以使用`layout_margin`設定邊界上的每一個子系檢視直接，如下所示的屬性

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

此外，在 Android 4 中，新的一般用途的間距檢視呼叫`Space`現已推出。 若要使用它，只要將它新增為子檢視。
例如，以下的 XML 將以額外的資料列`GridLayout`藉由設定其`rowcount`為 3，並將`Space`檢視，能夠之間的間距`TextViews`。

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

這段 XML 會建立在間距`GridLayout`，如下所示：

 [![螢幕擷取畫面的 GridLayoutDemo，說明具有間距較大的資料格](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

使用新的好處`Space`檢視是它允許間距，而且不需要我們在每個子檢視上設定屬性。



### <a name="spanning-columns-and-rows"></a>跨越資料行和資料列

`GridLayout`也支援跨多個資料行和資料列的資料格。 例如，假設我們新增另一個資料列包含一個按鈕`GridLayout`，如下所示：

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

這會導致第一個資料行`GridLayout`過度使用適應大小的按鈕，如我們在此處所見：

[![螢幕擷取畫面的 GridLayoutDemo 跨越第一個資料行的按鈕](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

為了避免自動縮放的第一個資料行，我們可以設定按鈕，以跨越兩個資料行，藉由設定其 columnspan 像這樣：

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

這樣會導致的版面配置`TextViews`類似我們稍早，已加入到底部的按鈕具備的版面配置`GridLayout`，如下所示：

 [![螢幕擷取畫面的 GridLayoutDemo 跨越兩個資料行的按鈕](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)


## <a name="related-links"></a>相關連結

- [GridLayoutDemo （範例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/GridLayoutDemo/)
- [簡介 Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](https://developer.android.com/sdk/android-4.0.html)
