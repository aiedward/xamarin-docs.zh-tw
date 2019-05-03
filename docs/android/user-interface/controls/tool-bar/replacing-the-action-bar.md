---
title: 取代動作列
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/27/2018
ms.openlocfilehash: 9e9fa1e2651661670f89baac7fcd438b3d14bfb3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61200799"
---
# <a name="replacing-the-action-bar"></a>取代動作列

## <a name="overview"></a>總覽

最常見的其中一個用於`Toolbar`就是把自訂的預設動作列`Toolbar`（建立新的 Android 專案時，它會使用預設的動作列）。 因為`Toolbar`讓您能夠新增至活動的應用程式列區段中加上品牌的標誌、 標題、 功能表項目、 瀏覽按鈕和甚至是自訂檢視的 UI，它提供重大升級比預設的動作列。

若要將使用的應用程式的預設動作列`Toolbar`: 

1.  建立新的自訂佈景主題，並修改應用程式的屬性，使其使用這個新佈景主題。 

2.  停用`windowActionBar`自訂佈景主題的屬性，並啟用`windowNoTitle`屬性。

3.  定義的配置`Toolbar`。

4.  包含`Toolbar`活動中的版面配置**Main.axml**版面配置檔。 

5.  將程式碼加入至活動的`OnCreate`方法來找出`Toolbar`，並呼叫`SetActionBar`安裝`ToolBar`為動作列。

下列各節說明此程序的詳細資料。 建立簡單的應用程式，並取代其動作列以自訂`Toolbar`。 



## <a name="start-an-app-project"></a>啟動應用程式專案

