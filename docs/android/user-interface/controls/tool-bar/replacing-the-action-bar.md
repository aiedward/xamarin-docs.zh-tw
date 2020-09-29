---
title: 取代動作列
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: 1339833c9971c70ccb855dc340b12eb60bf22350
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457662"
---
# <a name="replacing-the-action-bar"></a>取代動作列

## <a name="overview"></a>概觀

最常見的用途之一 `Toolbar` 是在 `Toolbar` 建立新的 Android 專案時，以自訂 (取代預設動作列，它會使用預設的動作列) 。 因為 `Toolbar` 可讓您將品牌標誌、標題、功能表項目、導覽按鈕，甚至是自訂的視圖新增至活動 UI 的應用程式行區段，所以它會透過預設動作列提供大幅的升級。

若要將應用程式的預設動作列取代為 `Toolbar` ： 

1. 建立新的自訂主題，並修改應用程式的屬性，使其使用這個新的主題。 

2. 停 `windowActionBar` 用自訂主題中的屬性，並啟用 `windowNoTitle` 屬性。

3. 定義的版面配置 `Toolbar` 。

4. `Toolbar`將版面配置包含在活動的 **.axml**版面配置檔案中。 

5. 將程式碼加入至活動的 `OnCreate` 方法，以找出 `Toolbar` 並呼叫，以將 `SetActionBar` 安裝 `ToolBar` 為動作列。

下列各節將詳細說明此程式。 建立簡單的應用程式，並以自訂的方式取代其動作列 `Toolbar` 。 

## <a name="start-an-app-project"></a>啟動應用程式專案

建立新的 Android 專案，稱為 **ToolbarFun** (如需有關建立新 android 專案) 的詳細資訊，請參閱 [Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md) 。 建立此專案之後，將目標和最低 Android API 層級設定為 **android 5.0 (API 層級 21-棒) ** 或更新版本。 如需設定 Android 版本層級的詳細資訊，請參閱 [瞭解 ANDROID API 層級](~/android/app-fundamentals/android-api-levels.md)。 建立並執行應用程式時，會顯示此螢幕擷取畫面中所見的預設動作列：

