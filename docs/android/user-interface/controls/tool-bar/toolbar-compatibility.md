---
title: 工具列相容性
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 809dc8ec8fd1106b8ad8631c0c506067abdf0d97
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029077"
---
# <a name="toolbar-compatibility"></a>工具列相容性

## <a name="overview"></a>總覽

本節說明如何在 Android 5.0 棒的 Android 版本之前，使用 `Toolbar`。 如果您的應用程式不支援 Android 5.0 之前的 Android 版本，您可以略過本節。 

因為 `Toolbar` 是 Android v7 支援程式庫的一部分，所以可用於執行 Android 2.1 （API 層級7）和更高版本的裝置上。 不過， [Android 支援程式庫 V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet 必須安裝並修改程式碼，才能使用此程式庫中提供的 `Toolbar` 執行。 本節說明如何安裝此 NuGet，並從[新增第二個工具列](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md)來修改**ToolbarFun**應用程式，使其在5.0 棒的 Android 版本上執行。

若要修改應用程式以使用 AppCompat 版本的工具列： 

1. 設定應用程式的最小和目標 Android 版本。

2. 安裝 AppCompat NuGet 套件。

3. 使用 AppCompat 主題，而不是內建的 Android 主題。

4. 修改 `MainActivity`，使其子類別 `AppCompatActivity` 而不是 `Activity`。 

下列各節將詳細說明每個步驟。

## <a name="set-the-minimum-and-target-android-version"></a>設定 Android 版本的最低和目標

應用程式的目標 Framework 必須設定為 API 層級21或更新版本，否則應用程式將無法正確部署。 部署應用程式時，如果找不到**套件 ' android ' 中屬性 ' tileModeX ' 的資源識別碼**之類的錯誤，這是因為目標 Framework 未設定為**ANDROID 5.0 （API 層級 21-棒）** 或更高。 

將目標 Framework 層級設定為 API 層級21或更新版本，並將 Android API 層級專案設定設為應用程式支援的最低 Android 版本。 如需設定 Android API 層級的詳細資訊，請參閱[瞭解 ANDROID Api 層級](~/android/app-fundamentals/android-api-levels.md)。 在 `ToolbarFun` 範例中，最低 Android 版本會設定為 KitKat （API 層級4.4）。 

## <a name="install-the-appcompat-nuget-package"></a>安裝 AppCompat NuGet 套件

接下來，將[Android 支援程式庫 V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)套件新增至專案。 在 Visual Studio 中，以滑鼠右鍵按一下 [**參考**]，然後選取 [**管理 NuGet 套件 ...** ]。按一下 **[流覽]** ，並搜尋**Android 支援程式庫 v7 AppCompat**。 選取 [ **v7] AppCompat** ，然後按一下 [**安裝**]： 

[在 [管理 NuGet 套件] 中選取的 V7 Appcompat 套件![螢幕擷取畫面](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

安裝此 NuGet 時，如果尚未存在，則也會安裝數個其他 NuGet 套件（例如，[**支援**]、[中]、[xamarin]、[**支援 v4**] 和**支援. Vector**）。 如需安裝 NuGet 套件的詳細資訊，請參閱[逐步解說：在您的專案中包含 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。 

## <a name="use-an-appcompat-theme-and-toolbar"></a>使用 AppCompat 主題和工具列

AppCompat 程式庫隨附數個 `Theme.AppCompat` 主題，可用於 AppCompat 程式庫所支援的任何 Android 版本。 `ToolbarFun` 範例應用程式主題衍生自 `Theme.Material.Light.DarkActionBar`，這在 Android 版之前無法使用。 因此，`ToolbarFun` 必須調整為使用此主題的 AppCompat 對應，`Theme.AppCompat.Light.DarkActionBar`。 此外，因為在 AppCompat 之前的 Android 版本上無法使用 `Toolbar`，所以我們必須使用 `Toolbar`的版本。 因此，版面配置必須使用 `android.support.v7.widget.Toolbar`，而不是 `Toolbar`。 

### <a name="update-layouts"></a>更新版面配置

編輯**Resources/layout/axml** ，並將 `Toolbar` 元素取代為下列 XML： 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

編輯**Resources/layout/toolbar** ，並將其內容取代為下列 xml： 

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

請注意，`?attr` 值不再加上 `android:` 的前置詞（請記住，`?` 標記法會參考目前主題中的資源）。 如果此處仍使用 `?android:attr`，Android 會從目前正在執行的平臺（而不是從 AppCompat 程式庫）參考屬性值。 因為此範例使用 AppCompat 程式庫所定義的 `actionBarSize`，所以會卸載 `android:` 前置詞。 同樣地，`@android:style` 會變更為 `@style`，因此 `android:theme` 屬性會設定為 AppCompat 程式庫中的主題，&ndash; `ThemeOverlay.AppCompat.Dark.ActionBar` 主題會在這裡使用，而不是 `ThemeOverlay.Material.Dark.ActionBar`。 

### <a name="update-the-style"></a>更新樣式

編輯**Resources/values/樣式 .xml** ，並將其內容取代為下列 xml： 

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

這個範例中的專案名稱和父主題已不再加上 `android:`，因為我們使用的是 AppCompat 程式庫。 此外，父主題也會變更為 `Light.DarkActionBar`的 AppCompat 版本。 

### <a name="update-menus"></a>更新功能表

為了支援舊版的 Android，AppCompat 程式庫會使用可鏡像 `android:` 命名空間之屬性的自訂屬性。 不過，某些屬性（例如 `<menu>` 標籤中使用的 `showAsAction` 屬性）不存在於 android framework 的舊版裝置上，&ndash; `showAsAction` 是在 android API 11 中引進，但不適用於 Android API 7。 基於這個理由，必須使用自訂命名空間來為支援程式庫所定義的所有屬性加上前置詞。 在功能表資源檔中，會定義名為 `local` 的命名空間，以在 `showAsAction` 屬性前面加上。 

編輯**Resources/menu/top_menus** ，並將其內容取代為下列 xml：

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

這一行會加入 `local` 命名空間：

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

`showAsAction` 屬性前面會加上這個 `local:` 命名空間，而不是 `android:` 

```csharp
local:showAsAction="ifRoom"
```

同樣地，請編輯**Resources/menu/edit_menus** ，並將其內容取代為下列 xml：

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

此命名空間交換器如何在 API 層級11之前，針對 Android 版本上的 `showAsAction` 屬性提供支援？ 安裝 AppCompat NuGet 時，自訂屬性 `showAsAction` 及其所有可能的值都包含在應用程式中。 

## <a name="subclass-appcompatactivity"></a>子類別 AppCompatActivity

轉換的最後一個步驟是修改 `MainActivity`，使其成為 `AppCompactActivity`的子類別。 編輯**MainActivity.cs**並新增下列 `using` 語句： 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

這會將 `Toolbar` 宣告為 `Toolbar`的 AppCompat 版本。 接下來，變更 `MainActivity`的類別定義： 

```csharp
public class MainActivity : AppCompatActivity
```

若要將動作列設定為 AppCompat 版本的 `Toolbar`，請以 `SetSupportActionBar`取代 `SetActionBar` 的呼叫。 在此範例中，標題也會變更，以指出正在使用 `Toolbar` 的 AppCompat 版本：

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

最後，將最小的 Android 層級變更為要支援的前棒型值（例如，API 19）。 

建立應用程式，並在棒型裝置或 Android 模擬器上執行。 下列螢幕擷取畫面顯示在執行 KitKat （API 19）的第4部上， **ToolbarFun**的 AppCompat 版本： 

[![在 KitKat 裝置上執行之應用程式的完整螢幕擷取畫面，會顯示這兩個工具列](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

使用 AppCompat 程式庫時，不需要根據 Android 版本來切換主題 &ndash; AppCompat 程式庫可讓您在所有支援的 Android 版本中提供一致的使用者體驗。 

## <a name="related-links"></a>相關連結

- [棒糖工具列（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具列（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
