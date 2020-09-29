---
title: 工具列相容性
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 8d5f5ff1cfe7876862371a9732f0ab8186bbeeba
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457616"
---
# <a name="toolbar-compatibility"></a>工具列相容性

## <a name="overview"></a>概觀

本節說明如何 `Toolbar` 在 android 5.0 棒棒的 android 版本上使用。 如果您的應用程式不支援 Android 5.0 之前的 Android 版本，您可以略過本節。 

因為 `Toolbar` 是 android v7 支援程式庫的一部分，所以可用於執行 Android 2.1 (API 層級 7) 和更高版本的裝置。 不過，必須安裝 [Android 支援程式庫 V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet 並修改程式碼，才能使用 `Toolbar` 此程式庫中提供的執行。 本節說明如何安裝此 NuGet 和修改 **ToolbarFun** 應用程式，使其無法 [新增第二個工具列](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) ，使其在比棒5.0 更早的 Android 版本上執行。

若要修改應用程式以使用 AppCompat 版本的工具列： 

1. 設定應用程式的最小和目標 Android 版本。

2. 安裝 AppCompat NuGet 套件。

3. 使用 AppCompat 主題，而不是內建的 Android 主題。

4. 將它修改為子 `MainActivity` 類別， `AppCompatActivity` 而不是 `Activity` 。 

下列各節將詳細說明每個步驟。

## <a name="set-the-minimum-and-target-android-version"></a>設定 Android 版本的最小值和目標

應用程式的目標架構必須設定為 API 層級21或更新版本，否則應用程式將無法正確部署。 部署應用程式時，如果在 **套件 ' android ' 中找不到屬性 ' tileModeX ' 的資源識別碼** ，就會發生這種錯誤，這是因為目標 Framework 未設定為 **ANDROID 5.0 (API 層級 21-棒) ** 或更高。 

將目標 Framework 層級設定為 API 層級21或更高，並將 Android API 層級專案設定設為應用程式支援的最低 Android 版本。 如需設定 Android API 層級的詳細資訊，請參閱 [瞭解 ANDROID Api 層級](~/android/app-fundamentals/android-api-levels.md)。 在此 `ToolbarFun` 範例中，最低 Android 版本會設定為 KitKat (API 層級 4.4) 。 

## <a name="install-the-appcompat-nuget-package"></a>安裝 AppCompat NuGet 套件

接下來，將 [Android 支援程式庫 V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) 套件新增至專案。 在 Visual Studio 中，以滑鼠右鍵按一下 [ **參考** ]，然後選取 [ **管理 NuGet 套件**...]。按一下 **[流覽** 並搜尋 **Android 支援程式庫 v7 AppCompat**]。 選取 [ **v7] AppCompat** ，然後按一下 [ **安裝**]： 

[![在 [管理 NuGet 套件] 中選取 V7 Appcompat 套件的螢幕擷取畫面](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

安裝此 NuGet 時，也會一併安裝數個其他 NuGet 套件（如果尚未提供 (例如，例如，如 **支援**），則會安裝這些 nuget 套件。可繪製、支援 **v4**和 **xamarin**) 。 如需安裝 NuGet 套件的詳細資訊，請參閱 [逐步解說：在您的專案中包含 NuGet](/visualstudio/mac/nuget-walkthrough)。 

## <a name="use-an-appcompat-theme-and-toolbar"></a>使用 AppCompat 主題和工具列

AppCompat 程式庫隨附數個 `Theme.AppCompat` 主題，可用於 AppCompat 程式庫所支援的任何 Android 版本。 `ToolbarFun`範例應用程式主題是衍生自 `Theme.Material.Light.DarkActionBar` ，而不是在以棒糖之前的 Android 版本上提供。 因此， `ToolbarFun` 必須調整為使用這個主題的 AppCompat 對應 `Theme.AppCompat.Light.DarkActionBar` 。 此外，因為 `Toolbar` 在超過了棒的 Android 版本上無法使用，所以我們必須使用的 AppCompat 版本 `Toolbar` 。 因此，版面配置必須使用 `android.support.v7.widget.Toolbar` 而不是 `Toolbar` 。 

### <a name="update-layouts"></a>更新版面配置

編輯 **Resources/layout/Main. .axml** ，並 `Toolbar` 以下列 XML 取代元素： 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

編輯 **資源/版面配置/toolbar.xml** ，並將其內容取代為下列 XML： 

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

請注意，這些 `?attr` 值不會再加上前置詞， `android:` (回想表示 `?` 標記法會參考目前主題) 中的資源。 如果 `?android:attr` 在這裡仍使用，則 Android 會從目前執行中的平臺（而不是從 AppCompat 程式庫）參考屬性值。 因為此範例會使用 `actionBarSize` AppCompat 程式庫所定義的，所以 `android:` 會卸載前置詞。 同樣地， `@android:style` 會變更為， `@style` 以便將 `android:theme` 屬性設為 AppCompat 程式庫中的主題，而不是 &ndash; `ThemeOverlay.AppCompat.Dark.ActionBar` 使用主題 `ThemeOverlay.Material.Dark.ActionBar` 。 

### <a name="update-the-style"></a>更新樣式

編輯 **資源/值/styles.xml** ，並將其內容取代為下列 XML： 

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

此範例中的專案名稱和父主題不會再加上前置詞， `android:` 因為我們使用 AppCompat 程式庫。 此外，父主題也會變更為的 AppCompat 版本 `Light.DarkActionBar` 。 

### <a name="update-menus"></a>更新功能表

為了支援較早的 Android 版本，AppCompat 程式庫會使用可鏡像命名空間屬性的自訂屬性 `android:` 。 不過，某些屬性 (例如標籤中所 `showAsAction` 使用的屬性 `<menu>`) 不存在於舊版裝置上的 android framework 中，但不適用於 &ndash; `showAsAction` android api 7。 基於這個理由，您必須使用自訂命名空間來前置支援程式庫所定義的所有屬性。 在功能表資源檔中，名為的命名空間 `local` 定義為在屬性前面加上 `showAsAction` 。 

編輯 **資源/功能表/top_menus.xml** ，並將其內容取代為下列 XML：

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

`showAsAction`屬性的前面會加上 `local:` 此命名空間，而不是`android:` 

```csharp
local:showAsAction="ifRoom"
```

同樣地，編輯 **資源/功能表/edit_menus.xml** ，並將其內容取代為下列 XML：

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

此命名空間交換器如何 `showAsAction` 在 API 層級11之前的 Android 版本上提供屬性的支援？ `showAsAction`安裝 AppCompat NuGet 時，自訂屬性及其所有可能的值都會包含在應用程式中。 

## <a name="subclass-appcompatactivity"></a>子類別 AppCompatActivity

轉換中的最後一個步驟是修改， `MainActivity` 使其成為的子類別 `AppCompactActivity` 。 編輯 **MainActivity.cs** 並加入下列 `using` 語句： 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

這 `Toolbar` 會宣告為的 AppCompat 版本 `Toolbar` 。 接下來，變更的類別定義 `MainActivity` ： 

```csharp
public class MainActivity : AppCompatActivity
```

若要將動作列設定為的 AppCompat 版本 `Toolbar` ，請將呼叫取代 `SetActionBar` 為 `SetSupportActionBar` 。 在此範例中，標題也會變更，以指出正在使用的 AppCompat 版本 `Toolbar` ：

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

最後，將最低 Android 層級變更為要支援的高達棒 (（例如，API 19) ）。 

建立應用程式，並在棒棒的裝置或 Android 模擬器上執行。 下列螢幕擷取畫面顯示 AppCompat 版本的 **ToolbarFun** 在執行 KITKAT (API 19) 的結點4上： 

[![在 KitKat 裝置上執行的應用程式完整螢幕擷取畫面，會顯示這兩個工具列](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

使用 AppCompat 程式庫時，不需要根據 Android 版本來切換主題， &ndash; 因為 AppCompat 程式庫可以在所有支援的 android 版本之間提供一致的使用者體驗。 

## <a name="related-links"></a>相關連結

- [棒糖工具列 (範例) ](/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具列 (範例) ](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)