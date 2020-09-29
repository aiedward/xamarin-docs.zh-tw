---
title: 新增第二個工具列
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: e6104a58c35b4daf8e204b3937022103d774615c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457768"
---
# <a name="adding-a-second-toolbar"></a>新增第二個工具列

## <a name="overview"></a>概觀 

`Toolbar`除了取代動作列之外，可以在 &ndash; 活動中多次使用它，您可以將它自訂為在螢幕上的任何位置進行放置，而且可以設定為只跨越螢幕的部分寬度。 下列範例說明如何建立第二個 `Toolbar` ，並將它放在畫面底部。 這會 `Toolbar` 實行 **複製**、 **剪**下和 **貼** 上功能表項目。 

## <a name="define-the-second-toolbar"></a>定義第二個工具列 

編輯版面配置檔案 **.axml** ，並將其內容取代為下列 XML：

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

這個 XML 會將第二個新增 `Toolbar` 至畫面底部，並將 `ImageView` 空白填滿螢幕的中間。 這的高度 `Toolbar` 會設定為動作列的高度： 

```xml
android:minHeight="?android:attr/actionBarSize"
```

這個的背景色彩會 `Toolbar` 設定為下一個將會定義的輔色：

```xml
android:background="?android:attr/colorAccent
```

請注意，這 `Toolbar` 是以不同的主題 (**ThemeOverlay ActionBar**) ，而這是在 `Toolbar` [取代動作列](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash; 未系結至活動的視窗 décor 或第一個中使用的主題 `Toolbar` 時所使用的主題。

編輯 **資源/值/styles.xml** ，並將下列輔色新增至樣式定義： 

```xml
<item name="android:colorAccent">#C7A935</item>
```

這會讓底部工具列成為深色的琥珀色色彩。 建立並執行應用程式會在畫面底部顯示空白的第二個工具列： 

[![畫面底部有黃色第二工具列的應用程式螢幕擷取畫面](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)

## <a name="add-edit-menu-items"></a>新增編輯功能表項目 

本節說明如何將 [編輯] 功能表項目新增至底部 `Toolbar` 。 

若要將功能表項目加入至次要 `Toolbar` ： 

1. 將功能表圖示新增至 `mipmap-` 應用程式專案的資料夾 (如有需要) 。

2. 藉由將額外的功能表資源檔新增至 **資源/功能表**，來定義功能表項目的內容。 

3. 在活動的 `OnCreate` 方法中， `Toolbar` 呼叫) 並擴充的功能表，以找出 (`FindViewById` `Toolbar` 。

4. `OnCreate`針對新的功能表項目，執行的 click 處理常式。 

下列各節將詳細說明此程式： [ **剪**下]、[ **複製**] 和 [ **貼** 上] 功能表項目會加入到底部 `Toolbar` 。 

### <a name="define-the-edit-menu-resource"></a>定義編輯功能表資源

在 [ **資源]/[功能表** ] 子目錄中，建立名為 **edit_menus.xml** 的新 XML 檔案，並將內容取代為下列 XML：

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

這個 XML 會建立 **剪**下、 **複製**和 **貼** 上功能表項目 (使用已加入資料夾中的圖示來 `mipmap-` [取代動作列](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)) 。

### <a name="inflate-the-menus"></a>擴充功能表

在 MainActivity.cs 方法的結尾 `OnCreate` ，新增**MainActivity.cs**下列幾行程式碼： 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

這段程式碼會尋找 `edit_toolbar` **.axml**中定義的視圖、將其標題設定為 **編輯**，以及擴大其功能表項目 (在 **edit_menus.xml**) 中定義。 它會定義功能表點擊處理常式，以顯示快顯通知，指出點擊了哪個編輯圖示。 

建置並執行應用程式。 當應用程式執行時，上面新增的文字和圖示將會出現，如下所示： 

[![具有剪下、複製和貼上圖示的底部工具列圖表](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

點擊 [ **剪** 下] 功能表圖示會顯示下列快顯通知： 

[![快顯通知的螢幕擷取畫面，指出已點擊剪下功能表圖示](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

在任一工具列上點擊功能表項目會顯示產生的快顯通知： 

[![點擊儲存、複製和貼上功能表項目的快顯通知螢幕擷取畫面](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)

## <a name="the-up-button"></a>向上鍵 

大部分的 Android 應用程式都依賴應用程式流覽的 [ **上一頁** ] 按鈕;按 [ **上一頁** ] 按鈕會將使用者帶到上一個畫面。
不過，您也可以提供 [ **向上** ] 按鈕，讓使用者輕鬆地流覽至應用程式的主畫面。 當使用者選取 [ **向上** ] 按鈕時，使用者會移至應用程式階層中較高的層級， &ndash; 也就是應用程式會在後端堆疊中快顯多個活動，而不是快顯至先前造訪過的活動。 

若要在第二個使用做為動作列的活動中啟用 [ **向上** ] 按鈕 `Toolbar` ，請 `SetDisplayHomeAsUpEnabled` `SetHomeButtonEnabled` 在第二個活動的方法中呼叫和方法 `OnCreate` ：

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

[ [支援 v7] 工具列](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar) 程式碼範例會示範作用中的 **向上** 按鈕。 此範例 (使用下一節所述的 AppCompat 程式庫) 會執行第二個活動，此活動會使用工具列 **向上** 按鈕來進行階層式流覽回到上一個活動。 在此範例中，[首頁] 按鈕會藉 `DetailActivity` 由進行下列方法呼叫來啟用 [ **向上** ] 按鈕 `SupportActionBar` ： 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

當使用者流覽至時 `MainActivity` `DetailActivity` ，會 `DetailActivity` 顯示 **向上** 按鈕 (左指向箭號) 如螢幕擷取畫面所示：

[![工具列中向上按鈕向左箭號的螢幕擷取畫面範例](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

按此 **按鈕** 會導致應用程式返回 `MainActivity` 。 在具有多層級階層的較複雜應用程式中，按此按鈕會將使用者返回應用程式中的下一個最高層級，而不是上一個畫面。 

## <a name="related-links"></a>相關連結

- [棒糖工具列 (範例) ](/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具列 (範例) ](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)