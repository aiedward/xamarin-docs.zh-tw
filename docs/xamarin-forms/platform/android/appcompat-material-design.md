---
title: 新增 AppCompat 和材質設計
description: 這篇文章說明如何將轉換現有的 Xamarin.Forms Android 應用程式，以使用 AppCompat 和材質設計。
ms.prod: xamarin
ms.assetid: 045FBCDF-4D45-48BB-9911-BD3938C87D58
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2017
ms.openlocfilehash: c2eed44a7c684b91ceed4493a83ff3b4e1578b5f
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209313"
---
# <a name="adding-appcompat-and-material-design"></a>新增 AppCompat 和材質設計

_請遵循下列步驟，將現有的 Xamarin.Forms Android 應用程式，以使用 AppCompat 和材質設計_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>總覽

下列指示說明如何更新現有的 Xamarin.Forms Android 應用程式來使用 AppCompat 程式庫並材料設計您的 Xamarin.Forms 應用程式的 Android 版本。

### <a name="1-update-xamarinforms"></a>1.更新 Xamarin.Forms

請確定此解決方案使用 Xamarin.Forms 2.0 或更新版本。 如有必要，請更新為 2.0 的 Xamarin.Forms Nuget 套件。

### <a name="2-check-android-version"></a>2.檢查 Android 版本

請確定此 Android 專案的目標架構是 Android 6.0 (Marshmallow)。 請檢查**Android 專案 > 選項 > 建置 > 一般**選取設定，以確保 corrent framework:

 ![](appcompat-images/target-android-6-sml.png "建置 android 一般組態")

### <a name="3-add-new-themes-to-support-material-design"></a>3.加入新的佈景主題，以支援材料設計

在 您的 Android 專案中建立下列三個檔案並貼上下列內容。 提供 Google[樣式指南](http://www.google.com/design/spec/style/color.html#color-color-palette)和[色彩調色盤的產生器](http://www.materialpalette.com/)可協助您選擇為指定替代的色彩配置。

**Resources/values/colors.xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Resources/values/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
  </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/primary</item>
    <item name="colorPrimaryDark">@color/primaryDark</item>
    <item name="colorAccent">@color/accent</item>
    <item name="android:windowBackground">@color/window_background</item>
    <item name="windowActionModeOverlay">true</item>
  </style>
</resources>
```

中必須包含其他的樣式**值 v21**資料夾，以執行在 Android Lollipop 及更新版本時，套用特定的屬性。

**Resources/values-v21/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4.更新 AndroidManifest.xml

若要確保這個新佈景主題的資訊中的使用，將佈景主題**AndroidManifest**檔案中的新增`android:theme="@style/MyTheme"`（因為它是保留 XML 的其餘部分）。

**Properties/AndroidManifest.xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5.提供工具列和索引標籤的版面配置

建立**Tabbar.axml**並**Toolbar.axml**中的檔案**資源/配置**目錄，並從底下其內容貼上：

**Resources/layout/Tabbar.axml**

```xml
<android.support.design.widget.TabLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/sliding_tabs"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:tabIndicatorColor="@android:color/white"
    app:tabGravity="fill"
    app:tabMode="fixed" />
```

有幾個屬性的索引標籤已設定，包括以索引標籤的重心`fill`要和模式`fixed`。
如果您有許多索引標籤切換這您可能要以可捲動-閱讀 Android [TabLayout 文件](http://developer.android.com/reference/android/support/design/widget/TabLayout.html)若要深入了。

**Resources/layout/Toolbar.axml**

```xml
<android.support.v7.widget.Toolbar
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
    app:layout_scrollFlags="scroll|enterAlways" />
```

這些檔案中，我們將建立特定的佈景主題，您的應用程式而有所不同的工具列。
請參閱[Hello 工具列](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/)部落格文章來了解更多。


### <a name="6-update-the-mainactivity"></a>6.更新 `MainActivity`

在現有的 Xamarin.Forms 應用程式**MainActivity.cs**類別會繼承`FormsApplicationActivity`。 這必須取代`FormsAppCompatActivity`啟用新功能。

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

最後，「 連接 」 中的步驟 5 中新的版面配置`OnCreate`方法，如下所示：

```csharp
protected override void OnCreate(Bundle bundle)
{
  // set the layout resources first
  FormsAppCompatActivity.ToolbarResource = Resource.Layout.Toolbar;
  FormsAppCompatActivity.TabLayoutResource = Resource.Layout.Tabbar;

  // then call base.OnCreate and the Xamarin.Forms methods
  base.OnCreate(bundle);
  Forms.Init(this, bundle);
  LoadApplication(new App());
}
```