建立新的 Android 專案，稱為**ToolbarFun** (請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)如需有關建立新的 Android 專案)。 此專案建立之後，目標及最低 Android API 層級設定為**Android 5.0 (API Level 21-棒棒糖符號)** 或更新版本。 如需有關設定 Android 版本層級的詳細資訊，請參閱 <<c0> [ 了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。 當應用程式會建置並執行時，它會顯示預設的動作列在這個螢幕擷取畫面所示：

[![預設動作列螢幕擷取畫面](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)



## <a name="create-a-custom-theme"></a>建立自訂佈景主題

開啟**資源/值**目錄] 和 [建立新的檔案稱為**styles.xml 組**。 其內容取代為下列 XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="@android:style/Theme.Material.Light.DarkActionBar">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowActionBar">false</item>
    <item name="android:colorPrimary">#5A8622</item>
  </style>
</resources>
```

這個 XML 會定義新的自訂佈景主題，以呼叫**MyTheme**為基礎**Theme.Material.Light.DarkActionBar** Lollipop 中的佈景主題。 `windowNoTitle`屬性設為`true`隱藏標題列： 

```xml
<item name="android:windowNoTitle">true</item>
```

若要顯示自訂工具列，預設值`ActionBar`必須停用： 

```xml
<item name="android:windowActionBar">false</item>
```

Olive-green`colorPrimary`設定適用於工具列的背景色彩： 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>套用自訂佈景主題

編輯**properties/Androidmanifest.xml**並新增下列`android:theme`屬性設定為`<application>`項目，讓應用程式使用`MyTheme`自訂佈景主題： 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

如需有關如何將自訂的佈景主題套用至應用程式的詳細資訊，請參閱 <<c0> [ 使用自訂的佈景主題](~/android/user-interface/material-theme.md#customtheme)。 



## <a name="define-a-toolbar-layout"></a>定義工具列版面配置

在 **資源/配置**目錄中，建立新的檔案，稱為**toolbar.xml**。 其內容取代為下列 XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?android:attr/actionBarSize"
    android:background="?android:attr/colorPrimary"
    android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"/>
```

這個 XML 會定義自訂`Toolbar`取代預設的動作列。 最小高度`Toolbar`設為它會取代動作列的大小： 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

背景色彩`Toolbar`設定為稍早在定義的 olive-green 色彩**styles.xml 組**:

```csharp
android:background="?android:attr/colorPrimary"
```

開頭為棒棒糖符號，`android:theme`屬性可用來設定樣式的個別檢視。 `ThemeOverlay.Material` Lollipop 中引進的佈景主題會使您能夠覆疊預設`Theme.Material`、 覆寫相關的屬性，讓它們淡或暗色調佈景主題。 在此範例中，`Toolbar`使用深色佈景主題，讓其內容是採用淺色： 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

會使用此設定，以便與深的背景色彩相反的功能表項目。



## <a name="include-the-toolbar-layout"></a>包含工具列版面配置

編輯版面配置檔**Resources/layout/Main.axml**並以下列 XML 取代其內容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
</RelativeLayout>
```

這項配置包含`Toolbar`中定義**toolbar.xml**並用`RelativeLayout`來指定`Toolbar`UI （上方按鈕） 的最上方放置。 



## <a name="find-and-activate-the-toolbar"></a>尋找及啟動工具列

編輯**MainActivity.cs**並新增下列 using 陳述式：

```csharp
using Android.Views;
```

此外，將下列程式碼行新增至結尾`OnCreate`方法：

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

此程式碼會尋找`Toolbar`並呼叫`SetActionBar`以便`Toolbar`會採取預設動作列特性。 在工具列的標題會變成**My 工具列**。 在此程式碼範例中所示`ToolBar`可以直接參考做為動作列。 編譯並執行此應用程式&ndash;自訂`Toolbar`預設動作列中的位置會顯示： 

[![具有綠色的色彩配置的自訂工具列的螢幕擷取畫面](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

請注意，`Toolbar`樣式之外，獨立`Theme.Material.Light.DarkActionBar`佈景主題套用至應用程式的其餘部分。 

如果在執行應用程式時發生例外狀況，請參閱[疑難排解](#troubleshooting)下一節。

 
## <a name="add-menu-items"></a>加入功能表項目 

在本節中，功能表會加入至`Toolbar`。 右上方`ToolBar`保留供功能表項目&ndash;每個功能表項目 (也稱為*動作項目*) 可以執行的目前活動內的某個動作，或它能執行的動作，代表整個應用程式。 

若要新增至功能表`Toolbar`: 

1.  將功能表圖示 （如有必要） 加入至`mipmap-`的應用程式專案的資料夾。 Google 提供一組免費的功能表圖示[材料圖示](https://design.google.com/icons/)頁面。 

2.  藉由加入新的功能表資源檔下定義的功能表項目內容**資源/功能表**。 

3.  實作`OnCreateOptionsMenu`活動方法&ndash;這個方法會擴大的功能表項目。 

4.  實作`OnOptionsItemSelected`活動方法&ndash;時點選功能表項目時，這個方法會執行動作。 

下列各節示範此程序的詳細資料，藉由新增**編輯**並**儲存**要自訂功能表項目`Toolbar`。 



### <a name="install-menu-icons"></a>安裝功能表圖示

繼續進行`ToolbarFun`範例應用程式，將功能表圖示加入至應用程式專案。 下載[工具列圖示](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true)、 解壓縮，並複製的內容解壓縮*mipmap-* 專案的資料夾*mipmap-* 下的資料夾**ToolbarFun /資源**和包含在專案中的每個新增的圖示檔案。


### <a name="define-a-menu-resource"></a>定義功能表資源

建立新 **功能表** 下方的子目錄 **資源**。 在 [ **] 功能表**子目錄中，建立新的功能表資源檔，稱為**top_menus.xml**並以下列 XML 取代其內容： 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       android:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       android:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       android:showAsAction="never"
       android:title="Preferences" />
</menu>
```

這段 XML 會建立三個功能表項目：

-   **編輯**使用的功能表項目`ic_action_content_create.png`圖示 （鉛筆）。 

-   A**儲存**使用的功能表項目`ic_action_content_save.png`圖示 （存到磁片）。 

-   A**喜好設定**沒有圖示的功能表項目。

`showAsAction`的屬性**編輯**並**儲存**功能表項目會設定為`ifRoom`&ndash;這項設定會導致這些功能表項目，才會出現在`Toolbar`如果沒有足夠的空間可加以顯示。 **喜好設定** 功能表項目集`showAsAction`要`never`&ndash;這樣**喜好設定**才會出現在功能表*溢位*功能表 （三個垂直的點）。 


### <a name="implement-oncreateoptionsmenu"></a>實作 OnCreateOptionsMenu

將下列方法加入**MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android 呼叫`OnCreateOptionsMenu`方法，讓應用程式可以指定活動的功能表資源。 在此方法中， **top_menus.xml**資源會擴大至傳遞`menu`。 此程式碼會使新**編輯**，**儲存**，並**喜好設定**功能表項目，才會出現在`Toolbar`。 



### <a name="implement-onoptionsitemselected"></a>實作 OnOptionsItemSelected

將下列方法加入**MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

當使用者點選功能表項目時，Android 會呼叫`OnOptionsItemSelected`方法，並選取功能表項目中的傳遞。 在此範例中，實作只會顯示快顯通知，指出所點選的功能表項目。 

建置並執行`ToolbarFun`檢視工具列中的新功能表項目。 `Toolbar`現在會顯示三個的功能表圖示，如以下螢幕擷取畫面所示： 

[![儲存圖表的編輯、 示範位置和溢位功能表項目](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

當使用者點選**編輯** 功能表項目，快顯通知會顯示表示`OnOptionsItemSelected`呼叫方法： 

[![螢幕擷取畫面的快顯通知時點選 編輯項目時顯示](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

當使用者點選溢位功能表中，**喜好設定**功能表項目會顯示。 一般而言，較不常見的動作應該放在溢位功能表&ndash;這個範例會使用的溢位功能表**喜好設定**因為不需要經常用作為**編輯**和**儲存**: 

[![溢位功能表中顯示的螢幕擷取畫面的喜好設定 功能表項目](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

如需有關 Android 功能表的詳細資訊，請參閱 Android 開發人員[功能表](https://developer.android.com/guide/topics/ui/menus.html)主題。 
 

## <a name="troubleshooting"></a>疑難排解

下列秘訣有助於偵錯時取代工具列中的動作列可能發生的問題。

### <a name="activity-already-has-an-action-bar"></a>活動已經有動作列

如果應用程式未正確設定要使用自訂的佈景主題中所述[將自訂的佈景主題套用](#apply-the-custom-theme)，執行應用程式可能會發生下列例外狀況：

![不使用自訂的佈景主題時可能發生的錯誤](replacing-the-action-bar-images/03-theme-not-defined.png)

此外，可能會產生錯誤訊息如下所示：_Java.Lang.IllegalStateException:此活動已經有視窗 decor 所提供的動作列。_ 

若要更正這個錯誤，請確認`android:theme`屬性 (attribute) 的自訂佈景主題新增至`<application>`(在**properties/Androidmanifest.xml**) 中稍早所述[套用自訂佈景主題](#apply-the-custom-theme). 此外，此錯誤可能造成如果`Toolbar`版面配置] 或 [自訂的佈景主題未正確設定。


## <a name="related-links"></a>相關連結

- [棒棒糖符號工具列 （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [AppCompat 工具列 （範例）](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
