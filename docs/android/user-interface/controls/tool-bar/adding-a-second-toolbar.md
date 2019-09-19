---
title: 新增第二個工具列
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 9fb5c696e830710e6ad99140477eedcbfe0e8823
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764681"
---
# <a name="adding-a-second-toolbar"></a>新增第二個工具列

## <a name="overview"></a>總覽 

除了取代動作列之外，也可以在活動中多次使用它，它可以自訂以放置在螢幕上的任何位置，而且可以設定為只跨越螢幕的部分寬度。&ndash; `Toolbar` 下列範例說明如何建立第二個`Toolbar` ，並將它放在畫面底部。 這`Toolbar`會實行 [**複製**]、[**剪**下] 和 [**貼**上] 功能表項目。 

## <a name="define-the-second-toolbar"></a>定義第二個工具列 

編輯配置檔案**axml** ，並將其內容取代為下列 XML：

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

此 XML 會在畫面`Toolbar`底部新增第二個，並以空白`ImageView`填滿螢幕的中間。 這個`Toolbar`的高度會設定為動作列的高度： 

```xml
android:minHeight="?android:attr/actionBarSize"
```

這個`Toolbar`的背景色彩會設定為下一個將定義的輔色：

```xml
android:background="?android:attr/colorAccent
```

請注意， `Toolbar`這是以不同的主題（**ThemeOverlay. ActionBar**）為基礎，而不是由中`Toolbar`建立的用來[取代動作列](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash;它未系結至活動的視窗 décor 或第一個`Toolbar`中使用的主題。

編輯**Resources/values/style .xml** ，並在樣式定義中加入下列輔色： 

```xml
<item name="android:colorAccent">#C7A935</item>
```

這會讓底部工具列具有深色琥珀色色彩。 建立並執行應用程式會在畫面底部顯示一個空白的第二個工具列： 

[![螢幕底部具有黃色第二個工具列的應用程式螢幕擷取畫面](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)

## <a name="add-edit-menu-items"></a>新增編輯功能表項目 

本節說明如何將 [編輯] 功能表項目新增至底部`Toolbar`。 

若要將功能表項目加入至`Toolbar`次要資料庫： 

1. 將功能表圖示新增至`mipmap-`應用程式專案的資料夾（如有需要）。

2. 將其他功能表資源檔新增至**資源/功能表**，以定義功能表項目的內容。 

3. 在活動的`OnCreate`方法中， `Toolbar`尋找（藉由`Toolbar`呼叫`FindViewById`）並擴大的功能表。

4. 在中`OnCreate` ，為新的功能表項目執行 click 處理常式。 

下列各節將詳細說明此程式：[**剪**下]、[**複製**] 和 [**貼**上] `Toolbar`功能表項目會加入至底部。 

### <a name="define-the-edit-menu-resource"></a>定義 [編輯] 功能表資源

在 [**資源]/[功能表**] 子目錄中，建立名為**EDIT_MENUS**的新 XML 檔案，並將其內容取代為下列 XML：

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

這個 XML 會建立**剪下**、**複製**和**貼上**功能表項目(使用已新增至資料夾中的圖示來`mipmap-`[取代動作列](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md))。

### <a name="inflate-the-menus"></a>擴大功能表

在`OnCreate` **MainActivity.cs**的方法結尾處，新增下列幾行程式碼： 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

此程式碼會`edit_toolbar`尋找**axml**中定義的視圖、將其標題設定為**編輯**，以及擴大其功能表項目（定義于**edit_menus**中）。 它會定義一個功能表按一下處理常式，它會顯示快顯通知，指出已點擊的編輯圖示。 

建置和執行應用程式。 當應用程式執行時，上面新增的文字和圖示會如下所示： 

[![具有剪下、複製和貼上圖示的底部工具列圖表](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

點擊 [**剪**下] 功能表圖示會顯示下列快顯通知： 

[![快顯通知的螢幕擷取畫面，表示已點擊剪下功能表圖示](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

點擊任一工具列上的功能表項目會顯示產生的快顯通知： 

[![用於 [儲存]、[複製] 和 [貼上] 功能表項目的快顯通知螢幕擷取畫面](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)

## <a name="the-up-button"></a>向上按鈕 

大部分的 Android 應用程式都依賴 [**上一頁**] 按鈕來流覽應用程式;按 [**上一步**] 按鈕會讓使用者前往上一個畫面。
不過，您可能也會想要提供 [**向上**] 按鈕，讓使用者可以輕鬆地流覽至應用程式的主畫面。 當使用者選取 [**上**一步] 按鈕時，使用者會在應用程式&ndash;階層中往上移到較高的層級，也就是應用程式會將使用者向後堆疊中的多個活動彈出，而不是向先前造訪的活動顯示。 

若要在使用`Toolbar`做為動作列的第二個活動中啟用 [**上移**] `SetDisplayHomeAsUpEnabled`按鈕，請在第二個`OnCreate`活動的方法中呼叫和`SetHomeButtonEnabled`方法：

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

[[支援 v7] 工具列](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)程式碼範例會示範作用中的 [**向上**] 按鈕。 這個範例（使用下一節所述的 AppCompat 程式庫）會執行第二個活動，使用工具列**上移**按鈕，以階層式導覽回到上一個活動。 在此範例中， `DetailActivity` [首頁] 按鈕會藉由進行下列`SupportActionBar`方法呼叫來啟用 [**啟動**] 按鈕： 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

當使用者從`MainActivity`流覽到`DetailActivity`時，會`DetailActivity`顯示 [**向上**] 按鈕（向左指向箭頭），如螢幕擷取畫面所示：

[![工具列中向上按鈕向左箭號的螢幕擷取畫面範例](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

點擊此**按鈕**會導致應用程式回到`MainActivity`。 在具有多個階層層級的更複雜應用程式中，點擊此按鈕會將使用者返回應用程式中的下一個最高層級，而不是上一個畫面。 

## <a name="related-links"></a>相關連結

- [棒糖工具列（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具列（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
