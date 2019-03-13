---
title: 工具列相容性
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 12c19cf1024b78e8be30b7c9f2652019e9854375
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110325"
---
# <a name="toolbar-compatibility"></a>工具列相容性


## <a name="overview"></a>總覽

本節說明如何使用`Toolbar`Android 5.0 Lollipop 之前的 Android 版本上。 如果您的應用程式不支援 Android 5.0 之前的 Android 版本，您可以略過本節。 

因為`Toolbar`屬於 Android v7 支援程式庫，它可以用於執行 Android 2.1 （API 層級 7） 的裝置及更高版本。 不過， [Android 支援程式庫 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)必須安裝 NuGet，並修改的程式碼，使其使用`Toolbar`此文件庫中提供實作。 本節說明如何安裝此 NuGet，並修改**ToolbarFun**應用程式[加入第二個工具列](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md)使其執行之前的版本的 Android Lollipop 5.0。

若要修改應用程式以使用工具列的 AppCompat 版本： 

1.  設定應用程式的最小值和目標 Android 版本。

2.  安裝 AppCompat NuGet 套件。

3.  使用 AppCompat 佈景主題，而不是內建 Android 佈景主題。

4.  修改`MainActivity`，讓它的子類別`AppCompatActivity`而非`Activity`。 

下列各節將詳細說明每個步驟。



## <a name="set-the-minimum-and-target-android-version"></a>設定最小值和目標 Android 版本

必須設定應用程式的目標 Framework，API Level 21 或更新版本，或應用程式將無法正確部署。 如果發生錯誤，例如**沒有資源識別項屬性 'tileModeX' 封裝 'android' 中找到**出現時部署應用程式，這是因為未設定目標 Framework **Android 5.0 (API Level 21-棒棒糖符號)** 或更新版本。 

