---
title: "取代動作列"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 91d5612991c2297418cf7003c499c1a1bbfc7558
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="replacing-the-action-bar"></a>取代動作列

<a name="overview" />

## <a name="overview"></a>總覽

最常見的其中一個用於`Toolbar`為預設動作列取代自訂`Toolbar`（建立新的 Android 專案時，它會使用預設動作列）。 因為`Toolbar`可讓您將加上品牌的標誌、 標題、 功能表項目、 瀏覽按鈕和甚至自訂的檢視加入至活動的應用程式列區段 UI，它會提供大幅的升級預設動作條上方。

若要取代的應用程式的預設動作列`Toolbar`: 

1.  建立新的自訂佈景主題，並修改應用程式的屬性，以便讓它使用這個新佈景主題。 

2.  停用`windowActionBar`自訂佈景主題中的屬性並啟用`windowNoTitle`屬性。

3.  定義的配置`Toolbar`。

4.  包含`Toolbar`版面配置中的活動**Main.axml**配置檔案。 

5.  將程式碼加入至活動的`OnCreate`方法來找出`Toolbar`呼叫`SetActionBar`安裝`ToolBar`為動作列。

下列各節說明此程序的詳細資料。 建立簡單的應用程式，而且要更換其動作列與自訂`Toolbar`。 


<a name="start_project" />

## <a name="start-an-app-project"></a>啟動應用程式專案

建立新的 Android 專案，稱為**ToolbarFun** (請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)如需有關建立新的 Android 專案)。 建立這個專案之後，設定目標與最低的 Android API 層級為**Android 5.0 (API 層級 21-棒棒糖符號)**。 如需設定 Android 版本層級的詳細資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。 當應用程式會建置並執行時，它會顯示預設的動作列這個螢幕擷取畫面所示： 

