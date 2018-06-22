---
title: 新增第二個工具列
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: f2255ab5ac7e153266093877a1c52bfc08927a09
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766633"
---
# <a name="adding-a-second-toolbar"></a>新增第二個工具列


## <a name="overview"></a>總覽 

`Toolbar`可以執行多個取代動作列&ndash;它可以活動中使用多次，它可以是自訂螢幕上的任何位置的放置，它可以設定成跨越只有部分螢幕的寬度。 以下範例說明如何建立第二個`Toolbar`並將它放在畫面底部。 這`Toolbar`實作**複製**，**剪下**，和**貼上**功能表項目。 


## <a name="define-the-second-toolbar"></a>定義第二個工具列 

編輯版面配置檔案**Main.axml** ，並以下列 XML 取代其內容與：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/main_content"
        android:layout_below="@id/toolbar">
      <ImageView
          android:layout_width="fill_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
      <Toolbar
          android:id="@+id/edit_toolbar"
          android:minHeight="?android:attr/actionBarSize"
          android:background="?android:attr/colorAccent"
          android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
          android:layout_width="match_parent"
          android:layout_height="wrap_content" />
    </LinearLayout>
</RelativeLayout>
```

這段 XML 會將第二個`Toolbar`為空白畫面的底部`ImageView`填滿螢幕中央。 這個高度`Toolbar`設定動作列的高度為： 

```xml
android:minHeight="?android:attr/actionBarSize"
```

這樣的背景色彩`Toolbar`設輔色接下來要定義為：

```xml
android:background="?android:attr/colorAccent
```

請注意這個`Toolbar`根據不同的主題 (**ThemeOverlay.Material.Dark.ActionBar**) 比使用`Toolbar`中建立[取代動作列](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash;並未繫結至活動的視窗裝潢或第一次使用的佈景主題`Toolbar`。

編輯**Resources/values/styles.xml**和樣式定義中加入下列的輔色： 

```xml
<item name="android:colorAccent">#C7A935</item>
```

這可讓底部工具列深色琥珀色。 建置和執行應用程式會顯示空白的第二個工具列，在畫面底部： 

[![在畫面底部的黃色第二個工具列的應用程式的螢幕擷取畫面](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)


 
## <a name="add-edit-menu-items"></a>加入編輯功能表項目 

本節說明如何將編輯功能表項目新增至底部`Toolbar`。 

功能表項目加入次要`Toolbar`: 

1.  加入至功能表圖示`mipmap-`（如有必要） 在應用程式專案的資料夾。

2.  藉由加入更多的功能表資源檔案中定義的功能表項目內容**資源/功能表**。 

3.  在活動中`OnCreate`方法中，尋找`Toolbar`(藉由呼叫`FindViewById`) 和擴大`Toolbar`的功能表。

4.  實作 click 處理常式中的`OnCreate`，新的功能表項目。 

下列章節將說明此程序的詳細資料：**剪下**，**複製**，和**貼上**功能表項目加入至下方`Toolbar`。 



### <a name="define-the-edit-menu-resource"></a>定義編輯功能表資源

在**資源/功能表**子目錄，建立新的 XML 檔案，稱為**edit_menus.xml**並以下列 XML 取代內容：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

這段 XML 會建立**剪下**，**複製**，和**貼上**功能表項目 (使用圖示新增至`mipmap-`資料夾中的[取代動作列](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).



### <a name="inflate-the-menus"></a>擴大功能表

在結尾`OnCreate`方法中的**Weatherapp**，加入下列程式碼： 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

這段程式碼會找出`edit_toolbar`中定義的檢視**Main.axml**，設定其標題為**編輯**，擴大其功能表項目和 (定義於**edit_menus.xml**)。 它會定義功能表按一下 顯示快顯通知，表示已點選的編輯圖示的處理常式。 

建置和執行應用程式。 當應用程式執行時，文字和上述新增的圖示將會出現如下所示： 

[![使用剪下、 複製和貼上的圖示的工具列下方的圖表](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

點選**剪下**功能表圖示會顯示下列快顯通知： 

[![螢幕擷取畫面的快顯通知，指出已點選剪下 功能表圖示](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

點選 [任一] 工具列上的功能表項目會顯示產生的快顯通知： 

[![螢幕擷取畫面的快顯通知的儲存，請複製和貼上所點選的功能表項目](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)



## <a name="the-up-button"></a>向上按鈕 

大部分的 Android 應用程式依賴**回**應用程式瀏覽按鈕; 按**回**按鈕將使用者帶到上一個螢幕。
不過，您可能也要提供**向上**按鈕，以簡化使用者巡覽 「 向上 」 應用程式的主畫面。 當使用者選取**向上**按鈕，使用者移到應用程式的階層中較高層次&ndash;也就是應用程式會將使用者回多個活動在上一頁堆疊，而非雜訊回到先前的瀏覽活動。 

若要啟用**向上**中會使用第二個活動按鈕`Toolbar`其動作列中，呼叫`SetDisplayHomeAsUpEnabled`和`SetHomeButtonEnabled`方法中的第二個活動`OnCreate`方法：

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

[支援 v7 工具列](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)程式碼範例示範**向上**中動作的按鈕。 這個範例 （這會使用下面所描述的 AppCompat 程式庫） 會實作使用工具列的第二個活動**向上**按鈕回到前一項活動的階層式導覽。 在此範例中， `DetailActivity` [首頁] 按鈕可讓**向上**藉由下列按鈕`SupportActionBar`方法呼叫： 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

當使用者瀏覽從`MainActivity`至`DetailActivity`、`DetailActivity`顯示**向上**螢幕擷取畫面所示的按鈕 （左邊指標箭號）：

[![螢幕擷取畫面的向上按鈕向左箭號工具列中的範例](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

點選此**向上**按鈕會導致應用程式以返回`MainActivity`。 在具有多個階層層級的更複雜應用程式，點選此按鈕會傳回使用者的應用程式中的下一個最高等級，而不是上一個螢幕。 



## <a name="related-links"></a>相關連結

- [棒棒糖符號工具列 （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [AppCompat 工具列 （範例）](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