設定目標架構層級 API Level 21 或更新版本，並設定 Android API 層級的專案設定為應用程式，就是支援的最低 Android 版本。 如需有關如何設定 Android API 層級的詳細資訊，請參閱 <<c0> [ 了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。 在 `ToolbarFun`範例中，最低 Android 版本設為 KitKat (4.4 的 API 層級)。 


## <a name="install-the-appcompat-nuget-package"></a>安裝 AppCompat NuGet 套件

接下來，新增[Android 支援程式庫 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)至專案的封裝。 在 Visual Studio 中，以滑鼠右鍵按一下**參考**，然後選取**管理 NuGet 套件...**.按一下 **瀏覽**並搜尋**Android 支援程式庫 v7 AppCompat**。 選取  **Xamarin.Android.Support.v7.AppCompat**然後按一下**安裝**: 

[![選取 管理 NuGet 套件中的螢幕擷取畫面的 V7 Appcompat 封裝](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

安裝此 NuGet 時，數個其他的 NuGet 封裝也會安裝尚不存在 (例如**Xamarin.Android.Support.Animated.Vector.Drawable**， **Xamarin.Android.Support.v4**，並**Xamarin.Android.Support.Vector.Drawable**)。 如需有關如何安裝 NuGet 套件的詳細資訊，請參閱 <<c0> [ 逐步解說： 在您的專案中包含 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。 


## <a name="use-an-appcompat-theme-and-toolbar"></a>使用 AppCompat 佈景主題和工具列

AppCompat 程式庫提供數個`Theme.AppCompat`可用在任何版本的 AppCompat 程式庫所支援的 Android 的佈景主題。 `ToolbarFun`範例應用程式佈景主題衍生自`Theme.Material.Light.DarkActionBar`，這並不適用於比棒棒糖符號之前的 Android 版本。 因此，`ToolbarFun`必須是改寫以使用 AppCompat 對應此佈景主題， `Theme.AppCompat.Light.DarkActionBar`。 此外，因為`Toolbar`是不適用於 Android 的版本早於棒棒糖符號，我們必須使用 AppCompat 版本`Toolbar`。 因此，必須使用版面配置`android.support.v7.widget.Toolbar`而不是`Toolbar`。 


### <a name="update-layouts"></a>更新版面配置

編輯**Resources/layout/Main.axml** ，並取代`Toolbar`具有下列 XML 項目： 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

編輯**Resources/layout/toolbar.xml**並以下列 XML 取代其內容： 

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"/>
```

請注意，`?attr`值會不會再加上`android:`(請記得，`?`標記法參考目前主題中的資源)。 如果`?android:attr`仍用在這裡，Android 會參考的屬性值從目前正在執行的平台，而不是從 AppCompat 程式庫。 因為此範例會使用`actionBarSize`AppCompat 程式庫所定義`android:`卸除前置詞。 同樣地，`@android:style`變更為`@style`以便`android:theme`屬性設為佈景主題 AppCompat 文件庫中&ndash;`ThemeOverlay.AppCompat.Dark.ActionBar`這裡使用佈景主題而不是`ThemeOverlay.Material.Dark.ActionBar`。 


### <a name="update-the-style"></a>更新樣式

編輯**Resources/values/styles.xml**並以下列 XML 取代其內容： 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="MyTheme.Base"> </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.DarkActionBar">
    <item name="windowNoTitle">true</item>
    <item name="windowActionBar">false</item>
    <item name="colorPrimary">#5A8622</item>
    <item name="colorAccent">#A88F2D</item>
  </style>
</resources>
```

項目名稱和父代佈景主題，在此範例中會不會再加上`android:`因為我們使用 AppCompat 程式庫。 此外，父佈景主題變更為 AppCompat 版本`Light.DarkActionBar`。 



### <a name="update-menus"></a>更新功能表

為了支援舊版的 Android，AppCompat 程式庫會使用鏡像的屬性的自訂屬性`android:`命名空間。 不過，某些屬性 (例如`showAsAction`中所使用的屬性`<menu>`標記) 不存在於較舊的裝置上 Android 架構&ndash; `showAsAction` Android API 11 中引進，但不適用於 Android API 7。 基於這個理由，必須使用自訂的命名空間前置詞的所有支援程式庫所定義的屬性。 在功能表資源檔中，呼叫命名空間`local`定義的前面加上`showAsAction`屬性。 

編輯**Resources/menu/top_menus.xml**並以下列 XML 取代其內容：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       local:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       local:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       local:showAsAction="never"
       android:title="Preferences" />
</menu>
```

`local`這一行加入命名空間：

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

`showAsAction`屬性開頭這`local:`命名空間而非 `android:` 

```csharp
local:showAsAction="ifRoom"
```

同樣地，編輯**Resources/menu/edit_menus.xml**並以下列 XML 取代其內容：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

此命名空間參數如何提供支援`showAsAction`API 層級 11 之前的 Android 版本的屬性？ 自訂屬性`showAsAction`和 AppCompat NuGet 安裝時，應用程式中包含所有可能的值。 


## <a name="subclass-appcompatactivity"></a>子類別 AppCompatActivity

轉換的最後一個步驟是修改`MainActivity`使它的子類別`AppCompactActivity`。 編輯 **MainActivity.cs** 並加入下列`using`陳述式： 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

這會宣告`Toolbar`AppCompat 版本`Toolbar`。 接下來，將變更的類別定義`MainActivity`: 

```csharp
public class MainActivity : AppCompatActivity
```

將動作列設定為 AppCompat 新版`Toolbar`的呼叫取代`SetActionBar`使用`SetSupportActionBar`。 在此範例中，也會變更標題表示 AppCompat 版本`Toolbar`正在使用：

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

最後，變更最低 Android 的層級 （例如，API 19） 都必須支援為前棒棒糖符號值。 

建置應用程式，並在前的棒棒糖符號裝置或 Android 模擬器上執行。 下列螢幕擷取畫面顯示 AppCompat 版本**ToolbarFun**執行 KitKat (API 19) Nexus 4 上： 

[![KitKat 裝置上執行的應用程式的完整螢幕擷取畫面，顯示這兩個工具列](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

使用 AppCompat 程式庫時，佈景主題不需要切換為基礎的 Android 版本&ndash;AppCompat 程式庫可讓您跨所有支援的 Android 版本提供一致的使用者體驗。 




## <a name="related-links"></a>相關連結

- [棒棒糖符號工具列 （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [AppCompat 工具列 （範例）](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