[![預設動作列螢幕擷取畫面](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png)


<a name="custom_theme" />

## <a name="create-a-custom-theme"></a>建立自訂佈景主題

開啟**資源/值**目錄] 和 [建立新的檔案稱為**styles.xml**。 以下列 XML 取代其內容： 

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

這段 XML 會定義新的自訂佈景主題稱為**MyTheme**根據**Theme.Material.Light.DarkActionBar**棒棒糖符號中的主題。 `windowNoTitle`屬性設為`true`隱藏標題列： 

```xml
<item name="android:windowNoTitle">true</item>
```

若要顯示自訂的工具列上，預設值`ActionBar`必須停用： 

```xml
<item name="android:windowActionBar">false</item>
```

Olive-green`colorPrimary`工具列的背景色彩設定的用途： 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

編輯**Properties/AndroidManifest.xml**並加入下列`android:theme`屬性`<application>`項目，讓應用程式使用`MyTheme`自訂佈景主題： 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

如需有關如何將自訂的佈景主題套用至應用程式的詳細資訊，請參閱[使用自訂的佈景主題](~/android/user-interface/material-theme.md#customtheme)。 


<a name="toolbar_layout" />

## <a name="define-a-toolbar-layout"></a>定義工具列版面配置

在**資源/配置**目錄中，建立新的檔案稱為**toolbar.xml**。 以下列 XML 取代其內容： 

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

這段 XML 會定義自訂`Toolbar`，後者會取代預設動作列。 最小高度`Toolbar`設為它會取代動作橫條的大小： 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

背景色彩`Toolbar`設為稍早在定義的 olive-green 色彩**styles.xml**:

```csharp
android:background="?android:attr/colorPrimary"
```

開頭為棒棒糖符號，`android:theme`屬性可以用來設定個別的檢視的樣式。 `ThemeOverlay.Material`棒棒糖符號中導入的佈景主題會使其可覆疊預設`Theme.Material`、 覆寫相關的屬性，讓它們淡或暗色調佈景主題。 在此範例中，`Toolbar`暗色調佈景主題會使用以其內容是淺色： 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

這項設定使用，讓較暗的背景色彩對比功能表項目。


<a name="include_layout" />

## <a name="include-the-toolbar-layout"></a>包含的工具列版面配置

編輯版面配置檔案**Resources/layout/Main.axml** ，並以下列 XML 取代其內容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <Button
        android:id="@+id/MyButton"
        android:layout_below="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Hello World, Click Me!" />
</RelativeLayout>
```

這項配置包含`Toolbar`中定義**toolbar.xml**並用`RelativeLayout`指定`Toolbar`放置最上方的 ui （上方按鈕）。 


<a name="activate_toolbar" />

## <a name="find-and-activate-the-toolbar"></a>尋找並啟動工具列

編輯**Weatherapp**並加入下列 using 陳述式：

```csharp
using Android.Views;
```

此外，將下列程式碼加入至結尾`OnCreate`方法：

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

此程式碼會尋找`Toolbar`和呼叫`SetActionBar`以便`Toolbar`需要預設動作列特性。 工具列的標題變更為**我工具列**。 此程式碼範例中所見`ToolBar`可以當做動作列來直接參考。 編譯及執行此應用程式&ndash;自訂`Toolbar`預設動作列的位置會顯示： 

[![具有綠色的色彩配置的自訂工具列的螢幕擷取畫面](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png)

請注意，`Toolbar`樣式之外，獨立`Theme.Material.Light.DarkActionBar`佈景主題套用至應用程式的其餘部分。 


<a name="main_menus" />
 
## <a name="add-menu-items"></a>加入功能表項目 

在本節中，功能表會加入至`Toolbar`。 上層區域的`ToolBar`是保留的功能表項目&ndash;每個功能表項目 (也稱為*動作項目*) 可以執行在目前活動的動作，或它可以執行的動作，代表整個應用程式。 

若要加入至功能表`Toolbar`: 

1.  將功能表圖示 （如果需要） 加入至`mipmap-`應用程式專案的資料夾。 提供一組可用的功能表圖示 Google[材料圖示](https://design.google.com/icons/)頁面。 

2.  藉由加入新的功能表資源檔下定義的功能表項目內容**資源/功能表**。 

3.  實作`OnCreateOptionsMenu`活動方法&ndash;這個方法會擴大的功能表項目。 

4.  實作`OnOptionsItemSelected`活動方法&ndash;點選功能表項目時，這個方法會執行動作。 

下列章節將說明此程序的詳細資料加入**編輯**和**儲存**功能表項目自訂的`Toolbar`。 


<a name="menu_icons" />

### <a name="install-menu-icons"></a>安裝功能表圖示

繼續`ToolbarFun`範例應用程式，將功能表圖示加入至應用程式專案。 下載[工具列 icons.zip](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons.zip?raw=true)並將它解壓縮。 將解壓縮的內容複製*mipmap-*至專案資料夾*mipmap-*資料夾下的**ToolbarFun/資源**並將每個加入的圖示檔案包含在專案中。

<a name="menu_resource" />

### <a name="define-a-menu-resource"></a>定義功能表資源

建立新**功能表**下的子目錄**資源**。 在**功能表**子目錄，建立新的功能表資源檔，稱為**top_menus.xml** ，並以下列 XML 取代其內容： 

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

-   **編輯**功能表項目使用`ic_action_content_create.png`圖示 （鉛筆）。 

-   A**儲存**功能表項目使用`ic_action_content_save.png`圖示 （磁片）。 

-   A**喜好設定**沒有圖示的功能表項目。

`showAsAction`屬性**編輯**和**儲存**功能表項目設定為`ifRoom`&ndash;才會出現在這些功能表項目，此設定會`Toolbar`如果沒有它們會顯示有足夠的空間。 **喜好設定**功能表項目集`showAsAction`至`never`&ndash;這會導致**喜好設定**功能表出現在*溢位*功能表 （三個垂直的點）。 

<a name="on_create_options_menu" />

### <a name="implement-oncreateoptionsmenu"></a>實作 OnCreateOptionsMenu

將下列方法加入**Weatherapp**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android 呼叫`OnCreateOptionsMenu`方法，讓應用程式可以指定活動的功能表資源。 在此方法中， **top_menus.xml**資源擴大到傳入`menu`。 此程式碼會造成新**編輯**，**儲存**，和**喜好設定**功能表項目才會出現在`Toolbar`。 


<a name="on_options_item_selected" />

### <a name="implement-onoptionsitemselected"></a>實作 OnOptionsItemSelected

將下列方法加入**Weatherapp**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

當使用者點選功能表項目時，會呼叫 Android`OnOptionsItemSelected`方法，並選取功能表項目中的傳遞。 在此範例中，實作只會顯示快顯通知，表示已點選的功能表項目。 

建置並執行`ToolbarFun`查看工具列中的新功能表項目。 `Toolbar`現在會顯示三個功能表圖示，如下列螢幕擷取畫面所示： 

[![儲存圖表的編輯，用於說明的位置和溢位功能表項目](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png)

當使用者點選**編輯**功能表項目，快顯會顯示表示`OnOptionsItemSelected`方法呼叫： 

[![螢幕擷取畫面的快顯通知時，點選 編輯項目顯示](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png)

當使用者點選溢位功能表，**喜好設定**功能表項目會顯示。 通常，較不常見的動作應該放在溢位功能表&ndash;這個範例會使用的溢位功能表**喜好設定**因為不需要經常使用做為**編輯**和**儲存**: 

[![會出現在溢位功能表的螢幕擷取畫面的喜好設定功能表項目](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png)

如需 Android 功能表的詳細資訊，請參閱 Android 開發人員[功能表](https://developer.android.com/guide/topics/ui/menus.html)主題。 
 



## <a name="related-links"></a>相關連結

- [棒棒糖符號工具列 （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [AppCompat 工具列 （範例）](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