[![預設動作列的螢幕擷取畫面](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)

## <a name="create-a-custom-theme"></a>建立自訂主題

開啟 **資源/值** 目錄，然後建立名為 **styles.xml**的新檔案。 將其內容取代為下列 XML： 

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

這個 XML 會定義一個名為 **MyTheme** 的新自訂主題，此主題是以 **DarkActionBar** 中的主題為基礎。 `windowNoTitle`屬性設定為 `true` 以隱藏標題列： 

```xml
<item name="android:windowNoTitle">true</item>
```

若要顯示自訂工具列， `ActionBar` 必須停用預設值： 

```xml
<item name="android:windowActionBar">false</item>
```

[橄欖色-綠色] `colorPrimary` 設定用於工具列的背景色彩： 

```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>套用自訂主題

編輯 **屬性/AndroidManifest.xml** ，並將下列 `android:theme` 屬性新增至 `<application>` 元素，讓應用程式使用 `MyTheme` 自訂主題： 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

如需將自訂主題套用至應用程式的詳細資訊，請參閱 [使用自訂主題](~/android/user-interface/material-theme.md#customtheme)。 

## <a name="define-a-toolbar-layout"></a>定義工具列版面配置

在 **Resources/layout** 目錄中，建立名為 **toolbar.xml**的新檔案。 將其內容取代為下列 XML： 

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

此 XML `Toolbar` 會定義可取代預設動作列的自訂。 的最小高度 `Toolbar` 會設定為它所取代的動作列大小： 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

的背景色彩 `Toolbar` 會設定為稍早在 **styles.xml**中定義的橄欖色-綠色色彩：

```csharp
android:background="?android:attr/colorPrimary"
```

從棒糖開始， `android:theme` 可以使用屬性來為個別的視圖樣式。 `ThemeOverlay.Material`在棒裡引進的主題可讓您覆迭預設 `Theme.Material` 主題，覆寫相關的屬性，使其成為淺色或深色。 在此範例中， `Toolbar` 會使用深色主題，使其內容為淺色色彩： 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

使用這個設定，讓功能表項目與深色的背景色彩對比。

## <a name="include-the-toolbar-layout"></a>包含工具列版面配置

編輯版面配置檔案 **資源/配置/Main. .axml** ，並將其內容取代為下列 XML：

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

這個版面配置包含 `Toolbar` **toolbar.xml** 中定義的，並使用 `RelativeLayout` 來指定 `Toolbar` 要放在按鈕) 上方 UI (的最上方。 

## <a name="find-and-activate-the-toolbar"></a>尋找並啟用工具列

編輯 **MainActivity.cs** ，並新增下列 using 語句：

```csharp
using Android.Views;
```

此外，將下列幾行程式碼加入至方法的結尾 `OnCreate` ：

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

此 `Toolbar` 程式碼會尋找和呼叫， `SetActionBar` 以 `Toolbar` 取得預設的動作列特性。 工具列的標題會變更為 [ **我的工具列**]。 如這個程式碼範例所示， `ToolBar` 可以直接參考為動作列。 編譯並執行此應用程式 &ndash; 時，會顯示自訂的， `Toolbar` 以取代預設動作列： 

[![具有綠色色彩配置的自訂工具列螢幕擷取畫面](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

請注意，的 `Toolbar` 樣式會與套用 `Theme.Material.Light.DarkActionBar` 至應用程式其餘部分的主題分開。 

如果執行應用程式時發生例外狀況，請參閱下方的 [疑難排解](#troubleshooting) 一節。

## <a name="add-menu-items"></a>新增功能表項目 

在本節中，會將功能表新增至 `Toolbar` 。 的右上方區域 `ToolBar` 會保留給功能表項目， &ndash; 每個功能表項目 (也稱為 *動作專案*) 可以在目前活動中執行動作，或者可以代表整個應用程式執行動作。 

若要將功能表新增至 `Toolbar` ： 

1. 如果需要) 應用程式專案的資料夾中， (新增功能表圖示 `mipmap-` 。 Google 在 [材質圖示](https://design.google.com/icons/) 頁面上提供一組免費的功能表圖示。 

2. 在 [ **資源]/功能表**下新增功能表資源檔，以定義功能表項目的內容。 

3. 以 `OnCreateOptionsMenu` &ndash;  這個方法擴大功能表項目來執行活動的方法。 

4. 執行 `OnOptionsItemSelected` 活動的方法  &ndash; 。此方法會在按下功能表項目時執行動作。 

下列各節會詳細說明此程式，方法是將 [ **編輯** ] 和 [ **儲存** ] 功能表項目新增至自訂的 `Toolbar` 。 

### <a name="install-menu-icons"></a>安裝功能表圖示

繼續進行 `ToolbarFun` 範例應用程式，將功能表圖示新增至應用程式專案。 下載[工具列圖示](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true)、解壓縮，然後將解壓縮*mipmap*資料夾的內容複寫到 [ **ToolbarFun]/[資源**] 底下的 [專案*mipmap* ] 資料夾，並在專案中包含每個新增的圖示檔。

### <a name="define-a-menu-resource"></a>定義功能表資源

在**資源**下建立新的**功能表**子目錄。 在 **功能表** 子目錄中，建立名為 **top_menus.xml** 的新功能表資源檔，並將其內容取代為下列 XML： 

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

這個 XML 會建立三個功能表項目：

- 使用**Edit** `ic_action_content_create.png` 圖示 (鉛筆) 的 [編輯] 功能表項目。 

- 使用**Save** `ic_action_content_save.png` (磁片) 圖示的 [儲存] 功能表項目。 

- 沒有圖示的 **喜好** 設定功能表項目。

`showAsAction`[**編輯**] 和 [**儲存**] 功能表項目的屬性會設定為 `ifRoom` &ndash; 此設定， `Toolbar` 如果有足夠的空間可顯示，這些功能表項目就會出現在中。 [ **喜好** 設定] 功能表項目設定為 [] `showAsAction` `never` &ndash; 會導致 [ **喜好** 設定] 功能表出現在 [ *溢* 位] 功能表中， (三個垂直點) 。 

### <a name="implement-oncreateoptionsmenu"></a>執行 OnCreateOptionsMenu

將下列方法新增至 **MainActivity.cs**：

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android 會呼叫 `OnCreateOptionsMenu` 方法，讓應用程式可以指定活動的功能表資源。 在這個方法中， **top_menus.xml** 資源會擴大至傳遞的 `menu` 。 這段程式碼會讓新的 [ **編輯**]、[ **儲存**] 和 [ **喜好** 設定] 功能表項目出現在中 `Toolbar` 。 

### <a name="implement-onoptionsitemselected"></a>執行 OnOptionsItemSelected

將下列方法新增至 **MainActivity.cs**：

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

當使用者按下功能表項目時，Android 會呼叫 `OnOptionsItemSelected` 方法，並在選取的功能表項目中傳遞。 在此範例中，執行只會顯示一個快顯通知，指出點擊了哪個功能表項目。 

建立並執行 `ToolbarFun` ，以查看工具列中的新功能表項目。 `Toolbar`現在會顯示三個功能表圖示，如下列螢幕擷取畫面所示： 

[![說明編輯、儲存和溢位功能表項目位置的圖表](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

當使用者按下 [ **編輯** ] 功能表項目時，就會顯示快顯通知，指出 `OnOptionsItemSelected` 已呼叫該方法： 

[![點擊編輯專案時顯示的快顯通知螢幕擷取畫面](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

當使用者按下溢位功能表時，會顯示 [ **喜好** 設定] 功能表項目。 通常，較不常見的動作應放置在溢位功能表中 &ndash; 。此範例會使用溢位功能表做為 **喜好** 設定，因為它不會像 **編輯** 和 **儲存**一般一般地使用： 

[![出現在溢位功能表中之 [喜好設定] 功能表項目的螢幕擷取畫面](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

如需 Android 功能表的詳細資訊，請參閱 Android 開發人員 [功能表](https://developer.android.com/guide/topics/ui/menus.html) 主題。 

## <a name="troubleshooting"></a>疑難排解

下列秘訣可協助您在以工具列取代動作列時，偵測可能發生的問題。

### <a name="activity-already-has-an-action-bar"></a>活動已經有動作列

如果應用程式未正確設定為使用自訂主題（如套用 [自訂主題](#apply-the-custom-theme)中所述），則在執行應用程式時可能會發生下列例外狀況：

![未使用自訂主題時可能發生的錯誤](replacing-the-action-bar-images/03-theme-not-defined.png)

此外，可能會產生類似下列的錯誤訊息： _, 發生 illegalstateexception：此活動已經有視窗 décor 所提供的動作列。_ 

若要更正這個錯誤，請確認 `android:theme` 已將自訂主題的屬性新增至 `<application>` **屬性/AndroidManifest.xml**) 中的 (，如稍早在套用 [自訂主題](#apply-the-custom-theme)中所述。 此外，如果 `Toolbar` 配置或自訂主題的設定不正確，可能會造成這個錯誤。

## <a name="related-links"></a>相關連結

- [棒糖工具列 (範例) ](/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具列 (範例) ](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)